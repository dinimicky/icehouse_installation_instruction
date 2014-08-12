icehouse_installation_instruction
=================================

### Pre-assumption:

> the icehouse installation instuction will be based on:
> 
> 1. VirtualBox
> 2. Ubuntu 12.04.4
>
> There are 4 VMs to be created. their roles are shown as below:
>
> 1. controller
> 2. network
> 3. compute1
> 4. compute2
>


>         +-------------+ 
>         | controller  |
>         |*************|
>         |    Mysql    |
>         |=============|
>         |  RabbitMQ   |
>         |=============|
>         |  Keystone   |
>         |=============|
>         |   Glance    |
>         |=============|
>         | Nova mgmt   |
>         |=============|
>         |   Neutron   |
>         |   Server    |
>         |=============|
>         |   Neutron   |
>         | ML2 Plugin  |
>         |=============|
>         |  Dashboard  |
>         |=============|
>         | Cinder Mgmt |
>         |=============|
>         | Swift Proxy |
>         |=============|
>         |Swift Storage|
>         +-------------+ 
>
>         +-------------+ 
>         |    nework   |
>         |*************|
>         |   Neutron   |
>         |   L2 Agent  |
>         |    (OVS)    |
>         |=============|
>         |   Neutron   |
>         |  L3 Agent   |
>         |=============|
>         |   Neutron   |
>         | ML2 Plugin  |
>         |=============|
>         |   Neutron   |
>         |  DHCP Agent |
>         |=============|
>         |Swift Storage|
>         +-------------+ 
>
>         +-------------+ 
>         |  compute1   |
>         |*************|
>         |    nova     |
>         |  hypervisor |
>         |    qemu     |
>         |=============|
>         |   Neutron   |
>         |   L2 Agent  |
>         |    (OVS)    |
>         |=============|
>         |   Neutron   |
>         | ML2 Plugin  |
>         |=============|
>         |    Cinder   |
>         |    Volume   |
>         |=============|
>         |Swift Storage|
>         +-------------+ 
>
>         +-------------+ 
>         |  compute2   |
>         |*************|
>         |    nova     |
>         |  hypervisor |
>         |    qemu     |
>         |=============|
>         |   Neutron   |
>         |   L2 Agent  |
>         |    (OVS)    |
>         |=============|
>         |   Neutron   |
>         | ML2 Plugin  |
>         |=============|
>         |    Cinder   |
>         |    Volume   |
>         |=============|
>         |Swift Storage|
>         +-------------+ 

### VM Configuration

> 
> + controller:
>   + CPU: 1 
>   + Mem: 2048MB
>   + Disk: 2 vdi disks, each size=100GB
>   + Network: 2 NICs
>      + NIC 1(host-only): Management Network
>      + NIC 2(NAT): internet
>
> + network:
>   + CPU: 1 
>   + Mem: 512MB
>   + Disk: 2 vdi disks, each size=100GB
>   + Network: 3 NICs
>      + NIC 1(host-only): Management Network
>      + NIC 2(internal network): instance tunnels
>      + NIC 3(NAT): internet
>      + NIC 4(bridge network): external network (promisuous: allow all)
>
> + compute1, compute2:
>   + CPU: 2 
>   + Mem: 2048MB
>   + Disk: 3 vdi disks, each size=100GB
>   + Network: 3 NICs
>      + NIC 1(host-only): Management Network
>      + NIC 2(internal network): instance tunnels
>      + NIC 3(NAT): internet
>

### Network

> Management Network: 10.0.0.0/24  
>> *Note: host-only network should be created in VirtualBox and set the gateway ip as 10.0.0.1/24*     
>
> instance tunnels: 10.0.1.0/24  
> internet: dhcp, provided by the VirtualBox   

### Openstack command-line clients
> You can get the relate information in [Chapter 4. Install and configure the OpenStack clients](http://docs.openstack.org/icehouse/install-guide/install/apt/content/ch_clients.html)  
> 
