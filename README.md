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

#### Access mysql on DB-1

***172.31.40.99*** is the private IP of DB-2 server

```
MariaDB [(none)]> stop slave;
Query OK, 0 rows affected (0.00 sec)
MariaDB [(none)]> CHANGE MASTER TO MASTER_HOST='172.31.40.99',MASTER_USER='master_user2',MASTER_PASSWORD='password',MASTER_LOG_FILE='mariadb-bin.000005',MASTER_LOG_POS=245;
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> start slave;
Query OK, 0 rows affected (0.00 sec)
```

#### Access mysql on DB-2

***172.31.43.142*** is the private IP of DB-1 server

```
MariaDB [(none)]> stop slave;
Query OK, 0 rows affected, 1 warning (0.00 sec)

MariaDB [(none)]> CHANGE MASTER TO MASTER_HOST='172.31.43.142',MASTER_USER='master_user1',MASTER_PASSWORD='password',MASTER_LOG_FILE='mariadb-bin.000005',MASTER_LOG_POS=245;
Query OK, 0 rows affected (0.02 sec)

MariaDB [(none)]> start slave;
Query OK, 0 rows affected (0.00 sec)
```

#### Creating User, Database and table on DB1.
##### Creating User 'project' with remote access

```
MariaDB [(none)]> create user 'project'@'%' identified by 'dbuserpass';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> grant all on *.* to 'project'@'%';
Query OK, 0 rows affected (0.00 sec)

MariaDB [(none)]> flush privileges;
Query OK, 0 rows affected (0.00 sec)
```

#### Creating Database 'company' and table 'employees' with dummy data

```
MariaDB [(none)]> create database company;
Query OK, 1 row affected (0.00 sec)

MariaDB [(none)]> use company;
Database changed
MariaDB [company]> create table employees(ID int(5), Name varchar(50));
Query OK, 0 rows affected (0.00 sec)

MariaDB [company]> insert into employees(ID, Name) values('01', 'surya');
Query OK, 1 row affected (0.00 sec)

MariaDB [company]> insert into employees(ID, Name) values('02', 'kiran');
Query OK, 1 row affected (0.00 sec)

MariaDB [company]> insert into employees(ID, Name) values('03', 'kannan');
Query OK, 1 row affected (0.00 sec)

MariaDB [company]> insert into employees(ID, Name) values('04', 'sam');
Query OK, 1 row affected (0.00 sec)
```
## Accessing DB servers from 'some other server' and listing table 'employees'

#### Install mysql on the other server.

> ~]# yum install mysql -y

#### Access DB1 using its private IP.

```
 ~]# mysql -u project -p -h 172.31.43.142 -e 'select * from company.employees;'
Enter password: 
+------+--------+
| ID   | Name   |
+------+--------+
|    1 | surya  |
|    2 | kiran  |
|    3 | kannan |
|    4 | sam    |
+------+--------+
```

#### Access DB2 using its private IP.
```
~]# mysql -u project -p -h 172.31.40.99 -e 'select * from company.employees;'
Enter password: 
+------+--------+
| ID   | Name   |
+------+--------+
|    1 | surya  |
|    2 | kiran  |
|    3 | kannan |
|    4 | sam    |
+------+--------+
```
