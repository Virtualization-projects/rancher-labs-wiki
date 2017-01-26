# Releases
---
Rancher supports 2 versions for Rancher server. 

* `rancher/server`: This build includes both the pre-release and stable versions of Rancher server.  Each pre-release build will be appended `*-pre{n}` suffix and will be shipped on a bi-weekly cadence leading up to the stable build release.  The pre-release builds are meant to allow the open source community to try out all the new features being developed for the current release rather than wait for the final release build.  Each pre-release build will support upgrades and will have passed all validation regression tests prior to shipping.  

* `rancher/enterprise`: This version is meant for users running Rancher for production workloads and is identical to the rancher/server stable build releases.  Patch releases will only be shipped on rancher/enterprise (and rancher/server:stable) builds to address critical and security issues.  We recommend Rancher users running production workloads to use this build so they do not accidentally upgrade to a pre-release build.

# Roadmap
---

Milestone |  Target Date | Release Plan |
---|---|---
1.2 | Mar 31, 2017 | [Rancher 1.5](https://github.com/rancher/rancher/wiki/Rancher-1.5.0)
1.4 | Feb 28, 2017 | [Rancher 1.4](https://github.com/rancher/rancher/wiki/Rancher-1.4.0)
1.3 | Jan 3, 2017 | [Rancher 1.3](https://github.com/rancher/rancher/wiki/Rancher-1.3.3)
1.2 | Nov 24, 2016 | [Rancher 1.2](https://github.com/rancher/rancher/wiki/Rancher-1.2.0)
1.1 | June 30, 2016 | [Rancher 1.1](https://github.com/rancher/rancher/wiki/Rancher-1.1.2)
1.0 | March 28, 2016 | [Rancher 1.0](https://github.com/rancher/rancher/wiki/Rancher-1.0.0)
