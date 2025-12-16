# String 02. 생성과 비교

> String 생성 방법과 비교 메서드 완전 정복  
> Java API Reference

---

## 📑 목차

1. [String 생성 방법](#1-string-생성-방법)
2. [equals() - 내용 비교](#2-equals---내용-비교)
3. [compareTo() - 사전순 비교](#3-compareto---사전순-비교)
4. [기타 비교 메서드](#4-기타-비교-메서드)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. String 생성 방법

### 1.1 리터럴 방식 (추천)

```java
String s1 = "Hello";
```

**특징:**
- String Pool 사용
- 같은 내용은 같은 객체
- 가장 효율적
- 컴파일 타임에 최적화

**실습:**
```java
public class LiteralCreation {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "Hello";
        String s3 = "Hello";
        
        // 모두 같은 객체
        System.out.println(s1 == s2);  // true
        System.out.println(s2 == s3);  // true
        
        // 메모리 주소 확인
        System.out.println(System.identityHashCode(s1));
        System.out.println(System.identityHashCode(s2));
        System.out.println(System.identityHashCode(s3));
        // 모두 동일한 해시코드!
    }
}
```

### 1.2 new 키워드 방식

```java
String s1 = new String("Hello");
```

**특징:**
- Heap에 새 객체 생성
- 매번 다른 객체
- 메모리 낭비
- 특별한 경우가 아니면 비추천

**실습:**
```java
public class NewCreation {
    public static void main(String[] args) {
        String s1 = new String("Hello");
        String s2 = new String("Hello");
        
        // 다른 객체
        System.out.println(s1 == s2);  // false
        
        // 하지만 내용은 같음
        System.out.println(s1.equals(s2));  // true
        
        // 메모리 낭비 확인
        System.out.println(System.identityHashCode(s1));  // 다름
        System.out.println(System.identityHashCode(s2));  // 다름
    }
}
```

### 1.3 생성자 종류

#### 기본 생성자
```java
String s = new String();  // 빈 문자열 ""
System.out.println(s.length());  // 0
```

#### String으로부터 생성
```java
String original = "Hello";
String copy = new String(original);

System.out.println(original == copy);  // false (다른 객체)
System.out.println(original.equals(copy));  // true (내용 같음)
```

#### char 배열로부터 생성
```java
char[] chars = {'H', 'e', 'l', 'l', 'o'};
String s = new String(chars);
System.out.println(s);  // "Hello"

// 부분 배열 사용
String s2 = new String(chars, 1, 3);  // offset=1, count=3
System.out.println(s2);  // "ell"
```

#### byte 배열로부터 생성
```java
byte[] bytes = {72, 101, 108, 108, 111};  // ASCII 코드
String s = new String(bytes);
System.out.println(s);  // "Hello"

// 인코딩 지정
byte[] utf8Bytes = "안녕".getBytes("UTF-8");
String s2 = new String(utf8Bytes, "UTF-8");
System.out.println(s2);  // "안녕"
```

#### StringBuilder/StringBuffer로부터 생성
```java
StringBuilder sb = new StringBuilder("Hello");
String s = new String(sb);
System.out.println(s);  // "Hello"

// 또는 toString() 사용 (더 일반적)
String s2 = sb.toString();
```

### 1.4 특수 생성 방법

#### valueOf() - 다양한 타입 변환
```java
// 기본 타입 → String
String s1 = String.valueOf(123);        // "123"
String s2 = String.valueOf(3.14);       // "3.14"
String s3 = String.valueOf(true);       // "true"
String s4 = String.valueOf('A');        // "A"

// null 안전
Object obj = null;
String s5 = String.valueOf(obj);        // "null" (NPE 안남!)

// char 배열
char[] chars = {'H', 'i'};
String s6 = String.valueOf(chars);      // "Hi"
```

**실습: valueOf의 null 안전성**
```java
public class ValueOfTest {
    public static void main(String[] args) {
        Object obj = null;
        
        // toString() - NullPointerException!
        try {
            String s1 = obj.toString();
        } catch (NullPointerException e) {
            System.out.println("toString() NPE 발생!");
        }
        
        // valueOf() - 안전!
        String s2 = String.valueOf(obj);
        System.out.println(s2);  // "null"
        System.out.println(s2.equals("null"));  // true
    }
}
```

#### format() - 포맷 문자열
```java
// C언어의 printf 스타일
String s1 = String.format("이름: %s, 나이: %d", "홍길동", 25);
System.out.println(s1);  // "이름: 홍길동, 나이: 25"

// 소수점 제어
String s2 = String.format("원주율: %.2f", 3.14159);
System.out.println(s2);  // "원주율: 3.14"

// 날짜 포맷
String s3 = String.format("오늘은 %tY년 %tm월 %td일", 
                         new Date(), new Date(), new Date());
System.out.println(s3);  // "오늘은 2024년 01월 15일"
```

### 1.5 생성 방식 비교 정리

```java
public class CreationComparison {
    public static void main(String[] args) {
        // 1. 리터럴 (추천!)
        String s1 = "Hello";
        
        // 2. new (비추천)
        String s2 = new String("Hello");
        
        // 3. valueOf (타입 변환)
        String s3 = String.valueOf(123);
        
        // 4. format (포맷팅)
        String s4 = String.format("User: %s", "John");
        
        // 5. char 배열
        char[] arr = {'H', 'i'};
        String s5 = new String(arr);
        
        
        // 비교
        System.out.println("s1 == s2: " + (s1 == s2));  // false
        System.out.println("s1.equals(s2): " + s1.equals(s2));  // true
    }
}
```

**추천 사용법:**
| 목적 | 방법 | 예시 |
|---|---|---|
| 일반 문자열 | 리터럴 | `"Hello"` |
| 타입 변환 | `valueOf()` | `String.valueOf(123)` |
| 포맷팅 | `format()` | `String.format("%.2f", 3.14)` |
| 배열 변환 | `new String(arr)` | `new String(chars)` |

---

## 2. equals() - 내용 비교

### 2.1 기본 사용법

```java
String s1 = "Hello";
String s2 = "Hello";
String s3 = new String("Hello");

// 내용 비교 (추천!)
System.out.println(s1.equals(s2));  // true
System.out.println(s1.equals(s3));  // true

// 참조 비교 (비추천)
System.out.println(s1 == s2);  // true (Pool)
System.out.println(s1 == s3);  // false (다른 객체)
```

### 2.2 equals() vs ==

```java
public class EqualsVsOperator {
    public static void main(String[] args) {
        String s1 = "Java";
        String s2 = "Java";
        String s3 = new String("Java");
        
        // == : 참조(메모리 주소) 비교
        System.out.println("s1 == s2: " + (s1 == s2));  // true
        System.out.println("s1 == s3: " + (s1 == s3));  // false
        
        // equals(): 내용 비교
        System.out.println("s1.equals(s2): " + s1.equals(s2));  // true
        System.out.println("s1.equals(s3): " + s1.equals(s3));  // true
        
        
        // 실전 예시: 사용자 입력
        Scanner sc = new Scanner(System.in);
        System.out.print("명령어 입력: ");
        String input = sc.next();  // new로 생성됨!
        
        // ❌ 틀린 비교
        if (input == "exit") {
            System.out.println("이건 거의 실행 안 됨!");
        }
        
        // ✅ 올바른 비교
        if (input.equals("exit")) {
            System.out.println("프로그램 종료");
        }
    }
}
```

### 2.3 equalsIgnoreCase() - 대소문자 무시

```java
public class EqualsIgnoreCaseTest {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = "HELLO";
        String s3 = "hello";
        
        // 일반 equals
        System.out.println(s1.equals(s2));  // false
        System.out.println(s1.equals(s3));  // false
        
        // 대소문자 무시
        System.out.println(s1.equalsIgnoreCase(s2));  // true
        System.out.println(s1.equalsIgnoreCase(s3));  // true
        
        
        // 실전 예시: 파일 확장자 확인
        String filename = "Document.PDF";
        
        if (filename.toLowerCase().endsWith(".pdf")) {  // 방법 1
            System.out.println("PDF 파일");
        }
        
        // 또는
        if (filename.substring(filename.lastIndexOf('.')).equalsIgnoreCase(".pdf")) {
            System.out.println("PDF 파일");  // 방법 2
        }
    }
}
```

### 2.4 equals() 내부 구현 이해

```java
// String.equals() 내부 로직 (단순화)
public boolean equals(Object obj) {
    if (this == obj) return true;  // 같은 객체면 즉시 true
    
    if (!(obj instanceof String)) return false;  // String 아니면 false
    
    String other = (String) obj;
    
    // 길이 다르면 false
    if (this.length() != other.length()) return false;
    
    // 한 글자씩 비교
    for (int i = 0; i < this.length(); i++) {
        if (this.charAt(i) != other.charAt(i)) {
            return false;
        }
    }
    
    return true;
}
```

**실습: 직접 구현해보기**
```java
public class MyEquals {
    public static boolean myEquals(String s1, String s2) {
        // null 체크
        if (s1 == null && s2 == null) return true;
        if (s1 == null || s2 == null) return false;
        
        // 길이 체크
        if (s1.length() != s2.length()) return false;
        
        // 문자 하나씩 비교
        for (int i = 0; i < s1.length(); i++) {
            if (s1.charAt(i) != s2.charAt(i)) {
                return false;
            }
        }
        
        return true;
    }
    
    public static void main(String[] args) {
        System.out.println(myEquals("Hello", "Hello"));  // true
        System.out.println(myEquals("Hello", "World"));  // false
        System.out.println(myEquals(null, null));        // true
    }
}
```

### 2.5 null 처리

```java
public class NullHandling {
    public static void main(String[] args) {
        String s1 = "Hello";
        String s2 = null;
        
        // ❌ NullPointerException!
        try {
            boolean result = s2.equals(s1);
        } catch (NullPointerException e) {
            System.out.println("NPE 발생!");
        }
        
        // ✅ 안전한 방법 1: null 체크
        if (s2 != null && s2.equals(s1)) {
            System.out.println("같음");
        }
        
        // ✅ 안전한 방법 2: 리터럴을 앞에
        if ("Hello".equals(s2)) {  // s2가 null이어도 안전!
            System.out.println("같음");
        }
        
        // ✅ 안전한 방법 3: Objects.equals (Java 7+)
        if (Objects.equals(s1, s2)) {
            System.out.println("같음");
        }
    }
}
```

---

## 3. compareTo() - 사전순 비교

### 3.1 기본 개념

**반환값:**
- `음수`: 현재 문자열이 더 작음 (앞쪽)
- `0`: 두 문자열이 같음
- `양수`: 현재 문자열이 더 큼 (뒤쪽)

```java
public class CompareToBasic {
    public static void main(String[] args) {
        String s1 = "Apple";
        String s2 = "Banana";
        String s3 = "Apple";
        
        System.out.println(s1.compareTo(s2));  // 음수 (Apple < Banana)
        System.out.println(s2.compareTo(s1));  // 양수 (Banana > Apple)
        System.out.println(s1.compareTo(s3));  // 0 (같음)
        
        
        // 정렬 예시
        String[] fruits = {"Banana", "Apple", "Cherry"};
        Arrays.sort(fruits);  // compareTo 사용
        System.out.println(Arrays.toString(fruits));
        // [Apple, Banana, Cherry]
    }
}
```

### 3.2 동작 원리

```java
public class CompareToMechanism {
    public static void main(String[] args) {
        // 1. 문자 단위로 비교 (ASCII/Unicode 값)
        String s1 = "abc";
        String s2 = "abd";
        
        // 'c'(99) vs 'd'(100) → -1
        System.out.println(s1.compareTo(s2));  // -1
        
        
        // 2. 길이가 다를 때
        String s3 = "abc";
        String s4 = "abcde";
        
        // 앞부분 같고 s3이 짧음 → 음수
        System.out.println(s3.compareTo(s4));  // -2 (길이 차이)
        
        
        // 3. 대소문자 구분
        String s5 = "Apple";
        String s6 = "apple";
        
        // 'A'(65) vs 'a'(97) → -32
        System.out.println(s5.compareTo(s6));  // -32
    }
}
```

**내부 구현 (단순화):**
```java
public int compareTo(String other) {
    int len1 = this.length();
    int len2 = other.length();
    int minLen = Math.min(len1, len2);
    
    // 문자 단위 비교
    for (int i = 0; i < minLen; i++) {
        char c1 = this.charAt(i);
        char c2 = other.charAt(i);
        
        if (c1 != c2) {
            return c1 - c2;  // ASCII 차이 반환
        }
    }
    
    // 앞부분 같으면 길이 차이 반환
    return len1 - len2;
}
```

### 3.3 compareToIgnoreCase() - 대소문자 무시

```java
public class CompareToIgnoreCaseTest {
    public static void main(String[] args) {
        String s1 = "Apple";
        String s2 = "apple";
        
        // 일반 compareTo
        System.out.println(s1.compareTo(s2));  // -32 (대소문자 다름)
        
        // 대소문자 무시
        System.out.println(s1.compareToIgnoreCase(s2));  // 0 (같음)
        
        
        // 정렬 예시
        String[] words = {"Zebra", "apple", "Banana"};
        
        // 대소문자 구분 정렬
        Arrays.sort(words);
        System.out.println(Arrays.toString(words));
        // [Banana, Zebra, apple] (대문자가 먼저)
        
        // 대소문자 무시 정렬
        Arrays.sort(words, String.CASE_INSENSITIVE_ORDER);
        System.out.println(Arrays.toString(words));
        // [apple, Banana, Zebra]
    }
}
```

### 3.4 정렬 활용

#### 기본 정렬
```java
public class SortExample {
    public static void main(String[] args) {
        String[] names = {"Charlie", "Alice", "Bob"};
        
        // 오름차순
        Arrays.sort(names);
        System.out.println(Arrays.toString(names));
        // [Alice, Bob, Charlie]
        
        
        // 내림차순
        Arrays.sort(names, Collections.reverseOrder());
        System.out.println(Arrays.toString(names));
        // [Charlie, Bob, Alice]
    }
}
```

#### 커스텀 정렬
```java
import java.util.*;

public class CustomSort {
    public static void main(String[] args) {
        String[] words = {"apple", "pie", "banana", "cat"};
        
        // 길이순 정렬
        Arrays.sort(words, new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return s1.length() - s2.length();
            }
        });
        System.out.println(Arrays.toString(words));
        // [pie, cat, apple, banana]
        
        
        // 람다 표현식 (Java 8+)
        Arrays.sort(words, (s1, s2) -> s1.length() - s2.length());
        
        // 또는
        Arrays.sort(words, Comparator.comparingInt(String::length));
    }
}
```

### 3.5 실전 응용

#### 이름 정렬
```java
import java.util.*;

public class NameSorting {
    static class Person {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
    }
    
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Charlie", 30),
            new Person("Alice", 25),
            new Person("Bob", 35)
        );
        
        // 이름순 정렬
        Collections.sort(people, (p1, p2) -> p1.name.compareTo(p2.name));
        
        for (Person p : people) {
            System.out.println(p.name + ": " + p.age);
        }
        // Alice: 25
        // Bob: 35
        // Charlie: 30
    }
}
```

---

## 4. 기타 비교 메서드

### 4.1 startsWith() / endsWith()

```java
public class StartsEndsWithTest {
    public static void main(String[] args) {
        String filename = "document.pdf";
        
        // 시작 확인
        if (filename.startsWith("doc")) {
            System.out.println("doc으로 시작");
        }
        
        // 끝 확인
        if (filename.endsWith(".pdf")) {
            System.out.println("PDF 파일");
        }
        
        
        // offset 지정
        String text = "Hello World";
        System.out.println(text.startsWith("World", 6));  // true
        // 인덱스 6부터 "World"로 시작하나?
        
        
        // 실전 예시: URL 검증
        String url = "https://example.com";
        
        if (url.startsWith("https://")) {
            System.out.println("보안 연결");
        } else if (url.startsWith("http://")) {
            System.out.println("비보안 연결");
        }
    }
}
```

### 4.2 contains()

```java
public class ContainsTest {
    public static void main(String[] args) {
        String text = "Java Programming Language";
        
        // 포함 여부 확인
        System.out.println(text.contains("Java"));        // true
        System.out.println(text.contains("Python"));      // false
        System.out.println(text.contains("Program"));     // true
        
        
        // 대소문자 구분됨!
        System.out.println(text.contains("java"));        // false
        
        // 대소문자 무시하려면
        System.out.println(text.toLowerCase().contains("java"));  // true
        
        
        // 실전 예시: 키워드 필터링
        String comment = "This is spam message";
        String[] badWords = {"spam", "ad", "click"};
        
        boolean isSpam = false;
        for (String word : badWords) {
            if (comment.toLowerCase().contains(word)) {
                isSpam = true;
                break;
            }
        }
        
        if (isSpam) {
            System.out.println("스팸 감지!");
        }
    }
}
```

### 4.3 matches() - 정규표현식

```java
public class MatchesTest {
    public static void main(String[] args) {
        // 숫자만
        String s1 = "12345";
        System.out.println(s1.matches("\\d+"));  // true
        
        // 이메일 형식
        String email = "user@example.com";
        System.out.println(email.matches("\\w+@\\w+\\.\\w+"));  // true
        
        // 전화번호 형식
        String phone = "010-1234-5678";
        System.out.println(phone.matches("\\d{3}-\\d{4}-\\d{4}"));  // true
        
        
        // 실전 예시: 비밀번호 검증
        String password = "Pass123!";
        
        // 8자 이상, 영문+숫자+특수문자
        boolean valid = password.matches("^(?=.*[A-Za-z])(?=.*\\d)(?=.*[@$!%*#?&])[A-Za-z\\d@$!%*#?&]{8,}$");
        
        if (valid) {
            System.out.println("유효한 비밀번호");
        } else {
            System.out.println("비밀번호 규칙 위반");
        }
    }
}
```

### 4.4 isEmpty() / isBlank()

```java
public class EmptyBlankTest {
    public static void main(String[] args) {
        String s1 = "";
        String s2 = "   ";
        String s3 = "Hello";
        
        // isEmpty(): 길이가 0인가?
        System.out.println(s1.isEmpty());  // true
        System.out.println(s2.isEmpty());  // false (공백도 문자!)
        System.out.println(s3.isEmpty());  // false
        
        
        // isBlank(): 비어있거나 공백만 있나? (Java 11+)
        System.out.println(s1.isBlank());  // true
        System.out.println(s2.isBlank());  // true (공백만!)
        System.out.println(s3.isBlank());  // false
        
        
        // 실전 예시: 입력 검증
        Scanner sc = new Scanner(System.in);
        String input = sc.nextLine();
        
        if (input.isBlank()) {
            System.out.println("입력값이 없습니다!");
        }
    }
}
```

### 4.5 contentEquals()

```java
public class ContentEqualsTest {
    public static void main(String[] args) {
        String s1 = "Hello";
        StringBuilder sb = new StringBuilder("Hello");
        StringBuffer sbf = new StringBuffer("Hello");
        
        // equals()는 String만 비교 가능
        // System.out.println(s1.equals(sb));  // false (타입 다름)
        
        // contentEquals()는 CharSequence 모두 비교
        System.out.println(s1.contentEquals(sb));   // true
        System.out.println(s1.contentEquals(sbf));  // true
        
        
        // 활용
        CharSequence cs = "Hello";  // String은 CharSequence의 구현체
        System.out.println(s1.contentEquals(cs));  // true
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 올바른 비교 방법

```java
// 다음 코드의 문제점을 찾아 수정하세요
public class Problem1 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("종료하려면 'exit' 입력: ");
        String input = sc.next();
        
        if (input == "exit") {  // 문제!
            System.out.println("프로그램 종료");
        } else {
            System.out.println("계속 실행");
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);
        System.out.print("종료하려면 'exit' 입력: ");
        String input = sc.next();
        
        // ✅ equals 사용!
        if (input.equals("exit")) {
            System.out.println("프로그램 종료");
        } else {
            System.out.println("계속 실행");
        }
    }
}
```

**이유:**
- `==`는 참조 비교
- `input`은 Scanner로 생성 (new)
- 리터럴 "exit"와 다른 객체
- `equals()`로 내용 비교해야 함
</details>

---

### 문제 2: 정렬 구현

```java
// 다음 배열을 길이순으로 정렬하되, 길이가 같으면 사전순으로 정렬
String[] words = {"dog", "cat", "elephant", "ant", "bear"};
// 원하는 결과: [ant, cat, dog, bear, elephant]
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
import java.util.*;

public class Problem2 {
    public static void main(String[] args) {
        String[] words = {"dog", "cat", "elephant", "ant", "bear"};
        
        Arrays.sort(words, new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                // 길이 먼저 비교
                if (s1.length() != s2.length()) {
                    return s1.length() - s2.length();
                }
                // 길이 같으면 사전순
                return s1.compareTo(s2);
            }
        });
        
        System.out.println(Arrays.toString(words));
        // [ant, cat, dog, bear, elephant]
    }
}

// 람다 버전
Arrays.sort(words, (s1, s2) -> {
    if (s1.length() != s2.length()) {
        return s1.length() - s2.length();
    }
    return s1.compareTo(s2);
});
```
</details>

---

### 문제 3: 파일 확장자 검사

```java
// 주어진 파일명이 이미지 파일인지 확인하는 메서드 작성
// 이미지 확장자: .jpg, .jpeg, .png, .gif (대소문자 무시)

public class Problem3 {
    public static boolean isImageFile(String filename) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(isImageFile("photo.jpg"));    // true
        System.out.println(isImageFile("photo.JPG"));    // true
        System.out.println(isImageFile("document.pdf")); // false
        System.out.println(isImageFile("image.png"));    // true
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static boolean isImageFile(String filename) {
        // null 체크
        if (filename == null || filename.isEmpty()) {
            return false;
        }
        
        // 소문자 변환
        String lower = filename.toLowerCase();
        
        // 확장자 확인
        return lower.endsWith(".jpg") 
            || lower.endsWith(".jpeg") 
            || lower.endsWith(".png") 
            || lower.endsWith(".gif");
    }
    
    // 더 우아한 방법
    public static boolean isImageFile2(String filename) {
        if (filename == null || filename.isEmpty()) {
            return false;
        }
        
        String[] imageExts = {".jpg", ".jpeg", ".png", ".gif"};
        String lower = filename.toLowerCase();
        
        for (String ext : imageExts) {
            if (lower.endsWith(ext)) {
                return true;
            }
        }
        return false;
    }
}
```
</details>

---

### 문제 4: 이메일 검증

```java
// 간단한 이메일 검증 메서드 작성
// 규칙: @ 하나 포함, @ 앞뒤로 1글자 이상, . 포함

public class Problem4 {
    public static boolean isValidEmail(String email) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(isValidEmail("user@example.com"));  // true
        System.out.println(isValidEmail("invalid"));           // false
        System.out.println(isValidEmail("@example.com"));      // false
        System.out.println(isValidEmail("user@"));             // false
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem4 {
    public static boolean isValidEmail(String email) {
        // null/빈 문자열 체크
        if (email == null || email.isEmpty()) {
            return false;
        }
        
        // @ 하나만 있어야 함
        int atIndex = email.indexOf('@');
        if (atIndex == -1 || atIndex != email.lastIndexOf('@')) {
            return false;
        }
        
        // @ 앞에 1글자 이상
        if (atIndex == 0) {
            return false;
        }
        
        // @ 뒤에 1글자 이상
        if (atIndex == email.length() - 1) {
            return false;
        }
        
        // . 포함
        if (!email.contains(".")) {
            return false;
        }
        
        // . 이 @ 뒤에 있어야 함
        int dotIndex = email.lastIndexOf('.');
        if (dotIndex < atIndex) {
            return false;
        }
        
        return true;
    }
    
    // 정규표현식 버전 (더 정확)
    public static boolean isValidEmail2(String email) {
        if (email == null) return false;
        return email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$");
    }
}
```
</details>

---

## 📌 핵심 정리

### 생성 방법
```java
// ✅ 추천
String s1 = "Hello";                    // 리터럴 (Pool 사용)
String s2 = String.valueOf(123);        // 타입 변환
String s3 = String.format("%.2f", 3.14);// 포맷팅

// ⚠️ 특수 상황에만
String s4 = new String("Hello");        // 새 객체 (비추천)
```

### 비교 메서드
| 메서드 | 용도 | 반환 타입 |
|---|---|---|
| `equals()` | 내용 같은지 | boolean |
| `equalsIgnoreCase()` | 대소문자 무시 비교 | boolean |
| `compareTo()` | 사전순 비교 | int |
| `compareToIgnoreCase()` | 대소문자 무시 사전순 | int |
| `startsWith()` | 시작 문자열 확인 | boolean |
| `endsWith()` | 끝 문자열 확인 | boolean |
| `contains()` | 포함 여부 | boolean |
| `matches()` | 정규식 매칭 | boolean |

### 필수 암기
```java
// ❌ 절대 금지
if (str == "Hello") { }  // 거의 항상 틀림!

// ✅ 항상 이렇게
if (str.equals("Hello")) { }

// ✅ null 안전
if ("Hello".equals(str)) { }  // str이 null이어도 OK
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. 기본 개념](./String-01-기본개념.md) | [다음: 03. 검색과 인덱싱 →](./String-03-검색과인덱싱.md)**

</div>
