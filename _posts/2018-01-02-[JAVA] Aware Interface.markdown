---
layout: post
title: "[JAVA] Aware Interface"
date:   2018-01-02 09:00:00 +0900
categories: JAVA SPRING
---

# Aware Interface

 - 가끔 스프링 소스를 보다보면 반복되는 주요 용어들이 등장할 때가 있다.
 - 이런 용어들에 대한 의미를 알고 소스를 읽기 시작하면 프레임워크를 이해하는데 도움이 된다.
 - 그 중 Aware 로 끝나는 애들의 역할에 대해 정리하고자 한다.

## Description
 - Marker 인터페이스이다.
 - Callback-Stype의 메소드를 정의한다.
 - Spring Framework Container 가 Bean 생성 시점에 해당 객체를 주입해준다.

## Inherit Class
 - BeanNameAware : Bean의 이름을 주입해준다.
 - ApplicationContextAware : ApplicationContext 객체를 주입해준다.

## Java Code
~~~java
package org.springframework.beans.factory;

/**
 * Marker superinterface indicating that a bean is eligible to be
 * notified by the Spring container of a particular framework object
 * through a callback-style method. Actual method signature is
 * determined by individual subinterfaces, but should typically
 * consist of just one void-returning method that accepts a single
 * argument.
 *
 * <p>Note that merely implementing {@link Aware} provides no default
 * functionality. Rather, processing must be done explicitly, for example
 * in a {@link org.springframework.beans.factory.config.BeanPostProcessor BeanPostProcessor}.
 * Refer to {@link org.springframework.context.support.ApplicationContextAwareProcessor}
 * and {@link org.springframework.beans.factory.support.AbstractAutowireCapableBeanFactory}
 * for examples of processing {@code *Aware} interface callbacks.
 *
 * @author Chris Beams
 * @since 3.1
 */
public interface Aware {

}
~~~



