
Ldap Authentication Integration
---------
Purpose: To be able to authenticate to Rancher using Ldap credentials. Add users to the site based on their Ldap username or groups.
Add users to a project based on their Ldap username or groups.

After configuring Ldap, you are able to login to Rancher using your Ldap username and password. Then you use Rancher the same as normal except that when creating projects you use Ldap users and groups instead of Github users, orgs, or teams. 
[Environments](http://docs.rancher.com/rancher/concepts/#environments)

##Conecting to Ldap
 * **loginDomain** 
    * default domain to login using
    * ex User1 becomes loginDomain\User
    * ex foo\User1 stays foo\User1
 * **port** (port Ldap is listening on. defaults to **389**)
 * **server** (server ip or domain that Rancher can use to connect.)
 * **tls** (Use tls or not. (Tls not tested/implemented yet.))
 * **enabled** boolean determining if auth is enabled or not. (If set to false, Ldap will not be used.)

###<a name="LdapAccess"></a>Ldap Account Access
 
These fields are used to determine who has access to Rancher and who Rancher talks to Ldap as when searching Ldap.
 
 * **accessMode**  (restricted or unrestricted)
     * **ous** [[Restricted|Glossary#restricted]] Organizational Units allowed access to Rancher. This is a list of Distinguished Names.
     * **domain** [[Unrestricted|Glossary#unrestricted]] Domain within Ldap to use. EX: ad.example.com
 * **serviceAccountPassword** (password for the service account that has read-only access to all of Ldap (All of Ldap that you may need access too for authentication.))
 * **serviceAccountUsername** (username for the service account, same account as above)
 * **uniqueIdentifierField** (field used as the unique identifier for Ldap Objects default **distinguishedname** this is what Identities use as externalId) ***Currently hard coded***

##Users
These fields are used by Rancher to determine how we identify an Ldap Object as a user as well as look for users withing Ldap.


 * **searchFieldUser** Attribute in Ldap to use as the search field when searching for users. 
     * Default Value **sAMAccountName**
 * **objectTypeUser** Value of the attribute *objectClass* to determine that an object is a user.
     * Default value **person**
 * **nameFieldUser** Attribute Rancher will use as the name of a user. 
     * Identity for a user will use the value of this attribute for the identity's name.
     * Default value **name** ***Currently hard coded***
 * <a name="memberOfField"></a>**memberOfField** Attribute used to check for membership. 
     * Default value **memberOf** ***Currently hard coded***
 * **userEnabledMaskBit** Bit to check to see if an account/user is enabled. 
     * Default value **514** ***Currently hard coded***
 * **userEnabledAttribute** Attribute to mask with the **userEnabledMaskBit** to determine if a user is enabled.
     * Default value **userAccountControl** ***Currently hard coded***
     
###Example
If using the defaults for all of the searching users fields a search for 

`user1` 

would result in a query to Ldap like this 

`(&(objectClass=person)(sAMAccountName=user1))` 

And only use results from the specified OUS from the [Ldap Account Access](#LdapAccess) section.

We would then take the value of **userAccountControl** attribute for each user and compare it with the **514** to determine if that user is enabled or disabled. We will not use disabled users. (Unless the want to be able to to use inactive and active user accounts.)


Rancher then would use the **name** attribute in Ldap as the name for the Identity within our api. The **distinguishedname** attribute would be used as its externalId which is what we store in the database for project/ environment membership. **distinguishedname** is the only value from Ldap that we store in our database for any user or group.

 
##Groups



 * **searchFieldGroup** (field used to search for group on defaults to (should this be multiple fields) **sAMAccountName**) ***Currently hard coded***
     * Ex: search on email name and/ or sAMAccountName

 * **objectTypeGroup** (Value used to compare with *objectClass* to determine if an object is a group or not. **group**)

 * **nameFieldGroup** Attribute Rancher uses as the name of a group.
     * Default value **name** ***Currently hard coded***
 * **memberField** Attribute used to determine members on a group. Similar to [**memberOfField**](#memberOfField)
     * Default value **member**  ***Currently hard coded***

###Example

If using the defaults for all of the searching groups fields, a search for

`groupA`

would result in a query to Ldap like this

`(&(objectClass=group)(sAMAccountName=groupA))`

And only use results from the specified OUS from the [Ldap Account Access](#LdapAccess) section.

Rancher would then use the **name** attribute in Ldap as the name for the Identity within our api. The **distingushedname** attribute would be used as its externalId which is what we store in our database for project/environment membership.
 
 ***We currently only support direct membership.***
 
 **Subject to change**
 
 [[More Examples|Ldap Examples]]