---
layout: post
title: "[SPRING-BOOT] Spring-Boot Profile"
date:   2016-10-01 17:45:49 +0900
categories: spring-boot 
---

# SPRING-BOOT PROFILE

## 2 ways to configure eacch profile constants
#### 1. application-{phase}.properties
JVM 옵션인 spring.profiles.active 의 정보를 {phase}에 대입하여 파일을 구성한다.
~~~
ex. application-local.properties
logging.level.org.springframework=DEBUG
~~~

~~~
@Component
public class ApplicationConstants {

    @Value("${logging.level.org.springframework}")
    private String loggingLevel;

    public String getLoggingLevel() {
        return loggingLevel;
    }
}
~~~

#### 2. {keyword}.yml (Editing...)
이 부분은 조금더 확인이 필요하다.
~~~
ex. fixtures.yml
fixtures:
  articles:
    -
      id: 1
      title: title1
      content: content1
~~~

~~~
@Component
@EnableConfigurationProperties
@ConfigurationProperties(locations = {"fixtures.yml"}, prefix = "fixtures")
public class FixturesProperty {
    @NestedConfigurationProperty
    private List<Map> articles = new ArrayList<>();

    public List<Map> getArticles() {
        return articles;
    }
}
~~~


## Reference
[Reference Official Document](http://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-profiles.html)
[JDM's Blog - SpringBoot Profile](http://jdm.kr/blog/200) 

## Execute Configuration (in intellij)

### 
Add VMOptions Parameter
~~~
-Dspring.profiles.active=local
~~~

![2016-10-05 09;26;13.PNG](https://s3-ap-northeast-1.amazonaws.com/torchpad-production/wikis/5790/m1MNdCh1R3iTmUY7POZV_2016-10-05%2009;26;13.PNG)
