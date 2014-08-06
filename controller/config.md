# Configuration Instruction

### Basic Configuration:

> 1. configure the [/etc/network/interfaces](interfaces)  
> 2. configure the [/etc/hosts](hosts)  
> 3. install ntp package: ' sudo apt-get install ntp'  
> 4. configure the [/etc/ntp.conf](ntp.conf)  
> 5. restart ntp service: 'sudo service ntp restart'  
> 6. verify ntp service: 'ntpq -p'  

### Database Configuration

> 1. install the mysql: 'sudo apt-get install python-mysqldb mysql-server'  
> 2. configure the [/etc/mysql/my.cnf](my.cnf)  
> 3. restart mysql service: 'sudo service mysql restart'  
> 4. mysql security: 'sudo mysql_install_db', 'sudo mysql_secure_installation'  

### Openstack Configuration

> + Install the Ubuntu Cloud Archive for Icehouse:
>
>>     sudo apt-get install python-software-properties  
>>     sudo add-apt-repository cloud-archive:icehouse  
>
> + Update the package database and upgrade your system:
>
>>     sudo apt-get update  
>>     sudo apt-get dist-upgrade  
> 
> + install the stability backported linux kernel:
>
>>     sudo apt-get install linux-image-generic-lts-saucy linux-headers-generic-lts-saucy  
>
> + reboot the system:  
>
>>     sudo reboot
>

### Message Server Configuration

> + Install rabbitmq:
>
>>     sudo apt-get install rabbitmq-server
>
