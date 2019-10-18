# Release Goals

Rancher 2.4 continues the journey of bringing multi-cluster management and a consistent managed Kubernetes everywhere. 

# Improved Rancher Scalability. 

The proliferation of Kubernetes clusters from branch, data center, cloud and edge.  Rancher 2.4 aims to manage 10,000 clusters and 100,000 nodes attached to a single control plane. 

# Rancher HA simplification

Leveraging the flexibility and simplicity of Rancher K3S, Rancher 2.4 HA will ship as an appliance. There will no longer be a requirement to run Rancher HA on a Kubernetes cluster or dependency on Docker. Just install a single Rancher binary, with a systemd unit file, on two or more nodes pointed at a database will be all that is needed. 

# Zero Downtime RKE Cluster Upgrades

Rancher 2.4 is bringing zero downtime cluster upgrades to  your RKE clusters. Starting in 2.4 cluster operators will have control over when etcd, control plane and worker nodes are upgraded. Application workloads will stay running as nodes are upgraded in configurable batch sizes. Leveraging Kubernetes, all system components deployed through add ons will maintain availability throughout the upgrade process. 

# CIS Compliance Scanning and Alerting

The industry has agreed the CIS Kubernetes benchmark is the standard for hardening a Kubernetes cluster. In Rancher 2.4, cluster operators can configure CIS benchmark scans of their clusters to ensure compliance. The results can be shared with auditors and security teams for verification of compliance. Operators can be alerted if any node falls out of compliance within the cluster. 

# Additional Features

Authorization improvements:
* Admins can create and customize global roles within Rancher.
* Rancher admins can assign groups to global roles. This will allow for the Rancher Admin role to be assigned to a group.
