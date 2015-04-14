If you haven't set up Rancher, please refer to this [set up guide](https://github.com/rancherio/rancher/wiki/Cowpoke-1%3A-Getting-Started-with-Rancher). This documentation assumes that you have already set up Rancher.

## Hosts

### How do I add a new Host?
Click on the **Add Host** image.  You can either add hosts using the Cloud providers that we work by following the UI. Alternatively, you can select the **Custom** option. This option allows you to connect any Linux machine that has the latest version of Docker running on it. The UI will also provide instructions on how to set up an existing Linux machine.

### Where do I get a Digital Ocean access token? 
You must have a Digital Ocean account in order to get a Digital Ocean access token.  If you already have an account, you can create an access token on [Applications & API page](https://cloud.digitalocean.com/settings/applications).  

If you don’t have an account, [sign up using our link](https://www.digitalocean.com/?refcode=9a390c23d815) and get $10 in credit. In order to activate the credit, you will be required to input billing details.

### What are all the different states that my host can display?
We try to provide as much detail to the user as possible so that you can understand what's going on.

* _Creating_: When adding a new host through the UI, Rancher has many steps that occur to be in touch with the cloud provider. In the host, there are many messages about the actions that are being performed. 
* _Active_: The host is up and running and communicating with Rancher. This state will be green to allow an easy check to see if there are any issues with hosts. In this state, you can add new containers and perform any actions on the containers. 
* _Deactivating_: While the host is being deactivated, the host will display this state until the deactivation is complete. Upon completion, the host will move to an _Inactive_ state.
* _Inactive_: The host has been deactivated. No new containers can be deployed and you will be able to perform actions (start/stop/restart) on the existing containers. The host is still connected to the Rancher server.
* _Activating_: From an _Inactive_ state, if the host is being activated, it will be in this state until it's back to an _Active_state.
* _Removed_: The host has been deleted by the user. This is the state that will show up on the UI until Rancher has completed the necessary actions. 
* _Purged_: The host is only in this state for a couple of seconds before disappearing from the UI. 
* _Reconnecting_: The host has lost its connection with Rancher. Rancher will attempt to restart the communication.

### How do I remove a host from my Rancher server?
In order to remove a host from the server, you will need to do a couple of step located in the host’s drop down menu. In order to view the drop down, hover over the host and a drop down icon will appear.

1. Select **Deactivate**. 
1. When the host has completed the deactivation, the host will display an _Inactive_ state. Select **Delete**. The server will start the removal process of the host from the Rancher server instance.  
 1. Notes: All containers including the Rancher agent will continue to remain on the host.  The first state that it will display after it’s finished deleting it will be _Removed_. It will continue to finalize the removal process and move to a _Purged_ state before immediately disappearing from the UI. 
1. Optional: Once the host ‘s status displays _Removed_, you can purge the host to remove it from the UI faster.  We have this option for the user so that if any errors occur during the removal process, it can be displayed in between the _Removed_ and _Purged_ states. 

### What happens when I deactivate my host?
Deactivating the host will put the host into an _Inactive_ state. In this state, no new containers can be deployed. Any active containers on the host will continue to be active and you will still have the ability to perform actions on these containers (start/stop/restart). The host will still be connected to the Rancher server.

### How do I get my _Inactive_ host up again? 
In the host’s drop down menu, select **Activate**. The host will become _Active_ and the ability to add additional containers will become available. 

### What happens if my host is deleted outside of Rancher?
If your host is deleted outside of Rancher, then Rancher server will continue to show the host until it’s removed. Typically, these hosts will show up in a _Reconnecting_ state and never be able to reconnect. You will be able to **Delete** these hosts to remove them from the UI. 

### Why does my host have this weird name (e.g. 1ph7)? 
If you didn’t enter a name during host creation, the UI will automatically assign a name. This name is displayed on the host. 

## Containers

### How do I add containers?
There are 2 ways to add containers to a host.

Option 1: On the Hosts view, go to the host that you want to add containers to. Click on the **Add Container** that is located beneath the host. It will display the detailed page for adding containers.

Option 2: On the Containers view, click on the **Add Container** button located at the top right corner. This will display the detailed page for adding containers.

### What is the Network Agent container? I didn’t request to create it.
The Network Agent container is a Rancher specific container that is deployed upon the creation of the first container on a host. This container is what Rancher uses to allow containers between different hosts be able to communicate with each other. You will not have the ability to do any commands (start/stop/restart/etc) to this container through the UI,

### What are my options with a container? 

If you hover over the container, a drop down will appear on the right hand side.

* **Restart:** The container will be restarted.
* **Stop:** The container will be stopped.
* **Start:** This option appears if the container is already stopped. This will start the container again.
* **Delete:** The container will be deleted from the host.
* **View in API:** This will bring up the API for the specific container. If Access Control has been enabled, you will be prompted for a username/password.
* **Execute Shell:** You will be connected to the shell and be able to run commands in the container. Note: If you changed the IP of the host to a private IP, you will no longer be able to access this command. 
* **View Logs:** This will show the docker logs -f on the container.
* **Edit:** In the Edit screen, you have the ability to update some of the settings regarding the container. 

### Why does my container still show up after I have deleted it?
In the background, Rancher is taking care of the necessary steps to remove the container. Upon completion, Rancher UI will automatically refresh and remove the container. 

If you had accidentally deleted your container, you still have an option to save the container from deletion. Using the container’s drop down, you can **Restore** the container and it will stop it from being deleted. Restoring the container will keep it from being deleted, but you will need to **Start** the container to get it running again.

If you want to make the container disappear from your Rancher UI immediately, you can **Purge** the container after deleting it. 

### Why do my deleted containers still show up on the Containers page?
Even if the container is no longer showing on the hosts page, the Container may still show up on the Containers page. Since Rancher needs to spend some time deleting the Container, it will show up on the Container page as _Removed_ until Rancher has finished cleaning it up. If you’d like to have it removed immediately, you can use the drop down menu to **Purge** the container.

## Registries

### What is a Private Registry?
A private registry is a private repository that has images that are not public. By adding a private registry to Rancher, you can access these private images when creating containers.  


## API Keys

### When is an API Key required?
If Access Control is not configured, there is no need for an API Key as anyone can access the Rancher server and therefore can access the API. 

### What is an API Key?
An API Key is a username and password that allows you to view the API. The API is restricted when access control is enabled. 

> **Note:** When creating the API key, please note the password as it won’t be shown again after creation.  

## Access Control

### What does “Access Control is not configured” mean?
Without Access Control, your Rancher server is public and anyone with the IP can access the server. 

### How do I configure Access Control?
Click on **Settings** and the instructions on how to set up Access Control are shown.

### Why does it have to be a GitHub authentication? What if I don’t have a GitHub account?
At this time, Rancher only supports GitHub authentication. If you don’t have a GitHub account, please consider signing up for one!

## Miscellaneous 

### How do I upgrade my Rancher server and save my configuration data?
Currently, upgrades are **NOT** officially supported between releases before we hit a GA release. Therefore, certain features might break in later versions as we enhance them.

Recently, we changed default DB backends in release v0.10.x to MySQL from H2. There is no migration plan between the two formats. You can continue to use H2 for smaller environments if you choose, but we recommend moving to MySQL.

The procedure we follow when we upgrade is outlined below. We typically only go from one version to the next if we do upgrade.

Use the original Rancher Server container to be your DB server forever. Any changes that are made in the upgraded version will always be saved in the original Rancher Server container.

> **Note:** Do not remove the original Rancher Server container at any time! 

1. Find the container name of Rancher Server.
```
docker ps
````
2. Stop the container.
```
docker stop <container name of old server>
```
3. Optional: Backup the data.
  * Create another container with `--volumes-from=<name_of_old_server>`
  * Copy the contents of /var/lib/cattle to someplace safe.
  * if you are running >v0.10.0 also copy /var/lib/mysql
     ** on containers >=v0.10.0 3306 is exposed and you could publish and create MySQL slaves.

4. Pull the most recent image of Rancher Server. Note: If you skip this step and try to run the latest image, it will not automatically pull an updated image.
```
docker pull rancher/server:latest
```
5. Run this command to start the Rancher Server container using the data from the original Rancher Server container. 

```
# MySQL
docker run -d --volumes-from=<container name of old server> -p 8080:8080 rancher/server:<version>
# H2
docker run -d --volumes-from=<container name of old server> -e CATTLE_DB_CATTLE_DATABASE=h2 -p 8080:8080 rancher/server:<version>
```

You can also configure an external MySQL database server by setting these environment variables on the container. This allows you to decouple the server from the DB.

```
    CATTLE_DB_CATTLE_MYSQL_HOST
    CATTLE_DB_CATTLE_MYSQL_PORT
    CATTLE_DB_CATTLE_USERNAME
    CATTLE_DB_CATTLE_PASSWORD
    CATTLE_DB_CATTLE_MYSQL_NAME
```

### How does the host determine IP address and how can I change it?

When the agent connects to Rancher server, it auto detects the IP of the agent. Sometimes, the IP that is selected is not the IP that you want to use. You can override this setting and set the host IP to what you want. 

In order to update the IP address for a host, you will need to alter the registration command for the host. You will need to set the CATTLE_AGENT_IP to the IP address that you want to use. 

If you already have hosts running, you just need to rerun the agent registration command. If you have any containers existing on the host, please follow the upgrade instructions in order to have the containers remained on your host.

> **Note:** You should not update the IP of a host to the docker0 interface on the host machine. 

Typically, the registration command from the UI follows this template:
```bash
sudo docker run -d --privileged -v /var/run/docker.sock:/var/run/docker.sock rancher/agent:v0.5.2 http://MANAGEMENT_IP:8080/v1/scripts/SECURITY_TOKEN
```
The command will need to be edited to include setting the CATTLE_AGENT_IP by adding the **-e CATTLE_AGENT_IP=x.x.x.x**

```bash
sudo docker run -d --privileged -v /var/run/docker.sock:/var/run/docker.sock –e CATTLE_AGENT_IP=x.x.x.x rancher/agent:v0.5.2 http://MANAGEMENT_IP:8080/v1/scripts/SECURITY_TOKEN
```
> **Note:** When override the IP address, if there are existing containers in the rancher server, those hosts will no longer to be able to ping the host with the new IP. We are working to fix this issue, but please update the IP address with caution.

### The subnet used by Rancher is already used in my network. How do I change the subnet?

In order for Rancher to work with a new subnet, you will need to start with a fresh install of Rancher. Before adding any hosts, you will need to update the subnet table with the new subnet IDs.

Within the Rancher server VM, you will need to follow these steps to update the subnet tables.

```bash
$ docker exec -it SERVER_CONTAINER_ID bash
SERVER_CONTAINER_ID$ mysql
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 5.5.41-0ubuntu0.14.04.1 (Ubuntu)

Copyright (c) 2000, 2014, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>
```

Get the cattle databases.

```bash
mysql> use cattle;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

Confirm that you have access to the subnet table.

```bash
mysql> show tables; 
+-----------------------------------------------+
| Tables_in_cattle                              |
+-----------------------------------------------+
| DATABASECHANGELOG                             |
| DATABASECHANGELOGLOCK                         |
| account                                       |
| agent                                         |
| agent_group                                   |
| certificate                                   |
| cluster_host_map                              |
| config_item                                   |
| config_item_status                            |
| credential                                    |
| credential_instance_map                       |
| data                                          |
| environment                                   |
| external_handler                              |
| external_handler_external_handler_process_map |
| external_handler_process                      |
| generic_object                                |
| global_load_balancer                          |
| host                                          |
| host_ip_address_map                           |
| host_vnet_map                                 |
| image                                         |
| image_storage_pool_map                        |
| instance                                      |
| instance_host_map                             |
| instance_link                                 |
| ip_address                                    |
| ip_address_nic_map                            |
| ip_association                                |
| ip_pool                                       |
| load_balancer                                 |
| load_balancer_config                          |
| load_balancer_config_listener_map             |
| load_balancer_host_map                        |
| load_balancer_listener                        |
| load_balancer_target                          |
| mount                                         |
| network                                       |
| network_service                               |
| network_service_provider                      |
| network_service_provider_instance_map         |
| nic                                           |
| offering                                      |
| physical_host                                 |
| port                                          |
| process_execution                             |
| process_instance                              |
| resource_pool                                 |
| service                                       |
| service_consume_map                           |
| service_expose_map                            |
| setting                                       |
| storage_pool                                  |
| storage_pool_host_map                         |
| subnet                                        |
| subnet_vnet_map                               |
| task                                          |
| task_instance                                 |
| vnet                                          |
| volume                                        |
| volume_storage_pool_map                       |
| zone                                          |
+-----------------------------------------------+
62 rows in set (0.00 sec)
```

Update the subnet table. 

```bash
mysql> update subnet set network_address='10.41.0.0', start_address='10.41.0.2', end_address='10.41.255.250', gateway='10.41.0.1' where id=1;                             
Query OK, 1 row affected (0.01 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

After the subnet table has been updated, you can add hosts/containers to the Rancher server and it will use the new subnet ID for the containers.


