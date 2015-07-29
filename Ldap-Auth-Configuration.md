
Ldap Authentication Integration
---------
Purpose: To be able to use configure [Access control](http://docs.rancher.com/rancher/configuration/access-control/) 
to use Ldap as the backing authentication system instead of github. Add users to the site based on their Ldap username 
or groups. Add users to an [Environment](http://docs.rancher.com/rancher/concepts/#environments)
 based on their Ldap username or groups.

After configuring Ldap, you are able to login to Rancher using your Ldap username and password. Then you use Rancher the same as normal except that when creating Environments you use Ldap users and groups instead of Github users, orgs, or teams. 

##Conecting to Ldap
 * *loginDomain* 
    * default domain to login using
    * ex User1 becomes loginDomain\User
    * ex foo\User1 stays foo\User1
 * *port* (port Ldap is listening on. defaults to **389**)
 * *server* (server ip or domain that Rancher can use to connect.)
 * **tls** (Use tls or not. (Tls not tested/implemented yet.))
 * *enabled* boolean determining if auth is enabled or not. (If set to false, Ldap will not be used.)

###<a name="LdapAccess"></a>Ldap Account Access
 
These fields are used to determine who has access to Rancher and who Rancher talks to Ldap as when searching Ldap.
 
 * *accessMode*  (restricted or unrestricted)
     * **ous** [[Restricted|Glossary#restricted]] Organizational Units allowed access to Rancher. This is a list of Distinguished Names.
     * *domain* [[Unrestricted|Glossary#unrestricted]] Domain within Ldap to use. EX: ad.example.com
 * *serviceAccountUsername*  Username for service account.
 * *serviceAccountPassword*  Password for the service account.
 * **uniqueIdentifierField** (field used as the unique identifier for Ldap Objects default **distinguishedname** this is what [[Identities|Identity And Authentication]] use as externalId) 

##Users
These fields are used by Rancher to determine how we identify an Ldap Object as a user.

 * Fields used for Authorization/ Searching
     * **searchFieldUser** Identifies the attribute in Ldap to use as the search field when searching for users. 
         * Default Value **sAMAccountName**
     * **objectTypeUser** Identifies the value of the attribute *objectClass* to determine that an object is a user.
         * Default value **person**
     * **userEnabledMaskBit** Specifies the bit to check to see if an account/user is enabled. 
         * Default value **514** 
     * **userEnabledAttribute** Identifies the Attribute in Ldap to mask with the **userEnabledMaskBit** to determine if a user is enabled.
         * Default value **userAccountControl** 
     * <a name="memberOfField"></a>**memberOfField** Identifies the attribute used to check for membership of groups to provide authorization to [Environments](http://docs.rancher.com/rancher/concepts/#environments). 
         * Default value **memberOf** 
 * User Metadata
     * **nameFieldUser** Identifies the attribute in Ldap that Rancher will use to populate the name of a user's [[Identity|Identity And Authentication]]. 
         * Default value **name** 

###Example
If using the defaults for all of the searching users fields a search for 

`user1` 

would result in a query to Ldap like this 

`(&(objectClass=person)(sAMAccountName=user1))` 

And only use results from the specified OUs from the [Ldap Account Access](#LdapAccess) section.

We would then take the value of **userAccountControl** attribute for each user and compare it with the **514** to determine if that user is enabled or disabled. We will not use disabled users. (Unless the want to be able to to use inactive and active user accounts.)


Rancher then would use the **name** attribute in Ldap as the name for the [[Identity|Identity And Authentication]] within our api. The **distinguishedname** attribute would be used as its externalId which is what we store in the database for Environment/ environment membership. **distinguishedname** is the only value from Ldap that we store in our database for any user or group.

 
##Groups

These fields are used for defining a group based on the ldap schema.
 * Fields used for Authorization/ Searching
     * **searchFieldGroup** (field used to search for group on defaults to (should this be multiple fields) **sAMAccountName**) 
         * Ex: search on email name and/ or sAMAccountName
     * **objectTypeGroup** (Value used to compare with *objectClass* to determine if an object is a group or not. **group**)
     * **memberField** Attribute used to determine members on a group. Similar to [**memberOfField**](#memberOfField)
         * Default value **member**
 * Group Metadata
      * **nameFieldGroup** Attribute Rancher uses as the name of a group.
          * Default value **name**

###Example

If using the defaults for all of the searching groups fields, a search for

`groupA`

would result in a query to Ldap like this

`(&(objectClass=group)(sAMAccountName=groupA))`

And only use results from the specified OUs from the [Ldap Account Access](#LdapAccess) section.

Rancher would then use the **name** attribute in Ldap as the name for the [[Identity|Identity And Authentication]] within our api. The **distingushedname** attribute would be used as its externalId which is what we store in our database for Environment/environment membership.
 
 ***We currently only support direct membership.***
 
 **Subject to change**
 
 [[More Examples|Ldap Examples]]
 
 
####Questions
 
 * Do we need to support multiple levels of group membership? 
     * ex: Group A is member of Group B and User a is a member of Group B so as a result User a is a member of Group A.
 * Do we need to be able to search for users/groups on multiple fields?
 * Do we need to support multiple servers/ connecting to multiple domain controllers?
 * Do we need tls?