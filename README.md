# web-gui for clickhouse
You can perform queries, save them and download as csv with this gui for clickhouse server. 

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/login.jpg)

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/query.jpg)

![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/save.jpg)

# Getting started

First, you need to install mysql server. If you use Debian or Ubuntu, you may do this by following command:

```
$  apt-get install mysql-server
```
As root then perform:
```
$ mysql -u root -p

mysql> create database clickhouse;
Query OK, 1 row affected (0.02 sec)

mysql> grant all on clickhouse.* to 'jboss'@'%' identified by 'set your password';
Query OK, 0 rows affected (0.00 sec)

```
Clone files from repository and restore dump to mysql
```
$ git clone https://github.com/AlexPosix/web-gui.git
$ cd ./web-gui/

$ mysql -u jboss -p clickhouse < clickhouse.sql
Enter password:
```

Download JDK and Wildfly:

You may download JDK from Oracle site: <br />
http://www.oracle.com/technetwork/java/javase/downloads/ (I used JDK 8)<br />
Copy file to your server, unarchive JDK and move to /usr/lib.
```
$ tar -xzf jdk-8u111-linux-x64.tar.gz
$ mv /home/user/jdk1.8.0_111 /usr/lib/
```
Modify your /etc/profile for JAVA_HOME variable, add export variable to file:
```
export JAVA_HOME=/usr/lib/jdk1.8.0_111
```
Check version:
```
$ . /etc/profile
$ java -version
java version "1.8.0_111"
Java(TM) SE Runtime Environment (build 1.8.0_111-b14)
Java HotSpot(TM) 64-Bit Server VM (build 25.111-b14, mixed mode)
```
Now download and unarchive Wildfly from http://wildfly.org/downloads/ (I used 10.1.0) <br />
If query result set may be a couple of millions, you can modify java memory heap space in Wildfly standalone.conf 
```
nano ./wildfly-10.1.0.Final/bin/standalone.conf
```
Find  <br />
JAVA_OPTS="-Xms64m -Xmx512m -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m -Djava.net.preferIPv4Stack=true" <bt /> 
and modify for your purposes. 

## Starting and configure Wildfly

Start wildfly:
```
$ /home/user/wildfly-10.1.0.Final/bin/standalone.sh -b=0.0.0.0 -bmanagement=0.0.0.0 &
```
Open in your browser Wildfly admin console 
http://yourhost:9990 <br />
You see <br />
![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/wildflyrealm.jpg)

You need to add user for Wildfly management. 
```
$ cd ~/wildfly-10.1.0.Final/bin
$ ./add-user.sh
 What type of user do you wish to add?
  a) Management User (mgmt-users.properties)
 b) Application User (application-users.properties)
(a):

Enter the details of the new user to add.
Using realm 'ManagementRealm' as discovered from the existing property files.
Username : root
The username 'root' is easy to guess
Are you sure you want to add user 'root' yes/no? yes
Password recommendations are listed below. To modify these restrictions edit the add-user.properties configuration file.
 - The password should be different from the username
 - The password should not be one of the following restricted values {root, admin, administrator}
 - The password should contain at least 8 characters, 1 alphabetic character(s), 1 digit(s), 1 non-alphanumeric symbol(s)
Password :
```
Try to reload page and set your credentials, you will enter into admin console.
![alt tag](https://github.com/AlexPosix/web-gui/blob/master/images/adminconsole.jpg)
