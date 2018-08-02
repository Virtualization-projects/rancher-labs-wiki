The dynamic DNS controller creates the necessary DNS entries to make the following endpoint URLs work:

<http/https>://<$ingress_name>.<$namespace>.<$cluster_id>.<rancher_root_domain>/<$path>

[[images/integration-tests/044.png]]

<h1>Dynamic DNS Controller</h1>

The dynamic DNS controller watches all the ingress resources in the cluster and creates the following DNS entries in <rancher_root_domain>:

<$ingress_name>.<$namespace>.<$cluster_id>.<rancher_root_domain> => [ingress IPs]

When an ingress resource has more than 10 IPs, only 10 IPs will be returned by DNS.

In an RKE cluster, when a node becomes unhealthy and the corresponding nginx ingress resource becomes unavailable, the dynamic DNS controller updates the DNS mapping to remove that node IP from the list.

Every once in a while (default 5m), the dynamic DNS controller will resync nginx ingress resource.

Every once in a while (default 24h), the dynamic DNS controller will renew the domain to refresh Expiration.

Default Settings

| Name | Value |
| ----- | ---- |
| Default Ingress Resync Duration | 5 minute |
| Default Domain Renew Duration | 24 hour |


<h1>Rancher Dynamic DNS Service</h1>

The Rancher DNS server needs to implement the following API:

| Operation   |  URL  |  Method/JSON Payload |
|-----------|------|------|
| Create Domain	| /v1/domain	| POST <br> <br> Payload: {"hosts": ["1.1.1.1", "2.2.2.2"]} <br> <br> Returns: {"status":200,"msg":"", "token": "abcfdssadasd", "data":{"fqdn":"11111.lb.rancher.cloud","hosts":["1.1.1.1","2.2.2.2"],"expiration":"2018-04-29T10:08:12.906557355Z"}}|
| Get Domain	| /v1/domain/&lt;FQDN&gt; | GET <br> <br> Returns: {"status":200,"msg":"","data":{"fqdn":"12345678.lb.rancher.cloud","hosts":["1.1.1.1","2.2.2.2"],"expiration":"2018-04-29T09:46:18.078663181Z"}}
| Renew Domain	| /v1/domain/&lt;FQDN&gt;/renew	| PUT <br> <br>[Header] Token: Bearer XXX <br> <br> Returns:  {"status":200,"msg":"","data":{"fqdn":"12345678.lb.rancher.cloud","hosts":["1.1.1.1","2.2.2.2"],"expiration":"2018-05-29T09:46:18.078663181Z"} |
| Update Domain	| /v1/domain/&lt;FQDN&gt;	| PUT <br> <br>[Header] Token: Bearer XXX <br> <br> Payload: {"hosts": ["3.3.3.3", "4.4.4.4"]} <br> <br> Returns {"status":200,"msg":"","data":{"fqdn":"12345678.lb.rancher.cloud","hosts":["3.3.3.3","4.4.4.4"],"expiration":"2018-04-30T01:03:40.870506511Z"}} |
| Delete Domain	| /v1/domain/&lt;FQDN&gt;	| DELETE <br> <br>[Header] Token: Bearer XXX

The token received in the create domain call should be passed as a bearer token in all subsequent calls to update and delete. The implementation of the DNS service should leverage something like CoreDNS or PowerDNS.

DNS Settings

| Name | Value |
| ----- | ---- |
| &lt;rancher_root_domain&gt; | lb.rancher.cloud |
| TTL | 1 minute |

<h1>Configuring Dynamic DNS Service</h1>

Configuring the following parameters in the global variable settings.

| Name | Value |
| ----- | ---- |
| &lt;ingress-ip-domain&gt; | lb.rancher.cloud | 

1.Modify <$ingress-ip-domain> to a specified domain: `lb.rancher.cloud`

2.Create ingress resource and select the following:
[[images/integration-tests/045.png]]

<h1>TLS Support</h1>
For TLS,  cluster configs letsencrypt, requests  *.clusterid.lb.rancher.cloud, and sends Rancher dynamic DNS service the challenge text record to add for the domain.