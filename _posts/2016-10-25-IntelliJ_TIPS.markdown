---
layout: post
title: "IntelliJ TIPS"
date:   2016-10-25 09:00:00 +0900
categories: etc 
---

### SHORTCUT

#### Eclipse 단축키 비교
아래 이름을 참고로 intellij에서 단축키 정보를 변경합니다.

Eclipse	| IntelliJ IDEA | Description
------------- | ------------- | -------------
F4	|ctrl+h|	show the type hierarchy
ctrl+alt+g	|ctrl+alt+F7|	find usages
ctrl+shift+u|	ctrl+f7	|finds the usages in the same file
alt+shift+r	|shift+F6	|rename
ctrl+shift+r|	ctrl+shift+N	|find file / open resource
ctrl+shift+x, j	|ctrl+shift+F10|	run (java program)
ctrl+shift+o|	ctrl+alt+o	|organize imports
ctrl+o	|ctrl+F12	|show current file structure / outline
ctrl+shift+m	|ctrl+alt+V	|create local variable refactoring
syso ctrl+space	|sout ctrj+j	|System.out.println(“”)
alt + up/down	|ctrl + shift + up/down|	move lines
ctrl + d	|ctrl + y	|delete current line
???	|alt + h	|show subversion history
ctrl + h	|ctrl + shift + f	|search (find in path)
ctrl + 1 or ctrl + shift +  |	ctrl + alt + v	|introduce local variable
alt + shift + s	|alt + insert|	generate getters / setters
ctrl + shift + f|	ctrl + alt + l	|format code
ctrl + y	|ctrl + shift + z	|redo
ctrl + alt + h	|ctrl + alt + h |(same!)	show call hierarchy
-	|ctrl + alt + f7|	to jump to one of the callers of a method
ctrl + shift + i|	alt + f8 |	evaluate expression (in debugger)
F3	|ctrl + b	|go to declaration (e.g. go to method)

### Plug-in

#### MoreUnit
Ctrl+J 로 TDD 시 테스트 클래스와 원 클래스의 이동 호흡을 빠르게 가져간다. 

#### Grep Console
Console 로그의 시각화

#### Eclipse Fommater Plug-in
기존에 사용하던 이클립스의 포맷터 파일을 임포트한다.

### TIPS

#### class, method 명 is never used 경고 없애기
~~~
http://blog.woniper.net/205
~~~

#### code Folding 관련
~~~
Go to File -> Settings -> Editor -> General -> Code Folding

아래 체크 해제 (개인취향)

Uncheck Show code folding outline
imports
One-line methods
~~~

#### Live Template 관련
main, sysout 등 이클립스에서 사용하던 축약 템플릿 관련 설정

[참고할만한 블로그 - Intellij Live Template 등록하기](http://uncle-bae.blogspot.kr/2015/09/intellij-live-template.html)

~~~
환경설정에서 Live Template을 등록한다. 
하단에 보이는 context 지정을 통해서 어떤 환경에서 
해당 Live Template을 사용할 지 결정해야 한다.
~~~

#### static import 관련 설정

#### windowed 모드
듀얼모니터를 사용하는 경우 현재 실행되고 있는 window를 별도의 창으로 확장한다.
각 창의 우상단에 있는 환경설정 마크를 통해서 windowed Mode를 선택하도록 한다.