# Chapter 14. 자바 모듈 시스템

### 내용 요약 <br>
#### 1. 모듈이란?
- 관련 클래스와 리소스를 캡슐화한 논리적 그룹
- 모듈은 `module-info.java` 파일을 통해 정의
- 모듈은 자신이 공개(export)한 패키지만 다른 모듈에서 접근 가능하도록 제어

<br>

#### 2. 모듈 시스템의 장점
- `캡슐화 강화`
    - 모듈 내부 구현을 외부에서 숨김 <br><br>

- `의존성 관리`
    - 필요한 모듈만 명시적으로 참조 <br><br>

- `어플리케이션 경량화`
    - 필요 없는 모듈은 런타임 환경에서 제거 가능 <br><br>

- `컴파일 시간 체크`
  - 의존성 누락을 컴파일 시점에 발견 <br><br>


<br>

#### 3. 주요 키워드
- `module` : 모듈 정의
- `requires` : 다른 모듈 의존성 선언
- `exports` : 외부에 공개할 패키지 선언
- `opens` : 리플렉션 사용을 허용할 패키지 선언
- `provides` / `uses` : 서비스 제공 및 사용 선언

<br>


### 4. 모듈 정의
#### `module-info.java` 파일
```java
module com.example.myapp {
    requires java.base;                 // 의존 모듈 선언
    exports com.example.myapp.service;  // 외부에 공개할 패키지
}
```

<br>

### 5. 모듈화된 어플리케이션 예제
#### 5-1) 모듈 구조
```text
my-app
├── module-info.java
├── com.example.myapp
│   └── service
│       └── GreetingService.java
```

<br>


#### 5-2) GreetingService.java
```java
package com.example.myapp.service;

public class GreetingService {
  public String greet(String name) {
    return "Hello, " + name + "!";
  }
}
```

<br>

#### 5-3) `module-info.java`
```java
module com.example.myapp {
  exports com.example.myapp.service;        // GreetingService가 속한 패키지를 외부에 공개
}
```

<br>

### 6. 클라이언트 모듈
```text
my-client
├── module-info.java
├── com.example.myclient
│   └── Main.java
```

<br>

#### 6-1) Main.java
```java
package com.example.myclient;

import com.example.myapp.service.GreetingService;

public class Main {
  public static void main(String[] args) {
    GreetingService service = new GreetingService();
    System.out.println(service.greet("Java Module System"));
  }
}
```

<br>

#### 6-2) `module-info.java` (클라이언트 모듈)
```java
module com.example.myclient {
  requires com.example.myapp; // 의존성 선언
}
```

<br>


### 7. 서비스 제공 및 소비
#### 7-1) 서비스 제공자 모듈
```java
module com.example.provider {
    provides com.example.service.MyService with com.example.provider.MyServiceImpl;
}
```

<br>

#### 7-2) MyService 인터페이스
```java
package com.example.service;

public interface MyService {
    String execute();
}
```

<br>

#### 7-3) 소비자 코드
```java
package com.example.consumer;

import com.example.service.MyService;
import java.util.ServiceLoader;

public class Main { 
    public static void main(String[] args) {
        ServiceLoader<MyService> services = ServiceLoader.load(MyService.class);
        services.forEach(service -> System.out.println(service.execute()));
    }
}
```

### 8. 모듈 시스템 구조
```text
+----------------------+
| Module: com.example  |
|                      |
| Exports:             |
| - com.example.api    |
| Requires:            |
| - java.base          |
+----------------------+
```

<br>

### 9. 모듈 의존성 그래프
```text
+-------------------+     +-------------------+
| com.example.myapp | --> | com.example.util  |
+-------------------+     +-------------------+
```

<br>

### 10. 캡슐화와 공개
```text
Module A                Module B
+----------------+      +----------------+
| - Internal1    |      | - Internal2    |
| - Internal2    |      | + PublicClass  |
| + PublicClass  |      +----------------+
+----------------+
```


### 정리하자면
1. 모듈 시스템을 사용하면 코드가 더 체계적이고 안전하게 관리 가능
2. 서비스 기반 설계를 통해 모듈 간 느슨한 결합 가능
3. 필요한 경우, 런타임에 필요한 모듈만 로드하여 어플리케이션 크기 줄일 수 있음