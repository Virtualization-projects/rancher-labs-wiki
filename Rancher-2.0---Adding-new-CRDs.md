When you add a type to rancher/types, if you want that type to be backed by a CRD and to be fully functional in the rancher API, you have to explicitly tell rancher to create a crd (and store) for it here:
https://github.com/rancher/rancher/blob/master/pkg/api/server/managementstored/setup.go
`createCRD` is called separately for management schema and project schema