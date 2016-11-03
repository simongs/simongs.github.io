---
layout: post
title: "Lambda Expression"
date:   2016-10-31 09:00:00 +0900
categories: java
---

#### Note
- 람다식을 사용하기 위해서는 인터페이스에 정의된 메소드를 통해 가능하다.
- 해당 인터페이스에는 반드시 하나의 메소드만 정의되어 있어야 한다. (2개 이상의 메소드가 있으면 오류가 난다.)
- @FunctionalInterface 인터페이스를 통해서 람다에서 쓰임을 명시한다.
- 

#### Note (sample)
~~~java
Func add = (int a, int b) -> a + b;
~~~

#### Reference
[자바 람다식(Lambda Expressions in Java)](http://jdm.kr/blog/181)