# DateTime 06. 레거시 vs 신규 API

> Date/Calendar에서 java.time으로 - 마이그레이션 가이드  
> Java API Reference

---

## 📑 목차

1. [레거시 API 복습](#1-레거시-api-복습)
2. [신규 API로 변환](#2-신규-api로-변환)
3. [상호 변환 방법](#3-상호-변환-방법)
4. [마이그레이션 패턴](#4-마이그레이션-패턴)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. 레거시 API 복습

### 1.1 Date 클래스

```java
import java.util.Date;

public class DateReview {
    public static void main(String[] args) {
        // 생성
        Date now = new Date();
        System.out.println("Now: " + now);
        
        
        // 특정 날짜 (Deprecated!)
        @SuppressWarnings("deprecation")
        Date date = new Date(2024 - 1900, 11, 25);  // 년은 1900 기준, 월은 0부터
        System.out.println("Date: " + date);
        
        
        // Epoch 밀리초
        long millis = System.currentTimeMillis();
        Date fromMillis = new Date(millis);
        
        
        // 문제점
        System.out.println("\n=== 문제점 ===");
        System.out.println("1. 가변 객체");
        Date mutableDate = new Date();
        System.out.println("Before: " + mutableDate);
        mutableDate.setTime(0);
        System.out.println("After: " + mutableDate);  // 변경됨!
        
        System.out.println("\n2. 이상한 API");
        @SuppressWarnings("deprecation")
        Date weirdDate = new Date(2024, 0, 1);  // 3924-01-01 로 생성!
        System.out.println("Weird: " + weirdDate);
        
        System.out.println("\n3. 타입 안정성 부족");
        @SuppressWarnings("deprecation")
        Date invalid = new Date(2024 - 1900, 15, 1);  // 15월도 가능!
        System.out.println("Invalid: " + invalid);
    }
}
```

### 1.2 Calendar 클래스

```java
import java.util.Calendar;

public class CalendarReview {
    public static void main(String[] args) {
        // 생성
        Calendar cal = Calendar.getInstance();
        
        // 특정 날짜 설정
        cal.set(2024, Calendar.DECEMBER, 25);
        
        // 읽기
        int year = cal.get(Calendar.YEAR);
        int month = cal.get(Calendar.MONTH);  // 0부터 시작!
        int day = cal.get(Calendar.DAY_OF_MONTH);
        
        System.out.println("Year: " + year);
        System.out.println("Month: " + month);  // 11 (12월인데!)
        System.out.println("Day: " + day);
        
        
        // 수정 (가변!)
        cal.add(Calendar.DAY_OF_MONTH, 7);
        System.out.println("After: " + cal.getTime());
        
        
        // 문제점
        System.out.println("\n=== 문제점 ===");
        System.out.println("1. 여전히 가변");
        System.out.println("2. 상수 사용 불편 (Calendar.DECEMBER 등)");
        System.out.println("3. 요일이 1(일요일)부터 시작");
        System.out.println("4. 직관적이지 않은 API");
    }
}
```

### 1.3 SimpleDateFormat 클래스

```java
import java.text.SimpleDateFormat;
import java.util.Date;

public class SimpleDateFormatReview {
    public static void main(String[] args) throws Exception {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        
        // 포맷팅
        String formatted = sdf.format(new Date());
        System.out.println("Formatted: " + formatted);
        
        
        // 파싱
        Date parsed = sdf.parse("2024-12-25 14:30:00");
        System.out.println("Parsed: " + parsed);
        
        
        // 문제점
        System.out.println("\n=== 문제점 ===");
        System.out.println("1. 스레드 안전하지 않음!");
        System.out.println("2. 예외 처리 필요 (ParseException)");
        System.out.println("3. 타입 안정성 없음");
        
        
        // 스레드 안전하지 않은 예시
        final SimpleDateFormat formatter = new SimpleDateFormat("yyyy-MM-dd");
        
        // 여러 스레드에서 동시 사용 시 문제 발생 가능
        Thread t1 = new Thread(() -> {
            try {
                for (int i = 0; i < 100; i++) {
                    Date d = formatter.parse("2024-12-25");
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        });
        
        Thread t2 = new Thread(() -> {
            try {
                for (int i = 0; i < 100; i++) {
                    Date d = formatter.parse("2024-01-01");
                }
            } catch (Exception e) {
                e.printStackTrace();
            }
        });
        
        t1.start();
        t2.start();
    }
}
```

---

## 2. 신규 API로 변환

### 2.1 Date → LocalDateTime

```java
public class DateToLocalDateTime {
    public static void main(String[] args) {
        // 레거시: Date
        Date date = new Date();
        System.out.println("Date: " + date);
        
        
        // 신규: LocalDateTime
        LocalDateTime localDateTime = LocalDateTime.now();
        System.out.println("LocalDateTime: " + localDateTime);
        
        
        // 비교
        System.out.println("\n=== 비교 ===");
        
        // Date - 가변
        Date mutableDate = new Date();
        mutableDate.setTime(0);  // 변경 가능!
        
        // LocalDateTime - 불변
        LocalDateTime immutableDateTime = LocalDateTime.now();
        LocalDateTime changed = immutableDateTime.plusDays(1);  // 새 객체 반환
        System.out.println("Original: " + immutableDateTime);
        System.out.println("Changed: " + changed);
        
        
        // Date - 직접 날짜/시간 필드 없음
        // date.getYear();  // Deprecated
        
        // LocalDateTime - 직접 접근 가능
        int year = localDateTime.getYear();
        int month = localDateTime.getMonthValue();
        int day = localDateTime.getDayOfMonth();
        
        System.out.println("Year: " + year);
        System.out.println("Month: " + month);  // 1~12 (정상!)
    }
}
```

### 2.2 Calendar → LocalDate/LocalDateTime

```java
public class CalendarToLocal {
    public static void main(String[] args) {
        // 레거시: Calendar
        Calendar cal = Calendar.getInstance();
        cal.set(2024, Calendar.DECEMBER, 25, 14, 30, 0);
        
        System.out.println("Calendar: " + cal.getTime());
        
        
        // 신규: LocalDate
        LocalDate date = LocalDate.of(2024, 12, 25);  // 월이 1~12!
        System.out.println("LocalDate: " + date);
        
        
        // 신규: LocalDateTime
        LocalDateTime dateTime = LocalDateTime.of(2024, 12, 25, 14, 30, 0);
        System.out.println("LocalDateTime: " + dateTime);
        
        
        // 비교
        System.out.println("\n=== 연산 비교 ===");
        
        // Calendar - 복잡
        Calendar cal2 = Calendar.getInstance();
        cal2.set(2024, Calendar.DECEMBER, 25);
        cal2.add(Calendar.DAY_OF_MONTH, 7);
        System.out.println("Calendar +7 days: " + cal2.getTime());
        
        // LocalDate - 간단
        LocalDate date2 = LocalDate.of(2024, 12, 25);
        LocalDate after7Days = date2.plusDays(7);
        System.out.println("LocalDate +7 days: " + after7Days);
    }
}
```

### 2.3 SimpleDateFormat → DateTimeFormatter

```java
public class FormatComparison {
    public static void main(String[] args) throws Exception {
        // 레거시: SimpleDateFormat
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        
        Date date = new Date();
        String formatted1 = sdf.format(date);
        Date parsed1 = sdf.parse("2024-12-25 14:30:00");
        
        System.out.println("SimpleDateFormat: " + formatted1);
        
        
        // 신규: DateTimeFormatter
        DateTimeFormatter dtf = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        
        LocalDateTime dateTime = LocalDateTime.now();
        String formatted2 = dateTime.format(dtf);
        LocalDateTime parsed2 = LocalDateTime.parse("2024-12-25 14:30:00", dtf);
        
        System.out.println("DateTimeFormatter: " + formatted2);
        
        
        // 비교
        System.out.println("\n=== 장점 ===");
        System.out.println("1. 스레드 안전 (불변)");
        System.out.println("2. 예외 처리 불필요 (런타임 예외)");
        System.out.println("3. 타입 안정성");
        System.out.println("4. 메서드 체이닝");
        
        
        // 스레드 안전성 테스트
        final DateTimeFormatter threadSafeFormatter = 
            DateTimeFormatter.ofPattern("yyyy-MM-dd");
        
        // 여러 스레드에서 안전하게 사용 가능
        Runnable task = () -> {
            for (int i = 0; i < 100; i++) {
                LocalDate d = LocalDate.parse("2024-12-25", threadSafeFormatter);
            }
        };
        
        Thread t1 = new Thread(task);
        Thread t2 = new Thread(task);
        
        t1.start();
        t2.start();
        
        t1.join();
        t2.join();
        
        System.out.println("Thread-safe execution completed!");
    }
}
```

---

## 3. 상호 변환 방법

### 3.1 Date ↔ LocalDateTime

```java
public class DateConversion {
    public static void main(String[] args) {
        // Date → LocalDateTime
        Date date = new Date();
        
        Instant instant = date.toInstant();
        LocalDateTime localDateTime = instant.atZone(ZoneId.systemDefault())
                                            .toLocalDateTime();
        
        System.out.println("Date: " + date);
        System.out.println("LocalDateTime: " + localDateTime);
        
        
        // LocalDateTime → Date
        LocalDateTime ldt = LocalDateTime.now();
        
        Instant instant2 = ldt.atZone(ZoneId.systemDefault()).toInstant();
        Date date2 = Date.from(instant2);
        
        System.out.println("\nLocalDateTime: " + ldt);
        System.out.println("Date: " + date2);
        
        
        // 간단한 헬퍼 메서드
        Date fromLocalDateTime = convertToDate(LocalDateTime.now());
        LocalDateTime fromDate = convertToLocalDateTime(new Date());
        
        System.out.println("\nConverted Date: " + fromLocalDateTime);
        System.out.println("Converted LocalDateTime: " + fromDate);
    }
    
    // LocalDateTime → Date
    public static Date convertToDate(LocalDateTime localDateTime) {
        return Date.from(localDateTime.atZone(ZoneId.systemDefault()).toInstant());
    }
    
    // Date → LocalDateTime
    public static LocalDateTime convertToLocalDateTime(Date date) {
        return date.toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime();
    }
}
```

### 3.2 Calendar ↔ LocalDateTime

```java
public class CalendarConversion {
    public static void main(String[] args) {
        // Calendar → LocalDateTime
        Calendar cal = Calendar.getInstance();
        cal.set(2024, Calendar.DECEMBER, 25, 14, 30, 0);
        
        Instant instant = cal.toInstant();
        LocalDateTime ldt = instant.atZone(ZoneId.systemDefault())
                                  .toLocalDateTime();
        
        System.out.println("Calendar: " + cal.getTime());
        System.out.println("LocalDateTime: " + ldt);
        
        
        // LocalDateTime → Calendar
        LocalDateTime localDateTime = LocalDateTime.of(2024, 12, 25, 14, 30);
        
        ZonedDateTime zdt = localDateTime.atZone(ZoneId.systemDefault());
        Calendar calendar = GregorianCalendar.from(zdt);
        
        System.out.println("\nLocalDateTime: " + localDateTime);
        System.out.println("Calendar: " + calendar.getTime());
    }
}
```

### 3.3 TimeZone ↔ ZoneId

```java
public class TimeZoneConversion {
    public static void main(String[] args) {
        // TimeZone → ZoneId
        TimeZone timeZone = TimeZone.getTimeZone("Asia/Seoul");
        ZoneId zoneId = timeZone.toZoneId();
        
        System.out.println("TimeZone: " + timeZone.getID());
        System.out.println("ZoneId: " + zoneId);
        
        
        // ZoneId → TimeZone
        ZoneId zoneId2 = ZoneId.of("America/New_York");
        TimeZone timeZone2 = TimeZone.getTimeZone(zoneId2);
        
        System.out.println("\nZoneId: " + zoneId2);
        System.out.println("TimeZone: " + timeZone2.getID());
    }
}
```

---

## 4. 마이그레이션 패턴

### 4.1 기본 마이그레이션

```java
public class BasicMigration {
    // BEFORE: 레거시 코드
    public static class LegacyCode {
        public String formatDate(Date date) {
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
            return sdf.format(date);
        }
        
        public Date parseDate(String dateStr) throws Exception {
            SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
            return sdf.parse(dateStr);
        }
        
        public Date addDays(Date date, int days) {
            Calendar cal = Calendar.getInstance();
            cal.setTime(date);
            cal.add(Calendar.DAY_OF_MONTH, days);
            return cal.getTime();
        }
    }
    
    // AFTER: 신규 코드
    public static class ModernCode {
        public String formatDate(LocalDate date) {
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
            return date.format(formatter);
        }
        
        public LocalDate parseDate(String dateStr) {
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
            return LocalDate.parse(dateStr, formatter);
        }
        
        public LocalDate addDays(LocalDate date, int days) {
            return date.plusDays(days);
        }
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== Legacy Code ===");
        LegacyCode legacy = new LegacyCode();
        Date date = new Date();
        System.out.println("Formatted: " + legacy.formatDate(date));
        System.out.println("Added 7 days: " + legacy.addDays(date, 7));
        
        System.out.println("\n=== Modern Code ===");
        ModernCode modern = new ModernCode();
        LocalDate localDate = LocalDate.now();
        System.out.println("Formatted: " + modern.formatDate(localDate));
        System.out.println("Added 7 days: " + modern.addDays(localDate, 7));
    }
}
```

### 4.2 점진적 마이그레이션

```java
public class GradualMigration {
    // 1단계: 레거시 API 유지하면서 내부는 신규 API 사용
    public static class HybridCode {
        // 레거시 인터페이스 유지
        public Date addDays(Date date, int days) {
            // 내부는 신규 API 사용
            LocalDateTime ldt = date.toInstant()
                                   .atZone(ZoneId.systemDefault())
                                   .toLocalDateTime();
            
            LocalDateTime result = ldt.plusDays(days);
            
            return Date.from(result.atZone(ZoneId.systemDefault()).toInstant());
        }
        
        public String formatDate(Date date, String pattern) {
            LocalDateTime ldt = date.toInstant()
                                   .atZone(ZoneId.systemDefault())
                                   .toLocalDateTime();
            
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern(pattern);
            return ldt.format(formatter);
        }
    }
    
    // 2단계: 새 메서드 추가 (오버로딩)
    public static class TransitionCode {
        // 레거시 메서드 (Deprecated)
        @Deprecated
        public Date addDays(Date date, int days) {
            return Date.from(addDays(convertToLocalDate(date), days)
                           .atStartOfDay(ZoneId.systemDefault())
                           .toInstant());
        }
        
        // 신규 메서드
        public LocalDate addDays(LocalDate date, int days) {
            return date.plusDays(days);
        }
        
        private LocalDate convertToLocalDate(Date date) {
            return date.toInstant()
                      .atZone(ZoneId.systemDefault())
                      .toLocalDate();
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Hybrid Code (1단계) ===");
        HybridCode hybrid = new HybridCode();
        Date date = new Date();
        Date result = hybrid.addDays(date, 7);
        System.out.println("Result: " + result);
        
        System.out.println("\n=== Transition Code (2단계) ===");
        TransitionCode transition = new TransitionCode();
        
        // 레거시 사용 (경고 발생)
        @SuppressWarnings("deprecation")
        Date legacyResult = transition.addDays(new Date(), 7);
        
        // 신규 사용 (권장)
        LocalDate modernResult = transition.addDays(LocalDate.now(), 7);
        
        System.out.println("Legacy: " + legacyResult);
        System.out.println("Modern: " + modernResult);
    }
}
```

### 4.3 외부 API 대응

```java
public class ExternalAPIAdaptor {
    // 외부 API가 Date를 요구하는 경우
    public static class ThirdPartyAPI {
        public void saveEvent(Date eventDate) {
            System.out.println("Saving event: " + eventDate);
        }
    }
    
    // 내부는 신규 API 사용
    public static class EventService {
        private ThirdPartyAPI thirdPartyAPI = new ThirdPartyAPI();
        
        public void createEvent(LocalDateTime eventDateTime) {
            // 내부에서 변환
            Date date = Date.from(eventDateTime
                               .atZone(ZoneId.systemDefault())
                               .toInstant());
            
            thirdPartyAPI.saveEvent(date);
        }
    }
    
    // 어댑터 패턴
    public static class DateAdapter {
        public static Date toDate(LocalDateTime localDateTime) {
            if (localDateTime == null) return null;
            return Date.from(localDateTime
                          .atZone(ZoneId.systemDefault())
                          .toInstant());
        }
        
        public static LocalDateTime toLocalDateTime(Date date) {
            if (date == null) return null;
            return date.toInstant()
                      .atZone(ZoneId.systemDefault())
                      .toLocalDateTime();
        }
    }
    
    public static void main(String[] args) {
        EventService service = new EventService();
        
        // 내부는 신규 API 사용
        LocalDateTime eventTime = LocalDateTime.of(2024, 12, 25, 14, 30);
        service.createEvent(eventTime);
        
        
        // 어댑터 활용
        LocalDateTime ldt = LocalDateTime.now();
        Date date = DateAdapter.toDate(ldt);
        LocalDateTime converted = DateAdapter.toLocalDateTime(date);
        
        System.out.println("Original: " + ldt);
        System.out.println("Converted: " + converted);
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 레거시 코드 리팩토링

```java
// 레거시 코드를 신규 API로 리팩토링
public class Problem1 {
    // BEFORE
    public static class LegacyBirthdayService {
        public int calculateAge(Date birthDate) {
            Calendar birth = Calendar.getInstance();
            birth.setTime(birthDate);
            
            Calendar today = Calendar.getInstance();
            
            int age = today.get(Calendar.YEAR) - birth.get(Calendar.YEAR);
            
            if (today.get(Calendar.MONTH) < birth.get(Calendar.MONTH)) {
                age--;
            } else if (today.get(Calendar.MONTH) == birth.get(Calendar.MONTH) &&
                      today.get(Calendar.DAY_OF_MONTH) < birth.get(Calendar.DAY_OF_MONTH)) {
                age--;
            }
            
            return age;
        }
    }
    
    // AFTER - 여기에 코드 작성
    public static class ModernBirthdayService {
        public int calculateAge(LocalDate birthDate) {
            // 여기에 코드 작성
            return 0;
        }
    }
    
    public static void main(String[] args) {
        // 테스트
        LegacyBirthdayService legacy = new LegacyBirthdayService();
        @SuppressWarnings("deprecation")
        Date birthDate = new Date(90, 4, 15);  // 1990-05-15
        System.out.println("Legacy age: " + legacy.calculateAge(birthDate));
        
        ModernBirthdayService modern = new ModernBirthdayService();
        LocalDate modernBirthDate = LocalDate.of(1990, 5, 15);
        System.out.println("Modern age: " + modern.calculateAge(modernBirthDate));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static class ModernBirthdayService {
    public int calculateAge(LocalDate birthDate) {
        LocalDate today = LocalDate.now();
        return Period.between(birthDate, today).getYears();
    }
}
```
</details>

---

### 문제 2: 날짜 포맷터 마이그레이션

```java
// SimpleDateFormat을 DateTimeFormatter로 변경
public class Problem2 {
    // BEFORE
    public static class LegacyFormatter {
        private SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        
        public String format(Date date) {
            return sdf.format(date);
        }
        
        public Date parse(String dateStr) throws ParseException {
            return sdf.parse(dateStr);
        }
    }
    
    // AFTER - 여기에 코드 작성
    public static class ModernFormatter {
        // 여기에 코드 작성
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static class ModernFormatter {
    private DateTimeFormatter formatter = 
        DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
    
    public String format(LocalDateTime dateTime) {
        return dateTime.format(formatter);
    }
    
    public LocalDateTime parse(String dateStr) {
        return LocalDateTime.parse(dateStr, formatter);
    }
}
```
</details>

---

### 문제 3: 타임존 변환 마이그레이션

```java
// Calendar/TimeZone을 ZonedDateTime으로 변경
public class Problem3 {
    // BEFORE
    public static Date convertTimezone(Date date, String fromZone, String toZone) {
        Calendar cal = Calendar.getInstance(TimeZone.getTimeZone(fromZone));
        cal.setTime(date);
        
        cal.setTimeZone(TimeZone.getTimeZone(toZone));
        return cal.getTime();
    }
    
    // AFTER - 여기에 코드 작성
    public static ZonedDateTime convertTimezoneModern(
        ZonedDateTime dateTime, String toZone) {
        // 여기에 코드 작성
        return null;
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static ZonedDateTime convertTimezoneModern(
    ZonedDateTime dateTime, String toZone) {
    
    return dateTime.withZoneSameInstant(ZoneId.of(toZone));
}

// 사용 예시
public static void main(String[] args) {
    ZonedDateTime seoul = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));
    ZonedDateTime newYork = convertTimezoneModern(seoul, "America/New_York");
    
    System.out.println("Seoul: " + seoul);
    System.out.println("New York: " + newYork);
}
```
</details>

---

## 📌 핵심 정리

### 레거시 문제점
```
Date/Calendar:
- 가변 객체 (스레드 불안전)
- 혼란스러운 API (월이 0부터 시작 등)
- 타입 안정성 부족

SimpleDateFormat:
- 스레드 불안전
- 예외 처리 필수
- 성능 이슈
```

### 신규 API 장점
```
LocalDate/LocalTime/LocalDateTime:
- 불변 객체 (스레드 안전)
- 직관적 API
- 타입 안정성

DateTimeFormatter:
- 스레드 안전
- 메서드 체이닝
- 더 나은 성능
```

### 변환 방법
```java
// Date → LocalDateTime
LocalDateTime ldt = date.toInstant()
                       .atZone(ZoneId.systemDefault())
                       .toLocalDateTime();

// LocalDateTime → Date
Date date = Date.from(ldt.atZone(ZoneId.systemDefault()).toInstant());

// Calendar → LocalDateTime
LocalDateTime ldt = cal.toInstant()
                      .atZone(ZoneId.systemDefault())
                      .toLocalDateTime();

// LocalDateTime → Calendar
Calendar cal = GregorianCalendar.from(
    ldt.atZone(ZoneId.systemDefault()));
```

### 마이그레이션 전략
```
1. 내부부터 점진적 변경
2. 레거시 인터페이스 유지하면서 내부 구현 변경
3. 새 메서드 추가 (오버로딩)
4. 기존 메서드 Deprecated
5. 외부 API는 어댑터 패턴 사용
```

---

## 🎉 Date & Time 시리즈 완료!

<div align="center">

### 📚 전체 시리즈

| Chapter | 주제 |
|:-------:|------|
| [01. Time API 개요](./DateTime-01-개요.md) | 레거시 문제점, 신규 API 소개 |
| [02. Local 클래스](./DateTime-02-Local.md) | LocalDate, LocalTime, LocalDateTime |
| [03. Zoned & Instant](./DateTime-03-Zoned.md) | ZonedDateTime, Instant, 시간대 |
| [04. Period & Duration](./DateTime-04-Period.md) | 날짜 기간, 시간 기간, ChronoUnit |
| [05. DateTimeFormatter](./DateTime-05-Formatter.md) | 포맷팅, 파싱, 로케일 |
| [06. 레거시 vs 신규](./DateTime-06-Legacy.md) ⭐ | 마이그레이션 가이드 |

<br/>

**다음 학습 추천**

💡 [Collections Framework 바로가기](../collections/) *(예정)*  
💡 [IO & 입출력 바로가기](../io/) *(예정)*  
💡 [Arrays 시리즈 바로가기](../arrays/)

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 05. DateTimeFormatter](./DateTime-05-Formatter.md)**

</div>
