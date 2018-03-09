# Releases
---
Rancher supports 2 images for Rancher server. 

* `rancher/server`: This image has 2 different tags. 
 * `rancher/server:latest` tag will be our latest development builds. These builds will have been validated through our CI automation framework. These releases are not meant for deployment in production.
 * `rancher/server:stable` tag will be our latest stable release builds. This tag is the version that we recommend for production.  
  

* `rancher/enterprise`: This version is meant for users running Rancher for production workloads and is identical to the `rancher/server:stable` build releases.  Patch releases will only be shipped on rancher/enterprise (and rancher/server:stable) builds to address critical and security issues.  We recommend Rancher users running production workloads to use this build so they do not accidentally upgrade to a pre-release build.

# Roadmap
---
**Notes: Rancher is currently working on v2.0 slated to ship in 2018.  Until it becomes available, 1.6.x will be our recommended stable build for all production workload.  The Rancher team will continue to ship a regular monthly release of 1.6.x that includes critical bugs fixes as well as smaller features as required by our community.  All 1.6.x milestones will be tracked under our [milestone](https://github.com/rancher/rancher/milestones) page labeled as a monthly release.**

Milestone |  Target Date | Release Plan |
---|---|---
2.0 | End Apr, 2018 | [Rancher 2.0](https://github.com/rancher/rancher/wiki/Rancher-2.0)
1.6 | May 4, 2017 | [Rancher 1.6](https://github.com/rancher/rancher/wiki/Rancher-1.6)
1.5 | Feb 28, 2017 | [Rancher 1.5](https://github.com/rancher/rancher/wiki/Rancher-1.5)
1.4 | Jan 31, 2017 | [Rancher 1.4](https://github.com/rancher/rancher/wiki/Rancher-1.4)
1.3 | Jan 3, 2017 | [Rancher 1.3](https://github.com/rancher/rancher/wiki/Rancher-1.3)
1.2 | Nov 24, 2016 | [Rancher 1.2](https://github.com/rancher/rancher/wiki/Rancher-1.2)
1.1 | June 30, 2016 | [Rancher 1.1](https://github.com/rancher/rancher/wiki/Rancher-1.1)
1.0 | March 28, 2016 | [Rancher 1.0](https://github.com/rancher/rancher/wiki/Rancher-1.0)
