---
layout: post
title: "[STUDY] SRPING @ASYNC 적용하기"
date:   2017-05-29 09:00:00 +0900
categories: STUDY
---

# 적용방법

## @Configuration을 통한 적용
▶ @EnableAsync 적용 
~~~
@SpringBootApplication(scanBasePackages = "com.dasolute.simons")
@EnableAsync
public class SpringBootTestApplication {
}
~~~

▶ taskExecutor 생성
~~~
@Configuration
public class AsyncConfiguration {

    @Bean
    public TaskExecutor taskExecutor() {
        ThreadPoolTaskExecutor taskExecutor = new ThreadPoolTaskExecutor();
        taskExecutor.setCorePoolSize(2);
        taskExecutor.setMaxPoolSize(8);
        taskExecutor.setQueueCapacity(32);
        return taskExecutor;
    }
}
~~~

## XML Configuration을 통한 적용

~~~
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:task="http://www.springframework.org/schema/task"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
       http://www.springframework.org/schema/beans/spring-beans-3.2.xsd
       http://www.springframework.org/schema/task
       http://www.springframework.org/schema/task/spring-task-3.2.xsd">

    <!-- @async 사용을 위한 XML 설정 -->
    <task:annotation-driven />
    <task:executor id="messageTaskExecutor" pool-size="8" queue-capacity="100"/> <!-- 메시지 발송은 최대 8개로만 운영한다. -->
</beans>
~~~

# 수행방법
위의 설정이 적용되면 spring task (schedule)과 관련한 annotation을 인식할 수 있다.
@Async, @Scheduled 가 붙어 있는 Method나 Class를 비동기적인 수행을 하도록 한다.

Spring-Boot에서는 @EnableAsync, @EnableScheduling 을 통해서 활성화 시킬 수 있다.

# Question

### Q1. executor는 언제 shutdown이 되는가?

ThreadPoolTaskExecutor 객체는 DisposableBean을 구현하고 있어서 Tomcat Instance가 종료되는 시점에 
BeanFactory로부터 destory()가 호출되고 ExecutorConfigurationSupport 에 구현된 destory()에 의해서 
executor.shutdown()이 호출된다.

~~~java
public class ThreadPoolTaskExecutor 
    extends ExecutorConfigurationSupport 
    implements AsyncListenableTaskExecutor, SchedulingTaskExecutor {
}
~~~

~~~java
public abstract class ExecutorConfigurationSupport 
    extends CustomizableThreadFactory 
    implements BeanNameAware, InitializingBean, DisposableBean {
~~~

~~~java
public interface DisposableBean {
	/**
	 * Invoked by a BeanFactory on destruction of a singleton.
	 * @throws Exception in case of shutdown errors.
	 * Exceptions will get logged but not rethrown to allow
	 * other beans to release their resources too.
     *
     * singleton 객체가 소멸되는 시점에 BeanFactory로부터 호출된다.
	 */
	void destroy() throws Exception;
}
~~~