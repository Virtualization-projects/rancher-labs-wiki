Identity API & Rancher Authentication
------------
####Purpose: To provide a generic way to interact with the identity of someone within Rancher.

Rancher uses Identities and the Identity API as a way to allow the UI / API UX of Rancher to be consistent across Github,
 LDAP, and other authentication providers. What this means is that when you get a JWT from `/v1/token` you have a set of
 Identities within Rancher. These Identities are what Rancher uses to determine if you have access to any resource within
 Rancher on every request.
 
 See: [Access control](http://docs.rancher.com/rancher/configuration/access-control/) and [Environment](http://docs.rancher.com/rancher/concepts/#environments)

##Identity Object

An Identity is composed of several fields:

* **name** 
    * This is the plain text English representation of the Identity that users know and use to refer to the identity.
* **externalId**
    * This is the unique identifier within currently configured backend for [Access control](http://docs.rancher.com/rancher/configuration/access-control/).
    externalIds are stored in our database and used when determining if an Identity is authorized to use an [Environment](http://docs.rancher.com/rancher/concepts/#environments).
* **kind**
    * This is the type of externalId that the Identity is representing.
    * Ex: `github_org` `ldap_user`
* **id**
    * This is the **kind** combined with the **externalId** to provide a unique identifier for Cattle when searching/getting
    Identities within the api as they are never stored within Cattle as a full identity; only the **kind** and **externalId**
    are stored.
* **profilePicture**
    * Picture people can use to easily recognize this identity in the format of a URL. 
* **profileUrl**
    * If there is a URL that can be utilized for users to investigate and Identity further, this field will contain that URL.
    * EX: a Github user's profile link.

###Example Identity
The user `joesmith84` on Github might look like this:

**kind** `github_user`

**name** `Joe C. Smith`

**profilePicture** `https://avatars2.githubusercontent.com/u/9327853`

**profileUrl** `https://github.com/joesmith84`

**externalId** `9327853`

**id** `github_user:9327853`

The Identities that Cattle creates and returns in its API allow the UI to easily display, search and add users to [Environments](http://docs.rancher.com/rancher/concepts/#environments) 
in a generic way without needing to know anything about LDAP, Github or any other backing authentication framework since
it has a generic set of information that it can display and use if present. 

##Authentication and Identities

When a user logs into Rancher using the UI a JWT is created by posting to `/v1/token` with the correct information for
the configured authentication provider. This JWT contains all of the Identities that the user has. This list of Identities
is used on every request to determine the resources that the user does and does not have access too.
