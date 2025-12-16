# String 01. 기본 개념 (Immutable & Intern Pool)

> String의 핵심 특성과 내부 동작 원리 완전 정복  
> Java API Reference

---

## 📑 목차

1. [String이란?](#1-string이란)
2. [Immutable (불변성)](#2-immutable-불변성)
3. [String Intern Pool](#3-string-intern-pool)
4. [메모리 구조와 성능](#4-메모리-구조와-성능)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. String이란?

### 기본 개념

```java
String str = "Hello";
```

- **참조 타입**: String은 클래스 (기본 타입 아님)
- **불변 객체**: 한 번 생성되면 내용 변경 불가
- **가장 많이 쓰는 클래스**: Java에서 특별 대우

### String은 객체인데 왜 특별한가?

```java
// 일반 객체: new 필수
ArrayList<Integer> list = new ArrayList<>();

// String: 리터럴로 생성 가능 (특별 대우!)
String s1 = "Hello";  // 간편한 문법
String s2 = new String("Hello");  // 일반적인 방법
```

**특별한 이유:**
- 문자열은 너무 자주 쓰여서 편의 문법 제공
- `+` 연산자 오버로딩 가능 (유일!)
- 리터럴 풀 관리

---

## 2. Immutable (불변성)

### 2.1 불변성의 의미

**String은 한 번 만들어지면 절대 변경할 수 없다!**

```java
String str = "Hello";
str = str + " World";  // 기존 "Hello"가 변경되는 게 아니다!

// 실제 동작:
// 1. "Hello" 객체 생성 (메모리 주소: 0x100)
// 2. " World" 추가 → 새로운 "Hello World" 객체 생성 (0x200)
// 3. str 변수가 0x200을 가리킴
// 4. 기존 0x100 "Hello"는 그대로 존재 (GC 대상)
```

### 2.2 불변성 증명 코드

#### 실험 1: 문자열 변경 시도
```java
public class StringImmutableTest {
    public static void main(String[] args) {
        String original = "Hello";
        String modified = original.concat(" World");
        
        System.out.println("original: " + original);    // "Hello" (변경 안 됨!)
        System.out.println("modified: " + modified);    // "Hello World"
        
        // original과 modified는 완전히 다른 객체
        System.out.println(original == modified);  // false
    }
}
```

**출력:**
```
original: Hello
modified: Hello World
false
```

#### 실험 2: 메모리 주소 확인
```java
public class StringMemoryTest {
    public static void main(String[] args) {
        String str = "Hello";
        System.out.println("Before: " + System.identityHashCode(str));
        
        str = str + " World";  // 새 객체 생성
        System.out.println("After:  " + System.identityHashCode(str));
        
        // 해시코드가 다름 = 다른 객체
    }
}
```

**출력 예시:**
```
Before: 2018699554
After:  1311053135
```

### 2.3 왜 불변으로 만들었을까?

#### 장점 1: 스레드 안전성
```java
// 여러 스레드가 동시에 접근해도 안전!
String shared = "Thread Safe";

// Thread 1
new Thread(() -> {
    String s = shared.toUpperCase();  // 새 객체 생성
}).start();

// Thread 2  
new Thread(() -> {
    String s = shared.toLowerCase();  // 새 객체 생성
}).start();

// shared는 절대 변하지 않음 → 동기화 불필요!
```

#### 장점 2: 보안
```java
// 비밀번호 검증 함수
public boolean validatePassword(String password) {
    // password가 불변이므로
    // 검증 중에 다른 스레드가 바꿀 수 없음!
    return password.equals("secret123");
}
```

#### 장점 3: HashMap 키로 안전
```java
Map<String, Integer> map = new HashMap<>();
String key = "age";
map.put(key, 25);

// key가 변경 가능하다면?
// key = "name";  // 만약 key 내용이 바뀐다면 HashMap 망가짐!

// 하지만 String은 불변이므로 안전!
```

#### 장점 4: String Pool 가능
```java
String s1 = "Hello";
String s2 = "Hello";
// 같은 객체 재사용 가능 (불변이니까!)
System.out.println(s1 == s2);  // true
```

### 2.4 불변성의 단점

#### 문제: 문자열 연결 시 비효율
```java
String result = "";
for (int i = 0; i < 10000; i++) {
    result += i;  // 매번 새 객체 생성! (10000개 생성)
}
// 엄청 느림!
```

**메모리 상황:**
```
"" → "0" → "01" → "012" → "0123" → ... (10000번 객체 생성)
```

**해결책: StringBuilder 사용**
```java
StringBuilder sb = new StringBuilder();
for (int i = 0; i < 10000; i++) {
    sb.append(i);  // 내부 버퍼 수정 (객체 재생성 X)
}
String result = sb.toString();  // 마지막에 한 번만 String 생성
```

### 2.5 실습: 불변성 체감하기

```java
public class ImmutablePractice {
    public static void main(String[] args) {
        // 실습 1: replace는 새 객체를 반환
        String s1 = "Hello World";
        s1.replace("World", "Java");  // ❌ 결과를 안 받음!
        System.out.println(s1);  // "Hello World" (변경 안 됨)
        
        String s2 = s1.replace("World", "Java");  // ✅ 새 객체 받기
        System.out.println(s2);  // "Hello Java"
        
        
        // 실습 2: toLowerCase도 새 객체
        String upper = "HELLO";
        upper.toLowerCase();  // ❌ 결과 안 받음
        System.out.println(upper);  // "HELLO" (그대로)
        
        String lower = upper.toLowerCase();  // ✅
        System.out.println(lower);  // "hello"
        
        
        // 실습 3: concat도 새 객체
        String first = "Hello";
        first.concat(" World");  // ❌
        System.out.println(first);  // "Hello"
        
        String combined = first.concat(" World");  // ✅
        System.out.println(combined);  // "Hello World"
    }
}
```

**핵심 교훈:**
> String의 모든 메서드는 **원본을 변경하지 않고** 새로운 String을 반환한다!

---

## 3. String Intern Pool

### 3.1 개념

**String Pool (Intern Pool):**
- **JVM의 특별한 메모리 영역** (Heap 내부 또는 Metaspace)
- 문자열 리터럴을 저장하는 공간
- **같은 내용의 문자열은 하나만 저장** (메모리 절약)

### 3.2 리터럴 vs new의 차이

```java
public class StringPoolTest {
    public static void main(String[] args) {
        // 리터럴 방식: String Pool 사용
        String s1 = "Hello";
        String s2 = "Hello";
        
        System.out.println(s1 == s2);  // true (같은 객체!)
        
        
        // new 방식: Heap에 새 객체 생성
        String s3 = new String("Hello");
        String s4 = new String("Hello");
        
        System.out.println(s3 == s4);  // false (다른 객체!)
        
        
        // 리터럴 vs new
        System.out.println(s1 == s3);  // false
        System.out.println(s1.equals(s3));  // true (내용은 같음)
    }
}
```

### 3.3 메모리 구조 시각화

```
[String Pool (특별한 영역)]
┌─────────────────┐
│  "Hello" (0x100)│ ← s1, s2가 모두 가리킴
│  "World" (0x101)│
│  "Java"  (0x102)│
└─────────────────┘

[Heap (일반 객체 영역)]
┌─────────────────┐
│  "Hello" (0x200)│ ← s3
│  "Hello" (0x201)│ ← s4
│  ... 기타 객체  │
└─────────────────┘
```

### 3.4 intern() 메서드

**역할: new로 만든 String을 Pool에 등록**

```java
public class InternTest {
    public static void main(String[] args) {
        String s1 = "Hello";  // Pool에 저장
        
        String s2 = new String("Hello");  // Heap에 생성
        System.out.println(s1 == s2);  // false
        
        String s3 = s2.intern();  // Pool에서 찾아서 반환!
        System.out.println(s1 == s3);  // true (같은 객체!)
        
        
        // 동작 방식:
        // 1. intern() 호출
        // 2. Pool에 "Hello" 있나 확인
        // 3. 있으면 → Pool의 객체 반환
        // 4. 없으면 → Pool에 등록 후 반환
    }
}
```

### 3.5 실전 예제: 메모리 절약

```java
public class PoolMemorySaving {
    public static void main(String[] args) {
        // 케이스 1: new 사용 (메모리 낭비)
        String[] arr1 = new String[1000];
        for (int i = 0; i < 1000; i++) {
            arr1[i] = new String("duplicate");  // 1000개 객체 생성!
        }
        
        
        // 케이스 2: 리터럴 사용 (메모리 절약)
        String[] arr2 = new String[1000];
        for (int i = 0; i < 1000; i++) {
            arr2[i] = "duplicate";  // 객체 1개만 생성!
        }
        
        
        // 케이스 3: 외부 입력을 Pool에 등록
        Scanner sc = new Scanner(System.in);
        Set<String> uniqueStrings = new HashSet<>();
        
        for (int i = 0; i < 100; i++) {
            String input = sc.next();  // Heap에 생성됨
            uniqueStrings.add(input.intern());  // Pool 사용으로 메모리 절약
        }
    }
}
```

### 3.6 컴파일 타임 최적화

**컴파일러가 자동으로 리터럴을 합침!**

```java
public class CompileTimeOptimization {
    public static void main(String[] args) {
        String s1 = "Hello" + "World";  // 컴파일 시 "HelloWorld"로 변환
        String s2 = "HelloWorld";
        
        System.out.println(s1 == s2);  // true!
        
        
        // 하지만 변수가 포함되면 런타임 연결
        String hello = "Hello";
        String s3 = hello + "World";  // 런타임에 새 객체 생성
        
        System.out.println(s1 == s3);  // false
    }
}
```

**바이트코드 확인:**
```java
// 소스 코드:
String s1 = "Hello" + "World";

// 컴파일 후:
String s1 = "HelloWorld";  // 최적화됨!
```

### 3.7 주의사항

#### 주의 1: Pool은 GC 대상이 아님 (Java 7 이전)
```java
// Java 6 이전: PermGen에 저장 (GC 안 됨!)
for (int i = 0; i < 1000000; i++) {
    String s = ("String" + i).intern();  // PermGen 터짐!
}

// Java 7+: Heap으로 이동 (GC 가능)
// 하지만 여전히 신중하게 사용!
```

#### 주의 2: == 비교 조심
```java
String input = scanner.next();  // 사용자 입력
if (input == "exit") {  // ❌ 거의 항상 false!
    // 절대 실행 안 됨
}

if (input.equals("exit")) {  // ✅ 올바른 비교
    // 정상 동작
}
```

---

## 4. 메모리 구조와 성능

### 4.1 String 내부 구조 (Java 9+)

```java
public final class String {
    private final byte[] value;  // 실제 문자 데이터 (Java 9+)
    private final byte coder;    // 인코딩 정보 (LATIN1 or UTF16)
    private int hash;            // 캐싱된 해시코드
    
    // Java 8 이하:
    // private final char[] value;
}
```

**왜 byte[]로 바뀌었나?**
```java
// Java 8: char[] 사용 (2바이트 * 문자 수)
String s1 = "Hello";  // 5 * 2 = 10바이트

// Java 9+: 똑똑한 압축
String s2 = "Hello";  // 순수 ASCII → 1바이트 * 5 = 5바이트 (절약!)
String s3 = "안녕";    // 한글 포함 → 2바이트 * 2 = 4바이트
```

### 4.2 메모리 사용량 비교

```java
public class StringMemoryTest {
    public static void main(String[] args) {
        // 케이스 1: 리터럴 재사용
        String[] arr1 = new String[1000];
        for (int i = 0; i < 1000; i++) {
            arr1[i] = "SAME";  // 객체 1개 (약 40바이트)
        }
        // 총 메모리: 40바이트 + (1000 * 참조 크기)
        
        
        // 케이스 2: new 사용
        String[] arr2 = new String[1000];
        for (int i = 0; i < 1000; i++) {
            arr2[i] = new String("SAME");  // 객체 1000개
        }
        // 총 메모리: 40바이트 * 1000 = 40,000바이트
        
        // 메모리 차이: 약 1000배!
    }
}
```

### 4.3 성능 측정 실습

```java
public class StringPerformanceTest {
    public static void main(String[] args) {
        int n = 10000;
        
        // 테스트 1: String 연결 (느림)
        long start1 = System.nanoTime();
        String s = "";
        for (int i = 0; i < n; i++) {
            s += i;  // 매번 새 객체 생성
        }
        long end1 = System.nanoTime();
        System.out.println("String +=: " + (end1 - start1) / 1_000_000 + "ms");
        
        
        // 테스트 2: StringBuilder (빠름)
        long start2 = System.nanoTime();
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < n; i++) {
            sb.append(i);  // 버퍼 수정
        }
        String result = sb.toString();
        long end2 = System.nanoTime();
        System.out.println("StringBuilder: " + (end2 - start2) / 1_000_000 + "ms");
    }
}
```

**실행 결과 (예시):**
```
String +=: 1250ms
StringBuilder: 2ms
```

### 4.4 언제 뭘 쓸까?

| 상황 | 추천 | 이유 |
|---|---|---|
| 문자열 고정 | `String` | 불변성 장점 활용 |
| 한두 번 연결 | `String s = a + b` | 컴파일러 최적화 |
| 반복문 안에서 연결 | `StringBuilder` | 성능 압도적 |
| 멀티스레드 | `StringBuffer` | 동기화 지원 |
| 단순 출력 | `System.out.println` | 간편함 |

---

## 5. 실전 연습 문제

### 문제 1: 불변성 이해하기

```java
public class Problem1 {
    public static void main(String[] args) {
        String s = "Hello";
        modify(s);
        System.out.println(s);  // 출력은?
    }
    
    public static void modify(String str) {
        str = str + " World";
    }
}
```

**정답 및 해설:**
<details>
<summary>정답 보기</summary>

출력: `Hello`

**이유:**
- String은 불변
- `modify` 메서드 안에서 `str`에 새 객체 할당
- 하지만 main의 `s`는 여전히 원래 객체를 가리킨다
- Java는 Call by Value (참조의 복사본 전달)

```java
// 메모리 상황:
// main의 s: 0x100 ("Hello")
// modify의 str: 0x100 → 0x200 ("Hello World")
// main의 s: 여전히 0x100 ("Hello")
```
</details>

---

### 문제 2: String Pool 판별

```java
public class Problem2 {
    public static void main(String[] args) {
        String s1 = "Java";
        String s2 = "Java";
        String s3 = new String("Java");
        String s4 = new String("Java").intern();
        
        System.out.println(s1 == s2);  // (a)
        System.out.println(s1 == s3);  // (b)
        System.out.println(s1 == s4);  // (c)
        System.out.println(s3 == s4);  // (d)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

- (a) `true` - 둘 다 Pool의 같은 객체
- (b) `false` - s1은 Pool, s3는 Heap
- (c) `true` - s4는 intern()으로 Pool 객체 반환
- (d) `false` - s3는 Heap, s4는 Pool

**메모리 그림:**
```
[Pool]
"Java" (0x100) ← s1, s2, s4

[Heap]
"Java" (0x200) ← s3
```
</details>

---

### 문제 3: 성능 최적화

```java
// 다음 코드를 최적화하세요
public class Problem3 {
    public static String createString() {
        String result = "";
        for (int i = 0; i < 1000; i++) {
            result += "Line " + i + "\n";
        }
        return result;
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static String createString() {
        StringBuilder sb = new StringBuilder();
        for (int i = 0; i < 1000; i++) {
            sb.append("Line ").append(i).append("\n");
        }
        return sb.toString();
    }
}
```

**개선 사항:**
- String 연결 → StringBuilder 사용
- 성능: 약 100~1000배 향상
- 메모리: 객체 생성 1000번 → 1번
</details>

---

### 문제 4: 실전 응용

**시나리오:** 로그 파일에서 중복 URL 제거하기

```java
import java.util.*;

public class Problem4 {
    public static void main(String[] args) {
        // 100만 개의 로그 (중복 많음)
        List<String> logs = new ArrayList<>();
        for (int i = 0; i < 1000000; i++) {
            logs.add("https://example.com/page" + (i % 100));
        }
        
        // TODO: 중복 제거하면서 메모리 최소화
        // 힌트: intern() 사용
        
        Set<String> uniqueUrls = removeDuplicates(logs);
        System.out.println("Unique URLs: " + uniqueUrls.size());
    }
    
    public static Set<String> removeDuplicates(List<String> logs) {
        // 여기에 코드 작성
        return null;
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static Set<String> removeDuplicates(List<String> logs) {
    Set<String> uniqueUrls = new HashSet<>();
    
    for (String log : logs) {
        // intern()으로 Pool 사용 → 메모리 절약
        uniqueUrls.add(log.intern());
    }
    
    return uniqueUrls;
}
```

**효과:**
- intern() 없이: 100만 개 String 객체 (중복 포함)
- intern() 사용: 100개 String 객체 (Pool에 공유)
- 메모리 절약: 약 10,000배!
</details>

---

## 📌 핵심 정리

### Immutable (불변성)
- ✅ String은 생성 후 **절대 변경 불가**
- ✅ 모든 메서드는 **새 String 반환**
- ✅ 장점: 스레드 안전, 보안, HashMap 키
- ⚠️ 단점: 연결 작업 시 비효율 (→ StringBuilder 사용)

### String Pool
- ✅ 리터럴은 **Pool에 자동 저장**
- ✅ 같은 내용은 **객체 하나만 생성**
- ✅ `intern()`으로 **Pool 등록 가능**
- ⚠️ `==` 비교는 **Pool에서만 유효**

### 메모리 & 성능
- ✅ 리터럴 재사용으로 **메모리 절약**
- ✅ Java 9+는 **자동 압축** (byte[])
- ⚠️ 반복 연결은 **StringBuilder 필수**

### 실전 팁
```java
// ✅ 좋은 예
String s1 = "Hello";
String s2 = "Hello";  // Pool 재사용
if (s1.equals(s2)) { }  // equals 사용

// ❌ 나쁜 예
String s3 = new String("Hello");  // 불필요한 객체 생성
if (s1 == s3) { }  // == 사용 (거의 항상 틀림)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. 생성과 비교 →](./String-02-생성과비교.md)**

</div>
