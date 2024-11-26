# Chapter 06. 스트림으로 데이터 수집

### 내용 요약 <br>
#### 1. Collector란?
- `Collector`는 스트림의 요소를 수집하고 요약하여 하나의 결과를 반환하는 객체
- 주로 `Collectors` 유틸리티 클래스를 사용해 미리 정의된 Collector를 활용.

<br>

#### 2. 주요 수집 방법
- 그룹화 및 분할
    - `groupingBy`
        - 특정 속성을 기준으로 데이터 그룹화. <br><br>
    
- 데이터 변환
  - `toList`, `toSet`, `toMap` : 데이터를 컬렉션 형태로 변환. <br><br>

- 요약 연산
  - `counting`, `summingInt`, `averagingInt` : 데이터의 개수, 합계, 평균 계산.
  - `summarizingInt` : 데이터 요약 통계 생성  <br><br>

- Joining
  - 스트림의 데이터를 문자열로 합치기 <br><br>

#### 3. Mutable Reduction
- 데이터를 `변경 가능한 컨테이너`(ex : List, Map)에 축적하는 방식


<br><br>

### 스트림 예시
#### 1) 그룹화 : `groupingBy`
```java
import java.util.*;

public class GroupingExample {
    public static void main(String[] args) {
        
        List<String> items = List.of("apple", "banana", "cherry", "apple", "banana");
        
        // 이름이 'C'로 시작하는 요소만 필터링
        Map<String, Long> grouped = items.stream()
                .collect(Collectors.groupingBy(
                        item -> item,                   // 그룹화 기준
                        Collectors.counting()           // 각 그룹의 요소 수 계산
                ));
        
        System.out.println(grouped);
    }
}

/**
 * 결과
 * 
 * {banana=2, cherry=1, apple=2}
 * */

```

<br>

#### 2) 그룹화 : `partitioningBy`
```java
import java.util.*;

public class PartitioningExample {
    public static void main(String[] args) {
        
        List<String> numbers = List.of(1, 2, 3, 4, 5, 6);
        
        // 짝수와 홀수로 데이터 분할
        Map<Boolean, List<Integer>> partitioned = numbers.stream()
                .collect(Collectors.partitioningBy(n -> n % 2 == 0));
        
        System.out.println(partitioned);
    }
}

/**
 * 결과
 * 
 * {false=[1, 3, 5], true = [2, 4, 6]}
 * */

```


<br>

#### 3) 데이터 변환 : `toList`, `toSet`, `toMap`
```java
import java.util.*;

public class ToListExample {
    public static void main(String[] args) {
        
        List<String> items = List.of("apple", "banana", "cherry", "apple");
        
        // 데이터를 Set으로 변환 (중복 제거)
        Set<String> uniqueItems = items.stream()
                .collect(Collectors.toSet());
        
        System.out.println(uniqueItems);
    }
}

/**
 * 결과
 * [banana, cherry, apple]
 * */
```

<br>

#### 4) 요약 연산 : `summarizingInt`
```java
import java.util.*;

public class SummarizingExample {
    public static void main(String[] args) {
        
        List<String> numbers = List.of(1, 2, 3, 4, 5, 6);
        
        // 요약 통계 계산
        IntSummaryStatistics stats = numbers.stream()
                .collect(Collectors.summarizingInt(n -> n));

        System.out.println(stats);
    }
}

/**
 * 결과
 * 
 * IntSummaryStatistics{count=5, sum=15, min=1, average=3.000000, max=5}
 * */
```

<br>

#### 5) 문자열 합치기 : `joining`
```java
import java.util.*;

public class JoiningExample {
    public static void main(String[] args) {
        
        List<String> words = List.of("Java", "Stream", "Collector");
        
        // 문자열을 쉼표로 구분하여 합치기
        String result = words.stream()
                        .collect(Collectors.joining(", "));
        
        System.out.println(result);
    }
}

/**
 * 결과
 * Java, Stream, Collector
 * */
```

<br>

#### 6) 사용자 정의 수집기 : `toMap`
```java
import java.util.*;

public class ToMapExample {
    public static void main(String[] args) {
        
        List<String> words = List.of("apple", "banana", "cherry");
        
        // 각 단어와 길이를 맵으로 수집
        Map<String, Integer> wordLengths = words.stream()
                        .collect(Collectors.toMap(
                                word -> word,               // 키
                                word -> word.length()       // 값
                        ));
        
        System.out.println(wordLengths);
    }
}

/**
 * 결과
 * 
 * {apple=5, banana=6, cherry=6}
 * */
```


<br>

### Collectors의 주요 기능
```text
Stream
  |
  |--- Collectors.groupingBy     -> Map (그룹화)
  |--- Collectors.partitioningBy -> Map<Boolean, List> (분할)
  |--- Collectors.toList         -> List (컬렉션 변환)
  |--- Collectors.summarizingInt -> IntSummaryStatistics (요약)
  |--- Collectors.joining        -> String (문자열 합치기)
```

### 그룹화와 분할의 차이
<table><thead>
  <tr>
    <th>그룹화(groupingBy)</th>
    <th>분할(partitioningBy)</th>
  </tr></thead>
<tbody>
  <tr>
    <td>여러 그룹으로 나뉨</td>
    <td>조건에 따라 두 그룹 (true/false)로 나뉨</td>
  </tr>
  <tr>
    <td>키가 다중 값일 수 있음</td>
    <td>키는 true 또는 false만 가질 수 있음</td>
  </tr>
</tbody>
</table>

