# Chapter 3. 람다 표현식

### 내용 요약 <br>
1. 람다 표현식이란?
- 이름이 없는 함수(익명 함수)를 표현하는 간결한 방법
- Java의 함수형 인터페이스를 활용하여 인스턴스를 생성하는 방식으로 사용됨 <br><br>


2. 기본 문법
```java
(파라미터 목록) -> {함수 본문}
```
- 파라미터 목록
  - 함수에 전달되는 인수, 타입은 생략 가능 <br><br>
    
- 화살표(->)
  - 파라미터와 함수 본문을 구분 <br><br>
  
- 본문 
  - 실행되는 코드. 단일 표현식은 중괄호 `{}` 생략 가능 <br><br>


3. 함수형 인터페이스
- 단 하나의 추상 메소드를 가지는 인터페이스
- Java에서 람다 표현식은 함수형 인터페이스의 구현체로 간주
- ex) `Predicate<T>`, `Consumer<T>`, `Function<T, R>`, `Supplier<T>`.

<br><br>

4. 타입 추론
- 컴파일러가 람다의 파라미터 타입을 추론 가능.
- 명시적 타입 선언 생략 가능

<br><br>

5. 사용 이유
- 코드 간결화
- 익명 클래스의 불필요한 선언 제거
- 함수형 프로그래밍 스타일 지원


<br><br>


### 간단한 람다 표현식
```java
// 두 정수를 더하는  람다
(int a, int b) -> a + b

// 두 문자열 길이를 반환하는 람다
(String s) -> s.length()

// 파라미터 타입 생략
(a, b) -> a * b

```


<br><br>

### 함수형 인터페이스 활용

#### 1) Predicate : 조건을 검사하는 함수
```java
import java.util.*;

public class LambdaExample {
    public static void main(String[] args) {
        List<String> list = List.of("apple", "banana", "cherry");
        
        // 문자열 길이가 5보다 큰지 검사
        Predcate<String> isLong = s -> s.length() > 5;
        
        for(String item : list) {
            if(isLong.test(item)) {
                System.out.println(item + "is long");
            }
        }
    }
}
```

<br>

#### 2) Consumer : 값을 소비하고 결과를 반환하지 않는 함수
```java
import java.util.*;

public class LambdaExample {
    public static void main(String[] args) {
        Consumer<String> printUpperCase = s -> System.out.println(s.toUpperCase());
        printUpperCase.accept("hello world");
    }
}
```

<br>

#### 3) Function : 값을 변환하는 함수
```java
import java.util.*;

public class LambdaExample {
    public static void main(String[] args) {
        Function<Integer, String> intToString = n -> "Number : " + n;
        System.out.println(intToString.apply(10));
    }
}
```

<br>


### 리스트 필터링
```java
import java.util.*;

public class FilterExample {
    
    public static List<String> filterStrings(List<String> list, Predicate<String> predicate) {
        return list.stream()
                .filter(predicate)
                .toList();
    }
    
    public static void main(String[] args) {
        List<String> names = List.of("Alice", "Bob", "Charlie");
        
        // 길이가 4 이상인 문자열 필터링
        List<String> filtered = filterStrings(names, s -> s.length() >= 4);
        System.out.println(filtered);
    }
}
```

<br>

### 람다 표현식과 익명 클래스 비교
```java

// 익명 클래스
new Runnable() {
    public void run() {
        System.out.println("Run");
    }
}

// 람다 표현식
() -> System.out.println("Run");
```

<br><br>


### 정리하자면
1. 동작 파라미터화는 `메소드의 유연성`을 높이고 `코드의 중복을 줄일 수 있는 방법`이며 Java 8 이후 추가된 람다와 스트림 API를 활용하여 더 쉽게 구현 가능하다.




