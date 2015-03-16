Overview
--------
This doc is meant to be used by rancher-compose client for translating rancher-compose actions and .yml file config parameters to Rancher APIs/parameters

Rancher-compose commands to Rancher API commands translation
----------
|Rancher-compose client command|Rancher API(s)|Comments|
---------|------|---------------|--------------------------------------------------------------------------
|rancher-compose up|1. environment.create<br>2. service.create for all the services defined in the .yml file<br>3. environment.activateServices||
|rancher-compose stop|environment.deactivateServices||
|rancher-compose rm|environment.remove|Just as in compose, environment can be removed only after all services in it are deactivated|


docker-compose.yml/fig.yml parameters to Rancher parameters translation
----------
|Compose parameter|Rancher parameter|Extra logic that needs to be done on a client side|
---------|------|---------------|--------------------------------------------------------------------------
|image|image|If image belongs to client's private repo, rancher-compose.yml file should have registryCredentialId specified|
|build|**Not supported**|-|
|command|command|-|
|links|serviceLinks|-|
|external_links|instanceLinks|-|
|ports|ports|-|
|expose|**Not supported**|-|
|volumes|dataVolumes|-|
|volumes_from|dataVolumesFrom|volumes_from can be containerName or serviceName. Client has to translate it to the cattle container id|
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
|healthCheck|false|Used to enable HealthCheck on the service|
|loadBalancer fields|false|Used to enable LoadBalancer on the service|
|registryCredentialId|true if "image" is located in a private repo|If "image" specified in docker-compose.yml belongs to private repo, registryCredentialId should be specified to let Rancher know the registry location|