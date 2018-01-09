---
layout: post
title: "[JAVA] @Conditional"
date:   2018-01-11 09:00:00 +0900
categories: JAVA SPRING
---

## @Conditional

#### XML 방식
 - 정적인 구조
 - 동적스캔지원 (어노테이션)
 - 커스텀 태그의 구현에 따른 일부 동적인 설정 (aop, transaction)
 
#### JavaConfig, Annotation
 - 동적인 빈 등록, 설정
 - @Bean
 - @Enable*
 - @Profile

#### @Enable*
 - @Configuration 단위 구성
 - @Import 활용
 - 해당 이름은 스프링 소스에서 관례적으로 붙은 이름
  - 확장방법
    - @Configuration 오버라이딩
    - @Import + Metadata/Registry
    - @Configuration + 자바코드 Configurer

#### @Profile
 - 빈 구성 그룹을 선택할 수 있도록 한다.
 - 환경에 따라 다르게 적용되는 빈 구성이 가능하다.

#### @Conditional
 - @Profile과 같이 특정 조건을 만족했을때 빈을 등록하는 기능을 일반화
 - 적용대상
    - @Configuration
    - @Bean
    - 스테레오 타입 어노테이션
 - 세밀하고 동적인 설정 구성 가능
 - Spring Boot 에서 적극적으로 사용한다.
    - Spring Boot 에 적용되었던 기술이 Spring 4 로 들어온 케이스

#### @Conditional 적용 구조
 - Spring은 @Conditional 의 결과를 보고 Bean 등록여부를 판단한다.
 - @Configuration Level에도 붙일수 있다. (해당 Configuration 하위의 Bean이 모두 대상이 된다.)

▶ 적용대상
~~~java
@Bean
@OnFlag(true)               // 확장1 
@Conditional(MyCondition.class) // 확장1 
@MyConditional(true)        // 확장2 
@MyConditional(BeanB.class) // 확장3
public BeanA bean() {
    return new BeanA();
}
~~~

▶ 판단방법
~~~java 
public class MyCondition implements Condition {
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        
        // 확장1. return (Boolean)metadata.getAnnotationAttributes(OnFlag.class.getName()).get("value");
        // 확장2. return (Boolean)metadata.getAnnotationAttributes(MyConditional.class.getName()).get("value");
        // 확장3. Class<?> beanClass = (Class<?>)metadata.getAnnotationAttributes(MyConditional.class.getName()).get("value");
        //        try {
        //            BeanFactoryUtils.beanOfTypeIncludingAncestors(context.getBeanFactory(), beanClass);
        //            return true;  
        //        } catch(NoSuchBeanDefinitionException e) {
        //            return false;
        //        }
    }
}
~~~

▶ (확장1) OnFlag annotation 개발
~~~java
@Retention(RetentionPolicy.RUNTIME) // 이 annotation이 언제까지 적용될 것인가? Default는 Class
public @interface OnFlag {
    boolean value();
}
~~~

▶ (확장2) MyConditional annotation 개발 (OnFlag 를 대체한다.)
~~~java
@Retention(RetentionPolicy.RUNTIME) 
@Conditional(MyCondition.class) // Meta Annotation을 정의한다.
public @interface MyConditional {
    boolean value();
}
~~~

▶ (확장3) MyConditional annotation 개발 (특정 Bean이 있을때만 등록한다.)
~~~java
@Retention(RetentionPolicy.RUNTIME) 
@Conditional(MyCondition.class) // Meta Annotation을 정의한다.
public @interface MyConditional {
    Class<?> value();
}
~~~

#### Condition Interface
▶ method code
~~~java
public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
     
}
~~~

▶ ConditionContext
 - BeanFactory 등등 정보를 가짐

▶ AnnotatedTypeMetadata
 - @Conditional 이 붙은 위치의 모든 annotation 정보를 가져온다. 
    - 위의 예제는 @Bean, @OnFlag, @Conditional 정보를 담고 있다.
    - RetentionPolicy.RUNTIME 만 실제 값을 가져올 수 있다.

#### Condition 활용예제
 - @Profile
 - Spring Boot 프로젝트의 AutoConfiguration 
    - OnBeanCondition : bean이 있느냐 없느냐
    - OnClassCondition : classpath에 있으냐 없느냐
    - OnExpressionCondition : SPEL 에 매칭이 되느냐 
    - OnJavaCondition : Java Version 에 따라
    - OnPropertyCondition : Property Value 에 따라
    - OnResourceCondition : Resource의 존재유무
    - OnWebApplicationCondition : 현재 WEB 기반의 어플리케이션인지 판단한다.

#### AutoConfiguration
 - @Configuration 과 @Conditional 의 조합이다.
 - 각종 조건 및 환경에 맞는 빈 구성 및 설정 정보
 - Default Bean 구성으로 빠르게 시작해서 점진적으로 설정을 추가 확장
    - @ConditionalOnMissingBean
        - 개발자가 직접 등록한 빈이 있으면 사용하고 없으면 Default Bean을 등록한다.
 - SpringBoot의 다양한 적용방법 참고
 - @Enable* 조합
 
#### 클래스 프록시 개선 
 - 기존 클래스 프록시 방식의 단점
    - 클래스 생성자 2번 호출 (proxy + target)
    - 디폴트 생성자만 사용가능 (CGLib)
 - 개선된 클래스 프록시
    - 클래스 생성자 1번만 호출
    - 임의의 생성자 사용 가능
    - Objenesis와 확장된 CGLib



##### Reference
 - [스프링4와 자바8 도입전략](http://olc.kr/course/course_online_view.jsp?id=437)
