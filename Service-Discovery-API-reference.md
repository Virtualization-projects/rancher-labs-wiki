Overview
--------
This doc covers API reference and V1 feature set for Rancher Service Discovery

Dictionary
----------
**Environment** - defines a namespace where the user application can be run. 

Example: test environment, development environment, production environment.

**Service** - set of configuration options describing user application.

Example: Web service, with the LB and healthcheck support containing 3 containers created from nginx image. 

**LaunchConfig** - a config containing container create/start options. 

Gets defined on per service basis to segregate options that are specific to service orchestration and monitoring (lb, healthcheck) from the container specific create/start options (imageUuid, volumes, etc).

Use cases
-----------
**Use Case #1** 

From Rancher UI, user:

1. Creates and environment in Cattle, and imports docker-compose.yml config to it - the services get built automatically based on the config content.

2. After all the services are created, user can activate the environment and all the services in it.

This scenario replicates the behavior of the scenario if user just did "docker-compose up"

**Use Case #2** 

This use case is similar to the Use Case1, with one extra option on the step 1). In addition to docker-compose.yml, user imports rancher-compose.yml where extra options only Rancher supports, are defined. 

**Use Case #3** 

User builds an environment + the services from Rancher UI omitting the config import. 

User flow
-----------

API Targets, Fields (* - required) and Actions
----------