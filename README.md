# mysql-enterprise


```
[root@node03 ~]# vi /etc/hosts
[root@node03 ~]# cat /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
192.168.242.8 node01
192.168.242.9 node02
192.168.50.136 node03
[root@node03 ~]#

```

```
[root@node03 mysql]# sudo yum install -y ncurses-compat-libs
```

```
[root@node03 mysql]# sudo mkdir /mysql/ /mysql/etc /mysql/data /mysql/installer 
[root@node03 mysql]# sudo mkdir /mysql/log /mysql/temp /mysql/binlog /mysql/mysql-latest/bin
```

```
[root@node03 mysql]# cd
[root@node03 ~]# vi .bashrc
[root@node03 ~]# cat .bashrc
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
export PATH=$PATH:/mysql/mysql-latest/bin    #
export MYSQL_PS1="\\u on \\h>\\_"            #
##############################################

[root@node03 ~]#
```

```
[root@node03 installer]# cd /mysql/installer/mysql-commercial/
[root@node03 mysql-commercial]# unzip p37497652_840_Linux-x86-64.zip
[root@node03 mysql-commercial]# tar xvf mysql-commercial-8.4.4-1.1.el8.x86_64.repo.tar.gz
[root@node03 mysql-commercial]# sudo dnf install ./mysql-commercial-*.rpm
```

```
[root@node03 mysql-commercial]# cd mysql-8.4/8.4.4/
[root@node03 8.4.4]# sudo dnf install ./mysql-commercial-*.rpm -y

```

```
[root@node03 8.4.4]# systemctl enable mysqld
[root@node03 8.4.4]# systemctl start mysqld
[root@node03 8.4.4]# systemctl status mysqld
```

```
[root@node03 8.4.4]# cp /usr/bin/mysq* /mysql/mysql-latest/bin/
[root@node03 8.4.4]#

```

```
[root@node03 8.4.4]# vi /etc/my.cnf
[root@node03 8.4.4]# cat /etc/my.cnf

#datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

#log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

#####################################
datadir=/mysql/data                 #
log-error=/mysql/log/mysqld.log     #
bind-address = 0.0.0.0              #
report_host = 192.168.242.136       #
#####################################

```

```
[root@node03 8.4.4]# sudo yum install -y rsync
[root@node03 8.4.4]# sudo rsync -av /var/lib/mysql/ /mysql/data/
[root@node03 8.4.4]# cp /var/log/mysqld.log /mysql/log/

```

```
[root@node03 8.4.4]# sudo setenforce 0
[root@node03 8.4.4]# sudo systemctl restart mysqld
[root@node03 8.4.4]# sudo systemctl status mysqld
```
```
[root@node03 8.4.4]# grep 'temporary password' /mysql/log/mysqld.log
2025-02-12T06:10:04.134055Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: FidhFtbwJ4.y
[root@node03 8.4.4]# /mysql/mysql-latest/bin/mysql_secure_installation -h127.0.0.1 -P3306

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
[root@node03 8.4.4]#
```

```
[root@node03 8.4.4]# mysql -uroot -pWelcome1! -h 127.0.0.1 -P3306
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
