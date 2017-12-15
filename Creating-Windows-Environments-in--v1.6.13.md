## Windows Host Requirements

System OS: Windows Server 2016

Required Applications
* Windows Features: RemoteAccess and Routing - Note: If this is not installed on the machine, steps on how to install are in the directions in Rancher server
* Docker: Version >= 17.06

AWS Instance Requirements
* 1 core CPU
* 2+ GB memory
* One Subnet will be for NAT and used across Rancher Server and any hosts added
* One Subnet will be for the overlay network that allows cross host communication 

## Networking Requirements in AWS

1. Create a VPC
2. Create 2 subnets in the VPC. Ensure that these 2 subnets are in the same availability zone.
  * One subnet will be used for NAT (`Subnet A`)
  * One subnet will be used for creating the overlay network (`Subnet B`)
3. Create an additional network interface in `Subnet B`. For each additional host that is added, an additional network interface will need to be created in `Subnet B`. 

## Launching Rancher Server

1. Launch Rancher Server in an AWS EC2 instance that is in `Subnet A`. Ensure the instance meets the [Rancher server](http://rancher.com/docs/rancher/v1.6/en/installing-rancher/installing-server/#requirements). 
2. In Rancher, create a new Windows environment. 
> Note: If you choose to add a new Windows environment template, you will need to **Disable** Ipsec before being able to switch container orchestrations. 

## Launching AWS EC2 Hosts with Windows and setting Networking up

1. Launch a new AWS EC2 instance that is in `Subnet A` and meets the [Windows hosts requirements](#windows-hosts-requirements).
2. After the instance is running, log in to the instance using Remote Desktop. The instance will already have a public IP from `Subnet A`. 
3. Attach the additional network interface to the instance. Select the instance in AWS, click on **Actions** -> **Networking** -> **Attach Network Interface**. Remember, this network interface needs to be in the same availability zone as your instance, and it should be in `Subnet B`. 
4. After attaching the additional network interface, restart the AWS EC2 instance.
5. After you can log back into the instance, there are currently two public IPs on the instance due to the 2 NICs. You can verify that there are two IPs by running `ipconfig`. We only want one public IP to be assigned to the instance. We need to set the default route on the interface from `Subnet A` that will have the public IP as this is the subnet for NAT. 
6. Set static ip of subnet B instead of DHCP. It will ensure that the route metric of subnet B will be greater than subnet A's. And then, the default route always will be subnet A.

```
$ip=Get-NetIPAddress -ipaddress <subnet-B-ipaddress>
set-NetIPInterface -ifIndex $ip.ifIndex -AddressFamily $ip.AddressFamily -Dhcp Disabled
Remove-NetIPAddress -ifIndex $ip.ifIndex -AddressFamily $ip.AddressFamily -Confirm:$false
$ip | New-NetIPAddress
```

## Adding Hosts into Rancher

In Rancher Server, click on **Infrastructure** -> **Add Hosts** in the Windows environment. Follow the instructions on the screen. 

* Subnet: Set the subnet that will be used to launch the containers on the host. In order to support an overlay network in Windows, each host in the environment must have a unique subnet.
* Route IP: Set the route IP for this instance, which is used to forward network traffic between the different hosts. In our AWS EC2 example, the route IP is the IP of `Subnet B`. 
* Agent IP (Optional): This is the public IP of the AWS EC2 instance, which is used in the Rancher agent. 

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

## Steps to Remove Windows Hosts

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