# Chapter 18. 함수형 관점으로 생각하기

### 내용 요약 <br>
#### 1. 함수형 프로그래밍의 기본 원칙
- `순수 함수` : 같은 입력에는 항상 같은 출력을 반환하며, 부작용이 없음
- `불변성` : 상태를 변경하지 않고 데이터를 복사하거나 새로운 데이터를 생성
- `고차 함수` : 함수를 인자로 받거나 반환하는 함수
- `참조 투명성` : 식을 동일한 값으로 교체해도 프로그램의 동작에 영향을 주지 않음

<br>


#### 2. 함수형 프로그래밍과 명령형 프로그래밍 비교
- `명령형 프로그래밍` : 상태와 명령어를 통해 문제를 해결
- `함수형 프로그래밍` : 선언적으로 문제를 해결하며 상태 변경을 피함

<br>

#### 3. Java에서의 함수형 프로그래밍
- 람다 표현식과 스트림 API를 통해 함수형 프로그래밍 스타일 구현
- `java.util.function` 패키지의 함수형 인터페이스 활용
  - `Function`, `Predicate`, `Consumer`, `Supplier` 등 <br><br>

- 재귀를 활용한 반복 처리

<br>

#### 4. 재귀와 꼬리 재귀
- 함수형 프로그래밍에서 루프 대신 재귀를 사용
- `꼬리 재귀 최적화` : 재귀 호출이 스택 오버플로우를 방지하도록 최적화

<br>

### 5. 순수 함수와 참조 투명성
```java
public class FunctionalExample {
    
    // 순수 함수 : 입력이 동일하면 출력이 항상 동일
    public static int add(int a, int b) {
        return a + b;
    }
    
    public static void main(String[] args) {
        System.out.println(add(2, 3));      // 항상 5 출력
    }
}
```
- `add` : 상태를 변경하지 않으며 부작용이 없음
- 같은 입력에는 항상 같은 출력 반환 -> 참조 투명성

<br>

### 6. 불변성과 데이터 처리
```java
import java.util.*;

public class ImmutableExample {
    public static void main(String[] args) {
        
        List<String> names = List.of("Alice", "Bob", "Charlie");
        
        // 원본 리스트는 변경되지 않음
        List<String> upperCaseNames = names.stream()
                .map(String::toUpperCase)
                .collect(Collectors.toList());
        
        System.out.println("Original : " + names);
        System.out.println("Modified : " + upperCaseNames);
    }
}

/**
 * 결과
 *
 * Original: [Alice, Bob, Charlie]
 * Modified: [ALICE, BOB, CHARLIE]
 * */
```
- `names` : 리스트는 불변성 유지
- 새로운 리스트 (upperCaseNames)는 원본 데이터에 영향을 주지 않고 생성

<br>

### 7. 고차 함수 활용
```java
import java.util.*;

public class HighOrderFunctionExample {
    
    public static void main(String[] args) {
      Function<Integer, Integer> square = x -> x * x;
      Function<Integer, Integer> doubleIt = x -> x * 2;
      
      // 고차 함수로 합성
      Function<Integer, Integer> combined = square.andThen(doubleIt);
      
      System.out.println(combined.apply(3));            // (3 * 3) * 2 = 18
    }
}

/**
 * 결과
 *
 * 18
 * */
```
- `Function` : 인터페이스를 활용해 함수를 합성
- `andThen` : 함수 결과를 연결

<br>


### 8. 재귀와 꼬리 재귀
```java
public class RecursiveExample {
    
    public static void main(String[] args) {
      System.out.println(factorial(5));   // 120
    }
    
    // 재귀 함수로 팩토리얼 구현
    public static int factorial(int n) {
        if(n == 1) 
            return 1;
        
        return n * factorial(n - 1);
    }
}
```

<br>

### 8-1. 꼬리 재귀 최적화
```java
public class TailRecursiveExample {
    public static void main(String[] args) {
      System.out.println(factorial(5, 1));   // 120
    }
    
    // 재귀 함수로 팩토리얼 구현
    public static int factorial(int n, int acc) {
        if(n == 1) 
            return acc;
        
        return factorial(n - 1, n * acc);
    }
}
```
- 일반 재귀는 스택 사용이 증가
- 꼬리 재귀는 호출 결과를 즉시 리턴하여 최적화 가능

<br>


### 9. 함수형 프로그래밍의 특징
```text
[ Immutable Data ] --> [ Pure Functions ] --> [ No Side Effects ]
```

<br>

### 10. 명령형 vs 함수형 코드 비교
#### 1) 명령형 프로그래밍
```text
for (int i = 0; i < list.size(); i++) {
    result.add(list.get(i).toUpperCase());
}
```

<br>

#### 2) 함수형 프로그래밍
```text
list.stream()
    .map(String::toUpperCase)
    .collect(Collectors.toList());
```
