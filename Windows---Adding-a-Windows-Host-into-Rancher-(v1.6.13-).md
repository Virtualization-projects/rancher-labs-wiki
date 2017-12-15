## Host Requirements

* 1 core CPU
* 2+ GB memory
* System OS: Windows Server 2016
* Windows Features: RemoteAccess and Routing - Note: If this is not installed on the machine, steps on how to install are in the directions in Rancher server
* Two NICs with network connected - WHY IS THIS REQUIRED?
* Docker: Version >= 17.06

## Networking Requirements in AWS

1. Create a VPC
2. Create 2 Subnets in the VPC. Ensure that these 2 subnets are in the same availability zone. In the rest of these instructions, we will refer to them as `Subnet A` and `Subnet B`.
3. Create an additional network interface in Subnet B. 

Steps to Create Rancher Windows Environment on AWS

Create a VPC
Create 2 subnet in this VPC. In the following steps, we can them as subnet A and subnet B.
Start a new instance of Rancher Server with subnet A. Run Rancher server and add a Windows environment.
Create a new instance of Windows with subnet A. Wait for success and log in it with Remote Desktop. Subnet will have a public IP for internet.
Add a new network interface in subnet B. Attach it into the Windows instance we just create.
We can see two network interfaces with ipconfig and two IPs on them.
Because Only one public IP can be assigned to an instance, we need to set the default route on the interface that has public IP. In this case, we need to use the interface from subnet A as default route interface. Use get-netadapter to find out the subnet A's and subnet B's interface indexs.
Set subnet B's interface default route RouteMetric greater than subnet A's with Set-netroute -DestinationPrefix 0.0.0.0/0 -ifIndex <subnet-B-interface-index> -RouteMetric 255.
Following the guide from Add host page.
Install latest version of Windows.
Install Windows features of RemoteAccess and Routing.
This two installations require to restart your Windows host.
Set the subnet you what to set to this Windows instance.
Set the route ip for this instance. In this case, you need to set the IP of subnet B.
Optional Set Agent IP
Optional Set labels of host
Click copy and run the powershell commands in Windows host.
After a while, this host will be shown in hosts page.
Metadata and DNS service will run on it automatically.
Play attention to following things:

Setp 8 will be required after you restart the windows host. That is the side effect of two network interfaces attached in one instance.
After the host is ready, you will need to check the agent and per-host-subnet service with following powershell command in Windows host. Get-service rancher-agent and Get-service rancher-per-host-subnet
Check for docker network has been set up docekr network inspect transparent.
Check for the IP of transparent NIC with ipconfig. The IP of transparent NIC should be in the host subnet.
Check for the metadata route in the host with this powershell command get-netroute 169.254.169.250/32. This route will be on transparent NIC.
Check for nat setting with netsh routing ip nat show interface. It should including all the physical network adapter you have.
Steps to Remove Windows Hosts.

Deacitvate and delete the host in Rancher UI.
Run following commands to unreigster services and stop them in windows.
& "c:\program files\rancher\agent.exe --unregister-service"
& "c:\program files\rancher\per-host-subnet.exe --unregister-service"
stop-service rancher-agent
stop-service rancher-per-host-subnet
Remove containers in transparent network.
Remove transparent network in docker with docker command docker network rm transparent.
Use devcon.exe to uninstall virtual NIC. & "c:\program files\rancher\devcon.exe remove *MSLOOP"
Remove rancher folders.
rm "c:\program files\rancher"
rm "c:\programdata\rancher"