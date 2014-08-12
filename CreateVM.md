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
>        `--allocation-pool start=FLOATING_IP_START,end=FLOATING_IP_END \`  
>        `--disable-dhcp --gateway EXTERNAL_NETWORK_GATEWAY EXTERNAL_NETWORK_CIDR`  
>     4. `source demo-openrc.sh`  
>     5. `neutron net-create demo4-net`  
>     6. `neutron subnet-create demo4-net --name demo4-subnet \`  
>        `--gateway TENANT_NETWORK_GATEWAY TENANT_NETWORK_CIDR`  
>     7. `neutron router-create demo-router`  
>     8. `neutron router-interface-add demo-router demo4-subnet`  
>     9. `neutron router-gateway-set demo-router ext-net`  
>     10. `ssh-keygen`  
>     11. `nova keypair-add --pub-key ~/.ssh/id_rsa.pub demo-key`  
>     12. `nova secgroup-add-rule default icmp -1 -1 0.0.0.0/0` 
>     13. `nova secgroup-add-rule default tcp 22 22 0.0.0.0/0`  
>     14. `nova boot --flavor m1.small --image IMAGE_NAME --nic net-id=DEMO_NET_ID \`  
>         `--security-group default --key-name demo-key demo-instance1`  
>     15. `neutron floatingip-create ext-net`  
>     16. `nova floating-ip-associate demo-instance1 FLOATING_IP`  
