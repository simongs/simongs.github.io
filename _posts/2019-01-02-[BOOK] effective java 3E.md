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

## 7. 다 쓴 객체 참조를 해제하라.
- 메모리 누수 이슈 

## 8. finalizer 와 cleaner 사용을 피하라.
- cleaner(자바8까지는 finalizer)는 안전망 역할이나 중요하지 않은 네이티브 자원 회수용으로만 사용하자.
- cleaner, finalizer

## 9. try-finally 보다는 try-with-resource 를 활용하라.
- 이 구조를 활용하려면 해당 자원이 AutoCloseable 을 구현해야 한다.
- 무조건 try-with-resource 를 활용하라.

## 10. equals는 일반 규약을 지켜 재정의하라.

## 11. equals를 재정의하려거든 hashCode도 재정의해라. 

## 12. toString() 는 항상 재정의하라.
 - toString() 의 규약은 "모든 하위클래스에서 이 메소드를 재정의하라" 이다.

## 13. clone 재정의는 주의해서 진행해리.

## 14. Comparable을 구현할지 고려하라.

## 15. 클래스와 멤버의 접근권한을 최소화하라.
 - 모든 클래스와 멤버의 접근성을 최대한으로 좁혀야 한다.
 - 멤버에 부여할 수 있는 접근 수준 
    - 멤버린 필드, 메소드, 중첩 클래스, 중첩 인터페이스 이다. 
    - private : 멤버를 선언한 톱레벨 클래스에서 접근가능
    - package-private : 멤버가 소속된 패키지 안에서 접근가능
    - protected : 이 멤버를 선안한 클래스의 하위클래스에서도 접근이 가능한다.
    - public : 외부에 열려있다.
- 자바 9에서는 두 가지 암묵적 접근 수준이 추가되었다.
    - 패키지가 클래스의 묶음이듯 모듈은 패키지의 묶음이다.
    - 모듈은 자신에게 속하는 패키지 중에 공개(export) 할 것들을 선언한다.
        - 관례상 module-info.java 파일에 선언한다.

## 16. public 클래스에서는 public 필드가 아닌 접근자 메소드를 사용하라.
 - getter, setter 를 이용하라.
 - package-private 클래스 혹은 private 중첩클래스라면 데이터 필드를 노출한다 해도 하등의 문제가 없다.

## 17. 변경가능성을 최소화해라.
 - 객체를 불변으로 만드는 5가지 원칙 
    - 객체의 상태를 변경하는 메소드를 제공하지 않는다.
    - 클래스를 확장할 수 없도록 한다. (final class, private 생성자)
    - 모든 필드를 final로 선언한다.
    - 모든 필드를 private 으로 선언한다.
    - 자신 외에 내부의 가변필드에 접근 할 수 없도록 한다.

## 18. 상속보다는 컴포지션을 사용하라.

## 19. 상속을 고려해 설계히고 문서화하라. 그러지 않았다면 상속하지 마라.

## 20. Abstract Class 보다는 Interface를 우선하라.
 - 기존 클래스에도 손쉽게 인터페이스 추가 가능하다.
 - 믹스드인 정의에 안성맞춤이다.
    - 대상 타입의 주된 기능에 부가적인 기능을 정의하는 것을 믹스인이라 한다.
    - 예를 들어 Comparable 은 객체간에 순서가 있을을 정의한다.
 - 인터페이스로 계층구조가 없는 타입 프레임워크를 만들 수 있다.

## 21. 인터페이스는 구현하는 쪽을 생각해 설계하라.

## 22 .인터페이스는 타입을 정의하는 용도로만 사용하라.
 - SomeThingConstants 같은 클래스를 인터페이스로 만들지 말자.

## 23. 태그 달린 클래스보다는 클래스 계층구조를 이용하라.

## 24. 멤버클래스는 최대한 static 으로 만들라.

## 25. Top Level Class는 한 파일에 하나씩만 담아라.

## 26. Raw Type은 사용하지 말라.
- 클래스와 인터페이스 선언에 타입 매개변수가 쓰이면 이를 제네릭 클래스, 제네릭 인터페이스라 한다. 
    -  ex) List 인터페이스는 원소 타입을 나타내는 타입 매개변수 E를 받는다.
    - 그래서 완전한 이름은 List<E> 이다.
    - 통칭해서 제네릭 타입이라 한다.
- 제네릭 타입은 일련의 매개변수화 타입을 정의한다.
    - 예컨데 List<String> 은 원소타입이 String인 리스트를 뜻하는 매개변수화 타입이다.
    - String은 타입 매개변수 E 에 해당하는 실제 타입매개변수이다.
- 제네릭 타입을 정의하면 그에 딸린 Raw Type도 정의된다.
    - 제네릭 타입에서 타입 매개변수를 전혀 사용하지 않을 때를 의미한다.
    - List<E> 의 Raw Type은 List이다.

## 27. 비검사 경고를 제거하라.
- 경고를 제거할 수 없지만 타입이 안전하다고 확신할 수 있다면 @SuppressWarning("unchecked") 애노테이션을 달자.

## 28. Array 보다는 List를 사용하라.
- 배열과 제네릭은 매우 다른 타입 규칙이 적용된다.
- 배열은 공변이고 실제화되는 반면에 제네릭은 불공변이고 타입정보가 소거(Erasure)된다.
- 그 결과 배열은 런타임에 타입 안전하지만 컴파일 타임에는 그렇지 않다.
- 제네릭은 그 반대이디.

## 29. 이왕이면 제네릭 타입으로 만들라.
- 기존 타입에서 제네릭이었어야 하는 게 있다면 제네릭으로 변경하자.

## 30. 이왕이면 제네릭 메소드로 만들라.
~~~ java
public static Set union(Set s1, Set s2) {
    Set result = new HashSet(s1);
    result.addAll(s2);
    return result;
}
~~~

~~~ java
public static <E> Set<E> union(Set<E> s1, Set<E> s2) {
    Set<E> result = new HashSet<E>(s1);
    result.addAll(s2);
    return result;
}
~~~

## 31. 한정적 와일드카드를 사용해 API의 유연성을 높여라.
 - Iterable<? extends E> -> E의 하위타입의 Iterable
 - Collection<? super E> -> E의 상위타입의 Collection
 - PECS : Producer-Extends, Consumer-Super

## 32. 제네릭과 가변인수를 쓸 때는 조심하라.

## 33. 타입 안전 이종 컨테이너를 고려하라.

## 34. int 상수 대신 열거 타입을 사용하라
  - 열거타입은 정수상수보다 뛰어나다.
  - 열거타입에서 상수별 메소드 구현도 가능하다.

## 35. ordinal 메소드 대신 인스턴스 필드를 사용하라.
- Enum타입에는 ordinal() 가 존재한다.
    - 그 열거타입에서 몇 번째에 위치해 있는지를 리턴한다.
- 중간에 하나가 껴들어가면 예상했던 순서의 값과 달라질 수 있다.
- 열거 타입 상수에 연결된 값은 인스턴스 필드에 저장하자.
    - SOLO, DUET
    - SOLO(1), DUET(2)

## 36. 비트 필드 대신에 EnumSet을 사용하라.

## 37. ordinal 인덱싱 대신 EnumMap을 사용하라.
~~~java
public void test() {
    Map<Plant.LifeCycle, Set<Plant>> enumMap = new EnumMap<>(Plant.LifeCycle.class);
    for (Plant.LifeCycle lc : Plant.LifeCycle.values()) {
        enumMap.put(lc, new HashSet<>());
    }
    for (Plant p : garden) {
        enumMap.get(p.lifeCycle).add(p);
    }
}
~~~

## 38. 확장 할 수 있는 열거타입이 필요하면 인터페이스를 사용하라.
 - 열거 타입 자체는 확장 할 수 없지만, 인터페이스와 그 인터페이스를 구현하는 기본 열거 타입을 함께 사용해 같은 효과를 낼 수 있다.

## 39. 명명 패턴보다는 어노테이션을 사용하라.

## 40. @Override 어노테이션을 일관되게 사용하라.

## 41. 정의하려는 것이 타입이라면 마커 인터페이스를 사용하라.
 - 마커 인터페이스, 마커 어노테이션은 각자의 쓰임새가 있다.
 - 새로 추가하는 메서드 없이 단지 타입 정의가 목적이라면 마커 인터페이스를 선택하자.
 - 적용 대상이 ElementType.TYPE인 마커 어노테이션을 작성하고 있다면, 마커 인터페이스가 나은지를 한번더 검토하라.

## 42. 익명 클래스보다는 람다를 사용하라.
 - 작은 함수 객체를 구현하는데 적합한 람다가 도입되었다.
 - 익명 클래스는 (함수형 인터페이스가 아닌) 타입의 인스턴스를 만들때만 사용하라.
 - 낡은 기법.
 ~~~ java
 Collections.sort(words, new Comparator<String>() {
     public int compare(String s1, String s2) {
         return Integer.compare(s1.length(), s2.length());
     }
 });
 ~~~
 - 람다 기법 
 ~~~java
 Collections.sort(words, (s1,s2)-> Integer.compare(d1.length((), s2.length())));
 ~~~

## 43. 람다보다는 메소드 레퍼런스를 이용하라.
  - 메소드 레퍼런스는 람다의 간단명료한 대인이 될 수 있다.
  - 메소드 레퍼런스 쪽이 짧고 명확하다면 메소드 레퍼런스를 쓰는 게 좋다.
  - map.merge(key, 1, (count, incr) -> count + incr);
  - map.merge(key, Integer::sum)

메소드 레퍼런스 유형 | 예시 | 같은 기능을 하는 람다 
--- | --- | --- 
정적 | Integer::parseInt | str -> Integer.parseInt(str)
한정적 (인스턴스) | Instant.now()::isAfter | Instant then = Instant.now(); t -> then.isAfter(t);
비한정적 (인스턴스) | String::toLowerCase | str -> str.toLowerCase(); 
클래스 생성자 | TreeMap<K,V>::new| () -> new TreeMap<K,V>();
배열 생성자 | int[]::new| len -> new int[len] 

## 44. 표준 함수형 인터페이스를 사용하라.
 - 자바가 람다를 지원하면서 API를 작성하는 모범사례도 크게 바뀌고 있다.
 - 예를 들어서 상위 클래스의 기본 메소드를 재정의해서 원하는 동작을 구현하는 템플릿 메소드 패턴의 매력이 크게 줄었다.
 - 이를 대체하는 현대적 방법은 같은 효과의 함수 객체를 받는 정적 팩토리나 생성자를 제공하는 것이다.
 - 함수 객체를 매개변수로 받는 생성자나 메소드를 더 많이 만들어야 한다.
 - java.util.fuction Package에는 다양한 표준 함수형 인터페이스가 담겨있다.
 - 필요한 용도에 맞는게 있다면, 직접 구현하지 말고 표준 함수형 인터페이스를 활용하라.
 - 총 43개의 인터페이스가 있지만 기본인터페이스 6개를 기억하자 
 - 직접 만든 함수형 인터페이스에는 항상 @FunctionalInterface 어노테이션을 사용하라.

 인터페이스 | 함수 시그니처 | 설명 | 예시 
 --- | --- | --- | --- | 
 UnaryOperation<T> | T apply(T t) | 반환값과 인수타입이 같다, 인수 1개 | String::toLowerCase
 BinaryOperation<T> | T apply<T t1, T t2> | 반환타입과 인수타입이 같다, 인수 2개 | BigInteger::add
 Predicate<T>| boolean test(T t) | 인수 하나를 받아 boolean을 반환하는 함수 | Collection::isEmpty
 Function<T> | R apply(T t) | 인수와 반환타입이 다른 함수 | Arrays::asList
 Supplier<T> | T get() | 인수는 없고 반환만 제공하는 함수 | Instant::now
 Consumer<T> | void accept(T t) | 인자만 있고 반환은 없음 | System.out::println

## 45. 스트림은 주의해서 사용하라.
 - 이 API의 추상개념 
    - 스트림은 데이터 원소의 유한 혹은 무한 시퀀스를 뜻한다.
    - 스트림 파이프라인은 이 원소들로 수행하는 연산단계를 표현하는 개념이다.
 - 스트림의 원소들은 어디로부터든 올 수 있다.
 - 대표적으로는 컬렉션, 배열, 파일, 정규표현식 패턴 매처, 난수 생성기, 혹은 다른 스트림이 있다.
 - 기본 타입으로는 int, long, double을 지원한다.
 - 스트림 파이프라인은 Source Stream에서부터 시작해서 Terminal Operation으로 끝나며 그 사이에 하나 이상의 중간 연산이 있을 수 있다.
 - 스트림 파이프라인은 Lazy Evaluation을 진행한다. Terminal Operation이 호출될때 실행이 이루어진다.

## 46. 스트림에서는 부작용 없는 함수를 사용하라 
 - forEach 연산은 스트림 계산결과를 보소할때만 사용하고, 계산하는 데는 쓰지말자.
    - 스트림 종단 연산 중에 기능이 가장 적고, 가장 덜 스트림답다.
 - 스트림을 올바르게 사용하려면 수집기를 잘 알아둬야 한다.
 - 가장 중요한 수집가 팩토리는 toList, toSet, toMap, groupingBy, joining 이다.

## 47. 반환 타입으로는 스트림보다 컬렉션이 낫다.

## 48. 스트림 병렬화는 주의해서 사용하라.
 - 계산도 올바르게 수행하고 성능도 빨라질거라는 확신 없이는 스트림 파이프라인을 사용하지 마라.
 
## 49. 매개변수가 유효한 지 검사하라.
 - Objects.requireNonNull() 는 유연하고 사용하기도 편한다. 더이상 null 검사를 수동으로 하지 않아도 된다.
 
## 50. 적시에 방어적 복사본을 만들어라.
 - 클라이언트가 여러분의 불변식을 깨뜨리려 혈안이 되어 있다고 가정하고 방어적으로 프로그래밍해야한다.
 - Date 클래스는 낡은 API이니 새로운 코드를 작성할 때는 더 이상 사용하면 안된다.
     - LocalDateTime 이나 ZonedDateTime 을 사용해야 한다.
## 51. 메소드 시그니처를 신중히 설계해라.

## 52. 다중 정의는 신중히 사용하라.

## 53. 가변 인수는 신중히 사용하라.

## 54. null 이 아닌 빈 컬렉션이나 배열을 반환하라.

## 55. Optional 반환은 신중히 하라.
 - 기존에는 특정 조건에서 반환 값을 리턴할 수 없을때 null 혹은 Exception을 리턴했다.
 - 자바8에서는 Optional 이 추가되었다.
    - Optional<T>는 null이 아닌 T타입 참조를 하나 담거나, 혹은 아무것도 담지 않을 수 있다.
- 값을 반환하지 못할 가능성이 있고, 호출할 때마다 반환값이 없을 가능성을 염두에 둬야 하는 메소드라면 옵셔널을 반환해야 할 상황일 수 있다.
    - 하지만 옵셔널에는 성능저하가 같이 오니 null, Exception을 고려하자.

~~~java
public static <E extends Comparable<E>> Optional<E> max(Collection<E> c) {
    return c.stream().max(Comparable.naturalOrder());
}

// 옵셔널 활용 1ㄴㅇ
String lastWord = max(words).orElse("단어없음");

// 옵셔널 활용 2
Toy toy = max(toys).orElseThrow(TemperTranException::new);

// 옵셔널 활용 3
Element el = max(Elements.NOBLE_GASES).get();
~~~

## 56. 공개된 API 요소에는 항상 문서화 주석을 작성하라.

## 57. 지역 변수의 범위를 최소화하라.

## 58. 전통적인 for문보다는 for-each 문을 사용하라.

## 59. 라이브러리를 익히고 사용하라.
 - 아래 코드는 문제가 있다.
    - n 이 그리크지 않은 2의 제곱수라면 얼마 지나지 않아 같은 수열이 반복된다.
    - n 이 2의 제곱수가 아니라면 몇몇 숫자가 평균적으로 더 자주 반환된다.
    - n 값이 크면 이 현상은 두드러진다.
 - 1차 개선은 Random.nextInt(n)에서 이루어졌다.
 - 자바 8에서는 ThreadLocalRandom으로 대체하면 대부분 잘 동작한다. (속도도 빠르다)
 - 자바 프로그래머라면 java.lang, java.util, java.io 와 그 하위 패키지는 익숙해야 한다.
    - 컬렉션 라이브러리, 스트림 라이브러리, 동시성 라이브러리 (java.util.concurrent)
- 바퀴를 다시 발명하지 말자.
 ~~~java
Static Random rnd = new Random();
static int random(int n) {
    return Math.abs(rnd.nextInt()) % n;
}
~~~

## 60. 정확한 답이 필요하다면, float, double은 피하라.
 - System.out.println(1.03 - 0.42) = 0.6100000000001을 출력한다.
 - System.out.println(1.00 - 9 * 0.10) = 0.099999999999를 출력한다.
 - 금융쪽 코드에는 BigDecimal을 사용하라.

## 61. 박싱된 기본 타입보다는 기본 타입을 사용하라.

## 62. 다른 타입이 적절하다면 문자열은 피하라.
 - 문자열을 잘못사용하는 흔한 예로는 기본타입, 열거타입, 혼합타입이 있다.

## 63. 문자열 연결은 느리니 주의하자.
 - String보다는 StringBuilder를 쓰자.

## 64. 객체는 인터페이스를 활용해 참조하자.
 - 다형성에 대한 이슈.

## 65.리플렉션보다는 인터페이스를 사용하라.

## 66. 네이티브 메소드는 신중히 사용하라.
  - 네이티브 메소드의 주요 쓰임새 3가지 
    - 레지스트리 같은 플랫폼 특화 기능을 사여ㅛㅇ한다.
    - 네이티브 코드로 작성된 기존 라이브러리를 사용한다.
    - 성능 개선을 목적으로 성능에 결정적인 영향을 주는 영역만 따로 네이티브 언어로 작성한다.
 - 성능을 개선할 목적으로 네이티브 메소드를 사용하는 것은 권장하지 않는다.

## 67. 최적화는 신중히 하라.
  - 빠른 프로그램보다 좋은 프로그램을 작성하라.

## 68. 일반적으로 통용되는 명명규칙을 따라라.

## 69. 예외는 진짜 예외 상황에서만 사용하라.

## 70. 복구할 수 있는 상황에서는 검사 예외, 불가능한 경우는 런타임 예외 활용 

## 71. 필요없는 검사 예외 사용은 피하라.

## 72. 표준 예외를 사용하라,

## 73. 추상화 예외 수준에 맞는 예외를 던져라.

## ... 예외 패스 

## 78. 공유중인 가변 데이터는 동기화 해 사용하라.

## 79. 과도한 동기화는 피하라.

## 80. 스레드보다는 Executor, Task, Stream을 애용하라.

## 81. wait, notify 보다는 동시성 유틸리티를 애용하라.

## 82. 스레드 안정성 수준을 문서화 하라.

## 83. Lazy Initialization은 신중히 사용하라.

## 84. 프로그램 동작을 스레드 스케쥴러에 기대지 말라.

## 85. 자바 직렬화의 대안을 찾으라.

## 86. Serializable을 구현할지는 신중히 선택하라.

## 87. 커스텀 직렬화 형태를 고려해보라.

## 88. readObject() 방어적으로 작성하라.

## 89. 인스턴스 수를 통제해야 한다면 readResolve보다는 열거 타입을 사용하라,

## 90. 직렬화된 인스턴스 대신 직렬화 프록시 사용을 검토하라.

# Reference
- 이펙티브 자바 3판

# History
날짜 | 정리 장수 
--- | --- 
2019-01-02 | 1~7장
2019-01-04 | 8장