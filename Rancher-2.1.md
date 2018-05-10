# Schedule

* Shipping: End Q3 2018

# Milestones

* [2.1 Milestone](https://github.com/rancher/rancher/milestone/121)

# Release Goals
Rancher 2.1 will be primarily focused on adding tools and guides to assist in migrating Rancher 1.6 users onto the new Rancher 2.0 platform.  The current planned items include the following and will be updated as necessary:
* A tool to convert current Rancher and Docker Compose file to a corresponding Kubernetes YAML equivalent.  
* A guide that maps current 1.6 Compose YAML format to a Kubernetes YAML format.

The following are a list of features that we are also planning to add for 2.1, subject to change.  We plan on shipping a few minor releases before 2.1 and it can include some of the features below as they are completed.

* **Project Quota** - Resources quota will now be supported at a project level.  The planned support will be identical to the ones currently supported at the [namespace level](https://kubernetes.io/docs/concepts/policy/resource-quotas/).
* **Cluster and Project level Catalogs** - The current Helm Catalog can only be enabled/disabled at a global level.  Support for cluster and project level catalogs will now be supported.
* **Audit Logs** - Support to push all audit logs to a centralized log store.
* **Enhanced Monitoring** Adding support for more extended monitoring using Prometheus.  
* **Enhanced Cloudprovider Plugins** - Support a plugin framework to allow the ability to add new hosted Kubernetes cloud providers.
* **More Authentication Providers** - SAML, OpenLDAP, and AzureAD will be supported.
* **Windows Support** - Add ability to create/import a Kubernetes Windows cluster.
* **Pipeline Enhancements**
  * Adding support for git and Gitlab
  * Adding support for more pipeline plugins
  * Adding support for Continuous Deployment (CD) in the form of deploying and updating k8s workloads.
* **Rancher CLI support for K8s cluster import/export** - Support the ability to export an entire k8s cluster managed by Rancher as a configuration file.  Also, support the ability to import a configuration file describe your k8s cluster into Rancher.
* **Dynamic Schema** - Add ability for UI to dynamically alter its user experience based on RBAC policies.
* **Kubernetes Service Catalog** - Add support for the [Kubernetes Service Catalog](https://kubernetes.io/docs/concepts/service-catalog/) feature that enables applications running in Kubernetes clusters to easily use external managed software offerings, such as a datastore service offered by a cloud provider.

  