---
layout: post
title: "[BUILD] GIT Branch 정보 가져오기."
date:   2017-06-25 09:00:00 +0900
categories: JAVA MAVEN 
---

* Table of Contents
{:toc}

## MAVEN에서 GIT Branch 정보 가져오는 법

#### MAVEN PLUG-IN

URL : https://github.com/ktoso/maven-git-commit-id-plugin

##### pom.xml 수정
 - 프로젝트 내부의 Resource 파일 중에 {git.branch} 같은 정의된 문자열을 치환한다.

~~~xml
<project>

    <pluginRepositories>
        <pluginRepository>
            <id>sonatype-snapshots</id>
            <name>Sonatype Snapshots</name>
            <url>https://oss.sonatype.org/content/repositories/snapshots/</url>
        </pluginRepository>
    </pluginRepositories>

    <build>
        <resource>
            <directory>src/main/resources</directory>
            <filtering>true</filtering>
            <includes>
                <include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
        </resource>

        <plugins>
            <!-- git 정보 가져오기 -->
            <plugin>
                <groupId>pl.project13.maven</groupId>
                <artifactId>git-commit-id-plugin</artifactId>
                <version>2.2.1</version>
                <executions>
                    <execution>
                        <id>get-the-git-infos</id>
                        <goals>
                            <goal>revision</goal>
                        </goals>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
~~~

##### common.properties 파일 수정
 - develop의 경우는 "origin/develop" 으로 들어온다.
 - tag 배포시에는 tag 명이 들어온다.

~~~
deploy.phase=beta
deploy.domain=https://application.com/

# ${git.branch} 에 branch 정보가 들어온다.
git.branch=${git.branch}
~~~
