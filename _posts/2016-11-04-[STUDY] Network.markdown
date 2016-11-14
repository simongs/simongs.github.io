---
layout: post
title: "[STUDY] Network Overview"
date:   2016-11-04 09:00:00 +0900
categories: network study
---

- TOC
{:toc}

## Network Base Knowledge

### Server Programming

#### socket, bind, listen, accept
1. socket으로 클라이언트 측과 마찬가지로 소켓을 만듬
2. (bind) 통신을 허가하는 IP주소와 자기 자신의 포트번호로 소켓에 정보를 등록
    특정 client와만 통신을 원하면 IP주소 부분에 등록
3. bind로 소켓에 값을 등록 후 listen을 호출하여 클라이언트에 접속 대기 중임을 TCP/IP 소프트웨어에 통지
4. 그러면 accept를 호출하게 되고 이것으로 서버는 클라이언트로부터의 통신을 시작하는 제어패킷을 기다리게 됩니다.

#### 다수의 클라이언트와 통신하는 방법
1. 클라이언트의 시작 제어 패킷이 도착하면 응답 패킷을 서버에서 보냅니다. 이걸로 접속 동작 끝
2. 서버측에서는 accept가 이 접속동작을 실행하여 끝나면 제어를 어플리케이션에 돌려주고 동시에 새로운 디스크립터를 어플리케이션에 넘겨줍니다.
3. 디스크립터는 소켓을 식볋하는 것으므로 새로운 디스크립터를 넘긴다는 것은 새로운 소켓이 내부에서 만들어진다는 것입니다.
4. 접속을 대기하기 위하여 만든 첫 소켓은 그대로 유지되며 다른 클라이언트의 시작 제어 패킷을 기다립니다.
5. 이렇게 다수의 클라이언트와 통신할 수 있습니다.

### socket
소켓의 실체는 TCP/IP 소프트웨어 내부의 메모리영역이다.
소켓을 만들때는 먼저 OS요청을 하여 소켓 하나 만큼의 메모리 영역을 확보합니다.
그 후 확보한 메모리 영역에 제어 정보를 기록해 나갑니다.
그 정보는 정의하는 프로토콜의 종류에 따라 다릅니다.

소켓을 만들고 통신할 준비가 끝나면 socket을 호출한 어플리케이션에게 디스크립터를 돌려줍니다.
디스크립터라는 것은 TCP/IP 소프트웨어 내부에 있는 다수의 소켓 중 어느것을 가리키는가 하는 정보입니다.

#### connection, session
서버와 클라이언트 사이의 소켓이 생성이 되면 논리적인 통로(pipe)가 만들어진다.
그 논리적인 통로를 통해서 데이터를 주고 받는다. 
이를 커넥션 이라고도 하고 세션이라고도 한다.

### TCP/IP

#### 통신 순서
1. 통신 개시를 알리는 제어용 패킷을 교환
2. 송신 데이터를 상대로부터 수신
3. 데이터를 상대에 송신
4. 통신의 종료를 알리는 제어용 패킷을 교환

#### 통신 순서 detail 

[접속단계]
~~~
(->) 데이터를 보내려고 합니다.
(<-) 알았습니다. 이쪽에서 데이터를 보내겠습니다.
(->) 알았습니다.
~~~

[데이터 송.수신 단계]
~~~
(->) 데이터 송신
(<-) 수신 확인 응답 전송
~~~

[접속 종료]
~~~
(<-) 이것으로 데이터는 끝입니다.
(->) 알았습니다.
(->) 이쪽도 보내는 데이터는 없습니다.
(<-) 알았습니다.
~~~

#### TCP Header
20 Byte 형식의 Header 정보 [TCP 프로토콜 관련 wikipdia](https://ko.wikipedia.org/wiki/전송_제어_프로토콜)

##### 주요 필드
 - 제어비트 (SYN (보내겠습니다.), ACK(알았습니다.))
 - 송신자, 수신자의 포트번호
 - 송, 수신 데이터의 일련번호
 - 데이터 오프셋

#### IP Header
TCP Header 생성후 IP 헤더를 만들어서 그 앞에 붙입니다.
가장 중요한 것은 패킷을 어디로 보낼지를 결정하는 IP 주소입니다. 


### DNS (Domain Name System)
 32bit IP주소(ipv4) 를 일일이 기억할 수 없어서 그와 매핑된 Domain을 관리하는 시스템
 네트워크 통신 즉 소켓 통신시에 필요한 IP 정보획득 (port 별도)

#### 관련용어
 - Name Resolution = DNS 시스템을 이용하여 IP 주소를 알아내는 것
 - DNS Resolver = Name Resolution을 수행하는 녀석 (DNS Client) 

#### DNS Request

요청항목|설명
-----|------
이름  |서버나 메일 배송처의 이름
클래스 | 이름의 클래스, 인터넷 IN 고정
타입  | A: 등록되어 있는 서버명 IP 회답 <br/> MX : 이름은 메일 배송처 나타냄

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
 - [성공과 실패를 결정하는 1%의 네트워크 원리]()
