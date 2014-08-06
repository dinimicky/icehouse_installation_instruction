icehouse_installation_instruction
=================================

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
> the each configuration of the 4 VMs in VirtualBox.
> 
> + controller:
>   + CPU: 1 
>   + Mem: 1024MB
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
