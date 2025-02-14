# mysql-enterprise


```
[root@node01 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6

192.168.50.127 node1
192.168.50.132 node2
192.168.50.136 node3
```

```
[root@node01 mysql]# sudo yum install -y ncurses-compat-libs
```

```
[root@node01 mysql]# sudo mkdir -p /mysql/etc
[root@node01 mysql]# sudo mkdir -p /mysql/data
[root@node01 mysql]# sudo mkdir -p /mysql/installer
[root@node01 mysql]# sudo mkdir -p /mysql/data 
[root@node01 mysql]# sudo mkdir -p /mysql/mysql-latest/bin/
```

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

```
[root@node01 installer]# cd /mysql/installer
[root@node01 mysql-commercial]# unzip p37497652_840_Linux-x86-64.zip
[root@node01 mysql-commercial]# tar xvf mysql-commercial-8.4.4-1.1.el8.x86_64.repo.tar.gz
[root@node01 mysql-commercial]# sudo dnf install ./mysql-commercial-*.rpm
```

```
[root@node01 mysql-commercial]# cd mysql-8.4/8.4.4/
[root@node01 8.4.4]# sudo dnf install ./mysql-commercial-*.rpm -y

```

```
[root@node01 8.4.4]# systemctl enable mysqld
[root@node01 8.4.4]# systemctl start mysqld
[root@node01 8.4.4]# systemctl status mysqld
```

```
[root@node01 8.4.4]# cp /usr/bin/mysq* /mysql/mysql-latest/bin/
[root@node01 8.4.4]#

```

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
report_host = 192.168.50.127       
#####################################

```

```
[root@node01 8.4.4]# sudo yum install -y rsync
[root@node01 8.4.4]# sudo rsync -av /var/lib/mysql/ /mysql/data/
[root@node01 8.4.4]# cp /var/log/mysqld.log /mysql/log/

```

```
[root@node01 8.4.4]# sudo chown -R mysql:mysql /mysql /var/lib/mysql
[root@node01 8.4.4]# sudo chmod -R 750 /mysql /var/lib/mysql
```

```
[root@node01 8.4.4]# sudo setenforce 0
[root@node01 8.4.4]# sudo systemctl restart mysqld
[root@node01 8.4.4]# sudo systemctl status mysqld
```
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

```
```

mysql> CREATE USER 'admin'@'%' IDENTIFIED BY 'Welcome1!';
Query OK, 0 rows affected (0.11 sec)

mysql> GRANT ALL PRIVILEGES ON *.* TO 'admin'@'%' WITH GRANT OPTION;
Query OK, 0 rows affected (0.01 sec)

mysql>

```

```
[root@node03 8.4.4]# cd /mysql/installer/mysql-shell/
[root@node03 mysql-shell]# sudo rpm -ivh mysql-shell-commercial-8.4.4-1.1.el8.x86_64.rpm


```
```
[root@node03 ~]# mysqlsh
MySQL Shell 8.4.4-commercial

Copyright (c) 2016, 2025, Oracle and/or its affiliates.
Oracle is a registered trademark of Oracle Corporation and/or its affiliates.
Other names may be trademarks of their respective owners.

Type '\help' or '\?' for help; '\quit' to exit.
 MySQL  SQL > \js
Switching to JavaScript mode...
 MySQL  JS > shell.connect("admin@192.168.50.136")
Creating a session to 'admin@192.168.50.136'
Please provide the password for 'admin@192.168.50.136': *********
Save password for 'admin@192.168.50.136'? [Y]es/[N]o/Ne[v]er (default No): y
Fetching schema names for auto-completion... Press ^C to stop.
Your MySQL connection id is 9 (X protocol)
Server version: 8.4.4-commercial MySQL Enterprise Server - Commercial
No default schema selected; type \use <schema> to set one.
<Session:admin@192.168.50.136:33060>
 MySQL  192.168.50.136:33060+ ssl  JS >
```

```
 MySQL  192.168.50.136:33060+ ssl  JS > dba.configureInstance('admin@192.168.50.136:3306')
Configuring local MySQL instance listening at port 3306 for use in an InnoDB Cluster...

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
 MySQL  19
```
```
isi

```

```
isi

```
```
isi

```
```
isi

```

```
isi

```
```
isi

```
```
isi

```

```
isi

```
