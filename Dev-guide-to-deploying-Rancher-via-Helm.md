_This is not an official install guide. It should only be used for development purposes, __not production__._

This is a guide for deploying Rancher via helm either on your local machine or to a cloud provider. You may want to do this for QA purposes, or to test development code changes (for which you'll need to deploy locally),
or to test changes to our helm chart. 

### Setup

* Make sure you are using the official helm, not Rancher's fork of helm.
* To run locally you can use docker desktop's Kubernetes or [K3s](https://github.com/rancher/k3s) (untested).
* If using docker desktop you should bump up your CPU's and memory.
* If using a cloud provider like Linode, you'll need to provision K8's first. You can use [RKE](https://rancher.com/docs/rke/latest/en/installation/) for that.

### Using local changes by building a docker image

If you have made local code changes that you wish to deploy locally: 

* Go to scripts/ci and comment out `./test` to make things faster
* Run `make ci`, this will take a while and build a `rancher/rancher:dev` image
* Now if you deploy locally it can pull that docker image
* Any subsequent code changes will require a rebuild of the docker image

### Helm deployment

The below guide will roughly follow [the HA install doc](https://rancher.com/docs/rancher/v2.x/en/installation/ha/helm-rancher/).

If something goes wrong you can start over with: `helm del --purge rancher`

Create a new kube cluster and setup kubectl with it. Then init tiller and make sure it is running: 

    helm init
    kubectl get pod --namespace kube-system | grep tiller

Now install cert-manager. See troubleshooting section for errors encountered here.


    helm install stable/cert-manager  --name cert-manager  --namespace kube-system  --version v0.5.2
    kubectl -n kube-system rollout status deploy/cert-manager

From your rancher dir, change versions and appVersion in rancher/chart/chart.yaml to `"dev"`, then:

    helm install --dry-run ./chart -n rancher
    helm install ./chart -n rancher --namespace cattle-system --set hostname=localhost
    kubectl -n cattle-system rollout status deploy/rancher
    # Wait for this to complete...

Now expose deployment. For a local deploy you should be able to use an LB: 

    kubectl -n cattle-system expose deployment rancher --name=lb443 --port 443 --target-port=443 --type=LoadBalancer  # you may want --port to be 8443 here to mimic local rancher setup
    kubectl -n cattle-system get services lb443
    Now you should see an 'External-IP' on the load balancer which you can now hit. Docker desktop will use localhost.

For a cloud host like Linode you can use nodeport:  

    kubectl -n cattle-system expose deployment rancher --name=np443 --port 443 --target-port=443 --type=NodePort
    kubectl -n cattle-system get services np443
    # Now you can hit the service by hitting the node's IP address and node port the last command output, something like 11.22.33.44:30615

### Troubleshooting

**Helm Error: namespaces "kube-system" is forbidden**

You'll need to create a service account for tiller, see: https://github.com/fnproject/fn-helm/issues/21

	kubectl create serviceaccount --namespace kube-system tiller
	kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
	kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
	# wait till tiller reloads

