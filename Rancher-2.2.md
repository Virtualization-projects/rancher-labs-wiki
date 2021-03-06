# Schedule

* Shipped: Mar 26, 2019

# Milestones

* [2.2 Milestone](https://github.com/rancher/rancher/milestone/140)

# Release Notes

* [2.2.10 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.10)
* [2.2.9 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.9)
* [2.2.8 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.8)
* [2.2.7 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.7)
* [2.2.6 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.6)
* [2.2.5 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.5)
* [2.2.4 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.4)
* [2.2.3 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.3)
* [2.2.2 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.2)
* [2.2.1 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.1)
* [2.2.0 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.2.0)

# Release Goals
Rancher 2.2 will be primarily focused on improving Rancher scalability, and adding new features to assist with the operation of your Kubernetes clusters.

The following is the list of features that we are also planning to add for 2.2, subject to change.

* **Enhanced Monitoring** - Monitoring of clusters, nodes, and k8s workloads will now be supported through integration with Prometheus. By leveraging Prometheus, Rancher will be able to provide a time series view into your data, and more sophisticated collection of stats and metric types beyond the basic Rancher monitoring.  Multi-tenancy support in terms of cluster and project-only Prometheus instances will also be supported.
* **Cross Cluster DNS** - Rancher will introduce the ability to deploy Kubernetes workloads across one or more k8s cluster with integration against a global DNS.  The following are the high level features included with the release:
  * Kubernetes workloads deployed can either be in standard k8s yaml format or helm catalog items deployed via Rancher across one or more k8s cluster.  If Rancher helm catalog items are used, upgrades and versioning of the application can then be supported and can be initiated across all clusters.
  * Users can specify an external DNS to workload mapping to allow easier access to applications.  Any changes to workload deployments across any cluster will also be automatically reflected in the external DNS.
  * Route53 will be the default DNS implementation.
* **Multi-tenant Catalog Support** - Rancher will allow catalogs to be configured and managed at both a cluster and project level.  This will allow admins more flexibility to specifying catalogs that users have access to deploy from.  
* **Cloud Key Management** - Cloud keys used by either node templates will now be now properly stored as Kubernetes secrets, and can be reused by existing Rancher features or any future Rancher integration with cloud providers such as AWS, Google, DigitalOcean, and Azure.
* **Etcd Backup/Restore** - Add capabilities to RKE clusters to support both backup and restore from the UI and using S3.  Local backups can also be used to restore a cluster.
* **Improved Rancher Scaling** - Improve Rancher scaling for both cluster, nodes, and workloads.
* **Bitbucket Support for pipelines** - Bitbucket will be available as an option for a git repo in Rancher pipelines.

  