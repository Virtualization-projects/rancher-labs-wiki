
LDAP Authentication Integration
---------
Purpose: To be able to configure [Access control](http://docs.rancher.com/rancher/configuration/access-control/) 
to use LDAP as the backing authentication system. 

 * Authenticate users to Rancher based on LDAP OUs or a Domain.
 * Add users to an [Environment](http://docs.rancher.com/rancher/concepts/#environments) based on their LDAP username or groups.

##Configure Connection To LDAP
 * ***loginDomain*** 
    * Specifies the default domain to login using.
    * ex User1 becomes loginDomain\User
    * ex foo\User1 stays foo\User1
 * ***port***
     * Specifies the port to use when connecting to LDAP.
     * Defaults to **389**
 * ***server***
     * Specifies the server ip or domain that Rancher uses to connect to ldap.
 * **tls**
     * Use TLS or not.
     * TLS is not implemented yet
 * ***enabled***
     * Boolean that determines if auth is enabled or not.
     * If set to false, LDAP will not be used.

###LDAP Account Access
 
These fields are used to determine who has access to Rancher, and specifies the service account that Rancher will
use. A service account, with read only access,  is needed for querying LDAP so that Rancher can perform user searches.
 
 * ***accessMode***  [[Restricted|Glossary#restricted]] or [[Unrestricted|Glossary#unrestricted]]
     * <a name="ous"></a>**ous** ([[Restricted|Glossary#restricted]]) List of OUs from LDAP in the form of DNs
     * *domain* ([[Unrestricted|Glossary#unrestricted]]) Domain within LDAP to use. EX: ad.example.com
 * ***serviceAccountUsername***
     * Username for the service account.
 * ***serviceAccountPassword***
     * Password for the service account.
 * **uniqueIdentifierField**
     * Specifies the LDAP attribute used as the unique identifier for LDAP Objects.
     This is what [[Identities|Identity And Authentication]] use as an externalId.
     * Default value **distinguishedname**  

##Users
These fields are used by Rancher to determine how we identify an LDAP Object as a user.

 * Fields used for Authorization/ Searching
     * **searchFieldUser**
         * Identifies the LDAP attribute to use as the search field when searching for users. 
         * Default Value **sAMAccountName**
     * **objectTypeUser**
         * Identifies the value of the LDAP attribute *objectClass* to determine that an object is a user.
         * Default value **person**
     * **userEnabledMaskBit**
         * Specifies the bit to check to see if an account/user is enabled. 
         * Default value **514** 
     * **userEnabledAttribute**
         * Identifies the LDAP attribute to mask with the **userEnabledMaskBit** to determine if a user is enabled.
         * Default value **userAccountControl** 
     * <a name="memberOfField"></a>**memberOfField**
         * Identifies the LDAP attribute used to check for membership of groups to
     provide authorization to [Environments](http://docs.rancher.com/rancher/concepts/#environments). 
         * Default value **memberOf** 
 * User Metadata
     * **nameFieldUser**
         * Identifies the LDAP attribute that Rancher will use to populate the name of a user's [[Identity|Identity And Authentication]]. 
         * Default value **name** 

###Example
Assuming the defaults for the above fields on a user object, a search for: 

`user1`

would result in a query to LDAP that looks like this:

`(&(objectClass=person)(sAMAccountName=user1))`

Only results from the specified [OUs](#ous) are returned as configured.

We would then take the value of **userAccountControl** attribute for each user and compare it with **514** to determine if that user is enabled or disabled. We will not use disabled users. 

The information from ldap for a user is used to create an [[Identity|Identity And Authentication]] for the user.

 
##Groups

These fields are used for defining a group based on the LDAP schema.

 * Fields used for Authorization/ Searching
     * **searchFieldGroup**
         * Specifies the LDAP attribute used when searching for a group. 
         * Default value **sAMAccountName**
     * **objectTypeGroup**
         * Specifies the value used to compare with LDAP attribute *objectClass* to determine if an
         object is a group or not.
         * Default value **group**
     * **memberField**
         * Specifies the LDAP attribute used to determine the members of a group. Similar to [**memberOfField**](#memberOfField)
         * Default value **member**
 * Group Metadata
      * **nameFieldGroup**
          * Identifies the LDAP attribute that Rancher will use to populate the name of a group's
      [[Identity|Identity And Authentication]]. 
          * Default value **name**

###Example

If using the defaults for all of the fields for a group, a search for:

`groupA`

would result in a query to LDAP that looks like:

`(&(objectClass=group)(sAMAccountName=groupA))`

And only use results from the specified OUs from the [LDAP Account Access](#LDAPAccess) section.

The information from LDAP for a group is used to create an [[Identity|Identity And Authentication]] for the group.
 
 ***We currently only support one level of membership.***
 
 [[More Examples|LDAP Examples]]
 
 
####Questions
 
 * Do we need to support multiple levels of group membership? 
     * ex: Group A is member of Group B and User a is a member of Group B so as a result User a is a member of Group A.
 * Do we need to be able to search for users/groups on multiple fields?
 * Do we need to support connecting to multiple servers/ domain controllers.