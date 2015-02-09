## Purpose

As a developer works on a new feature there are times when deploying the code for debugging, review or observation is necessary. This document walks through deploying code from git repositories.

## Pre-requisites

### Locally
* Docker 1.4.1+

### On Google Compute Engine
* 10acre-ranch tool.

## Deploying

### Locally
```
docker run -d --privileged -p 8080:8080 --name=rancher-dev rancher/build-master
```

### 10Acres on GCE
```
gce-10acre-ranch -s rancher/build-master -p server -c rancher-dev -n 1 -w
```

The command above will create a Rancher cluster with 1 master and 1 worker node on separate VMs in Google Cloud. Note: Running validation-tests requires a 3 node cluster. Launch with `-n 3`.

## Changing source
By default the repositories are configured via environment variable. These are impractical to manipulate in a development workflow. To accommodate development, there is a manual override allowing git commands to manage the directories.

### Directories
```
$ ls -l /opt/cattle
drwxr-xr-x  9 root root 4096 Feb  9 17:56 ./
drwxr-xr-x  4 root root 4096 Feb  9 05:47 ../
drwxr-xr-x  4 root root 4096 Feb  9 05:47 build-tools/
drwxr-xr-x  9 root root 4096 Feb  9 17:56 cattle/
drwxr-xr-x  6 root root 4096 Feb  9 17:56 node-agent/
drwxr-xr-x  7 root root 4096 Feb  9 17:56 python-agent/
drwxr-xr-x  2 root root 4096 Feb  9 05:47 scripts/
drwxr-xr-x 11 root root 4096 Feb  9 17:56 ui/
drwxr-xr-x  5 root root 4096 Feb  9 17:57 validation-tests/
```
Note: the UI is only managed here so that we can see the source. Cattle IS NOT using this directory.

### Overriding
In the folder for the repository you wish to change you need to `touch .manual` in that directory. To override cattle for instance you would: `touch /opt/cattle/cattle/.manual`

At this point you can add a git remote and pull in your code. Once the source you want is all set up, restart the docker container. `docker restart <container>`


## Under the hood

The build-master container is a Docker in Docker container that performs git checkouts from configured projects, and using the [rancherio/build-tools](https://github.com/rancherio/build-tools.git) builds all of the source. The Cattle project is configured via Environment variables to use the directories local to container instead of the standard global-properties file. 

The entry point of the container is `/opt/cattle/scripts/run` this script calls git-manager and build-projects scripts. 

If you want to run the validation tests, it is best to create a separate container with --volumes-from.
