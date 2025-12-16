# DateTime 04. Period & Duration

> 날짜 기간과 시간 기간 - 정확한 기간 계산  
> Java API Reference

---

## 📑 목차

1. [Period - 날짜 기반 기간](#1-period---날짜-기반-기간)
2. [Duration - 시간 기반 기간](#2-duration---시간-기반-기간)
3. [Period vs Duration 비교](#3-period-vs-duration-비교)
4. [ChronoUnit - 단위별 계산](#4-chronounit---단위별-계산)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. Period - 날짜 기반 기간

### 1.1 생성

```java
import java.time.Period;
import java.time.LocalDate;

public class PeriodCreation {
    public static void main(String[] args) {
        // 년, 월, 일 단위로 생성
        Period period1 = Period.ofYears(1);
        Period period2 = Period.ofMonths(6);
        Period period3 = Period.ofWeeks(4);
        Period period4 = Period.ofDays(10);
        
        System.out.println("1 year: " + period1);     // P1Y
        System.out.println("6 months: " + period2);   // P6M
        System.out.println("4 weeks: " + period3);    // P28D (일로 변환)
        System.out.println("10 days: " + period4);    // P10D
        
        
        // 조합
        Period period5 = Period.of(1, 6, 15);  // 1년 6개월 15일
        System.out.println("Combined: " + period5);  // P1Y6M15D
        
        
        // 두 날짜 사이 기간
        LocalDate start = LocalDate.of(2024, 1, 1);
        LocalDate end = LocalDate.of(2025, 6, 15);
        
        Period between = Period.between(start, end);
        System.out.println("Between: " + between);  // P1Y5M14D
        
        
        // 문자열 파싱 (ISO-8601 형식)
        Period parsed = Period.parse("P1Y2M3D");  // 1년 2개월 3일
        System.out.println("Parsed: " + parsed);
    }
}
```

### 1.2 읽기

```java
public class PeriodGetter {
    public static void main(String[] args) {
        Period period = Period.of(1, 6, 15);
        
        // 개별 필드
        int years = period.getYears();
        int months = period.getMonths();
        int days = period.getDays();
        
        System.out.println("Years: " + years);    // 1
        System.out.println("Months: " + months);  // 6
        System.out.println("Days: " + days);      // 15
        
        
        // 전체 월 수는 없음! (년과 월은 별도)
        // period.toTotalMonths();  // 이런 메서드 없음
        
        // 수동 계산
        long totalMonths = years * 12L + months;
        System.out.println("Total months: " + totalMonths);  // 18
        
        
        // 음수 여부
        boolean negative = period.isNegative();
        System.out.println("Is negative: " + negative);
        
        
        // 0 여부
        boolean zero = period.isZero();
        System.out.println("Is zero: " + zero);
    }
}
```

### 1.3 수정

```java
public class PeriodModification {
    public static void main(String[] args) {
        Period period = Period.of(1, 6, 15);
        
        // plus - 더하기
        Period plusYears = period.plusYears(1);
        Period plusMonths = period.plusMonths(3);
        Period plusDays = period.plusDays(10);
        
        System.out.println("Original: " + period);        // P1Y6M15D
        System.out.println("Plus 1 year: " + plusYears);  // P2Y6M15D
        System.out.println("Plus 3 months: " + plusMonths);  // P1Y9M15D
        
        
        // minus - 빼기
        Period minusMonths = period.minusMonths(2);
        System.out.println("Minus 2 months: " + minusMonths);  // P1Y4M15D
        
        
        // with - 설정
        Period withYears = period.withYears(5);
        Period withMonths = period.withMonths(0);
        Period withDays = period.withDays(1);
        
        System.out.println("With 5 years: " + withYears);  // P5Y6M15D
        
        
        // multipliedBy - 곱하기
        Period doubled = period.multipliedBy(2);
        System.out.println("Doubled: " + doubled);  // P2Y12M30D
        
        
        // negated - 부호 반전
        Period negated = period.negated();
        System.out.println("Negated: " + negated);  // P-1Y-6M-15D
        
        
        // normalized - 정규화 (월 → 년)
        Period period2 = Period.of(1, 14, 0);  // 1년 14개월
        Period normalized = period2.normalized();
        System.out.println("Before: " + period2);       // P1Y14M
        System.out.println("Normalized: " + normalized);  // P2Y2M
    }
}
```

### 1.4 날짜 연산에 활용

```java
public class PeriodDateCalculation {
    public static void main(String[] args) {
        LocalDate today = LocalDate.now();
        
        // Period를 사용한 날짜 연산
        Period period = Period.of(1, 6, 15);
        LocalDate future = today.plus(period);
        
        System.out.println("Today: " + today);
        System.out.println("Future: " + future);
        
        
        // 나이 계산
        LocalDate birthDate = LocalDate.of(1990, 5, 15);
        Period age = Period.between(birthDate, today);
        
        System.out.println("Age: " + age.getYears() + "년 " +
                         age.getMonths() + "개월 " +
                         age.getDays() + "일");
        
        
        // D-Day 계산
        LocalDate targetDate = LocalDate.of(2025, 1, 1);
        Period until = Period.between(today, targetDate);
        
        System.out.println("Until: " + until.getMonths() + "개월 " +
                         until.getDays() + "일");
        
        
        // 생일까지
        LocalDate nextBirthday = birthDate.withYear(today.getYear());
        if (nextBirthday.isBefore(today) || nextBirthday.isEqual(today)) {
            nextBirthday = nextBirthday.plusYears(1);
        }
        
        Period untilBirthday = Period.between(today, nextBirthday);
        System.out.println("Until birthday: " + untilBirthday.getMonths() + "개월 " +
                         untilBirthday.getDays() + "일");
    }
}
```

---

## 2. Duration - 시간 기반 기간

### 2.1 생성

```java
import java.time.Duration;
import java.time.LocalTime;
import java.time.LocalDateTime;

public class DurationCreation {
    public static void main(String[] args) {
        // 시간 단위로 생성
        Duration duration1 = Duration.ofDays(1);
        Duration duration2 = Duration.ofHours(24);
        Duration duration3 = Duration.ofMinutes(90);
        Duration duration4 = Duration.ofSeconds(3600);
        Duration duration5 = Duration.ofMillis(1000);
        Duration duration6 = Duration.ofNanos(1_000_000_000);
        
        System.out.println("1 day: " + duration1);      // PT24H
        System.out.println("24 hours: " + duration2);   // PT24H
        System.out.println("90 minutes: " + duration3); // PT1H30M
        System.out.println("3600 seconds: " + duration4);  // PT1H
        
        
        // 조합
        Duration duration7 = Duration.ofHours(1).plusMinutes(30);
        System.out.println("1h 30m: " + duration7);  // PT1H30M
        
        
        // 두 시간 사이 기간
        LocalTime start = LocalTime.of(9, 0);
        LocalTime end = LocalTime.of(17, 30);
        
        Duration workTime = Duration.between(start, end);
        System.out.println("Work time: " + workTime);  // PT8H30M
        
        
        // LocalDateTime으로도 가능
        LocalDateTime dt1 = LocalDateTime.of(2024, 12, 25, 14, 0);
        LocalDateTime dt2 = LocalDateTime.of(2024, 12, 25, 16, 30);
        
        Duration between = Duration.between(dt1, dt2);
        System.out.println("Between: " + between);  // PT2H30M
        
        
        // 문자열 파싱
        Duration parsed = Duration.parse("PT2H30M");
        System.out.println("Parsed: " + parsed);
    }
}
```

### 2.2 읽기

```java
public class DurationGetter {
    public static void main(String[] args) {
        Duration duration = Duration.ofHours(2).plusMinutes(30).plusSeconds(45);
        
        // 초 단위 (가장 정확)
        long seconds = duration.getSeconds();
        int nano = duration.getNano();
        
        System.out.println("Seconds: " + seconds);  // 9045
        System.out.println("Nano: " + nano);        // 0
        
        
        // 변환 메서드
        long totalSeconds = duration.toSeconds();
        long totalMillis = duration.toMillis();
        long totalNanos = duration.toNanos();
        
        long days = duration.toDays();
        long hours = duration.toHours();
        long minutes = duration.toMinutes();
        
        System.out.println("Total seconds: " + totalSeconds);  // 9045
        System.out.println("Total minutes: " + minutes);       // 150
        System.out.println("Total hours: " + hours);           // 2
        
        
        // 부분 추출 (Java 9+)
        long hoursPart = duration.toHoursPart();
        int minutesPart = duration.toMinutesPart();
        int secondsPart = duration.toSecondsPart();
        
        System.out.println(String.format("%d시간 %d분 %d초",
                                        hoursPart, minutesPart, secondsPart));
        // 2시간 30분 45초
        
        
        // 음수 여부
        boolean negative = duration.isNegative();
        boolean zero = duration.isZero();
        
        System.out.println("Is negative: " + negative);
        System.out.println("Is zero: " + zero);
    }
}
```

### 2.3 수정

```java
public class DurationModification {
    public static void main(String[] args) {
        Duration duration = Duration.ofHours(2).plusMinutes(30);
        
        // plus - 더하기
        Duration plusDays = duration.plusDays(1);
        Duration plusHours = duration.plusHours(1);
        Duration plusMinutes = duration.plusMinutes(15);
        Duration plusSeconds = duration.plusSeconds(30);
        
        System.out.println("Original: " + duration);       // PT2H30M
        System.out.println("Plus 1 day: " + plusDays);    // PT26H30M
        System.out.println("Plus 1 hour: " + plusHours);  // PT3H30M
        
        
        // minus - 빼기
        Duration minusMinutes = duration.minusMinutes(30);
        System.out.println("Minus 30m: " + minusMinutes);  // PT2H
        
        
        // with - 설정
        Duration withSeconds = duration.withSeconds(0);
        Duration withNanos = duration.withNanos(123456789);
        
        
        // multipliedBy - 곱하기
        Duration doubled = duration.multipliedBy(2);
        System.out.println("Doubled: " + doubled);  // PT5H
        
        
        // dividedBy - 나누기
        Duration halved = duration.dividedBy(2);
        System.out.println("Halved: " + halved);  // PT1H15M
        
        
        // negated - 부호 반전
        Duration negated = duration.negated();
        System.out.println("Negated: " + negated);  // PT-2H-30M
        
        
        // abs - 절대값
        Duration negative = Duration.ofHours(-2);
        Duration absolute = negative.abs();
        System.out.println("Absolute: " + absolute);  // PT2H
    }
}
```

### 2.4 시간 연산에 활용

```java
public class DurationTimeCalculation {
    public static void main(String[] args) {
        LocalTime now = LocalTime.now();
        
        // Duration을 사용한 시간 연산
        Duration duration = Duration.ofHours(2).plusMinutes(30);
        LocalTime later = now.plus(duration);
        
        System.out.println("Now: " + now);
        System.out.println("Later: " + later);
        
        
        // 근무 시간 계산
        LocalTime checkIn = LocalTime.of(9, 0);
        LocalTime checkOut = LocalTime.of(18, 0);
        
        Duration workTime = Duration.between(checkIn, checkOut);
        long workHours = workTime.toHours();
        
        System.out.println("Work hours: " + workHours);  // 9
        
        
        // 점심시간 제외
        Duration lunchBreak = Duration.ofHours(1);
        Duration actualWork = workTime.minus(lunchBreak);
        
        System.out.println("Actual work: " + actualWork.toHours() + "h");  // 8h
        
        
        // 경과 시간 측정
        LocalDateTime start = LocalDateTime.now();
        
        // 작업 수행...
        try {
            Thread.sleep(1000);  // 1초 대기
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        LocalDateTime end = LocalDateTime.now();
        Duration elapsed = Duration.between(start, end);
        
        System.out.println("Elapsed: " + elapsed.toMillis() + "ms");
    }
}
```

---

## 3. Period vs Duration 비교

### 3.1 차이점

```java
public class PeriodVsDuration {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now();
        LocalTime time = LocalTime.now();
        LocalDateTime dateTime = LocalDateTime.now();
        
        // Period: 날짜 기반 (년, 월, 일)
        Period period = Period.ofMonths(1);
        LocalDate nextMonth = date.plus(period);
        
        System.out.println("Date + 1 month: " + nextMonth);
        
        
        // Duration: 시간 기반 (시, 분, 초)
        Duration duration = Duration.ofHours(24);
        LocalTime tomorrow = time.plus(duration);
        LocalDateTime nextDay = dateTime.plus(duration);
        
        System.out.println("Time + 24h: " + tomorrow);
        System.out.println("DateTime + 24h: " + nextDay);
        
        
        // Period는 LocalTime에 사용 불가
        // LocalTime newTime = time.plus(Period.ofDays(1));  // 컴파일 에러!
        
        
        // Duration은 LocalDate에 사용 불가
        // LocalDate newDate = date.plus(Duration.ofHours(24));  // 컴파일 에러!
        
        
        // LocalDateTime은 둘 다 가능
        LocalDateTime dt = LocalDateTime.now();
        LocalDateTime dt1 = dt.plus(Period.ofDays(1));
        LocalDateTime dt2 = dt.plus(Duration.ofHours(24));
        
        System.out.println("DateTime + Period: " + dt1);
        System.out.println("DateTime + Duration: " + dt2);
    }
}
```

### 3.2 비교표

```java
public class ComparisonTable {
    public static void main(String[] args) {
        System.out.println("=== Period vs Duration ===\n");
        
        System.out.println("Period:");
        System.out.println("- 날짜 기반 (년, 월, 일)");
        System.out.println("- LocalDate와 함께 사용");
        System.out.println("- 사람의 관점 (1개월, 1년)");
        System.out.println("- 예: P1Y2M3D (1년 2개월 3일)");
        System.out.println();
        
        System.out.println("Duration:");
        System.out.println("- 시간 기반 (시, 분, 초, 나노초)");
        System.out.println("- LocalTime, LocalDateTime과 함께 사용");
        System.out.println("- 정확한 시간 (초 단위)");
        System.out.println("- 예: PT2H30M (2시간 30분)");
        System.out.println();
        
        
        // 실제 예시
        LocalDate date1 = LocalDate.of(2024, 1, 31);
        LocalDate date2 = date1.plus(Period.ofMonths(1));
        
        System.out.println("2024-01-31 + 1개월 = " + date2);
        // 2024-02-29 (윤년이므로 2월 29일)
        
        
        LocalDateTime dt1 = LocalDateTime.of(2024, 1, 31, 12, 0);
        LocalDateTime dt2 = dt1.plus(Duration.ofDays(1));
        
        System.out.println("2024-01-31 12:00 + 24시간 = " + dt2);
        // 2024-02-01 12:00 (정확히 24시간 후)
    }
}
```

### 3.3 선택 가이드

```java
public class SelectionGuide {
    public static void main(String[] args) {
        // Period 사용 경우
        System.out.println("=== Period 사용 ===");
        
        // 1. 나이 계산
        LocalDate birth = LocalDate.of(1990, 5, 15);
        Period age = Period.between(birth, LocalDate.now());
        System.out.println("나이: " + age.getYears() + "세");
        
        
        // 2. 만기일 계산
        LocalDate start = LocalDate.now();
        LocalDate maturity = start.plus(Period.ofYears(1));
        System.out.println("만기일: " + maturity);
        
        
        // 3. 월별 납부
        LocalDate paymentDate = LocalDate.now();
        LocalDate nextPayment = paymentDate.plus(Period.ofMonths(1));
        System.out.println("다음 납부일: " + nextPayment);
        
        
        System.out.println("\n=== Duration 사용 ===");
        
        // 1. 근무 시간
        LocalTime checkIn = LocalTime.of(9, 0);
        LocalTime checkOut = LocalTime.of(18, 0);
        Duration workTime = Duration.between(checkIn, checkOut);
        System.out.println("근무 시간: " + workTime.toHours() + "시간");
        
        
        // 2. 작업 시간 측정
        Instant startTime = Instant.now();
        // ... 작업 수행
        Instant endTime = Instant.now();
        Duration elapsed = Duration.between(startTime, endTime);
        System.out.println("소요 시간: " + elapsed.toMillis() + "ms");
        
        
        // 3. 타이머
        Duration timer = Duration.ofMinutes(3);
        System.out.println("타이머: " + timer.toSeconds() + "초");
    }
}
```

---

## 4. ChronoUnit - 단위별 계산

### 4.1 기본 사용

```java
import java.time.temporal.ChronoUnit;

public class ChronoUnitBasic {
    public static void main(String[] args) {
        LocalDate date1 = LocalDate.of(2024, 1, 1);
        LocalDate date2 = LocalDate.of(2024, 12, 31);
        
        // 단위별 계산
        long days = ChronoUnit.DAYS.between(date1, date2);
        long weeks = ChronoUnit.WEEKS.between(date1, date2);
        long months = ChronoUnit.MONTHS.between(date1, date2);
        long years = ChronoUnit.YEARS.between(date1, date2);
        
        System.out.println("Days: " + days);      // 365
        System.out.println("Weeks: " + weeks);    // 52
        System.out.println("Months: " + months);  // 11
        System.out.println("Years: " + years);    // 0
        
        
        // LocalTime
        LocalTime time1 = LocalTime.of(9, 0);
        LocalTime time2 = LocalTime.of(17, 30);
        
        long hours = ChronoUnit.HOURS.between(time1, time2);
        long minutes = ChronoUnit.MINUTES.between(time1, time2);
        long seconds = ChronoUnit.SECONDS.between(time1, time2);
        
        System.out.println("Hours: " + hours);      // 8
        System.out.println("Minutes: " + minutes);  // 510
        System.out.println("Seconds: " + seconds);  // 30600
    }
}
```

### 4.2 날짜 연산

```java
public class ChronoUnitCalculation {
    public static void main(String[] args) {
        LocalDate date = LocalDate.now();
        
        // plus - 더하기
        LocalDate tomorrow = date.plus(1, ChronoUnit.DAYS);
        LocalDate nextWeek = date.plus(1, ChronoUnit.WEEKS);
        LocalDate nextMonth = date.plus(1, ChronoUnit.MONTHS);
        LocalDate nextYear = date.plus(1, ChronoUnit.YEARS);
        
        System.out.println("Today: " + date);
        System.out.println("Tomorrow: " + tomorrow);
        System.out.println("Next week: " + nextWeek);
        System.out.println("Next month: " + nextMonth);
        
        
        // minus - 빼기
        LocalDate yesterday = date.minus(1, ChronoUnit.DAYS);
        LocalDate lastMonth = date.minus(1, ChronoUnit.MONTHS);
        
        System.out.println("Yesterday: " + yesterday);
        System.out.println("Last month: " + lastMonth);
        
        
        // 큰 단위
        LocalDate after100Days = date.plus(100, ChronoUnit.DAYS);
        LocalDate after10Years = date.plus(10, ChronoUnit.YEARS);
        
        System.out.println("After 100 days: " + after100Days);
        System.out.println("After 10 years: " + after10Years);
    }
}
```

### 4.3 사용 가능한 단위

```java
public class ChronoUnitList {
    public static void main(String[] args) {
        System.out.println("=== 날짜 단위 ===");
        System.out.println("DAYS: 일");
        System.out.println("WEEKS: 주");
        System.out.println("MONTHS: 월");
        System.out.println("YEARS: 년");
        System.out.println("DECADES: 10년");
        System.out.println("CENTURIES: 100년");
        System.out.println("MILLENNIA: 1000년");
        
        System.out.println("\n=== 시간 단위 ===");
        System.out.println("NANOS: 나노초");
        System.out.println("MICROS: 마이크로초");
        System.out.println("MILLIS: 밀리초");
        System.out.println("SECONDS: 초");
        System.out.println("MINUTES: 분");
        System.out.println("HOURS: 시");
        System.out.println("HALF_DAYS: 반나절 (12시간)");
        
        
        // 예시
        LocalDate date = LocalDate.now();
        
        LocalDate next10Years = date.plus(1, ChronoUnit.DECADES);
        LocalDate next100Years = date.plus(1, ChronoUnit.CENTURIES);
        
        System.out.println("\nToday: " + date);
        System.out.println("10 years later: " + next10Years);
        System.out.println("100 years later: " + next100Years);
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 프로젝트 기간 계산

```java
// 프로젝트 시작일과 종료일로 전체 기간과 남은 기간 계산
public class Problem1 {
    public static void printProjectStatus(LocalDate startDate, LocalDate endDate) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        LocalDate start = LocalDate.of(2024, 1, 1);
        LocalDate end = LocalDate.of(2024, 12, 31);
        
        printProjectStatus(start, end);
        // 전체 기간: 12개월
        // 남은 기간: X개월 X일
        // 진행률: X%
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void printProjectStatus(LocalDate startDate, LocalDate endDate) {
        LocalDate today = LocalDate.now();
        
        // 전체 기간
        Period totalPeriod = Period.between(startDate, endDate);
        long totalDays = ChronoUnit.DAYS.between(startDate, endDate);
        
        System.out.println("=== 프로젝트 현황 ===");
        System.out.println("전체 기간: " + totalPeriod.getMonths() + "개월 " +
                         totalPeriod.getDays() + "일 (" + totalDays + "일)");
        
        
        // 경과 기간
        if (today.isBefore(startDate)) {
            System.out.println("프로젝트 시작 전");
            Period until = Period.between(today, startDate);
            System.out.println("시작까지: " + until.getMonths() + "개월 " +
                             until.getDays() + "일");
        } else if (today.isAfter(endDate)) {
            System.out.println("프로젝트 종료");
        } else {
            // 진행 중
            long elapsedDays = ChronoUnit.DAYS.between(startDate, today);
            Period remaining = Period.between(today, endDate);
            long remainingDays = ChronoUnit.DAYS.between(today, endDate);
            
            double progress = (double) elapsedDays / totalDays * 100;
            
            System.out.println("경과 기간: " + elapsedDays + "일");
            System.out.println("남은 기간: " + remaining.getMonths() + "개월 " +
                             remaining.getDays() + "일 (" + remainingDays + "일)");
            System.out.println("진행률: " + String.format("%.1f%%", progress));
        }
    }
}
```
</details>

---

### 문제 2: 평균 작업 시간

```java
// 여러 작업의 소요 시간으로 평균 계산
public class Problem2 {
    public static Duration calculateAverage(List<Duration> durations) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        List<Duration> tasks = Arrays.asList(
            Duration.ofMinutes(30),
            Duration.ofMinutes(45),
            Duration.ofMinutes(25),
            Duration.ofMinutes(50)
        );
        
        Duration average = calculateAverage(tasks);
        System.out.println("Average: " + average.toMinutes() + " minutes");
        // Average: 37 minutes
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static Duration calculateAverage(List<Duration> durations) {
        if (durations.isEmpty()) {
            return Duration.ZERO;
        }
        
        // 전체 합계
        Duration total = durations.stream()
                                 .reduce(Duration.ZERO, Duration::plus);
        
        // 평균 계산
        long totalSeconds = total.getSeconds();
        long averageSeconds = totalSeconds / durations.size();
        
        return Duration.ofSeconds(averageSeconds);
    }
    
    // 더 자세한 버전
    public static String calculateAverageDetailed(List<Duration> durations) {
        Duration average = calculateAverage(durations);
        
        long hours = average.toHours();
        long minutes = average.toMinutes() % 60;
        long seconds = average.getSeconds() % 60;
        
        return String.format("%d시간 %d분 %d초", hours, minutes, seconds);
    }
}
```
</details>

---

### 문제 3: 근속 기간 계산

```java
// 입사일로 근속 기간 계산 (년, 월, 일)
public class Problem3 {
    public static String calculateTenure(LocalDate joinDate) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        LocalDate joinDate = LocalDate.of(2020, 3, 15);
        String tenure = calculateTenure(joinDate);
        System.out.println(tenure);
        // 4년 9개월 1일 근속
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static String calculateTenure(LocalDate joinDate) {
        LocalDate today = LocalDate.now();
        Period period = Period.between(joinDate, today);
        
        return String.format("%d년 %d개월 %d일 근속",
                           period.getYears(),
                           period.getMonths(),
                           period.getDays());
    }
    
    // 총 근무일 수 포함 버전
    public static String calculateTenureWithDays(LocalDate joinDate) {
        LocalDate today = LocalDate.now();
        Period period = Period.between(joinDate, today);
        long totalDays = ChronoUnit.DAYS.between(joinDate, today);
        
        return String.format("%d년 %d개월 %d일 근속 (총 %d일)",
                           period.getYears(),
                           period.getMonths(),
                           period.getDays(),
                           totalDays);
    }
}
```
</details>

---

## 📌 핵심 정리

### Period (날짜 기간)
```java
// 생성
Period.ofYears(1)
Period.ofMonths(6)
Period.ofDays(10)
Period.of(1, 6, 15)                // 1년 6개월 15일
Period.between(start, end)

// 읽기
period.getYears()
period.getMonths()
period.getDays()

// 수정
period.plusMonths(1)
period.minusDays(5)
period.normalized()                // 정규화 (월 → 년)

// 사용
date.plus(period)
date.minus(period)
```

### Duration (시간 기간)
```java
// 생성
Duration.ofDays(1)
Duration.ofHours(24)
Duration.ofMinutes(90)
Duration.ofSeconds(3600)
Duration.between(start, end)

// 읽기
duration.getSeconds()
duration.toHours()
duration.toMinutes()
duration.toHoursPart()             // Java 9+
duration.toMinutesPart()

// 수정
duration.plusHours(1)
duration.minusMinutes(30)
duration.multipliedBy(2)
duration.dividedBy(2)

// 사용
time.plus(duration)
dateTime.plus(duration)
```

### ChronoUnit
```java
// 계산
ChronoUnit.DAYS.between(date1, date2)
ChronoUnit.HOURS.between(time1, time2)

// 연산
date.plus(10, ChronoUnit.DAYS)
date.minus(1, ChronoUnit.MONTHS)
```

### 선택 가이드
```java
// Period: 사람의 관점
"1개월 후", "1년 뒤", "나이 계산"

// Duration: 정확한 시간
"2시간 30분", "작업 시간", "경과 시간"

// ChronoUnit: 단위 지정
"100일 후", "52주 전"
```

### 주의사항
```java
// ❌ Period는 LocalTime에 사용 불가
time.plus(Period.ofDays(1))        // 에러!

// ❌ Duration은 LocalDate에 사용 불가
date.plus(Duration.ofHours(24))    // 에러!

// ✅ LocalDateTime은 둘 다 가능
dateTime.plus(Period.ofDays(1))
dateTime.plus(Duration.ofHours(24))
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 03. ZonedDateTime & Instant](./DateTime-03-Zoned.md) | [다음: 05. DateTimeFormatter →](./DateTime-05-Formatter.md)**

</div>
