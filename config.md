# Configuration Instruction

### Ubuntu Installation procedure

>  1. download and mount the 'ubuntu-12.04.4-server-amd64' iso in the virtualbox  
>  2. select language: 'English'  
>  3. select "Install Ubuntu Server"  
>  4. select a language: 'English'  
>  5. select your location: 'United States'  
>  6. 'Detect keyboard layout?' select No, and then select 'English(US)' 
>  7. Primary network interface: select ethX which connects to the internet.  
>  8. hostname: use the related role name such as controller, network, compute1, compute2  
>  9. user and password: cloud:cloud  
>  10. encrypt your home directory: select No  
>  11. maybe it will probe the time zone, and then ask you to select, you can select the right answer by your actual situation.  
>  12. partition method: use the entire disk and set up LVM, and then select the sda to do it.  
>  13. http proxy is none.  
>  14. manage upgrades: No automatic updates.  
>  15. choose software to install: OpenSSH Server.  
>  16. install the GRUB boot loader: select Yes.  
> 


### Passwords
> | Service name | content |
> | :----------- | :-----: |
> | MYSQL_PASS | openstack |
> | RABBIT_PASS | openstack |
> | KEYSTONE_DBPASS | openstack |
> | DEMO_PASS | demo |
> | ADMIN_PASS | openstack |
> | GLANCE_DBPASS | openstack |
> | GLANCE_PASS | openstack |
> | NOVA_DBPASS | openstack |
> | NOVA_PASS | openstack |
> | DASH_DBPASS | openstack |
> | CINDER_DBPASS | openstack |
> | CINDER_PASS | openstack |
> | NEUTRON_DBPASS | openstack |
> | NEUTRON_PASS | openstack |
> | SWIFT_DBPASS | openstack |
> | SWIFT_PASS | openstack |

