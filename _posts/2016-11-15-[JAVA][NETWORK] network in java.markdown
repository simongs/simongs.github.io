---
layout: post
title: "[JAVA][NETWORK] Network Programming in Java"
date:   2016-11-15 09:00:00 +0900
categories: java
---

### Trouble Shouting 

#### inputstream의 read()에서 0이 리턴되는 경우
 - inputstream의 read()는 blocking I/O 이다.
 - 즉 연결된 소켓으로부터 응답이 있어야지 코드 상의 다음 라인이 진행이 된다. 

[관련 링크](http://stackoverflow.com/questions/2319395/what-0-returned-by-inputstream-read-means-how-to-handle-this)

~~~
The only situation in which a InputStream may return 0 from a call to read(byte[]) is when the byte[] passed in has a length of 0:

▤ byte[] buf = new byte[0];
▤ int read = in.read(buf); // read will contain 0

As specified by this part of the JavaDoc:

▶ If the length of b is zero, then no bytes are read and 0 is returned

My guess: you used available() to see how big the buffer should be and it returned 0. 
Note that this is a misuse of available(). The JavaDoc explicitly states that:

▶ It is never correct to use the return value of this method to allocate a buffer intended to hold all data in this stream.
~~~
