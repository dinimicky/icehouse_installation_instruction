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

> + install keystone: `sudo apt-get install keystone`  
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
> + Configure the log directory. Edit the [/etc/keystone/keystone.conf](keystone.conf) file and update the [DEFAULT] section:  
>      `[DEFAULT]`  
>      `...`  
>      `log_dir = /var/log/keystone`  
> + Restart the Identity Service: `sudo service keystone restart`  
> + By default, the Identity Service stores expired tokens in the database indefinitely. While potentially useful for auditing in production environments, the accumulation of expired tokens will considerably increase database size and may decrease service performance, particularly in test environments with limited resources. We recommend configuring a periodic task using cron to purge expired tokens hourly.  
>    + Run the following command to purge expired tokens every hour and log the output to /var/log/keystone/keystone-tokenflush.log:  
>    `sudo sh -c  "(crontab -l -u keystone 2>&1 | grep -q token_flush) || echo '@hourly /usr/bin/keystone-manage token_flush >/var/log/keystone/keystone-tokenflush.log 2>&1' >> /var/spool/cron/crontabs/keystone"`  
>

### Define users, tenants and roles
> + set the OS_SERVICE_TOKEN & OS_SERVICE_ENDPOINT environment variable.  
>      `export OS_SERVICE_TOKEN=ADMIN_TOKEN`  
>      `export OS_SERVICE_ENDPOINT=http://controller:35357/v2.0`  
> + create administrative user & normal user & service tenant:  
>      `keystone user-create --name=admin --pass=openstack --email=admin@example.com`  
>      `keystone role-create --name=admin`  
>      `keystone tenant-create --name=admin --description="Admin Tenant"`  
>      `keystone user-role-add --user=admin --tenant=admin --role=admin`  
>      `keystone user-role-add --user=admin --role=_member_ --tenant=admin`  
>      `keystone user-create --name=demo --pass=demo --email=demo@example.com`  
>      `keystone tenant-create --name=demo --description="demo Tenant"`  
>      `keystone user-role-add --user=demo --role=_member_ --tenant=demo`  
>      `keystone tenant-create --name=service --description="Service Tenant"`
>

### Define services and API endpoints
> + keystone service-create. Describes the service.  
> + keystone endpoint-create. Associates API endpoints with the service.  
>      `keystone service-create --name=keystone --type=identity \`  
>      `--description="OpenStack Identity"`  
>      `keystone endpoint-create \`  
>      `--service-id=$(keystone service-list | awk '/ identity / {print $2}') \`  
>      `--publicurl=http://controller:5000/v2.0 \`  
>      `--internalurl=http://controller:5000/v2.0 \`  
>      `--adminurl=http://controller:35357/v2.0`  
>  

### Create openrc.sh files
> + admin-openrc.sh for the administrative user:  
>      `export OS_USERNAME=admin`  
>      `export OS_PASSWORD=openstack`  
>      `export OS_TENANT_NAME=admin`  
>      `export OS_AUTH_URL=http://controller:35357/v2.0`  
>      `export PS1='[\u@\h \W(admin)]\$ '`  
> + demo-openrc.sh for the normal user:  
>      `export OS_USERNAME=demo`  
>      `export OS_PASSWORD=demo`  
>      `export OS_TENANT_NAME=demo`  
>      `export OS_AUTH_URL=http://controller:35357/v2.0`  
>      `export PS1='[\u@\h \W(demo)]\$ '`  
>
>

### Image Service Configuration
> + Install the Image Service: `sudo apt-get install glance python-glanceclient`  
> + configure database connection & message broker in [/etc/glance/glance-api.conf](glance-api.conf)  
> + configure database connection in [/etc/glance/glance-registry.conf](glance-registry.conf)  
> + Delete the glance.sqlite file created in the /var/lib/glance/ directory if it exists: `sudo rm /var/lib/glance/glance.sqlite`  
> + create a glance database user:  
>      `mysql -u root -p`  
>      `CREATE DATABASE glance;`  
>      `GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' IDENTIFIED BY 'openstack';`  
>      `GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' IDENTIFIED BY 'openstack';`  
>      `exit`  
> + Create the database tables for the Image Service: `sudo glance-manage db_sync`  
> + Create a glance user that the Image Service can use to authenticate with the Identity service.  
>      `keystone user-create --name=glance --pass=openstack --email=glance@example.com`  
>      `keystone user-role-add --user=glance --tenant=service --role=admin`  
> + Configure the Image Service to use the Identity Service for authentication in [/etc/glance/glance-api.conf](glance-api.conf) & [/etc/glance/glance-registry.conf](glance-registry.conf)  
> + Register the Image Service with the Identity service so that other OpenStack services can locate it.  
>      `keystone service-create --name=glance --type=image --description="OpenStack Image Service"`  
>      `keystone endpoint-create \`  
>      `--service-id=$(keystone service-list | awk '/ image / {print $2}') \`  
>      `--publicurl=http://controller:9292 \`  
>      `--internalurl=http://controller:9292 \`  
>      `--adminurl=http://controller:9292`  
>
> + Restart the glance service with its new settings:  
>      `sudo service glance-registry restart`  
>      `sudo service glance-api restart`  
>  

### Install Compute controller services
> + Install the Compute packages necessary for the controller node.  
>      `sudo apt-get install nova-api nova-cert nova-conductor nova-consoleauth \`  
>      `nova-novncproxy nova-scheduler python-novaclient`  
> + Configure the [/etc/nova/nova.conf](nova.conf)  
> + Delete the nova.sqlite file created in the /var/lib/nova/ directory  
> + Use the password you created previously to log in as root. Create a nova database user:  
>      `mysql -u root -p`  
>      `CREATE DATABASE nova;`  
>      `GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'localhost' \`  
>      `IDENTIFIED BY 'openstack';`  
>      `GRANT ALL PRIVILEGES ON nova.* TO 'nova'@'%' \`  
>      `IDENTIFIED BY 'openstack';`  
>      `exit`  
> + Create the Compute service tables:  
>      `sudo nova-manage db sync`  
> + Create a nova user that Compute uses to authenticate with the Identity Service. Use the service tenant and give the user the admin role:  
>      `keystone user-create --name=nova --pass=openstack --email=nova@example.com`  
>      `keystone user-role-add --user=nova --tenant=service --role=admin`  
> + Configure Compute to use these credentials with the Identity Service running on the controller in [/etc/nova/nova.conf](nova.conf).  
> + Register the service and specify the endpoint:  
>      `keystone service-create --name=nova --type=compute \`  
>      `--description="OpenStack Compute"`  
>      `keystone endpoint-create \`  
>      `--service-id=$(keystone service-list | awk '/ compute / {print $2}') \`  
>      `--publicurl=http://controller:8774/v2/%\(tenant_id\)s \`  
>      `--internalurl=http://controller:8774/v2/%\(tenant_id\)s \`  
>      `--adminurl=http://controller:8774/v2/%\(tenant_id\)s`  
> + Restart Compute services:  
>      `sudo service nova-api restart`   
>      `sudo service nova-cert restart`  
>      `sudo service nova-consoleauth restart`  
>      `sudo service nova-scheduler restart`  
>      `sudo service nova-conductor restart`  
>      `sudo service nova-novncproxy restart`  
>

### Install Network controller services
> + create the neutron database:  
>     `mysql -u root -p`  
>     `CREATE DATABASE neutron;`  
>     `GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'localhost' \`  
>     `IDENTIFIED BY 'openstack';`  
>     `GRANT ALL PRIVILEGES ON neutron.* TO 'neutron'@'%' \`  
>     `IDENTIFIED BY 'openstack';`  
>     `exit`  
> + Create Identity service credentials for Networking:  
>     `keystone user-create --name neutron --pass openstack --email neutron@example.com`  
>     `keystone user-role-add --user neutron --tenant service --role admin`  
>     `keystone service-create --name neutron --type network --description "OpenStack Networking"`  
>     `keystone endpoint-create \`  
>     `--service-id $(keystone service-list | awk '/ network / {print $2}') \`  
>     `--publicurl http://controller:9696 \`  
>     `--adminurl http://controller:9696 \`  
>     `--internalurl http://controller:9696`   
>   
> + To install the Networking components:  
>     `sudo apt-get install neutron-server neutron-plugin-ml2`  
> + To configure the Networking server component in [/etc/neutron/neutron.conf](neutron.conf)  
> + To configure the Modular Layer 2 (ML2) plug-in in [/etc/neutron/plugins/ml2/ml2_conf.ini](ml2_conf.ini)  
> + To configure Compute to use Networking in [/etc/nova/nova.conf](nova.conf)  
> + Restart the Compute & Networking services:  
>     `sudo service nova-api restart`  
>     `sudo service nova-scheduler restart`  
>     `sudo service nova-conductor restart`  
>     `sudo service neutron-server restart`  
>  

### Add the dashboard
> + Install the dashboard on the node that can contact the Identity Service as root:  
>     `sudo apt-get install apache2 memcached libapache2-mod-wsgi openstack-dashboard`  
> + Remove the openstack-dashboard-ubuntu-theme package. This theme prevents translations, several menus as well as the network map from rendering correctly:  
>     `sudo apt-get remove --purge openstack-dashboard-ubuntu-theme`  
> + Modify the value of CACHES['default']['LOCATION'] in [/etc/openstack-dashboard/local_settings.py](local_settings.py) to match the ones set in /etc/memcached.conf.  
> + Start the Apache web server and memcached:  
>     `sudo service apache2 restart`  
>     `sudo service memcached restart`  
> + Initialize and configure the database  
>     `mysql -u root -p`  
>     `CREATE DATABASE dash;`  
>     `GRANT ALL PRIVILEGES ON dash.* TO 'dash'@'%' IDENTIFIED BY 'openstack';`  
>     `GRANT ALL PRIVILEGES ON dash.* TO 'dash'@'localhost' IDENTIFIED BY 'openstack';`   
>     `exit`
> + add SESSION_ENGINE & DATABASES in [/etc/openstack-dashboard/local_settings.py](local_settings.py)  
> + After configuring the local_settings as shown, you can run the manage.py syncdb command to populate this newly created database.   
>     `sudo /usr/share/openstack-dashboard/manage.py syncdb`  

>>    Note: it will ask you to create a superusers.    
     You just installed Django's auth system, which means you don't have any superusers defined.  
Would you like to create one now? (yes/no): yes  
Username (leave blank to use 'root'):  
Email address: root@exmaple.com  
Password:openstack  
Password (again): openstack  
Superuser created successfully.  
Installing custom SQL ...  
Installing indexes ...  
Installed 0 object(s) from 0 fixture(s)  

> + On Ubuntu: If you want to avoid a warning when you restart apache2, create a blackhole directory in the dashboard directory, as follows:  
>     `sudo mkdir -p /var/lib/dash/.blackhole`  
> + Restart Apache to pick up the default site and symbolic link settings:  
>     `sudo service apache2 restart`  
> + On Ubuntu, restart the nova-api service to ensure that the API server can connect to the dashboard without error:  
>     `sudo service nova-api restart`  

### Configure a Block Storage service  

> + Install the appropriate packages for the Block Storage service:  
>     `sudo apt-get install cinder-api cinder-scheduler`  
> + Configure Block Storage to use your database in [/etc/cinder/cinder.conf](cinder.conf)  
> + create a cinder database:  
>     `mysql -u root -p`  
>     `CREATE DATABASE cinder;`  
>     `GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'localhost' IDENTIFIED BY 'openstack';`  
>     `GRANT ALL PRIVILEGES ON cinder.* TO 'cinder'@'%' IDENTIFIED BY 'openstack';`  
>     `exit`  
> + Create the database tables for the Block Storage service:  
>      `sudo cinder-manage db sync`  
> + Create a cinder user:  
>      `keystone user-create --name=cinder --pass=openstack --email=cinder@example.com`  
>      `keystone user-role-add --user=cinder --tenant=service --role=admin`  
> + add keystone_token section in [/etc/cinder/cinder.conf](cinder.conf)  
> + configure the RabbitMQ message broker in [/etc/cinder/cinder.conf](cinder.conf)  
> + Register the Block Storage service with the Identity service so that other OpenStack services can locate it:
>      `keystone service-create --name=cinder --type=volume --description="OpenStack Block Storage"`  
>      `keystone endpoint-create \`  
>      `--service-id=$(keystone service-list | awk '/ volume / {print $2}') \`  
>      `--publicurl=http://controller:8776/v1/%\(tenant_id\)s \`  
>      `--internalurl=http://controller:8776/v1/%\(tenant_id\)s \`  
>      `--adminurl=http://controller:8776/v1/%\(tenant_id\)s`  
> + Register a service and endpoint for version 2 of the Block Storage service API:  
>      `keystone service-create --name=cinderv2 --type=volumev2 --description="OpenStack Block Storage v2"`  
>      `keystone endpoint-create \`  
>      `--service-id=$(keystone service-list | awk '/ volumev2 / {print $2}') \`  
>      `--publicurl=http://controller:8776/v2/%\(tenant_id\)s \`  
>      `--internalurl=http://controller:8776/v2/%\(tenant_id\)s \`  
>      `--adminurl=http://controller:8776/v2/%\(tenant_id\)s`  
> + Restart the Block Storage services with the new settings:  
>      `sudo service cinder-scheduler restart`  
>      `sudo service cinder-api restart`  

### Configure an Object Storage service  

> + Create a swift user that the Object Storage Service can use to authenticate with the Identity Service.  
>      `keystone user-create --name=swift --pass=openstack --email=swift@example.com`  
>      `keystone user-role-add --user=swift --tenant=service --role=admin`  
> + Create a service entry for the Object Storage Service:  
>      `keystone service-create --name=swift --type=object-store --description="OpenStack Object Storage"`  
> + Specify an API endpoint for the Object Storage Service by using the returned service ID.  
>      `keystone endpoint-create \`  
>      `--service-id=$(keystone service-list | awk '/ object-store / {print $2}') \`  
>      `--publicurl='http://controller:8080/v1/AUTH_%(tenant_id)s' \`  
>      `--internalurl='http://controller:8080/v1/AUTH_%(tenant_id)s' \`  
>      `--adminurl=http://controller:8080`  

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

### Configure an swift proxy service
> + Install swift-proxy service:  
>      `sudo apt-get install swift swift-proxy python-keystoneclient python-swiftclient python-webob`  
> + Modify memcached to listen on the default interface on a local, non-public network. Edit this line in the /etc/memcached.conf file:  
>      `-l 10.0.0.11`  
> + Restart the memcached service:  
>      `sudo service memcached restart`  
> + Create [/etc/swift/proxy-server.conf](proxy-server.conf)  
> + Create the account, container, and object rings.   
>      `cd /etc/swift`  
>      `sudo swift-ring-builder account.builder create 18 3 1`  
>      `sudo swift-ring-builder container.builder create 18 3 1`  
>      `sudo swift-ring-builder object.builder create 18 3 1`   
> + For every storage device on each node add entries to each ring:  
>      `sudo swift-ring-builder account.builder add r1z1-10.0.0.11:6002/sdb1 100`  
>      `sudo swift-ring-builder container.builder add r1z1-10.0.0.11:6001/sdb1 100`  
>      `sudo swift-ring-builder object.builder add r1z1-10.0.0.11:6000/sdb1 100`  

>      `sudo swift-ring-builder account.builder add r1z1-10.0.0.21:6002/sdb1 100`  
>      `sudo swift-ring-builder container.builder add r1z1-10.0.0.21:6001/sdb1 100`  
>      `sudo swift-ring-builder object.builder add r1z1-10.0.0.21:6000/sdb1 100`  

>      `sudo swift-ring-builder account.builder add r1z1-10.0.0.31:6002/sdb1 100`  
>      `sudo swift-ring-builder container.builder add r1z1-10.0.0.31:6001/sdb1 100`  
>      `sudo swift-ring-builder object.builder add r1z1-10.0.0.31:6000/sdb1 100`  

>      `sudo swift-ring-builder account.builder add r1z1-10.0.0.41:6002/sdb1 100`  
>      `sudo swift-ring-builder container.builder add r1z1-10.0.0.41:6001/sdb1 100`  
>      `sudo swift-ring-builder object.builder add r1z1-10.0.0.41:6000/sdb1 100`  

> + Rebalance the rings:  
>      `sudo swift-ring-builder account.builder rebalance`  
>      `sudo swift-ring-builder container.builder rebalance`  
>      `sudo swift-ring-builder object.builder rebalance`  

> + Copy the account.ring.gz, container.ring.gz, and object.ring.gz files to each of the Proxy and Storage nodes in /etc/swift.  

> + Make sure the swift user owns all configuration files:  
>       `sudo chown -R swift:swift /etc/swift`  

> + Restart the Proxy service:  
>       `sudo service swift-proxy restart`  
> + To start all swift services at once, run the command:  
>       `sudo swift-init all start`  
>       `sudo service rsync restart`  

