# Util 04. 정규표현식 완전 정복

> 패턴 매칭의 모든 것 - Regular Expression 마스터하기  
> Java API Reference

---

## 📑 목차

1. [정규표현식 기본](#1-정규표현식-기본)
2. [Pattern과 Matcher](#2-pattern과-matcher)
3. [기본 패턴](#3-기본-패턴)
4. [메타 문자](#4-메타-문자)
5. [수량자](#5-수량자)
6. [그룹과 캡처](#6-그룹과-캡처)
7. [문자열 메서드](#7-문자열-메서드)
8. [실전 패턴](#8-실전-패턴)
9. [고급 기법](#9-고급-기법)
10. [실전 연습 문제](#10-실전-연습-문제)

---

## 1. 정규표현식 기본

### 1.1 정규표현식이란?

```java
import java.util.regex.*;

public class RegexIntro {
    public static void main(String[] args) {
        System.out.println("=== 정규표현식이란? ===\n");
        
        System.out.println("1. 패턴 매칭");
        System.out.println("   - 문자열 검색");
        System.out.println("   - 문자열 검증");
        System.out.println("   - 문자열 치환\n");
        
        System.out.println("2. 특수 문자 사용");
        System.out.println("   - 메타 문자");
        System.out.println("   - 수량자");
        System.out.println("   - 그룹\n");
        
        System.out.println("3. 활용");
        System.out.println("   - 이메일 검증");
        System.out.println("   - 전화번호 포맷");
        System.out.println("   - 데이터 추출");
    }
}
```

### 1.2 간단한 예제

```java
public class SimpleRegex {
    public static void main(String[] args) {
        String text = "Hello World 123";
        
        // 단순 문자열 매칭
        System.out.println("'Hello' 포함: " + text.matches(".*Hello.*"));
        
        // 숫자 매칭
        System.out.println("숫자 포함: " + text.matches(".*\\d+.*"));
        
        // 대소문자 무관
        System.out.println("'hello' 포함 (대소문자 무관): " + 
            text.matches("(?i).*hello.*"));
        
        
        System.out.println("\n=== 정규표현식 구성 ===");
        System.out.println(".   : 임의의 문자");
        System.out.println("*   : 0개 이상");
        System.out.println("\\d  : 숫자");
        System.out.println("(?i): 대소문자 무관");
    }
}
```

---

## 2. Pattern과 Matcher

### 2.1 기본 사용법

```java
public class PatternMatcherBasic {
    public static void main(String[] args) {
        String text = "Hello World 123";
        
        // Pattern 컴파일
        Pattern pattern = Pattern.compile("\\d+");
        
        // Matcher 생성
        Matcher matcher = pattern.matcher(text);
        
        // 매칭 확인
        if (matcher.find()) {
            System.out.println("숫자 발견: " + matcher.group());
            System.out.println("위치: " + matcher.start() + "-" + matcher.end());
        }
        
        
        System.out.println("\n=== 프로세스 ===");
        System.out.println("1. Pattern.compile(regex)");
        System.out.println("2. pattern.matcher(text)");
        System.out.println("3. matcher.find() or matches()");
        System.out.println("4. matcher.group()");
    }
}
```

### 2.2 Matcher 메서드

```java
public class MatcherMethods {
    public static void main(String[] args) {
        String text = "cat bat rat";
        Pattern pattern = Pattern.compile("\\w+at");
        Matcher matcher = pattern.matcher(text);
        
        // matches() - 전체 매칭
        System.out.println("=== matches ===");
        System.out.println("matches: " + text.matches(".*at.*"));
        
        
        // find() - 부분 매칭
        System.out.println("\n=== find ===");
        matcher = pattern.matcher(text);
        while (matcher.find()) {
            System.out.println("발견: " + matcher.group());
        }
        
        
        // lookingAt() - 시작 부분 매칭
        System.out.println("\n=== lookingAt ===");
        matcher = pattern.matcher(text);
        System.out.println("lookingAt: " + matcher.lookingAt());
        
        
        // 위치 정보
        System.out.println("\n=== 위치 정보 ===");
        matcher = pattern.matcher(text);
        if (matcher.find()) {
            System.out.println("start: " + matcher.start());
            System.out.println("end: " + matcher.end());
            System.out.println("group: " + matcher.group());
        }
    }
}
```

### 2.3 Pattern 플래그

```java
public class PatternFlags {
    public static void main(String[] args) {
        String text = "Hello\nWorld";
        
        // CASE_INSENSITIVE (대소문자 무관)
        Pattern p1 = Pattern.compile("hello", Pattern.CASE_INSENSITIVE);
        System.out.println("CASE_INSENSITIVE: " + p1.matcher(text).find());
        
        
        // MULTILINE (여러 줄)
        Pattern p2 = Pattern.compile("^World", Pattern.MULTILINE);
        System.out.println("MULTILINE: " + p2.matcher(text).find());
        
        
        // DOTALL (. 이 \n 포함)
        Pattern p3 = Pattern.compile("Hello.World", Pattern.DOTALL);
        System.out.println("DOTALL: " + p3.matcher(text).matches());
        
        
        // 여러 플래그 조합
        Pattern p4 = Pattern.compile("hello.world", 
            Pattern.CASE_INSENSITIVE | Pattern.DOTALL);
        System.out.println("조합: " + p4.matcher(text).matches());
        
        
        System.out.println("\n=== 주요 플래그 ===");
        System.out.println("CASE_INSENSITIVE: 대소문자 무관");
        System.out.println("MULTILINE:        ^,$ 각 줄마다");
        System.out.println("DOTALL:           . 이 \\n 포함");
        System.out.println("COMMENTS:         공백, 주석 허용");
    }
}
```

---

## 3. 기본 패턴

### 3.1 문자 클래스

```java
public class CharacterClasses {
    public static void main(String[] args) {
        String text = "a1B2c3D4";
        
        // [abc] - a, b, c 중 하나
        System.out.println("[abc]: " + text.matches(".*[abc].*"));
        
        // [^abc] - a, b, c 제외
        System.out.println("[^abc]: " + text.matches(".*[^abc].*"));
        
        // [a-z] - 소문자
        System.out.println("[a-z]: " + text.matches(".*[a-z].*"));
        
        // [A-Z] - 대문자
        System.out.println("[A-Z]: " + text.matches(".*[A-Z].*"));
        
        // [0-9] - 숫자
        System.out.println("[0-9]: " + text.matches(".*[0-9].*"));
        
        // [a-zA-Z0-9] - 영문자 + 숫자
        System.out.println("[a-zA-Z0-9]: " + text.matches("[a-zA-Z0-9]+"));
        
        
        System.out.println("\n=== 문자 클래스 ===");
        System.out.println("[abc]:       a, b, c 중 하나");
        System.out.println("[^abc]:      a, b, c 제외");
        System.out.println("[a-z]:       소문자");
        System.out.println("[A-Z]:       대문자");
        System.out.println("[0-9]:       숫자");
    }
}
```

### 3.2 미리 정의된 클래스

```java
public class PredefinedClasses {
    public static void main(String[] args) {
        System.out.println("=== 미리 정의된 클래스 ===\n");
        
        // \\d - 숫자 [0-9]
        System.out.println("\\d (숫자):");
        System.out.println("  '123' matches: " + "123".matches("\\d+"));
        System.out.println("  'abc' matches: " + "abc".matches("\\d+"));
        
        // \\D - 숫자 아님 [^0-9]
        System.out.println("\n\\D (숫자 아님):");
        System.out.println("  'abc' matches: " + "abc".matches("\\D+"));
        
        // \\w - 단어 문자 [a-zA-Z0-9_]
        System.out.println("\n\\w (단어 문자):");
        System.out.println("  'Hello_123' matches: " + "Hello_123".matches("\\w+"));
        
        // \\W - 단어 문자 아님
        System.out.println("\n\\W (단어 문자 아님):");
        System.out.println("  '!@#' matches: " + "!@#".matches("\\W+"));
        
        // \\s - 공백 [ \\t\\n\\r\\f]
        System.out.println("\n\\s (공백):");
        System.out.println("  ' \\t\\n' matches: " + " \t\n".matches("\\s+"));
        
        // \\S - 공백 아님
        System.out.println("\n\\S (공백 아님):");
        System.out.println("  'abc' matches: " + "abc".matches("\\S+"));
        
        
        System.out.println("\n=== 정리 ===");
        System.out.println("\\d: 숫자          [0-9]");
        System.out.println("\\D: 숫자 아님     [^0-9]");
        System.out.println("\\w: 단어 문자     [a-zA-Z0-9_]");
        System.out.println("\\W: 단어 문자 아님");
        System.out.println("\\s: 공백");
        System.out.println("\\S: 공백 아님");
    }
}
```

### 3.3 경계 매칭

```java
public class BoundaryMatching {
    public static void main(String[] args) {
        // ^ - 시작
        System.out.println("^ (시작):");
        System.out.println("  'Hello' matches ^H: " + "Hello".matches("^H.*"));
        System.out.println("  'World' matches ^H: " + "World".matches("^H.*"));
        
        // $ - 끝
        System.out.println("\n$ (끝):");
        System.out.println("  'Hello' matches .*o$: " + "Hello".matches(".*o$"));
        System.out.println("  'World' matches .*o$: " + "World".matches(".*o$"));
        
        // \\b - 단어 경계
        System.out.println("\n\\b (단어 경계):");
        String text = "cat category";
        Pattern pattern = Pattern.compile("\\bcat\\b");
        Matcher matcher = pattern.matcher(text);
        System.out.println("  'cat' 단어만: ");
        while (matcher.find()) {
            System.out.println("    - " + matcher.group());
        }
        
        // \\B - 단어 경계 아님
        System.out.println("\n\\B (단어 경계 아님):");
        pattern = Pattern.compile("\\Bcat");
        matcher = pattern.matcher(text);
        while (matcher.find()) {
            System.out.println("  - " + matcher.group());
        }
        
        
        System.out.println("\n=== 경계 ===");
        System.out.println("^:  시작");
        System.out.println("$:  끝");
        System.out.println("\\b: 단어 경계");
        System.out.println("\\B: 단어 경계 아님");
    }
}
```

---

## 4. 메타 문자

### 4.1 특수 문자

```java
public class MetaCharacters {
    public static void main(String[] args) {
        System.out.println("=== 메타 문자 ===\n");
        
        // . - 임의의 문자 (개행 제외)
        System.out.println(". (임의의 문자):");
        System.out.println("  'a1c' matches a.c: " + "a1c".matches("a.c"));
        System.out.println("  'abc' matches a.c: " + "abc".matches("a.c"));
        
        // | - OR
        System.out.println("\n| (OR):");
        System.out.println("  'cat' matches cat|dog: " + "cat".matches("cat|dog"));
        System.out.println("  'dog' matches cat|dog: " + "dog".matches("cat|dog"));
        System.out.println("  'rat' matches cat|dog: " + "rat".matches("cat|dog"));
        
        // () - 그룹
        System.out.println("\n() (그룹):");
        System.out.println("  'gray' matches gr(a|e)y: " + "gray".matches("gr(a|e)y"));
        System.out.println("  'grey' matches gr(a|e)y: " + "grey".matches("gr(a|e)y"));
        
        
        System.out.println("\n=== 이스케이프 ===");
        System.out.println("특수 문자 자체를 매칭하려면 \\\\ 사용");
        System.out.println("\\\\. : . 문자");
        System.out.println("\\\\* : * 문자");
        System.out.println("\\\\+ : + 문자");
    }
}
```

### 4.2 이스케이프

```java
public class EscapeCharacters {
    public static void main(String[] args) {
        // 특수 문자 매칭
        System.out.println("=== 특수 문자 매칭 ===");
        
        String text1 = "3.14";
        System.out.println("'3.14' matches \\d+\\.\\d+: " + 
            text1.matches("\\d+\\.\\d+"));
        
        String text2 = "$100";
        System.out.println("'$100' matches \\$\\d+: " + 
            text2.matches("\\$\\d+"));
        
        String text3 = "a*b+c";
        System.out.println("'a*b+c' matches \\w\\*\\w\\+\\w: " + 
            text3.matches("\\w\\*\\w\\+\\w"));
        
        
        System.out.println("\n=== 이스케이프 필요 문자 ===");
        System.out.println(". * + ? ^ $ ( ) [ ] { } | \\");
        System.out.println("\n사용: \\\\. \\\\* \\\\+ \\\\? 등");
    }
}
```

---

## 5. 수량자

### 5.1 기본 수량자

```java
public class BasicQuantifiers {
    public static void main(String[] args) {
        System.out.println("=== 기본 수량자 ===\n");
        
        // * - 0개 이상
        System.out.println("* (0개 이상):");
        System.out.println("  '' matches a*: " + "".matches("a*"));
        System.out.println("  'aaa' matches a*: " + "aaa".matches("a*"));
        
        // + - 1개 이상
        System.out.println("\n+ (1개 이상):");
        System.out.println("  '' matches a+: " + "".matches("a+"));
        System.out.println("  'aaa' matches a+: " + "aaa".matches("a+"));
        
        // ? - 0개 또는 1개
        System.out.println("\n? (0개 또는 1개):");
        System.out.println("  '' matches a?: " + "".matches("a?"));
        System.out.println("  'a' matches a?: " + "a".matches("a?"));
        System.out.println("  'aa' matches a?: " + "aa".matches("a?"));
        
        
        System.out.println("\n=== 정리 ===");
        System.out.println("*: 0개 이상");
        System.out.println("+: 1개 이상");
        System.out.println("?: 0개 또는 1개");
    }
}
```

### 5.2 범위 수량자

```java
public class RangeQuantifiers {
    public static void main(String[] args) {
        System.out.println("=== 범위 수량자 ===\n");
        
        // {n} - 정확히 n개
        System.out.println("{n} (정확히 n개):");
        System.out.println("  'aa' matches a{2}: " + "aa".matches("a{2}"));
        System.out.println("  'aaa' matches a{2}: " + "aaa".matches("a{2}"));
        
        // {n,} - n개 이상
        System.out.println("\n{n,} (n개 이상):");
        System.out.println("  'aa' matches a{2,}: " + "aa".matches("a{2,}"));
        System.out.println("  'aaaa' matches a{2,}: " + "aaaa".matches("a{2,}"));
        
        // {n,m} - n개 이상 m개 이하
        System.out.println("\n{n,m} (n개 이상 m개 이하):");
        System.out.println("  'aa' matches a{2,4}: " + "aa".matches("a{2,4}"));
        System.out.println("  'aaa' matches a{2,4}: " + "aaa".matches("a{2,4}"));
        System.out.println("  'aaaaa' matches a{2,4}: " + "aaaaa".matches("a{2,4}"));
        
        
        // 실전 예제
        System.out.println("\n=== 실전 예제 ===");
        System.out.println("전화번호 (010-1234-5678):");
        System.out.println("  패턴: \\d{3}-\\d{4}-\\d{4}");
        System.out.println("  '010-1234-5678': " + 
            "010-1234-5678".matches("\\d{3}-\\d{4}-\\d{4}"));
    }
}
```

### 5.3 탐욕적 vs 게으른

```java
public class GreedyVsLazy {
    public static void main(String[] args) {
        String text = "<div>Hello</div><div>World</div>";
        
        // 탐욕적 (Greedy) - 최대한 매칭
        System.out.println("=== 탐욕적 (Greedy) ===");
        Pattern greedy = Pattern.compile("<div>.*</div>");
        Matcher m1 = greedy.matcher(text);
        if (m1.find()) {
            System.out.println("매칭: " + m1.group());
            // <div>Hello</div><div>World</div> (전체)
        }
        
        // 게으른 (Lazy) - 최소한 매칭
        System.out.println("\n=== 게으른 (Lazy) ===");
        Pattern lazy = Pattern.compile("<div>.*?</div>");
        Matcher m2 = lazy.matcher(text);
        while (m2.find()) {
            System.out.println("매칭: " + m2.group());
            // <div>Hello</div>
            // <div>World</div>
        }
        
        
        System.out.println("\n=== 수량자 종류 ===");
        System.out.println("탐욕적:      *  +  ?  {n,m}");
        System.out.println("게으른:      *? +? ?? {n,m}?");
        System.out.println("독점적:      *+ ++ ?+ {n,m}+");
    }
}
```

---

## 6. 그룹과 캡처

### 6.1 캡처 그룹

```java
public class CaptureGroups {
    public static void main(String[] args) {
        String text = "John Doe, 30";
        
        // 캡처 그룹
        Pattern pattern = Pattern.compile("(\\w+) (\\w+), (\\d+)");
        Matcher matcher = pattern.matcher(text);
        
        if (matcher.find()) {
            System.out.println("=== 캡처 그룹 ===");
            System.out.println("전체: " + matcher.group(0));
            System.out.println("이름: " + matcher.group(1));
            System.out.println("성: " + matcher.group(2));
            System.out.println("나이: " + matcher.group(3));
        }
        
        
        // 날짜 파싱
        System.out.println("\n=== 날짜 파싱 ===");
        String date = "2024-12-17";
        pattern = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
        matcher = pattern.matcher(date);
        
        if (matcher.find()) {
            System.out.println("연도: " + matcher.group(1));
            System.out.println("월: " + matcher.group(2));
            System.out.println("일: " + matcher.group(3));
        }
    }
}
```

### 6.2 비캡처 그룹

```java
public class NonCapturingGroups {
    public static void main(String[] args) {
        String text = "http://example.com";
        
        // 캡처 그룹
        System.out.println("=== 캡처 그룹 ===");
        Pattern p1 = Pattern.compile("(https?)://(.+)");
        Matcher m1 = p1.matcher(text);
        if (m1.find()) {
            System.out.println("그룹 수: " + m1.groupCount());
            System.out.println("프로토콜: " + m1.group(1));
            System.out.println("도메인: " + m1.group(2));
        }
        
        // 비캡처 그룹 (?:)
        System.out.println("\n=== 비캡처 그룹 ===");
        Pattern p2 = Pattern.compile("(?:https?)://(.+)");
        Matcher m2 = p2.matcher(text);
        if (m2.find()) {
            System.out.println("그룹 수: " + m2.groupCount());
            System.out.println("도메인: " + m2.group(1));
        }
        
        
        System.out.println("\n=== 용도 ===");
        System.out.println("비캡처 그룹 (?:):");
        System.out.println("- 그룹화만 필요");
        System.out.println("- 메모리 절약");
        System.out.println("- 성능 향상");
    }
}
```

### 6.3 명명된 그룹

```java
public class NamedGroups {
    public static void main(String[] args) {
        String text = "John Doe, 30";
        
        // 명명된 그룹 (Java 7+)
        Pattern pattern = Pattern.compile(
            "(?<first>\\w+) (?<last>\\w+), (?<age>\\d+)"
        );
        Matcher matcher = pattern.matcher(text);
        
        if (matcher.find()) {
            System.out.println("=== 명명된 그룹 ===");
            System.out.println("이름: " + matcher.group("first"));
            System.out.println("성: " + matcher.group("last"));
            System.out.println("나이: " + matcher.group("age"));
        }
        
        
        // 이메일 파싱
        System.out.println("\n=== 이메일 파싱 ===");
        String email = "john@example.com";
        pattern = Pattern.compile(
            "(?<user>[^@]+)@(?<domain>.+)"
        );
        matcher = pattern.matcher(email);
        
        if (matcher.find()) {
            System.out.println("사용자: " + matcher.group("user"));
            System.out.println("도메인: " + matcher.group("domain"));
        }
    }
}
```

### 6.4 역참조

```java
public class Backreferences {
    public static void main(String[] args) {
        // 반복 단어 찾기
        System.out.println("=== 반복 단어 ===");
        String text = "hello hello world world";
        
        Pattern pattern = Pattern.compile("(\\w+) \\1");
        Matcher matcher = pattern.matcher(text);
        
        while (matcher.find()) {
            System.out.println("반복: " + matcher.group());
        }
        
        
        // HTML 태그 매칭
        System.out.println("\n=== HTML 태그 ===");
        String html = "<div>content</div><span>text</span>";
        
        pattern = Pattern.compile("<(\\w+)>.*?</\\1>");
        matcher = pattern.matcher(html);
        
        while (matcher.find()) {
            System.out.println("태그: " + matcher.group());
        }
        
        
        System.out.println("\n=== 역참조 ===");
        System.out.println("\\1: 첫 번째 그룹 참조");
        System.out.println("\\2: 두 번째 그룹 참조");
        System.out.println("\\k<name>: 명명된 그룹 참조");
    }
}
```

---

## 7. 문자열 메서드

### 7.1 matches

```java
public class StringMatches {
    public static void main(String[] args) {
        // matches - 전체 매칭
        System.out.println("=== matches ===");
        
        String email = "test@example.com";
        String pattern = "[\\w.]+@[\\w.]+\\.[a-z]+";
        
        System.out.println("이메일 검증: " + email.matches(pattern));
        
        
        // 숫자 검증
        System.out.println("\n=== 숫자 검증 ===");
        System.out.println("'123' is number: " + "123".matches("\\d+"));
        System.out.println("'12a' is number: " + "12a".matches("\\d+"));
        
        
        // 전화번호 검증
        System.out.println("\n=== 전화번호 ===");
        String phone = "010-1234-5678";
        String phonePattern = "\\d{3}-\\d{4}-\\d{4}";
        System.out.println("전화번호 검증: " + phone.matches(phonePattern));
    }
}
```

### 7.2 split

```java
public class StringSplit {
    public static void main(String[] args) {
        // 기본 분리
        System.out.println("=== 기본 분리 ===");
        String text = "apple,banana,cherry";
        String[] fruits = text.split(",");
        System.out.println(Arrays.toString(fruits));
        
        
        // 정규표현식으로 분리
        System.out.println("\n=== 정규표현식 분리 ===");
        String text2 = "apple  banana   cherry";  // 공백 여러 개
        String[] fruits2 = text2.split("\\s+");
        System.out.println(Arrays.toString(fruits2));
        
        
        // 여러 구분자
        System.out.println("\n=== 여러 구분자 ===");
        String text3 = "apple,banana;cherry:date";
        String[] fruits3 = text3.split("[,;:]");
        System.out.println(Arrays.toString(fruits3));
        
        
        // 제한 개수
        System.out.println("\n=== 제한 개수 ===");
        String text4 = "a:b:c:d:e";
        String[] parts = text4.split(":", 3);
        System.out.println(Arrays.toString(parts));  // [a, b, c:d:e]
    }
}
```

### 7.3 replace / replaceAll

```java
public class StringReplace {
    public static void main(String[] args) {
        String text = "Hello World 123";
        
        // replace - 단순 치환
        System.out.println("=== replace ===");
        String r1 = text.replace("World", "Java");
        System.out.println(r1);
        
        
        // replaceAll - 정규표현식 치환
        System.out.println("\n=== replaceAll ===");
        
        // 숫자 제거
        String r2 = text.replaceAll("\\d+", "");
        System.out.println("숫자 제거: " + r2);
        
        // 공백 제거
        String r3 = text.replaceAll("\\s+", "");
        System.out.println("공백 제거: " + r3);
        
        // 대소문자 변환
        String r4 = text.replaceAll("[a-z]", "X");
        System.out.println("소문자 X로: " + r4);
        
        
        // replaceFirst - 첫 번째만
        System.out.println("\n=== replaceFirst ===");
        String text2 = "cat bat cat";
        String r5 = text2.replaceFirst("cat", "dog");
        System.out.println(r5);  // dog bat cat
    }
}
```

---

## 8. 실전 패턴

### 8.1 이메일 검증

```java
public class EmailValidation {
    public static boolean isValidEmail(String email) {
        String pattern = "^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$";
        return email.matches(pattern);
    }
    
    public static void main(String[] args) {
        String[] emails = {
            "test@example.com",      // OK
            "user.name@domain.co.kr", // OK
            "invalid@",              // NG
            "@invalid.com",          // NG
            "no-at-sign.com"         // NG
        };
        
        System.out.println("=== 이메일 검증 ===");
        for (String email : emails) {
            System.out.println(email + ": " + isValidEmail(email));
        }
        
        
        System.out.println("\n=== 패턴 설명 ===");
        System.out.println("^[\\w.-]+     : 사용자명 (영문,숫자,._-)");
        System.out.println("@             : @");
        System.out.println("[\\w.-]+      : 도메인명");
        System.out.println("\\.[a-zA-Z]{2,}: .com, .kr 등");
    }
}
```

### 8.2 전화번호 검증

```java
public class PhoneValidation {
    public static boolean isValidPhone(String phone) {
        // 010-1234-5678 or 01012345678
        String pattern = "^\\d{3}-?\\d{4}-?\\d{4}$";
        return phone.matches(pattern);
    }
    
    public static String formatPhone(String phone) {
        // 숫자만 추출
        String numbers = phone.replaceAll("[^0-9]", "");
        
        // 포맷팅
        if (numbers.length() == 11) {
            return numbers.replaceAll("(\\d{3})(\\d{4})(\\d{4})", "$1-$2-$3");
        }
        return phone;
    }
    
    public static void main(String[] args) {
        String[] phones = {
            "010-1234-5678",  // OK
            "01012345678",    // OK
            "010-123-4567",   // NG
            "0101234567"      // NG
        };
        
        System.out.println("=== 전화번호 검증 ===");
        for (String phone : phones) {
            System.out.println(phone + ": " + isValidPhone(phone));
        }
        
        
        System.out.println("\n=== 포맷팅 ===");
        System.out.println("01012345678 → " + formatPhone("01012345678"));
        System.out.println("010 1234 5678 → " + formatPhone("010 1234 5678"));
    }
}
```

### 8.3 URL 파싱

```java
public class URLParsing {
    public static void parseURL(String url) {
        String pattern = "^(https?)://([^:/]+)(:(\\d+))?(/.*)?$";
        Pattern p = Pattern.compile(pattern);
        Matcher m = p.matcher(url);
        
        if (m.find()) {
            System.out.println("=== URL 파싱 ===");
            System.out.println("URL: " + url);
            System.out.println("프로토콜: " + m.group(1));
            System.out.println("호스트: " + m.group(2));
            System.out.println("포트: " + (m.group(4) != null ? m.group(4) : "default"));
            System.out.println("경로: " + (m.group(5) != null ? m.group(5) : "/"));
        }
    }
    
    public static void main(String[] args) {
        parseURL("https://example.com:8080/path/to/resource");
        System.out.println();
        parseURL("http://example.com/index.html");
    }
}
```

### 8.4 HTML 태그 제거

```java
public class HTMLTagRemoval {
    public static String removeHTMLTags(String html) {
        // HTML 태그 제거
        return html.replaceAll("<[^>]+>", "");
    }
    
    public static String extractText(String html) {
        // 태그 제거 + 공백 정리
        return html.replaceAll("<[^>]+>", "")
                   .replaceAll("\\s+", " ")
                   .trim();
    }
    
    public static void main(String[] args) {
        String html = "<div>Hello <span>World</span>!</div>";
        
        System.out.println("=== HTML 태그 제거 ===");
        System.out.println("원본: " + html);
        System.out.println("제거: " + removeHTMLTags(html));
        System.out.println("정리: " + extractText(html));
        
        
        // 복잡한 HTML
        String html2 = "<p>First paragraph.</p>\n<p>Second paragraph.</p>";
        System.out.println("\n원본:\n" + html2);
        System.out.println("추출: " + extractText(html2));
    }
}
```

### 8.5 비밀번호 검증

```java
public class PasswordValidation {
    public static boolean isStrongPassword(String password) {
        // 8자 이상, 대소문자, 숫자, 특수문자 포함
        boolean lengthOk = password.matches(".{8,}");
        boolean hasUpper = password.matches(".*[A-Z].*");
        boolean hasLower = password.matches(".*[a-z].*");
        boolean hasDigit = password.matches(".*\\d.*");
        boolean hasSpecial = password.matches(".*[!@#$%^&*].*");
        
        return lengthOk && hasUpper && hasLower && hasDigit && hasSpecial;
    }
    
    public static String checkPassword(String password) {
        if (password.length() < 8) {
            return "8자 이상 필요";
        }
        if (!password.matches(".*[A-Z].*")) {
            return "대문자 필요";
        }
        if (!password.matches(".*[a-z].*")) {
            return "소문자 필요";
        }
        if (!password.matches(".*\\d.*")) {
            return "숫자 필요";
        }
        if (!password.matches(".*[!@#$%^&*].*")) {
            return "특수문자 필요";
        }
        return "강력함";
    }
    
    public static void main(String[] args) {
        String[] passwords = {
            "Abc123!@",      // OK
            "password",      // NG
            "PASSWORD123",   // NG
            "Pass123"        // NG
        };
        
        System.out.println("=== 비밀번호 검증 ===");
        for (String pwd : passwords) {
            System.out.println(pwd + ": " + checkPassword(pwd));
        }
    }
}
```

---

## 9. 고급 기법

### 9.1 전방 탐색

```java
public class Lookahead {
    public static void main(String[] args) {
        String text = "apple123banana456cherry";
        
        // 긍정 전방 탐색 (?=)
        System.out.println("=== 긍정 전방 탐색 (?=) ===");
        Pattern p1 = Pattern.compile("\\w+(?=\\d)");
        Matcher m1 = p1.matcher(text);
        while (m1.find()) {
            System.out.println("숫자 앞 단어: " + m1.group());
        }
        
        
        // 부정 전방 탐색 (?!)
        System.out.println("\n=== 부정 전방 탐색 (?!) ===");
        Pattern p2 = Pattern.compile("\\w+(?!\\d)");
        Matcher m2 = p2.matcher(text);
        while (m2.find()) {
            System.out.println("숫자 없는 단어: " + m2.group());
        }
        
        
        System.out.println("\n=== 전방 탐색 ===");
        System.out.println("(?=pattern):  패턴 있으면 매칭");
        System.out.println("(?!pattern):  패턴 없으면 매칭");
    }
}
```

### 9.2 후방 탐색

```java
public class Lookbehind {
    public static void main(String[] args) {
        String text = "$100 €200 ¥300";
        
        // 긍정 후방 탐색 (?<=)
        System.out.println("=== 긍정 후방 탐색 (?<=) ===");
        Pattern p1 = Pattern.compile("(?<=\\$)\\d+");
        Matcher m1 = p1.matcher(text);
        while (m1.find()) {
            System.out.println("달러 금액: " + m1.group());
        }
        
        
        // 부정 후방 탐색 (?<!)
        System.out.println("\n=== 부정 후방 탐색 (?<!) ===");
        Pattern p2 = Pattern.compile("(?<!\\$)\\d+");
        Matcher m2 = p2.matcher(text);
        while (m2.find()) {
            System.out.println("달러 아닌 금액: " + m2.group());
        }
        
        
        System.out.println("\n=== 후방 탐색 ===");
        System.out.println("(?<=pattern): 패턴 뒤 매칭");
        System.out.println("(?<!pattern): 패턴 없으면 매칭");
    }
}
```

### 9.3 조건부 패턴

```java
public class ConditionalPattern {
    public static void main(String[] args) {
        // 주석 제거 (// 또는 /* */)
        String code = "int x = 10; // comment\nint y = 20; /* block */";
        
        System.out.println("=== 원본 코드 ===");
        System.out.println(code);
        
        // // 주석 제거
        String result1 = code.replaceAll("//.*", "");
        System.out.println("\n// 주석 제거:");
        System.out.println(result1);
        
        // /* */ 주석 제거
        String result2 = result1.replaceAll("/\\*.*?\\*/", "");
        System.out.println("\n/* */ 주석 제거:");
        System.out.println(result2);
    }
}
```

---

## 10. 실전 연습 문제

### 문제 1: 주민등록번호 검증

```java
// 주민등록번호 형식 검증 (123456-1234567)
public class Problem1 {
    public static boolean isValidSSN(String ssn) {
        // 구현
        return false;
    }
    
    public static void main(String[] args) {
        System.out.println(isValidSSN("123456-1234567"));  // true
        System.out.println(isValidSSN("1234567890123"));   // false
        System.out.println(isValidSSN("123456-123456"));   // false
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static boolean isValidSSN(String ssn) {
    return ssn.matches("\\d{6}-\\d{7}");
}
```
</details>

---

### 문제 2: 신용카드 번호 마스킹

```java
// 신용카드 번호 마스킹 (1234-5678-9012-3456 → ****-****-****-3456)
public class Problem2 {
    public static String maskCardNumber(String cardNumber) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        System.out.println(maskCardNumber("1234-5678-9012-3456"));
        // ****-****-****-3456
        
        System.out.println(maskCardNumber("1234567890123456"));
        // ************3456
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static String maskCardNumber(String cardNumber) {
    // 하이픈 있는 경우
    if (cardNumber.contains("-")) {
        return cardNumber.replaceAll("\\d(?=\\d{4})", "*");
    }
    // 하이픈 없는 경우
    return cardNumber.replaceAll("\\d(?=\\d{4})", "*");
}

// 또는
public static String maskCardNumber2(String cardNumber) {
    return cardNumber.replaceAll(".(?=.{4})", "*");
}
```
</details>

---

### 문제 3: 날짜 형식 변환

```java
// 날짜 형식 변환 (2024-12-17 → 17/12/2024)
public class Problem3 {
    public static String convertDateFormat(String date) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        System.out.println(convertDateFormat("2024-12-17"));
        // 17/12/2024
        
        System.out.println(convertDateFormat("2024-01-05"));
        // 05/01/2024
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static String convertDateFormat(String date) {
    return date.replaceAll("(\\d{4})-(\\d{2})-(\\d{2})", "$3/$2/$1");
}

// 또는 Matcher 사용
public static String convertDateFormat2(String date) {
    Pattern pattern = Pattern.compile("(\\d{4})-(\\d{2})-(\\d{2})");
    Matcher matcher = pattern.matcher(date);
    
    if (matcher.find()) {
        return matcher.group(3) + "/" + matcher.group(2) + "/" + matcher.group(1);
    }
    return date;
}
```
</details>

---

## 📌 핵심 정리

### 기본 패턴
```
.        : 임의의 문자
\d       : 숫자 [0-9]
\w       : 단어 문자 [a-zA-Z0-9_]
\s       : 공백
^        : 시작
$        : 끝
```

### 수량자
```
*        : 0개 이상
+        : 1개 이상
?        : 0개 또는 1개
{n}      : 정확히 n개
{n,}     : n개 이상
{n,m}    : n개 이상 m개 이하
```

### 그룹
```
(...)         : 캡처 그룹
(?:...)       : 비캡처 그룹
(?<name>...)  : 명명된 그룹
\1            : 역참조
```

### 전방/후방 탐색
```
(?=...)   : 긍정 전방 탐색
(?!...)   : 부정 전방 탐색
(?<=...)  : 긍정 후방 탐색
(?<!...)  : 부정 후방 탐색
```

### 주요 메서드
```java
// Pattern & Matcher
Pattern.compile(regex)
pattern.matcher(text)
matcher.find()
matcher.group()

// String
string.matches(regex)
string.split(regex)
string.replaceAll(regex, replacement)
```

### 실전 패턴
```java
// 이메일
"^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$"

// 전화번호
"^\\d{3}-?\\d{4}-?\\d{4}$"

// URL
"^(https?)://([^:/]+)(:(\\d+))?(/.*)?$"

// 비밀번호 (8자 이상, 대소문자+숫자+특수문자)
".{8,}" && ".*[A-Z].*" && ".*[a-z].*" && ".*\\d.*" && ".*[!@#$%^&*].*"
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 03. Optional](./Util-03-Optional.md)**

</div>
