# Chapter 11. null 대신 Optional 클래스

### 내용 요약 <br>
#### 1. null의 문제점
- `null` 값은 런타임에 `NullPointerException`을 발생시킬 위험이 있음
- 값이 없음을 명시적으로 표현하지 못해 코드 가독성이 떨어짐
- 방어적 코드 `(if (x != null))`가 필요해 코드가 복잡해짐

<br>

#### 2. Optional의 소개
- `Optional<T>`
  - 값이 있으면 그 값을 담고, 값이 없으면 `Optional.empty()`를 담는 컨테이너 <br><br>

- `Optional`
  - 값의 존재와 부재를 명확히 표현하며, `null`을 안전하게 대체 <br><br>

<br>

#### 3. Optional 사용 방법
- 값을 처리할 때 명시적으로 `isPresent`, `ifPresent` 등을 사용
- 스트림, 람다와 함께 활용하면 간결하고 안전한 코드 작성 가능

<br><br>


### 4. Optional 클래스의 주요 메소드
<table><thead>
  <tr>
    <th>메소드</th>
    <th>설명</th>
  </tr></thead>
<tbody>
  <tr>
    <td>empty()</td>
    <td>빈 Optional 객체 생성</td>
  </tr>
  <tr>
    <td>of(value)</td>
    <td>null이 아닌 값으로 Optional 생성</td>
  </tr>
  <tr>
    <td>ofNullable(value)</td>
    <td>값이 null일 수도 있는 경우 사용</td>
  </tr>
  <tr>
    <td>isPresent()</td>
    <td>값이 존재하는지 확인</td>
  </tr>
  <tr>
    <td>ifPresent(Consumer)</td>
    <td>값이 존재할 경우 Consumer 실행</td>
  </tr>
  <tr>
    <td>orElse(value)</td>
    <td>값이 존재하지 않을 때 기본값 반환</td>
  </tr>
  <tr>
    <td>orElseGet(Supplier)</td>
    <td>값이 없을 때 기본 값을 계산하는 로직 제공</td>
  </tr>
  <tr>
    <td>orElseThrow()</td>
    <td>값이 없을 때 예외를 던짐</td>
  </tr>
  <tr>
    <td>map(Function)</td>
    <td>값이 존재할 경우 값을 반환</td>
  </tr>
  <tr>
    <td>flatMap(Function)</td>
    <td>중첩된 Optional을 한 단계 평탄화하여 반환</td>
  </tr>
</tbody>
</table>


### 5. null과 Optional 비교
#### 1) 전통적인 null 처리 코드
```java
import java.util.*;

public class Person {
    private String name;
    private Address address;
    
    public Address getAddress() {
        return address;
    }
}

public class Address {
    private String city;
    
    public String getCity() {
        return city;
    }
}

public class Main {
    public static void main(String[] args) {
        Person person = new Person();
        
        String city = null;
        if(person != null) {
            Address address = person.getAddress();
            if(address != null) {
                city = address.getCity();
            }
        }
        
        System.out.println(city != null ? city : "Unknown City");
    }
}
```

<br>

#### 2) Optional 사용 코드
```java
import java.util.*;

public class Person {
    private Optional<Address> address = Optional.empty();
    
    public Optional<Address> getAddress() {
        return address;
    }
}

public class Address {
    private String city;
    
    public String getCity() {
        return city;
    }
}

public class Main {
    public static void main(String[] args) {
        Person person = new Person();
        
        String city = person.getAddress()
                        .map(Address::getCity)
                        .orElse("Unknown City");
        
        System.out.println(city);
    }
}

/**
 * 결과
 * 
 * Unknown City
 * */
```
- `Optional`을 사용하면 null 체크를 명시적으로 하지 않아도 되며 코드가 간결해짐

<br>

#### 2) `Optional`의 다양한 메소드 활용
```java
import java.util.*;

public class OptionalExample {
    public static void main(String[] args) {
        
        // Optional 생성
        Optional<String> name = Optional.ofNullable(null);
        
        // 값이 없을 경우 기본 값 제공
        System.out.println(name.orElse("Default Name"));
        
        // 값이 없을 경우 Supplier로 기본값 계산
        System.out.println(name.orElseGet(() -> "Generated Name"));
        
        // 값이 없을 경우 예외 던짐
        try {
            System.out.println(name.orElseThrow(() -> new IllegalArgumentException("No value present")));
        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
        
        // 값이 존재할 경우 실행
        name.ifPresent(value -> System.out.println("Value : " + value));
    }
}

/**
 * 결과
 *
 * Default Name
 * Generated Name
 * No value present
 * */
```

<br>

#### 3) `map`과 `flatMap`
```java
import java.util.*;

public class NestedOptionalExample {
    public static void main(String[] args) {
        
        Optional<Optional<String>> nestedOptional = Optional.of(Optional.of("Hello"));
        
        // map 사용 : 중첩된 Optional 유지
        Optional<Optional<String>> mapped = nestedOptional.map(opt -> opt.map(String::toUpperCase));
        System.out.println(mapped);
        
        // flatMap 사용 : 중첩 제거
        Optional<String> flatMapped = nestedOptional.flatMap(opt -> opt.map(String::toUpperCase));
        System.out.println(flatMapped);
    }
}

/**
 * 결과
 *
 * Optional[Optional[HELLO]]
 * Optional[HELLO]
 * */
```
- `map`은 중첩된 Optional을 반환, `flatMap`은 중첩을 평탄화하여 처리

<br>

### 4) `null`과 `Optional`의 흐름
```text
[Null 기반 코드]
    if (obj != null)
        -> obj.getValue();
    
[Optional 기반 코드]
    obj.map(Class::getValue).orElse(defaultValue);
```

<br>

### 5) Optional 주요 메소드 구조
```text
Optional
 ├── empty()        : 빈 Optional 생성
 ├── of(value)      : 값이 반드시 존재할 경우 생성
 ├── ofNullable(v)  : null 허용 Optional 생성
 ├── isPresent()    : 값 존재 여부 확인
 └── map/flatMap    : 값 변환 및 중첩 제거
```
