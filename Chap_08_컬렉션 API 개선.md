# Chapter 08. 컬렉션 API 개선

### 내용 요약 <br>
#### 1. Java 8에서 컬렉션 API의 새로운 기능
- `forEach` 메소드
  - Iterable 인터페이스에 추가되어 컬렉션의 요소를 순회하는 간단한 방법 제공

- `removeIf` 메소드
  - 조건에 맞는 요소를 쉽게 제거

- `replaceAll` 메소드
  - 리스트의 모든 요소를 일괄적으로 변경
  
- `sort` 메소드
  - Comparator를 이용해 리스트를 정렬

- `Map` 관련 새로운 메소드
  - `getOrDefault`
  - `compute`, `computeIfAbsent`, `computeIfPresent`
  - `merge`

<br>

#### 2. 컬렉션과 스트림의 통합
- 컬렉션에서 `stream()` 메소드를 통해 스트림을 생성하여 함수형 스타일로 데이터 처리
- 컬렉션의 요소를 필터링, 매핑, 수집하는 작업이 간단해짐


<br><br>


### 주요 메소드와 예제
#### 1) `forEach` 메소드
```java
import java.util.*;

public class ForEachExample {
    public static void main(String[] args) {
        
        List<String> items = Arrays.asList("apple", "banana", "cherry");
        
        // 람다를 사용하여 각 요소 출력
        items.forEach(item -> System.out.println(item));
    }
}

/**
 * 결과
 * 
 * apple
 * banana
 * cherry
 * */

```

<br>

#### 2) `removeIf` 메소드
- 컬렉션에서 특정 조건을 만족하는 요소를 제거
```java
import java.util.*;

public class RemoveIfExample {
    public static void main(String[] args) {
        
        List<Integer> numbers = new ArrayList<>(List.of(1, 2, 3, 4, 5, 6));
        
        // 짝수를 제거
        numbers.removeIf(n -> n % 2 == 0);
        
        System.out.println(numbers);
    }
}

/**
 * 결과
 *
 * [1, 3, 5]
 * */

```

<br>

#### 3) `replaceAll` 메소드
- 리스트의 모든 요소를 변경
```java
import java.util.*;

public class ParallelStreamIssue {
    public static void main(String[] args) {

        List<String> items = new ArrayList<>(List.of("apple", "banana", "cherry"));
        
        // 모든 문자열을 대문자로 변환
        items.replaceAll(String::toUpperCase);
        
        System.out.println(items);
    }
}

/**
 * 결과
 * [APPLE, BANANA, CHERRY]
 * */
```
- 병렬 스트림에서는 `공유 상태`를 변경하는 작업(ex : `list.add()`)이 안전하지 않다.) <br>
  이를 해결하려면 병렬 처리에서 상태를 공유하지 않거나, `collect`메소드를 사용하여 불변 데이터를 다뤄야 한다.

<br>

#### 4) `sort` 메소드 
- 리스트를 지정된 `Comparator`를 사용하여 정렬.
```java
import java.util.*;

public class ParallelStreamCollect {
    public static void main(String[] args) {
        
        List<Integer> list = IntStream.rangeClosed(1, 1000)
                .parallel()
                .boxed()
                .collect(Collectors.toList());
        
        System.out.println("Size of list : " + list.size());    // 올바른 값 : 1000
    }
}

/**
 * 결과
 * 
 * Size of list : 1000
 * */
```
- 병렬 스트림에서는 `Collector`를 사용하여 데이터를 수집하면 상태 공유 문제를 방지할 수 있음


<br>

#### 5) `Map`의 새로운 메소드
```java
import java.util.*;

public class GetOrDefaultExample {
    public static void main(String[] args) {
        
        Map<String, String> map = new HashMap<>();
        map.put("key1", "value1");
        
        // 기본 값 변환
        String value = map.getOrDefault("key2", "defaultValue");
        System.out.println(value);
    }
}

/**
 * 결과
 * defaultValue
 * */
```
```java
import java.util.*;

public class ComputeIfAbsentExample {
    public static void main(String[] args) {
        
        Map<String, Integer> map = new HashMap<>();
        
        // 키가 없으면 새 값을 생성하여 추가
        map.computeIfAbsent("key1", k -> k.length());
        System.out.println(map);
    }
}

/**
 * 결과
 * {key1=4}
 * */
```
```java
import java.util.*;

public class MergeExample {
    public static void main(String[] args) {
        
        Map<String, Integer> map = new HashMap<>();
        map.put("key1", 1);
        
        // 값 병합
        map.merge("key1", 2, Integer::sum);
        map.merge("key2", 3, Integer::sum);
        
        System.out.println(map);
    }
}

/**
 * 결과
 * {key1=3, key2=3}
 * */
```
<br>


### 컬렉션과 스트림의 통합 예제
#### 1) 스트림 생성
```java
import java.util.*;

public class StreamFromCollection {
    public static void main(String[] args) {

        List<String> items = List.of("apple", "banana", "cherry");
        
        // 스트림 생성 및 필터링
        items.stream()
                .filter(item -> item.startsWith("a"))
                .forEach(System.out::println);
    }
}

/**
 * 결과
 * apple
 * */
```

<br>

### 컬렉션 API 개선 사항
```text
컬렉션               주요 개선 메소드
List / Set         forEach, removeIf, replaceAll, sort
Map                getOrDefault, compute, computeIfAbsent, merge
```

<br>

### 스트림과 컬렉션의 통합 흐름
```text
Collection -> Stream
           -> 중간 연산 (filter, map)
           -> 최종 연산 (collect, forEach)
```