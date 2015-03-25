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

### What if my box has a private IP and public IP address? How can I set my host to use the private IP address?

In order to use the private IP address for a host, you will need to alter the registration command for the host. The CATTLE_AGENT_IP will need to be set to the private IP for the host. 

Typically, the command from the UI is:
```bash
sudo docker run -d --privileged -v /var/run/docker.sock:/var/run/docker.sock rancher/agent:v0.5.2 http://MANAGEMENT_IP:8080/v1/scripts/SECURITY_TOKEN
```
The command will need to be edited to include setting the CATTLE_AGENT_IP.
```bash
sudo docker run -d --privileged -v /var/run/docker.sock:/var/run/docker.sock –e CATTLE_AGENT_IP=PRIVATE_IP rancher/agent:v0.5.2 http://MANAGEMENT_IP:8080/v1/scripts/SECURITY_TOKEN
```
> **Note:** When setting the private IP address, if there are existing containers in the rancher server, those hosts will no longer to be able to ping the host with the new IP.
