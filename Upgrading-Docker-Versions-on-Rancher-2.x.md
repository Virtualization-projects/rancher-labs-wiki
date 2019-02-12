This document walks through the recommended steps to be followed when upgrading Docker on nodes that are part of Kubernetes cluster created by Rancher on the following 2 types of cluster:
1. Clusters created by Rancher by bringing your own nodes, i.e. creating a custom cluster through the UI, that are hosted in any infrastructure provider or in on-premise bare metal servers.
2. Clusters created by Rancher on nodes that are deployed by Rancher using an infrastructure provider like Amazon EC2, Azure, vSphere, DigitalOcean.

## Cluster created by Rancher by bringing your own nodes:

### Upgrading Worker Nodes

Before you start upgrading the Docker version on worker nodes, we highly recommend adding at least one additional worker node to help facilitate the draining of Docker containers during the upgrade process.

Steps to be followed when upgrading Docker version on the worker nodes:

1. Drain worker node to migrate all workloads to other existing worker nodes.
   * In Rancher v2.0.x, execute `kubectl drain --ignore-daemonsets  <node-name> ` using your kubectl. Note: Do not use kubectl shell provided by rancher as this shell can be stopped as part of the draining process. Wait for kubectl command to complete successfully.

   * In Rancher v2.1.x, use the `Drain` option provided for the worker node in UI. Leave the default option: "Even if there are DaemonSet-managed pods" selected. Wait for the status of the node to get to `Drained` state.

2. Upgrade Docker version on the node by following upgrade instructions specific to the OS version of the nodes.
* For Ubuntu - https://docs.docker.com/install/linux/docker-ce/ubuntu/#upgrade-docker-ce

3. Uncordon the node using `uncordon` option for the worker node in UI

4. Wait for work node to get to `Active` state.

Follow the above steps for all the worker nodes.

### Upgrading etcd Nodes

Before you start upgrading etcd nodes, it is highly recommend you backup etcd. There is no need for removing the etcd node from cluster when upgrading the Docker version. 

1. Upgrade the Docker version on the etcd node ONE at a time. Do NOT attempt upgrading Docker version on more than 1 etcd node at the same time.
2. Once Docker is upgraded on the etcd node, verify quorum is up and etcd is healthy .This can be checked by executing the following command from `etcd` container on any of the etcd nodes -  `etcdctl --endpoints=https://<node-ip>:2379 member list`.

### Upgrading Control Nodes

Upgrade the Docker version on the control nodes ONE at a time. Do NOT attempt upgrading the Docker version on more than 1 control node at the same time.

## Cluster created by Rancher on nodes that are deployed in Infrastructure provider

As we have the ability to create node pools with the desired Docker version, we recommend creating a new node pool with the new Docker version and replace the existing node pool in the cluster instead of having to upgrade the Docker version in each of the nodes in the cluster.  

### Upgrading Docker version for Worker Node Pool
As part of this upgrade process, you will be guided through the process of creating a new worker pool and adding this pool to the cluster before deleting the existing worker pool.

1. Create a new worker node template with same value as existing worker node template except that the “Docker Install Url” should point to the new Docker version . This option will be present under “Engine Options” section. User can choose to use the “Clone” option to achieve this.
2. Navigate to (Cluster Name) -> Nodes. 
3. Edit the cluster and use the `Add Node Pool` option to create a new worker pool (choose a different name from the existing worker pool) with same count as the count on the existing worker pool you want to upgrade and the template that was created in step 1.
4. Wait for all the new nodes in the pool to become active.
5. Select all nodes in old worker pool.
6. Cordon all nodes in old worker pool.
7. Drain all nodes in old worker pool.
   * In Rancher v2.0.x, execute `kubectl drain --ignore-daemonsets  <node-name> ` using your kubectl. Note: Do not use kubectl shell provided by rancher as this shell can be stopped as part of the draining process.
   * In Rancher v2.1.x, use the `Drain` option provided for the worker node in UI. Leave the default option: "Even if there are DaemonSet-managed pods" selected. 
8. Once all nodes in the old pool are in the drained state, delete the pool. 

### Upgrading docker version for Control/etcd Node Pools

As part of this upgrade process, you will be guided through the process of updating the existing node templates that the existing control/etcd node pool is using.  

1. Before you start upgrading etcd nodes, it is highly recommend that you backup etcd.
2. Update template that the etcd/control plane pool by changing the "Docker Install Url” option that is present under “Engine Options” section to point to the new Docker version.
3. Before proceeding to deleting nodes, make sure that you have at least 2 control nodes and 3 etcd nodes.
4. Navigate to (Cluster Name) -> Nodes.
5. Delete one node in the etcd/control plane pool. Node will automatically get recreated. 
6. Wait for node to create and transition to `Active` state.
7. In case of etcd nodes, make sure that etcd continues to be healthy and reports correct members. This can be checked by executing the following command from `etcd` container on any of the etcd nodes -  `etcdctl --endpoints=https://<node-ip>:2379 member list`.
8. Wait for all cluster updating actions to finish (red box at the top of the screen) and the cluster get to `Active` state.
9. Repeat steps 4-8 for each etcd/control plane node.