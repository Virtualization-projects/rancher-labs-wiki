# Summary
If you've run into trouble hacking on Rancher, check here for possible solutions.

### Starting over
If you're environment goes sideways and you have a tight deadline looming, sometimes the easiest path forward is to clean up and start over. Stop the cattle server, blow away your database, and destroy all your containers:
```
$CATTLE_HOME/resources/content/db/mysql/drop_tables.sh # Blow away your database
docker rm -fv $(docker ps -qa) # Destroy all containers
```
Restarting the cattle server will recreate the database. Then start up the rancher-agent container again by getting the command from the UI of the management server. Remember that it is under the "Add Host" UI for a Custom/Bare Metal host.
```
Additionally, sometimes, ***boot2docker*** itself can go sideways. Being out of space is a common culprit, but if you don't want to debug, you can just blow it away as well:
```
boot2docker destroy
boot2docker init
boot2docker up
```
### Docker unable to perform memory-related operations on containers
Change the existing GRUB_CMDLINE_LINUX_DEFAULT in /etc/default/grub to:
 
```GRUB_CMDLINE_LINUX_DEFAULT="cgroup_enable=memory swapaccount=1"```
 
Then run sudo update-grub and restart the server.

### Python-agent wont start
Tail the container's output to see what's going wrong:
```
docker logs -f rancher-agent
```
Cattle pushes code and config down to the agent. If you don't have some of the non-java projects built properly, the necessary artifacts might not be around.

One such case: if $CATTLE_HOME/code/agent doesn't have a target dir (and a jar in it), you should run an maven install on it:
```
cd $CATTLE_HOME/code/agent
mvn install
```
Refresh eclipse and restart the cattle server.

Another case: if you haven't build the python-agent project, you won't have the appropriate dist directory to push down to the agent. 
```
cd <path where you cloned python agent>
./scripts/build
```
***Note:*** the above assumes that you are pointing cattle your local python-agent directory via something like.

### Why is setting up cattle so insanely complicated? I just need a running rancher server and host so that I can work on &lt;component foo&gt;
Then don't follow the cowpoke instructions. Follow [these instructions](https://github.com/rancherio/rancher#management-server).

### Liquibase and jOOQ
If you're encountering some strange behaviors with liquibase such as not detecting a column change or jOOQ, make sure you're running java 1.7 instead of 1.8.

### Debugging a process on a Cattle server
There is a way to trace a specific process execution (account.create, container.start, etc) on a cattle server. 
First, request for processInstances [http://localhost:8080/v1/processinstances](http://localhost:8080/v1/processinstances). Locate your process by applying the filtering, get its pid.
Then in terminal, invoke print_process.py and pass the pid:
```
cd $CATTLE_HOME/tests/integration/cattletest/util

./print_process.py 1pi1700
0 seconds 1pi1700 account.create account:78 DONE DONE SUCCESS
  43 ms PROCESS: account.create account:78 DELEGATE
    25 ms HANDLER: AccountCreate
      14 ms PROCESS: credential.create credential:166 DELEGATE
        0 ms HANDLER: AgentApiKeyCreate
        4 ms HANDLER: ApiKeyCreate
        0 ms HANDLER: RegisterTokenCreate
        0 ms HANDLER: SshKeyCreate
        1 ms HANDLER: ActivateByDefault
      5 ms PROCESS: credential.activate credential:166 DONE
    9 ms HANDLER: RegisterTokenAccountCreate
    1 ms HANDLER: ActivateByDefault
  8 ms PROCESS: account.activate account:78 DONE
```
The output is the list of all the processes and handlers invoked by the original process in the order of execution


### My cattle hangs on startup (also, 'Cannot acquire lock' issues)

If your cattle server hangs on startup, it is likely because not being able to acquire a lock to perform initialization operations

In this case remove the database entries which hold the lock by executing the following commands

```
sh>> mysql -u root
mysql>> use cattle;
mysql>> DELETE FROM DATABASECHANGELOGLOCK;
```