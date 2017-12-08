# Schedule

* Scheduled: 2017 Mid Q1

# Milestones
* 2.0-tech-preview 

# Release Goals
Rancher 2.0 will be the most significant release since 1.0 was announced over a year ago. The following describes some of the innovations and improvements we are expecting to launch.  While we are ambitious and confident in what we plan to do, the final feature set can change based on ideas and feedback from our user base.  Unlike 1.x, we do plan to ship regular tech preview builds so that our community has a chance to look at what we are building and help shape the release.

* __K8s cluster management-__ In 1.x, Rancher made it easier for users to deploy and manage the lifecycle of kubernetes environments.  With 2.0, we plan to add features such as centralized auth, centralized RBAC, and multi-k8s cluster management.
* __Use any k8s cluster-__ 2.0 will support the ability to create and operate your own k8s deployments using RKE (Rancher Kubernetes Engine) or import any existing k8s clusters.  It will also have native integration with GKE (Google Kubernetes Engine), AKS (Azure Container Service), and Amazon ECS (Amazon Elastic Container Service) so you can quickly get your k8s cluster up and running.
* __Workload management-__ 2.0 will introduce an alternative and simplified way to deploying your k8s applications.  You will still be able to interact with native k8s constructs but we will incorporated our easy-to-use UX experience in 1.6 to k8s. 
* __New user interface-__ We are refreshing the existing UI with a new and improved look and feel.  The new UI will be much more responsive and will incorporate the feedback we have received with the UX since 1.0 went GA last year.
* __General improvements to scale, performance, and resiliency of the Rancher server-__ Enhancements have already been added in 1.6.x, but we plan to do more to push the number of hosts and containers that can be managed to greater numbers.
* __More network enhancements-__ We are planning to add improvements to ipsec reliability, vxlan performance, and to support non-overlay networking options for users that want to bridge their own network.  We will also be adding both flannel and Calico support.

# Release Goals (Stretch)
We are also working on a few stretch goals for 2.x that will come out after 2.0 has shipped.  
* Integrated CI/CD pipeline using Jenkins
* Integrated monitoring and alert management using Prometheus
* Integrated aggregated and centralized logging
