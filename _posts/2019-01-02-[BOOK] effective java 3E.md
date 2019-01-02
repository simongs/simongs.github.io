---
layout: post
title: "이펙티브 자바 3판 혼자 정리하기"
date:   2019-01-02 09:00:00 +0900
categories: JAVA BOOK
---

## 1. 생성자 보다는 static factory method
 - 클래스의 인스턴스를 반환하는 방법에 대한 이야기

### 흔히 사용하는 명명 방식

Method Name | Description | Sample Code
--- | --- | ---
from | 매개변수 하나를 받아서 해당 타입의 인스턴시를 반환하는 형변환 메소드 | Data d = Date.from(instant);
of | 여러 매개변수를 받아서 적절한 타입의 인스턴스를 반환하는 집계 메소드 | Set<Rank> cards = EnumSet.of(JACK, QUEEN, KING);
valueOf | from, of의 자세한 버전 | BigInteger.valueOf(Integer.MAX_VALUE)
instance or getInstance | 매개변수로 명시한 인스턴스를 반환 | StackWalker.getInstance(option)
create or newInstnace | 매번 새로운 인스턴스를 반환한다. | Array.newInstance(classObject, arrayLen)
getType | getInstance와 같으나, 생성할 클래스가 아닌 다른 클래스에 팩터리 메소드를 정의할 때 쓴다 | Files.getFileStore(path)

### 관련 코드
~~~java
public static Boolean valueOf(boolean b) {
    return b ? Boolean.TRUE : Boolean.FALSE;
}
~~~

## 2. (연습필요) 생성자에 매개변수가 많다면 빌더를 고려하자.
 - 빌더 패턴은 파이썬, 스칼라에 있는 named optional parameter 를 흉내낸 것이다.
 - 생성자나 정적팩토리가 처리해야 할 매개변수가 많다면 빌더 패턴을 선택하는게 더 낫다. 매개변수 중 다수가 필수가 아니거나 같은 타입이면 특히 더 그렇다. 빌더는 점층적 생성자보다 클라이언트 코드를 읽고 쓰기가 훨씬 간결하고, 자바빈즈보다 안전한다.

### 관련코드
~~~java
NutritionFacts cocaCola = new NutritionFacts.Builder(240,8).calories(100).sodium(35.carbonydrate(27).build();
~~~

## 3. private 생성자나 Enum Type으로 싱글턴임을 보장하라.
 - 싱글톤 패턴은 쓸일이 없어서 패스
 - Enum을 싱글톤에 활용하면 좋다는 이야기 

## 4. (연습필요) 인스턴스 화를 막으려면 private 생성자를 활용하라 
 - 제목 그대로이다.
 - Instance 화가 되는 것을 방지한다.
 - 상속을 불가능하게 한다.

~~~java
public class UtilityClass() {
    // 인스턴스화를 방지한다.
    private UtilityClass() {
        new AssertionError("Impossible To new Instance");
    }
}

~~~

## 5. 자원을 직접 의존하지 말고 의존객체주입을 사용하라.
- 원제가 무엇을지 궁금해지는 번역이다.
- 추후 관심이 생길때 정리한다.

## 6. 불필요한 객체 생성은 피해라.
- String s = new String("bikini") // 이렇게 하지 마라
- 박싱된 기본타입보다는 그냥 기본타입을 사용하고, 의도치 않은 오토박싱을 주의하라.

# Reference
- 이펙티브 자바 3판

# History
날짜 | 정리 장수 
--- | --- 
2019-01-02 | 1~7장