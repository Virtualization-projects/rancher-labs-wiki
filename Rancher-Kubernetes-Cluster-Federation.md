### Prerequisites

 * 1 or more kubernetes environments. The various cluster's apiservers should be able to the federation-apiserver.
 * The clusters should run rancher-k8s v1.4.6 or more
 
### Provision Federated Control Plane

The federated control plane must run in a Kubernetes host cluster which has access to a set of cluster configurations and secrets for accessing them. Let's call this cluster the host cluster. 

In the host cluster, perform the following operations

 1. Create a federation namespace
  `kubectl create namespace federation`
          
 2. Create a federated apiserver LB service
  `kubectl create -f federated-apiserver-lb-service.yml`
  
  the file federated-apiserver-lb-service should contain the following config
  ```
  apiVersion: v1
  kind: Service
  metadata:
    name: federation-apiserver
    namespace: federation
    labels:
      app: federated-cluster
  spec:
    type: LoadBalancer
    selector:
      app: federated-cluster
      module: federation-apiserver
    ports:
      - name: https
        protocol: TCP
        port: 443
        targetPort: 443
  ```

  Wait until the `EXTERNAL-IP` is populated as it will be required to configure the federation-controller-manager.

  `kubectl --namespace=federation get services`

  ```
  NAME                   CLUSTER-IP      EXTERNAL-IP    PORT(S)   AGE
  federation-apiserver   10.119.242.80   XX.XXX.XX.XX   443/TCP   1m
  ```

 3. Create the federation-apiserver secrets
 
  `kubectl --namespace=federation create secret generic federation-apiserver-secrets --from-literal=token.csv=${PASSWORD},admin,admin`

  Ensure that you replace `${PASSWORD}` with a password of your choice

 4. Deploy the federation-apiserver
 
  The federation api-server relies on etcd to store its config. You can choose to use the existing etcd or a new one. In this example, I've used a new etcd service for the federation-apiserver

  Extract the DeploymentSpec for the federation-apiserver

  ```
  curl -sSL https://gist.githubusercontent.com/wlan0/b55cf6f9f985af302a6fbb188f173e82/raw/ae6ff58ef6ddd80a8df0032e33fb5559c7acadbb/Rancher%2520Kubernetes%2520Cluster%2520Federation.txt > apiserver-deployment.yml
  ```

  If you wanted to build your own images instead of using the official ones (a required step if you're not running on GCE), then follow the step provided here - https://gist.github.com/wlan0/9f1d1e71d4f36f3fae693c976fbb3ea4

  Use the External-IP from Step 2, and Replace "ADVERTISE_ADDRESS" in the Deployment Spec with this IP address.

  ```
    FEDERATED_API_SERVER_ADDRESS=$(kubectl --context="gke_${GCP_PROJECT}_us-central1-b_gce-us-central1" \
--namespace=federation \
get services federation-apiserver \
-o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    sed -i "" "s|ADVERTISE_ADDRESS|${FEDERATED_API_SERVER_ADDRESS}|g" apiserver-deployment.yml
  ```

  Start the apiserver

  ```
    kubectl  --namespace=federation create -f apiserver-deployment.yml
  ```

  Verify that the federation-apiserver is actually running

  ```
    kubectl --namespace=federation get deployments
    NAME                   DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    federation-apiserver   1         1         1            0           7s

    kubectl --namespace=federation get pods
    NAME                                   READY     STATUS    RESTARTS   AGE
    federation-apiserver-116423504-4mwe8   2/2       Running   0          13s
   ```

 5. Provision the federation-controller-manager
 
  First, create the kubeconfig file for the federated-apiserver

  ```
    FEDERATED_API_SERVER_ADDRESS=$(kubectl --namespace=federation get services federation-apiserver -o jsonpath='{.status.loadBalancer.ingress[0].ip}')
    kubectl config set-cluster federation-cluster  --server=https://${FEDERATED_API_SERVER_ADDRESS} --insecure-skip-tls-verify=true
  ```

  Then, set the credentials using the same password you used above

  ```
    kubectl config set-credentials federation-cluster --token=${PASSWORD}
    kubectl config set-context federation-cluster --cluster=federation-cluster --user=federation-cluster
  ```

  Then switch to the `federation-cluster` context and dump the credentials needed for the controller-manager into a file

  ```
    kubectl config use-context federation-cluster
    kubectl  config view --flatten --minify > kubeconfig
    # Note that it is important that the file name is kubeconfig. This name is used as key while creating a secret
  ```

  Now, switch back to Default config and create a secret needed for the controller manager

  ```
   kubectl config use-context Default
   kubectl --namespace=federation create secret generic federation-apiserver-kubeconfig --from-file=kubeconfig
  ```
  Then deploy the federation-controller-manager

  ```
curl -sSL https://gist.githubusercontent.com/wlan0/f55f3674e3ab1e8bcc21ee16a2cdebcb/raw/53a041e5e7530d4e4a6b41343528bc39612d0e68/Kubernetes%2520Cluster%2520Federation%2520Federation%2520Controller-manager%2520Deployment%2520Spec.txt > controller-manager-deployment.yml
  kubectl --namespace=federation create -f controller-manager-deployment.yml
  ```
  Note that you'll need to update the spec above to use your own image (create it with the instruction above) if you're not running in GCE.
  
### Register clusters with the federation cluster  
  
  Add Host cluster to federation cluster

  Firstly, create the kubeconfig secret for host cluster

  Ensure that your context is set to Default and extract its kubeconfig
  ```
    kubectl config use-context Default
    kubectl  config view --flatten --minify > kubeconfig
    sed -i "" "s|https|http|g" kubeconfig
    kubectl --namespace=federation create secret generic Default --from-file=kubeconfig
  ```

  Now, create the cluster spec like this

  ```
    cat > default-cluster.yml << EOF
      apiVersion: federation/v1beta1
      kind: Cluster
      metadata:
        name: Default
      spec:
        serverAddressByClientCIDRs:
          - clientCIDR: "0.0.0.0/0"
            serverAddress: "${Host_Kubernetes_Server}" # looks like http://XX.XX.XX.XX:8080/r/projects/1a5/kubernetes
        secretRef:
          name: Default
    EOF
    sed -i "" "s|https|http|g" kubeconfig
  ```

  Now, Add the cluster 

  ```
    kubectl --context=federation-cluster create -f default-cluster.yml
  ```

  Verify that you've added the cluster

  ```
  kubectl --context=federation-cluster get clusters

  NAME               STATUS    VERSION   AGE
  Default             Ready               1m
  ```

Now you have the federated cluster up and running, you can add federated services. You can add more clusters by following the same steps as above

### Provisioning federated services

  Create this replica set across clusters. First write this spec to a file

  ```
  cat > federated-service.yml << EOF
  apiVersion: extensions/v1beta1
  kind: ReplicaSet
  metadata:
    name: nginx
  spec:
    replicas: 4
    template:
      metadata:
        labels:
          app: nginx
      spec:
        containers:
          - name: nginx
            image: nginx:1.10
            resources:
              requests:
                cpu: 100m
                memory: 100Mi
  EOF
  ```

 Then deploy it across clusters

 ```
  kubectl --context=federation-cluster create -f federated-service.yml
 ```

 Verify that it is up and running

 ```
  kubectl --context=federation-cluster get rs
  NAME      DESIRED   CURRENT   AGE
  nginx     4         0         13m
 ```
 
### Configuring DNS provider
 
Kubernetes federated services have the ability to manage external DNS entries based on services created across a federated set of Kubernetes clusters. More information is available here - http://kubernetes.io/docs/user-guide/federation/federated-services/#verifying-public-dns-records