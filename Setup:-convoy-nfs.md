Basis of that short tutorial / explanation is this [online Meetup](https://www.youtube.com/watch?v=up0s-J-Qi3s) from DEC 2015.

required:
- running rancher-server + hosts

things you'll need (mentioned in the meetup):
- rancher-compose
- https://github.com/ibuildthecloud/demo-compose-templates/tree/master/lychee-docker/nfs

you need three parameters to instal convoy-nfs on the already installed nfs-server
mount dir, nfs server (ip or hostname), mount options

the mount dir is not /exports but / (in nfs4)
mount_dir: /
nfs_server: xx.xx.xx.xx

and you need to specify the nfs version (nfsvers=4)
mount_opts: proto=tcp,port=2049,nfsvers=4


without declaring nfsvers=4 you get the error that you need statd
```
mount.nfs: rpc.statd is not running but is required for remote locking.
mount.nfs: Either use '-o nolock' to keep locks local, or start statd.
mount.nfs: an incorrect mount option was specified
```

and with /exports on nfs4 you get that error message:

```mount.nfs: mounting 10.42.84.90:/exports failed, reason given by server: No such file or directory```