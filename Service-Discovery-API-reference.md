Overview
--------
This doc covers API reference and V1 feature set for Rancher Service Discovery

References
---------
[Rancher-compose client to Rancher API reference](https://github.com/rancherio/rancher/wiki/Rancher-compose-client-to-Rancher-API-reference)

Dictionary
----------
**Environment** - defines a namespace where the user application can be run. 

Example: test environment, development environment, production environment.

**Service** - set of configuration options describing user application.

Example: Web service, with the LB and healthcheck support containing 3 containers created from nginx image. 

**LaunchConfig** - a config containing container create/start options. 

Gets defined on per service basis to segregate options that are specific to service orchestration and monitoring (lb, healthcheck, scale) from the container specific create/start options (imageUuid, volumes, etc). Not visible via API, internal object.

**LB** - local Load Balancer implemented by Cattle. Sits in front of the service's containers.

Use cases
-----------
**Use Case #1** 

From command line, user

1. Executes "rancher up" - the services get built in Cattle based on the docker-compose.yml config content. Rancher client will invoke Rancher Apis to build the environment, create/activate the services

2. User can also optionally put rancher-compose.yml config as an extra parameter to "rancher up" command to enable Rancher specific config on a service - LoadBalancer, HealthCheck, Scale

**Use Case #2** 

User builds an environment, creates/activates services from Rancher UI. 

For both use cases, all the services - once built - can be exported to docker-compose.yml/rancher-compose.yml files using Rancher UI/API.

Docker-compose support
----------
All the fields defined in docker-compose.yml are supported, with an exception for:

1. **build** - as the defined Dockerfile is local to user's setup, Rancher has no ability to read/execute it
2. **env_file** - the same reason as explained in 1.
3. **environment** - don't support the case when only key is specified, but no value - the same reason as explained in 1.
4. net - might add support for that before the feature goes out

Extra service options added by Rancher
----------
1. LoadBalancer
2. HealthCheck
3. Scale - this might eventually make it to docker-compose as per [docker-compose PR](https://github.com/docker/compose/pull/630)

User flow
-----------

User flow will be described from the Use Case 2 point, where everything gets defined via Rancher UI/API

1. Create an environment using environment.create API

2. Add 1+ services to the environment using **service.create** API. 

3. All services in the environment can be launched using **environment.activateServices** API. The API will trigger services activation (the order is determined based on services relationship). To note: services also can be activated individually by using **service.activate** API.

5. Service activation consists of:

* setting up the LB if specified
* setting up the HealthCheck if specified
* Starting container n=scale instances with options defined in a service. If no scale option is specified, one container is started per service

6. Active service can be deactivated using **service.deactivate** API

API Targets, Fields (* - required) and Actions
----------
1) /**environment** 

Fields:
* name

Actions:
* CRUD
* activateServices
* deactivateServices
* exportConfig

2) /**service** 

Fields:
* name (*)
* environmentId (*)
* healthCheck
* type: (supportedTypes=user/loadBalancer)
* scale
* bunch of properties defining container create/start options - imageUuid, volumes, etc - TBD

Actions:
* CRUD
* activate/deactivate
* createLink/removeLink

3) /**link**

Actions:
*R



To support in the future:
-----------
* Service update
