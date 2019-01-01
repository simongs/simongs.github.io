---
layout: post
title: "MAC에서 Vue.js 관련 개발환경 세팅하기"
date:   2018-12-24 09:00:00 +0900
categories: ETC DEV-TOOLS
---

## NodeJS 설치
### nvm 설치 
### nodejs 설치 
 - nvm use 명령어를 통해 node 버젼을 변경할 수 있다.
 - nvm install node
    - 최신버젼 설치 
 - nvm istall v10
    - v10 버젼대의 LTS 버젼 설치. /(ex.v10.42.2)
~~~
#가장최산버전 설치
➜  workspace-vue nvm install node
➜  workspace-vue nvm ls-remote
       v10.14.2   (Latest LTS: Dubnium)
        v11.0.0
        v11.1.0
        v11.2.0
        v11.3.0
        v11.4.0
->      v11.5.0
➜  workspace-vue nvm install node v10.14.2
v11.5.0 is already installed.
Now using node v11.5.0 (npm v6.4.1)
➜  workspace-vue nvm install v10
Downloading and installing node v10.14.2...
Downloading https://nodejs.org/dist/v10.14.2/node-v10.14.2-darwin-x64.tar.gz...
######################################################################## 100.0%
Computing checksum with shasum -a 256
Checksums matched!
Now using node v10.14.2 (npm v6.4.1)
➜  workspace-vue nvm use v10
Now using node v10.14.2 (npm v6.4.1)
➜  workspace-vue nvm use v11
Now using node v11.5.0 (npm v6.4.1)
➜  workspace-vue nvm use v10
Now using node v10.14.2 (npm v6.4.1)

~~~

### Reference
 - https://github.com/creationix/nvm
 - https://gist.github.com/TeddyH/57aadd2bd31edc2e7c6dc410b95548bc


## Visual Studio Code 세팅
 - Vue를 쉽게 사용하기 위한 플러그인 설치 (vetur//)
### Extension Program
 - https://vuejs.github.io/vetur/
    - Vue 하이라이팅, 