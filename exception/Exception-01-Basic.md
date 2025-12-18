# Exception 01. Exception 기본 완전 정복

> 예외 처리의 기초 - Exception 계층과 처리 방법  
> Java Exception

---

## 📑 목차

1. [Exception 소개](#1-exception-소개)
2. [Exception 계층](#2-exception-계층)
3. [try-catch-finally](#3-try-catch-finally)
4. [throws와 throw](#4-throws와-throw)
5. [Checked vs Unchecked](#5-checked-vs-unchecked)
6. [다중 catch](#6-다중-catch)
7. [try-with-resources](#7-try-with-resources)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Exception 소개

### 1.1 왜 Exception인가?

```java
public class WhyException {
    // ❌ Exception 없이 (오류 코드 방식)
    public static int divideBad(int a, int b) {
        if (b == 0) {
            return -999;  // 에러 코드?
        }
        return a / b;
    }
    
    // ✅ Exception 사용
    public static int divideGood(int a, int b) {
        if (b == 0) {
            throw new ArithmeticException("0으로 나눌 수 없습니다");
        }
        return a / b;
    }
    
    public static void main(String[] args) {
        System.out.println("=== Exception이란? ===\n");
        
        System.out.println("📌 정의");
        System.out.println("프로그램 실행 중 발생하는 예외적인 상황\n");
        
        System.out.println("📌 오류 코드 방식의 문제");
        System.out.println("❌ 에러 코드와 정상 값 구분 어려움");
        System.out.println("❌ 호출자가 체크하지 않을 수 있음");
        System.out.println("❌ 에러 정보 부족\n");
        
        System.out.println("📌 Exception의 장점");
        System.out.println("✅ 명확한 에러 표현");
        System.out.println("✅ 강제 처리 (Checked Exception)");
        System.out.println("✅ 풍부한 에러 정보");
        System.out.println("✅ 에러 전파\n");
        
        
        // Exception 사용
        try {
            int result = divideGood(10, 0);
            System.out.println("결과: " + result);
        } catch (ArithmeticException e) {
            System.out.println("오류 발생: " + e.getMessage());
        }
    }
}
```

### 1.2 Exception의 구성

```java
public class ExceptionStructure {
    public static void main(String[] args) {
        System.out.println("=== Exception 구조 ===\n");
        
        try {
            throw new IllegalArgumentException("잘못된 인자");
        } catch (IllegalArgumentException e) {
            System.out.println("📌 Exception 정보\n");
            
            // 메시지
            System.out.println("메시지: " + e.getMessage());
            
            // 타입
            System.out.println("타입: " + e.getClass().getName());
            
            // 스택 트레이스
            System.out.println("\n스택 트레이스:");
            e.printStackTrace();
            
            // Cause
            System.out.println("\nCause: " + e.getCause());
        }
    }
}
```

---

## 2. Exception 계층

### 2.1 Throwable 계층

```java
public class ThrowableHierarchy {
    public static void main(String[] args) {
        System.out.println("=== Throwable 계층 ===\n");
        
        System.out.println("java.lang.Throwable");
        System.out.println("├── Error");
        System.out.println("│   ├── OutOfMemoryError");
        System.out.println("│   ├── StackOverflowError");
        System.out.println("│   └── VirtualMachineError");
        System.out.println("│");
        System.out.println("└── Exception");
        System.out.println("    ├── RuntimeException (Unchecked)");
        System.out.println("    │   ├── NullPointerException");
        System.out.println("    │   ├── IllegalArgumentException");
        System.out.println("    │   ├── IndexOutOfBoundsException");
        System.out.println("    │   └── ArithmeticException");
        System.out.println("    │");
        System.out.println("    └── IOException (Checked)");
        System.out.println("        ├── FileNotFoundException");
        System.out.println("        └── EOFException\n");
        
        System.out.println("📌 Error");
        System.out.println("- 시스템 레벨 오류");
        System.out.println("- 복구 불가능");
        System.out.println("- catch 하지 않음\n");
        
        System.out.println("📌 Exception");
        System.out.println("- 애플리케이션 레벨 오류");
        System.out.println("- 복구 가능");
        System.out.println("- catch 해서 처리");
    }
}
```

### 2.2 주요 Exception

```java
public class CommonExceptions {
    public static void main(String[] args) {
        System.out.println("=== 주요 Exception ===\n");
        
        // NullPointerException
        System.out.println("1. NullPointerException");
        try {
            String str = null;
            str.length();
        } catch (NullPointerException e) {
            System.out.println("   null 참조\n");
        }
        
        
        // IllegalArgumentException
        System.out.println("2. IllegalArgumentException");
        try {
            setAge(-1);
        } catch (IllegalArgumentException e) {
            System.out.println("   " + e.getMessage() + "\n");
        }
        
        
        // IndexOutOfBoundsException
        System.out.println("3. IndexOutOfBoundsException");
        try {
            int[] arr = new int[5];
            arr[10] = 1;
        } catch (IndexOutOfBoundsException e) {
            System.out.println("   인덱스 범위 초과\n");
        }
        
        
        // ArithmeticException
        System.out.println("4. ArithmeticException");
        try {
            int result = 10 / 0;
        } catch (ArithmeticException e) {
            System.out.println("   " + e.getMessage() + "\n");
        }
        
        
        // NumberFormatException
        System.out.println("5. NumberFormatException");
        try {
            int num = Integer.parseInt("abc");
        } catch (NumberFormatException e) {
            System.out.println("   숫자 변환 실패\n");
        }
        
        
        // ClassCastException
        System.out.println("6. ClassCastException");
        try {
            Object obj = "String";
            Integer num = (Integer) obj;
        } catch (ClassCastException e) {
            System.out.println("   타입 변환 실패");
        }
    }
    
    static void setAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("나이는 음수일 수 없습니다");
        }
    }
}
```

---

## 3. try-catch-finally

### 3.1 기본 구조

```java
import java.io.*;

public class TryCatchFinally {
    public static void basicTry() {
        try {
            System.out.println("try 블록 실행");
            int result = 10 / 0;  // ArithmeticException
            System.out.println("이 줄은 실행 안 됨");
        } catch (ArithmeticException e) {
            System.out.println("catch 블록 실행");
            System.out.println("예외: " + e.getMessage());
        } finally {
            System.out.println("finally 블록 실행 (항상)");
        }
    }
    
    public static void withFinally() {
        FileReader reader = null;
        try {
            reader = new FileReader("file.txt");
            // 파일 읽기
        } catch (FileNotFoundException e) {
            System.out.println("파일 없음");
        } finally {
            // 자원 정리
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    System.out.println("닫기 실패");
                }
            }
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== try-catch-finally ===\n");
        
        System.out.println("📌 기본 try-catch:");
        basicTry();
        
        System.out.println("\n📌 finally 특징:");
        System.out.println("- 항상 실행");
        System.out.println("- 자원 정리에 사용");
        System.out.println("- return보다 나중에 실행\n");
        
        withFinally();
    }
}
```

### 3.2 catch 없는 try-finally

```java
public class TryFinally {
    public static void withoutCatch() throws Exception {
        try {
            System.out.println("작업 시작");
            throw new Exception("오류 발생");
        } finally {
            System.out.println("정리 작업");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== try-finally ===\n");
        
        try {
            withoutCatch();
        } catch (Exception e) {
            System.out.println("외부에서 처리: " + e.getMessage());
        }
        
        System.out.println("\n📌 catch 없는 try-finally");
        System.out.println("- finally만 실행");
        System.out.println("- 예외는 호출자에게 전파");
    }
}
```

---

## 4. throws와 throw

### 4.1 throw - 예외 발생

```java
public class ThrowKeyword {
    public static void validateAge(int age) {
        if (age < 0) {
            throw new IllegalArgumentException("나이는 음수일 수 없습니다");
        }
        if (age > 150) {
            throw new IllegalArgumentException("나이가 너무 큽니다");
        }
        System.out.println("유효한 나이: " + age);
    }
    
    public static void processPayment(double amount) {
        if (amount <= 0) {
            throw new IllegalArgumentException("금액은 양수여야 합니다");
        }
        System.out.println("결제: " + amount + "원");
    }
    
    public static void main(String[] args) {
        System.out.println("=== throw ===\n");
        
        try {
            validateAge(25);
            validateAge(-5);
        } catch (IllegalArgumentException e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        try {
            processPayment(0);
        } catch (IllegalArgumentException e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        System.out.println("\n📌 throw");
        System.out.println("- 예외를 직접 발생");
        System.out.println("- throw new ExceptionType(message)");
    }
}
```

### 4.2 throws - 예외 선언

```java
import java.io.*;

public class ThrowsKeyword {
    // Checked Exception - throws 필수
    public static void readFile(String path) throws IOException {
        FileReader reader = new FileReader(path);
        reader.close();
    }
    
    public static void processFile(String path) throws IOException {
        readFile(path);  // IOException 전파
    }
    
    // Unchecked Exception - throws 선택
    public static void divide(int a, int b) throws ArithmeticException {
        if (b == 0) {
            throw new ArithmeticException("0으로 나눌 수 없습니다");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== throws ===\n");
        
        // Checked Exception 처리
        try {
            processFile("test.txt");
        } catch (IOException e) {
            System.out.println("파일 처리 실패");
        }
        
        System.out.println("\n📌 throws");
        System.out.println("- 예외를 호출자에게 전파");
        System.out.println("- Checked Exception은 필수");
        System.out.println("- Unchecked Exception은 선택");
    }
}
```

---

## 5. Checked vs Unchecked

### 5.1 Checked Exception

```java
import java.io.*;

public class CheckedException {
    // Checked Exception - 컴파일 타임 체크
    public static void readFile() throws IOException {
        FileReader reader = new FileReader("file.txt");
        reader.close();
    }
    
    public static void parseDate() throws java.text.ParseException {
        java.text.SimpleDateFormat sdf = new java.text.SimpleDateFormat("yyyy-MM-dd");
        sdf.parse("2024-01-01");
    }
    
    public static void main(String[] args) {
        System.out.println("=== Checked Exception ===\n");
        
        // 반드시 처리 필요
        try {
            readFile();
        } catch (IOException e) {
            System.out.println("파일 읽기 실패");
        }
        
        System.out.println("\n📌 특징");
        System.out.println("- Exception의 직접 하위 클래스");
        System.out.println("- 컴파일 타임 체크");
        System.out.println("- 반드시 처리 (try-catch 또는 throws)");
        System.out.println("- 복구 가능한 예외\n");
        
        System.out.println("📌 주요 Checked Exception");
        System.out.println("- IOException");
        System.out.println("- SQLException");
        System.out.println("- ClassNotFoundException");
        System.out.println("- ParseException");
    }
}
```

### 5.2 Unchecked Exception

```java
public class UncheckedException {
    public static void causeNPE() {
        String str = null;
        str.length();  // NullPointerException
    }
    
    public static void causeIAE() {
        throw new IllegalArgumentException("잘못된 인자");
    }
    
    public static void causeIOOBE() {
        int[] arr = new int[5];
        arr[10] = 1;  // IndexOutOfBoundsException
    }
    
    public static void main(String[] args) {
        System.out.println("=== Unchecked Exception ===\n");
        
        // 처리 선택적
        try {
            causeNPE();
        } catch (NullPointerException e) {
            System.out.println("NPE 발생");
        }
        
        System.out.println("\n📌 특징");
        System.out.println("- RuntimeException의 하위 클래스");
        System.out.println("- 컴파일 타임 체크 안 함");
        System.out.println("- 처리 선택적");
        System.out.println("- 프로그래밍 오류\n");
        
        System.out.println("📌 주요 Unchecked Exception");
        System.out.println("- NullPointerException");
        System.out.println("- IllegalArgumentException");
        System.out.println("- IndexOutOfBoundsException");
        System.out.println("- ArithmeticException");
        System.out.println("- ClassCastException");
    }
}
```

---

## 6. 다중 catch

### 6.1 여러 catch 블록

```java
public class MultipleCatch {
    public static void process(String input) {
        try {
            // 여러 예외 발생 가능
            int num = Integer.parseInt(input);  // NumberFormatException
            int result = 100 / num;              // ArithmeticException
            
            int[] arr = new int[5];
            arr[num] = result;                   // ArrayIndexOutOfBoundsException
            
        } catch (NumberFormatException e) {
            System.out.println("숫자 변환 실패: " + input);
        } catch (ArithmeticException e) {
            System.out.println("0으로 나눌 수 없습니다");
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("인덱스 범위 초과");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 다중 catch ===\n");
        
        process("abc");   // NumberFormatException
        process("0");     // ArithmeticException
        process("10");    // ArrayIndexOutOfBoundsException
        
        System.out.println("\n📌 특징");
        System.out.println("- 위에서 아래로 체크");
        System.out.println("- 구체적인 예외를 먼저");
        System.out.println("- 첫 번째 매치만 실행");
    }
}
```

### 6.2 Multi-catch (Java 7+)

```java
public class MultiCatch {
    public static void process(String input, int divisor) {
        try {
            int num = Integer.parseInt(input);
            int result = num / divisor;
            System.out.println("결과: " + result);
            
        } catch (NumberFormatException | ArithmeticException e) {
            System.out.println("입력 또는 연산 오류: " + e.getMessage());
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Multi-catch ===\n");
        
        process("abc", 10);  // NumberFormatException
        process("10", 0);    // ArithmeticException
        
        System.out.println("\n📌 Multi-catch");
        System.out.println("- Java 7+ 기능");
        System.out.println("- | 로 여러 예외 처리");
        System.out.println("- 코드 중복 감소");
        System.out.println("- 상속 관계면 컴파일 에러");
    }
}
```

### 6.3 catch 순서

```java
public class CatchOrder {
    public static void badOrder() {
        try {
            throw new IllegalArgumentException();
        } catch (Exception e) {          // 넓은 범위
            System.out.println("Exception");
        } 
        // catch (IllegalArgumentException e) {  // 컴파일 에러!
        //     System.out.println("IAE");
        // }
    }
    
    public static void goodOrder() {
        try {
            throw new IllegalArgumentException();
        } catch (IllegalArgumentException e) {  // 구체적
            System.out.println("IAE");
        } catch (RuntimeException e) {          // 중간
            System.out.println("RuntimeException");
        } catch (Exception e) {                 // 넓은 범위
            System.out.println("Exception");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== catch 순서 ===\n");
        
        goodOrder();
        
        System.out.println("\n📌 규칙");
        System.out.println("✅ 구체적 → 일반적 순서");
        System.out.println("✅ 하위 → 상위 클래스");
        System.out.println("❌ 상위 클래스 먼저 = 컴파일 에러");
    }
}
```

---

## 7. try-with-resources

### 7.1 기본 사용

```java
import java.io.*;

public class TryWithResources {
    // ❌ 전통적 방식
    public static void oldWay() {
        BufferedReader reader = null;
        try {
            reader = new BufferedReader(new FileReader("file.txt"));
            String line = reader.readLine();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (reader != null) {
                try {
                    reader.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    
    // ✅ try-with-resources (Java 7+)
    public static void newWay() {
        try (BufferedReader reader = new BufferedReader(new FileReader("file.txt"))) {
            String line = reader.readLine();
        } catch (IOException e) {
            e.printStackTrace();
        }
        // 자동으로 close() 호출됨!
    }
    
    public static void main(String[] args) {
        System.out.println("=== try-with-resources ===\n");
        
        System.out.println("📌 장점");
        System.out.println("- 자동 자원 해제");
        System.out.println("- 코드 간결");
        System.out.println("- finally 불필요");
        System.out.println("- 예외 누락 방지\n");
        
        System.out.println("📌 조건");
        System.out.println("- AutoCloseable 구현");
        System.out.println("- try() 안에 선언");
    }
}
```

### 7.2 여러 자원

```java
import java.io.*;

public class MultipleResources {
    public static void copyFile() {
        try (
            FileInputStream input = new FileInputStream("source.txt");
            FileOutputStream output = new FileOutputStream("dest.txt")
        ) {
            byte[] buffer = new byte[1024];
            int bytesRead;
            while ((bytesRead = input.read(buffer)) != -1) {
                output.write(buffer, 0, bytesRead);
            }
        } catch (IOException e) {
            System.out.println("파일 복사 실패");
        }
        // input, output 자동 close
    }
    
    public static void main(String[] args) {
        System.out.println("=== 여러 자원 ===\n");
        
        System.out.println("📌 여러 자원");
        System.out.println("- 세미콜론으로 구분");
        System.out.println("- 역순으로 close");
        System.out.println("- output.close() → input.close()");
    }
}
```

### 7.3 커스텀 AutoCloseable

```java
class MyResource implements AutoCloseable {
    private String name;
    
    public MyResource(String name) {
        this.name = name;
        System.out.println(name + " 열림");
    }
    
    public void doSomething() {
        System.out.println(name + " 작업 수행");
    }
    
    @Override
    public void close() {
        System.out.println(name + " 닫힘");
    }
}

public class CustomAutoCloseable {
    public static void main(String[] args) {
        System.out.println("=== 커스텀 AutoCloseable ===\n");
        
        try (MyResource r1 = new MyResource("Resource-1");
             MyResource r2 = new MyResource("Resource-2")) {
            
            r1.doSomething();
            r2.doSomething();
            
        }  // 자동 close: r2 → r1
        
        System.out.println("\n📌 AutoCloseable");
        System.out.println("- close() 메서드 구현");
        System.out.println("- try-with-resources 사용 가능");
    }
}
```

---

## 8. 실전 예제

### 8.1 파일 처리

```java
import java.io.*;
import java.nio.file.*;

public class FileHandling {
    public static String readFile(String path) throws IOException {
        try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
            StringBuilder content = new StringBuilder();
            String line;
            
            while ((line = reader.readLine()) != null) {
                content.append(line).append("\n");
            }
            
            return content.toString();
        }
    }
    
    public static void writeFile(String path, String content) throws IOException {
        try (BufferedWriter writer = new BufferedWriter(new FileWriter(path))) {
            writer.write(content);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 파일 처리 ===\n");
        
        try {
            // 쓰기
            writeFile("test.txt", "Hello, World!");
            System.out.println("파일 쓰기 완료");
            
            // 읽기
            String content = readFile("test.txt");
            System.out.println("파일 내용: " + content);
            
        } catch (IOException e) {
            System.out.println("파일 처리 오류: " + e.getMessage());
        }
    }
}
```

### 8.2 입력 검증

```java
public class InputValidation {
    public static class User {
        private String name;
        private int age;
        private String email;
        
        public User(String name, int age, String email) {
            validateName(name);
            validateAge(age);
            validateEmail(email);
            
            this.name = name;
            this.age = age;
            this.email = email;
        }
        
        private void validateName(String name) {
            if (name == null || name.trim().isEmpty()) {
                throw new IllegalArgumentException("이름은 필수입니다");
            }
            if (name.length() < 2) {
                throw new IllegalArgumentException("이름은 2자 이상이어야 합니다");
            }
        }
        
        private void validateAge(int age) {
            if (age < 0) {
                throw new IllegalArgumentException("나이는 음수일 수 없습니다");
            }
            if (age > 150) {
                throw new IllegalArgumentException("나이가 유효하지 않습니다");
            }
        }
        
        private void validateEmail(String email) {
            if (email == null || !email.contains("@")) {
                throw new IllegalArgumentException("이메일 형식이 올바르지 않습니다");
            }
        }
        
        @Override
        public String toString() {
            return "User{name='" + name + "', age=" + age + ", email='" + email + "'}";
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 입력 검증 ===\n");
        
        // 유효한 입력
        try {
            User user1 = new User("Alice", 25, "alice@example.com");
            System.out.println("✅ " + user1);
        } catch (IllegalArgumentException e) {
            System.out.println("❌ " + e.getMessage());
        }
        
        // 무효한 입력
        try {
            User user2 = new User("", 25, "alice@example.com");
            System.out.println("✅ " + user2);
        } catch (IllegalArgumentException e) {
            System.out.println("❌ " + e.getMessage());
        }
        
        try {
            User user3 = new User("Bob", -5, "bob@example.com");
            System.out.println("✅ " + user3);
        } catch (IllegalArgumentException e) {
            System.out.println("❌ " + e.getMessage());
        }
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 안전한 나눗셈

```java
// 0으로 나누기를 처리하는 안전한 나눗셈
public class Problem1 {
    public static Double safeDivide(int a, int b) {
        // 구현: 0으로 나누면 null 반환
        return null;
    }
    
    public static void main(String[] args) {
        System.out.println(safeDivide(10, 2));  // 5.0
        System.out.println(safeDivide(10, 0));  // null
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static Double safeDivide(int a, int b) {
    try {
        return (double) a / b;
    } catch (ArithmeticException e) {
        return null;
    }
}
```
</details>

---

### 문제 2: 파일 읽기

```java
// 파일을 읽고 내용 반환, 실패 시 빈 문자열
import java.io.*;

public class Problem2 {
    public static String readFileOrEmpty(String path) {
        // 구현: try-with-resources 사용
        return "";
    }
    
    public static void main(String[] args) {
        String content = readFileOrEmpty("test.txt");
        System.out.println(content);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static String readFileOrEmpty(String path) {
    try (BufferedReader reader = new BufferedReader(new FileReader(path))) {
        StringBuilder sb = new StringBuilder();
        String line;
        while ((line = reader.readLine()) != null) {
            sb.append(line).append("\n");
        }
        return sb.toString();
    } catch (IOException e) {
        return "";
    }
}
```
</details>

---

### 문제 3: 다중 예외 처리

```java
// 여러 예외를 적절히 처리
public class Problem3 {
    public static void processInput(String input) {
        // 구현:
        // 1. 숫자로 변환 (NumberFormatException)
        // 2. 100을 나눔 (ArithmeticException)
        // 3. 배열 접근 (ArrayIndexOutOfBoundsException)
    }
    
    public static void main(String[] args) {
        processInput("10");   // 정상
        processInput("abc");  // NumberFormatException
        processInput("0");    // ArithmeticException
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void processInput(String input) {
    try {
        int num = Integer.parseInt(input);
        int result = 100 / num;
        
        int[] arr = new int[5];
        arr[result] = 1;
        
        System.out.println("성공: " + result);
        
    } catch (NumberFormatException e) {
        System.out.println("숫자 변환 실패");
    } catch (ArithmeticException e) {
        System.out.println("0으로 나눌 수 없음");
    } catch (ArrayIndexOutOfBoundsException e) {
        System.out.println("배열 인덱스 초과");
    }
}
```
</details>

---

## 📌 핵심 정리

### Exception 계층
```
Throwable
├── Error (시스템 오류, 복구 불가)
└── Exception
    ├── RuntimeException (Unchecked)
    └── 기타 (Checked)
```

### try-catch-finally
```java
try {
    // 예외 발생 가능 코드
} catch (ExceptionType e) {
    // 예외 처리
} finally {
    // 항상 실행 (자원 정리)
}
```

### throws vs throw
```java
// throws: 예외 선언 (호출자에게 전파)
public void method() throws IOException { }

// throw: 예외 발생
throw new IllegalArgumentException("message");
```

### Checked vs Unchecked
```
Checked (Exception):
- 컴파일 타임 체크
- 반드시 처리 필요
- IOException, SQLException

Unchecked (RuntimeException):
- 런타임 체크
- 처리 선택적
- NullPointerException, IllegalArgumentException
```

### try-with-resources
```java
try (Resource r = new Resource()) {
    // 사용
}  // 자동 close()
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Custom Exception →](./Exception-02-Custom.md)**

</div>
