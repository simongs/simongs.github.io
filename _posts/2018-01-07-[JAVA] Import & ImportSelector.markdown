---
layout: post
title: "[JAVA] Import & ImportSelector"
date:   2018-01-07 09:00:00 +0900
categories: JAVA SPRING
---

* content
{:toc}

해당 자바코드의 소스는 Spring 4.3.13 Version 의 소스입니다.
기본 골격이 되는 인터페이스 및 어노테이션 정보들로 잦은 변화는 적은 소스입니다.

# Import Interface

 - 가끔 스프링 소스를 보다보면 반복되는 주요 용어들이 등장할 때가 있다.
 - 이런 용어들에 대한 의미를 알고 소스를 읽기 시작하면 프레임워크를 이해하는데 도움이 된다.
 - 그 중 Aware 로 끝나는 애들의 역할에 대해 정리하고자 한다.

## Description
 - @Configuration 클래스를 Import 하기 위한 어노테이션이다.
 - Spring XML 에서는 <import /> 와 동일하다.

## Java Code
~~~java
package org.springframework.context.annotation;
/**
 * Indicates one or more {@link Configuration @Configuration} classes to import.
 *
 * <p>Provides functionality equivalent to the {@code <import/>} element in Spring XML.
 * Allows for importing {@code @Configuration} classes, {@link ImportSelector} and
 * {@link ImportBeanDefinitionRegistrar} implementations, as well as regular component
 * classes (as of 4.2; analogous to {@link AnnotationConfigApplicationContext#register}).
 *
 * <p>{@code @Bean} definitions declared in imported {@code @Configuration} classes should be
 * accessed by using {@link org.springframework.beans.factory.annotation.Autowired @Autowired}
 * injection. Either the bean itself can be autowired, or the configuration class instance
 * declaring the bean can be autowired. The latter approach allows for explicit, IDE-friendly
 * navigation between {@code @Configuration} class methods.
 *
 * <p>May be declared at the class level or as a meta-annotation.
 *
 * <p>If XML or other non-{@code @Configuration} bean definition resources need to be
 * imported, use the {@link ImportResource @ImportResource} annotation instead.
 *
 * @author Chris Beams
 * @author Juergen Hoeller
 * @since 3.0
 * @see Configuration
 * @see ImportSelector
 * @see ImportResource
 */
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface Import {

	/**
	 * {@link Configuration}, {@link ImportSelector}, {@link ImportBeanDefinitionRegistrar}
	 * or regular component classes to import.
	 */
	Class<?>[] value();

}
~~~

# ImportAware

## Descrition

## Java Code
~~~

~~~


# ImportSelector

## Description

## Java Code
~~~java
package org.springframework.context.annotation;

/**
 * Interface to be implemented by types that determine which @{@link Configuration}
 * class(es) should be imported based on a given selection criteria, usually one or more
 * annotation attributes.
 *
 * <p>An {@link ImportSelector} may implement any of the following
 * {@link org.springframework.beans.factory.Aware Aware} interfaces, and their respective
 * methods will be called prior to {@link #selectImports}:
 * <ul>
 * <li>{@link org.springframework.context.EnvironmentAware EnvironmentAware}</li>
 * <li>{@link org.springframework.beans.factory.BeanFactoryAware BeanFactoryAware}</li>
 * <li>{@link org.springframework.beans.factory.BeanClassLoaderAware BeanClassLoaderAware}</li>
 * <li>{@link org.springframework.context.ResourceLoaderAware ResourceLoaderAware}</li>
 * </ul>
 *
 * <p>ImportSelectors are usually processed in the same way as regular {@code @Import}
 * annotations, however, it is also possible to defer selection of imports until all
 * {@code @Configuration} classes have been processed (see {@link DeferredImportSelector}
 * for details).
 *
 * @author Chris Beams
 * @since 3.1
 * @see DeferredImportSelector
 * @see Import
 * @see ImportBeanDefinitionRegistrar
 * @see Configuration
 */
public interface ImportSelector {

	/**
	 * Select and return the names of which class(es) should be imported based on
	 * the {@link AnnotationMetadata} of the importing @{@link Configuration} class.
	 */
	String[] selectImports(AnnotationMetadata importingClassMetadata);

}
~~~

# ImportBeanDefinitionRegistrar

## JavaCode

~~~java
package org.springframework.context.annotation;

/**
 * Interface to be implemented by types that register additional bean definitions when
 * processing @{@link Configuration} classes. Useful when operating at the bean definition
 * level (as opposed to {@code @Bean} method/instance level) is desired or necessary.
 *
 * <p>Along with {@code @Configuration} and {@link ImportSelector}, classes of this type
 * may be provided to the @{@link Import} annotation (or may also be returned from an
 * {@code ImportSelector}).
 *
 * <p>An {@link ImportBeanDefinitionRegistrar} may implement any of the following
 * {@link org.springframework.beans.factory.Aware Aware} interfaces, and their respective
 * methods will be called prior to {@link #registerBeanDefinitions}:
 * <ul>
 * <li>{@link org.springframework.context.EnvironmentAware EnvironmentAware}</li>
 * <li>{@link org.springframework.beans.factory.BeanFactoryAware BeanFactoryAware}
 * <li>{@link org.springframework.beans.factory.BeanClassLoaderAware BeanClassLoaderAware}
 * <li>{@link org.springframework.context.ResourceLoaderAware ResourceLoaderAware}
 * </ul>
 *
 * <p>See implementations and associated unit tests for usage examples.
 *
 * @author Chris Beams
 * @since 3.1
 * @see Import
 * @see ImportSelector
 * @see Configuration
 */
public interface ImportBeanDefinitionRegistrar {

	/**
	 * Register bean definitions as necessary based on the given annotation metadata of
	 * the importing {@code @Configuration} class.
	 * <p>Note that {@link BeanDefinitionRegistryPostProcessor} types may <em>not</em> be
	 * registered here, due to lifecycle constraints related to {@code @Configuration}
	 * class processing.
	 * @param importingClassMetadata annotation metadata of the importing class
	 * @param registry current bean definition registry
	 */
	public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry);

}
~~~