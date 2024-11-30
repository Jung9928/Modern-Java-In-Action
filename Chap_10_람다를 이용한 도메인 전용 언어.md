# Chapter 10. 람다를 이용한 도메인 전용 언어 (DSL)

### 내용 요약 <br>
#### 1. DSL의 정의
- 특정 도메인에 특화된 문법과 표현을 가진 언어
- Java에서 람다와 메소드 체인을 활용해 내장 DSL을 설계할 수 있음

<br>

#### 2. DSL의 장점
- 도메인 전문가와 개발자 간의 의사소통이 쉬워짐
- 코드를 직관적이고 읽기 쉬운 형식으로 표현 가능
- 복잡한 비즈니스 로직을 간결하게 표현

<br>

#### 3. DSL의 구현 방식
- 유창한 API(Fluent API) 
  - 메소드 체이닝과 빌더 패턴 활용 <br><br>

- 람다 표현식 기반
  - 동작을 람다로 전달하여 코드 간결화 <br><br>

- 메소드 참조 활용
  - 가독성을 높이는 메소드 참조 사용 <br><br>

<br><br>


### 주문 처리 DSL : 유창한 API 구현 
#### 1) 일반 코드 (DSL 미사용)
```java
import java.util.*;

public class Order {
    private String customer;
    private List<String> items = new ArrayList<>();
    
    public void setCustomer(String customer) {
        this.customer = customer;
    }
    
    public void addItem(String item) {
        items.add(item);
    }
    
    @Override
    public String toString() {
        return "Order for " + customer + " with items" + items;
    }
}

public class Main {
    public static void main(String[] args) {
        
        Order order = new Order();
        order.setCustomer("Alice");
        order.addItem("Apple");
        order.addItem("Banana");
        
        System.out.println(order);
    }
}

/**
 * 결과
 *
 * Order for Alice with items [Apple, Banana]
 * */
```
<br>

#### 2) DSL을 활용한 코드
```java
import java.util.*;

public class Order {
    private String customer;
    private List<String> items = new ArrayList<>();
    
    public void setCustomer(String customer) {
        this.customer = customer;
        return this;
    }
    
    public void addItem(String item) {
        items.add(item);
        return this;
    }
    
    @Override
    public String toString() {
        return "Order for " + customer + " with items" + items;
    }
}

public class Main {
    public static void main(String[] args) {
        
        Order order = new Order()
                .forCustomer("Alice")
                .addItem("Apple")
                .addItem("Banana");
        
        System.out.println(order);
    }
}

/**
 * 결과
 *
 * Order for Alice with items [Apple, Banana]
 * */
```
- forCustomer와 addItem 메소드가 메소드 체이닝을 지원
- 더 읽기 쉽고 간결한 코드로 변환

<br>

#### 3) 람다를 활용한 DSL
```java
import java.util.*;

public class Order {
    private String customer;
    private List<String> items = new ArrayList<>();
    
    public void setCustomer(String customer) {
        this.customer = customer;
        return this;
    }
    
    public void addItem(String item) {
        items.add(item);
        return this;
    }
    
    @Override
    public String toString() {
        return "Order for " + customer + " with items" + items;
    }
}

class OrderBuilder {
    public static Order order(Consumer<Order> consumer) {
        Order order = new Order();
        consumer.accept(order);
        return order;
    }
}

public class Main {
    public static void main(String[] args) {
        
        Order order = OrderBuilder.order(o ->
                o.forCustomer("Alice")
                 .addItem("Apple")
                 .addItem("Banana"));
        
        System.out.println(order);
    }
}

/**
 * 결과
 *
 * Order for Alice with items [Apple, Banana]
 * */
```
- `OrderBuilder.order`는 람다를 받아 주문을 구성
- 코드가 더 유연해지고 재사용성이 증가

<br><br>

### DSL의 구조
```text
[DSL 사용자]
    |
[Fluent API or Lambda]
    |
[도메인 객체 구성]
```

<br>

### 코드 흐름
```text
람다로 동작 정의   ->  객체 생성  ->  람다에서 정의한 동작 적용   ->   최종 객체 반환
```
