---
layout: post
title: "[BUILD] Gradle"
date:   2017-04-26 17:45:49 +0900
categories: database 
---

## GRADLE

## 특정 JAVA HOME을 지정하기 (Console 작업시)
~~~
gradle :boot-api:test -Dorg.gradle.java.home=C:\framework\Java\jdk1.8.0_111
~~~

## build.proporties에서 gradle.properties의 속성 참조하기
~~~
in gradle.properties
servelt.api.version=3.1.0

in build.properties
providedCompile "javax.servlet:javax.servlet-api:${property('servlet.api.version')}"

~~~

