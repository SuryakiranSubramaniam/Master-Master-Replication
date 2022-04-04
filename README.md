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

#### Mariadb master-master replication on DB-1 and DB-2

#### In DB-1

> ~]#  mysql -u root -p123

```
MariaDB [(none)]> grant replication slave on *.* to master_user1 identified by 'password';

MariaDB [(none)]> flush privileges;

MariaDB [(none)]> flush tables with read lock;

MariaDB [(none)]> show master status;

+--------------------+----------+--------------+------------------+
| File               | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+--------------------+----------+--------------+------------------+
| mariadb-bin.000004 |      466 |              |                  |
+--------------------+----------+--------------+------------------+
1 row in set (0.00 sec)
[root@ip-172-31-43-142 ~]# systemctl restart mariadb
[root@ip-172-31-43-142 ~]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.68-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show master status;
+--------------------+----------+--------------+------------------+
| File               | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+--------------------+----------+--------------+------------------+
| mariadb-bin.000005 |      245 |              |                  |
+--------------------+----------+--------------+------------------+
1 row in set (0.00 sec)

```

#### In DB-2

> ~]#  mysql -u root -p123

```
MariaDB [(none)]> grant replication slave on *.* to master_user2 identified by 'password';

MariaDB [(none)]> flush privileges;

MariaDB [(none)]> flush tables with read lock;

MariaDB [(none)]> show master status;

+--------------------+----------+--------------+------------------+
| File               | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+--------------------+----------+--------------+------------------+
| mariadb-bin.000004 |      466 |              |                  |
+--------------------+----------+--------------+------------------+
1 row in set (0.00 sec)

MariaDB [(none)]> exit
Bye
[root@ip-172-31-40-99 ~]# systemctl restart mariadb
[root@ip-172-31-40-99 ~]# mysql -u root -p
Enter password: 
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 5.5.68-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> show master status;
+--------------------+----------+--------------+------------------+
| File               | Position | Binlog_Do_DB | Binlog_Ignore_DB |
+--------------------+----------+--------------+------------------+
| mariadb-bin.000005 |      245 |              |                  |
+--------------------+----------+--------------+------------------+
1 row in set (0.00 sec)

```
#### Restart Mariadb on both servers.

> ~]# systemctl restart mariadb
