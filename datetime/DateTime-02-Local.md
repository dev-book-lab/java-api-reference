# DateTime 02. LocalDate, LocalTime, LocalDateTime

> 시간대 없는 날짜/시간 처리 - Local 클래스 완전 정복  
> Java API Reference

---

## 📑 목차

1. [LocalDate - 날짜만](#1-localdate---날짜만)
2. [LocalTime - 시간만](#2-localtime---시간만)
3. [LocalDateTime - 날짜와 시간](#3-localdatetime---날짜와-시간)
4. [상호 변환과 조합](#4-상호-변환과-조합)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. LocalDate - 날짜만

### 1.1 생성

```java
import java.time.LocalDate;
import java.time.Month;

public class LocalDateCreation {
    public static void main(String[] args) {
        // 현재 날짜
        LocalDate today = LocalDate.now();
        System.out.println("Today: " + today);  // 2024-12-16
        
        
        // 특정 날짜 생성
        LocalDate date1 = LocalDate.of(2024, 12, 25);
        LocalDate date2 = LocalDate.of(2024, Month.DECEMBER, 25);
        
        
        // 연도와 일수로
        LocalDate date3 = LocalDate.ofYearDay(2024, 100);  // 2024년 100번째 날
        System.out.println("Day 100: " + date3);
        
        
        // Epoch 기준 (1970-01-01부터)
        LocalDate date4 = LocalDate.ofEpochDay(0);
        System.out.println("Epoch: " + date4);  // 1970-01-01
        
        
        // 문자열 파싱
        LocalDate date5 = LocalDate.parse("2024-12-25");
        System.out.println("Parsed: " + date5);
    }
}
```

### 1.2 읽기 (Getter)

```java
public class LocalDateGetter {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        
        // 년, 월, 일
        int year = date.getYear();
        Month month = date.getMonth();           // enum
        int monthValue = date.getMonthValue();    // 1~12
        int day = date.getDayOfMonth();
        
        System.out.println("Year: " + year);          // 2024
        System.out.println("Month: " + month);        // DECEMBER
        System.out.println("MonthValue: " + monthValue);  // 12
        System.out.println("Day: " + day);            // 25
        
        
        // 요일
        DayOfWeek dayOfWeek = date.getDayOfWeek();
        int dayOfWeekValue = dayOfWeek.getValue();  // 1(월)~7(일)
        
        System.out.println("DayOfWeek: " + dayOfWeek);  // WEDNESDAY
        System.out.println("Value: " + dayOfWeekValue);  // 3
        
        
        // 년 중 몇 번째 날
        int dayOfYear = date.getDayOfYear();
        System.out.println("DayOfYear: " + dayOfYear);  // 360
        
        
        // 월의 길이
        int lengthOfMonth = date.lengthOfMonth();
        int lengthOfYear = date.lengthOfYear();
        
        System.out.println("Days in month: " + lengthOfMonth);  // 31
        System.out.println("Days in year: " + lengthOfYear);    // 366 (윤년)
    }
}
```

### 1.3 수정 (with/plus/minus)

```java
public class LocalDateModification {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        
        // with - 특정 필드 설정
        LocalDate newYear = date.withYear(2025);
        LocalDate march = date.withMonth(3);
        LocalDate firstDay = date.withDayOfMonth(1);
        LocalDate hundredthDay = date.withDayOfYear(100);
        
        System.out.println("Original: " + date);       // 2024-12-25
        System.out.println("New year: " + newYear);    // 2025-12-25
        System.out.println("March: " + march);         // 2024-03-25
        
        
        // plus - 더하기
        LocalDate tomorrow = date.plusDays(1);
        LocalDate nextWeek = date.plusWeeks(1);
        LocalDate nextMonth = date.plusMonths(1);
        LocalDate nextYear = date.plusYears(1);
        
        System.out.println("Tomorrow: " + tomorrow);    // 2024-12-26
        System.out.println("Next week: " + nextWeek);   // 2025-01-01
        
        
        // minus - 빼기
        LocalDate yesterday = date.minusDays(1);
        LocalDate lastWeek = date.minusWeeks(1);
        LocalDate lastMonth = date.minusMonths(1);
        LocalDate lastYear = date.minusYears(1);
        
        System.out.println("Yesterday: " + yesterday);  // 2024-12-24
        
        
        // ChronoUnit 사용
        LocalDate after100Days = date.plus(100, ChronoUnit.DAYS);
        System.out.println("After 100 days: " + after100Days);
    }
}
```

### 1.4 비교

```java
public class LocalDateComparison {
    public static void main(String[] args) {
        LocalDate date1 = LocalDate.of(2024, 12, 25);
        LocalDate date2 = LocalDate.of(2025, 1, 1);
        LocalDate date3 = LocalDate.of(2024, 12, 25);
        
        // isBefore, isAfter, isEqual
        System.out.println(date1.isBefore(date2));   // true
        System.out.println(date1.isAfter(date2));    // false
        System.out.println(date1.isEqual(date3));    // true
        
        
        // compareTo (-1, 0, 1)
        int result = date1.compareTo(date2);
        System.out.println("compareTo: " + result);  // -1 (date1 < date2)
        
        
        // equals
        boolean equals = date1.equals(date3);
        System.out.println("equals: " + equals);     // true
        
        
        // 범위 확인
        LocalDate today = LocalDate.now();
        LocalDate start = LocalDate.of(2024, 1, 1);
        LocalDate end = LocalDate.of(2024, 12, 31);
        
        boolean inRange = !today.isBefore(start) && !today.isAfter(end);
        System.out.println("In range: " + inRange);
    }
}
```

### 1.5 유틸리티 메서드

```java
public class LocalDateUtility {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 2, 15);
        
        // 윤년 확인
        boolean isLeapYear = date.isLeapYear();
        System.out.println("Leap year: " + isLeapYear);  // true (2024는 윤년)
        
        
        // 연도의 윤년 확인
        boolean is2025LeapYear = Year.isLeap(2025);
        System.out.println("2025 leap: " + is2025LeapYear);  // false
        
        
        // 월의 마지막 날
        int lastDay = date.lengthOfMonth();
        System.out.println("Last day of Feb 2024: " + lastDay);  // 29
        
        
        // Epoch Day (1970-01-01부터의 일수)
        long epochDay = date.toEpochDay();
        System.out.println("Epoch day: " + epochDay);
        
        
        // 유효성 검증
        boolean valid = date.getMonthValue() >= 1 && date.getMonthValue() <= 12;
        System.out.println("Valid: " + valid);
    }
}
```

---

## 2. LocalTime - 시간만

### 2.1 생성

```java
import java.time.LocalTime;

public class LocalTimeCreation {
    public static void main(String[] args) {
        // 현재 시간
        LocalTime now = LocalTime.now();
        System.out.println("Now: " + now);  // 14:30:45.123456789
        
        
        // 특정 시간 생성
        LocalTime time1 = LocalTime.of(14, 30);              // 14:30
        LocalTime time2 = LocalTime.of(14, 30, 0);          // 14:30:00
        LocalTime time3 = LocalTime.of(14, 30, 0, 123456789);  // 나노초까지
        
        
        // 초 기준 (자정부터)
        LocalTime time4 = LocalTime.ofSecondOfDay(3661);  // 01:01:01
        System.out.println("From seconds: " + time4);
        
        
        // 나노초 기준
        LocalTime time5 = LocalTime.ofNanoOfDay(1_000_000_000L);  // 00:00:01
        
        
        // 문자열 파싱
        LocalTime time6 = LocalTime.parse("14:30:00");
        System.out.println("Parsed: " + time6);
        
        
        // 특수 시간
        LocalTime midnight = LocalTime.MIDNIGHT;  // 00:00
        LocalTime noon = LocalTime.NOON;          // 12:00
        LocalTime min = LocalTime.MIN;            // 00:00
        LocalTime max = LocalTime.MAX;            // 23:59:59.999999999
    }
}
```

### 2.2 읽기

```java
public class LocalTimeGetter {
    public static void main(String[] args) {
        LocalTime time = LocalTime.of(14, 30, 45, 123456789);
        
        // 시, 분, 초, 나노초
        int hour = time.getHour();
        int minute = time.getMinute();
        int second = time.getSecond();
        int nano = time.getNano();
        
        System.out.println("Hour: " + hour);      // 14
        System.out.println("Minute: " + minute);  // 30
        System.out.println("Second: " + second);  // 45
        System.out.println("Nano: " + nano);      // 123456789
        
        
        // 하루 기준
        int secondOfDay = time.toSecondOfDay();
        long nanoOfDay = time.toNanoOfDay();
        
        System.out.println("Second of day: " + secondOfDay);  // 52245
        System.out.println("Nano of day: " + nanoOfDay);
    }
}
```

### 2.3 수정

```java
public class LocalTimeModification {
    public static void main(String[] args) {
        LocalTime time = LocalTime.of(14, 30, 0);
        
        // with - 설정
        LocalTime newHour = time.withHour(10);
        LocalTime newMinute = time.withMinute(0);
        LocalTime newSecond = time.withSecond(30);
        LocalTime newNano = time.withNano(123456789);
        
        System.out.println("Original: " + time);      // 14:30:00
        System.out.println("New hour: " + newHour);   // 10:30:00
        
        
        // plus - 더하기
        LocalTime plusHours = time.plusHours(2);
        LocalTime plusMinutes = time.plusMinutes(30);
        LocalTime plusSeconds = time.plusSeconds(45);
        LocalTime plusNanos = time.plusNanos(1_000_000_000);
        
        System.out.println("Plus 2 hours: " + plusHours);  // 16:30:00
        
        
        // minus - 빼기
        LocalTime minusHours = time.minusHours(1);
        LocalTime minusMinutes = time.minusMinutes(15);
        
        System.out.println("Minus 1 hour: " + minusHours);  // 13:30:00
        
        
        // 자정 넘어가는 경우
        LocalTime late = LocalTime.of(23, 30);
        LocalTime next = late.plusHours(2);
        System.out.println("Next: " + next);  // 01:30 (다음 날 자정 지남)
    }
}
```

### 2.4 비교

```java
public class LocalTimeComparison {
    public static void main(String[] args) {
        LocalTime time1 = LocalTime.of(14, 30);
        LocalTime time2 = LocalTime.of(16, 0);
        LocalTime time3 = LocalTime.of(14, 30);
        
        // isBefore, isAfter
        System.out.println(time1.isBefore(time2));  // true
        System.out.println(time1.isAfter(time2));   // false
        
        
        // compareTo
        int result = time1.compareTo(time2);
        System.out.println("compareTo: " + result);  // -1
        
        
        // equals
        boolean equals = time1.equals(time3);
        System.out.println("equals: " + equals);  // true
        
        
        // 범위 확인
        LocalTime start = LocalTime.of(9, 0);
        LocalTime end = LocalTime.of(18, 0);
        LocalTime current = LocalTime.now();
        
        boolean workingHours = !current.isBefore(start) && !current.isAfter(end);
        System.out.println("Working hours: " + workingHours);
    }
}
```

---

## 3. LocalDateTime - 날짜와 시간

### 3.1 생성

```java
import java.time.LocalDateTime;

public class LocalDateTimeCreation {
    public static void main(String[] args) {
        // 현재 날짜와 시간
        LocalDateTime now = LocalDateTime.now();
        System.out.println("Now: " + now);
        
        
        // 특정 날짜와 시간
        LocalDateTime dt1 = LocalDateTime.of(2024, 12, 25, 14, 30);
        LocalDateTime dt2 = LocalDateTime.of(2024, 12, 25, 14, 30, 0);
        LocalDateTime dt3 = LocalDateTime.of(2024, 12, 25, 14, 30, 0, 123456789);
        
        
        // LocalDate + LocalTime 조합
        LocalDate date = LocalDate.of(2024, 12, 25);
        LocalTime time = LocalTime.of(14, 30);
        LocalDateTime dt4 = LocalDateTime.of(date, time);
        
        
        // 문자열 파싱
        LocalDateTime dt5 = LocalDateTime.parse("2024-12-25T14:30:00");
        System.out.println("Parsed: " + dt5);
        
        
        // LocalDate에서 변환
        LocalDateTime dt6 = date.atTime(14, 30);
        LocalDateTime dt7 = date.atTime(time);
        LocalDateTime dt8 = date.atStartOfDay();  // 자정
        
        
        // LocalTime에서 변환
        LocalDateTime dt9 = time.atDate(date);
    }
}
```

### 3.2 읽기

```java
public class LocalDateTimeGetter {
    public static void main(String[] args) {
        LocalDateTime dt = LocalDateTime.of(2024, 12, 25, 14, 30, 45);
        
        // 날짜 필드 (LocalDate 메서드)
        int year = dt.getYear();
        int month = dt.getMonthValue();
        int day = dt.getDayOfMonth();
        DayOfWeek dayOfWeek = dt.getDayOfWeek();
        
        System.out.println("Year: " + year);      // 2024
        System.out.println("Month: " + month);    // 12
        System.out.println("Day: " + day);        // 25
        System.out.println("DayOfWeek: " + dayOfWeek);  // WEDNESDAY
        
        
        // 시간 필드 (LocalTime 메서드)
        int hour = dt.getHour();
        int minute = dt.getMinute();
        int second = dt.getSecond();
        
        System.out.println("Hour: " + hour);      // 14
        System.out.println("Minute: " + minute);  // 30
        System.out.println("Second: " + second);  // 45
    }
}
```

### 3.3 수정

```java
public class LocalDateTimeModification {
    public static void main(String[] args) {
        LocalDateTime dt = LocalDateTime.of(2024, 12, 25, 14, 30, 0);
        
        // with - 날짜/시간 설정
        LocalDateTime newYear = dt.withYear(2025);
        LocalDateTime newHour = dt.withHour(10);
        
        System.out.println("Original: " + dt);      // 2024-12-25T14:30
        System.out.println("New year: " + newYear); // 2025-12-25T14:30
        System.out.println("New hour: " + newHour); // 2024-12-25T10:30
        
        
        // plus - 날짜/시간 더하기
        LocalDateTime tomorrow = dt.plusDays(1);
        LocalDateTime nextHour = dt.plusHours(1);
        LocalDateTime complex = dt.plusDays(7).plusHours(3).plusMinutes(30);
        
        System.out.println("Tomorrow: " + tomorrow);  // 2024-12-26T14:30
        System.out.println("Next hour: " + nextHour); // 2024-12-25T15:30
        System.out.println("Complex: " + complex);    // 2025-01-01T18:00
        
        
        // minus - 빼기
        LocalDateTime yesterday = dt.minusDays(1);
        LocalDateTime lastHour = dt.minusHours(1);
        
        System.out.println("Yesterday: " + yesterday);  // 2024-12-24T14:30
    }
}
```

### 3.4 비교

```java
public class LocalDateTimeComparison {
    public static void main(String[] args) {
        LocalDateTime dt1 = LocalDateTime.of(2024, 12, 25, 14, 30);
        LocalDateTime dt2 = LocalDateTime.of(2024, 12, 25, 16, 0);
        LocalDateTime dt3 = LocalDateTime.of(2024, 12, 26, 14, 30);
        
        // isBefore, isAfter, isEqual
        System.out.println(dt1.isBefore(dt2));  // true (같은 날, 이른 시간)
        System.out.println(dt1.isBefore(dt3));  // true (다른 날)
        
        
        // compareTo
        int result1 = dt1.compareTo(dt2);
        int result2 = dt1.compareTo(dt3);
        
        System.out.println("Compare with dt2: " + result1);  // -1
        System.out.println("Compare with dt3: " + result2);  // -1
        
        
        // equals
        LocalDateTime dt4 = LocalDateTime.of(2024, 12, 25, 14, 30);
        System.out.println(dt1.equals(dt4));  // true
    }
}
```

---

## 4. 상호 변환과 조합

### 4.1 LocalDate ↔ LocalDateTime

```java
public class DateTimeConversion {
    public static void main(String[] args) {
        // LocalDate → LocalDateTime
        LocalDate date = LocalDate.of(2024, 12, 25);
        
        LocalDateTime atMidnight = date.atStartOfDay();
        LocalDateTime atTime = date.atTime(14, 30);
        LocalDateTime atLocalTime = date.atTime(LocalTime.of(14, 30));
        
        System.out.println("At midnight: " + atMidnight);  // 2024-12-25T00:00
        System.out.println("At 14:30: " + atTime);        // 2024-12-25T14:30
        
        
        // LocalDateTime → LocalDate
        LocalDateTime dateTime = LocalDateTime.now();
        LocalDate extractedDate = dateTime.toLocalDate();
        
        System.out.println("DateTime: " + dateTime);
        System.out.println("Date only: " + extractedDate);
    }
}
```

### 4.2 LocalTime ↔ LocalDateTime

```java
public class TimeConversion {
    public static void main(String[] args) {
        // LocalTime → LocalDateTime
        LocalTime time = LocalTime.of(14, 30);
        LocalDate date = LocalDate.of(2024, 12, 25);
        
        LocalDateTime dateTime1 = time.atDate(date);
        System.out.println("DateTime: " + dateTime1);  // 2024-12-25T14:30
        
        
        // LocalDateTime → LocalTime
        LocalDateTime dateTime = LocalDateTime.now();
        LocalTime extractedTime = dateTime.toLocalTime();
        
        System.out.println("DateTime: " + dateTime);
        System.out.println("Time only: " + extractedTime);
    }
}
```

### 4.3 조합 예제

```java
public class CombinationExamples {
    public static void main(String[] args) {
        // 오늘 특정 시간
        LocalDateTime todayAtNoon = LocalDate.now().atTime(12, 0);
        System.out.println("Today at noon: " + todayAtNoon);
        
        
        // 특정 날짜의 자정
        LocalDateTime christmasMidnight = LocalDate.of(2024, 12, 25)
                                                  .atStartOfDay();
        
        
        // 현재 날짜에 특정 시간 설정
        LocalDateTime now = LocalDateTime.now();
        LocalDateTime todayAt9AM = now.toLocalDate().atTime(9, 0);
        
        
        // 날짜는 유지하고 시간만 변경
        LocalDateTime newDateTime = now.withHour(14).withMinute(30).withSecond(0);
        
        System.out.println("Original: " + now);
        System.out.println("Modified: " + newDateTime);
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 생일 축하

```java
// 오늘이 생일인지 확인
public class Problem1 {
    public static boolean isBirthday(LocalDate birthDate) {
        // 여기에 코드 작성
        return false;
    }
    
    public static void main(String[] args) {
        LocalDate birthDate = LocalDate.of(1990, 12, 16);
        
        if (isBirthday(birthDate)) {
            System.out.println("Happy Birthday!");
        } else {
            System.out.println("Not your birthday today.");
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static boolean isBirthday(LocalDate birthDate) {
        LocalDate today = LocalDate.now();
        return today.getMonthValue() == birthDate.getMonthValue() &&
               today.getDayOfMonth() == birthDate.getDayOfMonth();
    }
    
    // MonthDay 사용 버전
    public static boolean isBirthday2(LocalDate birthDate) {
        MonthDay birthday = MonthDay.of(birthDate.getMonth(), birthDate.getDayOfMonth());
        MonthDay today = MonthDay.now();
        return today.equals(birthday);
    }
}
```
</details>

---

### 문제 2: 근무 시간 계산

```java
// 출근 시간과 퇴근 시간으로 근무 시간 계산 (점심시간 1시간 제외)
public class Problem2 {
    public static long calculateWorkHours(LocalTime checkIn, LocalTime checkOut) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        LocalTime checkIn = LocalTime.of(9, 0);
        LocalTime checkOut = LocalTime.of(18, 0);
        
        long workHours = calculateWorkHours(checkIn, checkOut);
        System.out.println("Work hours: " + workHours);  // 8
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static long calculateWorkHours(LocalTime checkIn, LocalTime checkOut) {
        Duration duration = Duration.between(checkIn, checkOut);
        long hours = duration.toHours();
        
        // 점심시간 1시간 제외
        return hours - 1;
    }
    
    // 더 자세한 버전 (분 단위까지)
    public static String calculateWorkTime(LocalTime checkIn, LocalTime checkOut) {
        Duration duration = Duration.between(checkIn, checkOut);
        long totalMinutes = duration.toMinutes();
        
        // 점심시간 1시간 제외
        totalMinutes -= 60;
        
        long hours = totalMinutes / 60;
        long minutes = totalMinutes % 60;
        
        return String.format("%d시간 %d분", hours, minutes);
    }
}
```
</details>

---

### 문제 3: 다음 평일 찾기

```java
// 다음 평일(월~금) 찾기
public class Problem3 {
    public static LocalDate nextWeekday(LocalDate date) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        LocalDate friday = LocalDate.of(2024, 12, 20);  // 금요일
        LocalDate nextDay = nextWeekday(friday);
        
        System.out.println("Next weekday: " + nextDay);  // 2024-12-23 (월요일)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static LocalDate nextWeekday(LocalDate date) {
        LocalDate next = date.plusDays(1);
        
        while (next.getDayOfWeek() == DayOfWeek.SATURDAY ||
               next.getDayOfWeek() == DayOfWeek.SUNDAY) {
            next = next.plusDays(1);
        }
        
        return next;
    }
    
    // TemporalAdjuster 사용 버전
    public static LocalDate nextWeekday2(LocalDate date) {
        return date.with(temporal -> {
            LocalDate result = LocalDate.from(temporal).plusDays(1);
            
            while (result.getDayOfWeek() == DayOfWeek.SATURDAY ||
                   result.getDayOfWeek() == DayOfWeek.SUNDAY) {
                result = result.plusDays(1);
            }
            
            return result;
        });
    }
}
```
</details>

---

## 📌 핵심 정리

### LocalDate
```java
// 생성
LocalDate.now()                        // 현재 날짜
LocalDate.of(2024, 12, 25)            // 특정 날짜
LocalDate.parse("2024-12-25")         // 문자열 파싱

// 읽기
date.getYear()                         // 년
date.getMonthValue()                   // 월 (1~12)
date.getDayOfMonth()                   // 일
date.getDayOfWeek()                    // 요일

// 수정
date.plusDays(1)                       // 더하기
date.minusDays(1)                      // 빼기
date.withYear(2025)                    // 설정
```

### LocalTime
```java
// 생성
LocalTime.now()                        // 현재 시간
LocalTime.of(14, 30)                  // 특정 시간
LocalTime.parse("14:30:00")           // 문자열 파싱

// 읽기
time.getHour()                         // 시
time.getMinute()                       // 분
time.getSecond()                       // 초

// 수정
time.plusHours(1)                      // 더하기
time.minusMinutes(30)                  // 빼기
time.withHour(14)                      // 설정
```

### LocalDateTime
```java
// 생성
LocalDateTime.now()                    // 현재
LocalDateTime.of(date, time)          // 조합
date.atTime(14, 30)                   // LocalDate → LocalDateTime
time.atDate(date)                     // LocalTime → LocalDateTime

// 분리
dateTime.toLocalDate()                // → LocalDate
dateTime.toLocalTime()                // → LocalTime

// 수정 (날짜 + 시간 메서드 모두 사용 가능)
dateTime.plusDays(1).plusHours(3)
```

### 주의사항
```java
// ❌ 가변 (변경되지 않음)
LocalDate date = LocalDate.now();
date.plusDays(1);  // 반환값 무시!

// ✅ 불변 (새 객체 반환)
LocalDate tomorrow = date.plusDays(1);

// ✅ 메서드 체이닝
LocalDateTime result = LocalDateTime.now()
                                   .plusDays(1)
                                   .withHour(14);
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Time API 개요](./DateTime-01-개요.md) | [다음: 03. ZonedDateTime & Instant →](./DateTime-03-Zoned.md)**

</div>
