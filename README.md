# web-gui for clickhouse
You can perform queries, save them and download as csv with this gui for clickhouse server. 

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/login.jpg)

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/query.jpg)

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/save.jpg)

# Getting started

1. First, you need to install mysql server. If you use Debian or Ubuntu, you may do this by following command:
...
```
$  apt-get install mysql-server
```
As root then perform:
```
mysql -u root -p

mysql> create database clickhouse;
Query OK, 1 row affected (0.02 sec)

mysql> grant all on clickhouse.* to 'jboss'@'%' identified by 'set your password';
Query OK, 0 rows affected (0.00 sec)

```
Clone files from repository and restore dump to mysql
```
git clone https://github.com/AlexPosix/web-gui.git
cd ./web-gui/

mysql -u jboss -p clickhouse < clickhouse.sql
Enter password:
```

2. Download jdk and wildfly:

You may download JDK from Oracle site: <br />
http://www.oracle.com/technetwork/java/javase/downloads/ <br />
(I used JDK 8)
