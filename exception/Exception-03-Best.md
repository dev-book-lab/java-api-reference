# Exception 03. Exception Best Practices 완전 정복

> 예외 처리 베스트 프랙티스 - 올바른 예외 처리 방법  
> Java Exception

---

## 📑 목차

1. [예외 처리 원칙](#1-예외-처리-원칙)
2. [예외 설계](#2-예외-설계)
3. [예외 처리 패턴](#3-예외-처리-패턴)
4. [안티 패턴](#4-안티-패턴)
5. [성능 고려사항](#5-성능-고려사항)
6. [로깅과 모니터링](#6-로깅과-모니터링)
7. [테스트](#7-테스트)
8. [실전 시나리오](#8-실전-시나리오)
9. [체크리스트](#9-체크리스트)

---

## 1. 예외 처리 원칙

### 1.1 기본 원칙

```java
public class ExceptionPrinciples {
    // ❌ 나쁜 예 - 예외 무시
    public static void badExample1() {
        try {
            riskyOperation();
        } catch (Exception e) {
            // 아무것도 안 함!
        }
    }
    
    // ❌ 나쁜 예 - 일반적인 Exception catch
    public static void badExample2() {
        try {
            riskyOperation();
        } catch (Exception e) {
            System.out.println("오류 발생");
        }
    }
    
    // ✅ 좋은 예 - 구체적인 예외 처리
    public static void goodExample() {
        try {
            riskyOperation();
        } catch (FileNotFoundException e) {
            System.out.println("파일 없음: " + e.getMessage());
            // 적절한 처리
        } catch (IOException e) {
            System.out.println("IO 오류: " + e.getMessage());
            // 적절한 처리
        }
    }
    
    static void riskyOperation() throws java.io.IOException {}
    
    public static void main(String[] args) {
        System.out.println("=== 예외 처리 원칙 ===\n");
        
        System.out.println("📌 기본 원칙\n");
        
        System.out.println("1. 예외를 무시하지 말 것");
        System.out.println("   - 최소한 로깅");
        System.out.println("   - 적절한 처리\n");
        
        System.out.println("2. 구체적인 예외 catch");
        System.out.println("   - Exception 대신 구체적 타입");
        System.out.println("   - 의미 있는 처리\n");
        
        System.out.println("3. 예외를 문서화");
        System.out.println("   - JavaDoc @throws");
        System.out.println("   - 예외 발생 조건\n");
        
        System.out.println("4. 조기 실패 (Fail-Fast)");
        System.out.println("   - 빨리 실패하고 명확하게");
        System.out.println("   - 디버깅 용이\n");
        
        System.out.println("5. 복구 가능한 상황만 catch");
        System.out.println("   - 복구 불가능하면 전파");
        System.out.println("   - 프로그래밍 오류는 Unchecked");
    }
}
```

### 1.2 Checked vs Unchecked 선택

```java
public class CheckedVsUnchecked {
    // ✅ Checked - 복구 가능한 외부 오류
    public static class FileService {
        public String readFile(String path) throws java.io.IOException {
            // 파일 읽기 - 복구 가능
            return "";
        }
    }
    
    // ✅ Unchecked - 프로그래밍 오류
    public static class Calculator {
        public int divide(int a, int b) {
            if (b == 0) {
                throw new IllegalArgumentException("0으로 나눌 수 없습니다");
            }
            return a / b;
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Checked vs Unchecked ===\n");
        
        System.out.println("📌 Checked Exception 사용");
        System.out.println("✅ 외부 시스템 오류");
        System.out.println("   - 파일, 네트워크, DB");
        System.out.println("✅ 복구 가능한 상황");
        System.out.println("✅ 호출자가 처리해야 하는 경우\n");
        
        System.out.println("📌 Unchecked Exception 사용");
        System.out.println("✅ 프로그래밍 오류");
        System.out.println("   - null, 범위 초과, 잘못된 인자");
        System.out.println("✅ 복구 불가능");
        System.out.println("✅ 어디서든 발생 가능\n");
        
        System.out.println("⚖️ 고려사항");
        System.out.println("- API 사용자 부담");
        System.out.println("- 복구 가능성");
        System.out.println("- 명시성 vs 편의성");
    }
}
```

---

## 2. 예외 설계

### 2.1 명확한 메시지

```java
public class ClearMessages {
    // ❌ 나쁜 예
    public static void badMessages() {
        throw new IllegalArgumentException("오류");  // 뭐가 잘못됐는지 모름
    }
    
    // ✅ 좋은 예
    public static void goodMessages(String email, int age) {
        if (email == null || !email.contains("@")) {
            throw new IllegalArgumentException(
                "유효하지 않은 이메일 형식: " + email + " (@가 필요합니다)");
        }
        
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException(
                "유효하지 않은 나이: " + age + " (0-150 사이여야 합니다)");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 명확한 메시지 ===\n");
        
        System.out.println("📌 좋은 메시지");
        System.out.println("✅ 무엇이 잘못됐는지");
        System.out.println("✅ 왜 잘못됐는지");
        System.out.println("✅ 어떻게 고칠 수 있는지");
        System.out.println("✅ 관련 값 포함\n");
        
        try {
            goodMessages("invalid", -5);
        } catch (IllegalArgumentException e) {
            System.out.println("예시: " + e.getMessage());
        }
    }
}
```

### 2.2 계층적 예외

```java
// 최상위
class ServiceException extends Exception {
    public ServiceException(String message) {
        super(message);
    }
    
    public ServiceException(String message, Throwable cause) {
        super(message, cause);
    }
}

// 도메인별
class UserServiceException extends ServiceException {
    public UserServiceException(String message) {
        super(message);
    }
}

class OrderServiceException extends ServiceException {
    public OrderServiceException(String message) {
        super(message);
    }
}

// 구체적
class DuplicateUserException extends UserServiceException {
    private final String userId;
    
    public DuplicateUserException(String userId) {
        super("이미 존재하는 사용자: " + userId);
        this.userId = userId;
    }
    
    public String getUserId() { return userId; }
}

public class HierarchicalExceptions {
    public static void registerUser(String userId) throws UserServiceException {
        if (userExists(userId)) {
            throw new DuplicateUserException(userId);
        }
    }
    
    static boolean userExists(String userId) { return true; }
    
    public static void main(String[] args) {
        System.out.println("=== 계층적 예외 ===\n");
        
        System.out.println("📌 장점");
        System.out.println("✅ 레벨별 처리");
        System.out.println("✅ 명확한 분류");
        System.out.println("✅ 유지보수 용이\n");
        
        try {
            registerUser("alice");
        } catch (DuplicateUserException e) {
            System.out.println("중복: " + e.getUserId());
        } catch (UserServiceException e) {
            System.out.println("사용자 서비스 오류: " + e.getMessage());
        } catch (ServiceException e) {
            System.out.println("서비스 오류: " + e.getMessage());
        }
    }
}
```

---

## 3. 예외 처리 패턴

### 3.1 변환 (Translation)

```java
import java.sql.*;

class DataAccessException extends RuntimeException {
    public DataAccessException(String message, Throwable cause) {
        super(message, cause);
    }
}

class UserRepository {
    // ❌ 나쁜 예 - SQLException 노출
    public void saveBad(String username) throws SQLException {
        // JDBC 코드
        throw new SQLException("Connection failed");
    }
    
    // ✅ 좋은 예 - 도메인 예외로 변환
    public void saveGood(String username) {
        try {
            // JDBC 코드
            throw new SQLException("Connection failed");
        } catch (SQLException e) {
            throw new DataAccessException(
                "사용자 저장 실패: " + username, e);
        }
    }
}

public class TranslationPattern {
    public static void main(String[] args) {
        System.out.println("=== 변환 패턴 ===\n");
        
        System.out.println("📌 목적");
        System.out.println("- 하위 레벨 예외 숨기기");
        System.out.println("- 도메인 예외로 변환");
        System.out.println("- 구현 세부사항 감추기\n");
        
        UserRepository repo = new UserRepository();
        
        try {
            repo.saveGood("alice");
        } catch (DataAccessException e) {
            System.out.println("오류: " + e.getMessage());
            System.out.println("원인: " + e.getCause().getClass().getSimpleName());
        }
    }
}
```

### 3.2 복구 (Recovery)

```java
public class RecoveryPattern {
    public static String readFileWithFallback(String primaryPath, String fallbackPath) {
        try {
            return readFile(primaryPath);
        } catch (java.io.IOException e) {
            System.out.println("주 파일 읽기 실패, 대체 파일 사용");
            try {
                return readFile(fallbackPath);
            } catch (java.io.IOException e2) {
                System.out.println("대체 파일도 실패");
                return "";  // 기본값
            }
        }
    }
    
    public static String readWithRetry(String path, int maxRetries) {
        int attempt = 0;
        while (attempt < maxRetries) {
            try {
                return readFile(path);
            } catch (java.io.IOException e) {
                attempt++;
                System.out.println("재시도 " + attempt + "/" + maxRetries);
                
                if (attempt >= maxRetries) {
                    throw new RuntimeException("최대 재시도 횟수 초과", e);
                }
                
                try {
                    Thread.sleep(1000 * attempt);  // 점진적 대기
                } catch (InterruptedException ie) {
                    Thread.currentThread().interrupt();
                    throw new RuntimeException("중단됨", ie);
                }
            }
        }
        return "";
    }
    
    static String readFile(String path) throws java.io.IOException {
        throw new java.io.IOException("File not found");
    }
    
    public static void main(String[] args) {
        System.out.println("=== 복구 패턴 ===\n");
        
        System.out.println("1. Fallback");
        readFileWithFallback("primary.txt", "fallback.txt");
        
        System.out.println("\n2. Retry");
        try {
            readWithRetry("file.txt", 3);
        } catch (RuntimeException e) {
            System.out.println("최종 실패");
        }
    }
}
```

### 3.3 정리 (Cleanup)

```java
import java.io.*;

public class CleanupPattern {
    // ❌ 나쁜 예 - 자원 누수 위험
    public static void badCleanup() throws IOException {
        FileWriter writer = new FileWriter("file.txt");
        writer.write("data");
        writer.close();  // 예외 발생 시 실행 안 됨!
    }
    
    // ✅ 좋은 예 - try-finally
    public static void goodCleanup1() throws IOException {
        FileWriter writer = null;
        try {
            writer = new FileWriter("file.txt");
            writer.write("data");
        } finally {
            if (writer != null) {
                try {
                    writer.close();
                } catch (IOException e) {
                    // 로깅
                }
            }
        }
    }
    
    // ✅ 최고 - try-with-resources
    public static void goodCleanup2() throws IOException {
        try (FileWriter writer = new FileWriter("file.txt")) {
            writer.write("data");
        }  // 자동 close
    }
    
    public static void main(String[] args) {
        System.out.println("=== 정리 패턴 ===\n");
        
        System.out.println("📌 중요성");
        System.out.println("- 자원 누수 방지");
        System.out.println("- 메모리 관리");
        System.out.println("- 연결 해제\n");
        
        System.out.println("📌 방법");
        System.out.println("1. try-finally (레거시)");
        System.out.println("2. try-with-resources (권장)");
    }
}
```

---

## 4. 안티 패턴

### 4.1 예외 무시

```java
public class IgnoringExceptions {
    // ❌ 최악 - 완전 무시
    public static void worst() {
        try {
            riskyOperation();
        } catch (Exception e) {
            // 아무것도 안 함
        }
    }
    
    // ❌ 나쁨 - 출력만
    public static void bad() {
        try {
            riskyOperation();
        } catch (Exception e) {
            e.printStackTrace();  // 로그 남기고 무시
        }
    }
    
    // ✅ 좋음 - 적절한 처리
    public static void good() {
        try {
            riskyOperation();
        } catch (Exception e) {
            System.err.println("오류: " + e.getMessage());
            // 1. 로깅
            // 2. 복구 시도 또는
            // 3. 상위로 전파
            throw new RuntimeException("작업 실패", e);
        }
    }
    
    static void riskyOperation() throws Exception {
        throw new Exception("오류");
    }
    
    public static void main(String[] args) {
        System.out.println("=== 예외 무시 안티패턴 ===\n");
        
        System.out.println("❌ 하지 말 것");
        System.out.println("- 빈 catch 블록");
        System.out.println("- printStackTrace만");
        System.out.println("- 의미 없는 로그\n");
        
        System.out.println("✅ 해야 할 것");
        System.out.println("- 적절한 로깅");
        System.out.println("- 복구 또는 전파");
        System.out.println("- 의미 있는 처리");
    }
}
```

### 4.2 과도한 catch

```java
public class OverCatching {
    // ❌ 나쁜 예 - 너무 넓게 catch
    public static void bad() {
        try {
            specificOperation();
        } catch (Exception e) {  // 모든 예외 잡음
            System.out.println("오류");
        }
    }
    
    // ✅ 좋은 예 - 구체적으로 catch
    public static void good() {
        try {
            specificOperation();
        } catch (IllegalArgumentException e) {
            System.out.println("잘못된 인자: " + e.getMessage());
        } catch (IllegalStateException e) {
            System.out.println("잘못된 상태: " + e.getMessage());
        }
    }
    
    static void specificOperation() {
        throw new IllegalArgumentException("오류");
    }
    
    public static void main(String[] args) {
        System.out.println("=== 과도한 catch ===\n");
        
        System.out.println("❌ Exception catch 문제");
        System.out.println("- 의도하지 않은 예외까지 잡음");
        System.out.println("- NPE, ClassCastException도 잡힘");
        System.out.println("- 디버깅 어려움\n");
        
        System.out.println("✅ 구체적 예외 catch");
        System.out.println("- 예상된 예외만");
        System.out.println("- 명확한 처리");
        System.out.println("- 디버깅 용이");
    }
}
```

### 4.3 예외로 흐름 제어

```java
public class FlowControlAntipattern {
    // ❌ 나쁜 예 - 예외로 흐름 제어
    public static Integer parseBad(String str) {
        try {
            return Integer.parseInt(str);
        } catch (NumberFormatException e) {
            return null;  // 정상 흐름인데 예외 사용
        }
    }
    
    // ✅ 좋은 예 - 정상 흐름
    public static Integer parseGood(String str) {
        if (str == null || !str.matches("-?\\d+")) {
            return null;
        }
        return Integer.parseInt(str);
    }
    
    public static void main(String[] args) {
        System.out.println("=== 흐름 제어 안티패턴 ===\n");
        
        System.out.println("❌ 예외로 흐름 제어");
        System.out.println("- 느림 (예외 생성 비용)");
        System.out.println("- 의도 불명확");
        System.out.println("- 디버깅 방해\n");
        
        System.out.println("✅ 정상 흐름");
        System.out.println("- 빠름");
        System.out.println("- 명확함");
        System.out.println("- 예외는 예외적 상황에만");
        
        // 성능 비교
        long start = System.nanoTime();
        for (int i = 0; i < 10000; i++) {
            parseBad("abc");
        }
        long bad = System.nanoTime() - start;
        
        start = System.nanoTime();
        for (int i = 0; i < 10000; i++) {
            parseGood("abc");
        }
        long good = System.nanoTime() - start;
        
        System.out.println("\n성능 (10000회):");
        System.out.printf("예외 사용: %d ms%n", bad / 1_000_000);
        System.out.printf("정상 흐름: %d ms%n", good / 1_000_000);
    }
}
```

---

## 5. 성능 고려사항

### 5.1 예외 생성 비용

```java
public class ExceptionCost {
    static class LightException extends Exception {
        public LightException(String message) {
            super(message);
        }
        
        // 스택 트레이스 생성 안 함
        @Override
        public synchronized Throwable fillInStackTrace() {
            return this;
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 예외 생성 비용 ===\n");
        
        int iterations = 100000;
        
        // 일반 예외
        long start = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            Exception e = new Exception("message");
        }
        long normal = System.nanoTime() - start;
        
        // 경량 예외
        start = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            LightException e = new LightException("message");
        }
        long light = System.nanoTime() - start;
        
        System.out.printf("일반 예외: %d ms%n", normal / 1_000_000);
        System.out.printf("경량 예외: %d ms%n", light / 1_000_000);
        
        System.out.println("\n📌 비용 요소");
        System.out.println("- 스택 트레이스 생성");
        System.out.println("- 객체 생성");
        System.out.println("- 예외 전파\n");
        
        System.out.println("📌 최적화");
        System.out.println("- 예외를 흐름 제어로 사용 안 함");
        System.out.println("- 필요한 경우만 catch");
        System.out.println("- 풀링 (극히 드물게)");
    }
}
```

---

## 6. 로깅과 모니터링

### 6.1 적절한 로깅

```java
public class ProperLogging {
    public static void processOrder(String orderId) {
        try {
            System.out.println("주문 처리 시작: " + orderId);
            
            validateOrder(orderId);
            processPayment(orderId);
            shipOrder(orderId);
            
            System.out.println("주문 처리 완료: " + orderId);
            
        } catch (IllegalArgumentException e) {
            // 경고 레벨 - 예상된 오류
            System.err.println("경고 [" + orderId + "]: " + e.getMessage());
            
        } catch (Exception e) {
            // 에러 레벨 - 예상 밖 오류
            System.err.println("오류 [" + orderId + "]: " + e.getMessage());
            e.printStackTrace();
            
            // 재시도, 알림 등
        }
    }
    
    static void validateOrder(String orderId) {
        if (orderId == null) {
            throw new IllegalArgumentException("orderId is null");
        }
    }
    
    static void processPayment(String orderId) {}
    static void shipOrder(String orderId) {}
    
    public static void main(String[] args) {
        System.out.println("=== 로깅 ===\n");
        
        System.out.println("📌 로그 레벨");
        System.out.println("DEBUG: 상세 정보");
        System.out.println("INFO: 일반 정보");
        System.out.println("WARN: 경고 (복구 가능)");
        System.out.println("ERROR: 오류 (복구 불가)\n");
        
        System.out.println("📌 로그 내용");
        System.out.println("- 컨텍스트 (ID, 사용자)");
        System.out.println("- 타임스탬프");
        System.out.println("- 스택 트레이스");
        System.out.println("- 원인 예외\n");
        
        processOrder("ORD-001");
        processOrder(null);
    }
}
```

### 6.2 모니터링

```java
import java.util.concurrent.atomic.*;

class ExceptionMonitor {
    private static final AtomicLong totalExceptions = new AtomicLong();
    private static final java.util.concurrent.ConcurrentHashMap<String, AtomicLong> 
        exceptionCounts = new java.util.concurrent.ConcurrentHashMap<>();
    
    public static void recordException(Throwable t) {
        totalExceptions.incrementAndGet();
        
        String type = t.getClass().getSimpleName();
        exceptionCounts.computeIfAbsent(type, k -> new AtomicLong())
                      .incrementAndGet();
    }
    
    public static void printStats() {
        System.out.println("\n=== 예외 통계 ===");
        System.out.println("총 예외: " + totalExceptions.get());
        
        System.out.println("\n타입별:");
        exceptionCounts.forEach((type, count) -> 
            System.out.println("  " + type + ": " + count.get()));
    }
}

public class ExceptionMonitoring {
    public static void operation1() {
        try {
            throw new IllegalArgumentException("오류 1");
        } catch (Exception e) {
            ExceptionMonitor.recordException(e);
        }
    }
    
    public static void operation2() {
        try {
            throw new IllegalStateException("오류 2");
        } catch (Exception e) {
            ExceptionMonitor.recordException(e);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 예외 모니터링 ===\n");
        
        // 시뮬레이션
        for (int i = 0; i < 5; i++) operation1();
        for (int i = 0; i < 3; i++) operation2();
        for (int i = 0; i < 2; i++) operation1();
        
        ExceptionMonitor.printStats();
        
        System.out.println("\n📌 모니터링 지표");
        System.out.println("- 예외 발생 횟수");
        System.out.println("- 예외 타입별 분포");
        System.out.println("- 예외 발생 추세");
        System.out.println("- 응답 시간 영향");
    }
}
```

---

## 7. 테스트

### 7.1 예외 테스트

```java
public class ExceptionTesting {
    static class Calculator {
        public int divide(int a, int b) {
            if (b == 0) {
                throw new IllegalArgumentException("0으로 나눌 수 없습니다");
            }
            return a / b;
        }
    }
    
    // JUnit 스타일 테스트 (시뮬레이션)
    public static void testDivideByZero() {
        System.out.println("테스트: divide by zero");
        
        Calculator calc = new Calculator();
        
        try {
            calc.divide(10, 0);
            System.out.println("❌ 예외 발생 안 함!");
        } catch (IllegalArgumentException e) {
            System.out.println("✅ 예외 발생");
            System.out.println("✅ 메시지: " + e.getMessage());
        }
    }
    
    public static void testDivideNormal() {
        System.out.println("\n테스트: normal divide");
        
        Calculator calc = new Calculator();
        int result = calc.divide(10, 2);
        
        if (result == 5) {
            System.out.println("✅ 결과 정상");
        } else {
            System.out.println("❌ 결과 오류");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 예외 테스트 ===\n");
        
        testDivideByZero();
        testDivideNormal();
        
        System.out.println("\n📌 테스트 방법");
        System.out.println("- 예외 발생 확인");
        System.out.println("- 예외 타입 확인");
        System.out.println("- 예외 메시지 확인");
        System.out.println("- 원인 예외 확인");
    }
}
```

---

## 8. 실전 시나리오

### 8.1 API 응답 처리

```java
class ApiResponse<T> {
    private final boolean success;
    private final T data;
    private final String errorMessage;
    private final int errorCode;
    
    private ApiResponse(boolean success, T data, String errorMessage, int errorCode) {
        this.success = success;
        this.data = data;
        this.errorMessage = errorMessage;
        this.errorCode = errorCode;
    }
    
    public static <T> ApiResponse<T> success(T data) {
        return new ApiResponse<>(true, data, null, 0);
    }
    
    public static <T> ApiResponse<T> error(int code, String message) {
        return new ApiResponse<>(false, null, message, code);
    }
    
    public boolean isSuccess() { return success; }
    public T getData() { return data; }
    public String getErrorMessage() { return errorMessage; }
    public int getErrorCode() { return errorCode; }
}

class UserController {
    public ApiResponse<String> getUser(Long userId) {
        try {
            if (userId == null) {
                return ApiResponse.error(400, "userId는 필수입니다");
            }
            
            // 사용자 조회
            String user = findUser(userId);
            return ApiResponse.success(user);
            
        } catch (IllegalArgumentException e) {
            return ApiResponse.error(400, e.getMessage());
        } catch (Exception e) {
            System.err.println("서버 오류: " + e.getMessage());
            return ApiResponse.error(500, "서버 내부 오류");
        }
    }
    
    private String findUser(Long userId) {
        return "User-" + userId;
    }
}

public class ApiResponseHandling {
    public static void main(String[] args) {
        System.out.println("=== API 응답 처리 ===\n");
        
        UserController controller = new UserController();
        
        // 성공
        ApiResponse<String> response1 = controller.getUser(123L);
        if (response1.isSuccess()) {
            System.out.println("✅ 성공: " + response1.getData());
        }
        
        // 실패
        ApiResponse<String> response2 = controller.getUser(null);
        if (!response2.isSuccess()) {
            System.out.println("❌ 실패: " + response2.getErrorMessage());
            System.out.println("   코드: " + response2.getErrorCode());
        }
    }
}
```

### 8.2 배치 작업 오류 처리

```java
import java.util.*;

class BatchProcessor {
    static class BatchResult {
        int total;
        int success;
        int failed;
        List<String> errors = new ArrayList<>();
        
        void addSuccess() { success++; }
        void addFailure(String error) { 
            failed++; 
            errors.add(error);
        }
        
        void print() {
            System.out.println("\n=== 배치 결과 ===");
            System.out.println("전체: " + total);
            System.out.println("성공: " + success);
            System.out.println("실패: " + failed);
            
            if (!errors.isEmpty()) {
                System.out.println("\n오류 목록:");
                errors.forEach(e -> System.out.println("  - " + e));
            }
        }
    }
    
    public BatchResult processOrders(List<String> orderIds) {
        BatchResult result = new BatchResult();
        result.total = orderIds.size();
        
        for (String orderId : orderIds) {
            try {
                processOrder(orderId);
                result.addSuccess();
                
            } catch (Exception e) {
                result.addFailure(orderId + ": " + e.getMessage());
                // 계속 진행 (한 건 실패해도 나머지 처리)
            }
        }
        
        return result;
    }
    
    private void processOrder(String orderId) throws Exception {
        if (orderId.equals("ERR")) {
            throw new Exception("처리 오류");
        }
        System.out.println("처리: " + orderId);
    }
}

public class BatchErrorHandling {
    public static void main(String[] args) {
        System.out.println("=== 배치 오류 처리 ===\n");
        
        List<String> orders = Arrays.asList(
            "ORD-001", "ORD-002", "ERR", "ORD-004", "ERR", "ORD-006");
        
        BatchProcessor processor = new BatchProcessor();
        BatchProcessor.BatchResult result = processor.processOrders(orders);
        
        result.print();
    }
}
```

---

## 9. 체크리스트

### 9.1 설계 체크리스트

```java
public class DesignChecklist {
    public static void main(String[] args) {
        System.out.println("=== 예외 설계 체크리스트 ===\n");
        
        System.out.println("✅ 예외 타입");
        System.out.println("□ Checked vs Unchecked 적절히 선택");
        System.out.println("□ 복구 가능성 고려");
        System.out.println("□ 도메인 특화 예외 정의\n");
        
        System.out.println("✅ 예외 메시지");
        System.out.println("□ 무엇이 잘못됐는지 명확히");
        System.out.println("□ 관련 값 포함");
        System.out.println("□ 해결 방법 제시\n");
        
        System.out.println("✅ 예외 계층");
        System.out.println("□ 논리적 계층 구조");
        System.out.println("□ 적절한 추상화 레벨");
        System.out.println("□ 너무 많은 예외 X\n");
        
        System.out.println("✅ 추가 정보");
        System.out.println("□ 에러 코드");
        System.out.println("□ 컨텍스트 정보");
        System.out.println("□ 타임스탬프");
    }
}
```

### 9.2 처리 체크리스트

```java
public class HandlingChecklist {
    public static void main(String[] args) {
        System.out.println("=== 예외 처리 체크리스트 ===\n");
        
        System.out.println("✅ catch 블록");
        System.out.println("□ 구체적인 예외 타입");
        System.out.println("□ 적절한 처리 로직");
        System.out.println("□ 빈 catch 없음\n");
        
        System.out.println("✅ 자원 관리");
        System.out.println("□ try-with-resources 사용");
        System.out.println("□ finally에서 정리");
        System.out.println("□ 자원 누수 없음\n");
        
        System.out.println("✅ 예외 전파");
        System.out.println("□ 적절한 변환");
        System.out.println("□ 원인 예외 보존");
        System.out.println("□ 정보 손실 없음\n");
        
        System.out.println("✅ 로깅");
        System.out.println("□ 적절한 레벨");
        System.out.println("□ 충분한 컨텍스트");
        System.out.println("□ 스택 트레이스\n");
        
        System.out.println("✅ 테스트");
        System.out.println("□ 예외 케이스 테스트");
        System.out.println("□ 메시지 검증");
        System.out.println("□ 복구 로직 테스트");
    }
}
```

---

## 📌 핵심 정리

### 기본 원칙
```
1. 예외를 무시하지 말 것
2. 구체적인 예외 catch
3. 예외를 문서화
4. 조기 실패 (Fail-Fast)
5. 복구 가능한 상황만 catch
```

### Checked vs Unchecked
```
Checked:
- 외부 시스템 오류
- 복구 가능
- IOException, SQLException

Unchecked:
- 프로그래밍 오류
- 복구 불가능
- NullPointerException, IllegalArgumentException
```

### 좋은 예외 메시지
```
❌ "오류"
✅ "유효하지 않은 이메일: abc (@ 필요)"
```

### 안티 패턴
```
❌ 빈 catch 블록
❌ Exception catch
❌ 예외로 흐름 제어
❌ 예외 무시
```

### Best Practices
```
✅ try-with-resources
✅ 예외 변환
✅ 명확한 메시지
✅ 적절한 로깅
✅ 계층적 예외
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. Custom Exception](./Exception-02-Custom.md)**

</div>
