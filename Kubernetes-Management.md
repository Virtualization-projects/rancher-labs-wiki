# Prerequisites
* Rancher v1.1.1 or later
* Basic understanding of Data, Orchestration, Compute planes

## Planes

For production deployments, it is best practice that each plane runs on dedicated physical or virtual hosts. For development, multi-tenancy may be used to simplify management and reduce costs.

### Data Plane
Comprised of one or more Etcd nodes which persist state regarding the [Compute Plane](#compute-plane). Resiliency is achieved by adding 3 hosts to this plane.

### Orchestration Plane
Comprised of stateless Kubernetes/Rancher components which orchestrate and manage the [Compute Plane](#compute-plane):
* apiserver
* scheduler
* controller-manager
* kubectld
* rancher-kubernetes-agent
* rancher-ingress-controller)
Resiliency is achieved by adding 2 hosts to this plane.

### Compute Plane
Comprised of the real workload (Kubernetes pods), orchestrated and managed by Kubernetes.

# Deployment Types

## Standalone

### Characteristics
* Simple, low-cost environment for development and training
* No resiliency to host failure
* Low performance

### Instructions
1. Create a Kubernetes environment.
2. Add 1 host with >=1 CPU and >=4GB RAM.

## Resilient Overlapping-Planes

### Characteristics
* Simple, low-cost environment for distributed development and training
* [Data Plane](#data-plane) and [Orchestration Plane](#orchestration-plane) resilient to minority of hosts failing
* [Compute Plane](#compute-plane) resiliency depends on the deployment plan
* Potential performance issues with Etcd/Kubernetes components

### Instructions
1. Create a Kubernetes environment.
2. Add 3 hosts with >=1 CPU and >=2GB RAM.

## Resilient Separated-Planes

### Characteristics
* [Data Plane](#data-plane) resilient to minority of hosts failing
* [Orchestration Plane](#orchestration-plane) resilient to all but one host failing
* [Compute Plane](#compute-plane) resiliency depends on the deployment plan
* High-performance, production-ready environment

### Instructions (Rancher v1.2+)
1. Create a Cattle environment.
2. Add 3 hosts with 1 CPU, >=1.5GB RAM, >=20GB DISK. Label these hosts **etcd=true**.
    1. If you care about backups, see [Configuring Remote Backups](#configuring-remote-backups) now.
    2. If you don’t want pods scheduled on these hosts add label **nopods=true**.
3. Add 2 hosts with >=1 CPU and >=2GB RAM. Label these hosts **orchestration=true**.
    1. If you don’t want pods scheduled on these hosts, add label **nopods=true**.
4. Add 1+ hosts without any special labels. Resource requirements vary by workload.
5. Navigate to **Catalog > Library**. On the Kubernetes catalog item, click **View Details**. Select your desired version, optionally update configuration options, and click **Launch**.

### Instructions (Rancher v1.1.X and older)
1. Create a Cattle environment.
2. Add 3 hosts with 1 CPU, >=1.5GB RAM, >=20GB DISK. Label these hosts **etcd=true**.
    1. If you care about backups, see [Configuring Remote Backups](#configuring-remote-backups) now.
    2. If you don’t want pods scheduled on these hosts add label **nopods=true**.
3. Add 2 hosts with >=1 CPU and >=2GB RAM. Label these hosts **orchestration=true**.
    1. If you don’t want pods scheduled on these hosts, add label **nopods=true**.
4. Add 1+ hosts without any special labels. Resource requirements vary by workload.
5. Edit the environment and select Kubernetes.

# Management

## Configuring Remote Backups
With the newest release, full backups of the Data Plane are performed every 15 minutes and deleted after 24 hours, by default. A backup period below 30 seconds is not recommended.

Network storage latency and size should be taken into consideration. 50MB for any single backup is a good estimate for storage requirements. For example, backups created every 15 minutes and retained for 1 day would store a maximum of 96 backups, requiring ~5 GB storage. If there is no intention to ever restore to a previous point in time, fewer historical backups may be retained.

These backups are persisted to a static location `/var/etcd/backups` on exactly one of the Data Plane hosts. It is imperative that some type of network storage is mounted to this location on all hosts comprising the Data Plane. This must be done before Kubernetes is launched.

## Deployment Migrations
Any deployment plan may be migrated to any other deployment plan. For resilient deployment targets, this involves no downtime. If your desired migration is not listed, we don’t officially support it – but it is possible. Contact Rancher for further instruction.

### [Standalone](#standalone) to [Overlapping-Planes](#resilient-overlapping-planes)
1. Add 2 more hosts to the environment. The deployment automatically scales up.

### [Overlapping-Planes](#resilient-overlapping-planes) to [Separated-Planes](#resilient-separated-planes)
1. Add 3+ hosts to the environment. Ensure resource requirements are satisfied and create host labels as defined in the [Separated-Planes](#resilient-separated-planes) deployment instructions.
2. For etcd containers not on **etcd=true** hosts, migration is necessary. Delete one data sidekick container of an etcd. Ensure it is recreated on a correct host and becomes healthy (green circle) before continuing. Repeat this process as is necessary.
3. For kubernetes, scheduler, controller-manager, kubectld, rancher-kubernetes-agent, rancher-ingress-controller containers not on the **orchestration=true** host, delete the containers.
4. Make note of **etcd=true** and **orchestration=true** hostnames. From kubectl, type `kubectl delete node <hostname>` to remove the hosts from the [Compute Plane](#compute-plane). Wait until all pods on **etcd=true**, **orchestration=true** hosts are deleted. Kubelet and proxy containers will automatically shutdown once pod migration is complete.

## Failure Recovery
If a host enters reconnecting/disconnected state, attempt to re-run the agent registration command. Wait 3 minutes. If the host hasn’t re-entered active state, add a new host with similar resources and host labels. Delete the old host from the environment. Containers will be scheduled to the new host and eventually become healthy.

## Disaster Recovery
If a majority of hosts running etcd fail, follow these steps:

1. Find an etcd node in Running state (green circle). Click Execute Shell and run command `etcdctl cluster-health`. If the last output line reads *cluster is healthy*, then there is no disaster, stop immediately. If the last output line reads *cluster is unhealthy*, make a note of this etcd. This is your sole survivor, all other containers are dead or replaceable.
2. Delete hosts in reconnecting/disconnected state.
3. On your sole survivor, click **Execute Shell** and run command `disaster`. The container will restart automatically. Etcd will then heal itself and become a single-node cluster. System functionality is restored.
4. Add more hosts until you have at least three. Etcd scales back up. In most cases, everything will heal automatically. If new/dead containers are still initializing after three minutes, click **Execute Shell** and run command `delete`. **Do not, under any circumstance, run the `delete` command on your sole survivor.** System resiliency is restored.

## Restoring Backups
Backup restoration will only work for [Resilient Separated-Planes](#resilient-separated-planes) deployments. If all hosts running etcd fail, follow these steps:

1. Change your environment type to **Cattle**. This will tear down the Kubernetes system stack. Pods (the Compute plane) will remain intact and available.
2. Delete reconnecting/disconnected hosts and add new hosts if you need them.
3. Ensure at least one host is labelled **etcd=true**.
4. For each **etcd=true** host, mount the network storage containing backups - see [Configuring Remote Backups](#configuring-remote-backups) section for details. Then run these commands:

    ```bash
    # configure this to point to the desired backup in /var/etcd/backups
    target=2016-08-26T16:36:46Z_etcd_1
    # don’t touch anything below this line
    docker volume rm etcd
    docker volume create --name etcd
    docker run -d -v etcd:/data --name etcd-restore busybox
    docker cp /var/etcd/backups/$target etcd-restore:/data/data.current
    docker rm etcd-restore
    ```
    * Note - you must be logged in as a user with read access to the remote backups. Otherwise, the `docker cp` command will silently fail.
5. Change your environment type back to **Kubernetes**. The system stack will launch and your pods will be reconciled. Your backup may reflect a different deployment topology than what currently exists; pods may be deleted/recreated.

## Tearing Down

After tearing a down a Kubernetes stack, persistent data is left behind. A user may choose to manually delete this data if they wish to repurpose/reuse the hosts.

* A named volume `etcd` will exist on all hosts which ran etcd at some point. Run `docker volume rm etcd` on each host to delete it
* Backups are enabled by default and stored to `/var/etcd/backups` on hosts running etcd. Run `rm -r /var/etcd/backups/*` on one host (if network storage was mounted) or all hosts (if no network storage was mounted) to delete the backups