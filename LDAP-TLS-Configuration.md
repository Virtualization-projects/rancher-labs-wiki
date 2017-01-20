## How to Configure Rancher with LDAP and TLS

In order to use Rancher with LDAP and TLS, you must ensure that the correct certificates are in the java keystore. If you are using a ssl certificate from a known CA (ex: Godaddy), no additional configuration is needed to start Rancher. 

If you are using a self signed cert or a private CA, you must start Rancher with the cert the LDAP server uses.

**Self Signed Certificate**
 
* Place the self signed certificate in ```/some/dir``` and the cert must be named ```cert.crt```.
* Start Rancher server by adding a command to bind mount the volume, that has the certificate.

  ```docker run -d -v /some/dir/cert.crt:/var/lib/rancher/etc/ssl/ca.crt --restart=always -p 8080:8080 rancher/server:v0.33.0-rc3```

**Private CA**
* Place the certificate for the CA in ```/some/dir``` and the cert must be named ```cacert.crt```
* Start Rancher server by adding a command to bind mount the volume, that has the certificate.

     ```docker run -d -v /some/dir/cacert.crt:/ca.crt --restart=always -p 8080:8080 rancher/server:v0.33.0-rc3```

## Enabling LDAP in Rancher using the UI

Once you've started Rancher with the correct certificates, it's time to enable access control using LDAP. 

### Configuring your LDAP server

Enter the information for your LDAP server. Here are some additional notes for specific fields.

* **Port**: `389` is the standard port for insecure. `636` is the standard port for TLS. If you select port `636`. then please make sure to check off the TLS box.
* **Search Base:** Please make sure this matches the Distinguishedname (dn) for the search base.


### Customizing your Schema

In this example, we've set up the standard Active Directory format.

**Users**
* **Object Class:** person
* **Login Field:** sAMAccountName
* **Name Field:** name
* **Search Field:** name   _Note: You could search by sAMAccountName._
* **Status Field**: userAccountControl
* **Disabled Status BitMask:** 2

**Groups**
* **Object Class:** group
* **Name Field:** name
* **Search Field:** name _Note: You could search by sAMAccountName._

### Authenticate

After you've entered all your information, the last step is to authenticate your username/password. 

## Sample API Configuration

You can also use the Rancher'a API to enable LDAP, here's a sample configuration. To read more about the API fields for LDAP, please read this [documentation](https://github.com/rancher/rancher/wiki/Ldap-Auth-Configuration).

```
{
"id": null,
 "type":
"ldapconfig",
"links": { },
"actions": { },
"accessMode": null,
"configured": true,
"domain": "DC=rancher,dc=io",
"enabled": true,
"groupNameField": "name",
"groupObjectClass": "group",
"groupSearchField": "name",
"loginDomain": "rancher",
"name": "ldapconfig",
"port": 636,
"server": "ad.rancher.io",
"serviceAccountPassword": "password",
"serviceAccountUsername": "cattle",
"tls": true,
"userDisabledBitMask": 2,
"userEnabledAttribute": "userAccountControl",
"userLoginField": "sAMAccountName",
"userNameField": "name",
"userObjectClass": "person",
"userSearchField": "name",
}
```