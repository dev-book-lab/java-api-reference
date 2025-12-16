# String 04. 변환과 치환

> 문자열을 변환하고 치환하는 모든 방법  
> Java API Reference

---

## 📑 목차

1. [대소문자 변환](#1-대소문자-변환)
2. [replace() - 치환](#2-replace---치환)
3. [trim() / strip() - 공백 제거](#3-trim--strip---공백-제거)
4. [기타 변환 메서드](#4-기타-변환-메서드)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. 대소문자 변환

### 1.1 toUpperCase() / toLowerCase()

```java
public class CaseConversion {
    public static void main(String[] args) {
        String str = "Hello World";
        
        // 대문자 변환
        String upper = str.toUpperCase();
        System.out.println(upper);  // "HELLO WORLD"
        
        // 소문자 변환
        String lower = str.toLowerCase();
        System.out.println(lower);  // "hello world"
        
        // 원본은 변경 안 됨 (불변성!)
        System.out.println(str);    // "Hello World"
    }
}
```

**주의: 원본 변경 안 됨!**
```java
String str = "Hello";
str.toUpperCase();  // ❌ 결과를 받지 않음!
System.out.println(str);  // "Hello" (그대로)

String upper = str.toUpperCase();  // ✅ 새 객체 받기
System.out.println(upper);  // "HELLO"
```

### 1.2 로케일(Locale) 지정

```java
import java.util.Locale;

public class LocaleCase {
    public static void main(String[] args) {
        // 터키어의 특수한 경우
        String str = "title";
        
        // 기본 로케일
        System.out.println(str.toUpperCase());  // "TITLE"
        
        // 터키어 로케일
        System.out.println(str.toUpperCase(Locale.forLanguageTag("tr")));
        // 터키어에서 'i'는 'İ'로 변환 (점 있는 대문자 I)
        
        
        // 대부분의 경우 기본 메서드 사용
        String normal = "Hello".toUpperCase();  // 일반적
        String localed = "Hello".toUpperCase(Locale.US);  // 명시적
    }
}
```

### 1.3 실전 활용

#### 대소문자 무시 비교
```java
public class CaseInsensitiveCompare {
    public static void main(String[] args) {
        String input = "HELLO";
        
        // 방법 1: equalsIgnoreCase() 사용 (추천!)
        if (input.equalsIgnoreCase("hello")) {
            System.out.println("같음");
        }
        
        // 방법 2: toLowerCase() 사용
        if (input.toLowerCase().equals("hello")) {
            System.out.println("같음");
        }
        
        // 방법 1이 더 효율적 (새 객체 생성 안 함)
    }
}
```

#### 첫 글자만 대문자 (Title Case)
```java
public class TitleCase {
    public static String toTitleCase(String str) {
        if (str == null || str.isEmpty()) {
            return str;
        }
        
        return str.substring(0, 1).toUpperCase() 
             + str.substring(1).toLowerCase();
    }
    
    public static void main(String[] args) {
        System.out.println(toTitleCase("hello"));   // "Hello"
        System.out.println(toTitleCase("WORLD"));   // "World"
        System.out.println(toTitleCase("jAvA"));    // "Java"
    }
}
```

#### 각 단어의 첫 글자 대문자
```java
public class CapitalizeWords {
    public static String capitalizeWords(String str) {
        if (str == null || str.isEmpty()) {
            return str;
        }
        
        String[] words = str.split(" ");
        StringBuilder result = new StringBuilder();
        
        for (int i = 0; i < words.length; i++) {
            if (words[i].length() > 0) {
                result.append(words[i].substring(0, 1).toUpperCase());
                result.append(words[i].substring(1).toLowerCase());
            }
            
            if (i < words.length - 1) {
                result.append(" ");
            }
        }
        
        return result.toString();
    }
    
    public static void main(String[] args) {
        System.out.println(capitalizeWords("hello world"));
        // "Hello World"
        
        System.out.println(capitalizeWords("java programming language"));
        // "Java Programming Language"
    }
}
```

#### 토글 (대소문자 반전)
```java
public class ToggleCase {
    public static String toggleCase(String str) {
        StringBuilder result = new StringBuilder();
        
        for (char ch : str.toCharArray()) {
            if (Character.isUpperCase(ch)) {
                result.append(Character.toLowerCase(ch));
            } else if (Character.isLowerCase(ch)) {
                result.append(Character.toUpperCase(ch));
            } else {
                result.append(ch);
            }
        }
        
        return result.toString();
    }
    
    public static void main(String[] args) {
        System.out.println(toggleCase("Hello World"));
        // "hELLO wORLD"
    }
}
```

---

## 2. replace() - 치환

### 2.1 replace() - 문자/문자열 치환

```java
public class ReplaceBasic {
    public static void main(String[] args) {
        String str = "Hello World";
        
        // 문자 치환
        String r1 = str.replace('o', 'a');
        System.out.println(r1);  // "Hella Warld"
        
        // 문자열 치환
        String r2 = str.replace("World", "Java");
        System.out.println(r2);  // "Hello Java"
        
        // 모든 일치 항목 치환
        String text = "banana";
        String r3 = text.replace('a', 'o');
        System.out.println(r3);  // "bonono"
        
        
        // 원본은 불변!
        System.out.println(str);  // "Hello World"
    }
}
```

### 2.2 replaceAll() - 정규표현식

```java
public class ReplaceAllTest {
    public static void main(String[] args) {
        String str = "Hello123World456";
        
        // 모든 숫자 제거
        String r1 = str.replaceAll("\\d+", "");
        System.out.println(r1);  // "HelloWorld"
        
        // 모든 숫자를 *로
        String r2 = str.replaceAll("\\d", "*");
        System.out.println(r2);  // "Hello***World***"
        
        // 연속된 공백을 하나로
        String text = "Hello    World";
        String r3 = text.replaceAll("\\s+", " ");
        System.out.println(r3);  // "Hello World"
        
        
        // 특수문자 제거
        String str2 = "Hello@World#2024!";
        String r4 = str2.replaceAll("[^a-zA-Z0-9]", "");
        System.out.println(r4);  // "HelloWorld2024"
    }
}
```

### 2.3 replaceFirst() - 첫 번째만 치환

```java
public class ReplaceFirstTest {
    public static void main(String[] args) {
        String str = "banana";
        
        // replace(): 모든 'a' 치환
        String all = str.replace('a', 'o');
        System.out.println(all);  // "bonono"
        
        // replaceFirst(): 첫 'a'만 치환
        String first = str.replaceFirst("a", "o");
        System.out.println(first);  // "bonana"
        
        
        // 정규식으로 첫 숫자만 제거
        String text = "abc123def456";
        String result = text.replaceFirst("\\d+", "");
        System.out.println(result);  // "abcdef456"
    }
}
```

### 2.4 replace vs replaceAll 차이

```java
public class ReplaceComparison {
    public static void main(String[] args) {
        String str = "a.b.c";
        
        // replace(): 리터럴 문자열
        String r1 = str.replace(".", "-");
        System.out.println(r1);  // "a-b-c"
        
        // replaceAll(): 정규표현식
        // '.'은 정규식에서 "모든 문자"를 의미!
        String r2 = str.replaceAll(".", "-");
        System.out.println(r2);  // "-----" (모든 문자 치환)
        
        // replaceAll()에서 리터럴 '.'을 쓰려면 이스케이프
        String r3 = str.replaceAll("\\.", "-");
        System.out.println(r3);  // "a-b-c"
        
        
        // 결론: 단순 치환은 replace() 사용!
    }
}
```

**핵심 차이:**
| 메서드 | 파라미터 타입 | 성능 | 사용 시기 |
|---|---|---|---|
| `replace()` | 리터럴 문자열 | 빠름 | 단순 치환 |
| `replaceAll()` | 정규표현식 | 느림 | 패턴 매칭 필요 |
| `replaceFirst()` | 정규표현식 | 느림 | 첫 번째만 패턴 매칭 |

### 2.5 실전 활용

#### HTML 특수문자 이스케이프
```java
public class HtmlEscape {
    public static String escapeHtml(String str) {
        return str.replace("&", "&amp;")
                  .replace("<", "&lt;")
                  .replace(">", "&gt;")
                  .replace("\"", "&quot;")
                  .replace("'", "&#39;");
    }
    
    public static void main(String[] args) {
        String html = "<script>alert('XSS')</script>";
        System.out.println(escapeHtml(html));
        // &lt;script&gt;alert(&#39;XSS&#39;)&lt;/script&gt;
    }
}
```

#### 전화번호 포맷 정리
```java
public class PhoneFormat {
    public static String cleanPhone(String phone) {
        // 숫자만 남기기
        return phone.replaceAll("[^0-9]", "");
    }
    
    public static void main(String[] args) {
        System.out.println(cleanPhone("010-1234-5678"));
        // "01012345678"
        
        System.out.println(cleanPhone("(02) 123-4567"));
        // "021234567"
        
        System.out.println(cleanPhone("+82-10-1234-5678"));
        // "821012345678"
    }
}
```

#### 민감정보 마스킹
```java
public class MaskData {
    public static String maskEmail(String email) {
        // "user@example.com" → "u***@example.com"
        int atIndex = email.indexOf('@');
        if (atIndex <= 1) return email;
        
        return email.charAt(0) 
             + email.substring(1, atIndex).replaceAll(".", "*")
             + email.substring(atIndex);
    }
    
    public static String maskPhone(String phone) {
        // "010-1234-5678" → "010-****-5678"
        return phone.replaceAll("(\\d{3})-(\\d{4})-(\\d{4})", "$1-****-$3");
    }
    
    public static void main(String[] args) {
        System.out.println(maskEmail("user@example.com"));
        // "u***@example.com"
        
        System.out.println(maskPhone("010-1234-5678"));
        // "010-****-5678"
    }
}
```

#### SQL 인젝션 방지 (기본)
```java
public class SqlEscape {
    public static String escapeSql(String str) {
        // 작은따옴표 이스케이프
        return str.replace("'", "''");
    }
    
    public static void main(String[] args) {
        String input = "O'Reilly";
        System.out.println(escapeSql(input));
        // "O''Reilly"
        
        // 실무에서는 PreparedStatement 사용!
    }
}
```

---

## 3. trim() / strip() - 공백 제거

### 3.1 trim() - 앞뒤 공백 제거

```java
public class TrimTest {
    public static void main(String[] args) {
        String str = "  Hello World  ";
        
        String trimmed = str.trim();
        System.out.println("[" + trimmed + "]");  // "[Hello World]"
        
        // 원본은 불변
        System.out.println("[" + str + "]");  // "[  Hello World  ]"
        
        
        // 중간 공백은 제거 안 됨
        String text = "  Hello   World  ";
        System.out.println("[" + text.trim() + "]");
        // "[Hello   World]" (중간 공백 유지)
    }
}
```

**trim()이 제거하는 것:**
- ASCII 공백 문자 (코드 ≤ 32)
- 스페이스, 탭, 줄바꿈 등

### 3.2 strip() - 유니코드 공백 제거 (Java 11+)

```java
public class StripTest {
    public static void main(String[] args) {
        // 유니코드 공백 (U+2003)
        String str = "\u2003Hello\u2003";
        
        // trim(): 제거 못함
        System.out.println("[" + str.trim() + "]");
        // "[ Hello ]" (공백 남음)
        
        // strip(): 제거됨!
        System.out.println("[" + str.strip() + "]");
        // "[Hello]"
        
        
        // stripLeading(): 앞쪽만
        System.out.println("[" + "  Hello  ".stripLeading() + "]");
        // "[Hello  ]"
        
        // stripTrailing(): 뒤쪽만
        System.out.println("[" + "  Hello  ".stripTrailing() + "]");
        // "[  Hello]"
    }
}
```

### 3.3 실전 활용

#### 사용자 입력 정리
```java
public class InputCleaning {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        
        System.out.print("이름 입력: ");
        String name = sc.nextLine().trim();  // 앞뒤 공백 제거
        
        if (name.isEmpty()) {
            System.out.println("이름을 입력하세요!");
        } else {
            System.out.println("안녕하세요, " + name + "님!");
        }
    }
}
```

#### CSV 파싱
```java
public class CsvParsing {
    public static void main(String[] args) {
        String csv = "John , 25 , USA ";
        
        String[] parts = csv.split(",");
        for (int i = 0; i < parts.length; i++) {
            parts[i] = parts[i].trim();  // 각 항목의 공백 제거
        }
        
        System.out.println(Arrays.toString(parts));
        // [John, 25, USA]
    }
}
```

#### 빈 줄 제거
```java
public class RemoveBlankLines {
    public static List<String> removeBlankLines(String text) {
        List<String> lines = new ArrayList<>();
        
        for (String line : text.split("\n")) {
            String trimmed = line.trim();
            if (!trimmed.isEmpty()) {
                lines.add(trimmed);
            }
        }
        
        return lines;
    }
    
    public static void main(String[] args) {
        String text = "Line 1\n\n  \nLine 2\n\nLine 3";
        List<String> lines = removeBlankLines(text);
        System.out.println(lines);
        // [Line 1, Line 2, Line 3]
    }
}
```

---

## 4. 기타 변환 메서드

### 4.1 repeat() - 반복 (Java 11+)

```java
public class RepeatTest {
    public static void main(String[] args) {
        String str = "Ha";
        
        String repeated = str.repeat(3);
        System.out.println(repeated);  // "HaHaHa"
        
        // 0번 반복
        System.out.println(str.repeat(0));  // ""
        
        
        // 실전 예시: 구분선 생성
        System.out.println("=".repeat(50));
        // ==================================================
        
        // 들여쓰기
        int level = 3;
        System.out.println("  ".repeat(level) + "Indented text");
        //       Indented text (6칸 들여쓰기)
    }
}
```

### 4.2 concat() - 연결

```java
public class ConcatTest {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = " World";
        
        // concat() 사용
        String result1 = s1.concat(s2);
        System.out.println(result1);  // "Hello World"
        
        // + 연산자 사용 (더 일반적)
        String result2 = s1 + s2;
        System.out.println(result2);  // "Hello World"
        
        
        // 체이닝 가능
        String result3 = "Hello".concat(" ").concat("World");
        System.out.println(result3);  // "Hello World"
        
        
        // 주의: null 연결 시
        String s3 = "Hello";
        String s4 = null;
        
        // + 연산자: null을 "null"로 변환
        System.out.println(s3 + s4);  // "Hellonull"
        
        // concat(): NullPointerException!
        try {
            System.out.println(s3.concat(s4));
        } catch (NullPointerException e) {
            System.out.println("NPE 발생!");
        }
    }
}
```

### 4.3 indent() - 들여쓰기 (Java 12+)

```java
public class IndentTest {
    public static void main(String[] args) {
        String text = "Hello\nWorld";
        
        // 4칸 들여쓰기
        String indented = text.indent(4);
        System.out.println(indented);
        //     Hello
        //     World
        
        // 음수로 들여쓰기 제거
        String dedented = indented.indent(-2);
        System.out.println(dedented);
        //   Hello
        //   World
    }
}
```

### 4.4 intern() - String Pool 등록

```java
public class InternReview {
    public static void main(String[] args) {
        String s1 = new String("Hello");  // Heap
        String s2 = new String("Hello");  // Heap
        
        System.out.println(s1 == s2);  // false (다른 객체)
        
        // Pool에 등록
        String s3 = s1.intern();
        String s4 = s2.intern();
        
        System.out.println(s3 == s4);  // true (같은 Pool 객체)
        
        
        // 리터럴과 비교
        String s5 = "Hello";
        System.out.println(s3 == s5);  // true (Pool에서 가져옴)
    }
}
```

### 4.5 transform() - 변환 함수 적용 (Java 12+)

```java
public class TransformTest {
    public static void main(String[] args) {
        String result = "hello"
            .transform(String::toUpperCase)
            .transform(s -> s + " WORLD");
        
        System.out.println(result);  // "HELLO WORLD"
        
        
        // 람다로 커스텀 변환
        String formatted = "  text  "
            .transform(String::trim)
            .transform(s -> "[" + s + "]");
        
        System.out.println(formatted);  // "[text]"
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 문자열 정규화

```java
// 사용자 입력을 정규화하는 메서드 작성
// - 앞뒤 공백 제거
// - 중간의 연속된 공백을 하나로
// - 소문자로 변환
public class Problem1 {
    public static String normalize(String str) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(normalize("  Hello    World  "));
        // "hello world"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static String normalize(String str) {
        return str.trim()                      // 앞뒤 공백 제거
                  .replaceAll("\\s+", " ")     // 연속 공백을 하나로
                  .toLowerCase();              // 소문자 변환
    }
    
    // 또는 단계별로
    public static String normalize2(String str) {
        String trimmed = str.trim();
        String singleSpace = trimmed.replaceAll("\\s+", " ");
        String lower = singleSpace.toLowerCase();
        return lower;
    }
}
```
</details>

---

### 문제 2: URL 슬러그 생성

```java
// 제목을 URL 친화적인 슬러그로 변환
// - 소문자 변환
// - 공백을 하이픈(-)으로
// - 특수문자 제거
// 예: "Hello World!" → "hello-world"
public class Problem2 {
    public static String createSlug(String title) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(createSlug("Hello World!"));
        // "hello-world"
        
        System.out.println(createSlug("Java Programming 101"));
        // "java-programming-101"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static String createSlug(String title) {
        return title.toLowerCase()              // 소문자
                    .trim()                     // 앞뒤 공백 제거
                    .replaceAll("\\s+", "-")    // 공백을 하이픈으로
                    .replaceAll("[^a-z0-9-]", "")  // 영문자, 숫자, 하이픈만
                    .replaceAll("-+", "-")      // 연속 하이픈을 하나로
                    .replaceAll("^-|-$", "");   // 앞뒤 하이픈 제거
    }
}
```
</details>

---

### 문제 3: 센서티브한 단어 필터링

```java
// 금지어를 ***로 치환
public class Problem3 {
    public static String filterBadWords(String text, String[] badWords) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        String text = "This is bad and ugly content";
        String[] badWords = {"bad", "ugly"};
        
        System.out.println(filterBadWords(text, badWords));
        // "This is *** and ***** content"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static String filterBadWords(String text, String[] badWords) {
        String result = text;
        
        for (String word : badWords) {
            String stars = "*".repeat(word.length());
            
            // 대소문자 무시하고 치환
            result = result.replaceAll("(?i)" + word, stars);
        }
        
        return result;
    }
    
    // 정확한 단어만 매칭 (부분 문자열 제외)
    public static String filterBadWords2(String text, String[] badWords) {
        String result = text;
        
        for (String word : badWords) {
            String stars = "*".repeat(word.length());
            
            // \b: 단어 경계
            result = result.replaceAll("(?i)\\b" + word + "\\b", stars);
        }
        
        return result;
    }
}
```
</details>

---

### 문제 4: 카멜케이스 ↔ 스네이크케이스 변환

```java
// camelCase를 snake_case로, snake_case를 camelCase로 변환
public class Problem4 {
    public static String toSnakeCase(String camelCase) {
        // 여기에 코드 작성
    }
    
    public static String toCamelCase(String snakeCase) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(toSnakeCase("helloWorld"));
        // "hello_world"
        
        System.out.println(toCamelCase("hello_world"));
        // "helloWorld"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem4 {
    public static String toSnakeCase(String camelCase) {
        // 대문자 앞에 언더스코어 추가
        return camelCase.replaceAll("([a-z])([A-Z])", "$1_$2")
                        .toLowerCase();
    }
    
    public static String toCamelCase(String snakeCase) {
        StringBuilder result = new StringBuilder();
        boolean capitalizeNext = false;
        
        for (char ch : snakeCase.toCharArray()) {
            if (ch == '_') {
                capitalizeNext = true;
            } else {
                if (capitalizeNext) {
                    result.append(Character.toUpperCase(ch));
                    capitalizeNext = false;
                } else {
                    result.append(ch);
                }
            }
        }
        
        return result.toString();
    }
    
    // 정규식 버전
    public static String toCamelCase2(String snakeCase) {
        String[] words = snakeCase.split("_");
        StringBuilder result = new StringBuilder(words[0]);
        
        for (int i = 1; i < words.length; i++) {
            result.append(words[i].substring(0, 1).toUpperCase());
            result.append(words[i].substring(1));
        }
        
        return result.toString();
    }
}
```
</details>

---

## 📌 핵심 정리

### 대소문자 변환
```java
str.toUpperCase()                     // 전체 대문자
str.toLowerCase()                     // 전체 소문자
// ⚠️ 원본은 변경 안 됨!
```

### 치환
```java
str.replace("old", "new")             // 리터럴 치환 (빠름)
str.replaceAll("\\d+", "")            // 정규식 치환 (느림)
str.replaceFirst("\\d+", "")          // 첫 번째만 정규식 치환

// 단순 치환은 replace() 사용 추천!
```

### 공백 제거
```java
str.trim()                            // 앞뒤 ASCII 공백
str.strip()                           // 앞뒤 유니코드 공백 (Java 11+)
str.stripLeading()                    // 앞쪽만
str.stripTrailing()                   // 뒤쪽만
```

### 기타
```java
str.repeat(3)                         // 반복 (Java 11+)
str.concat(" world")                  // 연결 (+ 연산자가 더 편함)
str.intern()                          // Pool 등록
```

### 체이닝 패턴
```java
String result = input
    .trim()
    .toLowerCase()
    .replaceAll("\\s+", " ")
    .replace("old", "new");
```

### 주의사항
```java
// ❌
str.toUpperCase();  // 결과 안 받음!

// ✅
String upper = str.toUpperCase();

// ❌
str.replace(".", "*");  // replace는 리터럴

// ✅
str.replaceAll("\\.", "*");  // 정규식에서 . 이스케이프
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 03. 검색과 인덱싱](./String-03-검색과인덱싱.md) | [다음: 05. 분리와 결합 →](./String-05-분리와결합.md)**

</div>
