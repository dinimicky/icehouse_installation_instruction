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

### Configure the Compute service

> + Install the Compute packages:  
>      `sudo apt-get install nova-compute-kvm python-guestfs`  
> + For security reasons, the Linux kernel is not readable by normal users which restricts hypervisor services such as qemu and libguestfs.  
>      `sudo dpkg-statoverride  --update --add root root 0644 /boot/vmlinuz-$(uname -r)`  

>> + To also enable this override for all future kernel updates, create the file /etc/kernel/postinst.d/statoverride containing:  

>>     `#!/bin/sh`  
>>     `version="$1"`  
>>     `# passing the kernel version is required`  
>>     `[ -z "${version}" ] && exit 0`  
>>     `dpkg-statoverride --update --add root root 0644 /boot/vmlinuz-${version}`  


>> + make the file executable:  
>>     `sudo chmod +x /etc/kernel/postinst.d/statoverride`  

> + Config the [/etc/nova/nova.conf](nova.conf)  
> + Edit the [libvirt] section in the [/etc/nova/nova-compute.conf](nova-compute.conf) file to modify this key because we use the virtualbox:  

>>      [libvirt]  
>>      virt_type = qemu  

> + Remove the SQLite database created by the packages:  
>      `sudo rm /var/lib/nova/nova.sqlite`  

> + Restart the Compute service:  
>      `sudo service nova-compute restart`  

### Configure the Network service  
> + Configure [/etc/sysctl.conf](sysctl.conf) and implement the changes: 
>      `sudo sysctl -p`  
> + To install the Networking components  
>      `sudo apt-get install neutron-common neutron-plugin-ml2 neutron-plugin-openvswitch-agent `  
> + Configure the [/etc/neutron/neutron.conf](neutron.conf)     
> + To configure the Modular Layer 2 (ML2) plug-in in [/etc/neutron/plugins/ml2/ml2_conf.ini](ml2_conf.ini)  
> +     Restart the OVS service:  
>      `sudo service openvswitch-switch restart`  
> + Add the integration bridge:  
>      `sudo ovs-vsctl add-br br-int`  
> + To configure Compute to use Networking in [/etc/nova/nova.conf](nova.conf)  
> + Restart the Compute service:  
>      `sudo service nova-compute restart`  
> + Restart the Open vSwitch (OVS) agent:  
>      `sudo service neutron-plugin-openvswitch-agent restart`  
>

### Configure the Block Storage Service  

> + Create the LVM physical and logical volumes. This guide assumes a second disk /dev/sdc that is used for this purpose:  
>      `sudo pvcreate /dev/sdc`  
>      `sudo vgcreate cinder-volumes /dev/sdc`  
> + After you configure the operating system, install the appropriate packages for the Block Storage service:  
>      `sudo apt-get install cinder-volume`  
> + add keystone_authtoken in [/etc/cinder/cinder.conf](cinder.conf)  
> + Configure Block Storage to use the RabbitMQ message broker in [/etc/cinder/cinder.conf](cinder.conf)  
> + Configure Block Storage to use your MySQL database in [/etc/cinder/cinder.conf](cinder.conf)  
> + Configure Block Storage to use the Image Service in [/etc/cinder/cinder.conf](cinder.conf)  
> + Restart the Block Storage services with the new settings:  
>      `sudo service cinder-volume restart`  
>      `sudo service tgt restart`  

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

