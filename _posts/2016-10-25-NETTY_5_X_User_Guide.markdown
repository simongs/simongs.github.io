---
layout: post
title: "Netty USER Guide 따라하기"
date:   2016-10-25 09:00:00 +0900
categories: etc netty 
---

# Netty란

## 1강
10시 30분 ~ 11시 30분
김대현님. 프리랜서 제주도 개발자.
30대 서버였지만 지속적인 장애발생. 일일 3억 PV 
비동기기반의 통신으로 교체하면서 36대 장비를 2대 장비로 교체.
이때 비동기의 위력을 알게 되었다고 합니다.

HAProxy , Memcached, Redis, nginx, nodeJS = 비동기 기반.

Netty를 사용하는 이유는 고성능, 쓰기 편하고, 유연하고 쉽다.
어려운 주제임에도 불구하고 레이어가 잘 구성되어 있다.

Netty의 이점
 - Asyncronous Event-Driven Network Framework
 - Non-Blocking 비동기 처리가 기본이고 적은 스레드로 많은 사용자의 접속을 받을 수 있다.
 - GC부하를 최소화하는 Zero-copy ByteBuf를 지원한다. GC 시점에 회피하도록 한다.
 - 멀티스레딩 처리 없이도 개발할 수 있도록 한다.
 - Netty를 사용하는 이유는 고성능, 쓰기 편하고, 유연하고 쉽다.
 - 어려운 주제임에도 불구하고 레이어가 잘 구성되어 있다.

각종 네트워크 프로토콜 (코덱 기본 지원)
 - Size + Data = Payload 방식

### 동기 vs 비동기
완료될 때 까지 기다린다. 결과를 통보받는다. 의 차이점
비동기의 2가지 방식 : polling , pushing

#### Sync vs Async

#### Blocking vs Non-Blocking

#### Event-Driven 프로그래밍
Javascript, iOS 앱, Android앱, 데스크탑 어플리케이션
주로 클라이언트 단의 프로그래밍에서 등장하는 패턴

NodeJS는 비동기 싱글스레드이다.
비동기식 API와 동기식 API가 혼재되어 있다. (API에 명시됨)

#### JAVA의 NIO

### Netty

#### Interface

##### Channel
스트림, 파일, 모든 I/O 작업은 비동기
읽기, 쓰기, 연결(connect), 바인드(bind)
remoteAddress 지금 붙어있는 클라이언트의 IP정보 (SocketAddress)

##### ChannelFuture
사건을 걸어놓는 장소
Listener -> 여러개의 액션을 추가해놓는다. (addListener(List)

##### ChannelHandler
어떻게 핸들링할까? 어떤 액션을 할까?
Netty를 개발한다는 것은 ChannelHandler를 만드는 작업이다 (중요)
channelInboundHandlerAdapter

ChannelOutboundHandlerAdapter
내가 쓰고자 하는 요청을 받을 수 있다 여기서 내용을 writing 할 수 있다.
보통은 무언가를 받았을때 무언가를 한다를 기술하게 된다.

##### ChannelHandlerContext
Handler는 하나이지만 어떤 request(Context)인지를 확인하는 용도로 사용한다.


##### ChannelPipeline
이런것들을 다 연결해 놓는 곳

##### EventLoop
직접 건드릴 일은 거의 없음. 등록된 channel 들의 I/O 담당

##### Intercepting Filter

#### Netty와 UnitTest
비동기 방식의 유닛테스트는 어려울 수 있다. 
EmbeddedChannel 을 활용한다. (Inbound, Outbound 등을 정의할 수 있다.)


## 2강

### Netty 메모리 모델
#### 메모리 모델과 바이트 버퍼
중요한 포인트입니다.
Netty에서 사용하는 ByteBuf 는 별도로 관리한다. 
 - 성능 측면에서 GC부담 최소화
 - ByteBuf를 window를 쪼개서 준다?
 - heap에 할당하면 GC가 돌아갈때 계속 counting을 한다. 그러나 Netty는 별도의 메모리 공간을 미리 할당해서 GC가 돌아갈때 count되지 않도록 회피한다.
 - 디라이브드 버퍼?
 - ByteBuffer, ByteArray?

#### io.netty.buffer.*
 - flip() 호출할 필요없음.
 - 참조수 관리로 메모리 수동 해제를 한다. (성능과 직결되는 부분)

#### ReferenceCounted 인터페이스
 - 별도 메모리 풀에서 할당/해제
 - 생성시 최초의 참조수 1 +1 증가는 retain(), -1 감소는 release()
 - 언제 retain()을 하며 언제 release()를 할까요?

#### retain(), release() 정책
 - 마지막에 사용하는 메소드가 release()를 한다.

#### 유연한 파이프라인

#### ChannelInbound

#### Derived Buffer (파생버퍼)
 > 원 소스 바이트버퍼를 파생한 버퍼
 > 원래 소스가 사라지면 얘도 사라진다.
 > header + payload 가 있는 원소스라면

#### ByteBufferHolder
파생버퍼와 마찬가지로 원래 버퍼를 공유한다.

#### ChannelPipeline
ChannelHandler 같이 중요한 아이.
엮여있는 순서대로 차례대로 호출이 된다.
UNIX의 pipe 같은 개념 (프로세스를 chaning해서 사용한다)
Netty의 ChannelHandler 를 chaining 해서 사용한다.
TLS 소켓만들때는 파이프라인을 동적으로 변경가능하다. (일반 프로토콜로 접속하다가 중간에 SSL 프로토콜로 변경되는 케이스를 처리하고 싶을때)

## 3강
텍스트 기반 프로토콜의 채팅 서버를 만들자.

깔끔한 스레드 모델과 채널퓨처 & 프로미스

### 깔끔한 스레드 모델
멀티 프로세스 와 멀티 스레드 의 차이는 메모리 스페이스를 공유하느냐 하지 않느냐의 차이다.
프로세스간 데이터 전달과 스레드 간의 데이터 전달은 맥락이 다르다.
스레드는 그래서 동기화가 필요하다.
DeadLock은 4가지 필요조건이 있고 보통은 순환대기를 깨서 DeadLock을 발생시키지 않는다. 즉 Lock 잡는 순서를 관리한다.

타조 알고리즘? 천적이 나타나면 땅에 머리를 박는다ㅋㅋ
멀티스레드 프로그래밍은 제대로 하기가 어렵다.
Netty는 어떤 스레드 모델을 썼을까?

#### 스레드모델 (in netty)
ChannelHandler의 매소드는 동시에 불리지 않는다.
Channel은 단 하나의 스레드에 할당되고 그 스레드에서만 호출한다. 즉 채널은 싱글스레드임을 보장한다. 반대입장으로 스레드는 여러개의 채널을 받는다.
심지어 관련 ChannelFutureListener도 그 스레드를 사용한다.

결론은 각 채널은 싱글스레드로 운영되고 스레드 안전성을 신경쓰지 않아도 된다.

#### 채널과 스레드의 관계

### 채널퓨처 & 프로미스

무거운 작업은 다른 스레드가 했지만 결과를 원래 스레드로 받아서 활용

#### Future
~~~
Future<Image> img = loadImage(); // 이미지 로딩을 시작한다.
renderHtmlText() // 일단 텍스트 부터 노출한다.
drawImage(img.get()) // 이미지가 로딩이 완료되면 이미지를 노출한다.
~~~
img.get() 호출하면 일단 Blocking을 했다가 로딩이 완료되면 img.get()을 수행하여 완료한다.

#### Promise
Future애 결과를 통보하는 입장에서 사용.
~~~
위의 예로보면 이미지를 로딩을 기다리고 있는 곳에 알려주는 역할을 한다.
~~~


## 4강

### 풍부한 코덱들

#### io.netty.handler.codec.*
텍스트 처리, 압축처리, 다양한 프로토콜 지원 등등
* 코덱도 채널핸들러입니다. *

### 웹소켓 서버 구현

### Reference
 - [Netty Homepage](http://netty.io/)
 - [User guide for 5.x - eng](http://netty.io/wiki/user-guide-for-5.x.html)
 - [User guide for 4.x - kor](http://ikpil.com/1338)
 - [김대현(hatemogi)님 강의자료](https://github.com/hatemogi) 
