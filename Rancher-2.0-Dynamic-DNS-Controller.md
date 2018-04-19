The dynamic DNS controller creates the necessary DNS entries to make the following endpoint URLs work:

<http/https>://<$ingress_name>.<$namespace>.<$cluster_id>.<rancher_root_domain>/<$path>

<h1>Dynamic DNS Controller</h1>

The dynamic DNS controller watches all the ingress resources in the cluster and creates the following DNS entries in <rancher_root_domain>:

<$ingress_name>.<$namespace>.<$cluster_id>.<rancher_root_domain> => [ingress IPs]

When an ingress resource has more than 10 IPs, only 10 IPs will be returned by DNS.

In an RKE cluster, when a node becomes unhealthy and the corresponding nginx ingress resource becomes unavailable, the dynamic DNS controller updates the DNS mapping to remove that node IP from the list.

<h1>Rancher Dynamic DNS Service</h1>

The Rancher DNS server needs to implement the following API:

| Operation   |  URL  |  Method/JSON Payload |
|-----------|------|------|
| Create Domain	| /v1/domain	| POST {"members": ["1.1.1.1", "2.2.2.2"]} Returns: &lt;Token&gt; &lt;FQDN&gt; &lt;Expiration time&gt; |
| Get Domain	| /v1/domain/&lt;FQDN&gt; | GET 
| Renew Domain	| /v1/domain/&lt;FQDN&gt;/renew	| PUT Returns: &lt;New expiration time&gt; |
| Update Domain	| /v1/domain/&lt;FQDN&gt;	| PUT {"member": ["1.1.1.1", "2.2.2.2"]} |
| Delete Domain	| /v1/domain/&lt;FQDN&gt;	| DELETE

The token received in the create domain call should be passed as a bearer token in all subsequent calls to update and delete. The implementation of the DNS service should leverage something like CoreDNS or PowerDNS.

DNS Settings

| Name | Value |
| ----- | ---- |
| &lt;rancher_root_domain&gt; | lb.rancher.cloud |
| TTL | 1 minute |

<h1>TLS Support</h1>
For TLS,  cluster configs letsencrypt, requests  *.clusterid.lb.rancher.cloud, and sends Rancher dynamic DNS service the challenge text record to add for the domain.

<h1>Airgap install</h1>
The Dynamic DNS controller can be disabled in airgap installs. In that case, Ingress will revert to the current behavior.
