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

> + In compute Node:  
>        `sudo iptables -t nat -A neutron-openvswi-PREROUTING \`  
>        `-d 169.254.169.254/32 -p tcp -m tcp --dport 80 -j DNAT --to-destination 10.0.0.11:8775`  

