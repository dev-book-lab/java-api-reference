# DateTime 03. ZonedDateTime & Instant

> 시간대와 UTC 타임스탬프 - 글로벌 시간 처리  
> Java API Reference

---

## 📑 목차

1. [ZoneId - 시간대](#1-zoneid---시간대)
2. [ZonedDateTime - 시간대 포함 날짜/시간](#2-zoneddatetime---시간대-포함-날짜시간)
3. [Instant - UTC 타임스탬프](#3-instant---utc-타임스탬프)
4. [시간대 변환과 응용](#4-시간대-변환과-응용)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. ZoneId - 시간대

### 1.1 시간대 기본

```java
import java.time.ZoneId;
import java.util.Set;

public class ZoneIdBasic {
    public static void main(String[] args) {
        // 시스템 기본 시간대
        ZoneId systemDefault = ZoneId.systemDefault();
        System.out.println("System default: " + systemDefault);
        // Asia/Seoul
        
        
        // 특정 시간대
        ZoneId seoul = ZoneId.of("Asia/Seoul");
        ZoneId tokyo = ZoneId.of("Asia/Tokyo");
        ZoneId newYork = ZoneId.of("America/New_York");
        ZoneId utc = ZoneId.of("UTC");
        
        System.out.println("Seoul: " + seoul);
        System.out.println("Tokyo: " + tokyo);
        System.out.println("New York: " + newYork);
        
        
        // 짧은 ID (권장하지 않음)
        ZoneId pst = ZoneId.of("America/Los_Angeles");  // PST/PDT
        
        
        // 사용 가능한 모든 시간대
        Set<String> availableZones = ZoneId.getAvailableZoneIds();
        System.out.println("Total zones: " + availableZones.size());
        
        // 일부 출력
        availableZones.stream()
                     .filter(z -> z.startsWith("Asia/"))
                     .sorted()
                     .limit(10)
                     .forEach(System.out::println);
    }
}
```

### 1.2 ZoneOffset

```java
import java.time.ZoneOffset;

public class ZoneOffsetDemo {
    public static void main(String[] args) {
        // UTC 오프셋 생성
        ZoneOffset seoulOffset = ZoneOffset.of("+09:00");
        ZoneOffset tokyoOffset = ZoneOffset.ofHours(9);
        ZoneOffset nyOffset = ZoneOffset.ofHours(-5);
        
        System.out.println("Seoul offset: " + seoulOffset);   // +09:00
        System.out.println("Tokyo offset: " + tokyoOffset);   // +09:00
        System.out.println("NY offset: " + nyOffset);         // -05:00
        
        
        // UTC
        ZoneOffset utc = ZoneOffset.UTC;
        System.out.println("UTC: " + utc);  // Z
        
        
        // 초 단위
        ZoneOffset offset = ZoneOffset.ofTotalSeconds(32400);  // 9 * 3600
        System.out.println("From seconds: " + offset);  // +09:00
        
        
        // 오프셋 값 읽기
        int totalSeconds = seoulOffset.getTotalSeconds();
        System.out.println("Total seconds: " + totalSeconds);  // 32400
    }
}
```

### 1.3 시간대 규칙

```java
import java.time.zone.ZoneRules;

public class ZoneRulesDemo {
    public static void main(String[] args) {
        ZoneId seoul = ZoneId.of("Asia/Seoul");
        ZoneRules rules = seoul.getRules();
        
        // DST (서머타임) 여부
        boolean isDst = rules.isDaylightSavings(Instant.now());
        System.out.println("Is DST: " + isDst);  // false (한국은 서머타임 없음)
        
        
        // 현재 오프셋
        ZoneOffset offset = rules.getOffset(Instant.now());
        System.out.println("Current offset: " + offset);  // +09:00
        
        
        // 뉴욕 (서머타임 있음)
        ZoneId newYork = ZoneId.of("America/New_York");
        ZoneRules nyRules = newYork.getRules();
        
        boolean nyDst = nyRules.isDaylightSavings(Instant.now());
        System.out.println("NY DST: " + nyDst);
    }
}
```

---

## 2. ZonedDateTime - 시간대 포함 날짜/시간

### 2.1 생성

```java
import java.time.ZonedDateTime;

public class ZonedDateTimeCreation {
    public static void main(String[] args) {
        // 현재 시간 (시스템 기본 시간대)
        ZonedDateTime now = ZonedDateTime.now();
        System.out.println("Now: " + now);
        
        
        // 특정 시간대로
        ZonedDateTime seoulNow = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));
        ZonedDateTime tokyoNow = ZonedDateTime.now(ZoneId.of("Asia/Tokyo"));
        ZonedDateTime nyNow = ZonedDateTime.now(ZoneId.of("America/New_York"));
        
        System.out.println("Seoul: " + seoulNow);
        System.out.println("Tokyo: " + tokyoNow);
        System.out.println("NY: " + nyNow);
        
        
        // 특정 날짜/시간 + 시간대
        ZonedDateTime specific = ZonedDateTime.of(
            2024, 12, 25, 14, 30, 0, 0,
            ZoneId.of("Asia/Seoul")
        );
        
        
        // LocalDateTime + 시간대
        LocalDateTime ldt = LocalDateTime.of(2024, 12, 25, 14, 30);
        ZonedDateTime zdt = ldt.atZone(ZoneId.of("Asia/Seoul"));
        
        System.out.println("From LocalDateTime: " + zdt);
        
        
        // 문자열 파싱
        ZonedDateTime parsed = ZonedDateTime.parse("2024-12-25T14:30:00+09:00[Asia/Seoul]");
        System.out.println("Parsed: " + parsed);
    }
}
```

### 2.2 읽기

```java
public class ZonedDateTimeGetter {
    public static void main(String[] args) {
        ZonedDateTime zdt = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));
        
        // 날짜/시간 필드 (LocalDateTime과 동일)
        int year = zdt.getYear();
        int month = zdt.getMonthValue();
        int day = zdt.getDayOfMonth();
        int hour = zdt.getHour();
        int minute = zdt.getMinute();
        
        System.out.println("Year: " + year);
        System.out.println("Hour: " + hour);
        
        
        // 시간대 정보
        ZoneId zone = zdt.getZone();
        ZoneOffset offset = zdt.getOffset();
        
        System.out.println("Zone: " + zone);        // Asia/Seoul
        System.out.println("Offset: " + offset);    // +09:00
        
        
        // LocalDateTime 추출
        LocalDateTime ldt = zdt.toLocalDateTime();
        LocalDate ld = zdt.toLocalDate();
        LocalTime lt = zdt.toLocalTime();
        
        System.out.println("LocalDateTime: " + ldt);
    }
}
```

### 2.3 시간대 변환

```java
public class TimeZoneConversion {
    public static void main(String[] args) {
        // 서울 시간
        ZonedDateTime seoul = ZonedDateTime.of(
            2024, 12, 25, 14, 30, 0, 0,
            ZoneId.of("Asia/Seoul")
        );
        
        System.out.println("Seoul: " + seoul);
        
        
        // 같은 시점을 다른 시간대로 (시간 변환됨)
        ZonedDateTime tokyo = seoul.withZoneSameInstant(ZoneId.of("Asia/Tokyo"));
        ZonedDateTime newYork = seoul.withZoneSameInstant(ZoneId.of("America/New_York"));
        ZonedDateTime utc = seoul.withZoneSameInstant(ZoneId.of("UTC"));
        
        System.out.println("Tokyo: " + tokyo);      // 14:30 → 14:30 (같은 UTC+9)
        System.out.println("New York: " + newYork); // 14:30 → 00:30 (UTC-5)
        System.out.println("UTC: " + utc);          // 14:30 → 05:30
        
        
        // 시간대만 변경 (시간은 그대로)
        ZonedDateTime sameLocal = seoul.withZoneSameLocal(ZoneId.of("America/New_York"));
        System.out.println("Same local: " + sameLocal);  // 14:30 그대로 (시간대만 NY)
    }
}
```

### 2.4 수정

```java
public class ZonedDateTimeModification {
    public static void main(String[] args) {
        ZonedDateTime zdt = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));
        
        // 날짜/시간 수정 (시간대 유지)
        ZonedDateTime tomorrow = zdt.plusDays(1);
        ZonedDateTime nextHour = zdt.plusHours(1);
        ZonedDateTime withYear = zdt.withYear(2025);
        
        System.out.println("Original: " + zdt);
        System.out.println("Tomorrow: " + tomorrow);
        
        
        // 시간대 변경
        ZonedDateTime toTokyo = zdt.withZoneSameInstant(ZoneId.of("Asia/Tokyo"));
        
        
        // 오프셋 직접 설정 (권장하지 않음)
        ZonedDateTime withOffset = zdt.withFixedOffsetZone();
    }
}
```

---

## 3. Instant - UTC 타임스탬프

### 3.1 생성

```java
import java.time.Instant;

public class InstantCreation {
    public static void main(String[] args) {
        // 현재 UTC 시간
        Instant now = Instant.now();
        System.out.println("Now: " + now);
        // 2024-12-16T05:30:45.123456789Z
        
        
        // Epoch 기준 (1970-01-01 00:00:00 UTC)
        Instant epoch = Instant.ofEpochSecond(0);
        System.out.println("Epoch: " + epoch);  // 1970-01-01T00:00:00Z
        
        
        // 초 단위
        Instant fromSeconds = Instant.ofEpochSecond(1703505045);
        System.out.println("From seconds: " + fromSeconds);
        
        
        // 밀리초 단위
        long millis = System.currentTimeMillis();
        Instant fromMillis = Instant.ofEpochMilli(millis);
        System.out.println("From millis: " + fromMillis);
        
        
        // 초 + 나노초
        Instant withNano = Instant.ofEpochSecond(0, 123456789);
        
        
        // 문자열 파싱
        Instant parsed = Instant.parse("2024-12-25T05:30:00Z");
        System.out.println("Parsed: " + parsed);
    }
}
```

### 3.2 읽기

```java
public class InstantGetter {
    public static void main(String[] args) {
        Instant instant = Instant.now();
        
        // Epoch 기준 값
        long epochSecond = instant.getEpochSecond();
        int nano = instant.getNano();
        long epochMilli = instant.toEpochMilli();
        
        System.out.println("Epoch second: " + epochSecond);
        System.out.println("Nano: " + nano);
        System.out.println("Epoch milli: " + epochMilli);
        
        
        // 날짜/시간 필드는 없음!
        // instant.getYear();  // 컴파일 에러!
        
        // ZonedDateTime으로 변환해야 함
        ZonedDateTime zdt = instant.atZone(ZoneId.of("Asia/Seoul"));
        int year = zdt.getYear();
        int hour = zdt.getHour();
        
        System.out.println("Year: " + year);
        System.out.println("Hour: " + hour);
    }
}
```

### 3.3 수정

```java
public class InstantModification {
    public static void main(String[] args) {
        Instant instant = Instant.now();
        
        // 초 단위 연산
        Instant plus10Seconds = instant.plusSeconds(10);
        Instant plus1Hour = instant.plusSeconds(3600);
        Instant minus1Minute = instant.minusSeconds(60);
        
        System.out.println("Original: " + instant);
        System.out.println("Plus 10s: " + plus10Seconds);
        
        
        // 밀리초 단위
        Instant plusMillis = instant.plusMillis(1000);  // 1초
        
        
        // 나노초 단위
        Instant plusNanos = instant.plusNanos(1_000_000_000);  // 1초
        
        
        // Duration 사용
        Duration duration = Duration.ofHours(1);
        Instant plusDuration = instant.plus(duration);
        
        
        // 날짜 단위 연산 불가!
        // instant.plusDays(1);  // 컴파일 에러!
        
        // ZonedDateTime으로 변환 필요
        ZonedDateTime zdt = instant.atZone(ZoneId.of("Asia/Seoul"));
        ZonedDateTime nextDay = zdt.plusDays(1);
        Instant nextDayInstant = nextDay.toInstant();
    }
}
```

### 3.4 비교

```java
public class InstantComparison {
    public static void main(String[] args) {
        Instant now = Instant.now();
        Instant later = now.plusSeconds(60);
        Instant earlier = now.minusSeconds(60);
        
        // isBefore, isAfter
        System.out.println(now.isBefore(later));   // true
        System.out.println(now.isAfter(earlier));  // true
        
        
        // compareTo
        int result = now.compareTo(later);
        System.out.println("compareTo: " + result);  // -1
        
        
        // equals
        Instant same = Instant.ofEpochMilli(now.toEpochMilli());
        System.out.println("equals: " + now.equals(same));  // true
        
        
        // 시간 차이
        Duration duration = Duration.between(earlier, now);
        System.out.println("Duration: " + duration.getSeconds() + "s");  // 60
    }
}
```

---

## 4. 시간대 변환과 응용

### 4.1 글로벌 회의 시간

```java
public class GlobalMeeting {
    public static void scheduleMeeting() {
        // 서울 시간으로 회의 예약
        ZonedDateTime seoulMeeting = ZonedDateTime.of(
            2024, 12, 25, 14, 0, 0, 0,
            ZoneId.of("Asia/Seoul")
        );
        
        System.out.println("=== 글로벌 회의 시간 ===");
        System.out.println("Seoul: " + seoulMeeting.format(
            DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm z")));
        
        // 각 지역 시간으로 변환
        String[] zones = {
            "Asia/Tokyo",
            "Asia/Singapore",
            "Europe/London",
            "America/New_York",
            "America/Los_Angeles"
        };
        
        for (String zone : zones) {
            ZonedDateTime localTime = seoulMeeting.withZoneSameInstant(
                ZoneId.of(zone));
            System.out.println(zone + ": " + localTime.format(
                DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm z")));
        }
    }
    
    public static void main(String[] args) {
        scheduleMeeting();
    }
}
```

### 4.2 타임스탬프 변환

```java
public class TimestampConversion {
    public static void main(String[] args) {
        // Instant ↔ ZonedDateTime
        Instant instant = Instant.now();
        
        // Instant → ZonedDateTime (시간대 추가)
        ZonedDateTime seoul = instant.atZone(ZoneId.of("Asia/Seoul"));
        ZonedDateTime newYork = instant.atZone(ZoneId.of("America/New_York"));
        
        System.out.println("Instant: " + instant);
        System.out.println("Seoul: " + seoul);
        System.out.println("New York: " + newYork);
        
        
        // ZonedDateTime → Instant (시간대 제거)
        Instant backToInstant = seoul.toInstant();
        System.out.println("Back to instant: " + backToInstant);
        
        
        // LocalDateTime → Instant (시간대 필요!)
        LocalDateTime ldt = LocalDateTime.of(2024, 12, 25, 14, 30);
        
        // 방법 1: ZonedDateTime 거쳐서
        Instant instant1 = ldt.atZone(ZoneId.of("Asia/Seoul")).toInstant();
        
        // 방법 2: ZoneOffset 사용
        Instant instant2 = ldt.toInstant(ZoneOffset.of("+09:00"));
        
        System.out.println("From LocalDateTime: " + instant1);
    }
}
```

### 4.3 서머타임 처리

```java
public class DaylightSavingTime {
    public static void main(String[] args) {
        // 미국 뉴욕 (서머타임 있음)
        ZoneId newYork = ZoneId.of("America/New_York");
        
        // 서머타임 시작 전 (3월)
        ZonedDateTime winter = ZonedDateTime.of(
            2024, 3, 9, 14, 0, 0, 0, newYork);
        System.out.println("Winter: " + winter);
        System.out.println("Offset: " + winter.getOffset());  // -05:00 (EST)
        
        
        // 서머타임 시작 후 (3월 중순)
        ZonedDateTime summer = ZonedDateTime.of(
            2024, 3, 11, 14, 0, 0, 0, newYork);
        System.out.println("Summer: " + summer);
        System.out.println("Offset: " + summer.getOffset());  // -04:00 (EDT)
        
        
        // 자동 변환
        ZonedDateTime winterPlus1Day = winter.plusDays(2);
        System.out.println("Winter + 2 days: " + winterPlus1Day);
        System.out.println("New offset: " + winterPlus1Day.getOffset());  // -04:00
        
        
        // 한국 (서머타임 없음)
        ZoneId seoul = ZoneId.of("Asia/Seoul");
        ZonedDateTime seoulTime = ZonedDateTime.now(seoul);
        System.out.println("Seoul offset: " + seoulTime.getOffset());  // +09:00 (항상)
    }
}
```

### 4.4 실전 예제: 출시 시간 조정

```java
public class ProductLaunch {
    public static void launchAtSameTime() {
        // 전 세계 동시 출시: 2024-12-25 00:00 (각 지역 자정)
        LocalDate launchDate = LocalDate.of(2024, 12, 25);
        LocalTime midnight = LocalTime.MIDNIGHT;
        
        String[] zones = {
            "Asia/Seoul",
            "Asia/Tokyo",
            "Europe/London",
            "America/New_York"
        };
        
        System.out.println("=== 각 지역 출시 시간 (자정) ===");
        for (String zone : zones) {
            ZonedDateTime launchTime = ZonedDateTime.of(
                launchDate, midnight, ZoneId.of(zone));
            
            // UTC로 변환하여 실제 시간 확인
            Instant instant = launchTime.toInstant();
            
            System.out.println(zone + ": " + launchTime + 
                             " (UTC: " + instant + ")");
        }
    }
    
    public static void launchAtSameInstant() {
        // 전 세계 동시 출시: UTC 기준 특정 시점
        Instant launchInstant = Instant.parse("2024-12-25T00:00:00Z");
        
        String[] zones = {
            "Asia/Seoul",
            "Asia/Tokyo",
            "Europe/London",
            "America/New_York"
        };
        
        System.out.println("\n=== 동일 시점, 각 지역 시간 ===");
        for (String zone : zones) {
            ZonedDateTime launchTime = launchInstant.atZone(ZoneId.of(zone));
            System.out.println(zone + ": " + launchTime);
        }
    }
    
    public static void main(String[] args) {
        launchAtSameTime();
        launchAtSameInstant();
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 비행 시간 계산

```java
// 출발지와 도착지 시간대가 다를 때 비행 시간 계산
public class Problem1 {
    public static Duration calculateFlightDuration(
        ZonedDateTime departure,
        ZonedDateTime arrival) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        // 서울 출발
        ZonedDateTime departure = ZonedDateTime.of(
            2024, 12, 25, 14, 0, 0, 0,
            ZoneId.of("Asia/Seoul"));
        
        // 뉴욕 도착
        ZonedDateTime arrival = ZonedDateTime.of(
            2024, 12, 25, 16, 0, 0, 0,
            ZoneId.of("America/New_York"));
        
        Duration flightTime = calculateFlightDuration(departure, arrival);
        System.out.println("Flight duration: " + flightTime.toHours() + " hours");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static Duration calculateFlightDuration(
        ZonedDateTime departure,
        ZonedDateTime arrival) {
        
        // Instant로 변환하여 실제 시간 차이 계산
        Instant departureInstant = departure.toInstant();
        Instant arrivalInstant = arrival.toInstant();
        
        return Duration.between(departureInstant, arrivalInstant);
    }
    
    // 더 자세한 버전
    public static String calculateFlightDurationDetailed(
        ZonedDateTime departure,
        ZonedDateTime arrival) {
        
        Duration duration = calculateFlightDuration(departure, arrival);
        
        long hours = duration.toHours();
        long minutes = duration.toMinutes() % 60;
        
        return String.format("%d시간 %d분", hours, minutes);
    }
}
```
</details>

---

### 문제 2: 시간대 변환기

```java
// 특정 시간을 여러 시간대로 변환
public class Problem2 {
    public static Map<String, ZonedDateTime> convertToMultipleZones(
        ZonedDateTime source,
        List<String> zoneIds) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        ZonedDateTime seoul = ZonedDateTime.of(
            2024, 12, 25, 14, 0, 0, 0,
            ZoneId.of("Asia/Seoul"));
        
        List<String> zones = Arrays.asList(
            "Asia/Tokyo",
            "Europe/London",
            "America/New_York"
        );
        
        Map<String, ZonedDateTime> converted = convertToMultipleZones(seoul, zones);
        converted.forEach((zone, time) -> 
            System.out.println(zone + ": " + time));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static Map<String, ZonedDateTime> convertToMultipleZones(
        ZonedDateTime source,
        List<String> zoneIds) {
        
        Map<String, ZonedDateTime> result = new LinkedHashMap<>();
        
        for (String zoneId : zoneIds) {
            ZonedDateTime converted = source.withZoneSameInstant(
                ZoneId.of(zoneId));
            result.put(zoneId, converted);
        }
        
        return result;
    }
    
    // Stream 버전
    public static Map<String, ZonedDateTime> convertToMultipleZonesStream(
        ZonedDateTime source,
        List<String> zoneIds) {
        
        return zoneIds.stream()
                     .collect(Collectors.toMap(
                         zoneId -> zoneId,
                         zoneId -> source.withZoneSameInstant(ZoneId.of(zoneId)),
                         (a, b) -> a,
                         LinkedHashMap::new
                     ));
    }
}
```
</details>

---

### 문제 3: 영업시간 확인

```java
// 현재 시간이 영업시간(9:00~18:00) 내인지 확인
// 시간대를 고려해야 함
public class Problem3 {
    public static boolean isBusinessHours(ZoneId businessZone) {
        // 여기에 코드 작성
        return false;
    }
    
    public static void main(String[] args) {
        ZoneId seoulOffice = ZoneId.of("Asia/Seoul");
        
        boolean isOpen = isBusinessHours(seoulOffice);
        System.out.println("Office is open: " + isOpen);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static boolean isBusinessHours(ZoneId businessZone) {
        // 해당 시간대의 현재 시간
        ZonedDateTime now = ZonedDateTime.now(businessZone);
        LocalTime currentTime = now.toLocalTime();
        
        // 영업시간
        LocalTime openTime = LocalTime.of(9, 0);
        LocalTime closeTime = LocalTime.of(18, 0);
        
        // 범위 확인
        return !currentTime.isBefore(openTime) && 
               currentTime.isBefore(closeTime);
    }
    
    // 요일까지 고려한 버전
    public static boolean isBusinessHoursWithWeekday(ZoneId businessZone) {
        ZonedDateTime now = ZonedDateTime.now(businessZone);
        
        // 주말 확인
        DayOfWeek day = now.getDayOfWeek();
        if (day == DayOfWeek.SATURDAY || day == DayOfWeek.SUNDAY) {
            return false;
        }
        
        // 시간 확인
        LocalTime currentTime = now.toLocalTime();
        LocalTime openTime = LocalTime.of(9, 0);
        LocalTime closeTime = LocalTime.of(18, 0);
        
        return !currentTime.isBefore(openTime) && 
               currentTime.isBefore(closeTime);
    }
}
```
</details>

---

## 📌 핵심 정리

### ZoneId & ZoneOffset
```java
// ZoneId (지역 기반)
ZoneId.of("Asia/Seoul")            // 권장
ZoneId.systemDefault()             // 시스템 기본

// ZoneOffset (UTC 오프셋)
ZoneOffset.of("+09:00")
ZoneOffset.ofHours(9)
ZoneOffset.UTC                     // Z
```

### ZonedDateTime
```java
// 생성
ZonedDateTime.now(ZoneId.of("Asia/Seoul"))
ZonedDateTime.of(ldt, ZoneId.of("Asia/Seoul"))
ldt.atZone(ZoneId.of("Asia/Seoul"))

// 시간대 변환
zdt.withZoneSameInstant(newZone)   // 시간 변환 O
zdt.withZoneSameLocal(newZone)     // 시간 변환 X

// Instant 변환
zdt.toInstant()
instant.atZone(ZoneId.of("Asia/Seoul"))
```

### Instant
```java
// 생성
Instant.now()                      // 현재 UTC
Instant.ofEpochSecond(seconds)
Instant.ofEpochMilli(millis)

// 읽기
instant.getEpochSecond()
instant.toEpochMilli()

// 수정 (초/밀리/나노초만 가능)
instant.plusSeconds(3600)
instant.plusMillis(1000)
instant.plusNanos(1_000_000_000)

// 날짜 연산은 ZonedDateTime으로 변환 필요
instant.atZone(zone).plusDays(1).toInstant()
```

### 시간대 변환 패턴
```java
// 같은 순간, 다른 표현
Instant instant = Instant.now();
ZonedDateTime seoul = instant.atZone(ZoneId.of("Asia/Seoul"));
ZonedDateTime tokyo = instant.atZone(ZoneId.of("Asia/Tokyo"));

// 시간대 변경
ZonedDateTime toTokyo = seoul.withZoneSameInstant(
    ZoneId.of("Asia/Tokyo"));
```

### 주의사항
```java
// ❌ Instant로 날짜 연산 불가
instant.plusDays(1);  // 컴파일 에러!

// ✅ ZonedDateTime 거쳐서
instant.atZone(zone).plusDays(1).toInstant()

// ❌ LocalDateTime → Instant (시간대 없음)
ldt.toInstant();  // 컴파일 에러!

// ✅ 시간대 지정 필요
ldt.atZone(zone).toInstant()
ldt.toInstant(offset)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. LocalDate, LocalTime, LocalDateTime](./DateTime-02-Local.md) | [다음: 04. Period & Duration →](./DateTime-04-Period.md)**

</div>
