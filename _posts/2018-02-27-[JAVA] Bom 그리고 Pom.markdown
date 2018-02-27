---
layout: post
title: "[JAVA] BOM 그리고 POM"
date:   2018-02-27 09:00:00 +0900
categories: JAVA SPRING
---

* Table of Contents
{:toc}

## BOM (Bill Of Materials)
 - Bill Of Materials 의 약자
 - 일종의 특별한 POM 파일
 - 프로젝트에서 사용하는 Dependency들을 한 곳에서 관리하고 싶을 때 사용한다.
 - <dependencyManagement> 섹션을 사용함

## POM
 - 프로젝트의 정보 및 설정을 담고 있는 XML 파일
 - 프로젝트를 빌드하기 위한 Dependency 를 관리하는데 사용된다. 

## BOM Sample

~~~xml
<project ...>
    <modelVersion>4.0.0</modelVersion>
    <groupId>baeldung</groupId>
    <artifactId>Baeldung-BOM</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <packaging>pom</packaging>
    <name>BaelDung-BOM</name>
    <description>parent pom</description>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>test</groupId>
                <artifactId>a</artifactId>
                <version>1.2</version>
            </dependency>
            <dependency>
                <groupId>test</groupId>
                <artifactId>b</artifactId>
                <version>1.0</version>
                <scope>compile</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
</project>
~~~

## How to use BOM

▶ CASE 1

~~~xml
<project ...>
    <parent>
        <groupId>baeldung</groupId>
        <artifactId>Baeldung-BOM</artifactId>
        <version>0.0.1-SNAPSHOT</version>
    </parent>
</project>
~~~

▶ CASE 2

~~~xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>baeldung</groupId>
            <artifactId>Baeldung-BOM</artifactId>
            <version>0.0.1-SNAPSHOT</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
~~~

## Spring BOM File
~~~xml
<dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-framework-bom</artifactId>
            <version>4.3.8.RELEASE</version>
            <type>pom</type>
            <scope>import</scope>
        </dependency>
    </dependencies>
</dependencyManagement>
~~~

## How to Use POM
 - POM에서는 기정의된 Dependency Version이 존재하므로 Version 정보없이 기입가능
 - BOM에 명시된 Version 정보를 가져온다.

~~~xml
<dependencies>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-web</artifactId>
    </dependency>
<dependencies>
~~~

## GRADLE 의 Dependency 관리

### io.spring.dependency-management

### build.properties

~~~bash
buildscript {
    dependencies {
        classpath 'io.spring.gradle:dependency-management-plugin:$Version'
    }
}

configure(allprojects) {
    apply plugin: 'io.spring.dependency-management'

    # MAVEN의 <dependencyManagement> 섹션과 동일한 역할
    dependencyManagement {
        imports {
            mavenBom 'io.spring.platform:platform-bom:$Version'
        }
    }
}
~~~

## 또 다른 BOM (Byte Order Mark )
 - Byte Order Mark 의 약자
 - 유니코드 인코딩에 여러가지 방식이 존재하여 생긴 이슈
 - 문서 앞에 Signature 바이트를 넣는다.

### BOM 정보

| 인코딩 방식 | Byte Order Mark(BOM) |
|---|---|
| UTF-8 | EF BB BF |
| UTF-16 Big Endian | FE FF |
| UTF-16 Little Endian | FF FE |
| UTF-32 Big Endian | 00 00 FE FF |
| UTF-32 Little Endian | FF FE 00 00 |

### BOM의 Side Effect
 - Windows 환경에서 작성한 파일을 Linux 환경으로 올렸을 때 Byte 수가 맞지 않는 경우 발생
 - Windows 의 NotePad 등은 UTF-8 파일을 생성할때 자동으로 BOM을 넣는다.
 - 파일의 크기가 예상과는 다른 상황이 발생할 수 있다.

### Reference
  - [Spring With Maven BOM](http://www.baeldung.com/spring-maven-bom)
  - [기본적인 Managed Dependency 사용법](http://whiteship.tistory.com/1600)
  - [UTF-8 인코딩에서의 BOM(Byte Order Mark) 문제](http://blog.wystan.net/2007/08/18/bom-byte-order-mark-problem)