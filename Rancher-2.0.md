# Schedule

* Scheduled: 2018 End Q1

# Milestones
* 2.0-tech-preview
  * [Release v2.0.0-alpha6](https://github.com/rancher/rancher/releases/tag/v2.0.0-alpha6)
  * [Release v2.0.0-alpha12](https://github.com/rancher/rancher/releases/tag/v2.0.0-alpha12)
* 2.0-beta - 2018 End Feb

# Release Goals
Rancher 2.0 will be the most significant release since 1.0 was announced over a year ago. The following describes some of the innovations and improvements we are expecting to launch.  While we are ambitious and confident in what we plan to do, the final feature set can change based on ideas and feedback from our user base.  Unlike 1.x, we do plan to ship regular tech preview builds so that our community has a chance to look at what we are building and help shape the release.

- **Clusters and Node Management** - Rancher supports the ability to add your k8s cluster via a cloud provider, Rancher Kubernetes Engine (RKE), or simply by importing an existing cluster.
  * Cloud Providers - Full integrated experience of creating and managing your nodes of your k8s cluster from one of the major cloud providers
    * Google Kubernetes Engine (GKE) - Supported in tech preview
    * Azure Container Service (AKS) - Release in Beta.
    * Amazon's Elastic Kubernetes Service (EKS) - Post 2.0 as EKS is still in preview mode
  * Rancher Kubernetes Engine (RKE) - Allow you to create a Rancher supported k8s cluster anywhere, on any cloud or private infrastructure.  You will be able to scale your hosts for your k8s deployment as you see fit through Rancher.
    * Cloud Support - Only Digital Ocean and AWS on tech preview, but not mixed cloud providers
  * Import - Import any existing k8s cluster
- **Authentication** - In this preview, only local authentication is supported.  You will be prompted to change the password for your default admin account.  By GA, Github, AD, and SAML 2.0 will be added.
- **User Management** - Rancher will support two default user types (admin and user) with respective default permissions.  A custom user type will also be supported.
    * Admin - Full global permissions (super admin)
    * Standard User - standard user permissions including:
      * only able to manage their own clusters including namespaces, user, projects, etc.
      * can view catalogs and node drivers, but cannot manage them.
      * cannot create roles but can assign them to users invited to their cluster and/or projects.
    * Custom - custom user type role that you can use to define your own user type
- **Role Based Access Control (RBAC)** - Rancher allows you to create your own global cluster roles that can be easily assigned to any users to managed k8s clusters and projects.  Rancher includes all out-of-box k8s roles and the ability to your own custom roles.
- **Project and Namespace Management** - Users can create namespaces and assign them to projects.  Projects are a new Rancher concepts that allows you to group a set of namespaces and assigning a set of user permissions on those namespaces.
- **Workload UX** - Rancher is introducing a new Workload UX that will make allow users to leverage the simplicity of Rancher Compose to create and manage their k8s workloads.  This is a very experimental for this tech preview but we expect more to come including ingress, dns, volumes, registries, secrets, and certificate management in the following milestone releases.
- **Rancher CLI** - CLI support for all Rancher 2.0 feature set.
- **Dynamic Schema** - This will allow the UI to automatically enable/disable functionality based on user role permissions.
- **Pod Security Policies** - Allow users to create their own pod security policy or policies that can be applied to roles
- **Group Support** - Allow you to group one or more users and apply them to a cluster or project.
- **Custom Node Support** - Ability to add nodes from anywhere similar to how 1.6 custom node management worked.
- **Catalog Support for Helm** - In addition to Rancher Compose support, Helm charts will also be supported in the updated 2.0 catalog where users can launch and update applications based off of either YAML format. 
- **HA and SSL support for Rancher server**
- **Alerts**
  * Disabled by default.  Enabling will install Prometheus AlertManager
  * Support for basic out-of-box alert conditions (final list TBD)
  * Support for following notifiers (Slack, Email, PagerDuty, Webhooks)
  * Support for alert management including creating, deleting, disabling, and muting alerts
  * Support for out-of-box system alerts (system services, networking, etc.) that can be configured with a notifier
  * Support for selector labels on cluster and project-wide resources (pod, nodes, etc.)
  * Support for "Test" notifier feature
  * No support for "management" plane alerts (i.e Cluster 1 went down)
- **Logging**
  * Disabled by default.  Enabling will install Fluentd
  * Support for ability to configure cluster-wide logging of stdout/err of containers and workloads
  * Support for ability to configure project-wide logging of stdout/err of containers and workloads
  * Support for ability to configure workload logging of a specific directory
  * Support for following Log Targets
    * Rancher embedded logging - Experimental and used for testing 
    * ElasticSearch
    * Spelunk
    * Syslog
    * Kafka
- **CI/CD Pipelines** - A simple integrated pipeline feature that allows users to create pipelines within projects for CI/CD
  * Disabled by default.  Enabling will install Jenkins
  * Support for GitHub integration initially
  * Support for creating pipelines through UI wizard, importing a YAML configuration file, or reading it the pipeline YAML directly from a GitHub project.
  * Support for creating pipelines with multiple stages and steps within a stage.
  * Support for automatic detection of language used to default to a container image with corresponding compiler, if required.
  * Integrated with registries added into projects
