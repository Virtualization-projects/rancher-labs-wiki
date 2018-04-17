The dynamic DNS controller creates the necessary DNS entries to make the following endpoint URLs work:

<http/https>://<$ingress_name>.<$namespace>.<$cluster_id>.<rancher_root_domain>/<$path>

<h2>Dynamic DNS Controller</h2>

The dynamic DNS controller watches all the ingress resources in the cluster and creates the following DNS entries in <rancher_root_domain>:

<$ingress_name>.<$namespace>.<$cluster_id>.<rancher_root_domain> => [ingress IPs]

Where an ingress resource has more than 10 IPs, only 10 IPs will be returned by DNS.

In an RKE cluster, when a node becomes unhealthy and the corresponding nginx ingress resource becomes unavailable, the dynamic DNS controller updates the DNS mapping to remove that node IP from the list.

<h2>Rancher Dynamic DNS Service</h2>

The Rancher DNS server needs to implement the following API:

| Operation   |  URL  |  Method/JSON Payload |
|-----------|------|------|
| Create Domain	| /v1/domain	| POST {"members": ["1.1.1.1", "2.2.2.2"]} Returns: &lt;Token&gt; &lt;FQDN&gt; &lt;Expiration time&gt; |
| Get Domain	| /v1/domain/&lt;FQDN&gt; | GET 
| Renew Domain	| /v1/domain/&lt;FQDN&gt;/renew	| PUT Returns: &lt;New expiration time&gt; |
| Update Domain	| /v1/domain/&lt;FQDN&gt;	| PUT {"member": ["1.1.1.1", "2.2.2.2"]} |
| Delete Domain	| /v1/domain/&lt;FQDN&gt;	| DELETE

&lt;rancher_root_domain&gt; : in.rancher.space
TTL: 1 minute

<h2>Airgap install</h2>
The Dynamic DNS controller can be disabled in airgap installs. In that case, Ingress will revert to the current behavior.

