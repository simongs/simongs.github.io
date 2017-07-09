---
layout: post
title: "[JAVA] Java Concurrency (번역글)"
date:   2017-05-24 09:00:00 +0900
categories: JAVA 
---

- [원문 링크 : http://www.vogella.com/tutorials/JavaConcurrency/article.html](http://www.vogella.com/tutorials/JavaConcurrency/article.html)
- 본 글은 번역글입니다.
- SKIP 한 부분이 존재합니다.
- 작성중입니다.

- Table of Contents
{:toc}

# Java Concurrency

~~~
이 아티클은 자바에서 어떻게 병렬프로그래밍을 할 수 있는지 설명합니다.
parallel programming, immutability, thread, executor framework (thread Pool), 
futures, callables, CompletableFuture, fork-join 프레임워크 등에 대해 설명합니다.
~~~

## 1. Concurrency

### 1.1 Concurrency 란 무엇인가?
Concurrency 는 여러 프로그램을 동시에 실행할 수 있는 능력 혹은 프로그램의 여러 부분을 병렬적으로 수행할 수 있는 능력을 의미한다.
만약 시간을 소비하는 Task가 병렬적으로 혹은 비동기적으로 실행될 수 있다면, 프로그램의 인터렉티브한 부분과 처리량을 증진시킬수 있다.

현대 컴퓨터는 여러개의 CPU, 여러개의 Core를 가진 CPU를 가지고 있다. 멀티코어는 큰 볼륨의 어플리케이션을 만드는데 중요한 요소가 될 수 있다.

### 1.2 프로세스 vs 스레드

프로세스는 독립적으로 수행되고 다른 프로세스와는 독립적이다. 프로세스는 다른 프로세스와 직접적으로 데이터를 공유할 수 없다.
메모리, CPU 시간 같은 자원은 OS를 통해 할당받는다.

스레드는 light-weight 프로세스라고도 불리운다. 각각의 Call Stack을 지니고 있다.
동일한 프로세스 내에서는 프로세스의 자원을 다른 스레드와 같이 접근할 수 있다.
모든 스레드는 각자 메모리 캐쉬를 가지고 있다.
만약 한 스레드가 공유한 자원을 읽는다면 그 스레드는 본인의 메모리 캐쉬에 저장한다.
스레드는 공유자원을 다시 Read 할 수 있다.

## 2. Improvements and Issue With Concurrency

### 2.1. Limits of concurrency gains

자바에서는 병렬 프로세싱 이나 비동기적인 행동을 하기 위해서 여러개의 스레드를 운영할 것이다.
하나의 Task가 여러 개의 subtask로 나뉠수 있고, 각각의 subtask는 병렬적으로 수행될 것이다.

이론적으로 획득 가능한 성능 지표는 Amdahl's Law 에 의해 계산될 수 있다.

(skip)

### 2.2. Concurrency issues
스레드는 각각의 call Stack을 가지고 있지만, 프로세스의 공유 자원에도 접근이 가능합니다.
그래서 visibility, access 문제가 존재합니다.

visibility 는 A Thread가 공유자원을 읽은 후에 B가 공유자원에 변경을 일으키면, A는 이를 알지 못한다는 점을 의미한다.
access 문제는 동시에 공유자원을 변경하는 부분이 문제가 된다.

 - Liveness failure : deadlock 같은 문제 방생
 - Safely failure : 잘못된 데이터를 만들어낸다.

## 3. Concurrency in Java
 
### 3.1. Processes and Threads

자바는 thread 개념을 자바언어의 Thread 클래스를 통해서 지원한다.
자바는 해당 클래스를 통해 새로운 Thread를 생성할 수 있다.

Java 1.5부터는 java.util.concurrency Package를 통해서 비동기에 대한 지원이 더 강화되었다.

### 3.2. Locks and thread synchronization

자바는 동시에 여러 스레드에 의해 수행되는 코드 블락을 보호하려고 lock 을 제공한다.
제일 간단한 locking 방법은 자바 클래스나 메소드에 synchronized 키워드를 사용하는 것이다.

synchronized 키워드는 아래를 보장한다.
 - 오직 한 스레드만 접근할 수 있다.
 - 다른 스레드가 떠나기 전까지는 다른 스레드는 들어올 수 없다.

~~~java
public synchronized void critial() {
    // some thread critival stuff
    // here
}
~~~

당신은 메소드 안에서도 코드블락을 보호하기 위하여 synchronized 키워드를 사용할 수 있다.
이 블락은 키에 의해 보호되는데 이 키는 String 이거나 Object 이다. 이 키는 lock 이라고 불린다.

동일한 lock에 의해 보호되는 모든 코드는 동시에 오직 한 스레드에 의해서만 수행될 수 있다.

예를 들어서 아래 자료구조는 오직 한 스레드만 add(), next() 메소드를 접근 할 수 있도록 보장한다.

~~~java
public class CrawledSites {
    private List<String> crawledSites = new ArrayList<String>();
    private List<String> linkedSites = new ArrayList<String>();

    public void add(String site) {
        synchronized (this) {
            if (!crawledSites.contains(site)) {
                linkedSites.add(site);
            }
        }
    }

    /**
     * Get next site to crawl. Can return null (if nothing to crawl)
     */
    public String next() {
        if (linkedSites.size() == 0) {
            return null;
        }
        synchronized (this) {
            // Need to check again if size has changed
            if (linkedSites.size() > 0) {
                String s = linkedSites.get(0);
                linkedSites.remove(0);
                crawledSites.add(s);
                return s;
            }
            return null;
        }
    }
}
~~~

### 3.3. Volatile

(skip)

## 4. The Java Memory Model

### 4.1. Overview
Java 메모리 모델은 어플리케이션의 메인 메모리와 각 스레드의 메모리 간의 커뮤니케이션을 설명한다.
스레드에 의해 수행된 메모리의 변경이 다른 스레드로 전파되는 방식에 대한 규칙을 정의합니다.

또한 Java 메모리 모델은 스레드가 메인 메모리에서 각 스레드의 메모리를 re-fresh 하는 상황을 정의합니다.
또한 원자적인 Operation과 Orderation의 순서도 설명합니다.

### 4.2 Atomic operation
Atomic Operation이란 다른 Operation의 간섭가능성 없이 단일 unit으로 수행하는 operation을 의미합니다.

자바 언어 스펙은 변수를 읽거나 쓰는 것을 Atomic 하게 보장합니다. (long, double 타입이 아닌 경우에만)
long, double 자료형은 `volatile` 키워드와 함께 선언되어야만 Atomic 하게 움직입니다.

i 가 int 형으로 선언되어 있다고 가정한다.
i++ 연산은 자바에서 원자적인 연산이 아니다.
이것은 다른 numeric 자료형에서도 적용된다.

i++ 연산은 먼저 i에 현재 저장되어 있어 있는 값을 읽고(원자적), 그리고 1을 더합니다.(원자적)
그러나 읽고 쓰는 사이에서 i의 값이 변경되었을 수도 있습니다.

Java 1.5 부터는 자바는 원자적 변수를 제공합니다. (AtomicInteger, AtomicLong) 원자적인 연산을 위해 getAndDecrement(), getAndIncrement() 등의 메소드를 제공합니다.

### 4.3. Memory updates in synchronized code
자바 메모리 모델은 syncronized 블락에 진입한 각각의 스레드가 동일한 잠금에 의해서 보호된 이전 변경사항의 영향을 받는것을 보장합니다.

## 5. Immutability and Defensive Copies
### 5.1. Immutability

동시성 문제를 피하는 가장 간단한 방법은 스레드간에 불변 데이터만 공유하는 것이다. 불편데이터는 변경할 수 없는 데이터를 의미한다.

클래스를 불편으로 만들기 위해서는 
 - 모든 필드를 final로 선언한다.
 - class를 final로 선언한다.
 - the this reference is not allowed to escape during construction
 - 변경 가능한 데이터 객체를 참조하는 모든 필드는 private 으로 선언한다.
 - setter 메소드를 만들지 않는다.
 - 호출하는 쪽에 직접 노출되거나 반환되지 않는다.
 
불변 클래스는 그 상태를 관리하기 위해서 몇 개의 변경 가능한 데이터도 포함할 수 있습니다. 그러나 외부로부터. 클래스의 속성은 변경될 수 있습니다.