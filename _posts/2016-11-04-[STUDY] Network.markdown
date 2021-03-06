---
layout: post
title: "[STUDY] Network Overview"
date:   2016-11-04 09:00:00 +0900
categories: NETWORK
---

* Table of Contents
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

#### MAC 헤더
Ethernet에서 데이터를 통신을 하기 위해 필요한 정보
송신자 MAC 주소, 수신자 MAC 주소 정보가 담긴다.
broadcast 후 해당 장비만 그 정보를 취하고 나머지 장비는 그 정보를 버립니다.

##### 상대방의 MAC 주소는 어떻게 알수 있을까?
ARP (Address Resolution Protocol) 프로토콜을 통해서 획득한다.
이더넷은 보낸 신호가 모두에게 도달하므로 그 특성을 이용해서 정보를 획득한다.
'xxx.xxx.xxx.xxx' 주소를 가진 분 있습니까? MAC 정보를 알려주세요~
해당 주소를 가진 장비가 본인의 MAC 정보를 응답합니다.

- MAC 헤더까지 붙이는 작업이 IP의 담당입니다.
- Layer 1,2 의 역할이라고 알고 있는 경우도 있지만 그렇지 않습니다.

#### LAN 카드

~~~
|Application|

|TCP/IP 소프트웨어|

|LAN 드라이버|

|확장버스 슬롯|

|LAN 카드|
~~~

##### in LAN card

~~~
RJ-45 커넥터 - MAU - Ethernet Controller - Buffer Memory
                            |
                           ROM
송신용 신호선, 수신용 신호선 별도로 존재
~~~

 - MAU : 신호를 송,수신하는 회로를 내장
 - 이더넷 컨트롤러 : 충돌 검출이나 재송등 이더넷의 송.수신 동작을 제어
 - ROM : MAC 주소가 기록되어 있음
 - 버퍼 메모리 : 송.수신하는 프레임을 일시적으로 보존 

##### Packet에 붙이는 3개의 제어용 데이터

디지털 신호를 전기신호로 나타낼 때는 0,1 비트값을 일정한 전압이나 전류의 값으로 변경합니다.
이렇게 전기 신호를 디지털 데이터로 표현이 가능합니다.

- 프리앰블 : 101010.... 연속된 56비트, 타이밍을 잴수 있도록 도움
- 스타트 프레임 딜리미터(SFD) : 10101011, 프레임의 시작 위치를 선정, 8비트 
- 프레임 체크 시퀀스 : 오류검출용 데이터

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
 - 메모리는 시작주소(낮은 주소)에서 부터 끝주소(높은 주소)로 쓰인다.


#### Host Byte Order
 - 개별 시스템이 내부적으로 데이터를 표현하는 방식
 - Big-Endian, Little-Endian 으로 나뉜다.
 - 시스템의 CPU에 따라서 결정된다.

##### Big-Endian
 - IBM, ARM, Motorola 
 - 시작주소에 4바이트 중 제일 큰 바이트(BIG)인 0x12 값이 먼저 쓰이는 케이스
 - 11이라는 Integer(4 Byte)를 읽어내기 위해서 4개 바이트를 모두 읽어야 한다.
 - 상위 바이트가 메모리에 먼저 적재되는 방식

~~~
0×12345678
|0×12|0×34|0×56|0×78|
낮은주소 —> 높은주소
~~~

##### Little-Endian
 - Intel X86, AMD, EDC
 - 시작주소에 4바이트 중 제일 작은 바이트(BIG)인 0x78 값이 먼저 쓰이는 케이스
 - 11이라는 Integer(4 Byte)를 읽어내기 위해서 첫번째 바이트만 읽어도 알 수 있다.
 - 하위 바이트가 메모리에 먼저 적재되는 방식

~~~
0×12345678
|0×78|0×56|0×34|0×12|
낮은주소 —> 높은주소
~~~

#### Network Byte Order
 - 네트워크 바이트 오더는 `Big-Endian` 만 사용한다.
 - Little-Endian 시스템에서는 Big-Endian 으로 변경해서 통신해야 한다. 

#### 용어정리

##### Loop Detection
~~~
잘못된 네트워크 연결 또는 구성으로 인해 2계층에서 루프가 생성되고 
브로드 캐스트, 멀티 캐스트 또는 알 수 없는 유니 캐스트가 반복적으로 전송 될 수 있습니다.
반복되는 전송은 네트워크 리소스를 낭비 할 수 있으며 때때로 네트워크를 마비시킬 수 있습니다.
루프 감지 매커니즘은 루프가 발생할 때 즉시 로그를 생성하므로 네트워크 연결 및 구성을 조정하도록
즉시 알려줍니다. 루프 감지를 통해 루프된 포트를 종료 할 수 있습니다.
~~~

##### 루프백이란?
~~~

~~~

#### Reference
 - [joinc.co.kr 네트워크 관련](http://www.joinc.co.kr/w/Site/Network_Programing/Documents/IntroTCPIP3)
 - [호스트 바이트 순서(Host Byte Order) – Big-Endian, Little-Endian](http://iblog.or.kr/hungi/it/server/network/2056)
 - [Terrorism 블로그의 TCP/IP 정리글](http://blog.naver.com/rnjstjdwo14/40126043617)
 - [성공과 실패를 결정하는 1%의 네트워크 원리]()


