---
layout: post
title: "[DB] MariaDB (MySQL) 설치하기 - CentOS 7.3, MariaDB 10.1.XX"
date:   2016-09-28 17:45:49 +0900
categories: DB 
---

* Table of Contents
{:toc}

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

## DB Port 변경 (/etc/my.cnf)
~~~
[mysqld]
port=40006
~~~

## DB UTF-8 설정 (/etc/my.cnf)
~~~
[mysqld]
character-set-server=utf8
collation-server=utf8_general_ci
~~~

## DB Transation Isolation Level 변경 (/etc/my.cnf)
~~~
[mysqld]
transation-isolation = READ-UNCOMMITTED
transation-isolation = READ-COMMITTED
~~~

## Skip name resolve option 추가
~~~
검색 결과는 대부분 --skip-name-resolve 옵션에 대하여 언급했습니다. 
MySQL(Mariadb)은 새로운 커넥션 요청이 있을 때 인증을 위해 클라이언트의 IP 주소를 통하여 
hostname이 host_cache 테이블에 있는지 확인합니다. 
만약 없거나 인증 flag가 false라면 IP 주소로 hostname을 알아내는 과정을 거치게 됩니다. 
이때 오류가 발생하거나 시간이 오래 소요되어 너무 많은 클라이언트가 연결에 성공하지 못하면 해당 호스트의 추가 연결을 차단하게 됩니다.

MySQL 공식 문서에 따르면 DNS가 느리거나 많은 호스트들을 가지고 있다면 --skip-name-resolve 옵션을 
my.cnf 파일에 추가하여 위와 같은 과정을 무시(disable DNS lookup) 할 수 있다고 합니다. (인증 관련 문제가 생길 수 있기 때문에 사전에 확인하시기 바랍니다.)

추가방법
[mysqld]
skip-name-resolve
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