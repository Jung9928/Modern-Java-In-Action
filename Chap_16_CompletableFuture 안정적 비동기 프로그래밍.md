# Chapter 16. CompletableFuture 안정적 비동기 프로그래밍

### 내용 요약 <br>
#### 1. `CompletableFuture`와 비동기 프로그래밍
- 비동기 작업을 체계적으로 처리 <br><br>
- 스레드 블로킹을 최소화하여 시스템 성능 최적화 <br><br>
- `CompletableFuture`의 기능
    - 작업 체이닝 : `thenApply`, `thenCompose`
    - 작업 병렬 처리 : `allOf`, `anyOf`
    - 결과 조합 : `thenCombine`, `thenAcceptBoth`
    - 에러 처리 : `exceptionally`, `handle`

<br>

#### 2. 안정적 비동기 프로그래밍의 원칙
- 작업의 `캡슐화` : 비즈니스 로직과 비동기 처리 로직 분리
- 에러 처리 `통합` : 작업 실패 시, 적절한 복구 매커니즘
- 작업 `타임아웃` : 무한 대기 방지
- `병렬 처리 최적화` : `ForkJoinPool` 활용

<br>

#### 3. 패턴 및 응용
- 복잡한 비동기 작업 조합
- 비동기 API를 활용한 데이터 처리
- 여러 작업의 `동시 실행`과 결과 수집

<br>


### 4. 간단한 비동기 연산
```java
import java.util.*;

public class SimpleAsyncExample {
    public static void main(String[] args) {
        CompletableFuture.supplyAsync(() -> {
            System.out.println("Task running in a separate thread.");
            return "Data";
        }).thenApply(data -> {
            System.out.println("Processing : " + data);
            return data.toUpperCase();
        }).thenAccept(result ->  {
            System.out.println("Result : " + result);
        });
        
        System.out.println("Main thread is free!");
    }
}

/**
 * 결과
 *
 * Main thread is free!
 * Fetching data...
 * Processing Data
 * Result : DATA
 * */
```
- `supplyAsync` : 비동기 작업 실행
- `thenApply` : 작업 결과 반환
- `thenAccept` : 최종 결과를 소비

<br>

#### 5. 작업 병렬 처리와 결과 조합
```java
import java.util.*;

public class ParallelProcessingExample {
    public static void main(String[] args) {
        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Task1");
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "Task2");

        CompletableFuture<Void> combinedFuture = future1.thenCombine(future2, (result1, result2) -> result1 + " & " + result2)
                .thenAccept(System.out::println);

        combinedFuture.join(); // 모든 작업 완료 대기
    }
}

/**
 * 결과
 *
 * Task1 & Task2
 * */
```
- `thenCombine` : 두 작업의 결과를 조합
- `join` : 메인 스레드가 비동기 작업 완료를 기다림



<br>

### 6. 에러 처리와 복구
```java
import java.util.*;

public class ExceptionHandlingExample {
    
    public static void main(String[] args) {

      CompletableFuture.supplyAsync(() -> {
          if (Math.random() > 0.5) {
              throw new RuntimeException("Something went wrong!");
          }
          return "Success!";
      }).exceptionally(ex -> {
          System.out.println("Error: " + ex.getMessage());
          return "Default Value";
      }).thenAccept(System.out::println);
    }
}

/**
 * 결과
 * 
 * Error : Something went wrong!
 * Default Value
 * 
 * 또는
 * 
 * Success!
 * */
```
- `exceptionally` : 작업 실패 시, 기본 값 제공

<br>

### 7. 여러 작업의 동시 실행
```java
import java.util.*;

public class MultipleTasksExample {
    public static void main(String[] args) {
        List<CompletableFuture<String>> tasks = List.of(
                CompletableFuture.supplyAsync(() -> "Task1"), 
                CompletableFuture.supplyAsync(() -> "Task2"), 
                CompletableFuture.supplyAsync(() -> "Task3")
        );
        
        CompletableFuture<Void> allTasks = CompletableFuture.allOf(tasks.toArray(new CompletableFuture[0]));
        
        allTasks.thenRun(() -> {
            List<String> results = tasks.stream()
                    .map(CompletableFuture::join)
                    .collect(Collectors.toList());
            System.out.println("Results: " + results);
        }).join();
    }
}

/**
 * 결과
 * 
 * Result : [Task1, Task2, Task3]
 * */
```
- `allOf` : 모든 작업이 완료될 때까지 대기
- `join` : 결과를 안전하게 수집

<br>


### 8. `CompletableFuture`의 작업 체이닝
```text
[ Task 1 ]  --thenApply-->  [ Task2 ]  --thenAccept-->  [ 소비 ]
```

<br>

### 8-1. 병렬 작업 처리 흐름
```text
Task1  -->  
Task2      } --thenCombine--> [ Combined Result ]
Task3  -->
```

<br>

### 8-2. 작업 실패 처리 흐름
```text
[ Task ] --> [ Exceptionally ] --> [ Recovery ]
```
<br>

### 정리하자면
- `CompletableFuture`를 사용하면 기존 Future의 제약을 극복하고 더 유연한 비동기 프로그래밍이 가능하다