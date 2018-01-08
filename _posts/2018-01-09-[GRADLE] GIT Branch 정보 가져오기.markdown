---
layout: post
title: "[BUILD] GIT Branch 정보 가져오기."
date:   2018-01-09 09:00:00 +0900
categories: JAVA GRADLE
---

#### GRADLE PLUG-IN
 - 해당 플러그인은 build 시점에 git.properties 파일을 만들어냅니다.
 - build를 수행하는 환경에 따라 File의 encoding도 변합니다. (EUC-KR, UTF-8)
 - 프로젝트의 /src/main/resources 폴더에 해당 파일을 생성한다.

##### git.properties 샘플
~~~
#
#Mon Jan 08 22:58:29 KST 2018
git.commit.id=29034898348cv0e051cc6fb7322b8d55cd939733
git.commit.time=1515347499
git.commit.user.name=SIMONGS
git.commit.id.abbrev=cd939733
git.branch=develop
git.commit.message.short=테스트 SHORT 메시지
git.commit.user.email=chopokmado@gmail.com
git.commit.message.full=테스트 SHORT 메시지
~~~


##### 적용방법
~~~
buildscript {
    ext {
        springBootVersion = '1.2.3.RELEASE'
    }
    repositories {
        mavenCentral()
        maven {
            url "https://plugins.gradle.org/m2/"  //gradle 플러그인 URL
        }
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
        classpath "gradle.plugin.com.gorylenko.gradle-git-properties:gradle-git-properties:1.4.11" // gradle-git-properties
    }
}

apply plugin: "com.gorylenko.gradle-git-properties" // 단일 프로젝트에 적용시키는 방법
~~~

##### 특정 SubProject 에서만 플러그인 적용하기 (멀티프로젝트)
Parent (build.gradle)
ㄴboot-core (build.gradle) <= git.properties 를 생성하고 싶은 위치
ㄴboot-admin (build.gradle)
ㄴboot-api (build.gradle)

위와 같이 프로젝트가 멀티 프로젝트로 구성되어 있다고 가정한다.
Parent (build.gradle) 에 아래와 같이 특정 subproject일 때만 플러그인이 동작하도록 설정한다.

~~~
// create git.properties into dongle-core project
subprojects { subproject ->
    if (subproject.name.startsWith("dongle-core")) {
        apply plugin: "com.gorylenko.gradle-git-properties"
    }
}
~~~

##### GitProperties.java 에 프로퍼티 설정하기

▶ Case 1) PropertySourcesPlaceholderConfigurer 를 통한 프로퍼티 주입방법

~~~java
@Configuration
public class CommonConfig {

    @Bean
    public static PropertySourcesPlaceholderConfigurer gitPropertiesPlaceholderConfigurer() {
        PropertySourcesPlaceholderConfigurer propsConfig = new PropertySourcesPlaceholderConfigurer();
        propsConfig.setLocation(new ClassPathResource("git.properties"));
        propsConfig.setIgnoreResourceNotFound(true);
        propsConfig.setIgnoreUnresolvablePlaceholders(true);
        propsConfig.setFileEncoding("UTF-8"); // 파일이 만들어지는 환경에 따라 인코딩을 다르게 해야할 수 있다.
        return propsConfig;
    }
}
~~~

##### GitProperties.java Sample
~~~java
@Component
public class GitProperties extends BaseObject {
    private static final long serialVersionUID = -5810903077653462368L;

    @Value("${git.branch}")
    private String gitBranch;

    @Value("${git.commit.message.short}")
    private String lastCommitMessage;

    @Value("${git.commit.user.email}")
    private String lastCommitUser;

    public String getLastCommitMessage() {
        return lastCommitMessage;
    }

    public String getLastCommitUser() {
        return lastCommitUser;
    }

    /** git.branch=origin/develop 와 같은 값이 남음 */
    public String getGitBranch() {
        if (StringUtils.isBlank(gitBranch)) {
            return "develop";
        }

        return StringUtils.contains(gitBranch, "/") ? StringUtils.substringAfterLast(gitBranch, "/") : gitBranch;
    }
}
~~~

##### Reference
 - [[스프링부트] 빌드시 깃 커밋버전 정보 포함시키기](https://gist.github.com/ihoneymon/e1479fe11776eb545ac6)