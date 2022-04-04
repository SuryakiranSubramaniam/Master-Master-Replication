# Master-Master-Replication
Master-Master-Replication

## Creating 2 EC2 instances DB-1 and DB-2.

#### Install mariadb-server on DB1 and DB2

> [root@ip-172-31-33-1 ~]# yum install mariadb-server -y

#### After installation Enabling log_bin on both servers.

> [root@ip-172-31-33-1 ~]# vim /etc/my.cnf.d/server.cnf

#### In DB1

``` 
[mysqld]

server-id=1

log_bin=/var/log/mariadb/mariadb-bin.log 
```

#### In DB2

``` 
[mysqld]

server-id=2

log_bin=/var/log/mariadb/mariadb-bin.log 
```

Create 'root' user.
