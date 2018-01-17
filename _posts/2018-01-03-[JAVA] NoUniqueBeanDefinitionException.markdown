---
layout: post
title: "[JAVA] NoUniqueBeanDefinitionException 관련"
date:   2018-01-03 09:00:00 +0900
categories: JAVA SPRING
---

* Table of Contents
{:toc}

#### ▣ 에러로그

~~~
[2018/01/03 11:42:06.012][http-apr-31883-exec-3][ERROR][AbstractStep:225] Encountered an error executing step SomethingJobStep in job SomethingJob
org.springframework.beans.factory.NoUniqueBeanDefinitionException: No qualifying bean of type [org.springframework.transaction.PlatformTransactionManager] is defined: expected single matching bean but found 2: transactionManager,resourcelessTransactionManager
	at org.springframework.beans.factory.support.DefaultListableBeanFactory.getBean(DefaultListableBeanFactory.java:365) ~[spring-beans-4.1.7.RELEASE.jar:4.1.7.RELEASE]
~~~

#### ▣ 문제원인
PlatformTransactionManager를 bean 으로 2개 이상 등록한 경우
실제 어떤 bean을 선택해서 서비스해야할지 판단하기 어려운 케이스입니다.

 - 기본 Core단 로직에서 사용하는 transactionManager (DataSourceTransactionManager)
 - Spring Batch 에서 사용하는 transactionManager (ResourcelessTransactionManager)

#### ▣ 2개의 등록된 Bean 정보

##### DataSourceTransactionManager implements PlatformTransactionManager
~~~java
@Bean
public PlatformTransactionManager transactionManager(@Qualifier("masterDataSource") DataSource masterDataSource) {
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager(masterDataSource);
    transactionManager.setGlobalRollbackOnParticipationFailure(false);
    return transactionManager;
}
~~~

##### ResourcelessTransactionManager implements PlatformTransactionManager
~~~java
@Bean(name = "resourcelessTransactionManager")
public ResourcelessTransactionManager resourcelessTransactionManager() {
    ResourcelessTransactionManager resourcelessTransactionManager = new ResourcelessTransactionManager();
    return resourcelessTransactionManager;
}
~~~

#### ▣ 해결방안

##### 1. 명시적으로 @Transactional 에 이름을 명시합니다.
~~~java
@Transactional("transactionManager")
public void someMethod() {

}
~~~

##### 2. @Primary 를 통해서 특정 Bean을 Default Bean으로 설정합니다.
~~~java
@Primary
@Bean
public PlatformTransactionManager transactionManager(@Qualifier("masterDataSource") DataSource masterDataSource) {
    DataSourceTransactionManager transactionManager = new DataSourceTransactionManager(masterDataSource);
    transactionManager.setGlobalRollbackOnParticipationFailure(false);
    return transactionManager;
}
~~~

#### 궁금증

##### 1. 두 Bean에 모두 @Primary 를 적용한다면 어떻게 될 것인가?
 - 위와 동일한 에러가 발생한다. 선택장애 발생