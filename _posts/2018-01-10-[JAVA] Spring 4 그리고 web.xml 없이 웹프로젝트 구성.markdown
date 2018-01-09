---
layout: post
title: "[JAVA] Spring 4 그리고 web.xml 없이 웹프로젝트 구성"
date:   2018-01-10 09:00:00 +0900
categories: JAVA SPRING
---

## Spring 4

### Version
 - Spring 3.0 : 2009/12 (Servlet 3.0 - EE6)
 - Spring 3.1 : 2011/11 (ServletContainerInitializer)
 - Spring 3.2 : 2012/12 (Async)
 - Spring 4.0 : 2013/12 (Servlet 3.1 - EE7)
 - Spring 4.1 : 2014/09 (Java SE 8)

### @Deprecated 제거
 - 3.2 까지 @deprecated 였던 Package, Class 제거
 - 대표적으로 iBatis 제거
    - JPA 고려
    - MyBatis 스프링 지원 기능 활용

### Without web.xml

#### web.xml 없이 간단한 웹프로젝트 구성하기

▶ Servlet Application Context를 만든다.
~~~java
@configuration // WEB과 관련된 설정을 담당한다. Java 설정
@EnableWebMvc // Spring Web 과 관련한 Bean들을 가져온다.
@ComponentScan(basePackageClasses=HelloWeb.class)
public class WebContext extends WebMvcConfigurerAdapter {
    // Default에 대한 것을 바꾸려면 Adapter의 메소드를 적절히 Overriding
}
~~~

▶ 비지니스 Root Application Context를 만든다.
~~~java
@configuration 
@ComponentScan(basePackageClasses=HelloService.class)
public class AppContext {
    // 별도의 Extention은 없다.
}
~~~

▶ web.xml 을 대체하는 Java Class
~~~java
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class<?>[0] { AppContext.class }; // 비즈니스 로직
    }

    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class<?>[0] { WebContext.class }; // 웹과 관련한 설정
    }

    @Override
    protected String[] getServletMappings() {
        return new String[] { "/" };
    }
}
~~~

▶ Command Line으로 간단한 테스트
~~~java
public class HelloRunner {
    public static void main(String[] args) {
        // root context 를 가져올 수 있다.
        try (AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AppContext.class) {

            HelloService helloService = ac.getBean(HelloService.class);
            System.out.println(helloService.hello("TEST"));
        }
    }
}
~~~
 
## Spring 4 DI
 - Groovy 빈 설정
 - Generic 타입 식별자
 - 주입포인트 @Lazy
 - 빈 리스트의 정렬순서
 - @Conditional
 - 프록시 클래스 구현 개선
 - 합성 애노테이션의 속성 지원

#### Generic Type DI
 - 콜렉션 타입 인자로 빈 타입을 사용하는 방법
 - 여러개의 Bean을 collection으로 받는다.

▶ Style 1 (autowired 가능함)
~~~java
@Autowired
private List<Logger> loggers;

@Component
public class ConsoleLogger implements Logger { ... }

@Component
public class FileLogger implements Logger { ... }
~~~

▶ Style 2 (autowired 가능함)
~~~java
@Autowired
private List<Logger> loggers;

@Bean
public Logger consoleLogger() { return new ConsoleLogger(); }

@Bean
public Logger fileLogger() { return new FileLogger(); }
~~~

▶ Style 3 (autowired 가능)
~~~java
public interface Logger<T> {
    void log(T t);
}

@Component
public class StringLogger implements Logger<String> { ... }

@Autowired
private Logger<String> logger; 
~~~

▶ Style 4 (autowired 불가능)
 - NoUniqueBeanDefinitionException 발생 (Spring 3.2 에서 에러)
    - 3.x 방식에서는 Generic Autowired가 불가능하다. 
    - Logger<String> logger; 에서 <String> 이 정보를 참조하지 않는다.
    - 해결방안1. Autowired(DI)를 버리고 Constructor(DL) 방식을 취한다. 생성자로 접근한다.
        - public Construrctor(Class<? extends Logger<T>> tClass) { } + @PostConstructor
    - 해결방안2. Type 인자를 상위클래스 정보에서 가져온다. 리플랙션 사용
        - Class<?> types = GenericTypeResolver.resolveTypeArguments(getClass(), GenericService.class);
        - ac.getBean(type[1]);
 - 스프링 4에서는 해당 이슈가 해결이 되었다.
 - 왜 이것이 가능해졌는가?
    - Logger<String> logger; 에서 <String> 이 정보까지 참조해서 Bean을 찾는다.
~~~java
@Autowired
private Logger<String> loggers;

@Component
public class StringLogger implements Logger<String> { ... }

@Component
public class IntegerLogger implements Logger<Integer> { ... }
~~~

#### @Lazy
 - 스프링은 기본적으로 모든 빈을 모두 생성해 놓는다.
 - @Lazy는 실제 사용할 시점에 Bean을 생성하도록 하는 Annotation이다.
 - @Lazy를 사용하는 Bean이 @Lazy가 아니라면 @Lazy를 달아도 먼저 생성이된다.

##### 스프링4에서의 @Lazy  
 - Scope를 지원하는 Bean 생성 지연 Proxy 적용
 - Request Scope, Session Scope 같은 Bean Instance Scopr에 적용
    - Request Scope = 하나의 Request 안에서만 유효하다.
    - Session Scope = 하나의 사용자 Session 안에서만 유효하다.
    - 즉 Bean은 하나를 정의했지만 여러 개의 Instance를 가지게 된다.
 
##### Reference
 - [스프링4와 자바8 도입전략](http://olc.kr/course/course_online_view.jsp?id=437)
