Ldap Authentication Integration
---------
Purpose: To be able to authenticate to rancher using ldap credentials. Add users to the site based on their ldap username or groups.
Add users to a project based on their ldap user or groups.

After configuring ldap you are able to login to rancher using ldap username and password. Then you use rancher the same
as normal except that when creating projects you use ldap users and groups not github users ors or teams.

---------

In order to configure ldap you must specify the following by posting to /v1/ldapConfig:

 * **accessMode**  (restricted or unrestricted)
 * **domain** (domain with in ldap to use. ex ad.example.com)
 * **loginDomain** 
    * default domain to login using
    * ex User1 becomes loginDomain\User
    * ex foo\User1 stays foo\User1
 * **port** (port ldap is listening on. defaults to **389**)
 * **server** (server ip or domain that rancher can use to connect.)
 * **tls** (Use tls or not. (Tls not tested / implemented yet.))
 * **serviceAccountPassword** (password for the service account that has readonly access to all of ldap (All of ldap that you may need access too for authentication.))
 * **serviceAccountUsername** (username for the service account, same account as above)

--------------

 * Search fields
     * **searchFieldUser** (Field used on ldap object to search for a user defaults to (should this be multiple fields?) **sAMAccountName**)
     * **searchFieldGroup** (field used to search for group on defaults to (should this be multiple fields) **sAMAccountName**) ***Currently hard coded***
         * Ex: search on email name and/ or sAMAccountName
     * **objectTypeUser** (Value used to compare with *objectClass* to determine if an object is a user or not. Default **person**)
     * **objectTypeGroup** (Value used to compare with *objectClass* to determine if an object is a group or not. **group**)
 * **uniqueIdentifierField** (field used as the unique identifier for ldap Objects default **distinguishedname** this is what Identities use as externalId) ***Currently hard coded***
 * **nameFieldUser** (Name field on user objects default **name**) ***Currently hard coded***
 * **nameFieldGroup** (Name field on group objects default **name**) ***Currently hard coded***
 * **memberOfField** (Field used to check for items you are a member of default **memberOf**) ***Currently hard coded***  
 * **userEnabledMaskBit** (Bit to check to see if an account / user is enabled. default value is **514**) ***Currently hard coded***
 * **userEnabledAttribute** (Attribute to mask with the **userEnabledMaskBit** default value is **userAccountControl**) ***Currently hard coded***
     
 * **enabled** boolean determining if auth is enabled or not. 
 
 ***We only support direct membership currently.***
 
 **Subject to change**
 

 Example Group and user in the ldap domain ad.rancher.io:
 
 ![Group Ex:] (https://i.imgur.com/ybCDzyq.png)
 Given the above ldap Object for Group *Users* by default we would create the identity object:
 externalId : *CN=Users,CN=Builtin,DC=ad,DC=rancher,DC=io* grabbed from the distinguishedname field.
 name : *Users* grabbed from the name field.
 profileUrl : NONE what should we use?
 profilePicture : NONE what should we use/look for? Or make one up using the identicon api or perhaps robohash.org
 
 ![User Ex:] (https://i.imgur.com/6BIcwLf.png)
 
 Given the above ldap Object for User *test* by default we would create the identity object:
 externalId : *CN=Tester Testing,CN=Users,DC=ad,DC=rancher,DC=io* grabbed from the distinguishedname field.
 name : *Tester Testing* grabbed from the name field.
 profileUrl : NONE what should we use?
 profilePicture : NONE what should we use/look for? Or make one up using the identicon api or perhaps robohash.org
  
  
 These two objects are examples of what would be used to create a project member or grant access to the site in the rancher api.