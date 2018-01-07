---
layout: post
title: "[JAVA] Infrastructure Bean (with @Enable..)"
date:   2018-01-07 09:00:00 +0900
categories: JAVA SPRING
---

* Table of Contents
{:toc}

## Infrastructure Bean
 - BeanDefinition.getRole()
    - Bean은 역할을 가지고 있다.
    - Application(0), Support, Infrastucture(2)
    - Application : 개발자가 정의한 빈
    - Infrastucture
        - 백드라운드 역할
        - 개발자가 직접 사용할 필요는 없지만 중요한 내부 역할을 담당한다.
 - Spring 1.X 에는 XML에 모두 등록해서 사용했었다.
    - infrastucture를 의미를 모른 채 등록을 했어야 했다.
    ~~~
    <bean name="" class="TransactionAttributeSourceAdvisor" /> 
    ~~~
 - Spring 2.0 에는 네임스페이스와 커스텀 태그를 이용해서 인프라빈 정의를 단순하게 했다.
    - 1.X 대처럼 하나하나 등록해주지 않았다. 
    ~~~
    <tx:annotation-driven /> <!-- 4가지 종류의 bean을 등록한다. -->
    ~~~
 - Spring 2.5 에는 XML과 @Component 의 분리
    - Application Bean을 XML에서 분리해냈다.
    - 그러나 InfraStructure Bean은 그대로 XML에 남아있었다.
        - ex) Datasource, TransactionManager ..
 - Spring 3.0 에는 @Configuration이 등장했다.
    - InfraStructure Bean은 내부구조가 다양하고 복잡해서 자바코드로 만들기 힘들었다.
    - JavaConfig 라고 불리운다. (로드존슨이 만들었다.)
    - 3.0에서 과감히 코어에 통합, @Configuration, @Bean
    - 한계가 있었다. (스프링 코어에서도 잘 소화내지 못했음)
 - Spring 3.1 
    - No XML 이라는 슬로건을 검
    - 스프링이 모범을 보임 = @Enable..
        - 인프라 빈 커스텀 태그의 대부분에 적용
    - 스프링 사용자도 사용 가능하다.

## @Configuration 
#### @Configuration 동작원리
- 기존 Bean의 생성 과정
    - <XML, Property File, DB> -> BeanDefinition -> Bean Instance
    - BeanDefinitionReader, BeanDefinitionParser를 통함
- Bean 생성의 후처리기
    - BeanFactoryPostProcessor
        - Bean 메타정보 를 상대로 후처리를 진행
        - BeanDefinition(빈 설정 메타정보)
        - Ex. PropertyPlaceholderConfigurer
            - 해당 후처리기가 $xxx 정보를 실제 프로퍼티 파일 정보로 세팅한다.
    - BeanPostProcessor
        - Bean이 만들어진 상태에서 후처리를 진행
- BeanDefinitionRegistryPostProcessor (BeanFactoryPostProcessor 단계의 확장)
    - 아예 새로운 빈 등록도 가능하지 않을까?
    - 후처리기를 통해서 새로운 빈을 등록한다.
    - Resource - Reader - Definition의 틀을 깬다.
- ConfigurationClassPostProcessor
    - BeanDefinitionRegistryPostProcessor의 확장
    - @Configuration 클래스 정보를 이용해서 새로운 빈 메타 정보 생성
        - @Bean 메소드
        - Annotation 메타 정보
- XML에서 ConfigurationClassPostProcessor 후처리기 생성하기
    - @Configuration 으로 등록된 내용을 해석하여 Bean을 생성한다.
    - method들을 찾아서 @Bean이 붙어 있으면 해석하여 스프링 Bean으로 등록한다.
    - AnnotationConfigApplicationContext 는 해당 후처리기를 내장하고 있다.
        - Ex) new AnnotationConfigApplicationContext(Config.class);
    ~~~
    <context:annotation-config> 를 추가한다.
    ~~~

#### @Configuration 빈 등록 방법
- ConfigurationClassPostProcessor
    - XML \<bean\>
    - ComponentScan
- AnnotationConfigApplicationContext 직접 호출
- AnnotationConfigWebApplicationContext 직접 호출
    - DispacherServlet의 init-param 변경 (WEB.XML)
        - contextClass = AnnotationConfigWebApplicationContext
        - contextConfigLocation = enable.config

#### @Configuration 에 사용하는 Annotation
- 3.0
    - @ImportResource
    - @ImportResource("classpath:context.xml")
    - @Import
- 3.1
    - @ComponentScan
        - XML의 component-scan 과 매칭
        - @ComponentScan(basePackages= {"com.dasolute.simons"}, excludeFilters= {@Filter(Configuration.class)})
    - @PropertySource
        - @PropertySource("classpath:database.properties")
    - @Enable*

#### 특징
~~~java
public class Test {
    public static void main(String[] args) {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(HelloConfig.class);

        // hello1, hello2 는 동일한 인스턴스이다.
        Hello hello1 = ac.getBean(Hello.class).getHello();
        Hello hello2 = ac.getBean(HelloConfig.class).hello();
    }
}
~~~

- @Bean이 붙은 메소드는 동일한 instance를 리턴한다.
- @Configuration 의 @Bean method는 new 객체를 리턴하지만 싱글톤으로 유지된다.
- 싱글톤으로 유지되는 원리는 CGLIB 라는 것을 통해서 자바 바이너리를 Enhancement(변경)한다. 
    - ConfigurationClass$HelloConfig$$EnhancerByCGLIB
- @Configuration Bean은 스프링 Bean이 할 수 있는 기능을 모두 소화할 수 있다. (그 자체로써 Bean이다.)
- @Bean이 붙은 메소드는 private이면 안되고 final이 붙으면 안된다.
    - CGLIB이 처리를 할 수 없다.

#### @Configuration 단점
- @Bean이 붙으면 모두 Spring Bean으로 등록한다.
- 선별적으로 Bean들을 서로 다르게 구성할 수가 없다.

#### @Configuration 설정 확장 방법
##### Configuration 확장 (비추)
- class AppConfig extends BaseConfig + @Override
- 이 방식은 상속이 가지는 모든 단점을 가진다. 결합도가 높아진다.
##### @Import + Metadata/Registry
- @ImportAware 어노테이션, ImportSelector 인터페이스, ImportBeanDefinitionRegistrar 인터페이스
- 다른 Configuration 을 가져올 수 있다.
- 확장포인트
    - ImportAware 인터페이스를 통해서 확장한다.
- 보통은 @Import (A.class, B.class) 로 바로 접근하지 않고 @EnableXXX로 접근한다.
~~~java
    @Configuration
    @EnableHello // Import를 좀 더 명시적으로 사용하는 방법
    //@EnableHello("Spring") // ImportAware 예제 케이스 (옵션 정보를 넘기는 방법)
    //@EnableHello(type=1) // ImportSelector 예제 케이스 (설정정보의 분기를 처리하는 방법)
    //@EnableHello(type=1) // ImportBeanDefinitionRegistrar 예제 케이스 (커스터마이징 방법이 가장 높은 방법 - @Bean 사용대신 직접 Bean 설정)
    public class AppConfig {

    }

    //1) @Import(HelloConfig.class) // 특정 Configuration을 지정해서 Import한다.
    //2) @Import(HelloSelector.class) // Import할 Configuration을 판단하는 Selector를 Import한다.
    //2) @Retention(RetentionPolicy.RUNTIME) // 해당 어노테이션이 언제까지 살아있는가? Default Retention은 RetentionPolicy.CLASS이다.
    //2) (CLASS) JVM에 클래스가 로딩될때는 사라진다. - 클래스 시점에만 정보가 있는데 예제는 Runtime 시점에 정보를 알고 있어야 하므로 RUNTIME으로 변경해야한다.
    //2) 제일 오래 살아남는게 RetentionPolicy.RUNTIME이므로 대부분 케이스가 RetentionPolicy.RUNTIME 으로 설정한다.
    //3) @Import(HelloImportBeanDefinitionRegistrar.class) // 
    @interface EnableConfig {
        // 속성을 줄 수 있는 방법 (ImportAware)
        // String name();

        // type에 따른 Import 분기처리 (ImportSelector)
        // int type;
    }

    // ImportAware 를 통한 확장 방법
    @Configuration
    class HelloConfig implements ImportAware {
        @Bean
        Hello hello() {
            return new Hello("Test");
        }

        @Autowired
        Hello hello; // 위의 @Bean 에 생성한 bean을 Autowired 한다.

        @Override
        public void setImportMetadata(AnnotationMetadata importMetadata) {
            // Import 해서 HelloConfig를 Bean으로 등록하는 시점에
            // AnnotationMetadata란 해당 HelloConfig 를 최초로 가져오게된 시점의 클래스, 메타데이터 정보 담는다.
            // 해당 샘플코드에서는 AppConfig의 @EnableHello("Spring") 선언 정보이다.
            // 즉 Enable에 설정된 옵션을 적용해서 확장할 수 있다.
            String name = (String) importMetadata.getAnnotationAttributes(EnableHello.class.getName()).get("name");
            hello.setName(name);
        }
    }

    // ImportSelector 를 통한 확장 방법 
    // @Configuration이 안붙어있다. 조건만 정의하면 되기 때문에 Bean이 될 필요가 없다.
    class HelloSelector implements ImportSelector {
        @Override
        public String[] selectImports(AnnotationMetadata importingClassMetadata) {
            // AnnotationMetadata를 참조해보니 경우에 따라 Configuration을 선별적으로 Import 시키면 된다를 풀어낸다.
            // 하나 이상의 Configuration을 리턴한다.
            Intger name = (Integer) importingClassMetadata.getAnnotationAttributes(EnableHello.class.getName()).get("type");
            if (type == 1) {
                return new String[] {HelloConfig1.class.getName()};
            } else {
                return new String[] {HelloConfig2.class.getName()};
            }
        }
    }

    // 옵션에 따라 등록되는 빈의 종류와 수가 복잡한 방식으로 변경된다면 고려해볼만하다.
    public class HelloImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar {

        public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry) {
            // BeanDefinitionRegistry - 빈 정의를 코드로 만들어서 등록할 수 있는 방법을 제공한다.
            // BeanDefinition을 통해서 hello bean을 정의한다.
            // 해당 기술을 사용해서 좀더 구체적으로 Bean들을 설정할 수 있다.
            BeanDefinition beanDefinition = new RootBeanDefinition(Hello.class); // Bean 하나를 정의하는 방법
            beanDefinition.getPropertyValues().addPropertyValue("name", "TEST");
            registry.registerBeanDefinition("hello", beanDefinition);

            
        }
    }
~~~
##### Configuration + 자바코드 (Configurer)
- 개념적으로 보면 3.1 새로운 스펙이 아니고 기존 자바를 활용한 방법이다.
- EnableWebMvc 의 WebMvcConfigurer 가 해당 스타일로 작성이 되어 있다.
- WebMvcConfigurer[] configurers; 으로 선언해서 Collection 형태로 받을 수 있다. 
~~~java
    @Configuration
    @EnableHello
    public class AppConfig implements NameConfigurer {
        @Override
        public void configure(Hello hello) {
            hello.setName("END");
        }
    }

    @Import(HelloConfig.class)
    @interface EnableConfig {
    }
    
    // Configurer
    interface NameConfigurer {
        Hello configure(Hello hello);
    }

    @Configuration
    class HelloConfig {
        // AppConfig Bean이 주입이 된다. @Configuration도 Bean이다.
        @Autowired 
        NameConfigurer configurer; 

        @Bean
        Hello hello() {
            return configurer.configure(new Hello("TEST"));
        }
    }
~~~

## 용어정리
#### @Configuration
 - @Bean 들을 정의한 Java Config 스펙의 제일 기본이 되는 설정파일
 
#### @Bean
 - 스프링 Bean이 되는 정보
 - 싱글톤을 유지한다.

#### @EnableXXX
 - 직접적으로 @Import(A.class, B.class) 로 명시하지 않고 확장하는 스프링에서 가이드하는 방법
 - @EnableXXX 는 내부적으로 @Import를 수반한다.
 - @Configutaion 이 붙은 곳에 같이 붙인다.
 - Sample
    - @EnableWebMvc = <mvc:*>
    - @EnableAsync = <task:*>
    - @EnableScheduling = <task:*>
    - @EnableCaching = <cache:*>
    - @EnableTransactionManagement = <tx:*>

#### Import Annotation
 - 다른 @Configuration 클래스를 Import 하기 위한 어노테이션이다.
 - Spring XML 에서는 <import /> 와 동일하다.

#### ImportAware
 - 다른 설정(@Configuration) 을 가져와서 쓰면서 확장까지 할 수 있다.
 - AnnotationMetadata로 케이스별 옵션값을 받아 @Bean 생성 시 추가적인 정보를 세팅한다.
 - 위의 예제 설정

#### ImportSelector
 - AnnotationMetadata로 케이스별 옵션값을 받아 설정정보를 분기한다.

#### ImportBeanDefinitionRegistrar
 - @Bean 사용대신 직접 Bean 설정
 - 커스터마이징 레벨이 가장 높다.
 - BeanDefinition 과 BeanDefinitionRegistry 를 직접 코딩하여 Bean을 생성한다.

## Reference
 - [토비님의 교육자료 - 3.0 -> 3.1로 변화](https://open.egovframe.go.kr/projects/information/filearchive/4977/689/)
 - [강추!!! 스프링 3.1의 @Enable 기법을 활용한 설정 모듈화의 재사용 기법 - 토비님](http://olc.kr/course/course_online_view.jsp?id=209&cid=&s_style=webzine&s_listnum=12&scid=&s_field=&s_keyword=%EC%8A%A4%ED%94%84%EB%A7%81) 
 
