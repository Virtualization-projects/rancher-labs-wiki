Ldap Authentication Integration
---------
Purpose: To be able to authenticate to rancher using ldap credentials. Add users to the site based on their ldap username or groups.
Add users to a project based on their ldap user or groups.

After configuring ldap you are able to login to rancher using ldap username and password. Then you use rancher the same
as normal except that when creating projects you use ldap users and groups not github users ors or teams.

##Conecting to ldap
 * **loginDomain** 
    * default domain to login using
    * ex User1 becomes loginDomain\User
    * ex foo\User1 stays foo\User1
 * **port** (port ldap is listening on. defaults to **389**)
 * **server** (server ip or domain that rancher can use to connect.)
 * **tls** (Use tls or not. (Tls not tested / implemented yet.))
 * **enabled** boolean determining if auth is enabled or not. (If set to false ldap will not be used.)

###<a name="ldapAccess"></a>Ldap Account Access
 
These fields are used to determine who has access to rancher and who Rancher talks to ldap as when searching ldap.
 
 * **accessMode**  (restricted or unrestricted)
     * **domain** Used When in Unrestricted. (ex: ad.rancher.io)
     * **ous** Restricted mode. This is a list of Distinguished Names.
 * **serviceAccountPassword** (password for the service account that has readonly access to all of ldap (All of ldap that you may need access too for authentication.))
 * **serviceAccountUsername** (username for the service account, same account as above)
 


###Ldap Schema
 * **uniqueIdentifierField** (field used as the unique identifier for ldap Objects default **distinguishedname** this is what Identities use as externalId) ***Currently hard coded***

##Searching Users
These fields are used by rancher to determine how we identify an ldap Object as a user as well as look for users withing ldap.


 * **searchFieldUser** Attribute in ldap to use as the search field when searching for users. 
     * Default Value **sAMAccountName**
 * **objectTypeUser** Value of the attribute *objectClass* to determine that an object is a user.
     * Default value **person**
 * **nameFieldUser** Attribute rancher will use as the name of a user. 
     * Identity for a user will use the value of this attribute for the identity's name.
     * Default value **name** ***Currently hard coded***
 * <a name="memberOfField"></a>**memberOfField** Attribute used to check for membership. 
     * Default value **memberOf** ***Currently hard coded***
 * **userEnabledMaskBit** Bit to check to see if an account / user is enabled. 
     * Default value **514** ***Currently hard coded***
 * **userEnabledAttribute** Attribute to mask with the **userEnabledMaskBit** to determine if a user is enabled.
     * Default value **userAccountControl** ***Currently hard coded***
     
###Example
If using the defaults for all of the searching users fields a search for 

`user1` 

would result in a query to ldap like this 

`(&(objectClass=person)(sAMAccountName=user1))` 

And only use results from the specified OUS from the [Ldap Account Access](#ldapAccess) section.

We would then take the value of **userAccountControl** attribute for each user and compare it with the **514** to determine if that user is enabled or disabled. We will not use disabled users. (Unless the want to be able to to use inactive and active user accounts.)


Rancher then would use the **name** attribute in ldap as the name for the Identity within our api. The **distinguishedname** attribute would be used as its externalId which is what we store in the database for project/ environment membership. **distinguishedname** is the only value from ldap that we store in our database for any user or group.

 
##Searching Groups

 * **searchFieldGroup** (field used to search for group on defaults to (should this be multiple fields) **sAMAccountName**) ***Currently hard coded***
     * Ex: search on email name and/ or sAMAccountName

 * **objectTypeGroup** (Value used to compare with *objectClass* to determine if an object is a group or not. **group**)

 * **nameFieldGroup** Attribute rancher uses as the name of a group.
     * Default value **name** ***Currently hard coded***
 * **memberField** Attribute used to determine members on a group. Similar to [**memberOfField**](#memberOfField)
     * Default valuse **member**  ***Currently hard coded***

###Example

If using the defaults for all of the searching groups fields a search for

`groupA`

would result in a query to ldap like this

`(&(objectClass=group)(sAMAccountName=groupA))`

And only use results from the specified OUS from the [Ldap Account Access](#ldapAccess) section.

Rancher would then use the **name** attribute in ldap as the name for the Identity within our api. The **distingushedname** attribute would be used as its externalId which is what we store in our database for project/ environment membership.
 
 ***We currently only support direct membership.***
 
 **Subject to change**