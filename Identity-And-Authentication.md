Identity API & Rancher Authentication
------------
####Purpose: To provide a generic way to interact with the identity of someone within Rancher.

Rancher uses identities and the identity api as a way to allow the ui / api ux of Rancher to be consistent across Github, ldap, and other authentication providers. What this means is that when you get a jwt from `/v1/token` you have a set of identities within Rancher. These identities are what Rancher uses to determine if you have access to any resource within Rancher on every request. These identities are based off of the external authentication system that you have currently configured Rancher to use.

##Identity Object

An Identity is composed of several fields:

* **name** 
    * This is the plain text English representation of the identity that users know and use to refer to the identity.
* **externalId**
    * This is the unique identifier within the external system that is the backing authentication mechanism that is stored in our database and used when determining if an identity is authorized or not.
* **kind**
    * This is the type of externalId that the identity is representing. Ex: `github_org` `ldap_user`
* **id** This is the **kind** combined with the **externalId** to provide a unique identifier for Cattle when searching/getting identities within the api as they are never stored within Cattle as a full identity; only the **kind** and **externalId** are stored.
* **profilePicture** Picture people can use to easily recognize this identity in the format of a url. 
* **profileUrl** If there is a url that can be utilized for users to investigate and identity further, this field will contain that url. EX: a Github user's profile link.

###Example Identity
The user `joesmith84` on Github might look like this:

**kind** `github_user`

**name** `Joe C. Smith`

**profilePicture** `https://avatars2.githubusercontent.com/u/9327853`

**profileUrl** `https://github.com/joesmith84`

**externalId** `9327853`

**id** `github_user:9327853`

The identities that Cattle creates/returns in its api allow the ui to easily display/ search and add users to Environments in a generic way without needing to know anything about ldap vs github or any other backing authentication framework since it has a generic set of information that it can display if it is present. 

##Authentication and Identities

When a user logs into Rancher using the ui a jwt is created by posting to `/v1/token` with the correct information for the configureed authentication provider. This jwt contains all of the identities that the user has. This list of identities is used on every request to determine the resources that the user does and does not have access too. 