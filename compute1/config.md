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
>>     `chmod +x /etc/kernel/postinst.d/statoverride`  

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
> 
