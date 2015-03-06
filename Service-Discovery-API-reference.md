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

Gets defined on per service basis to segregate options that are specific to service orchestration and monitoring (lb, healthcheck, scale) from the container specific create/start options (imageUuid, volumes, etc).

**LB** - local Load Balancer implemented by Cattle. Sits in front of the service's containers.

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

User flow will be described from the Use Case 3 point, where everything gets defined via Rancher UI/API

1. Create an environment using environment.create API

2. Add 1+ services to the environment using service.create API. For the service, you also have to define the launchConfig using launchConfig.create api. 

3. Once you decide there are no more services to be added, the environment can be activated. Once the environment is activated, no more services can be added to it.

4. Once environment is activated, all the services in it can be launched using environment.activateServices API. The API will trigger services activation (the order is determined based on services relationship). To note: services also can be activated individually by using service.activate API.

5. Service activation will consists of:

* setting up the LB if specified
* setting up the HealthCheck if specified
* Starting container n=scale instances with options defined in launch config. If no scale option is specified, one container is started per service

Yet to define:

* What service.deactivate mean (instances stop, update for other services consuming this one, etc)
* Environment update - whether to allow parameters modifications once activated
* Service update - whether to allow parameters modifications once activated.


API Targets, Fields (* - required) and Actions
----------
1) /**environment** 

Fields:
* name

Actions:
* CRUD
* activate/deactivate
* activateServices
* importConfig

2) /**service** 

Fields:
* name
* healthCheck
* loadBalancing
* scale

Actions:
* CRUD
* activate/deactivate

3) /**launchConfig** 

Fields:
* name
* serviceId
* <bunch of properties defining container create/start options - imageUuid, volumes, etc>

Actions:
* CRD
