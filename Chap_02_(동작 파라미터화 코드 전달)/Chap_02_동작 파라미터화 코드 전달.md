# Chapter 2 : 동작 파라미터화 코드 전달하기

### 내용 요약 <br>
1. 동작 파라미터화란?
  - 메소드 내부에서 실행될 코드를 매 번 바꾸기 위해 하드코딩하지 않고, 메소드를 호출하는 시점에 특정 동작을 전달하는 방식
  - 동작을 파라미터화하면 코드의 재사용성과 유연성이 증가 <br><br>

2. 적용 조건
  - 다양한 조건을 기준으로 데이터를 필터링해야 하는 경우, 조건마다 새로운 메소드를 작성하는 것은 비효율적이고 중복된 코드를 초래할 수 있음. <br>
    -> ex) 사과 리스트를 색깔, 무게, 크기 등의 조건으로 필터링 시, 이를 동작 파라미터화로 해결 가능 <br><br>


3. 동작을 코드로 전달하는 방식
  - Java 8 이전에는 익명 클래스를 사용했지만, Java 8 이후부터는 `람다 표현식`과 `메소드 참조`를 활용하여 더 간결하고 직관적으로 구현 가능 <br><br>


<br><br>


### 예시 : 사과 필터링 (기존방식 -> 비효율적)
```java
import java.util.*;

public static List<Apple> filterGreenApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    
    for(Apple apple : inventory) {
        if("green".equals(apple.getColor())) {
            result.add(apple);
        }
    }
    return result;
}

public static List<Apple> filterHeavyApples(List<Apple> inventory) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(apple.getWeight() > 150) {
                result.add(apple);
        }  
    }
    return result;
}
```
위 코드는 중복 코드가 발생하고 조건이 추가되면 새로운 메소드가 계속 필요함

<br><br>

### 동작 파라미터화 방식 (Predicate 인터페이스 활용)
```java
import java.util.*;

public static List<Apple> filterApples(List<Apple> inventory, Predicate<Apple> predicate) {
    List<Apple> result = new ArrayList<>();
    for(Apple apple : inventory) {
        if(predicate.test(apple)){
            result.add(apple);
        }
    }
    return result;
}

public static void main(String[] args) {
    List<Apple> inventory = List.of(
            new Apple("green", 180),
            new Apple("red", 120),
            new Apple("green", 150),
    );    
    
    // 녹색 사과 필터링
    List<Apple> greenApples = filterApples(inventory, apple -> "green".equals(apple.getColor()));
    System.out.println("Green apples : " + greenApples);
    
    // 무거운 사과 필터링
    List<Apple> heavyApples = filterApples(inventory, apple -> apple.getWeight() > 150);
    System.out.println("Heavy apples : " + heavyApples);
}
```
- `Predicate 인터페이스`는 조건을 표현하는데 적합하고 `filterApples`메소드는 동작(조건)을 `Predicate<Apple>`로 전달받아 재사용 가능한 필터링 로직을 제공함.
- 조건을 람다 표현식으로 간결하게 전달할 수 있어 가독성이 크게 향상됨
- 필터 조건이 바뀌어도 동일한 메소드(filterApples)를 재사용 가능


<br><br>


### 정리하자면
1. 동작 파라미터화는 `메소드의 유연성`을 높이고 `코드의 중복을 줄일 수 있는 방법`이며 Java 8 이후 추가된 람다와 스트림 API를 활용하여 더 쉽게 구현 가능하다.




