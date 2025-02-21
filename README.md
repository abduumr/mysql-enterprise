# mysql-enterprise

### referensi : https://deepakmysqldba.wordpress.com/2020/08/19/mysql-innodb-cluster-setup-step-by-step/

```
192.168.50.127 node1
192.168.50.132 node2
192.168.50.136 node3
192.168.50.127 mysql-router-1
192.168.50.114 mysql-router-2
192.168.50.100 keepalived

```

# Setting host (all node)
```
[root@node01 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

192.168.50.127 node1
192.168.50.132 node2
192.168.50.136 node3
```

# (all node)
```
[root@node01 mysql]# sudo yum install -y ncurses-compat-libs
```
# setting directory (all node)
```
[root@node01 mysql]# sudo mkdir -p /mysql/etc
[root@node01 mysql]# sudo mkdir -p /mysql/data
[root@node01 mysql]# sudo mkdir -p /mysql/installer
[root@node01 mysql]# sudo mkdir -p /mysql/data 
[root@node01 mysql]# sudo mkdir -p /mysql/mysql-latest/bin/
```
# setting parameter OS (all node)
```
[root@node01 mysql]# cd
[root@node01 ~]# vi .bashrc
[root@node01 ~]# cat .bashrc
# .bashrc

# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'

# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi

##############################################
export PATH=$PATH:/mysql/mysql-latest/bin    
export MYSQL_PS1="\\u on \\h>\\_"            
##############################################

[root@node01 ~]#
```
# installasi mysql-commercial (all node)
```
[root@node01 installer]# cd /mysql/installer
[root@node01 mysql-commercial]# unzip p37497652_840_Linux-x86-64.zip
[root@node01 mysql-commercial]# tar xvf mysql-commercial-8.4.4-1.1.el8.x86_64.repo.tar.gz
[root@node01 mysql-commercial]# sudo dnf install ./mysql-commercial-*.rpm
[root@node01 mysql-commercial]# cd mysql-8.4/8.4.4/
[root@node01 8.4.4]# sudo dnf install ./mysql-commercial-*.rpm -y
[root@node01 8.4.4]# systemctl enable mysqld
[root@node01 8.4.4]# systemctl start mysqld
[root@node01 8.4.4]# systemctl status mysqld
```
# setting mysql-commercial mysql-commercial (all node)

```
[root@node01 8.4.4]# cp /usr/bin/mysq* /mysql/mysql-latest/bin/
[root@node01 8.4.4]#

```
# setting my.cnf (all node)

```
[root@node01 8.4.4]# vi /etc/my.cnf
[root@node01 8.4.4]# cat /etc/my.cnf

#datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

#log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

#####################################
datadir=/mysql/data                 
log-error=/mysql/log/mysqld.log     
bind-address = 0.0.0.0              
report_host = 192.168.50.127   #perhatikan ip servernya    
#####################################

```
# setting directory mysql-commercial (all node)

```
[root@node01 8.4.4]# sudo yum install -y rsync
[root@node01 8.4.4]# sudo rsync -av /var/lib/mysql/ /mysql/data/
[root@node01 8.4.4]# cp /var/log/mysqld.log /mysql/log/

[root@node01 8.4.4]# sudo chown -R mysql:mysql /mysql /var/lib/mysql
[root@node01 8.4.4]# sudo chmod -R 750 /mysql /var/lib/mysql

[root@node01 8.4.4]# sudo setenforce 0
[root@node01 8.4.4]# sudo systemctl restart mysqld
[root@node01 8.4.4]# sudo systemctl status mysqld
```
# setting mysql-commercial (all node)

```
[root@node01 8.4.4]# grep 'temporary password' /mysql/log/mysqld.log
2025-02-12T06:10:04.134055Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: FidhFtbwJ4.y
[root@node01 8.4.4]# /mysql/mysql-latest/bin/mysql_secure_installation -h127.0.0.1 -P3306

Securing the MySQL server deployment.

Enter password for user root:

The existing password for the user account root has expired. Please set a new password.

New password:

Re-enter new password:
The 'validate_password' component is installed on the server.
The subsequent steps will run with the existing configuration
of the component.
Using existing password for root.

Estimated strength of the password: 100
Change the password for root ? ((Press y|Y for Yes, any other key for No) : n

 ... skipping.
By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
[root@node01 8.4.4]#
```
# setting user mysql (all node)

```
[root@node01 8.4.4]# mysql -uroot -pWelcome1! -h 127.0.0.1 -P3306
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 12
Server version: 8.4.4-commercial MySQL Enterprise Server - Commercial

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql>

mysql> CREATE USER 'admin'@'%' IDENTIFIED BY 'Welcome1!';
Query OK, 0 rows affected (0.11 sec)

mysql> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql>


```

# Installasi mysqlsh (all node)

```
[root@node03 8.4.4]# cd /mysql/installer/mysql-shell/
[root@node03 mysql-shell]# sudo rpm -ivh mysql-shell-commercial-8.4.4-1.1.el8.x86_64.rpm

```
# Configurasi mysqlsh (node-01)

```
[root@node01 ~]# mysqlsh
MySQL Shell 8.4.1-commercial

Copyright (c) 2016, 2024, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its affiliates.
Other names may be trademarks of their respective owners.

Type '\help' or '\?' for help; '\quit' to exit.
 MySQL  SQL > \js
Switching to JavaScript mode...
 MySQL  JS > shell.connect('admin@192.168.50.127:3306')
Creating a session to 'admin@192.168.50.127:3306'
Please provide the password for 'admin@192.168.50.127:3306': *********
Save password for 'admin@192.168.50.127:3306'? [Y]es/[N]o/Ne[v]er (default No): y
Fetching schema names for auto-completion... Press ^C to stop.
Your MySQL connection id is 14
Server version: 8.4.4-commercial MySQL Enterprise Server - Commercial
No default schema selected; type \use <schema> to set one.
<ClassicSession:admin@192.168.50.127:3306>
```
# ConfigurasiInstance mysqlsh pada node01 (node-01)

```
 MySQL  192.168.50.127:33060+ ssl  JS > dba.configureInstance('admin@192.168.50.127:3306')
Configuring local MySQL instance listening at port 3306 for use in an InnoDB Cluster...

This instance reports its own address as 192.168.50.127:3306

applierWorkerThreads will be set to the default value of 4.

NOTE: Some configuration options need to be fixed:
+--------------------------+---------------+----------------+--------------------------------------------------+
| Variable                 | Current Value | Required Value | Note                                             |
+--------------------------+---------------+----------------+--------------------------------------------------+
| enforce_gtid_consistency | OFF           | ON             | Update read-only variable and restart the server |
| gtid_mode                | OFF           | ON             | Update read-only variable and restart the server |
| server_id                | 1             | <unique ID>    | Update read-only variable and restart the server |
+--------------------------+---------------+----------------+--------------------------------------------------+

Some variables need to be changed, but cannot be done dynamically on the server.
Do you want to perform the required configuration changes? [y/n]: y
Do you want to restart the instance after configuring it? [y/n]: y
Configuring instance...
The instance '192.168.50.127:3306' was configured to be used in an InnoDB Cluster.
Restarting MySQL...
NOTE: MySQL server at 192.168.50.127:3306 was restarted.
 MySQL  19
```
# Checkinstanceconfigurasi mysqlsh pada node01 (node-01)

```
 MySQL  192.168.50.127:3306 ssl  JS > dba.checkInstanceConfiguration('admin@192.168.50.127:3306')
Validating local MySQL instance listening at port 3306 for use in an InnoDB Cluster...

This instance reports its own address as 192.168.50.127:3306

Checking whether existing tables comply with Group Replication requirements...
No incompatible tables detected

Checking instance configuration...
Instance configuration is compatible with InnoDB cluster

The instance '192.168.50.127:3306' is valid for InnoDB Cluster usage.

{
    "status": "ok"
}
 MySQL  192.168.50.127:3306 ssl  JS > var cluster = dba.createCluster('InnodbPOCcluster')
A new InnoDB Cluster will be created on instance '192.168.50.127:3306'.

Validating instance configuration at 192.168.50.127:3306...

This instance reports its own address as 192.168.50.127:3306

Instance configuration is suitable.
NOTE: Group Replication will communicate with other members using '192.168.50.127:3306'. Use the localAddress option to override.

* Checking connectivity and SSL configuration...

Creating InnoDB Cluster 'InnodbPOCcluster' on '192.168.50.127:3306'...

Adding Seed Instance...
Cluster successfully created. Use Cluster.addInstance() to add MySQL instances.
At least 3 instances are needed for the cluster to be able to withstand up to
one server failure.
```
# ConfigurasiInstance mysqlsh pada node02 (node-01)

```
 MySQL  192.168.50.127:3306 ssl  JS > dba.configureInstance('admin@192.168.50.132:3306')
Please provide the password for 'admin@192.168.50.132:3306': *********
Save password for 'admin@192.168.50.132:3306'? [Y]es/[N]o/Ne[v]er (default No): y
Configuring MySQL instance at 192.168.50.132:3306 for use in an InnoDB Cluster...

This instance reports its own address as 192.168.50.132:3306

applierWorkerThreads will be set to the default value of 4.

NOTE: Some configuration options need to be fixed:
+--------------------------+---------------+----------------+--------------------------------------------------+
| Variable                 | Current Value | Required Value | Note                                             |
+--------------------------+---------------+----------------+--------------------------------------------------+
| enforce_gtid_consistency | OFF           | ON             | Update read-only variable and restart the server |
| gtid_mode                | OFF           | ON             | Update read-only variable and restart the server |
| server_id                | 1             | <unique ID>    | Update read-only variable and restart the server |
+--------------------------+---------------+----------------+--------------------------------------------------+

Some variables need to be changed, but cannot be done dynamically on the server.
Do you want to perform the required configuration changes? [y/n]: y
Do you want to restart the instance after configuring it? [y/n]: y
Configuring instance...
The instance '192.168.50.132:3306' was configured to be used in an InnoDB Cluster.
Restarting MySQL...
NOTE: MySQL server at 192.168.50.132:3306 was restarted.

```
# ConfigurasiInstance mysqlsh pada node03 (node-01)

```
 MySQL  192.168.50.127:3306 ssl  JS > dba.configureInstance('admin@192.168.50.136:3306')
Please provide the password for 'admin@192.168.50.136:3306': *********
Save password for 'admin@192.168.50.136:3306'? [Y]es/[N]o/Ne[v]er (default No): y
Configuring MySQL instance at 192.168.50.136:3306 for use in an InnoDB Cluster...

This instance reports its own address as 192.168.50.136:3306

applierWorkerThreads will be set to the default value of 4.

NOTE: Some configuration options need to be fixed:
+--------------------------+---------------+----------------+--------------------------------------------------+
| Variable                 | Current Value | Required Value | Note                                             |
+--------------------------+---------------+----------------+--------------------------------------------------+
| enforce_gtid_consistency | OFF           | ON             | Update read-only variable and restart the server |
| gtid_mode                | OFF           | ON             | Update read-only variable and restart the server |
| server_id                | 1             | <unique ID>    | Update read-only variable and restart the server |
+--------------------------+---------------+----------------+--------------------------------------------------+

Some variables need to be changed, but cannot be done dynamically on the server.
Do you want to perform the required configuration changes? [y/n]: y
Do you want to restart the instance after configuring it? [y/n]: y
Configuring instance...
The instance '192.168.50.136:3306' was configured to be used in an InnoDB Cluster.
Restarting MySQL...
NOTE: MySQL server at 192.168.50.136:3306 was restarted.

```
# addInstance mysqlsh pada node02 (node-01)

```
 MySQL  192.168.50.127:3306 ssl  JS > cluster.addInstance('admin@192.168.50.132:3306')


NOTE: The target instance '192.168.50.132:3306' has not been pre-provisioned (GTID set is empty). The Shell is unable to decide whether incremental state recovery can correctly provision it.
The safest and most convenient way to provision a new instance is through automatic clone provisioning, which will completely overwrite the state of '192.168.50.132:3306' with a physical snapshot from an existing cluster member. To use this method by default, set the 'recoveryMethod' option to 'clone'.

The incremental state recovery may be safely used if you are sure all updates ever executed in the cluster were done with GTIDs enabled, there are no purged transactions and the new instance contains the same GTID set as the cluster or a subset of it. To use this method by default, set the 'recoveryMethod' option to 'incremental'.


Please select a recovery method [C]lone/[I]ncremental recovery/[A]bort (default Clone):
Validating instance configuration at 192.168.50.132:3306...

This instance reports its own address as 192.168.50.132:3306

Instance configuration is suitable.
NOTE: Group Replication will communicate with other members using '192.168.50.132:3306'. Use the localAddress option to override.

* Checking connectivity and SSL configuration...

A new instance will be added to the InnoDB Cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Adding instance to the cluster...

Monitoring recovery process of the new cluster member. Press ^C to stop monitoring and let it continue in background.
Clone based state recovery is now in progress.

NOTE: A server restart is expected to happen as part of the clone process. If the
server does not support the RESTART command or does not come back after a
while, you may need to manually start it back.

* Waiting for clone to finish...
NOTE: 192.168.50.132:3306 is being cloned from 192.168.50.136:3306
** Stage DROP DATA: Completed
** Clone Transfer
    FILE COPY  ############################################################  100%  Completed
    PAGE COPY  ############################################################  100%  Completed
    REDO COPY  ############################################################  100%  Completed

NOTE: 192.168.50.132:3306 is shutting down...

* Waiting for server restart... ready
* 192.168.50.132:3306 has restarted, waiting for clone to finish...
** Stage RESTART: Completed
* Clone process has finished: 74.70 MB transferred in 41 sec (1.82 MB/s)

State recovery already finished for '192.168.50.132:3306'

The instance '192.168.50.132:3306' was successfully added to the cluster.


```
# addInstance mysqlsh pada node03 (node-01)

```
 MySQL  192.168.50.127:3306 ssl  JS > cluster.addInstance('admin@192.168.50.136:3306')

NOTE: The target instance '192.168.50.136:3306' has not been pre-provisioned (GTID set is empty). The Shell is unable to decide whether incremental state recovery can correctly provision it.
The safest and most convenient way to provision a new instance is through automatic clone provisioning, which will completely overwrite the state of '192.168.50.136:3306' with a physical snapshot from an existing cluster member. To use this method by default, set the 'recoveryMethod' option to 'clone'.

The incremental state recovery may be safely used if you are sure all updates ever executed in the cluster were done with GTIDs enabled, there are no purged transactions and the new instance contains the same GTID set as the cluster or a subset of it. To use this method by default, set the 'recoveryMethod' option to 'incremental'.


Please select a recovery method [C]lone/[I]ncremental recovery/[A]bort (default Clone):
Validating instance configuration at 192.168.50.136:3306...

This instance reports its own address as 192.168.50.136:3306

Instance configuration is suitable.
NOTE: Group Replication will communicate with other members using '192.168.50.136:3306'. Use the localAddress option to override.

* Checking connectivity and SSL configuration...

A new instance will be added to the InnoDB Cluster. Depending on the amount of
data on the cluster this might take from a few seconds to several hours.

Adding instance to the cluster...

Monitoring recovery process of the new cluster member. Press ^C to stop monitoring and let it continue in background.
Clone based state recovery is now in progress.

NOTE: A server restart is expected to happen as part of the clone process. If the
server does not support the RESTART command or does not come back after a
while, you may need to manually start it back.

* Waiting for clone to finish...
NOTE: 192.168.50.136:3306 is being cloned from 192.168.50.127:3306
** Stage DROP DATA: Completed
** Clone Transfer
    FILE COPY  ############################################################  100%  Completed
    PAGE COPY  ############################################################  100%  Completed
    REDO COPY  ############################################################  100%  Completed

NOTE: 192.168.50.136:3306 is shutting down...

* Waiting for server restart... ready
* 192.168.50.136:3306 has restarted, waiting for clone to finish...
** Stage RESTART: Completed
* Clone process has finished: 74.70 MB transferred in about 1 second (~74.70 MB/s)

State recovery already finished for '192.168.50.136:3306'

The instance '192.168.50.136:3306' was successfully added to the cluster.

```
# Periksa cluster.status (node-01)

```
 MySQL  192.168.50.127:3306 ssl  JS > cluster.status()
{
    "clusterName": "InnodbPOCcluster",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.50.127:3306",
        "ssl": "REQUIRED",
        "status": "OK",
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.",
        "topology": {
            "192.168.50.127:3306": {
                "address": "192.168.50.127:3306",
                "memberRole": "PRIMARY",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.132:3306": {
                "address": "192.168.50.132:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.136:3306": {
                "address": "192.168.50.136:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            }
        },
        "topologyMode": "Single-Primary"
    },
    "groupInformationSourceMember": "192.168.50.127:3306"
}
 MySQL  192.168.50.127:3306 ssl  JS >

```
# Installasi mysql-router (pada server yang dipasangkan mysql-router)

```
[root@node01 mysql-router]# mkdir -p /mysql/installer/mysql-router/
[root@node01 mysql-router]# cd  /mysql/installer/mysql-router/
[root@node01 mysql-router]# unzip
[root@node01 mysql-router]# chown -R mysql:mysql /mysql/
[root@node01 mysql-router]# sudo rpm -ivh mysql-router-commercial-8.4.4-1.1.el8.x86_64.rpm
[root@node01 mysql-router]# cp /usr/bin/mysqlroute* /mysql/mysql-latest/bin/
[root@node01 bin]# chown -R mysql:mysql /mysql/


```
# Configurasi mysql-router (pada server yang dipasangkan mysql-router)

```
[root@node01 mysql-router]# cd  /mysql/mysql-latest/bin/
[root@node01 bin]# ./mysqlrouter --user=mysql --bootstrap admin@192.168.50.127:3306 --directory /mysql/installer/mysql-router/bin/ --conf-use-sockets
Please enter MySQL password for admin:
# Bootstrapping MySQL Router 8.4.4 (MySQL Enterprise - Commercial) instance at '/mysql/installer/mysql-router/bin'...

- Creating account(s) (only those that are needed, if any)
- Verifying account (using it to run SQL queries that would be run by Router)
- Storing account in keyring
- Adjusting permissions of generated files
- Creating configuration /mysql/installer/mysql-router/bin/mysqlrouter.conf

# MySQL Router configured for the InnoDB Cluster 'InnodbPOCcluster'

After this MySQL Router has been started with the generated configuration

    $ ./mysqlrouter -c /mysql/installer/mysql-router/bin/mysqlrouter.conf

InnoDB Cluster 'InnodbPOCcluster' can be reached by connecting to:

## MySQL Classic protocol

- Read/Write Connections: localhost:6446, /mysql/installer/mysql-router/bin/mysql.sock
- Read/Only Connections:  localhost:6447, /mysql/installer/mysql-router/bin/mysqlro.sock
- Read/Write Split Connections: localhost:6450, /mysql/installer/mysql-router/bin/mysqlsplit.sock

## MySQL X protocol

- Read/Write Connections: localhost:6448, /mysql/installer/mysql-router/bin/mysqlx.sock
- Read/Only Connections:  localhost:6449, /mysql/installer/mysql-router/bin/mysqlxro.sock

[root@node01 bin]#


```
# Start mysql-router (pada server yang dipasangkan mysql-router)

```
[root@node01 bin]cd /mysql/installer/mysql-router/bin/
[root@node01 bin]# ./start.sh
[root@node01 bin]# PID 28505 written to '/mysql/installer/mysql-router/bin/mysqlrouter.pid'
stopping to log to the console. Continuing to log to filelog

[root@node01 bin]# ps aux | grep mysqlrouter
root       28503  0.0  0.0 112732  7548 pts/0    S    11:21   0:00 sudo ROUTER_PID=/mysql/installer/mysql-router/bin/mysqlrouter.pid /mysql/mysql-latest/bin/mysqlrouter -c /mysql/installer/mysql-router/bin/mysqlrouter.conf --user=mysql
mysql      28505  5.6  0.2 1149884 23044 pts/0   Sl   11:21   0:05 /mysql/mysql-latest/bin/mysqlrouter -c /mysql/installer/mysql-router/bin/mysqlrouter.conf --user=mysql
root       28527  0.0  0.0  12144  1160 pts/0    S+   11:22   0:00 grep --color=auto mysqlrouter
[root@node01 bin]#

```
### Edit systemd mysql-router (pada server yang dipasangkan mysql-router)

```
[root@node01 bin]# mv /usr/lib/systemd/system/mysqlrouter.service /usr/lib/systemd/system/mysqlrouter.service.bkp
[root@node01 bin]# vi /usr/lib/systemd/system/mysqlrouter.service
[root@node01 bin]# cat /usr/lib/systemd/system/mysqlrouter.service
[Unit]
Description=MySQL Router
After=network-online.target
Wants=network-online.target
After=syslog.target

[Service]
Type=forking
User=mysql
Group=mysql

ExecStart=/mysql/installer/mysql-router/bin/start.sh
ExecStop=/mysql/installer/mysql-router/bin/stop.sh
Restart=always

[Install]
WantedBy=multi-user.target

[root@node01 bin]# sudo systemctl daemon-reload
[root@node01 bin]# sudo systemctl restart mysqlrouter
[root@node01 bin]# sudo systemctl status mysqlrouter

```
# Periksa cluster.status (node-01)

```
MySQL  192.168.50.127:3306 ssl  JS > var cluster = dba.getCluster()
 MySQL  192.168.50.127:3306 ssl  JS > cluster.status()
{
    "clusterName": "InnodbPOCcluster",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.50.127:3306",
        "ssl": "REQUIRED",
        "status": "OK",
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.",
        "topology": {
            "192.168.50.127:3306": {
                "address": "192.168.50.127:3306",
                "memberRole": "PRIMARY",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.132:3306": {
                "address": "192.168.50.132:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.136:3306": {
                "address": "192.168.50.136:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            }
        },
        "topologyMode": "Single-Primary"
    },
    "groupInformationSourceMember": "192.168.50.127:3306"
}
 MySQL  192.168.50.127:3306 ssl  JS >

```
# Periksa replicasi (bebas)

```
[root@node01 bin]# mysql -uadmin -pWelcome1! -h 127.0.0.1 -P6446
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 0
Server version: 8.4.4-router MySQL Enterprise Server - Commercial

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

admin on 127.0.0.1> SELECT * FROM performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 446de9a9-eab5-11ef-bb0d-000c29c4284f | 192.168.50.127 |        3306 | ONLINE       | PRIMARY     | 8.4.4          | MySQL                      |
| group_replication_applier | afed1001-eab6-11ef-982c-000c2956de7c | 192.168.50.132 |        3306 | ONLINE       | SECONDARY   | 8.4.4          | MySQL                      |
| group_replication_applier | dcae9326-eab7-11ef-8c2b-000c2900b1cf | 192.168.50.136 |        3306 | ONLINE       | SECONDARY   | 8.4.4          | MySQL                      |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.01 sec)

admin on 127.0.0.1> \q
Bye

```
# test-failover (node-01)
```
[root@node01 bin]# systemctl stop mysqld

```

# verifikasi replikasi (bebas selain node01)
```
[root@node02 mysql-router]# mysql -uadmin -pWelcome1! -h 192.168.50.127 -P6446
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 0
Server version: 8.4.4-router MySQL Enterprise Server - Commercial

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

admin on 192.168.50.127> SELECT * FROM performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+-----------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | M_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+-----------------------+
| group_replication_applier | afed1001-eab6-11ef-982c-000c2956de7c | 192.168.50.132 |        3306 | ONLINE       | PRIMARY     | 8                     |
| group_replication_applier | dcae9326-eab7-11ef-8c2b-000c2900b1cf | 192.168.50.136 |        3306 | ONLINE       | SECONDARY   | 8                     |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+-----------------------+
2 rows in set (0.00 sec)


```
# test-failover (node-01)
```
[root@node01 bin]# systemctl start mysqld

admin on 192.168.50.127> SELECT * FROM performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 446de9a9-eab5-11ef-bb0d-000c29c4284f | 192.168.50.127 |        3306 | ONLINE       | SECONDARY   | 8.4.4          | MySQL                      |
| group_replication_applier | afed1001-eab6-11ef-982c-000c2956de7c | 192.168.50.132 |        3306 | ONLINE       | PRIMARY     | 8.4.4          | MySQL                      |
| group_replication_applier | dcae9326-eab7-11ef-8c2b-000c2900b1cf | 192.168.50.136 |        3306 | ONLINE       | SECONDARY   | 8.4.4          | MySQL                      |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)

```
# membalikan node01 menjadi primary (bebass)
```
[root@node02 mysql-router]# mysqlsh
MySQL Shell 8.4.1-commercial

Copyright (c) 2016, 2024, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its affiliates.
Other names may be trademarks of their respective owners.

Type '\help' or '\?' for help; '\quit' to exit.
 MySQL  SQL > \js
Switching to JavaScript mode...
 MySQL  JS > shell.connect('admin@192.168.50.127:3306')
Creating a session to 'admin@192.168.50.127:3306'
Please provide the password for 'admin@192.168.50.127:3306': *********
Save password for 'admin@192.168.50.127:3306'? [Y]es/[N]o/Ne[v]er (default No): y
Fetching schema names for auto-completion... Press ^C to stop.
Your MySQL connection id is 36
Server version: 8.4.4-commercial MySQL Enterprise Server - Commercial
No default schema selected; type \use <schema> to set one.
<ClassicSession:admin@192.168.50.127:3306>
 MySQL  192.168.50.127:3306 ssl  JS > var cluster = dba.getCluster();
 MySQL  192.168.50.127:3306 ssl  JS > cluster.setPrimaryInstance('192.168.50.127:3306');
Setting instance '192.168.50.127:3306' as the primary instance of cluster 'InnodbPOCcluster'...

Instance '192.168.50.136:3306' remains SECONDARY.
Instance '192.168.50.127:3306' was switched from SECONDARY to PRIMARY.
Instance '192.168.50.132:3306' was switched from PRIMARY to SECONDARY.

The instance '192.168.50.127:3306' was successfully elected as primary.
 MySQL  192.168.50.127:3306 ssl  JS >


```
# periksa replikasi (bebas)
```
admin on 192.168.50.127> SELECT * FROM performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 446de9a9-eab5-11ef-bb0d-000c29c4284f | 192.168.50.127 |        3306 | ONLINE       | PRIMARY     | 8.4.4          | MySQL                      |
| group_replication_applier | afed1001-eab6-11ef-982c-000c2956de7c | 192.168.50.132 |        3306 | ONLINE       | SECONDARY   | 8.4.4          | MySQL                      |
| group_replication_applier | dcae9326-eab7-11ef-8c2b-000c2900b1cf | 192.168.50.136 |        3306 | ONLINE       | SECONDARY   | 8.4.4          | MySQL                      |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.01 sec)


```
# membuat menjadi master to master (primary)
```
[root@node01 bin]# mysqlsh
MySQL Shell 8.4.1-commercial

Copyright (c) 2016, 2024, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its affiliates.
Other names may be trademarks of their respective owners.

Type '\help' or '\?' for help; '\quit' to exit.
 MySQL  SQL > \js
Switching to JavaScript mode...
 MySQL  JS > shell.connect('admin@192.168.50.127')
Creating a session to 'admin@192.168.50.127'
Fetching schema names for auto-completion... Press ^C to stop.
Your MySQL connection id is 2603 (X protocol)
Server version: 8.4.4-commercial MySQL Enterprise Server - Commercial
No default schema selected; type \use <schema> to set one.
<Session:admin@192.168.50.127:33060>
 MySQL  192.168.50.127:33060+ ssl  JS > var cluster = dba.getCluster()
 MySQL  192.168.50.127:33060+ ssl  JS > cluster.status();
{
    "clusterName": "InnodbPOCcluster",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.50.127:3306",
        "ssl": "REQUIRED",
        "status": "OK",
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.",
        "topology": {
            "192.168.50.127:3306": {
                "address": "192.168.50.127:3306",
                "memberRole": "PRIMARY",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.132:3306": {
                "address": "192.168.50.132:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.136:3306": {
                "address": "192.168.50.136:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            }
        },
        "topologyMode": "Single-Primary"
    },
    "groupInformationSourceMember": "192.168.50.127:3306"
}
 MySQL  192.168.50.127:33060+ ssl  JS > cluster.switchToMultiPrimaryMode();
Switching cluster 'InnodbPOCcluster' to Multi-Primary mode...

Instance '192.168.50.127:3306' remains PRIMARY.
Instance '192.168.50.132:3306' was switched from SECONDARY to PRIMARY.
Instance '192.168.50.136:3306' was switched from SECONDARY to PRIMARY.

The cluster successfully switched to Multi-Primary mode.
 MySQL  192.168.50.127:33060+ ssl  JS > cluster.status();
{
    "clusterName": "InnodbPOCcluster",
    "defaultReplicaSet": {
        "name": "default",
        "ssl": "REQUIRED",
        "status": "OK",
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.",
        "topology": {
            "192.168.50.127:3306": {
                "address": "192.168.50.127:3306",
                "memberRole": "PRIMARY",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.132:3306": {
                "address": "192.168.50.132:3306",
                "memberRole": "PRIMARY",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.136:3306": {
                "address": "192.168.50.136:3306",
                "memberRole": "PRIMARY",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            }
        },
        "topologyMode": "Multi-Primary"
    },
    "groupInformationSourceMember": "192.168.50.127:3306"
}
 MySQL  192.168.50.127:33060+ ssl  JS >


```
# verifikasikan status replika
```
[root@node02 mysql-router]# mysql -uadmin -pWelcome1! -h 192.168.50.127 -P6446
mysql: [Warning] Using a password on the command line interface can be insecure.
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 0
Server version: 8.4.4-router MySQL Enterprise Server - Commercial

Copyright (c) 2000, 2025, Oracle and/or its affiliates.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

admin on 192.168.50.127> SELECT * FROM performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 446de9a9-eab5-11ef-bb0d-000c29c4284f | 192.168.50.127 |        3306 | ONLINE       | PRIMARY     | 8.4.4          | MySQL                      |
| group_replication_applier | afed1001-eab6-11ef-982c-000c2956de7c | 192.168.50.132 |        3306 | ONLINE       | PRIMARY     | 8.4.4          | MySQL                      |
| group_replication_applier | dcae9326-eab7-11ef-8c2b-000c2900b1cf | 192.168.50.136 |        3306 | ONLINE       | PRIMARY     | 8.4.4          | MySQL                      |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)

admin on 192.168.50.127>

```
# mengembalikan menjadi single master (
```
 MySQL  192.168.50.127:33060+ ssl  JS > cluster.switchToSinglePrimaryMode();
Switching cluster 'InnodbPOCcluster' to Single-Primary mode...

Instance '192.168.50.127:3306' remains PRIMARY.
Instance '192.168.50.132:3306' was switched from PRIMARY to SECONDARY.
Instance '192.168.50.136:3306' was switched from PRIMARY to SECONDARY.

WARNING: Existing connections that expected a R/W connection must be disconnected, i.e. instances that became SECONDARY.

The cluster successfully switched to Single-Primary mode.
 MySQL  192.168.50.127:33060+ ssl  JS > cluster.status();
{
    "clusterName": "InnodbPOCcluster",
    "defaultReplicaSet": {
        "name": "default",
        "primary": "192.168.50.127:3306",
        "ssl": "REQUIRED",
        "status": "OK",
        "statusText": "Cluster is ONLINE and can tolerate up to ONE failure.",
        "topology": {
            "192.168.50.127:3306": {
                "address": "192.168.50.127:3306",
                "memberRole": "PRIMARY",
                "mode": "R/W",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.132:3306": {
                "address": "192.168.50.132:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            },
            "192.168.50.136:3306": {
                "address": "192.168.50.136:3306",
                "memberRole": "SECONDARY",
                "mode": "R/O",
                "readReplicas": {},
                "replicationLag": "applier_queue_applied",
                "role": "HA",
                "status": "ONLINE",
                "version": "8.4.4"
            }
        },
        "topologyMode": "Single-Primary"
    },
    "groupInformationSourceMember": "192.168.50.127:3306"
}
 MySQL  192.168.50.127:33060+ ssl  JS >


```
# verifikasikan status replikasi (bebas)
```
admin on 192.168.50.127> SELECT * FROM performance_schema.replication_group_members;
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| CHANNEL_NAME              | MEMBER_ID                            | MEMBER_HOST    | MEMBER_PORT | MEMBER_STATE | MEMBER_ROLE | MEMBER_VERSION | MEMBER_COMMUNICATION_STACK |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
| group_replication_applier | 446de9a9-eab5-11ef-bb0d-000c29c4284f | 192.168.50.127 |        3306 | ONLINE       | PRIMARY     | 8.4.4          | MySQL                      |
| group_replication_applier | afed1001-eab6-11ef-982c-000c2956de7c | 192.168.50.132 |        3306 | ONLINE       | SECONDARY   | 8.4.4          | MySQL                      |
| group_replication_applier | dcae9326-eab7-11ef-8c2b-000c2900b1cf | 192.168.50.136 |        3306 | ONLINE       | SECONDARY   | 8.4.4          | MySQL                      |
+---------------------------+--------------------------------------+----------------+-------------+--------------+-------------+----------------+----------------------------+
3 rows in set (0.00 sec)

```

# menambahkan mysql-router-2


```
[root@localhost bin]cd /mysql/installer/mysql-router/bin/
[root@localhost bin]# ./start.sh
[root@localhost bin]# PID 28505 written to '/mysql/installer/mysql-router/bin/mysqlrouter.pid'
stopping to log to the console. Continuing to log to filelog

[root@localhost bin]# ps aux | grep mysqlrouter
root       28503  0.0  0.0 112732  7548 pts/0    S    11:21   0:00 sudo ROUTER_PID=/mysql/installer/mysql-router/bin/mysqlrouter.pid /mysql/mysql-latest/bin/mysqlrouter -c /mysql/installer/mysql-router/bin/mysqlrouter.conf --user=mysql
mysql      28505  5.6  0.2 1149884 23044 pts/0   Sl   11:21   0:05 /mysql/mysql-latest/bin/mysqlrouter -c /mysql/installer/mysql-router/bin/mysqlrouter.conf --user=mysql
root       28527  0.0  0.0  12144  1160 pts/0    S+   11:22   0:00 grep --color=auto mysqlrouter
[root@localhost bin]#

```
# Edit systemd mysql-router (mysql-router2)

```
[root@localhost bin]# mv /usr/lib/systemd/system/mysqlrouter.service /usr/lib/systemd/system/mysqlrouter.service.bkp
[root@localhost bin]# vi /usr/lib/systemd/system/mysqlrouter.service
[root@localhost bin]# cat /usr/lib/systemd/system/mysqlrouter.service
[Unit]
Description=MySQL Router
After=network-online.target
Wants=network-online.target
After=syslog.target

[Service]
Type=forking
User=mysql
Group=mysql

ExecStart=/mysql/installer/mysql-router/bin/start.sh
ExecStop=/mysql/installer/mysql-router/bin/stop.sh
Restart=always

[Install]
WantedBy=multi-user.target

[root@localhost bin]# sudo systemctl daemon-reload
[root@localhost bin]# sudo systemctl restart mysqlrouter
[root@localhost bin]# sudo systemctl status mysqlrouter

```

# menambahkan keepalived (mysql-router1[node01])

```
[root@node01 bin]#sudo yum install keepalived -y
[root@node01 bin]# mv /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf.bkp
[root@node01 bin]# vi /etc/keepalived/keepalived.conf
[root@node01 bin]# cat /etc/keepalived/keepalived.conf
[root@node01 bin]# cat /etc/keepalived/keepalived.conf
global_defs {
    router_id MYSQL_ROUTER_1
}

vrrp_script chk_mysqlrouter {
    script "/usr/local/bin/check_mysqlrouter.sh"
    interval 2
    weight -20  # Jika script gagal, turunkan prioritas agar failover terjadi
}

vrrp_instance VI_1 {
    interface ens192  # Sesuaikan dengan interface yang digunakan
    state MASTER
    virtual_router_id 51
    priority 100
    unicast_src_ip 192.168.50.127
    unicast_peer {
        192.168.50.114
    }
    virtual_ipaddress {
        192.168.50.100
    }
    track_script {
        chk_mysqlrouter
    }
}

```
### buat script untuk memeriksa mysqlrouter jalan
```
[root@localhost ~]# vi /usr/local/bin/check_mysqlrouter.sh
[root@localhost ~]# cat /usr/local/bin/check_mysqlrouter.sh
#!/bin/bash

# Periksa apakah MySQL Router berjalan
if ! pgrep -x "mysqlrouter" > /dev/null; then
    exit 1  # Jika MySQL Router mati, return status error
else
    exit 0  # Jika MySQL Router hidup, return status sukses
fi

[root@localhost ~]# sudo chmod +x /usr/local/bin/check_mysqlrouter.sh

```

```
[root@node01 bin]# systemctl enable keepalived
[root@node01 bin]# systemctl start keepalived
[root@node01 bin]# systemctl status keepalived
● keepalived.service - LVS and VRRP High Availability Monitor
   Loaded: loaded (/usr/lib/systemd/system/keepalived.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2025-02-20 15:48:18 WIB; 7s ago
  Process: 35001 ExecStart=/usr/sbin/keepalived $KEEPALIVED_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 35004 (keepalived)
    Tasks: 2 (limit: 48452)
   Memory: 1.8M
   CGroup: /system.slice/keepalived.service
           ├─35004 /usr/sbin/keepalived -D
           └─35005 /usr/sbin/keepalived -D

Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: (VI_1) received lower priority (92) advert from 192.168.50.114 - discarding
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: (VI_1) Receive advertisement timeout
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: (VI_1) Entering MASTER STATE
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: (VI_1) setting VIPs.
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: (VI_1) Sending/queueing gratuitous ARPs on ens192 for 192.168.50.100
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: Sending gratuitous ARP on ens192 for 192.168.50.100
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: Sending gratuitous ARP on ens192 for 192.168.50.100
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: Sending gratuitous ARP on ens192 for 192.168.50.100
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: Sending gratuitous ARP on ens192 for 192.168.50.100
Feb 20 15:48:21 node01 Keepalived_vrrp[35005]: Sending gratuitous ARP on ens192 for 192.168.50.100

```
```
[root@node01 bin]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:c4:28:4f brd ff:ff:ff:ff:ff:ff
    altname enp11s0
    inet 192.168.50.127/24 brd 192.168.50.255 scope global dynamic noprefixroute ens192
       valid_lft 471sec preferred_lft 471sec
    inet 192.168.50.100/32 scope global ens192
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fec4:284f/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
3: tun0: <POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UNKNOWN group default qlen 100
    link/none
    inet 192.168.242.8/24 brd 192.168.242.255 scope global tun0
       valid_lft forever preferred_lft forever
    inet6 fe80::de5f:a02d:fe0b:529a/64 scope link stable-privacy
       valid_lft forever preferred_lft forever
```

# menambahkan keepalived (mysql-router2)

```
[root@localhost bin]#sudo yum install keepalived -y
[root@localhost bin]# mv /etc/keepalived/keepalived.conf /etc/keepalived/keepalived.conf.bkp
[root@localhost bin]# vi /etc/keepalived/keepalived.conf
[root@localhost ~]# cat /etc/keepalived/keepalived.conf
global_defs {
    router_id MYSQL_ROUTER_2
}

vrrp_script chk_mysqlrouter {
    script "/usr/local/bin/check_mysqlrouter.sh"
    interval 2
    weight -20  # Jika script gagal, turunkan prioritas agar failover terjadi
}

vrrp_instance VI_1 {
    interface ens192  # Sesuaikan dengan interface yang digunakan
    state BACKUP
    virtual_router_id 51
    priority 90
    unicast_src_ip 192.168.50.114
    unicast_peer {
        192.168.50.127
    }
    virtual_ipaddress {
        192.168.50.100
    }
    track_script {
        chk_mysqlrouter
    }
}

```
### buat script untuk memeriksa mysqlrouter jalan
```
[root@localhost ~]# vi /usr/local/bin/check_mysqlrouter.sh
[root@localhost ~]# cat /usr/local/bin/check_mysqlrouter.sh
#!/bin/bash

# Periksa apakah MySQL Router berjalan
if ! pgrep -x "mysqlrouter" > /dev/null; then
    exit 1  # Jika MySQL Router mati, return status error
else
    exit 0  # Jika MySQL Router hidup, return status sukses
fi

[root@localhost ~]# sudo chmod +x /usr/local/bin/check_mysqlrouter.sh

```

```
[root@localhost sbin]#  systemctl enable keepalived
[root@localhost sbin]#  systemctl start keepalived
[root@localhost sbin]# systemctl status keepalived
● keepalived.service - LVS and VRRP High Availability Monitor
   Loaded: loaded (/usr/lib/systemd/system/keepalived.service; enabled; vendor preset: disabled)
   Active: active (running) since Thu 2025-02-20 15:49:14 WIB; 10s ago
  Process: 3209063 ExecStart=/usr/sbin/keepalived $KEEPALIVED_OPTIONS (code=exited, status=0/SUCCESS)
 Main PID: 3209065 (keepalived)
    Tasks: 2 (limit: 100866)
   Memory: 1.8M
   CGroup: /system.slice/keepalived.service
           ├─3209065 /usr/sbin/keepalived -D
           └─3209066 /usr/sbin/keepalived -D

Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: WARNING - default user 'keepalived_script' for script execution does not exist - please>
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: SECURITY VIOLATION - scripts are being executed but script_security not enabled.
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: Assigned address 192.168.50.114 for interface ens192
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: Assigned address fe80::20c:29ff:fe18:394d for interface ens192
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: Registering gratuitous ARP shared channel
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: (VI_1) removing VIPs.
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: (VI_1) Entering BACKUP STATE (init)
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: VRRP sockpool: [ifindex(  2), family(IPv4), proto(112), fd(11,14), unicast, address(192>
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: VRRP_Script(chk_mysqlrouter) succeeded
Feb 20 15:49:14 localhost.localdomain Keepalived_vrrp[3209066]: (VI_1) Changing effective priority from 90 to 92


```

```
[root@localhost sbin]# ip a
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: ens192: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 00:0c:29:18:39:4d brd ff:ff:ff:ff:ff:ff
    altname enp11s0
    inet 192.168.50.114/24 brd 192.168.50.255 scope global dynamic noprefixroute ens192
       valid_lft 545sec preferred_lft 545sec
    inet 192.168.50.100/24 scope global secondary ens192
       valid_lft forever preferred_lft forever
    inet6 fe80::20c:29ff:fe18:394d/64 scope link noprefixroute
       valid_lft forever preferred_lft forever
[root@localhost sbin]#

```
