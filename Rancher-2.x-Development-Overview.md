# Setting up rancher for development
see: https://github.com/rancher/rancher/wiki/Getting-started-with-Rancher-2.0-Development

# Intention of Document

Intended audience: developers new to working on rancher or developers not familiar with certain components of rancher. This should be the next step after reading "Getting Start with Rancher".

Goal: kickstart development by explaining heavily used rancher concepts/components/constructs.

# Developing the Rancher API

General API Docs: https://github.com/rancher/api-spec/blob/master/specification.md

### Understanding CRDs

Much of the Rancher API works by forwarding requests to and from the Kubernetes API. Most objects in the Rancher API map to Kubernetes objects. Kubernetes comes with some of these objects out of the box. Others were added by creating CRDs. CRDs (custom resource definitions) are a means of extending the Kubernetes API by creating custom objects. Rancher creates CRDs by defining them in Types, then instantiating them on startup. Types will also create the schema that is used to add the object to the rancher API. 

Once a CRD has been added, objects of the CRD can be interacted with via kubectl or the Rancher API. These interactions can be one of 5 verbs: create, get, update, delete, watch. Sometimes when one of these is done to a CRD, we want to perform some logic. For example:  

I made a CRD called DownloadURL. If a DownloadURL is created I want rancher to try to download from that URL.

This is done using a Controller. Every CRD has a controller which can add logic that will be executed whenever a CRD is has been created, removed, etc..

Kubernetes CRD Doc:
* [Read more about CRDS](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/)

CRDs with Rancher:
* [How to add CRDs and Custom Controllers](https://github.com/rancher/rancher/wiki/Rancher-2.0---Adding-new-CRDs-and-custom-controllers)

Additional Resources:
* [Writing Kubernetes Controllers for CRDs: Challenges, Approaches and Solutions - Alena Prokharchyk](https://www.youtube.com/watch?v=7wdUa4Ulwxg)
* [Extending Kubernetes 101](https://www.youtube.com/watch?v=yn04ERW0SbI)
* [Kubernetes Feature Prototyping with External Controllers and Custom Resource Definitions](https://www.youtube.com/watch?v=fnSNPgwXcUc)
* [Extending the Kubernetes API: What the Docs Don't Tell You](https://www.youtube.com/watch?v=PYLFZVv68lM)
* [client-go: The Good, The Bad and The Ugly](https://www.youtube.com/watch?v=Q88kI8X5R48)
* [Extending Kubernetes: Our Journey & Roadmap](https://www.youtube.com/watch?v=KVEKkr7-IJI)

### API Pathing


### Validators

If you want to check the input of an API request before it is forwarded to Kubernetes, then you probably want to use a validator. Another use of a validator is to block a type of request. It is worth mentioning that validators are only applied to Post and Put requests. Validators are assigned to a schema in one of two setup.go files. Files containing validators are found in the /pkg/api/customization/ folder. An example of the NodeTemplate validator found in /pkg/api/customization/nodetemplate/validation.go:

`package nodetemplate`

`import (`
	`"github.com/rancher/norman/httperror"`
	`"github.com/rancher/norman/types"`
	`"github.com/rancher/rancher/pkg/configfield"`
`)`

`const (`
	`Amazonec2driver    = "amazonec2"`
	`Azuredriver        = "azure"`
	`Vmwaredriver       = "vmwarevsphere"`
	`DigitalOceandriver = "digitalocean"`
`)`

`func Validator(request *types.APIContext, schema *types.Schema, data map[string]interface{}) error {`
	`driver := configfield.GetDriver(data)`
	`if driver == "" {`
		`return httperror.NewAPIError(httperror.MissingRequired, "a Config field must be set")`
	`}`
	`if data != nil {`
		`data["driver"] = driver`
	`}`
	`return nil`
`}`

The above example checks if the input data map contained a field, and returned an error if it did not. If it did, it assigned a value derived from the field onto a separate field of the input map - this is another capability of a validator.

Assigning the validator to the NodeTemplate schema in the setup.go file:

`func NodeTemplates(schemas *types.Schemas, management *config.ScaledContext) {
	schema := schemas.Schema(&managementschema.Version, client.NodeTemplateType)
	npl := management.Management.NodePools("").Controller().Lister()
	f := nodetemplate.Formatter{
		NodePoolLister: npl,
	}
	schema.Formatter = f.Formatter
	s := &nodeTemplateStore.Store{
		Store:                 userscope.NewStore(management.Core.Namespaces(""), schema.Store),
		NodePoolLister:        npl,
		CloudCredentialLister: management.Core.Secrets(namespace.GlobalNamespace).Controller().Lister(),
	}
	schema.Store = s
	schema.Validator = nodetemplate.Validator
}`


### Formatters

If you want to change the output for a schema in the API, you would probably want to use a formatter. Formatters are created and assigned similarly to validators.


### Action Handlers

Action handlers are user when the API needs to be capable of an action that is not: create, update, delete, get, watch. An example would be the 'rollback' or 'upgrade' actions for Apps.


### Stores

# Rancher Owned Dependencies

Rancher has many dependencies, some of which are owned/maintained by rancher. Some functionalities for rancher will need to be modified or added by updating the Rancher dependency in its own repository, than importing the new version into rancher.

Some rancher maintained dependencies and what they do:

kontainer-engine: breaks down provisioning logic based on kubernetes provider (RKE, GKE, EKS, AKS)
repo: https://github.com/rancher/kontainer-engine

types: defines schemas and structures used in kubernetes
repo: https://github.com/rancher/types

norman: contains bulk of under the hood API logic, responsible for talking to and from kube API
repo: https://github.com/rancher/norman

docker-machine: ranchers fork of dockers docker-machine. Responsible for node proivisioning (VMs)
repo: https://github.com/rancher/machine

## Binaries vs Libraries

Some rancher dependencies are used as binaries, while others are imported and used as go libraries.

### Binaries

Binary dependencies, such as docker-machine, can be used by rancher locally by simply building the binary for the dependency you are working on and putting it into your path. Once your commits have been merged into master, a release has to be build for them. The release will contain the library and must be created by someone with admin privileges. Once the release is created, a PR must be made for the Rancher repository that sets the binary version to the new one that contains your changes. This is usually set in package/Dockerfile.

### Go Libraries


# Writing Tests

There are two types of tests that concern most developers: unit tests and integration tests.

## Unit Tests

Unit tests are written in go and should have the a file name that is ends with "_test.go", like so "catalog_test.go"

## Integration Tests

Integration tests are written in python using Rancher's Python client.

