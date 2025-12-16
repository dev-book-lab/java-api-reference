# String 06. StringBuilder & StringBuffer

> 가변 문자열 처리의 모든 것 - 성능 최적화의 핵심  
> Java API Reference

---

## 📑 목차

1. [왜 StringBuilder가 필요한가?](#1-왜-stringbuilder가-필요한가)
2. [StringBuilder 완전 정복](#2-stringbuilder-완전-정복)
3. [StringBuffer와의 차이](#3-stringbuffer와의-차이)
4. [성능 비교와 최적화](#4-성능-비교와-최적화)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. 왜 StringBuilder가 필요한가?

### 1.1 String 연결의 문제점

```java
public class StringConcatProblem {
    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        
        String result = "";
        for (int i = 0; i < 10000; i++) {
            result += i;  // 매번 새 객체 생성!
        }
        
        long end = System.currentTimeMillis();
        System.out.println("Time: " + (end - start) + "ms");
        // 약 800~1500ms (매우 느림!)
    }
}
```

**문제 분석:**
```
반복 1: "" → "0" (새 객체)
반복 2: "0" → "01" (새 객체)
반복 3: "01" → "012" (새 객체)
...
반복 10000: 새 객체 생성

총 10000개의 String 객체 생성!
```

**메모리 구조:**
```
[Heap Memory]
"" (버려짐)
"0" (버려짐)
"01" (버려짐)
"012" (버려짐)
...
"0123456789..." (최종 결과)

→ GC 부담 증가, 메모리 낭비
```

### 1.2 StringBuilder로 해결

```java
public class StringBuilderSolution {
    public static void main(String[] args) {
        long start = System.currentTimeMillis();
        
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 10000; i++) {
            sb.append(i);  // 내부 버퍼만 수정!
        }
        String result = sb.toString();
        
        long end = System.currentTimeMillis();
        System.out.println("Time: " + (end - start) + "ms");
        // 약 2~5ms (300배 이상 빠름!)
    }
}
```

**동작 원리:**
```
StringBuilder 내부:
[버퍼: ___________] (초기 크기 16)

append(0): [0__________]
append(1): [01_________]
append(2): [012________]
...

버퍼가 부족하면 자동 확장!
최종적으로 toString() 한 번만 호출
```

---

## 2. StringBuilder 완전 정복

### 2.1 생성자

```java
public class StringBuilderConstructors {
    public static void main(String[] args) {
        // 1. 기본 생성자 (초기 용량 16)
        StringBuilder sb1 = new StringBuilder();
        System.out.println("Capacity: " + sb1.capacity());  // 16
        
        
        // 2. 초기 용량 지정
        StringBuilder sb2 = new StringBuilder(100);
        System.out.println("Capacity: " + sb2.capacity());  // 100
        
        
        // 3. 문자열로 초기화
        StringBuilder sb3 = new StringBuilder("Hello");
        System.out.println(sb3.toString());  // "Hello"
        System.out.println("Capacity: " + sb3.capacity());  // 21 (16 + 5)
        
        
        // 4. CharSequence로 초기화
        CharSequence cs = "World";
        StringBuilder sb4 = new StringBuilder(cs);
        System.out.println(sb4.toString());  // "World"
    }
}
```

**용량 계산:**
```java
// String으로 생성 시: 문자열 길이 + 16
new StringBuilder("Hello")  // capacity = 5 + 16 = 21
```

### 2.2 append() - 추가

```java
public class AppendMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        
        // 문자열 추가
        sb.append("Hello");
        sb.append(" ");
        sb.append("World");
        System.out.println(sb);  // "Hello World"
        
        
        // 체이닝
        sb = new StringBuilder();
        sb.append("Java")
          .append(" ")
          .append("Programming");
        System.out.println(sb);  // "Java Programming"
        
        
        // 다양한 타입
        sb = new StringBuilder();
        sb.append(123);          // int
        sb.append(3.14);         // double
        sb.append(true);         // boolean
        sb.append('!');          // char
        System.out.println(sb);  // "1233.14true!"
        
        
        // 배열 추가
        char[] chars = {'a', 'b', 'c'};
        sb = new StringBuilder();
        sb.append(chars);
        System.out.println(sb);  // "abc"
        
        
        // 부분 배열
        sb = new StringBuilder();
        sb.append(chars, 1, 2);  // offset=1, length=2
        System.out.println(sb);  // "bc"
    }
}
```

### 2.3 insert() - 삽입

```java
public class InsertMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello World");
        
        // 특정 위치에 삽입
        sb.insert(6, "Java ");
        System.out.println(sb);  // "Hello Java World"
        
        
        // 다양한 타입 삽입
        sb = new StringBuilder("ab");
        sb.insert(1, 'X');       // "aXb"
        sb.insert(0, "Start-");  // "Start-aXb"
        sb.insert(sb.length(), "-End");  // "Start-aXb-End"
        System.out.println(sb);
        
        
        // 배열 삽입
        char[] chars = {'1', '2', '3'};
        sb = new StringBuilder("ac");
        sb.insert(1, chars, 0, 2);  // offset=0, length=2
        System.out.println(sb);  // "a12c"
    }
}
```

### 2.4 delete() / deleteCharAt() - 삭제

```java
public class DeleteMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello World");
        
        // 범위 삭제 (start ~ end-1)
        sb.delete(5, 11);  // " World" 삭제
        System.out.println(sb);  // "Hello"
        
        
        // 한 문자 삭제
        sb = new StringBuilder("abcde");
        sb.deleteCharAt(2);  // 'c' 삭제
        System.out.println(sb);  // "abde"
        
        
        // 전체 삭제
        sb = new StringBuilder("Hello");
        sb.delete(0, sb.length());
        System.out.println(sb.length());  // 0
        System.out.println("[" + sb + "]");  // "[]"
    }
}
```

### 2.5 replace() - 치환

```java
public class ReplaceMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello World");
        
        // 범위 치환 (start ~ end-1)
        sb.replace(6, 11, "Java");
        System.out.println(sb);  // "Hello Java"
        
        
        // 더 긴 문자열로 치환
        sb = new StringBuilder("abc");
        sb.replace(1, 2, "12345");  // 'b' → "12345"
        System.out.println(sb);  // "a12345c"
        
        
        // 더 짧은 문자열로 치환
        sb = new StringBuilder("abcde");
        sb.replace(1, 4, "X");  // "bcd" → "X"
        System.out.println(sb);  // "aXe"
    }
}
```

### 2.6 reverse() - 뒤집기

```java
public class ReverseMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello");
        sb.reverse();
        System.out.println(sb);  // "olleH"
        
        
        // 팰린드롬 확인
        String word = "racecar";
        String reversed = new StringBuilder(word).reverse().toString();
        System.out.println(word.equals(reversed));  // true
        
        
        // 숫자 뒤집기
        int num = 12345;
        String reversedNum = new StringBuilder(String.valueOf(num))
                              .reverse()
                              .toString();
        System.out.println(reversedNum);  // "54321"
    }
}
```

### 2.7 capacity() / length() / setLength()

```java
public class CapacityMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        
        System.out.println("Capacity: " + sb.capacity());  // 16
        System.out.println("Length: " + sb.length());      // 0
        
        
        // 문자열 추가
        sb.append("Hello");
        System.out.println("Capacity: " + sb.capacity());  // 16
        System.out.println("Length: " + sb.length());      // 5
        
        
        // 용량 초과 시 자동 확장
        sb.append("0123456789AB");  // 5 + 12 = 17 > 16
        System.out.println("Capacity: " + sb.capacity());  // 34 (16*2+2)
        
        
        // 길이 변경
        sb = new StringBuilder("Hello");
        sb.setLength(3);
        System.out.println(sb);  // "Hel"
        
        sb.setLength(10);  // 길이 증가 (null 문자로 채움)
        System.out.println("[" + sb + "]");  // "[Hel       ]"
    }
}
```

**용량 확장 공식:**
```java
새 용량 = (현재 용량 * 2) + 2
```

### 2.8 ensureCapacity() / trimToSize()

```java
public class CapacityControl {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder();
        System.out.println("Initial: " + sb.capacity());  // 16
        
        // 최소 용량 보장
        sb.ensureCapacity(100);
        System.out.println("After ensure: " + sb.capacity());  // 100 이상
        
        
        // 문자열 추가 후
        sb.append("Hello");
        System.out.println("Length: " + sb.length());      // 5
        System.out.println("Capacity: " + sb.capacity());  // 100+
        
        
        // 여유 공간 제거
        sb.trimToSize();
        System.out.println("After trim: " + sb.capacity());  // 5
    }
}
```

### 2.9 charAt() / setCharAt() - 문자 접근

```java
public class CharMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello");
        
        // 문자 읽기
        char ch = sb.charAt(1);
        System.out.println(ch);  // 'e'
        
        
        // 문자 변경 (String과 다르게 가능!)
        sb.setCharAt(1, 'a');
        System.out.println(sb);  // "Hallo"
        
        
        // 모든 문자 대문자로
        for (int i = 0; i < sb.length(); i++) {
            char c = sb.charAt(i);
            sb.setCharAt(i, Character.toUpperCase(c));
        }
        System.out.println(sb);  // "HALLO"
    }
}
```

### 2.10 substring() / toString()

```java
public class ConversionMethods {
    public static void main(String[] args) {
        StringBuilder sb = new StringBuilder("Hello World");
        
        // 부분 문자열 추출 (String 반환)
        String sub1 = sb.substring(6);
        String sub2 = sb.substring(0, 5);
        System.out.println(sub1);  // "World"
        System.out.println(sub2);  // "Hello"
        
        
        // String 변환
        String str = sb.toString();
        System.out.println(str.getClass());  // class java.lang.String
        
        
        // StringBuilder는 변경 안 됨
        System.out.println(sb);  // "Hello World"
    }
}
```

---

## 3. StringBuffer와의 차이

### 3.1 기본 비교

```java
public class BufferVsBuilder {
    public static void main(String[] args) {
        // StringBuilder: 동기화 X, 빠름
        StringBuilder builder = new StringBuilder();
        builder.append("Hello");
        
        // StringBuffer: 동기화 O, 느림
        StringBuffer buffer = new StringBuffer();
        buffer.append("Hello");
        
        
        // API는 거의 동일
        System.out.println(builder.toString());  // "Hello"
        System.out.println(buffer.toString());   // "Hello"
    }
}
```

| 특징 | StringBuilder | StringBuffer |
|---|---|---|
| **동기화** | ❌ 없음 | ✅ 있음 |
| **속도** | 빠름 | 느림 (약 10~20%) |
| **스레드 안전** | ❌ 불안전 | ✅ 안전 |
| **권장 사용** | 단일 스레드 | 멀티 스레드 |
| **도입 버전** | Java 5 | Java 1.0 |

### 3.2 멀티스레드 환경

```java
public class ThreadSafety {
    public static void main(String[] args) throws InterruptedException {
        // StringBuilder: 스레드 불안전
        StringBuilder sb = new StringBuilder();
        
        Thread t1 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                sb.append("A");
            }
        });
        
        Thread t2 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                sb.append("B");
            }
        });
        
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        
        System.out.println("Length: " + sb.length());
        // 2000이 아닐 수 있음! (동기화 문제)
        
        
        // StringBuffer: 스레드 안전
        StringBuffer sbf = new StringBuffer();
        
        Thread t3 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                sbf.append("A");
            }
        });
        
        Thread t4 = new Thread(() -> {
            for (int i = 0; i < 1000; i++) {
                sbf.append("B");
            }
        });
        
        t3.start();
        t4.start();
        t3.join();
        t4.join();
        
        System.out.println("Length: " + sbf.length());
        // 항상 2000 (동기화 보장)
    }
}
```

### 3.3 사용 가이드

```java
public class UsageGuide {
    // ✅ 좋은 예: 단일 스레드 → StringBuilder
    public String buildMessage() {
        StringBuilder sb = new StringBuilder();
        sb.append("Hello");
        sb.append(" ");
        sb.append("World");
        return sb.toString();
    }
    
    
    // ✅ 좋은 예: 멀티스레드 공유 → StringBuffer
    private StringBuffer sharedBuffer = new StringBuffer();
    
    public void addLog(String message) {
        // 여러 스레드에서 호출 가능
        sharedBuffer.append(message).append("\n");
    }
    
    
    // ❌ 나쁜 예: 단일 스레드인데 StringBuffer
    public String slow() {
        StringBuffer sb = new StringBuffer();  // 불필요한 동기화 비용!
        sb.append("Slow");
        return sb.toString();
    }
    
    
    // ❌ 나쁜 예: 멀티스레드인데 StringBuilder
    private StringBuilder unsafeBuilder = new StringBuilder();
    
    public void unsafeAdd(String message) {
        // 여러 스레드에서 호출 시 문제 발생!
        unsafeBuilder.append(message);
    }
}
```

**사용 기준:**
- **99%의 경우**: StringBuilder (빠름)
- **멀티스레드에서 공유**: StringBuffer (안전)
- **확실하지 않으면**: StringBuilder (대부분 단일 스레드)

---

## 4. 성능 비교와 최적화

### 4.1 성능 벤치마크

```java
public class PerformanceBenchmark {
    public static void main(String[] args) {
        int iterations = 10000;
        
        // 1. String +
        long start1 = System.currentTimeMillis();
        String s = "";
        for (int i = 0; i < iterations; i++) {
            s += "a";
        }
        long end1 = System.currentTimeMillis();
        System.out.println("String +: " + (end1 - start1) + "ms");
        
        
        // 2. StringBuilder
        long start2 = System.currentTimeMillis();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < iterations; i++) {
            sb.append("a");
        }
        String result = sb.toString();
        long end2 = System.currentTimeMillis();
        System.out.println("StringBuilder: " + (end2 - start2) + "ms");
        
        
        // 3. StringBuffer
        long start3 = System.currentTimeMillis();
        StringBuffer sbf = new StringBuffer();
        for (int i = 0; i < iterations; i++) {
            sbf.append("a");
        }
        String result2 = sbf.toString();
        long end3 = System.currentTimeMillis();
        System.out.println("StringBuffer: " + (end3 - start3) + "ms");
    }
}
```

**결과 예시 (10000번 반복):**
```
String +:     800~1500ms  (매우 느림)
StringBuilder:    2~5ms   (빠름)
StringBuffer:    3~6ms    (약간 느림)
```

### 4.2 초기 용량 설정의 중요성

```java
public class CapacityOptimization {
    public static void main(String[] args) {
        int target = 1000000;  // 100만 글자
        
        // 1. 기본 용량 (16)
        long start1 = System.currentTimeMillis();
        StringBuilder sb1 = new StringBuilder();
        for (int i = 0; i < target; i++) {
            sb1.append("a");
        }
        long end1 = System.currentTimeMillis();
        System.out.println("Default capacity: " + (end1 - start1) + "ms");
        
        
        // 2. 충분한 초기 용량
        long start2 = System.currentTimeMillis();
        StringBuilder sb2 = new StringBuilder(target);
        for (int i = 0; i < target; i++) {
            sb2.append("a");
        }
        long end2 = System.currentTimeMillis();
        System.out.println("Pre-sized capacity: " + (end2 - start2) + "ms");
        
        
        // 결과: 초기 용량 설정 시 약 2배 빠름!
    }
}
```

**최적화 팁:**
```java
// ❌ 나쁜 예
StringBuilder sb = new StringBuilder();  // capacity=16
// 많은 재할당 발생!

// ✅ 좋은 예
int expectedSize = 1000;
StringBuilder sb = new StringBuilder(expectedSize);
// 재할당 최소화!
```

### 4.3 실전 최적화 패턴

```java
public class OptimizationPatterns {
    // 패턴 1: 예상 크기 계산
    public String createLargeString(List<String> items) {
        // 평균 길이 * 개수로 예상 크기 계산
        int avgLength = 50;
        int expectedSize = items.size() * avgLength;
        
        StringBuilder sb = new StringBuilder(expectedSize);
        for (String item : items) {
            sb.append(item);
        }
        return sb.toString();
    }
    
    
    // 패턴 2: 재사용
    private StringBuilder reusableSb = new StringBuilder(1000);
    
    public String format(String name, int age) {
        reusableSb.setLength(0);  // 초기화
        reusableSb.append("Name: ").append(name)
                  .append(", Age: ").append(age);
        return reusableSb.toString();
    }
    
    
    // 패턴 3: 큰 결과 예상 시 충분한 버퍼
    public String processLargeData() {
        StringBuilder sb = new StringBuilder(100000);  // 100KB
        // ... 대량 처리
        return sb.toString();
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 문자열 압축

```java
// 연속된 같은 문자를 개수로 압축
// 예: "aaabbcccc" → "a3b2c4"
public class Problem1 {
    public static String compress(String str) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(compress("aaabbcccc"));
        // "a3b2c4"
        
        System.out.println(compress("abc"));
        // "a1b1c1" 또는 "abc" (압축이 더 길면 원본 반환)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static String compress(String str) {
        if (str == null || str.length() <= 1) {
            return str;
        }
        
        StringBuilder sb = new StringBuilder();
        int count = 1;
        
        for (int i = 1; i < str.length(); i++) {
            if (str.charAt(i) == str.charAt(i - 1)) {
                count++;
            } else {
                sb.append(str.charAt(i - 1)).append(count);
                count = 1;
            }
        }
        
        // 마지막 문자
        sb.append(str.charAt(str.length() - 1)).append(count);
        
        // 압축이 더 길면 원본 반환
        return sb.length() < str.length() ? sb.toString() : str;
    }
}
```
</details>

---

### 문제 2: 괄호 제거

```java
// 문자열에서 괄호와 그 안의 내용 제거
// 예: "Hello (world) test (abc)" → "Hello  test "
public class Problem2 {
    public static String removeParentheses(String str) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(removeParentheses("Hello (world) test (abc)"));
        // "Hello  test "
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static String removeParentheses(String str) {
        StringBuilder sb = new StringBuilder();
        int depth = 0;
        
        for (char ch : str.toCharArray()) {
            if (ch == '(') {
                depth++;
            } else if (ch == ')') {
                depth--;
            } else if (depth == 0) {
                sb.append(ch);
            }
        }
        
        return sb.toString();
    }
}
```
</details>

---

### 문제 3: 중복 문자 제거

```java
// 연속된 중복 문자를 하나만 남기고 제거
// 예: "aaabbbaac" → "abac"
public class Problem3 {
    public static String removeDuplicates(String str) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(removeDuplicates("aaabbbaac"));
        // "abac"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static String removeDuplicates(String str) {
        if (str == null || str.length() <= 1) {
            return str;
        }
        
        StringBuilder sb = new StringBuilder();
        sb.append(str.charAt(0));
        
        for (int i = 1; i < str.length(); i++) {
            if (str.charAt(i) != str.charAt(i - 1)) {
                sb.append(str.charAt(i));
            }
        }
        
        return sb.toString();
    }
}
```
</details>

---

### 문제 4: HTML 태그 생성기

```java
// 주어진 태그명과 내용으로 HTML 태그 생성
public class Problem4 {
    public static String createTag(String tag, String content, 
                                   Map<String, String> attrs) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        Map<String, String> attrs = new HashMap<>();
        attrs.put("class", "btn");
        attrs.put("id", "submit");
        
        System.out.println(createTag("button", "Click Me", attrs));
        // <button class="btn" id="submit">Click Me</button>
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem4 {
    public static String createTag(String tag, String content, 
                                   Map<String, String> attrs) {
        StringBuilder sb = new StringBuilder();
        
        sb.append("<").append(tag);
        
        // 속성 추가
        if (attrs != null) {
            for (Map.Entry<String, String> entry : attrs.entrySet()) {
                sb.append(" ")
                  .append(entry.getKey())
                  .append("=\"")
                  .append(entry.getValue())
                  .append("\"");
            }
        }
        
        sb.append(">")
          .append(content)
          .append("</")
          .append(tag)
          .append(">");
        
        return sb.toString();
    }
}
```
</details>

---

## 📌 핵심 정리

### StringBuilder vs String
```java
// ❌ 느림: 반복문에서 String +
String s = "";
for (int i = 0; i < 1000; i++) {
    s += i;  // 1000개 객체 생성!
}

// ✅ 빠름: StringBuilder
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 1000; i++) {
    sb.append(i);  // 객체 1개!
}
String s = sb.toString();
```

### StringBuilder vs StringBuffer
```java
// ✅ 단일 스레드 (99%): StringBuilder
StringBuilder sb = new StringBuilder();

// ✅ 멀티스레드 공유: StringBuffer
StringBuffer sbf = new StringBuffer();
```

### 주요 메서드
```java
sb.append("text")         // 추가
sb.insert(pos, "text")    // 삽입
sb.delete(start, end)     // 삭제
sb.replace(start, end, "text")  // 치환
sb.reverse()              // 뒤집기
sb.toString()             // String 변환
```

### 최적화
```java
// ❌ 비효율
StringBuilder sb = new StringBuilder();  // capacity=16

// ✅ 효율적
int size = 1000;
StringBuilder sb = new StringBuilder(size);
```

### 주의사항
```java
// StringBuilder는 불변이 아님!
StringBuilder sb = new StringBuilder("Hello");
sb.append(" World");
System.out.println(sb);  // "Hello World" (변경됨!)

// String 변환 필수
String str = sb.toString();
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 05. 분리와 결합](./String-05-분리와결합.md) | [다음: 07. 실전 패턴 →](./String-07-실전패턴.md)**

</div>
