---
layout: post
title: "[Netty] Start Netty Programming 2 - Event Handler"
date:   2016-11-09 09:00:00 +0900
categories: NETWORK JAVA
---

* Table of Contents
{:toc}

 - [본 글에 나오는 Source Github Link](https://github.com/krisjey/netty.book.kor)
 - 본 글은 `자바 네트워크 소녀 Netty` 책을 보고 정리한 글입니다. (책 내용이 참으로 좋습니다.)
 - Netty 4.1.6 Version Source 를 첨부하였습니다. 

### Event handler

소켓 채널의 이벤트를 인터페이스로 정의하고 이 인터페이스를 상속받은 이벤트 핸들러를 작성하여
채널 파이프 라인에 등록한다. 채널 파이프라인으로 입력되는 이벤트를 이벤트 루프가 가로채어 
이벤트에 해당하는 메소드를 수행하는 구조이다.

#### ChannelInboundHandler

- 아래 소스를 참고 합니다.
- 이벤트 루프(Event Loop)는 네티가 실행하는 스레도로써 부트 스트렙에 설정한 이벤트 루프이다.
- 코드의 주석에 각 설명을 대체합니다.

~~~java
public interface ChannelInboundHandler extends ChannelHandler {

    /**
     * 1) 서버 기준으로 처음 서버소켓 채널이 생성될 때
     * 2) 새로운 클라이언트가 서버에 접속하여 클라이언트 소켓 채널이 생성될때 이벤트가 발생한다.
     */
    void channelRegistered(ChannelHandlerContext ctx) throws Exception;

    /**
     * 이벤트 루프에서 채널이 제거되었을 때 발생하는 이벤트
     */
    void channelUnregistered(ChannelHandlerContext ctx) throws Exception;

    /**
     * channelRegistered() 이벤트 후에 발생한다.
     * 채널이 생성되어 이벤트 루프에 등록된 이후에 네티 API를 사용하여 채널 입출력을 수행할 상태가 되었다를 알려준다.
     *
     * ex) 서버 어플에 연결된 클라이언트의 연결 세기, 최초 연결메시지 전달 
     */
    void channelActive(ChannelHandlerContext ctx) throws Exception;

    /**
     * 채널이 비활성화 되었을때 발생하는 이벤트
     * channelInactive 가 발생한 이후에는 채널에 대한 입출력 작업을 수행할 수 없다.
     */
    void channelInactive(ChannelHandlerContext ctx) throws Exception;

    /**
     * 데이터가 수신되었음을 알려준다.
     * 수신된 데이터는 네티의 ByteBuf 객체에 저장되어 있으며 이벤트 메서드의 두번째 인자인 msg를 통해서 접근할 수 있다. 
     * 
     */
    void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception;

    /**
     * channelRead 이벤트는 채널에 데이터가 있을 때 발생하고 
     * 채널의 데이터를 다 읽어서 더 이상 데이타가 없을 때 channelReadComplete 이벤트가 발생한다.
     */
    void channelReadComplete(ChannelHandlerContext ctx) throws Exception;

    /**
     * Gets called if an user event was triggered.
     */
    void userEventTriggered(ChannelHandlerContext ctx, Object evt) throws Exception;

    /**
     * Gets called once the writable state of a {@link Channel} changed. You can check the state with
     * {@link Channel#isWritable()}.
     */
    void channelWritabilityChanged(ChannelHandlerContext ctx) throws Exception;

    /**
     * Gets called if a {@link Throwable} was thrown.
     */
    @Override
    @SuppressWarnings("deprecation")
    void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception;
}
~~~

#### ChannelOutboundHandler

~~~java
public interface ChannelOutboundHandler extends ChannelHandler {
    /**
     * 서버 소켓 채널이 클라이언트의 연결을 대기하는 IP와 포트가 설정되었을 때 발생하는 이벤트
     */
    void bind(ChannelHandlerContext ctx, SocketAddress localAddress, ChannelPromise promise) throws Exception;

    /**
     * 클라이언트 소켓 채널이 서버와 연결되었을 때 발생하는 이벤트 
     * 원격지의 SocketAddress, 로컬의 SocketAddress 정보가 인수로 입력된다.
     */
    void connect(ChannelHandlerContext ctx, SocketAddress remoteAddress, SocketAddress localAddress, ChannelPromise promise) throws Exception;

    /**
     * 클라이언트 소켓 채널의 연결이 끊어졌을 때 발생
     */
    void disconnect(ChannelHandlerContext ctx, ChannelPromise promise) throws Exception;

    /**
     * 클라이언트 소켓 채널의 연결이 닫혔을 때 발생
     */
    void close(ChannelHandlerContext ctx, ChannelPromise promise) throws Exception;

    /**
     * Called once a deregister operation is made from the current registered {@link EventLoop}.
     */
    void deregister(ChannelHandlerContext ctx, ChannelPromise promise) throws Exception;

    /**
     * 
     */
    void read(ChannelHandlerContext ctx) throws Exception;

    /**
     * 소켓 채널에 데이터가 기록되었을 때 발생한다. 
     * wirte 이벤트에는 소켓 채널에 기록된 데이터 버퍼가 인수로 입력된다.
     */
    void write(ChannelHandlerContext ctx, Object msg, ChannelPromise promise) throws Exception;

    /**
     * 소켓 채널에 대한 flush 메소드가 호출되었을 때 발생한다.
     */
    void flush(ChannelHandlerContext ctx) throws Exception;
}
~~~

#### Trouble Shouting

하나의 특정 이벤트를 구현한 이벤트 핸들러 2개를 등록한 경우는 어떻게 동작할 것인가?
예를 들어 channelRead() 를 구현한 A, B를 모두 파이프라인에 등록하였다. 
이 경우에는 앞에 등록한 A 의 channelRead()만 수행한다.

그 이유는 이벤트에 해당하는 이벤트 메소드를 수행하면서 이벤트가 사라졌기 때문이다.

그럼 두번째 이벤트 핸들러의 channelRead()를 수행하고 싶다면 아래의 코드를 수행하면 된다.
```
ctx.fireChannelRead(msg); 수행하여 강제적으로 channelRead 이벤트를 다시 발생시킵니다.
```

### 이벤트 루프
이벤트 루프란 이벤트를 실행하기 위한 무한루프 스레드를 지칭한다.
즉 이벤트가 발생하면 별도의 이벤트 큐에 이벤트를 던지게 되고 이벤트 루프는 
현재 이벤트 큐에 들어있는 이벤트를 가져다가 수행하는 방식이다.

이 때 이벤트 루프가 지원하는 스레드 종류에 따라 `단일 스레드 이벤트 루프`와 `다중 스레드 이벤트 루프`로 분기된다.
이벤트 루프가 처리한 이벤트의 결과를 돌려주는 방식에 따라 `콜백 패턴` 과 `퓨처 패턴`으로 나뉜다.

#### 네티는 어떻게 다중 스레드 이벤트 루프에서 순서를 보장하는가
 - 네티의 이벤트는 채널에서 발생한다.
 - 이벤트 루프 객체는 이벤트 큐를 가지고 있다.
 - 네티의 채널은 하나의 이벤트 루프에 등록된다.
 - 즉 채널에서 발생한 이벤트는 항상 동일한 이벤트 루프 스레드에서 처리하여 이벤트 발생 순서와 처리 순서가 일치된다.

#### Future Pattern

~~~java

ChannelFuture f = b.bind(8888).sync();
f.channel().closeFuture().sync();

// 비동기 bind()를 호출한다. 포트 바인딩이 완료되기 전에 ChannelFuture 객체를 돌려준다.
ChannelFuture bindFuture = b.bind(8888);
// 주어진 ChannelFuture 객체의 작업이 완료될 때까지 블로킹한다.
// bind() 가 완료되면 sync() 도 완료된다.
bindFuture.sync();

// bindFuture 객체를 통해 채널을 얻어온다. 여기서 얻은 채널은 8888번 포트에 바인딩된 서버 채널이다.
Channel serverChannel = bindFuture.channel();
// 바인드가 완료된 서버 채널의 closeFuture 객체를 돌려준다.
// 내티 내부에서 채널이 생성될 때 closeFuture 객체도 같이 생성되므로 
// closeFuture()가 돌려주는 closeFuture 객체는 항상 동일한 객체이다.
channelFuture closeFuture = serverChannel.closeFuture();
// closeFuture 객체는 채널의 연결이 종료될 때 연결 종료 이벤트를 받는다.
closeFuture.sync();
~~~


### 보안 

#### 네트워크 데이터 캡쳐 방법
 - TCP Dump 계열의 네트워크 분석 도구 이용 (wireshark, Ethereal)
 - 브라우져의 플러그인 (크롬의 개발자 도구)
 - 특정한 프로토콜의 캡쳐 방법 (Fiddler, charles)
    - Forward Proxy 방식
    - 브라우져의 proxy 설정에 자신의 port를 등록하여 proxy 한다.
####