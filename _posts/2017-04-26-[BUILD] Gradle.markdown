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

## spring-boot 프로젝트 WAR 를 TOMCAT에 배포하기

[How To Traditional War Deployment](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-traditional-deployment.html)

### Step 1
SpringBootServletInitializer 클래스를 상속하고 그것의 configure()를 override 한다.
Spring 프레임워크의 Servelt 3.0 스펙 지원과 서블릿 컨테이너에 의해 너의 어플리케이션에 설정되게끔 한다.
보통은 당신이 만든 SpringBoot의 메인클래스가 SpringBootServletInitializer 를 상속하게끔 한다.

### Step 2
당신의 프로젝트가 war 배포판을 생산할 수 있도록 build 설정을 변경한다.

~~~
<packaging>war</packaging> // pom.xml
or
apply plugin: 'war' // build.gradle
~~~

### Step 3
embedded된 서블릿 컨테이너가 war 파일이 서블릿 컨테이너에 배포되는 시점에 방해되지 않도록 보장한다.
그렇게 하기 위해 embedded된 서블릿 컨테이너의 dependency를 provided 로 마킹한다.

### Sample 
~~~java
@SpringBootApplication(scanBasePackages = "com.dasolute.simons")
@EnableJpaRepositories(basePackages = "com.dasolute.simons", includeFilters = @ComponentScan.Filter(JPARepository.class))
@EntityScan(basePackages = "com.dasolute.simons")
public class SpringBootWebApplication extends SpringBootServletInitializer {

    public static void main(String args[]) {
        SpringApplication.run(SpringBootWebApplication.class, args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder application) {
        return application.sources(SpringBootWebApplication.class);
    }
}
~~~
