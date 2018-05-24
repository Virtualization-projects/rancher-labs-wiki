1. When you add a type to rancher/types, if you want that type to be backed by a CRD and to be fully functional in the rancher API, you have to explicitly tell rancher to create a crd (and store) for it here:
https://github.com/rancher/rancher/blob/master/pkg/api/server/managementstored/setup.go
`createCRD` is called separately for management schema and project schema

2. Adding a cluster level resource in rancher/types:
Make sure your CRD has these 2 fields to make it a cluster-level resource: </br>
`types.Namespaced`
`ClusterName string `json:"clusterName" norman:"type=reference[cluster]"``
Make sure the norman tag referencing the cluster is present

3. Adding a project level resource in rancher/types:
Make sure the CRD has these 2 fields to make it a project-level resource:
`types.Namespaced`
`ProjectName string `json:"projectName" norman:"type=reference[project]"`
Make sure the norman tag referencing the project is present
