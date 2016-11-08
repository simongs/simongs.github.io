---
layout: post
title: "JVM Internal and Operation Tips"
date:   2016-11-08 09:00:00 +0900
categories: java
---

## Reference
 - [Java Garbage Collection - GC의 기초학습](http://d2.naver.com/helloworld/1329)
 - [Jvm Internal](http://d2.naver.com/helloworld/1230)
 - [Java Reference와 GC](http://d2.naver.com/helloworld/329631)

## TIPS

#### Get JVM GC Algorithm ([관련 링크 - STACKOVERFLOW](http://stackoverflow.com/questions/2498942/how-can-i-see-which-garbage-collector-java-is-using))
  
현재 운영중인 서버의 GC 알고리즘이 궁금하다면?

~~~
~) jmap -heap 2313

Attaching to process ID 2313, please wait...
Debugger attached successfully.
Server compiler detected.
JVM version is 24.60-b09

using thread-local object allocation.
Parallel GC with 4 thread(s)

Heap Configuration:
   MinHeapFreeRatio = 0
   MaxHeapFreeRatio = 100
   MaxHeapSize      = 2065694720 (1970.0MB)
   NewSize          = 1310720 (1.25MB)
   MaxNewSize       = 17592186044415 MB
   OldSize          = 5439488 (5.1875MB)
   NewRatio         = 2
   SurvivorRatio    = 8
   PermSize         = 174063616 (166.0MB)
   MaxPermSize      = 174063616 (166.0MB)
   G1HeapRegionSize = 0 (0.0MB)

Heap Usage:
PS Young Generation
Eden Space:
   capacity = 559415296 (533.5MB)
   used     = 379181248 (361.61541748046875MB)
   free     = 180234048 (171.88458251953125MB)
   67.78170899352742% used
From Space:
   capacity = 60293120 (57.5MB)
   used     = 0 (0.0MB)
   free     = 60293120 (57.5MB)
   0.0% used
To Space:
   capacity = 61341696 (58.5MB)
   used     = 0 (0.0MB)
   free     = 61341696 (58.5MB)
   0.0% used
PS Old Generation
   capacity = 176160768 (168.0MB)
   used     = 82114880 (78.31085205078125MB)
   free     = 94045888 (89.68914794921875MB)
   46.613602411179315% used
PS Perm Generation
   capacity = 174063616 (166.0MB)
   used     = 56193552 (53.59034729003906MB)
   free     = 117870064 (112.40965270996094MB)
   32.28334174098739% used

22328 interned Strings occupying 2681160 bytes.

~) java -version
java version "1.7.0_60"
Java(TM) SE Runtime Environment (build 1.7.0_60-b19)
Java HotSpot(TM) 64-Bit Server VM (build 24.60-b09, mixed mode)

~~~
