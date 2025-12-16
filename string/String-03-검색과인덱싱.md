# String 03. 검색과 인덱싱

> String에서 문자와 부분 문자열을 찾고 접근하는 모든 방법  
> Java API Reference

---

## 📑 목차

1. [charAt() - 문자 접근](#1-charat---문자-접근)
2. [indexOf() / lastIndexOf() - 위치 찾기](#2-indexof--lastindexof---위치-찾기)
3. [substring() - 부분 문자열](#3-substring---부분-문자열)
4. [length() - 길이 확인](#4-length---길이-확인)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. charAt() - 문자 접근

### 1.1 기본 사용법

```java
public class CharAtBasic {
    public static void main(String[] args) {
        String str = "Hello";
        
        // 인덱스로 문자 접근 (0부터 시작!)
        char ch0 = str.charAt(0);  // 'H'
        char ch1 = str.charAt(1);  // 'e'
        char ch4 = str.charAt(4);  // 'o'
        
        System.out.println(ch0);  // H
        System.out.println(ch1);  // e
        System.out.println(ch4);  // o
        
        
        // ❌ 잘못된 인덱스
        try {
            char ch = str.charAt(5);  // StringIndexOutOfBoundsException!
        } catch (StringIndexOutOfBoundsException e) {
            System.out.println("인덱스 초과!");
        }
        
        // ❌ 음수 인덱스
        try {
            char ch = str.charAt(-1);  // 예외 발생!
        } catch (StringIndexOutOfBoundsException e) {
            System.out.println("음수 인덱스 불가!");
        }
    }
}
```

**인덱스 범위:**
- 유효 범위: `0 ~ length() - 1`
- `length()`는 포함 안 됨!

```
String: "Hello"
인덱스:  0 1 2 3 4
문자:    H e l l o
```

### 1.2 전체 순회

#### for문으로 순회
```java
public class CharAtIteration {
    public static void main(String[] args) {
        String str = "Java";
        
        // 방법 1: 전통적인 for
        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);
            System.out.print(ch + " ");
        }
        System.out.println();  // J a v a
        
        
        // 방법 2: char 배열로 변환
        for (char ch : str.toCharArray()) {
            System.out.print(ch + " ");
        }
        System.out.println();  // J a v a
    }
}
```

#### 역순 순회
```java
public class ReverseIteration {
    public static void main(String[] args) {
        String str = "Hello";
        
        // 뒤에서부터 순회
        for (int i = str.length() - 1; i >= 0; i--) {
            char ch = str.charAt(i);
            System.out.print(ch);
        }
        System.out.println();  // olleH
    }
}
```

### 1.3 실전 활용

#### 대문자 개수 세기
```java
public class CountUpperCase {
    public static int countUpper(String str) {
        int count = 0;
        
        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);
            if (Character.isUpperCase(ch)) {
                count++;
            }
        }
        
        return count;
    }
    
    public static void main(String[] args) {
        String text = "Hello World";
        System.out.println(countUpper(text));  // 2 (H, W)
    }
}
```

#### 모음 개수 세기
```java
public class CountVowels {
    public static int countVowels(String str) {
        int count = 0;
        String vowels = "aeiouAEIOU";
        
        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);
            if (vowels.indexOf(ch) != -1) {
                count++;
            }
        }
        
        return count;
    }
    
    public static void main(String[] args) {
        String text = "Hello World";
        System.out.println(countVowels(text));  // 3 (e, o, o)
    }
}
```

#### 팰린드롬 확인
```java
public class Palindrome {
    public static boolean isPalindrome(String str) {
        int left = 0;
        int right = str.length() - 1;
        
        while (left < right) {
            if (str.charAt(left) != str.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        
        return true;
    }
    
    public static void main(String[] args) {
        System.out.println(isPalindrome("level"));   // true
        System.out.println(isPalindrome("hello"));   // false
        System.out.println(isPalindrome("radar"));   // true
    }
}
```

### 1.4 성능 고려사항

```java
public class CharAtPerformance {
    public static void main(String[] args) {
        String str = "Hello World";
        
        // ✅ 효율적: charAt() 한 번만 호출
        for (int i = 0; i < str.length(); i++) {
            char ch = str.charAt(i);
            // ch를 여러 번 사용
            System.out.println(ch);
            System.out.println(Character.toUpperCase(ch));
        }
        
        
        // ❌ 비효율적: 같은 위치를 여러 번 호출
        for (int i = 0; i < str.length(); i++) {
            System.out.println(str.charAt(i));
            System.out.println(Character.toUpperCase(str.charAt(i)));
            // charAt() 두 번 호출!
        }
    }
}
```

---

## 2. indexOf() / lastIndexOf() - 위치 찾기

### 2.1 indexOf() 기본

**반환값:**
- 찾으면: 첫 번째 발견 위치 (0부터 시작)
- 못 찾으면: -1

```java
public class IndexOfBasic {
    public static void main(String[] args) {
        String str = "Hello World";
        
        // 문자 찾기
        int idx1 = str.indexOf('o');     // 4 (첫 번째 'o')
        int idx2 = str.indexOf('W');     // 6
        int idx3 = str.indexOf('x');     // -1 (없음)
        
        System.out.println(idx1);  // 4
        System.out.println(idx2);  // 6
        System.out.println(idx3);  // -1
        
        
        // 문자열 찾기
        int idx4 = str.indexOf("World");   // 6
        int idx5 = str.indexOf("llo");     // 2
        int idx6 = str.indexOf("Java");    // -1 (없음)
        
        System.out.println(idx4);  // 6
        System.out.println(idx5);  // 2
        System.out.println(idx6);  // -1
    }
}
```

### 2.2 시작 위치 지정

```java
public class IndexOfFromIndex {
    public static void main(String[] args) {
        String str = "Hello World Hello";
        
        // indexOf(찾을 것, 시작위치)
        int first = str.indexOf("Hello");      // 0
        int second = str.indexOf("Hello", 1);  // 12 (두 번째 "Hello")
        
        System.out.println(first);   // 0
        System.out.println(second);  // 12
        
        
        // 활용: 모든 위치 찾기
        int pos = 0;
        while ((pos = str.indexOf('o', pos)) != -1) {
            System.out.println("'o' found at: " + pos);
            pos++;  // 다음 위치로 이동
        }
        // 'o' found at: 4
        // 'o' found at: 7
        // 'o' found at: 16
    }
}
```

### 2.3 lastIndexOf() - 뒤에서부터 찾기

```java
public class LastIndexOfTest {
    public static void main(String[] args) {
        String str = "Hello World Hello";
        
        // 마지막 위치 찾기
        int last1 = str.lastIndexOf('o');      // 16 (마지막 'o')
        int last2 = str.lastIndexOf("Hello");  // 12 (마지막 "Hello")
        
        System.out.println(last1);  // 16
        System.out.println(last2);  // 12
        
        
        // 비교: indexOf vs lastIndexOf
        String text = "abcabc";
        System.out.println(text.indexOf('a'));      // 0 (첫 번째)
        System.out.println(text.lastIndexOf('a'));  // 3 (마지막)
        
        
        // 끝 위치 지정 (역방향 검색)
        int idx = str.lastIndexOf('o', 10);  // 10 이전에서 마지막 'o'
        System.out.println(idx);  // 7
    }
}
```

### 2.4 실전 활용 패턴

#### 패턴 1: 존재 여부 확인
```java
public class IndexOfCheck {
    public static void main(String[] args) {
        String email = "user@example.com";
        
        // @ 포함 여부 확인
        if (email.indexOf('@') != -1) {
            System.out.println("유효한 이메일 형식");
        } else {
            System.out.println("@ 없음");
        }
        
        
        // contains()가 더 직관적
        if (email.contains("@")) {
            System.out.println("@ 포함");
        }
        
        // 하지만 indexOf()는 위치도 알 수 있음!
        int atPos = email.indexOf('@');
        System.out.println("@ 위치: " + atPos);  // 4
    }
}
```

#### 패턴 2: 파일 확장자 추출
```java
public class ExtractExtension {
    public static String getExtension(String filename) {
        int dotIndex = filename.lastIndexOf('.');
        
        if (dotIndex == -1 || dotIndex == filename.length() - 1) {
            return "";  // 확장자 없음
        }
        
        return filename.substring(dotIndex + 1);
    }
    
    public static void main(String[] args) {
        System.out.println(getExtension("document.pdf"));    // pdf
        System.out.println(getExtension("archive.tar.gz"));  // gz
        System.out.println(getExtension("noext"));           // ""
        System.out.println(getExtension("dotend."));         // ""
    }
}
```

#### 패턴 3: URL 파싱
```java
public class UrlParser {
    public static void parseUrl(String url) {
        // 프로토콜 추출
        int protocolEnd = url.indexOf("://");
        String protocol = url.substring(0, protocolEnd);
        
        // 도메인 추출
        int domainStart = protocolEnd + 3;
        int domainEnd = url.indexOf('/', domainStart);
        if (domainEnd == -1) {
            domainEnd = url.length();
        }
        String domain = url.substring(domainStart, domainEnd);
        
        // 경로 추출
        String path = "";
        if (domainEnd < url.length()) {
            path = url.substring(domainEnd);
        }
        
        System.out.println("프로토콜: " + protocol);
        System.out.println("도메인: " + domain);
        System.out.println("경로: " + path);
    }
    
    public static void main(String[] args) {
        parseUrl("https://example.com/path/to/page");
        // 프로토콜: https
        // 도메인: example.com
        // 경로: /path/to/page
    }
}
```

#### 패턴 4: 모든 위치 찾기
```java
public class FindAllOccurrences {
    public static List<Integer> findAll(String text, String target) {
        List<Integer> positions = new ArrayList<>();
        int pos = 0;
        
        while ((pos = text.indexOf(target, pos)) != -1) {
            positions.add(pos);
            pos += target.length();  // 다음 검색 위치
        }
        
        return positions;
    }
    
    public static void main(String[] args) {
        String text = "banana";
        List<Integer> positions = findAll(text, "an");
        System.out.println(positions);  // [1, 3]
        
        
        // 개수 세기
        String sentence = "to be or not to be";
        int count = findAll(sentence, "to").size();
        System.out.println("'to' appears " + count + " times");  // 2
    }
}
```

#### 패턴 5: 문자열 분리 (수동)
```java
public class ManualSplit {
    public static List<String> split(String text, char delimiter) {
        List<String> result = new ArrayList<>();
        int start = 0;
        int pos;
        
        while ((pos = text.indexOf(delimiter, start)) != -1) {
            result.add(text.substring(start, pos));
            start = pos + 1;
        }
        
        // 마지막 부분 추가
        result.add(text.substring(start));
        
        return result;
    }
    
    public static void main(String[] args) {
        String csv = "apple,banana,cherry";
        List<String> items = split(csv, ',');
        System.out.println(items);  // [apple, banana, cherry]
    }
}
```

---

## 3. substring() - 부분 문자열

### 3.1 기본 사용법

#### substring(시작인덱스)
```java
public class SubstringBasic {
    public static void main(String[] args) {
        String str = "Hello World";
        
        // 시작 인덱스부터 끝까지
        String sub1 = str.substring(6);   // "World"
        String sub2 = str.substring(0);   // "Hello World" (전체)
        String sub3 = str.substring(7);   // "orld"
        
        System.out.println(sub1);  // World
        System.out.println(sub2);  // Hello World
        System.out.println(sub3);  // orld
    }
}
```

#### substring(시작, 끝)
```java
public class SubstringRange {
    public static void main(String[] args) {
        String str = "Hello World";
        
        // 시작 ~ 끝-1 (끝 인덱스는 포함 안 됨!)
        String sub1 = str.substring(0, 5);    // "Hello"
        String sub2 = str.substring(6, 11);   // "World"
        String sub3 = str.substring(2, 7);    // "llo W"
        
        System.out.println(sub1);  // Hello
        System.out.println(sub2);  // World
        System.out.println(sub3);  // llo W
        
        
        // 주의: 끝 인덱스 포함 안 됨!
        String text = "abcde";
        System.out.println(text.substring(1, 3));  // "bc" (c까지)
        // 인덱스 1부터 2까지 (3은 미포함)
    }
}
```

**인덱스 범위 이해:**
```
String: "Hello"
인덱스:  0 1 2 3 4 5
        ↓ ↓ ↓ ↓ ↓ ↓
문자:    H e l l o

substring(1, 4):
        ↓ [e l l] ↓
인덱스:  1 2 3   4
결과: "ell"
```

### 3.2 실전 활용

#### 이름에서 성/이름 분리
```java
public class NameParser {
    public static void main(String[] args) {
        String fullName = "홍길동";
        
        // 성 추출 (첫 글자)
        String lastName = fullName.substring(0, 1);
        
        // 이름 추출 (나머지)
        String firstName = fullName.substring(1);
        
        System.out.println("성: " + lastName);    // 홍
        System.out.println("이름: " + firstName);  // 길동
        
        
        // 영어 이름 (공백 기준)
        String name = "John Smith";
        int spaceIdx = name.indexOf(' ');
        
        String first = name.substring(0, spaceIdx);
        String last = name.substring(spaceIdx + 1);
        
        System.out.println("First: " + first);  // John
        System.out.println("Last: " + last);    // Smith
    }
}
```

#### 전화번호 포맷팅
```java
public class PhoneFormatter {
    public static String formatPhone(String phone) {
        // "01012345678" → "010-1234-5678"
        if (phone.length() != 11) {
            return phone;
        }
        
        String part1 = phone.substring(0, 3);    // 010
        String part2 = phone.substring(3, 7);    // 1234
        String part3 = phone.substring(7, 11);   // 5678
        
        return part1 + "-" + part2 + "-" + part3;
    }
    
    public static void main(String[] args) {
        System.out.println(formatPhone("01012345678"));
        // 010-1234-5678
    }
}
```

#### 주민등록번호 마스킹
```java
public class MaskSSN {
    public static String maskSSN(String ssn) {
        // "123456-1234567" → "123456-1******"
        if (ssn.length() != 14) {
            return ssn;
        }
        
        String front = ssn.substring(0, 8);   // "123456-1"
        String masked = front + "******";
        
        return masked;
    }
    
    public static void main(String[] args) {
        System.out.println(maskSSN("123456-1234567"));
        // 123456-1******
    }
}
```

#### 날짜 파싱
```java
public class DateParser {
    public static void parseDate(String date) {
        // "2024-01-15" 형식
        String year = date.substring(0, 4);
        String month = date.substring(5, 7);
        String day = date.substring(8, 10);
        
        System.out.println("Year: " + year);
        System.out.println("Month: " + month);
        System.out.println("Day: " + day);
    }
    
    public static void main(String[] args) {
        parseDate("2024-01-15");
        // Year: 2024
        // Month: 01
        // Day: 15
    }
}
```

### 3.3 주의사항

#### 인덱스 초과 방지
```java
public class SubstringSafety {
    public static void main(String[] args) {
        String str = "Hello";
        
        // ❌ 인덱스 초과
        try {
            String sub = str.substring(10);  // Exception!
        } catch (StringIndexOutOfBoundsException e) {
            System.out.println("인덱스 초과!");
        }
        
        
        // ✅ 안전한 방법
        int start = 10;
        if (start < str.length()) {
            String sub = str.substring(start);
        } else {
            System.out.println("인덱스가 너무 큼");
        }
        
        
        // ✅ 더 안전한 방법
        String safeSub = str.substring(Math.min(start, str.length()));
        System.out.println(safeSub);  // "" (빈 문자열)
    }
}
```

#### 음수 인덱스 불가
```java
public class NegativeIndex {
    public static void main(String[] args) {
        String str = "Hello";
        
        // ❌ 음수 인덱스
        try {
            String sub = str.substring(-1);  // Exception!
        } catch (StringIndexOutOfBoundsException e) {
            System.out.println("음수 인덱스 불가!");
        }
        
        
        // Python처럼 뒤에서부터 접근하려면?
        int lastIndex = str.length() - 1;
        String lastChar = str.substring(lastIndex);
        System.out.println(lastChar);  // "o"
    }
}
```

---

## 4. length() - 길이 확인

### 4.1 기본 사용법

```java
public class LengthBasic {
    public static void main(String[] args) {
        String str1 = "Hello";
        String str2 = "";
        String str3 = "안녕하세요";
        
        System.out.println(str1.length());  // 5
        System.out.println(str2.length());  // 0
        System.out.println(str3.length());  // 5 (한글도 1글자)
        
        
        // 공백도 길이에 포함!
        String str4 = "Hello World";
        System.out.println(str4.length());  // 11 (공백 포함)
        
        String str5 = "   ";
        System.out.println(str5.length());  // 3 (공백 3개)
    }
}
```

### 4.2 실전 활용

#### 입력 검증
```java
public class InputValidation {
    public static boolean isValidPassword(String password) {
        // 8~20자 사이
        if (password.length() < 8 || password.length() > 20) {
            return false;
        }
        return true;
    }
    
    public static void main(String[] args) {
        System.out.println(isValidPassword("abc"));      // false (너무 짧음)
        System.out.println(isValidPassword("abcd1234")); // true
    }
}
```

#### 마지막 문자 접근
```java
public class LastCharacter {
    public static void main(String[] args) {
        String str = "Hello";
        
        if (str.length() > 0) {
            char lastChar = str.charAt(str.length() - 1);
            System.out.println(lastChar);  // 'o'
        }
        
        
        // 마지막 단어 추출
        String sentence = "Hello World";
        int lastSpace = sentence.lastIndexOf(' ');
        String lastWord = sentence.substring(lastSpace + 1);
        System.out.println(lastWord);  // World
    }
}
```

#### 문자열 자르기
```java
public class TruncateString {
    public static String truncate(String text, int maxLen) {
        if (text.length() <= maxLen) {
            return text;
        }
        return text.substring(0, maxLen) + "...";
    }
    
    public static void main(String[] args) {
        String longText = "This is a very long text";
        System.out.println(truncate(longText, 10));
        // This is a ...
    }
}
```

### 4.3 length() vs length (배열)

```java
public class LengthComparison {
    public static void main(String[] args) {
        // String: length() 메서드
        String str = "Hello";
        System.out.println(str.length());  // 5
        
        // 배열: length 필드
        int[] arr = {1, 2, 3, 4, 5};
        System.out.println(arr.length);    // 5
        
        
        // ❌ 헷갈리기 쉬운 실수
        // System.out.println(str.length);   // 컴파일 에러!
        // System.out.println(arr.length());  // 컴파일 에러!
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 특정 문자 개수 세기

```java
// 주어진 문자열에서 특정 문자가 몇 번 나타나는지 세는 메서드 작성
public class Problem1 {
    public static int countChar(String str, char target) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(countChar("Hello World", 'o'));  // 2
        System.out.println(countChar("banana", 'a'));       // 3
        System.out.println(countChar("test", 'x'));         // 0
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    // 방법 1: charAt() 사용
    public static int countChar(String str, char target) {
        int count = 0;
        for (int i = 0; i < str.length(); i++) {
            if (str.charAt(i) == target) {
                count++;
            }
        }
        return count;
    }
    
    // 방법 2: indexOf() 반복 사용
    public static int countChar2(String str, char target) {
        int count = 0;
        int pos = 0;
        
        while ((pos = str.indexOf(target, pos)) != -1) {
            count++;
            pos++;
        }
        
        return count;
    }
}
```
</details>

---

### 문제 2: 첫 번째와 마지막 단어 추출

```java
// 공백으로 구분된 문장에서 첫 번째와 마지막 단어를 추출
public class Problem2 {
    public static String[] getFirstAndLast(String sentence) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        String[] result = getFirstAndLast("Hello beautiful world");
        System.out.println(Arrays.toString(result));
        // [Hello, world]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static String[] getFirstAndLast(String sentence) {
        // 빈 문자열 체크
        if (sentence == null || sentence.trim().isEmpty()) {
            return new String[]{"", ""};
        }
        
        // 앞뒤 공백 제거
        sentence = sentence.trim();
        
        // 첫 번째 단어
        int firstSpace = sentence.indexOf(' ');
        String firstWord;
        if (firstSpace == -1) {
            // 단어가 하나뿐
            return new String[]{sentence, sentence};
        }
        firstWord = sentence.substring(0, firstSpace);
        
        // 마지막 단어
        int lastSpace = sentence.lastIndexOf(' ');
        String lastWord = sentence.substring(lastSpace + 1);
        
        return new String[]{firstWord, lastWord};
    }
}
```
</details>

---

### 문제 3: URL에서 도메인 추출

```java
// URL에서 도메인만 추출하는 메서드 작성
// 예: "https://www.example.com/path" → "www.example.com"
public class Problem3 {
    public static String extractDomain(String url) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(extractDomain("https://www.google.com/search"));
        // www.google.com
        
        System.out.println(extractDomain("http://example.com"));
        // example.com
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static String extractDomain(String url) {
        // "://" 찾기
        int protocolEnd = url.indexOf("://");
        int domainStart = (protocolEnd == -1) ? 0 : protocolEnd + 3;
        
        // 첫 번째 '/' 찾기 (경로 시작)
        int pathStart = url.indexOf('/', domainStart);
        
        if (pathStart == -1) {
            // 경로 없음
            return url.substring(domainStart);
        } else {
            // 경로 있음
            return url.substring(domainStart, pathStart);
        }
    }
}
```
</details>

---

### 문제 4: 문자열 회전 확인

```java
// s2가 s1을 회전시킨 문자열인지 확인
// 예: "waterbottle"과 "erbottlewat"는 회전 관계
public class Problem4 {
    public static boolean isRotation(String s1, String s2) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(isRotation("waterbottle", "erbottlewat")); // true
        System.out.println(isRotation("hello", "llohe"));              // true
        System.out.println(isRotation("hello", "world"));              // false
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem4 {
    public static boolean isRotation(String s1, String s2) {
        // 길이가 다르면 회전 불가
        if (s1.length() != s2.length()) {
            return false;
        }
        
        // s1을 두 번 이어붙인 문자열에 s2가 포함되는지 확인
        // 예: "waterbottle" + "waterbottle" = "waterbottlewaterbottle"
        // "erbottlewat"가 포함되어 있음!
        String doubled = s1 + s1;
        return doubled.contains(s2);
    }
    
    // 또는 수동으로 확인
    public static boolean isRotation2(String s1, String s2) {
        if (s1.length() != s2.length()) {
            return false;
        }
        
        // 모든 회전 위치 시도
        for (int i = 0; i < s1.length(); i++) {
            String rotated = s1.substring(i) + s1.substring(0, i);
            if (rotated.equals(s2)) {
                return true;
            }
        }
        
        return false;
    }
}
```
</details>

---

## 📌 핵심 정리

### 문자 접근
```java
char ch = str.charAt(0);              // 첫 문자
char last = str.charAt(str.length()-1);// 마지막 문자

// ⚠️ 주의: 인덱스는 0부터 length()-1까지
```

### 위치 찾기
```java
int idx = str.indexOf("text");        // 첫 번째 위치
int lastIdx = str.lastIndexOf("text");// 마지막 위치
// 못 찾으면 -1 반환!

// 존재 확인
if (str.indexOf("text") != -1) { }    // 방법 1
if (str.contains("text")) { }         // 방법 2 (더 직관적)
```

### 부분 문자열
```java
String sub1 = str.substring(3);       // 3부터 끝까지
String sub2 = str.substring(3, 7);    // 3~6 (7은 미포함!)

// ⚠️ 주의: 끝 인덱스는 포함 안 됨!
```

### 길이 확인
```java
int len = str.length();               // 메서드 (괄호 필요!)
// 배열: arr.length (필드, 괄호 없음)
```

### 일반적인 실수
```java
// ❌
str.charAt(str.length())              // 인덱스 초과!
str.substring(-1)                     // 음수 불가!
if (str == "text") { }                // 참조 비교!

// ✅
str.charAt(str.length() - 1)          // 마지막 문자
str.substring(0, str.length())        // 전체
if (str.equals("text")) { }           // 내용 비교
```

---

**다음 문서:** [String 04. 변환과 치환](./String-04-변환과치환.md)
