# Chapter 12. 새로운 날짜와 시간 API

### 내용 요약 <br>
#### 1. 새로운 API의 장점
- `불변성` 
  - 모든 날짜와 시간 객체는 불변(immutable)으로 설계. <br><br>
  
- `명확한 설계`
  - 다양한 클래스가 분리되어 용도에 맞는 명확한 API 제공 (`LocalDate`, `LocalTime`, `LocalDateTime` 등) <br><br>

- `읽기 쉬운 코드`
  - 날짜와 시간을 처리하는 직관적이고 간결한 메소드 <br><br>

- `타임존 지원`
  - `ZonedDateTime` 및 `ZoneId`로 타임존 관리 가능 <br><br>
  
- `조정과 포맷팅`
  - 날짜 조정(`plusDays`, `minusMonths` 등)과 포맷팅(`DateTimeFormatter`) 기능 제공 <br><br>
  

<br>

#### 2. 주요 클래스
<table><thead>
  <tr>
    <th>클래스</th>
    <th>설명</th>
  </tr></thead>
<tbody>
  <tr>
    <td>LocalDate</td>
    <td>날짜 (연, 월, 일)만 관리</td>
  </tr>
  <tr>
    <td>LocalTime</td>
    <td>시간 (시, 분, 초, 나노초)만 관리</td>
  </tr>
  <tr>
    <td>LocalDateTime</td>
    <td>날짜와 시간 모두 관리</td>
  </tr>
  <tr>
    <td>ZonedDateTime</td>
    <td>타임존이 포함된 날짜와 시간 관리</td>
  </tr>
  <tr>
    <td>Duration</td>
    <td>두 시간 간의 차이(시간, 분, 초)</td>
  </tr>
  <tr>
    <td>Period</td>
    <td>두 날짜 간의 차이(년, 월, 일)</td>
  </tr>
  <tr>
    <td>Instant</td>
    <td>타임스탬프(UTC 기준 나노초 정밀도)</td>
  </tr>
  <tr>
    <td>DateTimeFormatter</td>
    <td>날짜와 시간의 포맷 정의 및 변환</td>
  </tr>
</tbody>
</table>


<br><br>


### 3. `LocalDate`와 `LocalTime`
#### 1) `LocalDate` 예제
```java
import java.time.*;

public class LocalDateExample {
    public static void main(String[] args) {
        
        LocalDate today = LocalDate.now();                              // 현재 날짜
        LocalDate specificDate = LocalDate.of(2024, 11, 22);            // 특정 날짜
        LocalDate parsedDate = LocalDate.parse("2024-11-22");           // 문자열 파싱
      
        System.out.println("오늘 날짜 : " + today);
        System.out.println("특정 날짜 : " + specificDate);
        System.out.println("파싱한 날짜 : " + parsedDate);
        
        LocalDate tomorrow = today.plusDay(1);                          // 날짜 더하기
        LocalDate lastMonth = today.minusMonths(1);                     // 날짜 빼기
      
        System.out.println("내일 : " + tomorrow);
        System.out.println("지난 달 : " + lastMonth);
    }
}

/**
 * 결과
 * 
 * 오늘 날짜: 2024-11-22
 * 특정 날짜: 2024-11-22
 * 파싱한 날짜: 2024-11-22
 * 내일: 2024-11-23
 * 지난달: 2024-10-22
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

### 3. `LocalDateTime`와 `ZonedDateTime`
#### 1) `LocalDateTime` 예제
```java
import java.time.*;

public class LocalDateTimeExample {
    public static void main(String[] args) {
        
        LocalDateTime now = LocalDateTime.now();  
        LocalDateTime meeting = LocalDateTime.of(2024, 11, 22, 15, 30);
      
        System.out.println("현재 날짜와 시간 : " + now);
        System.out.println("회의 시간 : " + meeting);
    }
}
```

<br>

#### 2) `ZonedDateTime` 예제
```java
import java.time.*;

public class ZonedDateTimeExample {
    public static void main(String[] args) {
        
        ZonedDateTime nowInUtc = ZonedDateTime.now(ZoneId.of("UTC"));
        ZonedDateTime nowInSeoul = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));                
      
        System.out.println("UTC 현재 시간 : " + nowInUtc);
        System.out.println("서울 현재 시간 : " + nowInSeoul);
    }
}

```

<br>

### 4. `Duration`과 `Period`
#### 1) 두 시간 간의 차이 계산 (Duration)
```java
import java.time.*;

public class DurationExample {
    public static void main(String[] args) {
        
        LocalTime start = LocalTime.of(9, 0);  
        LocalTime end = LocalTime.of(17, 0);
      
        Duration duration = Duration.between(start, end);
        System.out.println("근무 시간 : " + duration.toHours() + "시간");
    }
}
```

<br>

#### 2) 두 날짜 간의 차이 계산 (Period)
```java
import java.time.*;

public class PeriodExample {
    public static void main(String[] args) {
        
        LocalDate start = LocalDate.of(2024, 1, 1);
        LocalDate end = LocalDate.of(2024, 12, 31);                
      
        Period period = Period.between(start, end);
        System.out.println("기간 : " + period.getYears() + "년 " + period.getMonths() + "개월 " + period.getDays() + "일");
    }
}

```


<br>


### 5. `DateTimeFormatter`
#### 1) 포맷팅 및 파싱
```java
import java.time.*;

public class DateTimeFormatterExample {
    public static void main(String[] args) {
        
        LocalDateTime now = LocalDateTime.now();
      
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        String formatted = now.format(formatter);
        
        System.out.println("포맷된 날짜와 시간 : " + formatted);
        
        LocalDateTime parsed = LocalDateTime.parse("2024-11-22 15:30:00", formatter);
        System.out.println("파싱된 날짜와 시간 : " + parsed);
    }
}
```

<br>

### 6. 주요 클래스 관계
```text
LocalDate      ->   날짜 정보만 포함 (예 : 2024-11-22)
LocalTime      ->   시간 정보만 포함 (예 : 14:30:00)
LocalDateTime  ->   날짜 + 시간 정보만 포함 (예 : 2024-11-22T14:30:00)
ZonedDateTime  ->   날짜 + 시간 + 타임존 정보 포함
```

<br>

### 7. API의 주요 흐름
```text
날짜와 시간 생성
    |
+---+---+---+
|   |   |   |
v   v   v   v
LocalDate, LocalTime, LocalDateTime, ZonedDateTime
    |
+---+---+---+
|   |   |   |
조작, 비교, 포맷팅
```
