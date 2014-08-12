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

> + Create the network in controller:  
>     1. `source admin-openrc.sh`  
>     2. `neutron net-create ext-net --shared --router:external=True`  
>     3. `neutron subnet-create ext-net --name ext-subnet \`
  --allocation-pool start=FLOATING_IP_START,end=FLOATING_IP_END \
  --disable-dhcp --gateway EXTERNAL_NETWORK_GATEWAY EXTERNAL_NETWORK_CIDR


