---
layout: post
title: "[JAVA] Servlet 3.X 와 Java 기반 설정"
date:   2018-01-17 09:00:00 +0900
categories: JAVA SPRING
---

* Table of Contents
{:toc}

## Servlet
 - Servlet 3.0 이상부터 Java Based 설정을 제공한다.
 - web.xml 을 이용한 설정도 가능
 - web.xml 없이 자바코드만으로도 설정 가능
 - web.xml 은 ServletContext Object를 초기화하는데 사용하던 메타정보

## javax.servlet.ServletContainerInitializer
 - /META-INF/services/javax.servlet.ServletContainerInitializer 라는 텍스트 파일로 존재한다.
 - 해당 파일안에는 구현클래스를 지정하게 된다.
 - web.xml이라는 표준 설정 파일을 없애므로써 클래스패스내의 기반 설정이 되어야 하는 파일을 찾아야 하는데 스캐닝 비용이 부담스러웠다.
 - 그래서 명시적인 Text File로 설정 정보를 가지고 있는 구현클래스 정보를 담는다.
 - 구현클래스에 @HandlesTypes 를 사용하면 @HandlesTypes 안에 지정한 타입의 클래스를 모조리 찾아서 onStartup()의 파라미터로 넣어준다

## Spring에서 구현한 클래스 정보
### Text File 
in /META-INF/services/javax.servlet.ServletContainerInitializer
~~~
org.springframework.web.SpringServletContainerInitializer
~~~

### SpringServletContainerInitializer
- spring-web 컴포넌트에 존재한다.
- WebApplicationInitializer Type의 클래스를 모조리 찾아서 SpringServletContainerInitializer의 onStartUp() 으로 전달한다.
- SpringServletContainerInitializer는 직접적으로 ServletContext에 무언가를 등록하지 않는다.
- 모두 WebApplicationInitializer에 위임한다.
- @Order로 실행순서를 조정하는 역할을 한다.

~~~java
package org.springframework.web;

@HandlesTypes(WebApplicationInitializer.class)
public class SpringServletContainerInitializer implements ServletContainerInitializer {

	@Override
	public void onStartup(Set<Class<?>> webAppInitializerClasses, ServletContext servletContext)
			throws ServletException {

		List<WebApplicationInitializer> initializers = new LinkedList<WebApplicationInitializer>();

		if (webAppInitializerClasses != null) {
			for (Class<?> waiClass : webAppInitializerClasses) {
				// Be defensive: Some servlet containers provide us with invalid classes,
				// no matter what @HandlesTypes says...
				if (!waiClass.isInterface() && !Modifier.isAbstract(waiClass.getModifiers()) &&
						WebApplicationInitializer.class.isAssignableFrom(waiClass)) {
					try {
						initializers.add((WebApplicationInitializer) waiClass.newInstance());
					}
					catch (Throwable ex) {
						throw new ServletException("Failed to instantiate WebApplicationInitializer class", ex);
					}
				}
			}
		}

		if (initializers.isEmpty()) {
			servletContext.log("No Spring WebApplicationInitializer types detected on classpath");
			return;
		}

		servletContext.log(initializers.size() + " Spring WebApplicationInitializers detected on classpath");
		AnnotationAwareOrderComparator.sort(initializers);
		for (WebApplicationInitializer initializer : initializers) {
			initializer.onStartup(servletContext);
		}
	}
}

~~~
### Reference
  - [Spring Java Base Configuration](http://hashmap27.tistory.com/6?category=664045)
  - [WebApplicationInitializer을 이용한 컨텍스트 초기화](http://toby.epril.com/?tag=webapplicationinitializer)