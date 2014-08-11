# Configuration Instruction

### Basic Configuration:

> 1. configure the [/etc/network/interfaces](interfaces)  
> 2. configure the [/etc/hosts](hosts)  
> 3. install ntp package: ' sudo apt-get install ntp'  
> 4. configure the [/etc/ntp.conf](ntp.conf)  
> 5. restart ntp service: 'sudo service ntp restart'  
> 6. verify ntp service: 'ntpq -p'  

### Database Client Configuration

> 1. install the mysql: 'sudo apt-get install python-mysqldb'  

### Openstack Configuration

> + Install the Ubuntu Cloud Archive for Icehouse:
>
>     `sudo apt-get install python-software-properties`  
>     `sudo add-apt-repository cloud-archive:icehouse`    
>
> + Update the package database and upgrade your system:
>
>      `sudo apt-get update`  
>      `sudo apt-get dist-upgrade`  
> 
> + install the stability backported linux kernel:
>
>      `sudo apt-get install linux-image-generic-lts-saucy linux-headers-generic-lts-saucy`  
>
> + reboot the system:  
>
>      `sudo reboot`  
>

### Configure network service

> + Configure [/etc/sysctl.conf](sysctl.conf) and implement the changes: 
>      `sudo sysctl -p`  
> + To install the Networking components  
>      `sudo apt-get install neutron-plugin-ml2 neutron-plugin-openvswitch-agent \`  
>      `openvswitch-datapath-dkms neutron-l3-agent neutron-dhcp-agent`  

>> + Note: Ubuntu installations using Linux kernel version 3.11 or newer do not require the openvswitch-datapath-dkms package.  
> + To configure the [/etc/neutron/neutron.conf](neutron.conf)  
> + To configure the DHCP agent in [/etc/neutron/dhcp_agent.ini](dhcp_agent.ini)  
> + To configure the metadata agent in [ /etc/neutron/metadata_agent.ini](metadata_agent.ini)  
> + On the controller node, add the configuration in /etc/nova/nova.conf   
>      `[DEFAULT]`  
>      `...`  
>      `service_neutron_metadata_proxy = true`  
>      `neutron_metadata_proxy_shared_secret = METADATA_SECRET`  
> + Restart the Compute API service on the controller node:  
>      `sudo service nova-api restart`  
> + To configure the Modular Layer 2 (ML2) plug-in in [/etc/neutron/plugins/ml2/ml2_conf.ini](ml2_conf.ini)  
> + To configure the Layer-3 (L3) agent in [/etc/neutron/l3_agent.ini](l3_agent.ini)  
> + To configure the Open vSwitch (OVS) service:  
>      `sudo service openvswitch-switch restart`  
>      `sudo ovs-vsctl add-br br-int`  
>      `sudo ovs-vsctl add-br br-ex`  
>      `sudo ovs-vsctl add-port br-ex eth3`  
>      `sudo service neutron-plugin-openvswitch-agent restart`  
>      `sudo service neutron-l3-agent restart`  
>      `sudo service neutron-dhcp-agent restart`  
>      `sudo service neutron-metadata-agent restart`  

### Configure the Object storage service  

> + Create the configuration directory:  
>      `sudo mkdir -p /etc/swift`  

> + Create [/etc/swift/swift.conf](swift.conf):  
>      `[swift-hash]`  
>      `# random unique string that can never change (DO NOT LOSE)`  
>      `swift_hash_path_prefix = xrfuniounenqjnw`  
>      `swift_hash_path_suffix = fLIbertYgibbitZ`  

> + Install storage node packages:  
>      `sudo apt-get install swift swift-account swift-container swift-object xfsprogs`  
> + For each device on the node that you want to use for storage, set up the XFS volume (/dev/sdb is used as an example).  
>      `sudo fdisk /dev/sdb`  
>      `sudo mkfs.xfs /dev/sdb1`  
>      `sudo sh -c 'echo "/dev/sdb1 /srv/node/sdb1 xfs noatime,nodiratime,nobarrier,logbufs=8 0 0" >> /etc/fstab'`  
>      `sudo mkdir -p /srv/node/sdb1`  
>      `sudo mount /srv/node/sdb1`  
>      `sudo chown -R swift:swift /srv/node`  
>      ` sudo chown -R swift:swift /etc/swift`  
> + Create [/etc/rsyncd.conf](rsyncd.conf)  
> + Edit the following line in [/etc/default/rsync](rsync)  
> + Start the rsync service:  
>      `sudo service rsync start`  
> + Create the swift recon cache directory and set its permissions:  
>      `sudo mkdir -p /var/swift/recon`  
>      `sudo chown -R swift:swift /var/swift/recon`  



