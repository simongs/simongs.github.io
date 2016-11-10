---
layout: post
title: "[STUDY] Network Overview"
date:   2016-11-04 09:00:00 +0900
categories: network study
---

#### Reference
 - [joinc.co.kr 네트워크 관련](http://www.joinc.co.kr/w/Site/Network_Programing/Documents/IntroTCPIP3)
 - [호스트 바이트 순서(Host Byte Order) – Big-Endian, Little-Endian](http://iblog.or.kr/hungi/it/server/network/2056)
 - [Terrorism 블로그의 TCP/IP 정리글](http://blog.naver.com/rnjstjdwo14/40126043617)

### Byte Order
 - 쓰이는 곳에 따라 호스트 바이트 순서, 네트워크 바이트 순서 로 나뉜다.

#### Host Byte Order
 - 시스템이 내부적으로 데이터를 표현하는 방식
 - Big-Endian, Little-Endian 으로 나뉜다.
 - 시스템의 CPU에 따라서 결정된다.

##### Big-Endian
 - IBM, ARM, Motorola 

~~~
0×12345678
|0×12|0×34|0×56|0×78|
낮은주소 —> 높은주소
~~~

##### Little-Endian
 - Intel X86, AMD, EDC

~~~
0×12345678
|0×78|0×56|0×34|0×12|
낮은주소 —> 높은주소
~~~

#### Network Byte Order
 - 네트워크 바이트 오더는 `Big-Endian` 만 사용한다.
 - Little-Endian 시스템에서는 Big-Endian 으로 변경해서 통신해야 한다. 
