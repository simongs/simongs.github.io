---
layout: post
title: "[DB] MariaDB (MySQL) 설치하기 - CentOS 7.3, MariaDB 10.1.XX"
date:   2016-09-28 17:45:49 +0900
categories: database 
---

## Version Info
~~~
[mysql]$ cat /etc/redhat-release
entOS Linux release 7.3.1611 (Core)

[mysql]$ mysql -V
mysql  Ver 15.1 Distrib 10.1.23-MariaDB, for Linux (x86_64) using readline 5.1
~~~

## yum repository 설정

### 설치 FLOW
~~~
vi /etc/yum.repos.d/CentOS-MariaDB.repo
 MariaDB 10.1 CentOS repository list - created 2017-05-02 14:41 UTC
# http://downloads.mariadb.org/mariadb/repositories/
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
~~~

## MariaDB 설치

~~~
yum install MariaDB-server MariaDB-client -y
~~~

## MariaDB 설정파일 복사
~~~
cp -r /usr/share/mysql/my-small.cnf /etc/my.cnf
~~~

## DB Port 변경
~~~
[mysqld]
port=40006
~~~

## DB UTF-8 설정
~~~
[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci
~~~

## DB Transation Isolation Level 변경
~~~
[mysqld]
transation-isolation = READ-UNCOMMITTED
transation-isolation = READ-COMMITTED
~~~

## mariadb start & stop
~~~
[irteam@simons-app901 etc]$ systemctl stop mariadb
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: irteam
Password:
==== AUTHENTICATION COMPLETE ===

[irteam@simons-app901 etc]$ systemctl start mariadb
==== AUTHENTICATING FOR org.freedesktop.systemd1.manage-units ===
Authentication is required to manage system services or units.
Authenticating as: irteam
Password:
==== AUTHENTICATION COMPLETE ===
~~~

### ROOT password 변경
~~~
sudo mysqladmin password
~~~

### DB 생성 및 사용자 추가
~~~
[etc]$ mysql -u root -p
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 4
Server version: 10.1.23-MariaDB MariaDB Server

Copyright (c) 2000, 2017, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]> use mysql
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
MariaDB [mysql]> create database simpledb;
Query OK, 1 row affected (0.00 sec)

MariaDB [mysql]> create user 'simongs'@'localhost' identified by 'password';
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> create user 'simongs'@'%' identified by 'password';
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> flush privileges;
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> grant all privileges on simpledb.* to 'simongs'@'%';
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> grant all privileges on simpledb.* to 'simongs'@'localhost';
Query OK, 0 rows affected (0.00 sec)

MariaDB [mysql]> grant all privileges on *.* to 'root'@'%' identified by 'ROOT PASSWORD';
Query OK, 0 rows affected (0.00 sec)
~~~

### 잘 떴는지 확인
~~~
[ ~]$ netstat -anp | grep 3306
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
tcp        0      0 0.0.0.0:3306            0.0.0.0:*               LISTEN      -
~~~

### Client

- MySQL Workbench (Mac, Windows, Linux), Free, open-source
- Sequel Pro (Mac), Free, open-source

### 참고 URL

- https://ko.wikipedia.org/wiki/MariaDB (위키)
- http://firstboos.tistory.com/entry/CentOS-7-에서-mariadb-설치
- [How to Install and Secure MariaDB 10 in CentOS 7](https://www.tecmint.com/install-mariadb-in-centos-7/)