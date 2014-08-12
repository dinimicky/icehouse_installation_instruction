### Create one VM 

```
  +-------------+                       
  | controller  |
  |*************|
  |    Mysql    |
  |=============|
  |  RabbitMQ   |
  |=============|                               
  |  Keystone   |
  |=============|                              +-------------+       +-------------+
  |   Glance    |        +-------------+       |  compute1   |       |  compute2   |
  |=============|        |    nework   |       |*************|       |*************|
  | Nova mgmt   |        |*************|       |    nova     |       |    nova     |
  |=============|        |   Neutron   |       |  hypervisor |       |  hypervisor |
  |   Neutron   |        |   L2 Agent  |       |    qemu     |       |    qemu     |
  |   Server    |        |    (OVS)    |       |=============|       |=============|
  |=============|        |=============|       |   Neutron   |       |   Neutron   |
  |   Neutron   |        |   Neutron   |       |   L2 Agent  |       |   L2 Agent  |
  | ML2 Plugin  |        |  L3 Agent   |       |    (OVS)    |       |    (OVS)    |
  |=============|        |=============|       |=============|       |=============|
  |  Dashboard  |        |   Neutron   |       |   Neutron   |       |   Neutron   |
  |=============|        | ML2 Plugin  |       | ML2 Plugin  |       | ML2 Plugin  |
  | Cinder Mgmt |        |=============|       |=============|       |=============|
  |=============|        |   Neutron   |       |    Cinder   |       |    Cinder   |
  | Swift Proxy |        |  DHCP Agent |       |    Volume   |       |    Volume   |
  |=============|        |=============|       |=============|       |=============|
  |Swift Storage|        |Swift Storage|       |Swift Storage|       |Swift Storage|
  +-------------+        +-------------+       +-------------+       +-------------+
  |             |       /    |     |    \      |      |      |       |      |      |  
 eth0          eth2   eth0  eth1  eth2  eth3  eth0   eth1   eth2    eth0   eth1   eth2
  |             |       |    |     |      |    |      |      |       |      |      |  
  |             |       |    |     |      |    |      |      |       |      |      |  
  *-------------|-------*----|-----|------|----*------|------|-------*------|------|------ (Management Network)
                |            |     |      |           |      |              |      |
                |            |     |      |           |      |              |      |
                |            *-----|------|-----------*------|--------------*------|------- (Instance tunnels)
                |                  |      |                  |                     |
                |                  |      |                  |                     |
                *------------------*------|------------------*---------------------*------- (internet)
                                          |
                                          |
                                          *------------------------------------------------ (external network)
```

> + Network Information:
>     + Mgmt Network: 10.0.0.0/24
>     + Instance tunnels Network: 10.0.1.0/24
>     + Internet Network: Provide by VirutalBox NAT network
>     + external Network: 192.168.83.0/24

> + IPv4 Configuration in controller:  
>     1. `source admin-openrc.sh`  
>     2. `neutron net-create ext-net --shared --router:external=True`  
>     3. `neutron subnet-create ext-net --name ext-subnet \`  
>        `--allocation-pool start=192.168.83.10,end=192.168.83.99 \`  
>        `--disable-dhcp --gateway 192.168.83.1 192.168.83.0/24`  
>     4. `source demo-openrc.sh`  
>     5. `neutron net-create demo4-net`  
>     6. `neutron subnet-create demo4-net --name demo4-subnet \`  
>        `--gateway 192.168.1.1 192.168.1.0/24`  
>     7. `neutron router-create demo-router`  
>     8. `neutron router-interface-add demo-router demo4-subnet`  
>     9. `neutron router-gateway-set demo-router ext-net`  


> + IPv6 Configuration in controller:  
>     1. `source demo-openrc.sh`  
>     2. `neutron net-create demo6-net`  
>     3. `neutron subnet-create demo6-net --name demo6-subnet --ip-version 6 --disable-dhcp --gateway 2001:db8:1:1::1 2001:db8:1:1::/64`  
>        `Note: we have to disable the dhcp service because the dhcp6 isn't supported in icehouse. `  
>     4. `neutron router-create demo6-router` 
>     5. `neutron router-interface-add demo6-router demo6-subnet`    

> + Create VM instance
>      Image is from [ubuntu-12.04-server-cloudimg-amd64-disk1.img](https://cloud-images.ubuntu.com/releases/12.04.4/release/ubuntu-12.04-server-cloudimg-amd64-disk1.img)
>     1. `ssh-keygen`  
>     2. `nova keypair-add --pub-key ~/.ssh/id_rsa.pub demo-key`  
>     3. `nova secgroup-add-rule default icmp -1 -1 0.0.0.0/0` 
>     4. `nova secgroup-add-rule default tcp 22 22 0.0.0.0/0`  
>     5. `nova boot --flavor m1.small --image IMAGE_NAME --nic net-id=DEMO4_NET_ID --nic net-id=DEMO6_NET_ID \`  
>         `--security-group default --key-name demo-key ub1`  
>     6. `nova boot --flavor m1.small --image IMAGE_NAME --nic net-id=DEMO4_NET_ID --nic net-id=DEMO6_NET_ID \`  
>         `--security-group default --key-name demo-key ub2`  
>     7. `neutron floatingip-create ext-net`  
>     8. `neutron floatingip-create ext-net`  
>     9. `nova floating-ip-associate FLOATINGIP_ID1 UB1_DEMO4_PORT_ID`  
>     10. `nova floating-ip-associate FLOATINGIP_ID2 UB2_DEMO4_PORT_ID`  

> + Configure IPv6 in instance:
>     1. create eth1.cfg in /etc/network/interfaces.d/, the content is shown as below, the IPv6 Address should keep align with instance IPv6 Address in openstack:   
>        `# The primary network interface`
>        `auto eth1`  
>        `iface eth1 inet6 static`  
>        `address 2001::db8:1:1::5`  
>        `netmask 64`  
>        `gateway 2001::db8:1:1::1`  
>     2. restart network service:
>        `sudo service networking restart`  
