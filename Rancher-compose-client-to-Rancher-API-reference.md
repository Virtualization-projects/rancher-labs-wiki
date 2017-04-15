Overview
--------
This doc is meant to be used by rancher-compose client for translating rancher-compose actions and .yml file config parameters to Rancher APIs/parameters

Rancher-compose commands to Rancher API commands translation

----------
|Rancher-compose client command|Rancher API(s)|Comments|
|---------|------|---------------|
|rancher-compose up|1. environment.create<br>2. service.create for all the services defined in the .yml file<br>3.service.addServiceLink for "links" items to create mappings to another services<br>4. environment.activateServices||
|rancher-compose stop|environment.deactivateServices|Deactivate all the services that are the part of the environment|
|rancher-compose rm|environment.remove|All active services in the environment get deactivated; then all the services get removed. After that, the environment gets removed|

docker-compose.yml/fig.yml parameters to Rancher parameters translation
----------
|Compose parameter|Rancher parameter|Extra logic that needs to be done on a client side|
|---------|------|---------------|
|image|image|Prepend image with "docker:" as its used by Cattle to locate the storage driver (currently, there are 2: "docker:"/"sim:"<br>If image belongs to client's private repo, rancher-compose.yml file should have registryCredentialId specified<|
|build|**Not supported**|-|
|command|command|-|
|links|Links get programmed via add/removeServiceLink Rancher APIs|-|
|external_links|instanceLinks|-|
|ports|ports|-|
|expose|**Not supported**|-|
|volumes|dataVolumes|-|
|volumes_from|dataVolumesFrom<br>dataVolumesFromService|volumes_from can be containerName or serviceName. Client has to make a translation to either of option|
|environment|environment|-|
|env_file|**Not supported**|-|
|net|**Not supported**|-|
|dns|dns|-|
|cap_add|capAdd|-|
|cap_drop|capDrop|-|
|dns_search|dnsSearch|-|
|working_dir|directory|-|
|entrypoint|entryPoint|-|
|user|user|-|
|hostname|hostname|-|
|domainname|domainName|-|
|mem_limit|memory|-|
|privileged|privileged|-|
|restart|restartPolicy|-|
|stdin_open|stdinOpen|-|
|tty|tty|-|
|cpu_shares|cpuShares|-|

Extra parameters supported by rancher-compose.yml

---------
|Parameter name|Required|Comments|
---------|------|------|
|scale|false|Number of containers exposing the service |
|registryCredentialId|true if "image" is located in a private repo|If "image" specified in docker-compose.yml belongs to private repo, registryCredentialId should be specified to let Rancher know the registry location|
|TBD: loadBalancer and HealthCheck|false||
