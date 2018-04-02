# Schedule

* Scheduled: End Apr, 2018

# Milestones
* 2.0-tech-preview
  * [Release v2.0.0-alpha6](https://github.com/rancher/rancher/releases/tag/v2.0.0-alpha6)
  * [Release v2.0.0-alpha12](https://github.com/rancher/rancher/releases/tag/v2.0.0-alpha12)
  * [Release v2.0.0-alpha21](https://github.com/rancher/rancher/releases/tag/v2.0.0-alpha21)
  * [Release v2.0.0-alpha23](https://github.com/rancher/rancher/releases/tag/v2.0.0-alpha23)
  * [Release v2.0.0-alpha24](https://github.com/rancher/rancher/releases/tag/v2.0.0-alpha24)
* 2.0-beta - Apr 2nd, 2018

# Release Goals
Rancher 2.0 will be the most significant release since 1.0 was announced over two years ago. The following describes some of the innovations and improvements we are expecting to launch.  While we are ambitious and confident in what we plan to do, the final feature set can change based on ideas and feedback from our user base.  Unlike 1.x, we do plan to ship regular tech preview builds so that our community has a chance to look at what we are building and help shape the release.

- **Clusters and Node Management** - Rancher supports the ability to add your k8s cluster hosted by a cloud provider, create one using Rancher Kubernetes Engine (RKE), or simply by importing an existing cluster of your own.
  * Cloud Providers - Full integrated experience of creating and managing your nodes of your k8s cluster from one of the major cloud providers
    * Google Kubernetes Engine (GKE)
    * Azure Container Service (AKS)
    * Amazon's Elastic Kubernetes Service (EKS) - Unlikely to be in by GA as EKS is still in preview.
  * Rancher Kubernetes Engine (RKE) - Allow you to create a Rancher supported k8s cluster anywhere, on any cloud or private infrastructure.  You will be able to scale your hosts for your k8s deployment as you see fit through Rancher.
  * Import - Import any existing k8s cluster.  Only v1.8+.
- **Authentication** - Support for local auth, Github, and AD/LDAP.  After installing Rancher, you will be prompted to change your admin password.
- **User Management** - Rancher will support two default user types (admin and user) with respective default permissions.  A custom user type will also be supported.
    * Admin - Full global permissions (super admin)
    * Standard User - standard user permissions including:
      * only able to manage their own clusters including namespaces, user, projects, etc.
      * can view catalogs and node drivers, but cannot manage them.
      * cannot create roles but can assign them to users invited to their cluster and/or projects.
    * Custom - custom user type role that you can use to define your own user type
- **Role Based Access Control (RBAC)** - Rancher allows you to create your own global cluster roles that can be easily assigned to any users to manage k8s clusters and projects.  Rancher includes all out-of-box k8s roles and the ability to customize your own roles.  Each custom role can be assigned to be assignable at a global, cluster, or project level.
- **Project and Namespace Management** - Users can create namespaces and assign them to projects.  Projects are a new Rancher concepts that allows you to group a set of namespaces and assign a set of user permissions on those namespaces.
- **Workload UX** - Rancher is introducing a new Workload UX to create and manage their k8s workloads.  Supported workload features:
  * Ability to create workloads that will automatically translate into appropriate k8s resources (Pods, StatefulSets, Deployments, DaemonSets, etc.).  Rancher will also automatically create an appropriate k8s service (NodePort, LoadBalance Service, ClusterIP) based on how the user wants to publish their ports.  Rancher does the heavy lifting and translation for you.  You do not need to know or understand k8s constructs before using this.
  * Ability to create and use Ingress.
  * Ability to generate DNS records for k8s services or external IP addresses.
  * Ability to add authenticated registries
  * Ability to manage k8s secrets
  * Ability to manage your SSL certificates for Ingress.
  * Ability to generate a public endpoint based on ports exposed by NodePort Services, LoadBalancer Services, Ingress, and HostPorts.
- **Rancher CLI** - CLI support for all Rancher 2.0 feature set.
- **Pod Security Policies** - Allow users to create their own pod security policy or policies that can be applied to roles.
- **Catalog Support for Helm** - Helm charts will be supported in the updated 2.0 catalog.
- **HA and SSL support for Rancher server**
  * Rancher can be installed via `docker run`
  * Rancher can be installed into an existing k8s cluster.  This option will enable HA support as Rancher HA will be managed by the external k8s cluster.
- **Alert Management**
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
    * Splunk
    * Syslog
    * Kafka
- **CI/CD Pipelines** - A simple integrated pipeline feature that allows users to create pipelines within projects for CI/CD
  * Disabled by default.  Enabling will install Jenkins
  * Support for GitHub integration initially
  * Support for creating pipelines through UI wizard, importing a YAML configuration file, or reading it the pipeline YAML directly from a GitHub project.
  * Support for creating pipelines with multiple stages and steps within a stage.
  * Support for automatic detection of language used to default to a container image with corresponding compiler, if required.
  * Integrated with registries added into projects
