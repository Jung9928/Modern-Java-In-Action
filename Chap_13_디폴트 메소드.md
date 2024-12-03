# Chapter 13. 디폴트 메소드

### 내용 요약 <br>
#### 1. 디폴트 메소드란?
- 인터페이스에서 구현체를 포함할 수 있는 메소드
- `default` 키워드를 사용해 선언
- 인터페이스를 구현하는 클래스에서 반드시 재정의하지 않아도 됨

<br>

#### 2. 디폴트 메소드의 장점
- `기존 코드와의 호환성 유지`
  - 기존 인터페이스에 새로운 메소드를 추가해도 구현체를 수정할 필요 없음 <br><br>
  
- `코드 재사용성`
  - 공통된 동작을 인터페이스에서 제공 가능 <br><br>
  
- `다중 상속`
  - 여러 인터페이스의 디폴트 메소드를 조합하여 클래스 구현 가능 <br><br>

<br>

#### 3. 디폴트 메소드의 한계
- 다중 상속의 경우, 동일한 메소드를 가진 인터페이스들 간 충돌이 발생할 수 있음
- 디폴트 메소드는 `공통 기능 제공`에 적합하며 복잡한 비즈니스 로직 구현에는 적합하지 않음

<br>


### 디폴트 메소드의 기본 문법
```java
public interface MyInterface {
    default void myDefaultMethod() {
        System.out.println("This is a default method.");
    }
}

public class MyClass implements MyInterface {
    public static void main(String[] args) {
        MyClass myClass = new MyClass();
        myClass.myDefaultMethod();                  // 디폴트 메소드 호출
    }
}

/**
 * This is a default method.
 * */
```

<br>

#### 2) `LocalTime` 예제
```java
import java.time.*;

public class LocalTimeExample {
    public static void main(String[] args) {
        
        LocalTime now = LocalTime.now();                              // 현재 시간
        LocalTime specificTime = LocalTime.of(14, 30);                // 특정 시간
      
        System.out.println("현재 시간 : " + now);
        System.out.println("특정 시간 : " + specificTime);
        
        LocalTime plusHour = now.plusHours(2);                         // 시간 더하기
        LocalTime minusMinutes = now.minusMinutes(15);                 // 시간 빼기
      
        System.out.println("2시간 후 : " + plusHours);
        System.out.println("15분 전 : " + minusMinutes);
    }
}

```

<br>

### 3. `디폴트 메소드`와 `기존 인터페이스`
#### 1) 기존 인터페이스 확장
```java
public interface Vehicle {
    void startEngine();                             // 기존 메소드
  
    default void stopEngine() {
        System.out.println("Engine stopped.");      // 새로운 디폴트 메소드
    }
}

public class Car implements Vehicle {
    
    @Override
    public void startEngine() {
        System.out.println("Engine started.");
    }
    
    public static void main(String[] args) {
        
        Car car = new Car();
        car.startEngine();          // 기존 메소드 호출
        car.stopEngine();           // 디폴트 메소드 호출
    }
}

/**
 * 결과
 * 
 * Engine started.
 * Engine stopped.
 * */
```

<br>

### 4. 다중 상속 충돌 해결
```java
public interface InterfaceA {
    default void hello() {
        System.out.println("Hello from InterfaceA");
    }
}

public interface InterfaceB {
    default void hello() {
        System.out.println("Hello from InterfaceB");
    }
}

public class MyClass implements InterfaceA, InterfaceB {
    
    @Override
    public void hello() {
        InterfaceA.super.hello();               // 명시적으로 호출
    }
    
    public static void main(String[] args) {
        
        MyClass myClass = new MyClass();
        myClass.hello();
    }
}

/**
 * 결과
 * 
 * Hello from InterfaceA
 * */
```
- 다중 상속 충돌 시, `명시적으로` 어떤 인터페이스의 메소드를 호출할 지 지정.

<br>

### 5. 디폴트 메소드의 재정의
```java
public interface Animal {
    default void sound() {
        System.out.println("This animal makes a sound.");
    }
}

public class Dog implements Animal {
    @Override
    public void sound() {
        System.out.println("The dog barks.");
    }
    
    public static void main(String[] args) {
        
        Dog dog = new Dog();
        dog.sound();                // 재정의된 메소드 호출
    }
}

/**
 * 결과
 *
 * The dog barks.
 * */
```

<br>

### 6. 디폴트 메소드와 정적 메소드의 차이
```java
public interface MathOperations {
    default int add(int a, int b) {
        return a + b;
    }
    
    static int multiPly(int a, int b) {
        return a * b;
    }
}

public class Calculator implements MathOperations {
    
    public static void main(String[] args) {
        Calculator calculator = new Calculator();
        
        System.out.println("Addition : " + calculator.add(5, 3));                       // 디폴트 메소드
        System.out.println("Multiplication : " + MathOperations.multiPly(5, 3));        // 정적 메소드
    }
}

/**
 * 결과
 *
 * Addition : 8
 * Multiplication : 15
 * */
```
- `디폴트 메소드`
  - 인스턴스에서 호출 가능 <br><br>

- `정적 메소드`
  - 클래스철머 인터페이스에서 직접 호출


<br>


### 7. 디폴트 메소드 흐름
```text
인터페이스
   |
+--+---------------------------+
|                             |
디폴트 메소드             기존 메소드
|                             |
+-------------------+         |
구현 클래스 (옵션적으로 재정의)
```

<br>

### 8. 다중 상속 충돌 해결
```text
InterfaceA
  hello() -- Default
     |
     v
InterfaceB
  hello() -- Default
     |
  Conflict -> 명시적으로 선택
```


### 정리하자면
1. 공통 로직을 인터페이스에서 제공할 수 있음
2. 다중 상속 문제를 해결하며 유연하게 기능 확장 가능
3. 디폴트 메소드와 정적 메소드를 활용하여 보다 명확한 설계 가능