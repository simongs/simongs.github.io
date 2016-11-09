---
layout: post
title: "[Netty] Start Netty Programming"
date:   2016-10-25 09:00:00 +0900
categories: etc netty framework 
---

 - [본 글에 나오는 Source Github Link](https://github.com/krisjey/netty.book.kor)
 - 본 글은 `자바 네트워크 소녀 Netty` 책을 보고 정리한 글입니다. (책 내용이 참으로 좋습니다.) 

## 네트워크 프로그래밍 개념학습

### 동기 vs 비동기

#### 동기
 - 완료될 때 까지 결과를 기다리는 것

#### 비동기
 - 완료될 때까지 기다리지 않는것
 - 호출한 쪽이 주기적으로 끝났는지 알아내거나 (Polling)
 - 호출받은 쪽이 완료되면 알려주거나 한다 (Notify, Callback)

### Blocking I/O vs Non-Blocking I/O

#### Blocking I/O
 - 요청한 작업이 성공하거나 에러가 발생할 때 까지 응답을 돌려주지 않는다.
 - 아래 소스에서 다수의 요청을 받기 위한 모델이 request 별로 별도의 thread 생성
 - 1 request = 1 Socket = 1 Thread
 - 1 thread 별 thread stack 이 쌓이므로 메모리의 한계가 있다
 - 그 다음모델은 thread pool 을 관리하여 메모리의 누수를 막는 방법이 있다.
 - 적절한 스레드 갯수를 정해야 하는데 두가지 관점을 고려해야한다.
    - JVM GC를 고려해야한다. heap이 클수록 Stop-the-world 시간은 길어진다.
    - Context Switching의 비용은 thread 수가 늘어날 수록 커진다.
 - 그래서 Blocking방식으로는 아주 많은 수의 동시접속 사용자를 수용하기에 한계가 있다.

 ~~~java

 public class BlockingServer {

    public static void main(String[] args) throws Exception {
        BlockingServer server = new BlockingServer();
        server.run();
    }

    private void run() throws IOException {
        ServerSocket server = new ServerSocket(8888);
        System.out.println("접속 대기중");

        while (true) {
            Socket sock = server.accept();
            System.out.println("클라이언트 연결됨");

            OutputStream out = sock.getOutputStream();
            InputStream in = sock.getInputStream();

            while (true) {
                try {
                    int request = in.read(); // blocking method
                    
                    // blocking method, 운영체제의 송신버퍼에 전송할 데이터를 기록한다. 
                    // 이때 송신버퍼의 남은 크기가 write 메소드에서 기록한 데이터의 크기보다 작다면 
                    // 송신버퍼가 비워질 때까지 블로킹된다.
                    out.write(request); 
                }
                catch (IOException e) {
                    break;
                }
            }
        }
    }
}
~~~

#### Non-Blocking I/O
 - 요청한 작업의 성공여부와 상관없이 바로 결과를 돌려주는 것
 - blocking 소켓은 read, write, accept() 등과 같이 입출력 메소드가 호출되면 완료될 때까지 스레드가 멈추게 된다.
 - 일종의 이벤트 기반 프로그래밍이다.
 - Non-Blocking 소캣의 Selector 를 활용한 I/O 이벤트 감시

~~~java

public class NonBlockingServer {
    private Map<SocketChannel, List<byte[]>> keepDataTrack = new HashMap<>();
    private ByteBuffer buffer = ByteBuffer.allocate(2 * 1024);

    private void startEchoServer() {
       try (
          // Selector는 자신에게 등록된 채널에 변경 사항이 발생했는지 확인한다.
          // 변경사항이 발생한 채널에 대한 접근을 가능하게 한다.
          Selector selector = Selector.open();

          // blocking 소켓의 ServerSocket에 대응하는 Non-Blocking 소켓클래스
          ServerSocketChannel serverSocketChannel = ServerSocketChannel.open()
        ) {

          if ((serverSocketChannel.isOpen()) && (selector.isOpen())) {
             serverSocketChannel.configureBlocking(false); // default value is true. 비동기로 하려면 세팅필요
             serverSocketChannel.bind(new InetSocketAddress(8888)); // port binding

             // ServerSocketChannel 에 Selector 를 등록한다.
             // Selector가 감지할 이벤트는 연결 요청에 해당하는 Accept() Operation 이다.
             serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
             System.out.println("접속 대기중");

             while (true) {
                // Selector 에 등록된 채널에서 변경사항이 있는지 검사한다.
                // Selector 에 아무런 I/O 이밴트도 발생하지 않으면 스레드는 이 부분에서 블로킹 된다.
                // I/O 이벤트가 발생하지 않았을 때 블로킹을 피하고 싶다면 selectNow()를 사용하면 된다.
                selector.select();

                // Selector 에 등록돤 채널 중에서 I/O 이벤트가 발생한 채널들의 목록을 조회한다.
                Iterator<SelectionKey> keys = selector.selectedKeys().iterator();

                while (keys.hasNext()) {
                   SelectionKey key = (SelectionKey) keys.next();

                   // I/O 이벤트가 발생한 채널에서 동일한 이벤트가 감지되는 것을 방지하기 위해 제거한다.
                   keys.remove();

                   if (!key.isValid()) {
                      continue;
                   }
                   
                   // 연결요청
                   if (key.isAcceptable()) {
                      this.acceptOP(key, selector);
                   }

                   // 데이터 수신
                   else if (key.isReadable()) {
                      this.readOP(key);
                   }

                   // 데이터 쓰기 가능
                   else if (key.isWritable()) {
                      this.writeOP(key);
                   }
                }
             }
          }
          else {
             System.out.println("서버 소캣을 생성하지 못했습니다.");
          }
       }
       catch (IOException ex) {
          System.err.println(ex);
       }
    }

    private void acceptOP(SelectionKey key, Selector selector) throws IOException {
       ServerSocketChannel serverChannel = (ServerSocketChannel) key.channel();
       // 클라이언트의 연결을 수락하고 연결된 소켓 채널을 가져온다.
       SocketChannel socketChannel = serverChannel.accept();
       socketChannel.configureBlocking(false);

       System.out.println("클라이언트 연결됨 : " + socketChannel.getRemoteAddress());

       keepDataTrack.put(socketChannel, new ArrayList<byte[]>());

       // 클라이언트 소켓 채널을 Selector에 등록하여 I/O 이벤트를 감시한다.
       socketChannel.register(selector, SelectionKey.OP_READ);
    }

    private void readOP(SelectionKey key) {
       try {
          SocketChannel socketChannel = (SocketChannel) key.channel();
          buffer.clear();
          int numRead = -1;
          try {
             numRead = socketChannel.read(buffer);
          }
          catch (IOException e) {
             System.err.println("데이터 읽기 에러!");
          }

          if (numRead == -1) {
             this.keepDataTrack.remove(socketChannel);
             System.out.println("클라이언트 연결 종료 : "
                                + socketChannel.getRemoteAddress());
             socketChannel.close();
             key.cancel();
             return;
          }

          byte[] data = new byte[numRead];
          System.arraycopy(buffer.array(), 0, data, 0, numRead);
          System.out.println(new String(data, "UTF-8")
                              + " from " + socketChannel.getRemoteAddress());

          doEchoJob(key, data);
       }
       catch (IOException ex) {
          System.err.println(ex);
       }
    }

    private void writeOP(SelectionKey key) throws IOException {
       SocketChannel socketChannel = (SocketChannel) key.channel();

       List<byte[]> channelData = keepDataTrack.get(socketChannel);
       Iterator<byte[]> its = channelData.iterator();

       while (its.hasNext()) {
          byte[] it = its.next();
          its.remove();
          socketChannel.write(ByteBuffer.wrap(it));
       }

       key.interestOps(SelectionKey.OP_READ);
    }

    private void doEchoJob(SelectionKey key, byte[] data) {
       SocketChannel socketChannel = (SocketChannel) key.channel();
       List<byte[]> channelData = keepDataTrack.get(socketChannel);
       channelData.add(data);

       key.interestOps(SelectionKey.OP_WRITE);
    }

    public static void main(String[] args) {
       NonBlockingServer main = new NonBlockingServer();
       main.startEchoServer();
    }
 }
~~~

### Event-Driven 프로그래밍
 - Javascript, iOS 앱, Android앱, 데스크탑 어플리케이션 주로 클라이언트 단의 프로그래밍에서 등장하는 패턴
 - NodeJS는 비동기 싱글스레드이다. 비동기식 API와 동기식 API가 혼재되어 있다. (API에 명시됨)
 - 이벤트 기반 프로그래밍은 추상화 레벨이 중요하다. abstract <-> detail 의 간격 조절 중요

#### Event Driven in Netty

##### 소켓 연결 순서

소켓이란 데이터 송수신을 위한 네트워크 추상화 단위이다.
일반적으로 네트워크 프로그래밍에서 소켓은 IP주소와 포트를 가지고 있으며 양방향 네트워크 통신이 가능한 객체이다.

소켓에 데이터를 기록하거나 읽으려면 소켓에 연결된 소켓 채널(NIO) 이나 스트림(Old Blocking I.O)를 사용해야 한다.
네티가 제공하는 소켓 채널과 용어를 분리하고자 스트림으로 통칭한다.
클라이언트 어플리케이션이 소켓에 연결된 스트림에 데이터를 기록하면 소켓이 해당 데이터를 인터넷으로 연결된 서버로 전송한다.

클라이언트|서버
------|------
-|서버소켓생성
소켓생성|포트바인딩
연결요청|연결대기
- | 연결수락
- | 소켓생성
데이터 전송 | 데이터 수신
데이터 수신 | 데이터 전송
소켓 닫기 | 소켓 닫기

#### Netty inbound vs outbound
어떤 입장의 inbound, outbound인지를 명확하게 해야한다. (client 와 server는 정반대이다.)

### Netty BootStrap

#### 대표적인 구성요소
- 이벤트 루프
    - 소켓 채널애서 발생한 이벤트를 처리하는 스레드 모델에 대한 구현
- 채널의 전송 모드
    - blocking, non-blocking, epoll
    - epoll은 linux 커널 2.6 이상에서만 사용가능한 입출력 다중화 기볍
- 채널 파이프라인
    - 소켓 채널로 수신된 데이터를 처리할 데이터핸들러들을 지정한다.

#### 정의
부트스트랩은 네티로 작성한 네트워크 어플리케이션의 동작 방식과 환경을 설정하는 도우미 클래스이다.
예를 들어 네트워크에서 수신한 데이터를 단일 스레드로 데이터베이스에 저장하는 네트워크 프로그램을 작성한다고 가정
 - NIO 소켓 모드를 지원하는 서버 소캣 채널
 - 데이터를 변환하여 데이터베이스에 저장하는 데이터 처리 이벤트 핸들러
 - 8099 포트 바인딩
 - 단일 스레드를 지원하는 이벤트 루프

#### 구조
- 전송계층 (소켓모드 및 I/O 종류)
- 이벤트루프 (단일스레드, 다중스레드)
- 채널 파이프라인
- 소켓 주소와 포트
- 소켓 옵션

#### 종류
- 서버 어플리케이션 : ServerBootStrap
- 클라이언트 어플리케이션 : BootStrap







### Netty의 이점
 - Asyncronous Event-Driven Network Framework
 - Non-Blocking 비동기 처리가 기본이고 적은 스레드로 많은 사용자의 접속을 받을 수 있다.
 - GC부하를 최소화하는 Zero-copy ByteBuf를 지원한다. GC 시점에 회피하도록 한다.
 - 멀티스레딩 처리 없이도 개발할 수 있도록 한다.
 - Netty를 사용하는 이유는 고성능, 쓰기 편하고, 유연하고 쉽다.
 - 어려운 주제임에도 불구하고 레이어가 잘 구성되어 있다.

각종 네트워크 프로토콜 (코덱 기본 지원)
 - Size + Data = Payload 방식

### Netty Interface List

#### Channel
스트림, 파일, 모든 I/O 작업은 비동기
읽기, 쓰기, 연결(connect), 바인드(bind)
remoteAddress 지금 붙어있는 클라이언트의 IP정보 (SocketAddress)

#### ChannelFuture
사건을 걸어놓는 장소
Listener -> 여러개의 액션을 추가해놓는다. (addListener(List)

#### ChannelHandler
어떻게 핸들링할까? 어떤 액션을 할까?
Netty를 개발한다는 것은 ChannelHandler를 만드는 작업이다 (중요)
channelInboundHandlerAdapter

ChannelOutboundHandlerAdapter
내가 쓰고자 하는 요청을 받을 수 있다 여기서 내용을 writing 할 수 있다.
보통은 무언가를 받았을때 무언가를 한다를 기술하게 된다.

#### ChannelHandlerContext
Handler는 하나이지만 어떤 request(Context)인지를 확인하는 용도로 사용한다.

#### ChannelPipeline
이런것들을 다 연결해 놓는 곳

#### EventLoop
직접 건드릴 일은 거의 없음. 등록된 channel 들의 I/O 담당

#### Intercepting Filter

### Netty와 UnitTest
비동기 방식의 유닛테스트는 어려울 수 있다. 
EmbeddedChannel 을 활용한다. (Inbound, Outbound 등을 정의할 수 있다.)

### Netty 메모리 모델

#### 메모리 모델과 바이트 버퍼
 - 중요한 포인트입니다. Netty에서 사용하는 ByteBuf 는 별도로 관리한다. 
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
