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
