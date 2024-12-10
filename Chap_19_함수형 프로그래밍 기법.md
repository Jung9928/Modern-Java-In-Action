# Chapter 19. 함수형 프로그래밍 기법

### 내용 요약 <br>
#### 1. 커링(Currying)
- 여러 개의 매개변수를 받는 함수(f(a,b))를 일련의 단일 매개변수를 받는 함수(f(a)(b))로 변환
- 함수 재사용성과 조합성을 높임
- Java에서는 람다 표현식과 `Function` 인터페이스를 활용해 구현 가능

<br>


#### 2. 함수 합성 (Composition)
- 두 함수를 결합하여 새로운 함수를 생성
- Java의 `Function` 인터페이스에서 제공하는 `andThen` 및 `compose` 메소드로 구현
- 복잡한 로직을 작은 함수 단위로 나누고 결합하여 단순화

<br>

#### 3. 패턴 매칭 (Pattern Matching)
- 데이터 구조를 분석하고 조건에 따라 다른 로직을 실행
- Java는 기본적으로 패턴 매칭을 제공하지 않지만, `switch`구문을 활용하거나 외부 라이브러리 사용

<br>

#### 4. 모나드 (Monad)
- 계산을 캡슐화하고 조합 가능성을 제공하는 컨테이너
- Java의 `Optional` 및 `CompletableFuture`가 모나드의 예

<br>

### 5. 재귀와 꼬리 재귀
- 반복 대신 재귀로 문제 해결
- 꼬리 재귀 최적화를 통해 효율성을 유지

<br>

### 6. 커링 (Currying)
```java
import java.util.*;

public class CurryingExample {
    public static void main(String[] args) {
        
        Function<Integer, Function<Integer, Integer>> add = a -> b -> a + b;
        
        Function<Integer, Integer> add5 = add.apply(5);         // a = 5
        
        System.out.println(add5.apply(3));                      // b = 3, 결과 : 8
    }
}

/**
 * 결과
 *
 * 8
 * */
```
- `add`는 커링된 함수로, 두 단계로 인자를 처리
- `add5`는 첫 번째 인자로 5를 고정하여 새로운 함수를 생성

<br>

### 7. 함수 합성 (Composition)
```java
import java.util.*;

public class CompositionExample {
    
    public static void main(String[] args) {
        Function<Integer, Integer> square = x -> x * x;
        Function<Integer, Integer> doubleIt = x -> x * 2;
      
        // compose : double -> square
        Function<Integer, Integer> composed = square.compose(doubleIt);
        System.out.println(composed.apply(3));            // (3 * 2) ^ 2 = 36
        
        // andThen : square -> double
        Function<Integer, Integer> andThen = square.andThen(doubleIt);
        System.out.println(andThen.apply(3));            // (3^2) * 2 = 18
    }
}

/**
 * 결과
 *
 * 36
 * 18
 * */
```
- `compose` : 먼저 `doubleIt`을 적용하고 그 결과를 `square`에 전달
- `andThen` : 먼저 `square`를 적용하고 그 결과를 `doubleIt`에 전달

<br>


### 8. Java 17 `switch` 패턴 매칭 예시
```java
public class PatternMatchingExample {
    
    public static void main(String[] args) {
        Object obj = "Hello";
        
        String result = switch (obj) {
            case String s -> "It's a String : " + s;
            case Integer i -> "It's an Integer : " + i;
            default -> "Unknown type";
        };
        
        System.out.println(result);
    }
}

/**
 * 결과
 * 
 * It's a String : Hello
 * */
```
- `switch` 구문에서 객체 타입에 따라 다르게 처리
- Java 17부터는 패턴 매칭 스타일로 더 간결한 `switch`사용 가능

<br>

### 9. 모나드(Monad) 예시 : `Optional`
```java
public class MonadExample {
    public static void main(String[] args) {
      Optional<String> maybeValue = Optional.of("Hello");
      
      String result = maybeValue
              .map(String::toUpperCase)             // "HELLO"
              .orElse("Default Value");
      
      System.out.println(result);                   // 결과 : HELLO
    }
}

/**
 * 결과
 *
 * HELLO
 * */
```
- `Optional`은 값의 존재 여부를 캡슐화
- `map`과 `orElse`를 통해 안전한 값 조작

<br>


### 10. 재귀와 꼬리 재귀
```java
public class TailRecursionExample  {
    public static void main(String[] args) {
      System.out.println(factorial(5, 1));       // 결과 : 120
    }
    
    public static int factorial(int n, int acc) {
        if(n == 1) return acc;
        return factorial(n-1, n * acc);
    }
}

/**
 * 결과
 *
 * 120
 * */
```
- 꼬리 재귀로 구현하여 스택 오버플로우 방지
- 반복적인 상태 저장 없이 효율적으로 처리

<br>

### 11. 커링과 함수 합성의 시각화
#### 1) 커링
```text
add(a, b) -> add(a)(b)
```

<br>

#### 2) 함수 합성
```text
square(x) -> doubleIt(y) -> composed(z) = square(doubleIt(x))
```

<br>

### 정리하자면
1. `커링` : 여러 매개변수를 단계적으로 처리
2. `함수 합성` : 작은 함수를 결합해 복잡한 로직을 구성
3. `패턴 매칭` : 데이터 구조에 따라 다르게 처리
4. `모나드` : 계산을 캡슐화하여 안전하고 선언적으로 처리
5. `재귀와 꼬리재귀` : 반복 처리를 함수형 방식으로 구현