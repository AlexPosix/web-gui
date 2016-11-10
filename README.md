Written by Vaskovskij Alex, http://vaskovskij.org.ua

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
nano ~/wildfly-10.1.0.Final/bin/standalone.conf
```
Find  <br />
JAVA_OPTS="-Xms64m -Xmx512m -XX:MetaspaceSize=96M -XX:MaxMetaspaceSize=256m -Djava.net.preferIPv4Stack=true" <bt /> 
and modify for your purposes. 

## Starting and configure Wildfly

Start wildfly:
```
$ ~/wildfly-10.1.0.Final/bin/standalone.sh -b=0.0.0.0 -bmanagement=0.0.0.0 &
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

Now we need to add mysql jdbc connector to our wildfly.
Download connector from http://dev.mysql.com/downloads/connector/j/ or from another resource.
Go to ~/wildfly-10.1.0.Final/modules/system/layers/base/com and create mysql and then main folder
```
$ cd ~/wildfly-10.1.0.Final/modules/system/layers/base/com
$ mkdir mysql
$ cd ~/wildfly-10.1.0.Final/modules/system/layers/base/com/mysql/
$ mkdir main
```
Place module into directory
```
$ cp mysql-connector-java-5.1.38-bin.jar ~/wildfly-10.1.0.Final/modules/system/layers/base/com/mysql/main
```
and create module.xml with the following content
```
<?xml version="1.0" encoding="UTF-8"?>
  <module xmlns="urn:jboss:module:1.1" name="com.mysql">
     <resources>
         <resource-root path="mysql-connector-java-5.1.38-bin.jar"/>
     </resources>
     <dependencies>
         <module name="javax.api"/>
         <module name="javax.transaction.api"/>
     </dependencies>
 </module>
```
Place it into the same directory as connector.
```
$ ls ~/wildfly-10.1.0.Final/modules/system/layers/base/com/mysql/main
$ module.xml  mysql-connector-java-5.1.38-bin.jar
```

Now add our jdbc driver into datasource:
```
$ cd ~/wildfly-10.1.0.Final/bin
$ ./jboss-cli.sh --connect

[standalone@localhost:9990 /] /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.jdbc.Driver)

```
The output should be as 
```
12:46:20,419 INFO  [org.jboss.as.connector.deployers.jdbc] (MSC service thread 1-8) WFLYJCA0018: Started Driver service with driver-name = mysql
12:46:20,461 INFO  [org.jboss.as.controller] (management-handler-thread - 3) WFLYCTL0183: Service status report
WFLYCTL0185:    Newly corrected services:
      service jboss.data-source.reference-factory.clickhouse (no longer required)
      service jboss.data-source-config.clickhouse (no longer required)
      service jboss.jdbc-driver.mysql (new available)

{"outcome" => "success"}
```
Let's start to add our datasource into Wildfly subsystem. Go to the Wildfly console configuration -> Subsystems -> Datasoutces -> Non-XA and press add
