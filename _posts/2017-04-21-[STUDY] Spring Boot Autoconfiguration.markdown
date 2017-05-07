---
layout: post
title: "[STUDY] Spring Boot Auto configuration"
date:   2017-04-19 17:45:49 +0900
categories: etc 
---

## Spring Boot Autoconfiguration

### @SpringBootApplication
~~~java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@Configuration
@EnableAutoConfiguration
@ComponentScan
public @interface SpringBootApplication {

	/**
	 * Exclude specific auto-configuration classes such that they will never be applied.
	 * @return the classes to exclude
	 */
	Class<?>[] exclude() default {};

}
~~~

### EnableAutoConfiguration
자동설정 관련한 어노테이션
특정 기준의 설정클래스를 로드

### @Enable* 어노테이션
3.0에서는 잘 사용하지 않았다.
자바 Configuration 을 잘 사용하지 않았었다.
3.1로 넘어오면서 사용빈도가 높아진다.

예를 들어 EnableWebMvc 같은 어노테이션이 있다.
EnableAspectJAutoProxy

@Import 와 함께 자신만의 모듈을 만들수 있다.

### @EnableAutoConfiguration

스프링부트의 설정의 시작

~~~java
@Import({ EnableAutoConfigurationImportSelector.class,
		AutoConfigurationPackages.Registrar.class })
public @interface EnableAutoConfiguration {

	/**
	 * Exclude specific auto-configuration classes such that they will never be applied.
	 * @return the classes to exclude
	 */
	Class<?>[] exclude() default {};
}
~~~

### @EnableAutoConfigurationImportSelector



제일 핵심인 코드

DeferredImportSelector 이 어노테이션을 통해서 설정정보를 가져온다.

~~~java
@Order(Ordered.LOWEST_PRECEDENCE)
class EnableAutoConfigurationImportSelector implements DeferredImportSelector, BeanClassLoaderAware, ResourceLoaderAware {

	private ClassLoader beanClassLoader;

	private ResourceLoader resourceLoader;

    /*
    * 문자열 배열의 의미는 package와 class명 정보를 가져온다.
    */
	@Override
	public String[] selectImports(AnnotationMetadata metadata) {
		try {
			AnnotationAttributes attributes = AnnotationAttributes.fromMap(metadata.getAnnotationAttributes(EnableAutoConfiguration.class.getName(), true));

			// Find all possible auto configuration classes, filtering duplicates
			List<String> factories = new ArrayList<String>(new LinkedHashSet<String>(SpringFactoriesLoader.loadFactoryNames(EnableAutoConfiguration.class, this.beanClassLoader)));

			// Remove those specifically disabled
			factories.removeAll(Arrays.asList(attributes.getStringArray("exclude")));

			// Sort
			factories = new AutoConfigurationSorter(this.resourceLoader).getInPriorityOrder(factories);

			return factories.toArray(new String[factories.size()]);
		}
		catch (IOException ex) {
			throw new IllegalStateException(ex);
		}
	}

	@Override
	public void setBeanClassLoader(ClassLoader classLoader) {
		this.beanClassLoader = classLoader;
	}

	@Override
	public void setResourceLoader(ResourceLoader resourceLoader) {
		this.resourceLoader = resourceLoader;
	}

}

~~~

### spring.factories
설정리스트의 보관소
스프링 개발자들이 만들어놓은 노가다 작업

/META-INF/spring.factories 파일에 이미 정의 되어있다.
해당 파일은 boot-Autoconfiguration.jar 파일에 존재한다.

모든 클래스는 로딩이 되나 스프링 컨테이너에 올라가지는 않는다.
메모리에 올라가면 상당이 느려질 수 있다.
@Conditional 이라는 옵션을 통해서 조건적으로 Bean에 등록한다.
Spring 4.x.x 에서부터 지원하기 시작하는 어노테이션
그래서 스프링은 4.x.x 대부터 스프링부트를 사용할 수 있다.

~~~
# Initializers
org.springframework.context.ApplicationContextInitializer=\
org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
org.springframework.boot.autoconfigure.logging.AutoConfigurationReportLoggingInitializer

# Application Listeners
org.springframework.context.ApplicationListener=\
org.springframework.boot.autoconfigure.BackgroundPreinitializer

# Auto Configure
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
~~~

### @Conditional 예제
특정 Bean을 등록할 때 특정 환경에서만 등록하고 싶다.

~~~
@Bean
@Conditional(Phase.class)
public CommandLineRunner .... 

class Phase implements Condition {
    public boolean match
}

~~~

### @Profile 어노테이션
대표적인 Conditional 에 대한 예제

### 참고 URL

- [DEEP DIVE INFO SPRING BOOT AUTO..](https://www.youtube.com/watch?v=ssT24xB9UTc)