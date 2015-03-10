Overview
--------
This doc is meant to be used by rancher-compose client for translating rancher-compose actions and .yml file config parameters to Rancher APIs/parameters

Rancher-compose commands to Rancher API commands translation
----------

docker-compose.yml/fig.yml parameters to Rancher parameters translation
----------
|Compose parameter|Rancher parameter|Extra logic that needs to be done on a client side|
---------|------|---------------|--------------------------------------------------------------------------
|Image|Image|If image belongs to client's private repo, rancher-compose.yml file should have registryCredentialId specified|

Extra parameters supported by rancher-compose.yml
---------