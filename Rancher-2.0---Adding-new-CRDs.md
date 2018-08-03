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