# Prerequisites
* Rancher v1.1.1 or later
* Basic understanding of Data, Orchestration, Compute planes

## Planes
The Data Plane is comprised of one of more Etcd nodes which persist state regarding the Compute Plane. Resiliency is achieved by adding 3 hosts to this plane.

The Orchestration plane is comprised of stateless K8s components (kubernetes, scheduler, controller-manager, kubectld, rancher-kubernetes-agent, rancher-ingress-controller) which orchestrate and manage the Compute Plane. Resiliency is achieved by adding 2 hosts to this plane.

The Compute Plane is comprised of the real workload, orchestrated and managed by Kubernetes.

In a production deployment, each plane runs on separate physical or virtual hosts. For development, planes may overlap to simplify management and reduce costs.

# Deployment

## Standalone Deployment
Characteristics
Simple, low-cost environment for development and training
No resiliency to host failure
Low performance
Instructions
Create a Kubernetes environment.
Add 1 host with >=1 CPU and >=4GB RAM.

## Resilient Overlapping-Planes Deployment
### Characteristics
Simple, low-cost environment for distributed development and training
Data/Orchestration plane availability resilient to minority of hosts failing
Failure tolerance of Compute plane depends on the deployment plan
Potential performance issues with etcd/kubernetes components
### Instructions
Create a Kubernetes environment.
Add 3 hosts with >=1 CPU and >=2GB RAM.

## Resilient Separated-Planes Deployment
### Characteristics
Data plane availability resilient to minority of hosts failing
Orchestration plane resilient to all but one host failing
Failure tolerance of Compute plane depends on the deployment plan
High-performance, production-ready environment
### Instructions
Create a Cattle environment.
Add 3 hosts with 1 CPU, >=1.5GB RAM, >=20GB DISK. Label these hosts etcd=true.
If you care about backups, see ‘Configuring Remote Backups’ now.
If you don’t want pods scheduled on these hosts, also add label nopods=true.
Add 2 hosts with >=1 CPU and >=2GB RAM. Label these hosts orchestration=true.
If you don’t want pods scheduled on these hosts, also add label nopods=true.
Add 1+ hosts without any special labels. Resource requirements vary by workload.
Edit the environment and select Kubernetes.

## Configuring Remote Backups
With the newest release, full backups of the Data Plane are performed every 15 minutes and deleted after 24 hours, by default. A backup period below 30 seconds is not recommended.

Network storage latency and size should be taken into consideration. 50MB for any single backup is a good estimate for storage requirements. For example, backups created every 15 minutes and retained for 1 day would store a maximum of 96 backups, requiring ~5 GB storage. If there is no intention to ever restore to a previous point in time, fewer historical backups may be retained.

These backups are persisted to a static location /var/etcd/backups on exactly one of the Data Plane hosts. It is imperative that some type of network storage is mounted to this location on all hosts comprising the Data Plane. This must be done before Kubernetes is launched.

## Deployment Migrations
Any deployment plan may be migrated to any other deployment plan. For resilient deployment targets, this involves no downtime. If your desired migration is not listed, we don’t officially support it – but it is possible. Contact Rancher for further instruction.

## Standalone -> Overlapping
Add 2 more hosts to the environment. The deployment automatically scales up.
Overlapping -> Separated
Add 3+ hosts to the environment. Ensure resource requirements are satisfied and create host labels as defined in Resilient Separated-Planes deployment instructions.
For etcd containers not on etcd=true hosts, migration is necessary. Delete one data sidekick container of an etcd. Ensure it is recreated on a correct host and becomes healthy (green circle) before continuing. Repeat this process as is necessary.
For kubernetes, scheduler, controller-manager, kubectld, rancher-kubernetes-agent, rancher-ingress-controller containers not on the orchestration=true host, delete the containers.
Make note of etcd=true and orchestration=true hostnames. From kubectl, type 'kubectl delete node <hostname>’ to remove the hosts from the Compute plane. Wait until all pods on etcd=true, orchestration=true hosts are deleted. At this point, delete kubelet and proxy containers from these hosts.

# Management

## Failure Recovery
If a host enters RECONNECTING state, attempt to re-run the agent registration command. Wait 3 minutes. If the host hasn’t entered ACTIVE state, add a new host of similar resources and host labels. Delete the old host from the environment. Containers will be scheduled to the new host and eventually become healthy.

## Disaster Recovery
If a majority of hosts running etcd fail, follow these steps:
Find an etcd node in Running state (green circle). Click Execute Shell and run command 'etcdctl cluster-health'. If the last output line reads 'cluster is healthy' then there is no disaster, stop immediately. If the last output line reads 'cluster is unhealthy', make a note of this etcd. This is your sole survivor, all other containers are dead or replaceable.
Delete hosts in RECONNECTING state.
On your sole survivor, click Execute Shell and run command ‘disaster’. The container will restart automatically. Etcd will then heal itself and become a 1-node cluster. System functionality is restored.
Add more hosts until you have at least 3. Etcd scales back up. In most cases, everything will heal automatically. If new/dead containers are still initializing after 3 minutes, delete their data containers (green circle, shaded-in). Do not, under any circumstance, delete the data container of your sole survivor or you lose everything. System resiliency is restored.

## Restoring Backups
Backup restoration will only work for Resilient Separated-Planes deployments. If all hosts running etcd fail, follow these steps:
Change your environment type to ‘Cattle’. This will tear down the Kubernetes system stack. Pods (the Compute plane) will remain intact and available.
Delete reconnecting/disconnected hosts and add new hosts if you need them.
Ensure at least one host is labelled etcd=true.
For each etcd=true host, mount the network storage containing backups - see ‘Configuring Remote Backups’ section for details. Then run these commands:
# configure this to point to the desired backup in /var/etcd/backups
target=2016-08-26T16:36:46Z
# don’t touch anything below this line
docker volume rm etcd
docker volume create --name etcd
docker run -d -v etcd:/data --name etcd-restore busybox
docker cp /var/etcd/backups/$target etcd-restore:/data/data.current
docker rm etcd-restore
Change your environment type back to ‘Kubernetes’. The system stack will launch and your pods will be reconciled. Note: your backup may reflect a different deployment topology than what currently exists; pods may be deleted/recreated at this time.