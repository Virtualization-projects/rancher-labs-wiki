### Who this is for
You are developing rancher locally AND using k3s(k3d) for your local kubernetes AND want to back up your data

### Why backup dev data
Simulating rollbacks or plan on doing possibly destructive things

### Hoow
1. list docker containers: `docker ps`

2. Find running container using image rancher/k3s:v* 

3. Substitute container id <container-id> in following steps 

4. Shell into k3s container: `docker exec -it <container-id> /bin/sh `

5. Create folder for backup on local: `mkdir <folder-name>` 

6. Copy sqllite state files to folder made in mkdir: `docker cp a4e6be43503b:/var/lib/rancher/k3s/server/db/ <folder-name>/`

7. (Optional) you can zip/tarball backup for posterity: `tar zcvf /backup/rancher-data-backup-<RANCHER_VERSION>-<DATE>.tar.gz <backup-directory>`

Restore local k3s using backup 

1. list docker containers: `docker ps` 

2. Find running container using image rancher/k3s:v* 

3. Substitute container id <container-id> in following steps 

4. Copy sqllite state files from backup folder to k3s container: `docker cp <folder-name>/.  <container-id>:/var/lib/rancher/k3s/server/db/ `

 