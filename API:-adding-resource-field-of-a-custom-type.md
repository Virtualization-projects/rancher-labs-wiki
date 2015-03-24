Overview
----------------
In some cases when you introduce a new field for the resource, you need this field to be of a composite type, and strongly typed when it comes to the API.

**Implementation** section below will provide an example on how to define this field in Cattle.

Implementation
---------------
As an example, lets take container's field **restartPolicy**. It consists of 2 properties:

* String name
* int maximumRetryCount

## To register the field, do:

1. Create **RestartPolicy.java** class Under $CATTLE_HOME/code/iaas/model/src/main/java/io/cattle/platform/core/addon/

2. Register the newly added addOn in **spring-core-model-context.xml**

3. In **container.json** define the field: 
"restartPolicy":{
    "type": "restartPolicy",
    "nullable": true
 }

4. In user-auth.json define the permissions for the field (container.restartPolicy) **and** the field type (restartPolicy) **and** the field type's properties (maximumRetryCount):

"container.restartPolicy" : "cr"

"restartPolicy" : "cr"

"restartPolicy.maximumRetryCount" : "cr"

