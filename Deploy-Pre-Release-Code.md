# Purpose

As a developer works on a new feature there are times when deploying the code for debugging, review or observation is necessary. This document walks through deploying code from git repositories.

# Pre-requisites

### Locally
* Docker 1.4.1+

### On Google Compute Engine
* 10acre-ranch tool.

# Deploying

### Locally
```
docker run -d --privileged -p 8080:8080 --name=rancher-dev rancher/build-master
```

### 10Acres on GCE
```
gce-10acre-ranch -s rancher/build-master -p server -c rancher-dev -n 1 -w
```

The command above will create a Rancher cluster with 1 master and 1 worker node on separate VMs in Google Cloud. Note: Running validation-tests requires a 3 node cluster. Launch with `-n 3`.

