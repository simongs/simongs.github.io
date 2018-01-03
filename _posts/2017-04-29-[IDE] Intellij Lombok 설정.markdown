---
layout: post
title: "[IDE] Intellij Lombok IDE 설정 및 배포 설정"
date:   2017-04-29 09:00:00 +0900
categories: IDE 
---

# Lombok 설정

## Intellij 설정
아래 레퍼런스의 lombok-intellij-plugin 항목의 링크를 천천히 따라하면 됩니다.

## Lombok이 반영된 프로젝트 Deploy 방법
AnnotationProcessor를 이용하여 컴파일 시점에 코드를 생성한다.
배포 시점에 lombok-1.16.xx.jar 파일이 존재하면 javac 가 동작하는 시점에 바이너리 코드가 생성된다.

## Lombok은 어떻게 동작하는가

javax.annotation.processing 패키지


## Reference
[[Lombok]사용 설명](http://lahuman.jabsiri.co.kr/124)
[lombok-intellij-plugin](https://github.com/mplushnikov/lombok-intellij-plugin)