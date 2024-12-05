# Chapter 15. CompletableFuture와 리액티브 프로그래밍

### 내용 요약 <br>
#### 1. `CompletableFuture`
- `비동기 작업`을 처리하기 위한 API
- `Future`의 한계를 극복하기 위한 설계
  - 블로킹 호출 제거(`get` 메소드 필요 X)
  - 여러 작업을 쉽게 조합 가능
  - 작업 완료 후, 콜백 등록 가능

- 다양한 메소드 제공
  - `supplyAsync`, `thenApply`, `thenCompose`, `thenCombine`, `exceptionally` 등


<br>

#### 2. 리액티브 프로그래밍
- 데이터의 `비동기 스트림`을 중심으로 한 프로그래밍 패러다임 
- Java 9에서는 `java.util.concurrent.Flow`를 통해 리액티브 스트림 표준을 지원
- 주요 구성요소
  - `Publisher` : 데이터를 발행
  - `Subscriber` : 데이터를 구독
  - `Processor` : 발행자와 구독자 사이에서 데이터 반환

<br>

#### 3. 장점
- 고성능 비동기 데이터 처리
- 비동기 이벤트 기반 아키텍처 구현 가능
- 멀티스레드 환경에서의 자원 최적화

<br>


### 4. CompletableFuture 예제와 설명
#### 1) 간단한 비동기 작업
```java
import java.util.*;

public class CompletableFutureExample {
    public static void main(String[] args) {
        CompletableFuture.supplyAsync(() -> {
            System.out.println("Task running in a separate thread.");
            return "Hello, CompletableFuture!";
        }).thenAccept(result -> {
            System.out.println("Result : " + result);
        });
        
        System.out.println("Main thread continues...");
    }
}

/**
 * 결과
 * 
 * Main thread continues...
 * Task running in a separate thread.
 * Result: Hello, CompletableFuture!
 * */
```
- `supplyAsync` : 별도의 스레드에서 작업 실행
- `thenAccept` : 작업 완료 후, 결과를 소비

<br>

#### 2) 작업 조합 (`thenCompose`와 `thenCombine`)
```java
import java.util.*;

public class CombineExample {
    public static void main(String[] args) {
        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> "Hello");
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> "World");
        
        future1.thenCombine(future2, (s1, s2) -> s1 + " " + s2)
                .thenAccept(System.out::println);
    }
}

/**
 * 결과
 *
 * Hello World
 * */
```
- `thenCombine` : 두 비동기 작업의 결과를 조합
- 각 작업은 독립적으로 실행되며 결과가 결합됨



<br>

### 5. 에러 처리
```java
import java.util.*;

public class ExceptionHandlingExample {
    
    public static void main(String[] args) {
        
        CompletableFuture.supplyAsync(() -> {
            if(Math.random() > 0.5) {
                throw new RuntimeException("Something went wrong!");
            }
            return "Success!";
        }).exceptionally(ex -> {
            System.out.println("Error : " + ex.getMessage());
            return "Recovered!";
        }).thenAccept(System.out::println);
    }
}

/**
 * 결과
 * 
 * Error : Something went wrong!
 * Recovered!
 * 
 * 또는
 * 
 * Success!
 * */
```
- `exceptionally` : 비동기 작업 중 발생한 예외를 처리

<br>

### 6. 리액티브 프로그래밍 예제
#### 1) 기본 `Publisher` 정의
```java
import java.util.*;

public class SimplePublisher implements Publisher<String> {
    
    private final String[] items = {"Item 1", "Item 2", "Item 3"};
    
    @Override
    public void subscribe(Flow.Subscriber<? super String> subscriber) {
        for(String item : items) {
            subscriber.onNext(item);
        }
        subscriber.onComplete();
    }
}
```

<br>

### 2) 기본 `Subscriber` 정의
```java
import java.util.*;

public class SimpleSubscriber implements Subscriber<String> {
    @Override
    public void onSubscribe(Subscription subscription) {
        System.out.println("Subscribed!");
    }

    @Override
    public void onNext(String item) {
        System.out.println("Recieved : " + item);
    }
  
    @Override
    public void onError(Throwable throwable) {
      System.out.println("Subscribed!");
    }
  
    @Override
    public void onComplete() {
      System.out.println("Completed!");
    }
}

public class Main {
    public static void main(String[] args) {
        SimplePublisher publisher = new SimpleSubscriber();
        SimpleSubscriber subscriber = new SimpleSubscriber();
        publisher.subscribe(subscriber);
    }
}

/**
 * 결과
 * 
 * Subscribed!
 * Received : Item 1
 * Received : Item 2
 * Received : Item 3
 * Completed!
 * */
```
- `Publisher`가 데이터를 발행하고 `Subscriber`가 이를 수신

<br>


### 6. CompletableFuture의 작업 흐름
```text
[ Task 1 ]  -->  [ thenApply ]  -->  [ Result ]
```

<br>

### 7. 리액티브 프로그래밍 흐름
```text
Publisher  -->  Processor  -->  Subscriber
```
