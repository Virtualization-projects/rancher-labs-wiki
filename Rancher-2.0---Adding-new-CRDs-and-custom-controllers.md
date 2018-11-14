1. When you add a type to rancher/types, if you want that type to be backed by a CRD and to be fully functional in the rancher API, you have to explicitly tell rancher to create a crd (and store) for it here:
https://github.com/rancher/rancher/blob/master/pkg/api/server/managementstored/setup.go <br>
`createCRD` is called separately for management schema and project schema

2. Adding a cluster level resource in rancher/types:
Make sure your CRD has these 2 fields to make it a cluster-level resource: <br>
```
types.Namespaced
ClusterName string `json:"clusterName" norman:"type=reference[cluster]"
```

It should have the norman tag referencing the cluster

3. Adding a project level resource in rancher/types:
Make sure the CRD has these 2 fields to make it a project-level resource: <br>
```
types.Namespaced
ProjectName string `json:"projectName" norman:"type=reference[project]"
```

It should have the norman tag referencing the project

Any instance you create for this CRD will be created in the management plane. The controller for this CRD can be added in management, or under user package within controllers. This can be decided based on whether CRUDing this resource, causes a controller to CRUD a resource in the cluster.

## Adding controllers in rancher for these CRDs

There are two packages under rancher/pkg/controllers, management and user.  
### How to decide if your controller goes in user or management package
1. If your controller needs to CRUD any resources from the user cluster, then you should add your controller in the user package. This does not mean your controller is actually running in the user cluster. It is still running in management plane, but one instance of your controller is running for each cluster.
2. If your controller does not need to CRUD any user cluster resources, add it to the management controllers package.  

Both types of controllers described above run in the management plane. If you want your controller running in each user cluster, be sure that it does not need any of the management plane resources. (For example, ones registered in the function `RegisterUserOnly` in pkg/controller/user/controller.go)

### AddHandler vs AddClusterScopedHandler vs AddLifecycle

#### AddHandler
This will give your controller an update on ALL instances of the resource it's listening on

#### AddClusterScopedHandler
This gives updates for all instances of the resource belonging to THIS cluster

#### AddLifecycle
This is heavier than `AddHandler`, use only if getting updates on different events such as separate updates on Create/Update/Delete is required