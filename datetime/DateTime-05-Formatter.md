# DateTime 05. DateTimeFormatter

> 날짜/시간 포맷팅과 파싱 - 문자열 변환의 모든 것  
> Java API Reference

---

## 📑 목차

1. [DateTimeFormatter 기본](#1-datetimeformatter-기본)
2. [미리 정의된 포맷터](#2-미리-정의된-포맷터)
3. [커스텀 패턴](#3-커스텀-패턴)
4. [로케일과 다국어](#4-로케일과-다국어)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. DateTimeFormatter 기본

### 1.1 포맷팅 (객체 → 문자열)

```java
import java.time.LocalDate;
import java.time.LocalTime;
import java.time.LocalDateTime;
import java.time.format.DateTimeFormatter;

public class FormattingBasic {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        LocalTime time = LocalTime.of(14, 30, 45);
        LocalDateTime dateTime = LocalDateTime.of(date, time);
        
        // 기본 toString()
        System.out.println("Date: " + date);           // 2024-12-25
        System.out.println("Time: " + time);           // 14:30:45
        System.out.println("DateTime: " + dateTime);   // 2024-12-25T14:30:45
        
        
        // DateTimeFormatter 사용
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
        String formatted = date.format(formatter);
        System.out.println("Formatted: " + formatted);  // 2024-12-25
        
        
        // 한글 포맷
        DateTimeFormatter koreanFormatter = DateTimeFormatter.ofPattern("yyyy년 MM월 dd일");
        String korean = date.format(koreanFormatter);
        System.out.println("Korean: " + korean);  // 2024년 12월 25일
        
        
        // 시간 포맷
        DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern("HH:mm:ss");
        String formattedTime = time.format(timeFormatter);
        System.out.println("Time: " + formattedTime);  // 14:30:45
        
        
        // 날짜와 시간
        DateTimeFormatter dtFormatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        String formattedDateTime = dateTime.format(dtFormatter);
        System.out.println("DateTime: " + formattedDateTime);  // 2024-12-25 14:30:45
    }
}
```

### 1.2 파싱 (문자열 → 객체)

```java
public class ParsingBasic {
    public static void main(String[] args) {
        // 기본 ISO 형식
        LocalDate date1 = LocalDate.parse("2024-12-25");
        LocalTime time1 = LocalTime.parse("14:30:45");
        LocalDateTime dateTime1 = LocalDateTime.parse("2024-12-25T14:30:45");
        
        System.out.println("Date: " + date1);
        System.out.println("Time: " + time1);
        System.out.println("DateTime: " + dateTime1);
        
        
        // 커스텀 포맷으로 파싱
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy/MM/dd");
        LocalDate date2 = LocalDate.parse("2024/12/25", formatter);
        System.out.println("Custom date: " + date2);
        
        
        // 한글 포맷 파싱
        DateTimeFormatter koreanFormatter = DateTimeFormatter.ofPattern("yyyy년 MM월 dd일");
        LocalDate date3 = LocalDate.parse("2024년 12월 25일", koreanFormatter);
        System.out.println("Korean date: " + date3);
        
        
        // 시간 파싱
        DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern("HH시 mm분");
        LocalTime time2 = LocalTime.parse("14시 30분", timeFormatter);
        System.out.println("Korean time: " + time2);
        
        
        // 예외 처리
        try {
            LocalDate invalid = LocalDate.parse("2024-13-01");  // 13월!
        } catch (Exception e) {
            System.out.println("Parse error: " + e.getMessage());
        }
    }
}
```

---

## 2. 미리 정의된 포맷터

### 2.1 ISO 포맷터

```java
public class ISOFormatters {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        LocalTime time = LocalTime.of(14, 30, 45);
        LocalDateTime dateTime = LocalDateTime.of(date, time);
        ZonedDateTime zonedDateTime = ZonedDateTime.of(dateTime, ZoneId.of("Asia/Seoul"));
        
        // ISO_LOCAL_DATE
        String isoDate = date.format(DateTimeFormatter.ISO_LOCAL_DATE);
        System.out.println("ISO_LOCAL_DATE: " + isoDate);
        // 2024-12-25
        
        
        // ISO_LOCAL_TIME
        String isoTime = time.format(DateTimeFormatter.ISO_LOCAL_TIME);
        System.out.println("ISO_LOCAL_TIME: " + isoTime);
        // 14:30:45
        
        
        // ISO_LOCAL_DATE_TIME
        String isoDateTime = dateTime.format(DateTimeFormatter.ISO_LOCAL_DATE_TIME);
        System.out.println("ISO_LOCAL_DATE_TIME: " + isoDateTime);
        // 2024-12-25T14:30:45
        
        
        // ISO_ZONED_DATE_TIME
        String isoZonedDateTime = zonedDateTime.format(DateTimeFormatter.ISO_ZONED_DATE_TIME);
        System.out.println("ISO_ZONED_DATE_TIME: " + isoZonedDateTime);
        // 2024-12-25T14:30:45+09:00[Asia/Seoul]
        
        
        // ISO_INSTANT
        Instant instant = Instant.now();
        String isoInstant = DateTimeFormatter.ISO_INSTANT.format(instant);
        System.out.println("ISO_INSTANT: " + isoInstant);
        // 2024-12-16T05:30:45.123Z
        
        
        // ISO_DATE
        String isoDate2 = date.format(DateTimeFormatter.ISO_DATE);
        System.out.println("ISO_DATE: " + isoDate2);
        // 2024-12-25
    }
}
```

### 2.2 RFC 포맷터

```java
public class RFCFormatters {
    public static void main(String[] args) {
        ZonedDateTime zonedDateTime = ZonedDateTime.now(ZoneId.of("Asia/Seoul"));
        
        // RFC_1123_DATE_TIME (HTTP 헤더 등에 사용)
        String rfc1123 = zonedDateTime.format(DateTimeFormatter.RFC_1123_DATE_TIME);
        System.out.println("RFC_1123_DATE_TIME: " + rfc1123);
        // Mon, 25 Dec 2024 14:30:45 +0900
        
        
        // 파싱도 가능
        ZonedDateTime parsed = ZonedDateTime.parse(
            "Mon, 25 Dec 2024 14:30:45 +0900",
            DateTimeFormatter.RFC_1123_DATE_TIME
        );
        System.out.println("Parsed: " + parsed);
    }
}
```

### 2.3 BASIC 포맷터

```java
public class BasicFormatters {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        LocalTime time = LocalTime.of(14, 30, 45);
        
        // BASIC_ISO_DATE (구분자 없음)
        String basicDate = date.format(DateTimeFormatter.BASIC_ISO_DATE);
        System.out.println("BASIC_ISO_DATE: " + basicDate);
        // 20241225
        
        
        // ISO_ORDINAL_DATE (년 + 일수)
        String ordinal = date.format(DateTimeFormatter.ISO_ORDINAL_DATE);
        System.out.println("ISO_ORDINAL_DATE: " + ordinal);
        // 2024-360
        
        
        // ISO_WEEK_DATE (주 기반)
        String week = date.format(DateTimeFormatter.ISO_WEEK_DATE);
        System.out.println("ISO_WEEK_DATE: " + week);
        // 2024-W52-3
    }
}
```

---

## 3. 커스텀 패턴

### 3.1 날짜 패턴

```java
public class DatePatterns {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        
        // 년도
        System.out.println(date.format(DateTimeFormatter.ofPattern("yyyy")));     // 2024
        System.out.println(date.format(DateTimeFormatter.ofPattern("yy")));       // 24
        
        
        // 월
        System.out.println(date.format(DateTimeFormatter.ofPattern("MM")));       // 12
        System.out.println(date.format(DateTimeFormatter.ofPattern("M")));        // 12
        System.out.println(date.format(DateTimeFormatter.ofPattern("MMM")));      // Dec
        System.out.println(date.format(DateTimeFormatter.ofPattern("MMMM")));     // December
        
        
        // 일
        System.out.println(date.format(DateTimeFormatter.ofPattern("dd")));       // 25
        System.out.println(date.format(DateTimeFormatter.ofPattern("d")));        // 25
        
        
        // 요일
        System.out.println(date.format(DateTimeFormatter.ofPattern("E")));        // Wed
        System.out.println(date.format(DateTimeFormatter.ofPattern("EEEE")));     // Wednesday
        
        
        // 조합 예시
        System.out.println(date.format(DateTimeFormatter.ofPattern("yyyy-MM-dd")));           // 2024-12-25
        System.out.println(date.format(DateTimeFormatter.ofPattern("yyyy/MM/dd")));           // 2024/12/25
        System.out.println(date.format(DateTimeFormatter.ofPattern("yyyy년 MM월 dd일")));      // 2024년 12월 25일
        System.out.println(date.format(DateTimeFormatter.ofPattern("yyyy.MM.dd (E)")));       // 2024.12.25 (Wed)
        System.out.println(date.format(DateTimeFormatter.ofPattern("MMMM d, yyyy")));         // December 25, 2024
    }
}
```

### 3.2 시간 패턴

```java
public class TimePatterns {
    public static void main(String[] args) {
        LocalTime time = LocalTime.of(14, 30, 45, 123456789);
        
        // 시간 (24시간)
        System.out.println(time.format(DateTimeFormatter.ofPattern("HH")));       // 14
        System.out.println(time.format(DateTimeFormatter.ofPattern("H")));        // 14
        
        
        // 시간 (12시간)
        System.out.println(time.format(DateTimeFormatter.ofPattern("hh")));       // 02
        System.out.println(time.format(DateTimeFormatter.ofPattern("h")));        // 2
        System.out.println(time.format(DateTimeFormatter.ofPattern("a")));        // PM
        
        
        // 분
        System.out.println(time.format(DateTimeFormatter.ofPattern("mm")));       // 30
        System.out.println(time.format(DateTimeFormatter.ofPattern("m")));        // 30
        
        
        // 초
        System.out.println(time.format(DateTimeFormatter.ofPattern("ss")));       // 45
        System.out.println(time.format(DateTimeFormatter.ofPattern("s")));        // 45
        
        
        // 밀리초 / 나노초
        System.out.println(time.format(DateTimeFormatter.ofPattern("SSS")));      // 123
        System.out.println(time.format(DateTimeFormatter.ofPattern("SSSSSS")));   // 123456
        System.out.println(time.format(DateTimeFormatter.ofPattern("SSSSSSSSS"))); // 123456789
        
        
        // 조합 예시
        System.out.println(time.format(DateTimeFormatter.ofPattern("HH:mm:ss")));           // 14:30:45
        System.out.println(time.format(DateTimeFormatter.ofPattern("HH시 mm분 ss초")));      // 14시 30분 45초
        System.out.println(time.format(DateTimeFormatter.ofPattern("h:mm a")));            // 2:30 PM
        System.out.println(time.format(DateTimeFormatter.ofPattern("HH:mm:ss.SSS")));      // 14:30:45.123
    }
}
```

### 3.3 복합 패턴

```java
public class ComplexPatterns {
    public static void main(String[] args) {
        LocalDateTime dateTime = LocalDateTime.of(2024, 12, 25, 14, 30, 45);
        
        // 다양한 조합
        DateTimeFormatter f1 = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        System.out.println(dateTime.format(f1));
        // 2024-12-25 14:30:45
        
        
        DateTimeFormatter f2 = DateTimeFormatter.ofPattern("yyyy년 MM월 dd일 HH시 mm분");
        System.out.println(dateTime.format(f2));
        // 2024년 12월 25일 14시 30분
        
        
        DateTimeFormatter f3 = DateTimeFormatter.ofPattern("MMMM d, yyyy 'at' h:mm a");
        System.out.println(dateTime.format(f3));
        // December 25, 2024 at 2:30 PM
        
        
        DateTimeFormatter f4 = DateTimeFormatter.ofPattern("yyyy/MM/dd (E) HH:mm");
        System.out.println(dateTime.format(f4));
        // 2024/12/25 (Wed) 14:30
        
        
        // 텍스트 리터럴 (작은따옴표로 감싸기)
        DateTimeFormatter f5 = DateTimeFormatter.ofPattern("'Today is' yyyy-MM-dd");
        System.out.println(LocalDate.now().format(f5));
        // Today is 2024-12-16
        
        
        DateTimeFormatter f6 = DateTimeFormatter.ofPattern("'['HH:mm:ss']'");
        System.out.println(LocalTime.now().format(f6));
        // [14:30:45]
    }
}
```

### 3.4 패턴 문자 정리

```java
public class PatternCheatSheet {
    public static void main(String[] args) {
        System.out.println("=== 날짜 패턴 ===");
        System.out.println("y: 년도 (yyyy=2024, yy=24)");
        System.out.println("M: 월 (MM=12, M=12, MMM=Dec, MMMM=December)");
        System.out.println("d: 일 (dd=25, d=25)");
        System.out.println("E: 요일 (E=Wed, EEEE=Wednesday)");
        System.out.println("D: 년 중 일수 (1-365/366)");
        System.out.println("w: 년 중 주차 (1-53)");
        System.out.println("W: 월 중 주차 (1-5)");
        
        System.out.println("\n=== 시간 패턴 ===");
        System.out.println("H: 시간 24시간 (HH=14, H=14)");
        System.out.println("h: 시간 12시간 (hh=02, h=2)");
        System.out.println("a: AM/PM");
        System.out.println("m: 분 (mm=30, m=30)");
        System.out.println("s: 초 (ss=45, s=45)");
        System.out.println("S: 밀리초 (SSS=123)");
        System.out.println("n: 나노초");
        
        System.out.println("\n=== 시간대 ===");
        System.out.println("z: 시간대 이름 (PST, KST)");
        System.out.println("Z: 시간대 오프셋 (+0900)");
        System.out.println("X: ISO 시간대 (+09, +0900, +09:00)");
        System.out.println("V: 시간대 ID (Asia/Seoul)");
    }
}
```

---

## 4. 로케일과 다국어

### 4.1 로케일 적용

```java
import java.util.Locale;

public class LocaleFormatting {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        
        // 한국어
        DateTimeFormatter koreanFormatter = DateTimeFormatter
            .ofPattern("yyyy년 MMMM d일 EEEE")
            .withLocale(Locale.KOREAN);
        System.out.println("한국어: " + date.format(koreanFormatter));
        // 2024년 12월 25일 수요일
        
        
        // 영어
        DateTimeFormatter englishFormatter = DateTimeFormatter
            .ofPattern("MMMM d, yyyy (EEEE)")
            .withLocale(Locale.ENGLISH);
        System.out.println("English: " + date.format(englishFormatter));
        // December 25, 2024 (Wednesday)
        
        
        // 일본어
        DateTimeFormatter japaneseFormatter = DateTimeFormatter
            .ofPattern("yyyy年 M月 d日 (E)")
            .withLocale(Locale.JAPANESE);
        System.out.println("日本語: " + date.format(japaneseFormatter));
        // 2024年 12月 25日 (水)
        
        
        // 중국어
        DateTimeFormatter chineseFormatter = DateTimeFormatter
            .ofPattern("yyyy年 M月 d日 E")
            .withLocale(Locale.SIMPLIFIED_CHINESE);
        System.out.println("中文: " + date.format(chineseFormatter));
        // 2024年 12月 25日 星期三
        
        
        // 프랑스어
        DateTimeFormatter frenchFormatter = DateTimeFormatter
            .ofPattern("d MMMM yyyy (EEEE)")
            .withLocale(Locale.FRENCH);
        System.out.println("Français: " + date.format(frenchFormatter));
        // 25 décembre 2024 (mercredi)
    }
}
```

### 4.2 ofLocalizedDate/Time

```java
import java.time.format.FormatStyle;

public class LocalizedFormatters {
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        LocalTime time = LocalTime.of(14, 30, 45);
        LocalDateTime dateTime = LocalDateTime.of(date, time);
        
        // 날짜 스타일
        System.out.println("=== Date Styles (Korean) ===");
        System.out.println("FULL: " + date.format(
            DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL).withLocale(Locale.KOREAN)));
        // 2024년 12월 25일 수요일
        
        System.out.println("LONG: " + date.format(
            DateTimeFormatter.ofLocalizedDate(FormatStyle.LONG).withLocale(Locale.KOREAN)));
        // 2024년 12월 25일
        
        System.out.println("MEDIUM: " + date.format(
            DateTimeFormatter.ofLocalizedDate(FormatStyle.MEDIUM).withLocale(Locale.KOREAN)));
        // 2024. 12. 25.
        
        System.out.println("SHORT: " + date.format(
            DateTimeFormatter.ofLocalizedDate(FormatStyle.SHORT).withLocale(Locale.KOREAN)));
        // 24. 12. 25.
        
        
        // 시간 스타일
        System.out.println("\n=== Time Styles (Korean) ===");
        System.out.println("FULL: " + time.format(
            DateTimeFormatter.ofLocalizedTime(FormatStyle.FULL).withLocale(Locale.KOREAN)));
        
        System.out.println("MEDIUM: " + time.format(
            DateTimeFormatter.ofLocalizedTime(FormatStyle.MEDIUM).withLocale(Locale.KOREAN)));
        
        System.out.println("SHORT: " + time.format(
            DateTimeFormatter.ofLocalizedTime(FormatStyle.SHORT).withLocale(Locale.KOREAN)));
        
        
        // 날짜+시간
        System.out.println("\n=== DateTime Styles (English) ===");
        System.out.println("MEDIUM: " + dateTime.format(
            DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM)
                           .withLocale(Locale.ENGLISH)));
        // Dec 25, 2024, 2:30:45 PM
    }
}
```

### 4.3 다국어 파싱

```java
public class MultilingualParsing {
    public static void main(String[] args) {
        // 한국어 파싱
        DateTimeFormatter koreanFormatter = DateTimeFormatter
            .ofPattern("yyyy년 MM월 dd일")
            .withLocale(Locale.KOREAN);
        
        LocalDate koreanDate = LocalDate.parse("2024년 12월 25일", koreanFormatter);
        System.out.println("Korean: " + koreanDate);
        
        
        // 영어 파싱 (월 이름)
        DateTimeFormatter englishFormatter = DateTimeFormatter
            .ofPattern("MMMM d, yyyy")
            .withLocale(Locale.ENGLISH);
        
        LocalDate englishDate = LocalDate.parse("December 25, 2024", englishFormatter);
        System.out.println("English: " + englishDate);
        
        
        // 대소문자 구분 없이 파싱
        DateTimeFormatter caseInsensitive = new DateTimeFormatterBuilder()
            .parseCaseInsensitive()
            .appendPattern("MMMM d, yyyy")
            .toFormatter(Locale.ENGLISH);
        
        LocalDate flexibleDate = LocalDate.parse("december 25, 2024", caseInsensitive);
        System.out.println("Case insensitive: " + flexibleDate);
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 로그 파싱

```java
// 로그 문자열에서 날짜/시간 추출
// 예: "[2024-12-25 14:30:45] User login: admin"
public class Problem1 {
    public static LocalDateTime extractDateTime(String log) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        String log = "[2024-12-25 14:30:45] User login: admin";
        LocalDateTime dateTime = extractDateTime(log);
        System.out.println("Extracted: " + dateTime);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static LocalDateTime extractDateTime(String log) {
        // 대괄호 안의 날짜/시간 추출
        int start = log.indexOf('[') + 1;
        int end = log.indexOf(']');
        String dateTimeStr = log.substring(start, end);
        
        // 파싱
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
        return LocalDateTime.parse(dateTimeStr, formatter);
    }
    
    // 정규식 사용 버전
    public static LocalDateTime extractDateTimeRegex(String log) {
        Pattern pattern = Pattern.compile("\\[(\\d{4}-\\d{2}-\\d{2} \\d{2}:\\d{2}:\\d{2})\\]");
        Matcher matcher = pattern.matcher(log);
        
        if (matcher.find()) {
            String dateTimeStr = matcher.group(1);
            DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
            return LocalDateTime.parse(dateTimeStr, formatter);
        }
        
        throw new IllegalArgumentException("No datetime found in log");
    }
}
```
</details>

---

### 문제 2: 파일명 생성

```java
// 현재 날짜/시간으로 파일명 생성
// 예: backup_20241225_143045.zip
public class Problem2 {
    public static String generateBackupFilename(String prefix) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        String filename = generateBackupFilename("backup");
        System.out.println("Filename: " + filename);
        // backup_20241225_143045.zip
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static String generateBackupFilename(String prefix) {
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd_HHmmss");
        String timestamp = now.format(formatter);
        
        return String.format("%s_%s.zip", prefix, timestamp);
    }
    
    // 밀리초 포함 버전
    public static String generateBackupFilenameWithMillis(String prefix) {
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd_HHmmss_SSS");
        String timestamp = now.format(formatter);
        
        return String.format("%s_%s.zip", prefix, timestamp);
    }
    
    // 안전한 파일명 (특수문자 제거)
    public static String generateSafeFilename(String prefix) {
        LocalDateTime now = LocalDateTime.now();
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyyMMdd-HHmmss");
        String timestamp = now.format(formatter);
        
        // prefix에서 특수문자 제거
        String safePrefix = prefix.replaceAll("[^a-zA-Z0-9_-]", "");
        
        return String.format("%s-%s.zip", safePrefix, timestamp);
    }
}
```
</details>

---

### 문제 3: 다국어 날짜 표시

```java
// 같은 날짜를 여러 언어로 표시
public class Problem3 {
    public static Map<String, String> formatInMultipleLanguages(LocalDate date) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        LocalDate date = LocalDate.of(2024, 12, 25);
        Map<String, String> formatted = formatInMultipleLanguages(date);
        
        formatted.forEach((lang, text) -> 
            System.out.println(lang + ": " + text));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static Map<String, String> formatInMultipleLanguages(LocalDate date) {
        Map<String, String> result = new LinkedHashMap<>();
        
        // 한국어
        DateTimeFormatter korean = DateTimeFormatter
            .ofPattern("yyyy년 M월 d일 (EEEE)")
            .withLocale(Locale.KOREAN);
        result.put("한국어", date.format(korean));
        
        // 영어
        DateTimeFormatter english = DateTimeFormatter
            .ofPattern("MMMM d, yyyy (EEEE)")
            .withLocale(Locale.ENGLISH);
        result.put("English", date.format(english));
        
        // 일본어
        DateTimeFormatter japanese = DateTimeFormatter
            .ofPattern("yyyy年M月d日 (E)")
            .withLocale(Locale.JAPANESE);
        result.put("日本語", date.format(japanese));
        
        // 중국어
        DateTimeFormatter chinese = DateTimeFormatter
            .ofPattern("yyyy年M月d日 E")
            .withLocale(Locale.SIMPLIFIED_CHINESE);
        result.put("中文", date.format(chinese));
        
        // 프랑스어
        DateTimeFormatter french = DateTimeFormatter
            .ofPattern("d MMMM yyyy (EEEE)")
            .withLocale(Locale.FRENCH);
        result.put("Français", date.format(french));
        
        return result;
    }
    
    // FormatStyle 사용 버전
    public static Map<String, String> formatWithStyles(LocalDate date) {
        Map<String, String> result = new LinkedHashMap<>();
        
        Locale[] locales = {
            Locale.KOREAN,
            Locale.ENGLISH,
            Locale.JAPANESE,
            Locale.FRENCH
        };
        
        String[] names = {"한국어", "English", "日本語", "Français"};
        
        for (int i = 0; i < locales.length; i++) {
            DateTimeFormatter formatter = DateTimeFormatter
                .ofLocalizedDate(FormatStyle.FULL)
                .withLocale(locales[i]);
            result.put(names[i], date.format(formatter));
        }
        
        return result;
    }
}
```
</details>

---

## 📌 핵심 정리

### 기본 사용
```java
// 포맷팅 (객체 → 문자열)
DateTimeFormatter formatter = DateTimeFormatter.ofPattern("yyyy-MM-dd");
String formatted = date.format(formatter);

// 파싱 (문자열 → 객체)
LocalDate date = LocalDate.parse("2024-12-25", formatter);
```

### 미리 정의된 포맷터
```java
DateTimeFormatter.ISO_LOCAL_DATE           // 2024-12-25
DateTimeFormatter.ISO_LOCAL_TIME           // 14:30:45
DateTimeFormatter.ISO_LOCAL_DATE_TIME      // 2024-12-25T14:30:45
DateTimeFormatter.ISO_ZONED_DATE_TIME      // 2024-12-25T14:30:45+09:00[Asia/Seoul]
DateTimeFormatter.RFC_1123_DATE_TIME       // Mon, 25 Dec 2024 14:30:45 +0900
```

### 주요 패턴
```java
// 날짜
"yyyy-MM-dd"           // 2024-12-25
"yyyy/MM/dd"           // 2024/12/25
"yyyy년 MM월 dd일"      // 2024년 12월 25일
"MMMM d, yyyy"         // December 25, 2024

// 시간
"HH:mm:ss"             // 14:30:45
"HH시 mm분"             // 14시 30분
"h:mm a"               // 2:30 PM

// 조합
"yyyy-MM-dd HH:mm:ss"  // 2024-12-25 14:30:45
```

### 로케일
```java
DateTimeFormatter formatter = DateTimeFormatter
    .ofPattern("MMMM d, yyyy")
    .withLocale(Locale.KOREAN);

// 또는
DateTimeFormatter.ofLocalizedDate(FormatStyle.FULL)
                 .withLocale(Locale.KOREAN);
```

### 주의사항
```java
// ❌ 파싱 오류
LocalDate.parse("2024/12/25")  // 기본은 ISO 형식!

// ✅ 포맷터 지정
LocalDate.parse("2024/12/25", DateTimeFormatter.ofPattern("yyyy/MM/dd"))

// ❌ 월 이름 로케일 불일치
LocalDate.parse("12월 25, 2024", englishFormatter)  // 에러!

// ✅ 로케일 일치
LocalDate.parse("12월 25, 2024", koreanFormatter)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 04. Period & Duration](./DateTime-04-Period.md) | [다음: 06. 레거시 vs 신규 API →](./DateTime-06-Legacy.md)**

</div>
