# Chapter 09. 리팩터링, 테스팅, 디버깅

### 내용 요약 <br>
#### 1. 리팩터링
- 가독성과 유지보수성을 개선하며, 기존 기능을 변경하지 않고 코드를 더 나은 구조로 변경

- `중요 포인트`
    - 코드 중복 제거
    - 명령형 스타일에서 함수형 스타일로 변환
    - 람다와 메소드 참조 활용
    - 스트림 API를 사용해 데이터 처리 코드 간소화

<br>

#### 2. 테스팅
- 함수형 프로그래밍의 특성으로 인해 테스트가 더 용이
- `순수 함수` : 입력이 같으면 항상 동일한 출력 반환 -> 예측 가능성과 테스트 용이성 향상
- 테스트 작성 시, `경계 조건`과 `예외 처리`를 포함해 다양한 시나리오를 고려

<br>

#### 3. 디버깅
- 스트림이나 람다를 디버깅하는 것은 전통적인 명령형 코드보다 어려울 수 있음
- 해결 방안
  - 스트림 중간 연산에서 `peek()`메소드를 사용해 디버깅
  - IDE를 활용해 람다와 스트림을 디버깅

<br><br>


### 리팩토링 : 명령형에서 함수형으로 변환
#### 1) 명령형 스타일 코드
```java
import java.util.*;

public class ImperativeToFunctional {
    public static void main(String[] args) {
        
        List<String> names = List.of("Alice", "Bob", "Charlie", "David");
        List<String> result = new ArrayList<>();
        
        for(String name : names) {
            if(name.length() > 3) {
                result.add(name.toUpperCase());
            }
        }
        
        System.out.println(result);
    }
}

/**
 * 결과
 *
 * [ALICE, CHARLIE, DAVID]
 * */
```
<br>

#### 2) 함수형 스타일 코드
```java
import java.util.*;

public class FunctionalStyle {
    public static void main(String[] args) {
        
        List<String> names = List.of("Alice", "Bob", "Charlie", "David");
        List<String> result = names.stream()
                .filter(name -> name.length() > 3)
                .map(String::toUpperCase)
                .collect(Collectors.toList());
        
        System.out.println(result);
    }
}

/**
 * 결과
 *
 * [ALICE, CHARLIE, DAVID]
 * */
```
- 함수형 스타일은 코드가 간결하고 읽기 쉬움
- `filter`와 `map`을 사용하여 명확한 데이터 처리 흐름을 작성

<br>

### 테스트 : 순수 함수의 예제
#### 1) 테스트하기 쉬운 순수 함수
```java
public class Calculator {
    public static int add(int a, int b) {
        return a+b;
    }
    
    public static int subtract(int a, int b) {
        return a-b;
    }
}

/**
 * 결과
 *
 * [ALICE, CHARLIE, DAVID]
 * */
```
<br>

#### 2) JUnit 테스트
```java
import org.junit.jupiter.api.Test;
import static org.junit.jupiter.api.Assertions.assertEquals;

public class FunctionalStyle {
    @Test
    void testAdd() {
        assertEquals(5, Calculator.add(2, 3));
    }

  @Test
  void testSubtract() {
    assertEquals(1, Calculator.subtract(3, 2));
  }
}

/**
 * 결과
 *
 * [ALICE, CHARLIE, DAVID]
 * */
```
- `add`와 `subtract`는 입력이 동일하면 항상 동일한 결과를 반환하는 순수 함수
- 테스트 작성과 유지보수가 용이

<br>

### 디버깅 : `peek()` 활용
#### 1) 문제 상황
```java
import java.util.*;

public class DebuggingStream {
    public static void main(String[] args) {
        
        List<String> names = List.of("Alice", "Bob", "Charlie", "David");
        
        List<String> result = names.stream()
                .filter(name -> name.length() > 3)
                .map(String::toUpperCase)
                .toList();
        
        System.out.println(result);
    }
}

/**
 * 결과
 *
 * [ALICE, CHARLIE, DAVID]
 * */
```
<br>

#### 2) 해결방안 : `peek()` 추가
```java
import java.util.*;

public class DebuggingStream {
    public static void main(String[] args) {

      List<String> names = List.of("Alice", "Bob", "Charlie", "David");

      List<String> result = names.stream()
              .peek(name -> System.out.println("Original : " + name))
              .filter(name -> name.length() > 3)
              .peek(name -> System.out.println("Filtered : " + name))
              .map(String::toUpperCase)
              .peek(name -> System.out.println("Mapped : " + name))
              .toList();
              
      System.out.println(result);
    }
}

/**
 * 결과
 *
 * Original: Alice
 * Filtered: Alice
 * Mapped: ALICE
 * Original: Bob
 * Original: Charlie
 * Filtered: Charlie
 * Mapped: CHARLIE
 * Original: David
 * Filtered: David
 * Mapped: DAVID
 * [ALICE, CHARLIE, DAVID]
 * */
```
- `peek()`는 스트림의 각 단계에서 요소를 확인하는 데 유용
- 디버깅 목적으로만 사용해야 하며, 최종 코드에서는 제거하는 것이 권장됨



<br><br>

### 리팩토링 흐름
```text
명령형 코드   ->   함수형 코드
반복문       ->   스트림 API
조건문       ->   filter
변환         ->   map
```

<br>

### 스트림과 컬렉션의 통합 흐름
```text
스트림 생성   ->    peek()   ->    중간 연산   -> 최종 연산
```