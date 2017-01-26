# Schedule

* Shipped: 2016-11-30

# Release Notes

* [1.2.2 Release Notes](https://github.com/rancher/rancher/releases/tag/v1.2.3)
* [1.2.2 Release Notes](https://github.com/rancher/rancher/releases/tag/v1.2.2)
* [1.2.1 Release Notes](https://github.com/rancher/rancher/releases/tag/v1.2.1)
* [1.2.0 Release Notes](https://github.com/rancher/rancher/releases/tag/v1.2.0)

# Pre-release builds

* [1.2.0-pre4](https://github.com/rancher/rancher/milestone/79)
* [1.2.0-pre3 Release Notes](https://github.com/rancher/rancher/releases/tag/v1.2.0-pre3)
* [1.2.0-pre2 Release Notes](https://github.com/rancher/rancher/releases/tag/v1.2.0-pre2)
* [1.2.0-pre1 Release Notes](https://github.com/rancher/rancher/releases/tag/v1.2.0-pre1)

# Release Goals
We have an aggressive [list of features](https://github.com/rancher/rancher/labels/kind%2F1.2-feature)
 we plan for Rancher 1.2.0.  Here are some of the highlights:

* **[#5201](https://github.com/rancher/rancher/issues/5201): Docker built-in orchestration as a framework**
  * With the announcement of Docker's new built-in orchestration framework, Rancher will support the ability to select it as a framework when creating an environment and still leverage Rancher's catalog system, access control, and infrastructure management.
* **[#5267](https://github.com/rancher/rancher/issues/5267): Integration with Container Network Interface (CNI)**
  * With more and more network vendors creating plugins to support the CNI specification, Rancher will now support CNI (and by extension allow support in both kubernetes and mesos) natively.  Users will be able to continue to use Rancher's own ipsec overlay networking or the option to "bring-your-own-network."
* **[#4576](https://github.com/rancher/rancher/issues/4576): Windows container support - Experimental**
  * Microsoft plans to ship Docker container support as part of Windows 2016 in the later half of 2016 and is already current in beta.  With Rancher 1.2.0, users will have an experimental feature to launch and managed containers in Windows.
* **[5278](https://github.com/rancher/rancher/issues/5278): Longhorn storage integration - Experimental**
  * Longhorn (see https://github.com/rancher/longhorn) is a project that was created to solve being able to manage stateful containers and provide the ability to leverage local disks on each host to support volume mirroring, snapshotting, and backups to either NFS or object store like S3.