# Schedule

* Shipping: End Feb
# Milestones

* [2.2 Milestone](https://github.com/rancher/rancher/milestone/140)

# Release Goals
Rancher 2.1 will be primarily focused on improving Rancher scalability, and adding new features to assist with the operation of your Kubernetes clusters.

The following is the list of features that we are also planning to add for 2.2, subject to change.

* **Enhanced Monitoring** - Monitoring of clusters, nodes, and k8s workloads will now be supported through integration with Prometheus. By leveraging Prometheus, Rancher will be able to provide a time series view into your data, and more sophisticated collection of stats and metric types beyond the basic Rancher monitoring.  Multi-tenancy support in terms of cluster and project-only Prometheus instances will also be supported.
* **Cross Cluster DNS/LoadBalancer** - Rancher will introduce the ability to deploy Kubernetes workloads across one or more k8s cluster with integration against a global DNS or LoadBalancer.  The following are the high level features included with the release:
  * Kubernetes workloads deployed can either be in standard k8s yaml format or helm catalog items deployed via Rancher across one or more k8s cluster.  If Rancher helm catalog items are used, upgrades and versioning of the application can then be supported and can be initiated across all clusters.
  * Users can specify either an external DNS or Load Balancer to workload mapping to allow easier access to applications.  Any changes to workload deployments across any cluster will also be automatically reflected in the external DNS/LoadBalancer.
  * Route53 will be the default DNS implementation but Rancher will allow other DNS and Load Balancer providers to be configured.
* **Multi-tenant Catalog Support** - Rancher will allow catalogs to be configured and managed at both a cluster and project level.  This will allow admins more flexibility to specifying catalogs that users have access to deploy from.  
* **Cloud Key Management** - Cloud keys used by either node templates will now be now properly stored as Kubernetes secrets, and can be reused by existing Rancher features or any future Rancher integration with cloud providers such as AWS, Google, DigitalOcean, and Azure.
* **Etcd Backup/Restore** - Add capabilities to RKE clusters to support both backup and restore from the UI and using S3.  Local backups can also be used to restore a cluster.
* **Improved Rancher Scaling** - Improve Rancher scaling for both cluster, nodes, and workloads.
* **Bitbucket Support for pipelines** - Bitbucket will be available as an option for a git repo in Rancher pipelines.

  