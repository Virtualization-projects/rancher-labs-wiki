Overview
--------
This doc is meant to be used by rancher-compose client for translating rancher-compose actions and .yml file config parameters to Rancher APIs/parameters

Rancher-compose commands to Rancher API commands translation
----------
|Compose command|Rancher API(s)|Comments|
---------|------|---------------|--------------------------------------------------------------------------
||||
||||
||||
||||

docker-compose.yml/fig.yml parameters to Rancher parameters translation
----------
|Compose parameter|Rancher parameter|Extra logic that needs to be done on a client side|
---------|------|---------------|--------------------------------------------------------------------------
|image|image|If image belongs to client's private repo, rancher-compose.yml file should have registryCredentialId specified|
|build|**Not supported**|-|
|command|command|-|
|links|links|We can't translate links to Rancher instanceLinks directly as link in compose means link to the entire service; the translation will be done on the server side |
|external_links|externalLinks|Again, the translation to instanceLinks will be done on the server side|
|ports|ports|-|
|expose||-|
|volumes|dataVolumes|-|
|volumes_from|dataVolumesFrom|-|
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
|ha|false|Used to enable HA on the service|
|loadBalancer|false|Used to enable LoadBalancer on the service|
|registryCredentialId|true if "image" is located in a private repo|If "image" specified in docker-compose.yml belongs to private repo, registryCredentialId should be specified to let Rancher know the registry location|