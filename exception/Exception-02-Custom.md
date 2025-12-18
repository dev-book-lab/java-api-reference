# Exception 02. Custom Exception 완전 정복

> 커스텀 예외 만들기 - 도메인에 맞는 예외 설계  
> Java Custom Exception

---

## 📑 목차

1. [커스텀 Exception 소개](#1-커스텀-exception-소개)
2. [Checked Exception 만들기](#2-checked-exception-만들기)
3. [Unchecked Exception 만들기](#3-unchecked-exception-만들기)
4. [Exception 체인](#4-exception-체인)
5. [계층 구조](#5-계층-구조)
6. [메시지와 정보](#6-메시지와-정보)
7. [실전 패턴](#7-실전-패턴)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. 커스텀 Exception 소개

### 1.1 왜 커스텀 Exception인가?

```java
public class WhyCustomException {
    // ❌ 표준 Exception 사용
    public static void withdrawBad(int balance, int amount) throws Exception {
        if (amount > balance) {
            throw new Exception("잔액 부족");  // 너무 일반적
        }
    }
    
    // ✅ 커스텀 Exception 사용
    static class InsufficientBalanceException extends Exception {
        private final int balance;
        private final int requested;
        
        public InsufficientBalanceException(int balance, int requested) {
            super(String.format("잔액 부족: 잔액 %d원, 요청 %d원", balance, requested));
            this.balance = balance;
            this.requested = requested;
        }
        
        public int getBalance() { return balance; }
        public int getRequested() { return requested; }
    }
    
    public static void withdrawGood(int balance, int amount) 
            throws InsufficientBalanceException {
        if (amount > balance) {
            throw new InsufficientBalanceException(balance, amount);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 커스텀 Exception ===\n");
        
        System.out.println("📌 장점");
        System.out.println("✅ 명확한 의미");
        System.out.println("✅ 추가 정보 포함");
        System.out.println("✅ 타입으로 구분");
        System.out.println("✅ 도메인 표현\n");
        
        try {
            withdrawGood(10000, 20000);
        } catch (InsufficientBalanceException e) {
            System.out.println(e.getMessage());
            System.out.println("부족액: " + (e.getRequested() - e.getBalance()));
        }
    }
}
```

### 1.2 언제 만들까?

```java
public class WhenToCreate {
    public static void main(String[] args) {
        System.out.println("=== 커스텀 Exception 생성 시기 ===\n");
        
        System.out.println("✅ 만들어야 할 때");
        System.out.println("- 도메인 특화 오류");
        System.out.println("- 추가 정보 필요");
        System.out.println("- 별도 처리 필요");
        System.out.println("- 명확한 의미 전달\n");
        
        System.out.println("❌ 만들지 말아야 할 때");
        System.out.println("- 표준 Exception으로 충분");
        System.out.println("- 단순 메시지만 필요");
        System.out.println("- 재사용성 없음\n");
        
        System.out.println("📌 예시");
        System.out.println("만들기:");
        System.out.println("- InvalidPasswordException");
        System.out.println("- InsufficientBalanceException");
        System.out.println("- DuplicateUserException\n");
        
        System.out.println("표준 사용:");
        System.out.println("- IllegalArgumentException");
        System.out.println("- IllegalStateException");
        System.out.println("- UnsupportedOperationException");
    }
}
```

---

## 2. Checked Exception 만들기

### 2.1 기본 구조

```java
// 기본 Checked Exception
class MyCheckedException extends Exception {
    public MyCheckedException() {
        super();
    }
    
    public MyCheckedException(String message) {
        super(message);
    }
    
    public MyCheckedException(String message, Throwable cause) {
        super(message, cause);
    }
    
    public MyCheckedException(Throwable cause) {
        super(cause);
    }
}

public class BasicCheckedEx {
    public static void riskyOperation() throws MyCheckedException {
        throw new MyCheckedException("오류 발생");
    }
    
    public static void main(String[] args) {
        System.out.println("=== Checked Exception ===\n");
        
        try {
            riskyOperation();
        } catch (MyCheckedException e) {
            System.out.println("처리: " + e.getMessage());
        }
        
        System.out.println("\n📌 4가지 생성자");
        System.out.println("1. 기본 생성자");
        System.out.println("2. 메시지");
        System.out.println("3. 메시지 + 원인");
        System.out.println("4. 원인");
    }
}
```

### 2.2 도메인 Checked Exception

```java
class FileProcessingException extends Exception {
    private final String fileName;
    private final String operation;
    
    public FileProcessingException(String fileName, String operation, String message) {
        super(String.format("파일 처리 실패 [%s]: %s - %s", 
            fileName, operation, message));
        this.fileName = fileName;
        this.operation = operation;
    }
    
    public FileProcessingException(String fileName, String operation, Throwable cause) {
        super(String.format("파일 처리 실패 [%s]: %s", fileName, operation), cause);
        this.fileName = fileName;
        this.operation = operation;
    }
    
    public String getFileName() { return fileName; }
    public String getOperation() { return operation; }
}

class FileProcessor {
    public void processFile(String fileName) throws FileProcessingException {
        try {
            // 파일 처리
            if (fileName == null) {
                throw new FileProcessingException(fileName, "read", "파일명이 null");
            }
        } catch (Exception e) {
            throw new FileProcessingException(fileName, "read", e);
        }
    }
}

public class DomainCheckedEx {
    public static void main(String[] args) {
        System.out.println("=== 도메인 Checked Exception ===\n");
        
        FileProcessor processor = new FileProcessor();
        
        try {
            processor.processFile(null);
        } catch (FileProcessingException e) {
            System.out.println(e.getMessage());
            System.out.println("파일: " + e.getFileName());
            System.out.println("작업: " + e.getOperation());
        }
    }
}
```

---

## 3. Unchecked Exception 만들기

### 3.1 기본 구조

```java
// 기본 Unchecked Exception
class MyUncheckedException extends RuntimeException {
    public MyUncheckedException() {
        super();
    }
    
    public MyUncheckedException(String message) {
        super(message);
    }
    
    public MyUncheckedException(String message, Throwable cause) {
        super(message, cause);
    }
    
    public MyUncheckedException(Throwable cause) {
        super(cause);
    }
}

public class BasicUncheckedEx {
    public static void riskyOperation() {
        throw new MyUncheckedException("오류 발생");
    }
    
    public static void main(String[] args) {
        System.out.println("=== Unchecked Exception ===\n");
        
        try {
            riskyOperation();
        } catch (MyUncheckedException e) {
            System.out.println("처리: " + e.getMessage());
        }
        
        System.out.println("\n📌 RuntimeException 상속");
        System.out.println("- 처리 선택적");
        System.out.println("- throws 불필요");
    }
}
```

### 3.2 도메인 Unchecked Exception

```java
class InvalidPasswordException extends RuntimeException {
    private final String userId;
    private final String reason;
    
    public InvalidPasswordException(String userId, String reason) {
        super(String.format("비밀번호 오류 [%s]: %s", userId, reason));
        this.userId = userId;
        this.reason = reason;
    }
    
    public String getUserId() { return userId; }
    public String getReason() { return reason; }
}

class DuplicateUserException extends RuntimeException {
    private final String userId;
    
    public DuplicateUserException(String userId) {
        super("중복된 사용자: " + userId);
        this.userId = userId;
    }
    
    public String getUserId() { return userId; }
}

class UserService {
    private java.util.Set<String> users = new java.util.HashSet<>();
    
    public void registerUser(String userId, String password) {
        // 중복 체크
        if (users.contains(userId)) {
            throw new DuplicateUserException(userId);
        }
        
        // 비밀번호 검증
        if (password.length() < 8) {
            throw new InvalidPasswordException(userId, "8자 이상 필요");
        }
        if (!password.matches(".*[A-Z].*")) {
            throw new InvalidPasswordException(userId, "대문자 필요");
        }
        
        users.add(userId);
        System.out.println("등록 완료: " + userId);
    }
}

public class DomainUncheckedEx {
    public static void main(String[] args) {
        System.out.println("=== 도메인 Unchecked Exception ===\n");
        
        UserService service = new UserService();
        
        // 성공
        try {
            service.registerUser("alice", "Password123");
        } catch (RuntimeException e) {
            System.out.println(e.getMessage());
        }
        
        // 중복
        try {
            service.registerUser("alice", "Password123");
        } catch (DuplicateUserException e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        // 비밀번호 오류
        try {
            service.registerUser("bob", "pass");
        } catch (InvalidPasswordException e) {
            System.out.println("오류: " + e.getMessage());
            System.out.println("이유: " + e.getReason());
        }
    }
}
```

---

## 4. Exception 체인

### 4.1 원인 예외

```java
class DataAccessException extends RuntimeException {
    public DataAccessException(String message, Throwable cause) {
        super(message, cause);
    }
}

class UserNotFoundException extends DataAccessException {
    public UserNotFoundException(Long userId, Throwable cause) {
        super("사용자를 찾을 수 없습니다: " + userId, cause);
    }
}

class UserRepository {
    public Object findUser(Long userId) {
        try {
            // DB 접근 시뮬레이션
            throw new java.sql.SQLException("Connection timeout");
        } catch (java.sql.SQLException e) {
            // 원인 예외 체인
            throw new UserNotFoundException(userId, e);
        }
    }
}

public class ExceptionChain {
    public static void main(String[] args) {
        System.out.println("=== Exception 체인 ===\n");
        
        UserRepository repo = new UserRepository();
        
        try {
            repo.findUser(123L);
        } catch (UserNotFoundException e) {
            System.out.println("예외: " + e.getMessage());
            System.out.println("원인: " + e.getCause());
            System.out.println("\n스택 트레이스:");
            e.printStackTrace();
        }
        
        System.out.println("\n📌 장점");
        System.out.println("- 원인 추적 가능");
        System.out.println("- 디버깅 용이");
        System.out.println("- 정보 손실 방지");
    }
}
```

### 4.2 initCause

```java
class LegacyException extends Exception {
    private Throwable cause;
    
    public LegacyException(String message) {
        super(message);
    }
    
    // Java 1.4 이전 스타일
    public LegacyException initCause(Throwable cause) {
        this.cause = cause;
        return this;
    }
    
    @Override
    public Throwable getCause() {
        return cause;
    }
}

public class InitCauseExample {
    public static void main(String[] args) {
        System.out.println("=== initCause ===\n");
        
        try {
            Exception original = new Exception("원본 예외");
            LegacyException legacy = new LegacyException("래거시 예외");
            legacy.initCause(original);
            
            throw legacy;
        } catch (LegacyException e) {
            System.out.println("예외: " + e.getMessage());
            System.out.println("원인: " + e.getCause().getMessage());
        }
        
        System.out.println("\n📌 initCause");
        System.out.println("- 생성 후 원인 설정");
        System.out.println("- 한 번만 호출 가능");
        System.out.println("- 주로 레거시 코드");
    }
}
```

---

## 5. 계층 구조

### 5.1 예외 계층

```java
// 최상위
class BusinessException extends Exception {
    public BusinessException(String message) {
        super(message);
    }
    
    public BusinessException(String message, Throwable cause) {
        super(message, cause);
    }
}

// 중간 계층
class ValidationException extends BusinessException {
    public ValidationException(String message) {
        super(message);
    }
}

class ResourceException extends BusinessException {
    public ResourceException(String message) {
        super(message);
    }
}

// 구체적 예외
class InvalidEmailException extends ValidationException {
    public InvalidEmailException(String email) {
        super("유효하지 않은 이메일: " + email);
    }
}

class InvalidAgeException extends ValidationException {
    public InvalidAgeException(int age) {
        super("유효하지 않은 나이: " + age);
    }
}

class ResourceNotFoundException extends ResourceException {
    public ResourceNotFoundException(String resource) {
        super("리소스를 찾을 수 없습니다: " + resource);
    }
}

public class ExceptionHierarchy {
    public static void validateUser(String email, int age) 
            throws ValidationException {
        if (!email.contains("@")) {
            throw new InvalidEmailException(email);
        }
        if (age < 0) {
            throw new InvalidAgeException(age);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 예외 계층 ===\n");
        
        // 구체적 catch
        try {
            validateUser("invalid", 25);
        } catch (InvalidEmailException e) {
            System.out.println("이메일 오류: " + e.getMessage());
        } catch (InvalidAgeException e) {
            System.out.println("나이 오류: " + e.getMessage());
        } catch (ValidationException e) {
            System.out.println("검증 오류: " + e.getMessage());
        }
        
        // 상위 타입 catch
        try {
            validateUser("test@test.com", -5);
        } catch (ValidationException e) {
            System.out.println("\n검증 실패: " + e.getMessage());
        }
        
        System.out.println("\n📌 계층 구조");
        System.out.println("BusinessException");
        System.out.println("├── ValidationException");
        System.out.println("│   ├── InvalidEmailException");
        System.out.println("│   └── InvalidAgeException");
        System.out.println("└── ResourceException");
        System.out.println("    └── ResourceNotFoundException");
    }
}
```

---

## 6. 메시지와 정보

### 6.1 풍부한 정보

```java
class OrderException extends Exception {
    private final String orderId;
    private final String customerId;
    private final String productId;
    private final int quantity;
    private final String errorCode;
    
    public OrderException(String orderId, String customerId, String productId,
                         int quantity, String errorCode, String message) {
        super(String.format(
            "[주문 오류] 주문ID:%s, 고객:%s, 상품:%s, 수량:%d - %s (코드: %s)",
            orderId, customerId, productId, quantity, message, errorCode));
        
        this.orderId = orderId;
        this.customerId = customerId;
        this.productId = productId;
        this.quantity = quantity;
        this.errorCode = errorCode;
    }
    
    public String getOrderId() { return orderId; }
    public String getCustomerId() { return customerId; }
    public String getProductId() { return productId; }
    public int getQuantity() { return quantity; }
    public String getErrorCode() { return errorCode; }
    
    public java.util.Map<String, Object> toMap() {
        java.util.Map<String, Object> map = new java.util.HashMap<>();
        map.put("orderId", orderId);
        map.put("customerId", customerId);
        map.put("productId", productId);
        map.put("quantity", quantity);
        map.put("errorCode", errorCode);
        map.put("message", getMessage());
        return map;
    }
}

public class RichInformation {
    public static void main(String[] args) {
        System.out.println("=== 풍부한 정보 ===\n");
        
        try {
            throw new OrderException(
                "ORD-001", "CUST-123", "PROD-456", 
                10, "OUT_OF_STOCK", "재고 부족");
        } catch (OrderException e) {
            System.out.println(e.getMessage());
            System.out.println("\n상세 정보:");
            System.out.println("  주문ID: " + e.getOrderId());
            System.out.println("  에러코드: " + e.getErrorCode());
            
            System.out.println("\nMap 변환:");
            System.out.println("  " + e.toMap());
        }
        
        System.out.println("\n📌 포함할 정보");
        System.out.println("- 식별자 (ID)");
        System.out.println("- 에러 코드");
        System.out.println("- 관련 데이터");
        System.out.println("- 타임스탬프");
    }
}
```

### 6.2 로깅 친화적

```java
class LoggableException extends RuntimeException {
    private final java.time.Instant timestamp;
    private final String userId;
    private final String requestId;
    
    public LoggableException(String message, String userId, String requestId) {
        super(message);
        this.timestamp = java.time.Instant.now();
        this.userId = userId;
        this.requestId = requestId;
    }
    
    public String getLogMessage() {
        return String.format(
            "[%s] [User:%s] [Request:%s] %s",
            timestamp, userId, requestId, getMessage());
    }
    
    public java.util.Map<String, Object> getLogContext() {
        java.util.Map<String, Object> context = new java.util.HashMap<>();
        context.put("timestamp", timestamp.toString());
        context.put("userId", userId);
        context.put("requestId", requestId);
        context.put("message", getMessage());
        return context;
    }
}

public class LoggablException {
    public static void main(String[] args) {
        System.out.println("=== 로깅 친화적 ===\n");
        
        try {
            throw new LoggableException(
                "데이터베이스 연결 실패",
                "user123",
                "req-abc-456");
        } catch (LoggableException e) {
            // 로그 출력
            System.out.println("로그: " + e.getLogMessage());
            System.out.println("\n컨텍스트:");
            e.getLogContext().forEach((k, v) -> 
                System.out.println("  " + k + ": " + v));
        }
    }
}
```

---

## 7. 실전 패턴

### 7.1 Result 패턴

```java
class Result<T> {
    private final T value;
    private final Exception error;
    
    private Result(T value, Exception error) {
        this.value = value;
        this.error = error;
    }
    
    public static <T> Result<T> success(T value) {
        return new Result<>(value, null);
    }
    
    public static <T> Result<T> failure(Exception error) {
        return new Result<>(null, error);
    }
    
    public boolean isSuccess() {
        return error == null;
    }
    
    public T getValue() {
        if (error != null) {
            throw new IllegalStateException("Result is failure", error);
        }
        return value;
    }
    
    public Exception getError() {
        return error;
    }
}

class UserService {
    public Result<String> getUserName(Long userId) {
        try {
            if (userId == null) {
                return Result.failure(new IllegalArgumentException("userId is null"));
            }
            if (userId < 0) {
                return Result.failure(new IllegalArgumentException("userId < 0"));
            }
            
            return Result.success("User-" + userId);
        } catch (Exception e) {
            return Result.failure(e);
        }
    }
}

public class ResultPattern {
    public static void main(String[] args) {
        System.out.println("=== Result 패턴 ===\n");
        
        UserService service = new UserService();
        
        // 성공
        Result<String> result1 = service.getUserName(123L);
        if (result1.isSuccess()) {
            System.out.println("성공: " + result1.getValue());
        }
        
        // 실패
        Result<String> result2 = service.getUserName(-1L);
        if (!result2.isSuccess()) {
            System.out.println("실패: " + result2.getError().getMessage());
        }
        
        System.out.println("\n📌 Result 패턴");
        System.out.println("- Exception 대신 반환값으로");
        System.out.println("- 명시적 오류 처리");
        System.out.println("- 함수형 스타일");
    }
}
```

### 7.2 ErrorCode Enum

```java
enum ErrorCode {
    INVALID_INPUT(400, "잘못된 입력"),
    UNAUTHORIZED(401, "인증 필요"),
    NOT_FOUND(404, "찾을 수 없음"),
    INTERNAL_ERROR(500, "내부 오류");
    
    private final int code;
    private final String message;
    
    ErrorCode(int code, String message) {
        this.code = code;
        this.message = message;
    }
    
    public int getCode() { return code; }
    public String getMessage() { return message; }
}

class ApplicationException extends RuntimeException {
    private final ErrorCode errorCode;
    
    public ApplicationException(ErrorCode errorCode) {
        super(errorCode.getMessage());
        this.errorCode = errorCode;
    }
    
    public ApplicationException(ErrorCode errorCode, String detail) {
        super(errorCode.getMessage() + ": " + detail);
        this.errorCode = errorCode;
    }
    
    public ErrorCode getErrorCode() {
        return errorCode;
    }
}

public class ErrorCodePattern {
    public static void main(String[] args) {
        System.out.println("=== ErrorCode 패턴 ===\n");
        
        try {
            throw new ApplicationException(ErrorCode.NOT_FOUND, "User ID 123");
        } catch (ApplicationException e) {
            System.out.println("코드: " + e.getErrorCode().getCode());
            System.out.println("메시지: " + e.getMessage());
        }
        
        System.out.println("\n📌 ErrorCode 패턴");
        System.out.println("- 에러 코드 중앙 관리");
        System.out.println("- 일관성 있는 응답");
        System.out.println("- API 응답에 유용");
    }
}
```

---

## 8. 실전 예제

### 8.1 Banking System

```java
class InsufficientFundsException extends Exception {
    private final long accountId;
    private final double balance;
    private final double amount;
    
    public InsufficientFundsException(long accountId, double balance, double amount) {
        super(String.format(
            "잔액 부족 [계좌:%d] 잔액:%.2f원, 요청:%.2f원",
            accountId, balance, amount));
        this.accountId = accountId;
        this.balance = balance;
        this.amount = amount;
    }
    
    public double getShortfall() {
        return amount - balance;
    }
}

class InvalidAmountException extends RuntimeException {
    public InvalidAmountException(double amount) {
        super("유효하지 않은 금액: " + amount);
    }
}

class Account {
    private final long id;
    private double balance;
    
    public Account(long id, double balance) {
        this.id = id;
        this.balance = balance;
    }
    
    public void withdraw(double amount) throws InsufficientFundsException {
        if (amount <= 0) {
            throw new InvalidAmountException(amount);
        }
        if (amount > balance) {
            throw new InsufficientFundsException(id, balance, amount);
        }
        
        balance -= amount;
        System.out.println("출금 성공: " + amount + "원");
    }
    
    public double getBalance() {
        return balance;
    }
}

public class BankingSystem {
    public static void main(String[] args) {
        System.out.println("=== Banking System ===\n");
        
        Account account = new Account(12345, 10000);
        
        // 성공
        try {
            account.withdraw(5000);
            System.out.println("잔액: " + account.getBalance());
        } catch (Exception e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        // 잔액 부족
        try {
            account.withdraw(10000);
        } catch (InsufficientFundsException e) {
            System.out.println("\n오류: " + e.getMessage());
            System.out.println("부족액: " + e.getShortfall());
        }
        
        // 유효하지 않은 금액
        try {
            account.withdraw(-100);
        } catch (InvalidAmountException e) {
            System.out.println("\n오류: " + e.getMessage());
        } catch (Exception e) {
            // InsufficientFundsException은 여기서 안 잡힘
        }
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 사용자 등록 예외

```java
// InvalidUsernameException 구현
class InvalidUsernameException extends RuntimeException {
    // 구현: username, reason 필드
}

public class Problem1 {
    public static void validateUsername(String username) {
        if (username == null || username.length() < 3) {
            throw new InvalidUsernameException(username, "3자 이상 필요");
        }
    }
    
    public static void main(String[] args) {
        try {
            validateUsername("ab");
        } catch (InvalidUsernameException e) {
            System.out.println(e.getMessage());
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
class InvalidUsernameException extends RuntimeException {
    private final String username;
    private final String reason;
    
    public InvalidUsernameException(String username, String reason) {
        super(String.format("유효하지 않은 사용자명 [%s]: %s", username, reason));
        this.username = username;
        this.reason = reason;
    }
    
    public String getUsername() { return username; }
    public String getReason() { return reason; }
}
```
</details>

---

### 문제 2: 예외 체인

```java
// DataAccessException 구현 (원인 예외 포함)
class DataAccessException extends RuntimeException {
    // 구현
}

class UserRepository {
    public void save(String username) {
        try {
            // DB 시뮬레이션
            if (username.equals("error")) {
                throw new java.sql.SQLException("DB 오류");
            }
        } catch (java.sql.SQLException e) {
            // DataAccessException으로 래핑
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
class DataAccessException extends RuntimeException {
    public DataAccessException(String message, Throwable cause) {
        super(message, cause);
    }
}

class UserRepository {
    public void save(String username) {
        try {
            if (username.equals("error")) {
                throw new java.sql.SQLException("DB 오류");
            }
            System.out.println("저장 완료: " + username);
        } catch (java.sql.SQLException e) {
            throw new DataAccessException(
                "사용자 저장 실패: " + username, e);
        }
    }
}
```
</details>

---

### 문제 3: 계층 구조

```java
// 예외 계층 구현
// PaymentException (최상위)
// ├── InvalidCardException
// └── InsufficientBalanceException

class PaymentException extends Exception {
    // 구현
}

class InvalidCardException extends PaymentException {
    // 구현
}

class InsufficientBalanceException extends PaymentException {
    // 구현
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
class PaymentException extends Exception {
    public PaymentException(String message) {
        super(message);
    }
}

class InvalidCardException extends PaymentException {
    private final String cardNumber;
    
    public InvalidCardException(String cardNumber) {
        super("유효하지 않은 카드: " + cardNumber);
        this.cardNumber = cardNumber;
    }
    
    public String getCardNumber() { return cardNumber; }
}

class InsufficientBalanceException extends PaymentException {
    private final double balance;
    private final double amount;
    
    public InsufficientBalanceException(double balance, double amount) {
        super(String.format("잔액 부족: %.2f원 (필요: %.2f원)", balance, amount));
        this.balance = balance;
        this.amount = amount;
    }
    
    public double getBalance() { return balance; }
    public double getAmount() { return amount; }
}
```
</details>

---

## 📌 핵심 정리

### 기본 구조
```java
class MyException extends Exception {
    // Checked
}

class MyException extends RuntimeException {
    // Unchecked
}
```

### 4가지 생성자
```java
public MyException() { super(); }
public MyException(String message) { super(message); }
public MyException(String message, Throwable cause) { super(message, cause); }
public MyException(Throwable cause) { super(cause); }
```

### 추가 정보
```java
class MyException extends Exception {
    private final String userId;
    private final String errorCode;
    
    public MyException(String userId, String errorCode, String message) {
        super(message);
        this.userId = userId;
        this.errorCode = errorCode;
    }
}
```

### 예외 체인
```java
try {
    // ...
} catch (SQLException e) {
    throw new DataAccessException("DB 오류", e);
}
```

### 계층 구조
```
BusinessException
├── ValidationException
│   ├── InvalidEmailException
│   └── InvalidAgeException
└── ResourceException
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Exception Basic](./Exception-01-Basic.md) | [다음: 03. Best Practices →](./Exception-03-Best.md)**

</div>
