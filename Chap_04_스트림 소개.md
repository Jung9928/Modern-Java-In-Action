# Chapter 04. 스트림 소개

### 내용 요약 <br>
#### 1. 스트림이란?
- 데이터 처리 연산을 지원하는 `순차적` or `병렬적 데이터 흐름`
- 데이터 소스를 변경하지 않고 데이터를 필터링, 매핑, 집계 등의 연산에 활용
- 함수형 프로그래밍 스타일을 사용하여 선언적이고 간결한 코드 작성 가능

<br>

#### 2. 스트림의 특징
- 선언적
  - 데이터를 처리하는 방법을 코드로 명확히 표현 <br><br>
  
- 파이프라이닝
  - 여러 연산을 연결하여 처리 가능 <br><br>

- 비파괴성
  - 데이터 소스를 변경하지 않음 <br><br>

- 지연 연산 (Lazy Execution)
  - 최종 연산(Terminal Operation)이 호출되기 전까지 연산 수행 안함 <br><br>


#### 3. 스트림의 구성
- 소스(Source)
  - 스트림의 데이터 (ex : `리스트`, `배열`, `파일`) <br><br>
  
- 중간 연산(Intermediate Operation) 
  - 스트림 변환 (ex : `filter`, `map`) <br><br>

- 최종 연산(Terminal Operation)
  - 결과를 생성 (ex : `collect`, `forEach`) <br><br>


<br><br>


### 스트림 예시
#### 1) 스트림 생성
```java
import java.util.*;

public class StreamExample {
    public static void main(String[] args) {
        // 리스트에서 스트림 생성
        List<String> list = List.of("apple", "banana", "cherry");
        Stream<String> stream = list.stream();
        
        // 배열에서 스트림 생성
        String[] array = {"one", "two", "three"};
        Stream<String> arrayStream = Stream.of(array);
        
        // 값에서 직접 생성
        Stream<Integer> numberStream = Stream.of(1, 2, 3, 4, 5);
        
        stream.forEach(System.out::println);
    }
}
```
<br>

#### 2) 중간 연산과 최종 연산 (필터링 : `filter`)
```java
import java.util.*;

public class StreamFilterExample {
    public static void main(String[] args) {
        
        List<String> fruit = List.of("apple", "banana", "cherry", "date");
        
        // 'a'를 포함하는 단어만 필터링
        fruits.stream()
                .filter(fruit -> fruit.contains("a"))
                .forEach(System.out::println);
    }
}

/**
 * 결과
 * apple
 * banana
 * date
 * */
```

<br>

#### 2-1) 중간 연산과 최종 연산 (변환: `map`)
```java
import java.util.*;

public class StreamMapExample {
    public static void main(String[] args) {
        
        List<String> names = List.of("alice", "bob", "charlie");
        
        // 이름을 대문자로 변환
        names.stream()
                .map(String::toUpperCase)
                .forEach(System.out::println);
    }
}

/**
 * 결과
 * ALICE
 * BOB
 * CHARLIE
 * */
```

<br>

#### 2-2) 중간 연산과 최종 연산  (수집: `collect`)
```java
import java.util.*;

public class StreamCollectExample {
    public static void main(String[] args) {
        
        List<String> names = List.of("apple", "banana", "cherry", "date");
        
        // 'a'를 포함하는 단어를 리스트로 수집
        List<String> filtered = names.stream()
                        .filter(name -> name.contains("a"))
                        .collect(Collectors.toList());
        
        System.out.println(filtered);
    }
}

/**
 * 결과
 * [apple, banana, date]
 * */
```

<br>

### 스트림 파이프라인
- 스트림의 데이터 처리는 파이프라인 방식으로 이루어짐.
```java
import java.util.*;

public class StreamPipelineExample {
    public static void main(String[] args) {
        
        List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6);
        
        // 2로 나눠떨어지는 숫자만 필터링한 후, 제곱 값 반환
        numbers.stream()
                .filter(n -> n % 2 == 0)                // 중간 연산
                .map(n -> n * n)                        // 중간 연산
                .forEach(System.out::println);          // 최종 연산
    }
}

/**
 * 결과
 * 4
 * 16
 * 36
 * */
```

<br>

### 스트림 처리 파이프라인
```text
Data Source  ->  Stream   ->   Intermediate Operations    ->   Terminal Operation
(ex: List)     (스트림 생성)        (ex: filter, map)              (ex: collect)
```

### 중간 연산과 최종 연산의 관계
- 중간 연산
  - 데이터를 변환하며 연결 가능 (Lazy Execution)

- 최종 연산
  - 스트림을 종료하고 결과 리턴.



<br><br>


### 정리하자면
1. 동작 파라미터화는 `메소드의 유연성`을 높이고 `코드의 중복을 줄일 수 있는 방법`이며 Java 8 이후 추가된 람다와 스트림 API를 활용하여 더 쉽게 구현 가능하다.




