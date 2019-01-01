---
layout: post
title: "[ETC] Docker with Mac"
date:   2018-11-12 09:00:00 +0900
categories: ETC DEV-TOOLS
---

## 설치
 - 공식 Reference Site 따라함
 - Download 링크
    - https://hub.docker.com/editions/community/docker-ce-desktop-mac
 - ![Docker 상단 이미지](http://prntscr.com/ly7aoo)


## Terminal
 - postgres 이미지 다운로드 및 컨테이너 실행 
    - docker ps 
        - 현재 실행중인 Docker 프로세스를 보여준다.
    - docker ps -a
        - 현재 일시정지된 Docker 프로세스까지 보여준다.
    - docker images
        - 다운로드돤 이미지 파일들을 보여준다.
    - docker run hello-world
        - 현재 로컬에 있는 hello-world 이미지를 수행한다.
        - 로컬에 해당 이미지가 없다면 Docker Remote Repository에 해당 이미지명의 가장 최신 이미지를 가져온다.
        - docker run --name demo -e POSTGRES_PASS=password -d postgres
        - docker run -p 5432:5432 -e POSTGRES_PASS=password -e POSTGRES_USER=simons -e POSTGRES_DB=springdata --name demo -d postgres
    - docker exec -it demo bash

## postgresql 문법 
- \d+ tablename
   - table 스키마 보기 
- \dt
    - table 리스트 보기
    
    

## Reference
 - 