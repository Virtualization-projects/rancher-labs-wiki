The supported/endorsed IDE for Rancher is Goland. It's the only one that the dev team will document, but (obviously) you can hack on Rancher in whatever editor/IDE you want.

### Steps to debug Rancher using an external k8s in Goland
1. Create a run/debug configuration from main.go
2. Open the configuration and add this to the Go Tool arguments: `-i -gcflags="-N -l"`. This disables optimiztions and inlining, which is important for debugging with breakpoints properly
3. Before running, delete the the `pkg` dir at the root of your rancher project's GOPATH. I'm not sure, but if you later compile rancher from the command line without those gcflags arguments, then you'll probably have to delete the pkg dir again before debugging again in Goland.

### Steps to use the "embedded" k8s in Goland
Assuming you already have a debug config setup using the above steps, then:
1. Settings->Go->Vendoring & Build Tags -> Custom Tags: Add "k8s"
2. Edit your Run/Debug Configuration
  a. Click "Use all custom build tags"
  b. do NOT set KUBECONFIG env var
Notes:
- You're compiler will explode the first time. Run it again.
- To switch back to external revert step 2.  step 1 doesn't matter much
- embedded logs  to /tmp/rancher.{INFO,WARNING,ERROR} for the internal k8s stuff
- when you do this setup k8s api server, rancher, and etcd all run in the same process.  that is lightly different than when running in docker.  in docker we fork a child for k8s api and etcd.  to get that behavior add `--k8s-mode=exec` as a command line argument