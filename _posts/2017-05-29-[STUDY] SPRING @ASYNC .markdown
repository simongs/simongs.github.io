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

## Toby의 리액티브 웹 개발 4부

### 자바와 스프링의 비동기 개발 기술 살펴보기

#### 자바에서 비동기 작업의 결과를 받아오는 두가지 방법
1. Future
2. Callback

#### Future 에 대한 이야기
1.5 버젼때 만들어진 객체.

Thread를 하나하나 생성하는 것 자체가 비용이다. 
그래서 Thread Pool을 만들어 놓고 Thread를 재활용하여 비용을 줄이는 기법
Executors.newCachedThreadPool() => 만들어진 Thread를 GC 하지 않고 그대로 남겨놓음

ExecutorService의 submit()를 사용하면 다른 Thread의 결과 값을 받을 수 있다.
Callable 인터페이스는 예외 발생 시 Exception을 던지고 결과객체도 리턴할 수 있다.
Runnable 인터페이스는 예외를 안에서 모두 해결해야 하고 결과객체 리턴 불가.

future.get() - blocking method, 결과를 리턴해 줄 수 있을 때까지 대기한다.

#### FutureTask 에 대한 이야기
 - Callback에 대한 이야기 - 시작은 FutureTask


~~~java
ExecutorService es = Executors.newCachedThreadPool();

FutureTask<String> futureTask = new FutureTask<String>(() -> {
    Thread.sleep(2000L);
    return "done";
}) {
    // 익명클래스를 정의한다.
    // 
    @Override
    protected void done() {
        System.out.println(get());
    }
}

es.execute(futureTask);
es.shutdown(); //  비동기 작업들이 모두 끝날때까지 대기하다가 종료하라
~~~

#### ListenableFuture

#### CompletableFuture

#### DeferedResult
 - Controller의 리턴 값으로 DeferedResult를 사용한다.
 - DeferedResult 에 별도의 signal 을 보내면 (Event 발생) 다시 응답 스레드에 응답한다.
 - request -> tomcat Thread -> deferedResult -> Event -> tomcat Thread -> response
 - regsiter, counter, event 의 URI 로 테스트 가능

#### ResponseBodyEmitter
 - 한번 요청에 여러번 응답을 보내는 기술?

#### 스프링의 기본 Executor

##### SimpleAsyncTaskExecutor
이건 작업 하나당 무조건 1 Thread를 만든다.

##### @Bean
 - Thread 통계를 보고 싶다면 ThreadDecorator를 통해서 수집하면 좋다.
~~~java
@Bean
ThreadPoolTaskExecutor tp() {
    ThreadPoolTaskExecutor te = new ThreadPoolTaskExecutor();
    te.setCorePoolSize(10); // ThreadPool을 기본적으로 4개를 만들어둔다. (첫 요청 시점에)
    te.setMaxPoolSize(100); // pool이 꽉찼는데 100개까지 만든다 는 아니다. queue만큼 차면 늘리는 개념이다.
    te.setQueueCapacity(200); // Queue가 꽉 차면 MaxPoolSize까지 늘린다. 
    te.setThreadNamePrefix("myThread");
    te.initialize();
    return te;
}
~~~