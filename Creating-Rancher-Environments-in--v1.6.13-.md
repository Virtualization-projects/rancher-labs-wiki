## Windows Host Requirements

* System OS: Windows Server 2016
* Windows Features: RemoteAccess and Routing - Note: If this is not installed on the machine, steps on how to install are in the directions in Rancher server

* 1 core CPU
* 2+ GB memory
* Two NICs with network connected - YUXING - WHY IS THIS REQUIRED?
* Individual Subnets for each host

* Docker: Version >= 17.06

## Networking Requirements in AWS

1. Create a VPC
2. Create 2 Subnets in the VPC. Ensure that these 2 subnets are in the same availability zone. In the rest of these instructions, we will refer to them as `Subnet A` and `Subnet B`.
3. Create an additional network interface in `Subnet B`. 

## Launching Rancher Server

1. Launch Rancher Server in an AWS EC2 instance that is in `Subnet A`. Ensure the instance meets the [Rancher server](http://rancher.com/docs/rancher/v1.6/en/installing-rancher/installing-server/#requirements). 
2. In Rancher, create a new Windows environment. 
> Note: If you choose to add a new Windows environment template, you will need to **Disable** Ipsec before being able to switch container orchestrations. 

## Launching AWS EC2 Hosts with Windows and setting Networking up

1. Launch a new AWS EC2 instance that is in `Subnet A` and meets the [Windows hosts requirements](#windows-hosts-requirements).
2. After the instance is running, log in to the instance using Remote Desktop.  
3. Attach the additional network interface to the instance. Select the instance in AWS, click on **Actions** -> **Networking** -> **Attach Network Interface**. Remember, this network interface needs to be in the same availability zone as your instance, but should be in `Subnet B`. 
4. After attaching the additional network interface, restart the instance.
5. After you can log back into the instance, there are currently two public IPs on the instance due to the 2 NICS. You can verify that there are two IPs by running `ipconfig`. Since only one public IP can assigned to the instance, we need to set the default route on the interface from `Subnet A` that will have the public IP. 
6. In order to set `Subnet B`'s interface default route, we will need to use `get-netadapter` to determine the interface indices . After determining the indices, we run this command to set `Subnet B`'s interface default route `RouteMetric` greater  than `Subnet A`'s. 

```
Set-netroute -DestinationPrefix 0.0.0.0/0 -ifIndex <subnet-B-interface-index> -RouteMetric 255
```

## Adding Hosts into Rancher

In Rancher Server, click on **Infrastructure** -> **Add Hosts** in the Windows environment. Follow the instructions on the screen. 

* Subnet: Set the subnet you what to set to this Windows instance.
* Route IP: Set the route IP for this instance. <Describe what is the Route IP> In our AWS EC2 example, the route IP is the IP of `Subnet B`.  case, you need to set the IP of subnet B.
* Agent IP (Option): This is the public IP of the instance. 

After running the custom command to add the hosts, you'll need to wait a couple minutes before the host is up and running in Rancher. There will be a couple infrastructure stacks launched and running on the hosts.

## Troubleshooting

### Agent
If your host isn't running, you can check the Rancher agent is running correctly. This service was launched through the `agent-windows` container. 

```
Get-service rancher-agent
```

### Networking 

If the agent is running correctly, but there is no networking, check on the networking services (`per-host-subnet`).

```
Get-service rancher-per-host-subnet
```

Confirm that the Docker network has been established.
```
docker network inspect transparent
```

Check for the IP of transparent NIC with `ipconfig`. The IP of the transparent NIC should be in the host subnet.

Check that the metadata route in the host is on the transparent NIC. 

```
get-netroute 169.254.169.250/32
```

Check the NAT setting and confirm it includes all the physical network adapters being used. 

## Steps to Remove Windows Hosts.

1. In the Rancher UI, deactivate and delete the host under **Infrastructure**.
2. On the Windows host, run following commands to un-register services and stop them.
```
& "c:\program files\rancher\agent.exe --unregister-service"
& "c:\program files\rancher\per-host-subnet.exe --unregister-service"
stop-service rancher-agent
stop-service rancher-per-host-subnet
```

3. On the Windows host, remove any containers in the transparent network.
4. On the Windows host, remove the transparent network in Docker. 
```
docker network rm transparent
```
5. Use devcon.exe to uninstall the virtual NIC 
```
& "c:\program files\rancher\devcon.exe remove *MSLOOP"
```

6. Remove the folders created by Rancher.
```
rm "c:\program files\rancher"
rm "c:\programdata\rancher"
```