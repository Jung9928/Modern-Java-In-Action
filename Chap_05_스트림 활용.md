# Chapter 05. 스트림 활용

### 내용 요약 <br>
#### 1. 스트림 필터링
- 조건에 맞는 요소를 선택하는 연산
- `filter(Predicate)` 메소드를 사용하여 데이터 필터링

<br>

#### 2. 스트림 슬라이싱
- 스트림의 크기를 제한하거나 특정 조건에 따라 종료
    - `limit(n)` 
      - 처음 n개의 요소 반환 <br><br>
      
    - `skip(n)`
      - 처음 n개의 요소 건너뜀 <br><br>
      
    - `takeWhile(predicate)`
      - 조건이 참인 동안 요소를 가져옴 <br><br>
      
    - `dropWhile(predicate)`
      - 조건이 참인 동안 요소를 건너뜀 <br><br>

    
#### 3. 매핑
- 스트림의 각 요소를 반환 <br><br>
  - `map(Function)`
    - 각 요소를 다른 값으로 변환 <br><br>
   
  - `flatMap(Function)`
    - 스트림의 각 요소를 다른 스트림으로 변환 후 병합 <br><br>


#### 4. 검색 및 매칭
- 특정 조건이 스트림의 요소에 부합하는지 확인 <br><br>

  - `anyMatch`
    - 조건에 맞는 요소가 하나라도 있는지. <br><br>
    
  - `allMatch`
    - 모든 요소가 조건에 부합하는지. <br><br>

  - `noneMatch`
    - 조건에 맞는 요소가 없는지. <br><br>


#### 5. 리듀싱
- 모든 요소를 하나의 값으로 결합
- `reduce(BinaryOperator)` 또는 `reduce(identity, BinaryOperator)` 사용. <br><br>


#### 6. 정렬
- `sorted()` 
  - 자연 순서 정렬 <br><br>
  
- `sorted(Comparator)`
  - 사용자 정의 순서로 정렬 


<br><br>


### 스트림 예시
#### 1) 스트림 필터링
```java
import java.util.*;

public class StreamFilterExample {
    public static void main(String[] args) {
        
        List<String> names = List.of("Alice", "Bob", "Charlie", "David");
        
        // 이름이 'C'로 시작하는 요소만 필터링
        names.stream()
                .filter(name -> name.startsWith("C"))
                .forEach(System.out::println);
    }
}

/**
 * 결과
 * 
 * Charlie
 * */

```
<br>

#### 2) 스트림 슬라이싱
```java
import java.util.*;

public class StreamSlicingExample {
    public static void main(String[] args) {
        
        List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8);
        
        // 처음 4개 요소 가져오기
        numbers.stream()
                .limit(4)
                .forEach(System.out::println);

        // 처음 4개 요소 건너뛰기
        numbers.stream()
                .skip(4)
                .forEach(System.out::println);
    }
}

/**
 * 결과
 * // limit(4)
 * 1
 * 2
 * 3
 * 4
 * 
 * // skip(4)
 * 5
 * 6
 * 7
 * 8
 * */
```

<br>

#### 3) 매핑
```java
import java.util.*;

public class StreamMapExample {
    public static void main(String[] args) {
        
        List<String> words = List.of("Java", "Stream", "Example");
        
        // 각 단어의 길이 계산
        words.stream()
                .map(String::length)
                .forEach(System.out::println);
    }
}

/**
 * 결과
 * 4
 * 6
 * 7
 * */
```

<br>

#### 4) 검색 및 매칭
```java
import java.util.*;

public class StreamMatchExample {
    public static void main(String[] args) {
        
        List<String> names = List.of("Alice", "Bob", "Charlie");
        
        // 'A'를 시작하는 이름이 있는지 확인
        boolean anyStartsWithA = names.stream()
                        .anyMatch(name -> name.startsWith("A"));

        // 모든 이름이 3글자 이상인지 확인
        boolean allLongerThan3 = names.stream()
                .allMatch(name -> name.length() > 3);
        
        System.out.println("A로 시작하는 문자열이 있는지 : " + anyStartsWithA);
        System.out.println("3글자 이상인 이름이 있는지 : " + allLongerThan3);
    }
}

/**
 * 결과
 * 
 * A로 시작하는 문자열이 있는지 : true
 * 3글자 이상인 이름이 있는지 : false
 * */
```

<br>

#### 5) 리듀싱
```java
import java.util.*;

public class StreamReduceExample {
    public static void main(String[] args) {
        
        List<Integer> numbers = List.of(1, 2, 3, 4, 5);
        
        // 모든 숫자 더하기
        int sum = numbers.stream()
                        .reduce(0, Integer::sum);
        
        System.out.println("Sum : " + sum);
    }
}

/**
 * 결과
 * Sum : 15
 * */
```

<br>

#### 6) 정렬
```java
import java.util.*;

public class StreamSortExample {
    public static void main(String[] args) {
        
        List<String> names = List.of("Charlie", "Alice", "Bob");
        
        // 순서 정렬
        names.stream()
             .sorted()
             .forEach(System.out::println);
        
        // 사용자 정의 정렬 (역순)
        names.stream()
             .sorted((a, b) -> b.compareTo(a))
             .forEach(System.out::println);
    }
}

/**
 * 결과
 * 
 * // sorted()
 * Alice
 * Bob
 * Charlie
 * 
 * // 사용자 정의 정렬
 * Charlie
 * Bob
 * Alice
 * */
```


<br>

### 스트림 처리의 주요 흐름
```text
Data Source  ->  Intermediate Operations    ->   Terminal Operation
(ex: List)         (ex: filter, map)              (ex: collect)
```

### 중간 연산과 최종 연산
- `중간 연산` : 연결 가능하며 실행 X
- `최종 연산` : 호출되면 모든 연산 실행

