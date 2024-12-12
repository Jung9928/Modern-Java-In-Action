# Chapter 20. OOP와 FP의 조화 : 자바와 스칼라 비교

### 내용 요약 <br>
#### 1. 객체지향 프로그래밍(OOP)과 함수형 프로그래밍(FP)의 차이점
- OOP : 상태(객체)와 동작(메소드)을 중심으로 모델링
    - 캡슐화, 상속, 다형성 등
    - 주요 문제 : 가변 상태, 동시성 이슈

<br>

- FP : 불변성과 순수 함수를 강조하여 선언적으로 문제를 해결
    - 고차 함수, 커링, 재귀 등
    - 주요 문제 : 상태 관리를 명시적으로 해야 함

<br>

#### 2. Java의 OOP와 FP 혼합
- Java 8 이후 람다 표현식, 스트림 API, `Optional`, `CompeletableFuture` 등을 통해 FP 요소를 도입
- 제한적으로 FP를 지원하므로 추가 라이브러리(ex : Vavr)를 사용하는 경우도 많음

<br>

#### 3. Scala의 OOP와 FP 통합
- Scala는 OOP와 FP를 자연스럽게 조화
    - 클래스, 객체, 상속과 같은 OOP 기능
    - 고차 함수, 불변성, 패턴 매칭과 같은 FP 기능

<br>

#### 4. Java와 Scala 비교
- Java : OOP기반에 FP 기능 추가
- Scala : OOP와 FP를 자연스럽게 통합

<br>

### 5. 실제 사용 사례
- `Java`는 기업 환경에서의 확장성과 안정성에 중점
- `Scala`는 데이터 처리 및 동시성 작업에 강점

<br>

### 6. Java의 OOP와 FP 혼합 (ex : 람다와 스트림)
```java
import java.util.*;

public class JavaExample {
    public static void main(String[] args) {
        
        List<String> names = List.of("Alice", "Bob", "Charlie");
        
        // FP 스타일 : 스트림과 람다를 활용한 필터 및 맵핑
        List<String> filteredNames = names.stream()
                .filter(name -> name.startsWith("A"))
                .map(String::toUpperCase)
                .collect(Collectors.toList());
        
        System.out.println(filteredNames);              // [ALICE]
    }
}
```
- 스트림 API와 람다를 사용해 FP 스타일로 데이터 처리
- Java의 FP기능은 제한적이며, 외부 라이브러리로 확장 가능

<br>

### 7. Scala의 OOP와 FP 통합
```text
object ScalaExample extends App {
  val names = List("Alice", "Bob", "Charlie")

  // FP 스타일: 필터 및 맵핑
  val filteredNames = names.filter(_.startsWith("A")).map(_.toUpperCase)

  println(filteredNames) // List(ALICE)

  // 패턴 매칭을 통한 데이터 처리
  def greet(person: Any): String = person match {
    case "Alice" => "Hello, Alice!"
    case "Bob" => "Hi, Bob!"
    case _ => "Who are you?"
  }

  println(greet("Alice")) // Hello, Alice!
  println(greet("John"))  // Who are you?
}
```
- Scala는 불변성을 기본으로 제공하며, FP 스타일의 간결한 문법을 지원.
- 패턴 매칭을 통해 선언적이고 직관적인 데이터 처리가 가능.

<br>


### 8. Java와 Scala의 동시성 처리 비교
```java
public class JavaConcurrency {
    
    public static void main(String[] args) {
        CompletableFuture.supplyAsync(() -> "Hello")
                .thenApply(result -> result + ", World!")
                .thenAccept(System.out::println);           // Hello, World!
    }
}
```

<br>

```text
import scala.concurrent._
import scala.concurrent.ExecutionContext.Implicits.global
import scala.util.{Success, Failure}

object ScalaConcurrency extends App {
  val future = Future {
    "Hello"
  }.map(_ + ", World!")

  future.onComplete {
    case Success(result) => println(result) // Hello, World!
    case Failure(e) => println(s"Error: ${e.getMessage}")
  }
}
```
- `Java`는 `CompletableFuture`로 비동기 작업을 처리.
- `Scala`는 `Future`와 `패턴 매칭`을 통해 비동기 작업을 선언적으로 처리.

<br>


### 9. Java와 Scala의 OOP-FP 통합 차이
```text
Java:
[ OOP 기반 ] + [ 제한적 FP 기능 ]

Scala:
[ OOP ] ↔ [ FP 완벽 통합 ]
```

<br>

### 10. 패턴 매칭의 흐름
```text
[ Input 데이터 ] --> [ 조건에 따라 처리 ] --> [ 결과 반환 ]
```

<br>

### 정리하자면
#### 1) Java의 특징
- OOP 중심, FP는 제한적 지원.
- 높은 안정성과 확장성.
- 대규모 기업 환경에 적합.

<br>

#### 2) Scala의 특징
- OOP와 FP 통합 설계.
- 간결한 문법과 강력한 FP 기능.
- 데이터 처리 및 동시성 작업에 적합.