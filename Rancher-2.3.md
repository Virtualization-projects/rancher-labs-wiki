# Release Goals

Rancher 2.3 will have two major focus areas (themes) - Enhanced Security and support for Windows containers. We may also consider other usability enhancements.  

## Enhanced Security:
To provide enhanced security in Kubernetes, Rancher 2.3 may include the following features: 
* **Cluster Templates** - Users will be able to configure Kubernetes clusters to make them more secure. These configurations will be stored as templates.  Kubernetes Pod Security Policies, Network Policies, Master components settings, Kubernetes version, default roles and users, projects and even apps may be defined in the template. IT Operators could enforce enterprise wide security policies as follows:
    * Create template by choosing the appropriate Kubernetes and/or Rancher settings
    * Enforce template for cluster creation (when a new cluster is created, user will be asked to choose a template or default template will be applied)
    * Users may not change the settings inherited from the template (for example, Kubernetes version, network plugin and API server settings will be visible but not updatable)
    * Users are free to update other settings 
    * Deviation from template may trigger notifications for the admin
    * Out of the box (best practices) templates may be shipped by Rancher
* **Security Scans** - Scan all existing clusters and check settings against the template that was applied. If deviation is uncovered, either shut down or notify (or both) the admin. Periodic scans may be scheduled  and reports will be mailed/uploaded for the admin. Users may also validate their clusters against industry  benchmarks (CIS/NIST)
* **Private Registry** - Using  Rancher 2.3 UI or CLI Users may choose to deploy Harbor on-premise or in the cloud. 
* **Image Scanning** - Clair will be integrated as an out of the box image scanning tool 

## Windows GA Support
In 2.3, Rancher will make support for Windows containers on Kubernetes generally available. This will mean that using Rancher 2.3, customer will be able to add Windows worker nodes to their new or existing clusters. We may also add new capabilities to solve issues that Windows GA support in Kubernetes 1.14 doesn’t fully address. 
Here is a list of proposed features for 2.3 Windows GA Support:
* Add Windows Worker Nodes to New Or Existing Clusters - From Rancher UI, users will be able to add  nodes of type Windows (2019) to a cluster. Control plane node will always be linux. Worker nodes can be Windows only or Windows and Linux
* Networking - Using Rancher UI, users can add Flannel as a network provider. 
* Node scheduler - Kubernetes 1.14 does not support auto-scheduling of Windows pods on Windows nodes. Rancher may solve this problem by tying workloads to specific nodes. This capability will also work with self-healing nodes (discussed later)

## Additional Features
### Self-healing Nodes
For RKE clusters, users will be able to define a minimum number of nodes required. If the number of worker nodes goes below this threshold, we can start a new node with same settings. Admin may be able to define a maximum number of nodes as well. If a new node is added to this cluster and it exceeds the total allowed nodes, the node creation will fail and an error message will be displayed. We will never kill an already running node automatically

