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

### TIPS

#### class, method 명 is never used 경고 없애기
~~~
http://blog.woniper.net/205
~~~

#### code Folding 관련
~~~
Go to File -> Settings -> Editor -> General -> Code Folding
Uncheck Show code folding outline
~~~

#### Live Template 관련
[참고할만한 블로그 - Intellij Live Template 등록하기](http://uncle-bae.blogspot.kr/2015/09/intellij-live-template.html)

~~~
환경설정에서 Live Template을 등록한다. 
하단에 보이는 context 지정을 통해서 어떤 환경에서 
해당 Live Template을 사용할 지 결정해야 한다.
~~~