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

### Message Server Configuration

> + Install rabbitmq:
> 
>      `sudo apt-get install rabbitmq-server`  
>
> + set the passworkd of the rabbitmq user 'guest'
>  
>      `sudo rabbitmqctl change_password guest openstack`
> 

### Identity Service configuration

> + install keystone: sudo apt-get install keystone  
> + Specify the location of the database in the configuration file [/etc/keystone/keystone.conf](keystone.conf)  
>      `[database]`   
>      `# The SQLAlchemy connection string used to connect to the database`  
>      `connection = mysql://keystone:openstack@controller/keystone`  
>
> + Delete the keystone.db file created in the /var/lib/keystone/ directory so that it does not get used by mistake:  
>
>      `sudo rm /var/lib/keystone/keystone.db`
>
> + Use the password that you set previously to log in as root. Create a keystone database user:  
>      `mysql -u root -p`  
>      `CREATE DATABASE keystone;`  
>      `GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' IDENTIFIED BY 'openstack';`  
>      `GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' IDENTIFIED BY 'openstack';`  
>      `exit`  
>
> + Create the database tables for the Identity Service:  
>      `sudo keystone-manage db_sync`  
>
> + Define an authorization token to use as a shared secret between the Identity Service and other OpenStack services. Use openssl to generate a random token and store it in the configuration file:  
>      `openssl rand -hex 10`  
> Edit [/etc/keystone/keystone.conf](keystone.conf) and change the [DEFAULT] section, replacing ADMIN_TOKEN with the results of the command:  
>      `[DEFAULT]`  
>      `# A "shared secret" between keystone and other openstack services`  
>      `admin_token = ADMIN_TOKEN`  
