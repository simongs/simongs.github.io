---
layout: post
title: "[JAVA] Java Callable & Future - @Async 적용하기"
date:   2017-05-29 09:00:00 +0900
categories: JAVA
---

* Table of Contents
{:toc}

#### Note
 - 본 글은 5월 26일에 작성된 [Java Callable & Future Sample](http://simongs.github.io/java/2017/05/26/JAVA-Java-Callable-&-Future-Sample.html)의 연장선상에 있는 글입니다.

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

# @Async의 Return Type
 - void
 - Future<T>
 - ListenableFuture<T>
 - CompletableFuture<T>

# @Async with Future
 - java의 기본 비동기 interface
 - get() Blocking Method이다

~~~java
@Async
public Future<String> service() {
    return AsyncResult<>(result);
}

Future<String> f = myService.service();
String result = f.get()
~~~

# @Async with ListenableFuture
 - Spring 4.X 대에 만들어진 Future
 - Future.get() 처럼 Blocking 되지 않고 Callback을 통하여 호출한다.
~~~java
@Async
public ListenableFuture<String> service() {
    return AsyncResult<>(result);
}

ListenableFuture<String> f = myService.service();
f.addCallback(r->log.info("success", r), e->log.info("error", e));
~~~

# @Async with CompletableFuture
 - Java 8에서 지원되는 비동기 Future

~~~java
@Async
public CompletableFuture<String> service() {
    return CompletableFuture.completedFuture(result);
}

CompletableFuture<String> f = myService.service();
f.thenAccept(r->log.info("success", r));
~~~

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