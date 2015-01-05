# Summary
If you've run into trouble hacking on Rancher, check here for possible solutions.

### Starting over
If you're environment goes sideways and you have a tight deadline looming, sometimes the easiest path forward is to clean up and start over. Stop the cattle server, blow away your database, and destroy all your containers:
```
$CATTLE_HOME/resources/content/db/mysql/drop_tables.sh # Blow away your database
docker rm -f $(docker ps -qa) # Destroy all containers
```
Restarting the cattle server will recreate the database. Then start up the rancher-agent container again:
```
docker run --rm -it -v /var/run/docker.sock:/var/run/docker.sock rancher/agent http://10.0.2.2:8080
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
