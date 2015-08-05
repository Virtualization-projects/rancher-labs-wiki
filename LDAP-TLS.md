#Tls Certificates

##Purpose: Configure Rancher with LDAP and TLS

In order to use Rancher with LDAP and TLS you must make sure that the right certificates are in the java keystore.

If using a ssl certificate from a known CA (ex: Godaddy) no configuration should be needed before starting Rancher.

If using a self signed cert or a private CA you must start Rancher with the cert the ldap server uses. 
* Using a self signed cert place the certificate in ```/some/dir``` and the cert named ```cert.crt```
    * Start rancher server with this command ```docker run -v /some/dir/cert.crt:ldap.pem {other args} rancher/server```
* Using a private CA place the certificate for the CA in ```/some/dir``` and the cert named ```cacert.crt```
    * Start rancher server with this command ```docker run -v /some/dir/cacert.crt:ldap.pem {other args} rancher/server```