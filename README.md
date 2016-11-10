# web-gui for clickhouse
You can perform queries, save them and download as csv with this gui for clickhouse server. 

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/login.jpg)

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/query.jpg)

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/save.jpg)

# Getting started

1. First, you need to install mysql server. If you use Debian or Ubuntu, you may do this by following command:
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

git clone https://github.com/AlexPosix/web-gui.git

cd ./web-gui/

mysqldump -u jboss -p clickhouse > clickhouse.sql
Enter password:

```

