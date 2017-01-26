# Releases
---
Rancher supports 2 images for Rancher server. 

* `rancher/server`: This image has 2 different tags. 
 * `rancher/server:latest` tag will be our latest development builds. These builds will have been validated through our CI automation framework. These releases are not meant for deployment in production.
 * `rancher/server:stable` tag will be our latest stable release builds. This tag is the version that we recommend for production.  
  

* `rancher/enterprise`: This version is meant for users running Rancher for production workloads and is identical to the `rancher/server:stable` build releases.  Patch releases will only be shipped on rancher/enterprise (and rancher/server:stable) builds to address critical and security issues.  We recommend Rancher users running production workloads to use this build so they do not accidentally upgrade to a pre-release build.

# Roadmap
---

Milestone |  Target Date | Release Plan |
---|---|---
1.5 | Feb 28, 2017 | [Rancher 1.5](https://github.com/rancher/rancher/wiki/Rancher-1.5.0)
1.4 | Jan 31, 2017 | [Rancher 1.4](https://github.com/rancher/rancher/wiki/Rancher-1.4.0)
1.3 | Jan 3, 2017 | [Rancher 1.3](https://github.com/rancher/rancher/wiki/Rancher-1.3.3)
1.2 | Nov 24, 2016 | [Rancher 1.2](https://github.com/rancher/rancher/wiki/Rancher-1.2.3)
1.1 | June 30, 2016 | [Rancher 1.1](https://github.com/rancher/rancher/wiki/Rancher-1.1.4)
1.0 | March 28, 2016 | [Rancher 1.0](https://github.com/rancher/rancher/wiki/Rancher-1.0.2)
