# Chapter 17. 리액티브 프로그래밍

### 내용 요약 <br>
#### 1. `리액티브 스트림(Reactive Streams)`
- Java 9에서 추가된 `java.util.concurrent.Flow` API는 리액티브 스트림 표준을 구현
- 핵심 구성 요소
  - `Publisher` : 데이터를 발행
  - `Subscriber` : 데이터를 구독
  - `Processor` : 발행자와 구독자 사이에서 데이터 변환
  - `Subscription` : 구독 정보를 관리

- `비동기 스트림 처리`와 `역압(Backpressure)` 지원

<br>


#### 2. 리액티브 프로그래밍의 특징
- 비동기와 논블로킹 방식으로 데이터 처리
- 데이터 흐름을 중심으로 선언적 코드를 작성
- 이벤트 기반 처리 방식
- 역압을 통해 리소스 최적화

<br>

#### 3. 리액티브 라이브러리
- Java에서 리액티브 프로그래밍을 구현하기 위해 널리 사용되는 라이브러리
  - Project Reactor (Spring Webflux 기반)
  - RxJava
  - Java 9 Flow API

<br>

#### 4. 리액티브 스트림의 장점
- 효율성 : 논블로킹 I/O로 자원 사용 최적화
- 확장성 : 대규모 데이터 처리에 적합
- 유연성 : 다양한 데이터 소스를 하나의 스트림으로 처리 가능

<br>

### 5. 리액티브 스트림 기본 구성
```java
import java.util.*;

// Publisher 구현
public class SimplePublisher implements Publisher<String> {

  private final String[] items = {"Item 1", "Item 2", "Item 3"};

  @Override
  public void subscribe(Subscriber<? super String> subscriber) {
      SimpleSubscription subscription = new SimpleSubscription(subscriber, items);
      subscriber.onSubscribe(subscription);
  }
}

class SimpleSubscription implements Subscription {
    private final Subscriber<? super String> subscriber;
    private final String[] items;
    private int currentIndex = 0;
    
    public SimpleSubscription(Subscriber<? super String> subscriber, String[] items) {
        this.subscriber = subscriber;
        this.items = items;
    }
    
    @Override
    public void request(long n) {
        while(n-- > 0 && currentIndex < items.length) {
            subscriber.onNext(items[currentIndex++]);
        }
        
        if(currentIndex == items.length) {
            subscriber.onComplete();
        }
    }
    
    @Override
    public void cancel() {
        System.out.println("Subscription cancelled.");
    }
}

// Subscriber 구현
public class SimpleSubscriber implements Subscriber<String> {
    @Override
    public void onSubscribe(Subscription subscription) {
        System.out.println("Subscribed!");
        subscription.request(3);            // 요청할 데이터 수
    }
    
    @Override
    public void onNext(String item) {
        System.out.println("Received : " + item);
    }
    
    @Override
    public void onError(Throwable throwable) {
        System.out.println("Error : " + throwable.getMessage());
    }
    
    @Override
    public void onComplete() {
        System.out.println("All items received!");
    }
}

public class Main {
    public static void main(String[] args) {
      SimplePublisher publisher = new SimplePublisher();
      SimpleSubscriber subscriber = new SimpleSubscriber();
  
      publisher.subscribe(subscriber);
    }
}

/**
 * 결과
 *
 * Subscribed!
 * Received: Item 1
 * Received: Item 2
 * Received: Item 3
 * All items received!
 * */
```
- `SimplePublisher` : 데이터를 발행
- `SimpleSubscriber` : 데이터를 구독하고 처리

<br>

### 6. Project Reactor 활용
#### 1) `Flux`와 `Mono` 예시
```java
import reactor.core.*;

public class ReactorExample {
    public static void main(String[] args) {
        
        // Flux : 여러 개의 데이터 스트림
        Flux<String> flux = Flux.just("A", "B", "C");
        flux.map(String::toLowerCase)
                .subscribe(System.out::println);
        
        // Mono : 단일 데이터 스트림
        Mono<String> mono = Mono.just("Hello, Mono!");
        mono.map(String::toUpperCase)
                .subscribe(System.out::println);
    }
}

/**
 * 결과
 *
 * a
 * b
 * c
 * HELLO, MONO!
 * */
```
- `Flux` : 여러 데이터를 처리하는 스트림
- `Mono` : 하나의 데이터를 처리하는 스트림

<br>

### 7. 역압(Backpressure) 처리
#### [역압의 필요성]
- 구독자가 발행 속도를 따라가지 못할 때 발생
- 리액티브 스트림 표준에서 `Subscription`을 통해 해결

```java
import reactor.core.*;

public class BackpressureExample {
    
    public static void main(String[] args) {
      Flux<Integer> flux = Flux.range(1, 100)
              .onBackpressureDrop();            // 초과 데이터 삭제
      
      flux.subscribe(
              item -> System.out.println("Received : " + item),
              err -> System.err.println("Error : " + err),
              () -> System.out.println("Complete!")
      );
    }
}

/**
 * 결과
 *
 * Received: 1
 * Received: 2
 * ...
 * Received: 100
 * Complete!
 * */
```
- `onBackpressureDrop` : 역압이 발생하면 초과 데이터를 삭제

<br>


### 8. 리액티브 스트림 구성 요소
```text
[ Publisher ] --> [ Processor (Optional) ] --> [ Subscriber ]
```

<br>

### 8-1. 데이터 흐름과 역압
```text
Publisher --(data)--> Subscriber
    ↑                    ↓
   (request(n)) <-- Backpressure
```
