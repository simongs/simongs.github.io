---
layout: post
title: "[JAVA] @Conditional Annotation  "
date:   2018-01-04 09:00:00 +0900
categories: JAVA SPRING
---

#### ▣ 동작원리

#### ▣ @Conditional Annotation
~~~java
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.TYPE, ElementType.METHOD})
public @interface Conditional {

	/**
	 * All `Condition`s that must `Condition#matches` match in order for the component to be registered.
	 */
	Class<? extends Condition>[] value();

}
~~~

#### ▣ Condition Interface
~~~java
public interface Condition {

	/**
	 * Determine if the condition matches.
	 */
	boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata);
}
~~~

#### ▣ @Conditional 종류

##### @ConditionalOnMissingBean

##### @ConditionalOnBean

##### @ConditionalOnProperty

##### @ConditionalOnProperty

##### @ConditionalOnClass

##### @ConditionalOnMissingClass