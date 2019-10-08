# Schedule
* Shipped: Oct 8, 2019

# Milestones

* [2.3 Milestone](https://github.com/rancher/rancher/milestone/153)

# Release Notes

* [2.3.0 Release Notes](https://github.com/rancher/rancher/releases/tag/v2.3.0)

# Release Goals

Rancher 2.3 will have three major focus areas (themes) - Enhanced Security, support for Windows containers, and Istio. We may also consider other usability enhancements.  

## Enhanced Security:
To provide enhanced security in Kubernetes, Rancher 2.3 may include the following features: 
* **Cluster Templates** - Users will be able to configure Kubernetes clusters to make them more secure. These configurations will be stored as templates.  Kubernetes Pod Security Policies, Network Policies, Master components settings, and Kubernetes version. IT Operators could enforce enterprise wide security policies as follows:
    * Create template by choosing the appropriate Kubernetes and/or Rancher settings
    * Enforce template for cluster creation (when a new cluster is created, user will be asked to choose a template or default template will be applied)
    * Users may not change the settings inherited from the template (for example, Kubernetes version, network plugin and API server settings will be visible but not updatable)
    * Users are free to update other settings 
    * Out of the box (best practices) templates may be shipped by Rancher

## Windows GA Support
In 2.3, Rancher will make support for Windows containers on Kubernetes generally available. This will mean that using Rancher 2.3, customer will be able to add Windows worker nodes to their new or existing clusters. We may also add new capabilities to solve issues that Windows GA support in Kubernetes 1.14 doesn’t fully address. 
Here is a list of proposed features for 2.3 Windows GA Support:
* Add Windows Worker Nodes to New Or Existing Clusters - From Rancher UI, users will be able to add  nodes of type Windows (2019) to a cluster. Control plane node will always be linux. Worker nodes can be Windows only or Windows and Linux
* Networking - Using Rancher UI, users can add Flannel as a network provider. 
* Node scheduler - Kubernetes 1.14 does not support auto-scheduling of Windows pods on Windows nodes. Rancher may solve this problem by tying workloads to specific nodes. This capability will also work with self-healing nodes (discussed later)

## Istio Support
In 2.3, Rancher will support Istio v1.2+ as a Service Mesh solution.  By leverage Istio, users will be able to take advantage of the following:
* Automatic load balancing for HTTP, gRPC, WebSocket, and TCP traffic.
* Fine-grained control of traffic behavior with rich routing rules, retries, failovers, and fault injection.
* A pluggable policy layer and configuration API supporting access controls, rate limits and quotas.
* Automatic metrics, logs, and traces for all traffic within a cluster, including cluster ingress and egress.
* Secure service-to-service communication in a cluster with strong identity-based authentication and authorization.

Rancher will also provide a simplified way of installing and configuring Istio that also comes with an in-built Kiali dashboard for traffic and telemetry visualization, Jaegar for tracing, and Prometheus/Grafana for observability.

## Additional Features
### Self-healing Nodes
In Rancher 2.3, users will be able to create node groups. We will integrate with our cloud providers to support the self-healing process of adding nodes which will let users define a minimum number of nodes. If the number of worker nodes goes below this threshold,  a new node could be started without manual intervention. Users may be able to define a maximum number of nodes as well. If a new node is added and it exceeds the total allowed nodes, the node creation will fail and an error message will be displayed (we will never kill an already running node automatically).
We may also add a webhook mechanism to support non-standard cloud provider API (the ones we don’t support out of the box) and/or bare-metal on-premise datacenter. 

