# DateTime 01. Java 8 Time API 개요

> 날짜와 시간 처리의 새로운 표준 - java.time 패키지 완전 이해  
> Java API Reference

---

## 📑 목차

1. [레거시 API의 문제점](#1-레거시-api의-문제점)
2. [Java 8 Time API 소개](#2-java-8-time-api-소개)
3. [주요 클래스 개요](#3-주요-클래스-개요)
4. [설계 원칙과 특징](#4-설계-원칙과-특징)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. 레거시 API의 문제점

### 1.1 Date 클래스의 문제

```java
import java.util.Date;

public class DateProblems {
    public static void main(String[] args) {
        // 문제 1: 가변 객체 (Mutable)
        Date date = new Date();
        System.out.println("Before: " + date);
        date.setTime(0);  // 변경 가능!
        System.out.println("After: " + date);
        
        
        // 문제 2: 이상한 API
        Date date2 = new Date(2024, 12, 25);  // 3924-01-25로 생성됨!
        System.out.println(date2);
        // year는 1900년 기준, month는 0부터 시작
        
        
        // 문제 3: 타입 안정성 부족
        Date date3 = new Date();
        date3.setMonth(15);  // 15월? 컴파일 에러 없음!
        System.out.println(date3);
        
        
        // 문제 4: 시간대 처리 복잡
        // Date는 내부적으로 UTC 시간만 저장
        // 현지 시간대 처리가 매우 복잡
    }
}
```

### 1.2 Calendar 클래스의 문제

```java
import java.util.Calendar;

public class CalendarProblems {
    public static void main(String[] args) {
        // 문제 1: 여전히 가변
        Calendar cal = Calendar.getInstance();
        cal.set(2024, Calendar.DECEMBER, 25);
        System.out.println(cal.getTime());
        
        cal.set(Calendar.MONTH, Calendar.JANUARY);  // 변경됨!
        System.out.println(cal.getTime());
        
        
        // 문제 2: 상수 사용의 불편함
        cal.set(Calendar.MONTH, 11);  // 12월인데 11?
        cal.set(Calendar.MONTH, Calendar.DECEMBER);  // 이게 더 명확
        
        
        // 문제 3: 요일이 1(일요일)부터 시작
        int dayOfWeek = cal.get(Calendar.DAY_OF_WEEK);
        System.out.println(dayOfWeek);  // 1=일요일, 7=토요일 (혼란스러움)
        
        
        // 문제 4: 사용하기 불편
        // 날짜 연산이 복잡하고 직관적이지 않음
        cal.add(Calendar.DATE, 10);
        cal.add(Calendar.MONTH, -2);
    }
}
```

### 1.3 SimpleDateFormat의 문제

```java
import java.text.SimpleDateFormat;
import java.util.Date;

public class SimpleDateFormatProblems {
    public static void main(String[] args) throws Exception {
        // 문제 1: 스레드 안전하지 않음!
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
        
        // 멀티스레드 환경에서 사용 시 문제 발생 가능
        // 매번 새로 생성해야 함 (성능 저하)
        
        
        // 문제 2: 예외 처리 필요
        try {
            Date date = sdf.parse("2024-12-25");
        } catch (Exception e) {
            // ParseException 처리 필요
        }
        
        
        // 문제 3: 타입 안정성 없음
        String dateStr = sdf.format(new Date());  // 잘못된 패턴도 실행 시점에만 오류
    }
}
```

---

## 2. Java 8 Time API 소개

### 2.1 java.time 패키지

```java
// Java 8부터 도입된 새로운 날짜/시간 API
import java.time.*;
import java.time.format.*;
import java.time.temporal.*;

public class TimeAPIIntro {
    public static void main(String[] args) {
        // 현재 날짜
        LocalDate today = LocalDate.now();
        System.out.println("Today: " + today);
        
        // 현재 시간
        LocalTime now = LocalTime.now();
        System.out.println("Now: " + now);
        
        // 현재 날짜와 시간
        LocalDateTime dateTime = LocalDateTime.now();
        System.out.println("DateTime: " + dateTime);
        
        // 시간대 포함
        ZonedDateTime zonedDateTime = ZonedDateTime.now();
        System.out.println("ZonedDateTime: " + zonedDateTime);
        
        // UTC 기준 타임스탬프
        Instant instant = Instant.now();
        System.out.println("Instant: " + instant);
    }
}
```

### 2.2 주요 개선사항

```java
public class ImprovementsDemo {
    public static void main(String[] args) {
        // 1. 불변 객체 (Immutable)
        LocalDate date = LocalDate.of(2024, 12, 25);
        LocalDate newDate = date.plusDays(10);  // 새 객체 반환
        System.out.println("Original: " + date);      // 2024-12-25
        System.out.println("New: " + newDate);        // 2025-01-04
        
        
        // 2. 직관적인 API
        LocalDate christmas = LocalDate.of(2024, Month.DECEMBER, 25);
        LocalDate newYear = LocalDate.of(2025, 1, 1);
        
        Period period = Period.between(christmas, newYear);
        System.out.println("Days: " + period.getDays());  // 7
        
        
        // 3. 메서드 체이닝
        LocalDateTime result = LocalDateTime.now()
                                           .plusDays(1)
                                           .plusHours(3)
                                           .minusMinutes(30);
        System.out.println(result);
        
        
        // 4. 타입 안정성
        // LocalDate date2 = LocalDate.of(2024, 13, 25);  // 컴파일 에러!
        // Month는 enum이므로 잘못된 값 입력 불가
    }
}
```

---

## 3. 주요 클래스 개요

### 3.1 핵심 클래스 구조

```
java.time 패키지 구조:

📦 날짜/시간 표현
├── LocalDate          날짜만 (2024-12-25)
├── LocalTime          시간만 (14:30:00)
├── LocalDateTime      날짜+시간 (시간대 없음)
├── ZonedDateTime      날짜+시간+시간대
└── Instant            UTC 기준 타임스탬프

📦 기간 표현
├── Period             날짜 기반 기간 (년, 월, 일)
└── Duration           시간 기반 기간 (시, 분, 초)

📦 보조 클래스
├── ZoneId             시간대
├── ZoneOffset         UTC 오프셋
├── DayOfWeek          요일 (enum)
├── Month              월 (enum)
└── Year               년도

📦 포맷팅
├── DateTimeFormatter  날짜/시간 포맷
└── DateTimeFormatterBuilder

📦 조정
└── TemporalAdjusters  날짜 조정 유틸
```

### 3.2 LocalDate - 날짜만

```java
import java.time.LocalDate;
import java.time.Month;

public class LocalDateDemo {
    public static void main(String[] args) {
        // 생성
        LocalDate today = LocalDate.now();
        LocalDate specificDate = LocalDate.of(2024, 12, 25);
        LocalDate withMonth = LocalDate.of(2024, Month.DECEMBER, 25);
        
        
        // 읽기
        int year = today.getYear();
        Month month = today.getMonth();
        int monthValue = today.getMonthValue();  // 1~12
        int day = today.getDayOfMonth();
        DayOfWeek dayOfWeek = today.getDayOfWeek();
        
        System.out.println("Year: " + year);
        System.out.println("Month: " + month);
        System.out.println("Day: " + day);
        System.out.println("DayOfWeek: " + dayOfWeek);
        
        
        // 수정 (불변 - 새 객체 반환)
        LocalDate tomorrow = today.plusDays(1);
        LocalDate nextMonth = today.plusMonths(1);
        LocalDate lastYear = today.minusYears(1);
        
        
        // 비교
        boolean isBefore = today.isBefore(tomorrow);
        boolean isAfter = today.isAfter(lastYear);
        boolean isEqual = today.isEqual(today);
        
        System.out.println("isBefore: " + isBefore);  // true
    }
}
```

### 3.3 LocalTime - 시간만

```java
import java.time.LocalTime;

public class LocalTimeDemo {
    public static void main(String[] args) {
        // 생성
        LocalTime now = LocalTime.now();
        LocalTime specificTime = LocalTime.of(14, 30, 0);
        LocalTime withNano = LocalTime.of(14, 30, 0, 123456789);
        
        
        // 읽기
        int hour = now.getHour();
        int minute = now.getMinute();
        int second = now.getSecond();
        int nano = now.getNano();
        
        System.out.println("Hour: " + hour);
        System.out.println("Minute: " + minute);
        
        
        // 수정
        LocalTime later = now.plusHours(2);
        LocalTime earlier = now.minusMinutes(30);
        
        
        // 비교
        boolean isBefore = now.isBefore(later);
        System.out.println("isBefore: " + isBefore);  // true
    }
}
```

### 3.4 LocalDateTime - 날짜와 시간

```java
import java.time.LocalDateTime;

public class LocalDateTimeDemo {
    public static void main(String[] args) {
        // 생성
        LocalDateTime now = LocalDateTime.now();
        LocalDateTime specific = LocalDateTime.of(2024, 12, 25, 14, 30, 0);
        
        // LocalDate + LocalTime 조합
        LocalDate date = LocalDate.of(2024, 12, 25);
        LocalTime time = LocalTime.of(14, 30);
        LocalDateTime combined = LocalDateTime.of(date, time);
        
        
        // 읽기 (날짜 + 시간 메서드 모두 사용 가능)
        int year = now.getYear();
        int month = now.getMonthValue();
        int hour = now.getHour();
        
        
        // 변환
        LocalDate dateOnly = now.toLocalDate();
        LocalTime timeOnly = now.toLocalTime();
        
        
        // 수정
        LocalDateTime tomorrow = now.plusDays(1);
        LocalDateTime nextHour = now.plusHours(1);
    }
}
```

### 3.5 ZonedDateTime - 시간대 포함

```java
import java.time.ZonedDateTime;
import java.time.ZoneId;

public class ZonedDateTimeDemo {
    public static void main(String[] args) {
        // 현재 시간 (시스템 기본 시간대)
        ZonedDateTime now = ZonedDateTime.now();
        System.out.println(now);
        
        
        // 특정 시간대
        ZonedDateTime tokyo = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"));
        ZonedDateTime newYork = ZonedDateTime.now(ZoneId.of("America/New_York"));
        ZonedDateTime seoul = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));
        
        System.out.println("Tokyo: " + tokyo);
        System.out.println("New York: " + newYork);
        System.out.println("Seoul: " + seoul);
        
        
        // LocalDateTime에 시간대 추가
        LocalDateTime ldt = LocalDateTime.of(2024, 12, 25, 14, 30);
        ZonedDateTime zdt = ldt.atZone(ZoneId.of("Asia/Seoul"));
        
        
        // 시간대 변환
        ZonedDateTime tokyoTime = seoul.withZoneSameInstant(ZoneId.of("Asia/Tokyo"));
        System.out.println("Seoul: " + seoul);
        System.out.println("Tokyo: " + tokyoTime);
    }
}
```

### 3.6 Instant - UTC 타임스탬프

```java
import java.time.Instant;

public class InstantDemo {
    public static void main(String[] args) {
        // 현재 UTC 시간
        Instant now = Instant.now();
        System.out.println("Now: " + now);
        
        
        // epoch 기준 (1970-01-01 00:00:00 UTC)
        Instant epoch = Instant.ofEpochSecond(0);
        System.out.println("Epoch: " + epoch);
        
        
        // 밀리초 변환
        long epochMilli = now.toEpochMilli();
        Instant fromMilli = Instant.ofEpochMilli(epochMilli);
        
        System.out.println("Epoch milli: " + epochMilli);
        
        
        // 연산
        Instant later = now.plusSeconds(3600);  // 1시간 후
        Instant earlier = now.minusSeconds(60);  // 1분 전
        
        
        // 비교
        boolean isBefore = now.isBefore(later);
        System.out.println("isBefore: " + isBefore);  // true
    }
}
```

---

## 4. 설계 원칙과 특징

### 4.1 불변성 (Immutability)

```java
public class ImmutabilityDemo {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        
        // 모든 수정 메서드는 새 객체 반환
        LocalDate newDate = date.plusDays(10);
        
        System.out.println("Original: " + date);     // 2024-12-25
        System.out.println("Modified: " + newDate);  // 2025-01-04
        
        
        // 장점 1: 스레드 안전
        // 여러 스레드에서 동시에 사용해도 안전
        
        // 장점 2: 예측 가능
        // 메서드 호출로 원본이 변경되지 않음
        
        // 장점 3: 캐싱 가능
        // 불변 객체는 안전하게 캐시 가능
    }
}
```

### 4.2 메서드 체이닝 (Fluent API)

```java
public class FluentAPIDemo {
    public static void main(String[] args) {
        // 여러 연산을 연결
        LocalDateTime result = LocalDateTime.now()
                                           .plusDays(7)
                                           .plusHours(3)
                                           .minusMinutes(30)
                                           .withSecond(0)
                                           .withNano(0);
        
        System.out.println(result);
        
        
        // 가독성 좋은 코드
        LocalDate nextMonday = LocalDate.now()
                                       .plusWeeks(1)
                                       .with(DayOfWeek.MONDAY);
    }
}
```

### 4.3 명확한 의미 (Clear Semantics)

```java
import java.time.temporal.ChronoUnit;

public class ClearSemanticsDemo {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now();
        
        // plus vs with
        LocalDate plusDays = date.plusDays(10);      // 더하기
        LocalDate withDay = date.withDayOfMonth(10);  // 설정
        
        System.out.println("plusDays: " + plusDays);
        System.out.println("withDay: " + withDay);
        
        
        // ChronoUnit으로 명확한 단위
        long days = ChronoUnit.DAYS.between(date, plusDays);
        System.out.println("Days between: " + days);  // 10
        
        
        // 타입으로 의미 전달
        Period period = Period.ofDays(10);     // 날짜 기반
        Duration duration = Duration.ofHours(24);  // 시간 기반
    }
}
```

### 4.4 확장 가능한 설계

```java
import java.time.temporal.TemporalAdjuster;

public class ExtensibleDemo {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now();
        
        // 내장 조정자
        LocalDate nextMonday = date.with(TemporalAdjusters.next(DayOfWeek.MONDAY));
        LocalDate lastDayOfMonth = date.with(TemporalAdjusters.lastDayOfMonth());
        
        
        // 커스텀 조정자
        TemporalAdjuster nextWorkingDay = temporal -> {
            LocalDate result = LocalDate.from(temporal);
            do {
                result = result.plusDays(1);
            } while (result.getDayOfWeek() == DayOfWeek.SATURDAY ||
                     result.getDayOfWeek() == DayOfWeek.SUNDAY);
            return result;
        };
        
        LocalDate nextWorking = date.with(nextWorkingDay);
        System.out.println("Next working day: " + nextWorking);
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 나이 계산

```java
// 생년월일로 나이 계산 (만 나이)
public class Problem1 {
    public static int calculateAge(LocalDate birthDate) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        LocalDate birthDate = LocalDate.of(1990, 5, 15);
        int age = calculateAge(birthDate);
        System.out.println("Age: " + age);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static int calculateAge(LocalDate birthDate) {
        LocalDate today = LocalDate.now();
        return Period.between(birthDate, today).getYears();
    }
    
    // 더 자세한 버전
    public static String calculateAgeDetailed(LocalDate birthDate) {
        LocalDate today = LocalDate.now();
        Period period = Period.between(birthDate, today);
        
        return String.format("%d년 %d개월 %d일",
                           period.getYears(),
                           period.getMonths(),
                           period.getDays());
    }
}
```
</details>

---

### 문제 2: D-Day 계산

```java
// 특정 날짜까지 남은 일수
public class Problem2 {
    public static long daysUntil(LocalDate targetDate) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        LocalDate newYear = LocalDate.of(2025, 1, 1);
        long days = daysUntil(newYear);
        System.out.println("D-" + days);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static long daysUntil(LocalDate targetDate) {
        LocalDate today = LocalDate.now();
        return ChronoUnit.DAYS.between(today, targetDate);
    }
    
    // 더 자세한 버전
    public static String daysUntilDetailed(LocalDate targetDate) {
        LocalDate today = LocalDate.now();
        long days = ChronoUnit.DAYS.between(today, targetDate);
        
        if (days > 0) {
            return "D-" + days;
        } else if (days < 0) {
            return "D+" + Math.abs(days);
        } else {
            return "D-Day!";
        }
    }
}
```
</details>

---

### 문제 3: 근무일 계산

```java
// 주말 제외 근무일 개수 계산
public class Problem3 {
    public static long countWorkingDays(LocalDate start, LocalDate end) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        LocalDate start = LocalDate.of(2024, 12, 1);
        LocalDate end = LocalDate.of(2024, 12, 31);
        
        long workingDays = countWorkingDays(start, end);
        System.out.println("Working days: " + workingDays);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static long countWorkingDays(LocalDate start, LocalDate end) {
        long count = 0;
        LocalDate current = start;
        
        while (!current.isAfter(end)) {
            DayOfWeek day = current.getDayOfWeek();
            if (day != DayOfWeek.SATURDAY && day != DayOfWeek.SUNDAY) {
                count++;
            }
            current = current.plusDays(1);
        }
        
        return count;
    }
    
    // Stream 버전
    public static long countWorkingDaysStream(LocalDate start, LocalDate end) {
        return Stream.iterate(start, date -> !date.isAfter(end), date -> date.plusDays(1))
                    .filter(date -> {
                        DayOfWeek day = date.getDayOfWeek();
                        return day != DayOfWeek.SATURDAY && day != DayOfWeek.SUNDAY;
                    })
                    .count();
    }
}
```
</details>

---

## 📌 핵심 정리

### 레거시 vs 신규 API

| 구분 | 레거시 (Date/Calendar) | 신규 (java.time) |
|------|----------------------|------------------|
| **가변성** | 가변 (Mutable) | 불변 (Immutable) |
| **스레드 안전** | 불안전 | 안전 |
| **API 설계** | 복잡하고 혼란스러움 | 직관적이고 명확 |
| **시간대** | 복잡 | 간단 (ZonedDateTime) |
| **타입 안정성** | 약함 | 강함 (enum 활용) |

### 주요 클래스 선택

```java
// 날짜만 필요
LocalDate date = LocalDate.now();

// 시간만 필요
LocalTime time = LocalTime.now();

// 날짜 + 시간 (시간대 불필요)
LocalDateTime dateTime = LocalDateTime.now();

// 날짜 + 시간 + 시간대
ZonedDateTime zonedDateTime = ZonedDateTime.now();

// UTC 타임스탬프
Instant instant = Instant.now();
```

### 핵심 특징

```java
// 1. 불변성
LocalDate date = LocalDate.now();
date.plusDays(1);  // 새 객체 반환, 원본 불변

// 2. 메서드 체이닝
LocalDateTime result = LocalDateTime.now()
                                   .plusDays(1)
                                   .withHour(14);

// 3. 명확한 의미
date.plusDays(10);      // 더하기
date.withDay(10);       // 설정

// 4. Period vs Duration
Period.ofDays(10);      // 날짜 기간
Duration.ofHours(24);   // 시간 기간
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. LocalDate, LocalTime, LocalDateTime →](./DateTime-02-Local.md)**

</div>
