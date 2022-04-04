# Master-Master-Replication
Master-Master-Replication

## Creating 2 EC2 instances DB-1 and DB-2.

#### Install mariadb-server on DB-1 and DB-2

> ~]# yum install mariadb-server -y

#### After installation Enabling log_bin on both servers.

> ~]# vim /etc/my.cnf.d/server.cnf

#### In DB-1

``` 
[mysqld]

server-id=1

log_bin=/var/log/mariadb/mariadb-bin.log 
```

#### In DB-2

``` 
[mysqld]

server-id=2

log_bin=/var/log/mariadb/mariadb-bin.log 
```

#### Start and enable mysql server on both servers

> ~]# systemctl start mariadb
> ~]# systemctl enable mariadb


#### Create 'root' user on both servers

> ~]# mysql_secure_installation

#### Restart mariadb

> ~]# systemctl restart mariadb

#### Mariadb master-master replication on DB-1 and DB-2.
