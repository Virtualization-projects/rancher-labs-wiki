##Examples

 Example Group and user in the Ldap domain ad.Rancher.io:
 
 ![Group Ex:] (https://i.imgur.com/ybCDzyq.png)
 Given the above Ldap Object for Group *Users* by default we would create the identity object:
 externalId : *CN=Users,CN=Builtin,DC=ad,DC=Rancher,DC=io* grabbed from the distinguishedname field.
 name : *Users* grabbed from the name field.
 profileUrl : NONE what should we use?
 profilePicture : NONE what should we use/look for? Or make one up using the identicon api or perhaps robohash.org
 
 ![User Ex:] (https://i.imgur.com/6BIcwLf.png)
 
 Given the above Ldap Object for User *test* by default we would create the identity object:
 externalId : *CN=Tester Testing,CN=Users,DC=ad,DC=Rancher,DC=io* grabbed from the distinguishedname field.
 name : *Tester Testing* grabbed from the name field.
 profileUrl : NONE what should we use?
 profilePicture : NONE what should we use/look for? Or make one up using the identicon api or perhaps robohash.org
  
  
 These two objects are examples of what would be used to create a project member or grant access to the site in the Rancher api.