---
layout: post
title: "[STUDY] Network Overview"
date:   2016-11-04 09:00:00 +0900
categories: network study
---

{:toc}


### DNS (Domain Name System)
 32bit IP주소(ipv4) 를 일일이 기억할 수 없어서 그와 매핑된 Domain을 관리하는 시스템

#### 관련용어
 - Name Resolution = DNS 시스템을 이용하여 IP 주소를 알아내는 것
 - DNS Resolver = Name Resolution을 수행하는 녀석 (DNS Client) 

#### DNS Request
문의항목|설명
-----|------
이름|서버나 메일 배송처의 이름
클래스 | 이름의 클래스, 인터넷 IN 고정
타입 | A: 등록되어 있는 서버명 IP 회답 <br/> MX : 이름은 메일 배송처 나타냄

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

#### Reference
 - [joinc.co.kr 네트워크 관련](http://www.joinc.co.kr/w/Site/Network_Programing/Documents/IntroTCPIP3)
 - [호스트 바이트 순서(Host Byte Order) – Big-Endian, Little-Endian](http://iblog.or.kr/hungi/it/server/network/2056)
 - [Terrorism 블로그의 TCP/IP 정리글](http://blog.naver.com/rnjstjdwo14/40126043617)

