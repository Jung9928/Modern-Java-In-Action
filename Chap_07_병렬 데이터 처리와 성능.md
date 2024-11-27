# Chapter 07. 병렬 데이터 처리와 성능

### 내용 요약 <br>
#### 1. 병렬 스트림(Parallel Stream) 이란?
- 스트림을 병렬 처리하여 멀티코어 CPU를 활용
- `parallelStream()` 또는 `stream().parallel()` 메소드를 사용해 생성
- 작업을 여러 쓰레드로 나누어 실행하므로 성능 향상이 가능

<br>

#### 2. 병렬 스트림의 동작 원리
- 내부적으로 `Fork/Join 프레임워크` 사용
- 입력 데이터를 `작은 청크(chunk)로 나누어` 병렬로 처리
- 최종적으로 결과를 `병합`하여 반환.

<br><br>

#### 3. 병렬 스트림의 장점
- 대용량 데이터 처리 시, 성능 향상 가능
- 멀티코어 시스템에서 효율적인 CPU 사용

<br><br>

#### 4. 병렬 스트림의 단점 및 주의 사항
- `오버헤드`
  - 병렬 처리 설정 비용 & 쓰레드 관리 비용 <br><br>

- `데이터 크기`
  - 데이터가 작을 경우 병렬 처리 이점 없음 <br><br>

- `데이터 의존성`
  - 병렬 처리는 순서에 의존하지 않는 작업에 적합 <br><br>

- `공유 자원`
  - 병렬 처리는 상태를 공유하지 않는 불변 데이터에 적합 

<br><br>


### 병렬 스트림 예시
#### 1) 기본 병렬 스트림 사용
```java
import java.util.*;

public class ParallelStreamExample {
    public static void main(String[] args) {
        
        // 1부터 1,000,000까지 숫자 합계 계산 (병렬 스트림 사용)
        long sum = IntStream.rangeClosed(1, 1_000_000)
                        .parallel()             // 병렬 스트림 활성화
                        .sum();
        
        System.out.println("Sum : " + sum);
    }
}

/**
 * 결과
 * 
 * Sum: 500000500000
 * */

```

<br>

#### 2) 순차 스트림 vs 병렬 스트림 비교
```java
import java.util.*;

public class SequentialVsParallel {
    public static void main(String[] args) {
        
        // 순차 스트림
        long start = System.currentTimeMillis();
        IntStream.rangeClosed(1, 1_000_000).sum();
        long sequentialTime = System.currentTimeMillis() = start;
        
        // 병렬 스트림
        start = System.currentTimeMillis();
        IntStream.rangeClosed(1, 1_000_000).parallel().sum();
        long parallelTime = System.currentTimeMillis() - start;
        
        System.out.println("Sequential Time : " + sequentialTime + "ms");
      System.out.println("Parallel Time : " + parallelTime + "ms");
    }
}

/**
 * 결과
 * 
 * Sequential Time : 15ms
 * Parallel Time : 5ms
 * */

```
- 병렬 스트림이 더 빠른 이유는 여러 쓰레드가 동시에 작업을 수행하기 때문임. <br>
  하지만 데이터가 작거나 작업이 간단할 경우, 병렬화로 인한 오버헤드 때문에 순차 스트림이 더 효율적일 수 있음.

  
<br>

#### 3) 잘못된 병렬처리 : 상태 공유 문제
```java
import java.util.*;

public class ParallelStreamIssue {
    public static void main(String[] args) {
        
        List<Integer> list = new ArrayList<>();
        
        // 병렬 스트림에서 상태를 공유하면 문제 발생
        IntStream.rangeClosed(1, 1000)
                .parallel()
                .forEach(list::add);                // 상태 공유 문제 발생
      
        System.out.println("Size of list : " + list.size());    // 예상 값: 1000
    }
}

/**
 * 결과
 * Size of list : 993
 * */
```
- 병렬 스트림에서는 `공유 상태`를 변경하는 작업(ex : `list.add()`)이 안전하지 않다.) <br>
  이를 해결하려면 병렬 처리에서 상태를 공유하지 않거나, `collect`메소드를 사용하여 불변 데이터를 다뤄야 한다.

<br>

#### 4) 병렬 수집 작업
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

#### 5) 병렬 스트림의 적합성 판단
```java
import java.util.*;

public class ParallelStreamPerformance {
    public static void main(String[] args) {
        
        // 데이터 크기가 작은 경우
        measurePerformance(10);
        
        // 데이터 크기가 큰 경우
        measurePerformance(1_000_000);
    }
    
    private static void measurePerformance(int size) {
        long start, sequentialTime, parallelTime;
        
        start = System.currentTimeMillis();
        IntStream.rangeClosed(1, size).sum();
        sequentialTime = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        IntStream.rangeClosed(1, size).parallel().sum();
        parallelTime = System.currentTimeMillis() - start;
        
        System.out.println("Size : " + size);
        System.out.println("Sequential : " + sequentialTime + "ms");
        System.out.println("Parallel : " + parallelTime + "ms");
        System.out.println();
    }
}

/**
 * 결과
 * 
 * Size : 10
 * Sequential : 0ms
 * Parallel : 3ms
 * 
 * Size : 1000000
 * Sequential : 12ms
 * Parallel : 4ms
 * */
```
- 데이터 크기가 작으면 병렬화의 오버헤드 때문에 오히려 성능이 저하됨. <br>
  대규모 데이터 처리에 병렬 스트림을 사용하는 것이 더 적합.

<br>

### 병렬 스트림 처리의 주요 흐름
```text
Input Data   ->    Split    ->   Process (in parallel)   ->   Combine   ->   Output Data
```

### 병렬 스트림과 순차 스트림의 비교
- `순차 스트림` : 데이터를 한 번에 하나 씩 처리
- `병렬 스트림` : 데이터를 여러 청크로 나누어 여러 쓰레드가 동시에 처리


