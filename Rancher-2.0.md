# Schedule

* Scheduled: 2017 Q4

# Milestones
* 2.0-tech-preview 

# Release Goals
Rancher 2.0 will be the most significant release since 1.0 was announced over a year ago. The following describes some of the innovations and improvements we are expecting to launch.  While we are ambitious in what we plan to do, the final feature set can change based on ideas and feedback from our user base.  Unlike 1.x, we do plan to ship regular tech preview builds so that our community has a chance to look at what we are building and help shape the release.

* __New user interface-__ We are refreshing the existing UI with a new and improved look and feel.  The new UI will be much more responsive and will incorporate the feedback we have received with the UX since 1.0 went GA last year.
* __More k8s improvements-__ In 1.x, Rancher made it easier for users to deploy and manage the lifecycle of kubernetes environments.  With 2.0, we plan to add more features on top of k8s that will make k8s even simpler to operate and use.  Stay tuned for more...
* __General improvements to scale, performance, and resiliency of the Rancher server-__ Enhancements have already been added in 1.6.x, but we plan to do more to push the number of hosts and containers that can be managed to greater numbers.
* __More network enhancements-__ We are planning to add improvements to ipsec reliability, vxlan performance, and to support non-overlay networking options for users that want to bridge their own network.
* __Shared hosts within an environment-__ Multi-tenancy within Rancher was always achieved by isolating access between environments.  We are looking to add multi-tenant support within an environment so multiple users can use the same compute nodes for containers but not share the same management and access rights to those containers.

# Release Goals (Stretch)
We are also working on a few stretch goals for 2.x that will come out after 2.0 has shipped.  
* Integrated CI/CD pipeline using Jenkins
* Integrated monitoring and alert management using Prometheus
* Integrated aggregated and centralized logging
