The cattle resource framework gives you several ways to extend resources. By extend a resource, we mean add properties to the API. Let's walk through some of them.

### Option 1: Adding a new property &lt;resource name&gt;.json
Follow these steps to add the property ```myFoo``` to the [container](http://localhost:8080/v1/containers) resource:

1. In Eclipse, open up container.json ($CATTLE_HOME/cattle-resources/content/schema/base/container.json)
1. Find a simple string property defined and copy and paste it to the bottom. ```cpuSet``` is a nice simple one. Modify the bit you just copy and pasted so that you have something that looks like:
  
  ```
  "myFoo": {
    "type": "string",
    "nullable": true
  }
  ```
1. Open ```admin-auth.json``` and ```user-auth.json``` and add c(reate) r(read) u(pdate) permissions for this new property. Should end up adding a line like this: 
  ```
  "container.myFoo" : "cru",
  ```
1. Restart the cattle server in eclipse.

**Boom.** *That's it.* The container resource now has a new field called myFoo. You can prove it by going to [http://localhost:8080/v1/containers](http://localhost:8080/v1/containers) and clicking the create button. myFoo will show up in the create dialog and if you assign it a value, that value will be persisted. 

Try it. The minimal fields you need to spin up a container are just name and imageID. Once you've successfully create the container via the API, click the *Follow Self Link* button at the bottom of the create dialog box so that we can explore what just happened a little bit further.

If you search for myFoo, you should notice that myFoo shows up in two places:
 * As a top-level property, sibling to fields like ```id``` and ```created```
 * As a nested property at ```data.fields.myFoo```
To avoid having to create a new database column (and regenerating the java interfaces and classes) every time a field needs added to a resource, we store these fields as part of a json object in the DB. In this case, the column storing the json blob is instance.data. 

In this ```data``` json object, the ```fields``` property is used to store fields that should be exposed as top-level fields on the resource, but ```data``` can have other arbitrary properties as well. For example, the entire contents of [docker inspect](https://docs.docker.com/reference/api/docker_remote_api_v1.16/#inspect-a-container) is store as ```data.dockerInspect``` and is not surfaced as a top-level property on the resource.

The other two ways to add fields to resources are discussed next.

### Option 2: &lt;resource&gt;.json.d config directories
This is only a slight variation on the above. In eclipse, open up docker.json ($CATTLE_HOME/cattle-docker-api/src/main/resources/schema/base/container.json.d/docker.json). Had you added myFoo to this file instead, it would have behaved exactly the same as adding it to container.json. Note the &lt;resource&gt;.json.d naming convention. This is based off of the unix and linux ".d" convention for config files. The framework will find directories matching this pattern and add their contents into the appropriate resource definition.

*Why do this?* Vendor-specific plugins. Imagine we supported another container technology that required a field that docker doesn't. We could add that field to $CATTLE_HOME/cattle-**SHINY_NEW_TOY**-api/src/main/resources/schema/base/container.json.d/**arbitrary_filename**.json and that field would get picked up without polluting the base container.json configuration.

### Option 3: Database columns
You can also add a field to a resource by creating a new database column for it. [[This article|Create Database Type]] explains how to create a new database-backed resource from scratch. You can adapt the instructions to modify an existing resource.

Take the time to try that now. Add myFoo or a similarly named property as column to a table in the cattle database and get it working in the API.

***FAQ:*** 
*Q: How should I choose between options 1, 2, and 3?*

A: Choosing isn't 100% clearcut. The best way to answer is to go from least fuzzy to most fuzzy:

1. If you know that the code you're writing that will use the property will need to perform searches on the property, make it a database column.
1. If the concept is fundamental to the core concept of the resource, make it database column.
1. If it doesn't meet the above two criteria, it should probably be in &lt;resource&gt;.json (option 1)
1. If the resource doesn't have it's own database table*, it should probably by in &lt;resource&gt;.json (option 1)
1. If it "vendor" specific and the vendor isn't docker, one could make a good case for it going in (option 2)
Looking at existing art should be useful as well. Look at what is in.

\* Some first-class API resources are built as extensions to others. For example, there is no container DB table. It inherits all the DB fields of instance. All container specific fields are added via json.

Still in doubt? Look at existing examples. Again, container is a good example (DB table is instance; Core fields are in container.json; a few supplementary fields are in cotainer.json.d/docker.json).

**Next:** [[Cowpoke 3.2: Creating a new resource]]