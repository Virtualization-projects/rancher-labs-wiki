This is a post mortem on https://github.com/rancher/rancher/issues/8326 to share the things I learned about debugging cattle.

## The problem
Hosts would flapping through the states of disconnecting, reconnecting, and active after a single host was deactivated and then activated or if a new host was added.

## The root cause
A call to the external scheduler during global service deployment planning was consuming threads used for host agent pings such that pings were never processed and cattle marked the hosts as disconnected.

The calls to the external scheduler are not particularly slow, but the volume of them and the overhead of the network traffic was enough to trigger a snowballing effect where more and more events were getting put onto the core thread pool's queue. This cause the agent ping events to not get processed in a timely manner. 

The issue could be reproduced by having a high number of global services (I reproduced with 20) and deactivating and then activating a host.

## The fix
We removed the call to the external sceduler in the global deployment planner and that completely fixed the issue. This will cause our cause our [io.rancher.scheduler.require_any](https://github.com/rancher/rancher/issues/7254) feature to not work properly for global services, but we'll address that problem separately in the future.

I believe we also plan to move the agent pings to a separate thread pool so that they cannot get starved like this in the future.

## Things learned/re-enforced
### Global services are special
Global services are special and different as far as scheduling is concerned. Containers for global services get created and pre-scheduled by the global service deployment planner. By they time the containers get to "normal" allocation, their host has already been decided by the global service deployment and communicated to the allocator via the requestedHostId field. The allocator makes no scheduling decisions and instead just records the container host relationship.

Every time a host is added, removed or changes to or from a certain state (ie goes from inactive to active), the global service deployment planner runs for every single global service to see if their are any new hosts where the global service should be deployed. With a lot of global services, this can generate a good bit of processing in cattle

### Thread pools in cattle
We have thread pools in cattle that are segregated for different purposes. 

**You can see the cattle thread pools them and their usage by hitting this URL: http:/<your rancher here>/admin/processes/pools.**

We dont expose this in the UI because it donesnt work correctly in HA. It only shows the threads for the particular instance of cattle that served that request.

In this case, when a host was activated, we saw the core pool hit its maximum and a large queue start to build up. This was the smoking gun that allowed us to find the root cause. The core pool is the default pool and unless you specify otherwise explicity in cattle events will use this pool. So, things that are in this pool need to be fast. A network call to the external scheduler was not fast enough.

### Java thread dumps in rancher logs
You can get a thread dump in cattle by doing a `kill -3` on the java process. This will not kill cattle, it will just pause it long enough to get a thread dump. This thread dump showed that threads in the core pool were busy making calls to the external scheduler and that told us what we needed.

### Running cattle in HA mode locally
If you are a java developer and run cattle out of eclipse, you run cattle in a single-node HA mode. This is important because when in HA mode, even if you have just a single node, we turn on on Hazelcast as our distributed datastore for locking and eventing. In non-HA mode, we just handled this in-memory. To turn on HA mode (ie start using hazelcast), you can add the following environment variables to your eclipse debug configuration.
```
CATTLE_CLUSTER_ADVERTISE_ADDRESS: <your ip>
CATTLE_MODULE_PROFILE_HAZELCAST_EVENTING: true
CATTLE_MODULE_PROFILE_HAZELCAST_LOCK: true
DEFAULT_CATTLE_TRAEFIK_EXECUTE: true # Not sure if this is still used because we stopped using traefik, but it was in ourHA mode script
SPRING_PROFILES_ACTIVE: hazelcast.eventing,hazelcast.lock,hazelcast.config.basic
```
I now have two debug conigs: one for HA and one for normal single node.
For awhile, we suspected that HA mode had something to do with the problem, which is why I pursued this, but it ended up not being part of the problem.

## Red herrings
### websocket-proxy and ELB
For a while, I suspected either ELB or websocket-proxy as the problem because it seemed like the pings from the agents to cattle were not making it to cattle.

ELB made us suspicious because it can be finicky if you don't have it setup properly. But, both users experiencing the problem had it set up properly with proxy protocol (which is needed to handle websocket connections).

websocket-proxy made use suspicious because killing the wsp process on the server would resolve the issue. But this fixed the issue because it made the external scheduler disconnect from cattle and the global service deployment planning could proceed without making the call to the scheduler.