# Enum 03. Enum Patterns 완전 정복

> 실전 Enum 패턴 - 설계부터 최적화까지  
> Java Enum

---

## 📑 목차

1. [Constant Specific Method](#1-constant-specific-method)
2. [Strategy Pattern 심화](#2-strategy-pattern-심화)
3. [Type Safe Heterogeneous Container](#3-type-safe-heterogeneous-container)
4. [Enum 기반 Visitor 패턴](#4-enum-기반-visitor-패턴)
5. [성능 최적화](#5-성능-최적화)
6. [Enum vs Alternatives](#6-enum-vs-alternatives)
7. [실전 시나리오](#7-실전-시나리오)
8. [Common Pitfalls](#8-common-pitfalls)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Constant Specific Method

### 1.1 기본 패턴

```java
enum Calculator {
    PLUS("+") {
        @Override
        public double calculate(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        @Override
        public double calculate(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        @Override
        public double calculate(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        @Override
        public double calculate(double x, double y) {
            if (y == 0) throw new ArithmeticException("Division by zero");
            return x / y;
        }
    };
    
    private final String symbol;
    
    Calculator(String symbol) {
        this.symbol = symbol;
    }
    
    public String getSymbol() {
        return symbol;
    }
    
    public abstract double calculate(double x, double y);
    
    @Override
    public String toString() {
        return symbol;
    }
}

public class ConstantSpecificMethodBasic {
    public static void main(String[] args) {
        System.out.println("=== Constant Specific Method ===\n");
        
        double x = 10, y = 5;
        
        for (Calculator op : Calculator.values()) {
            System.out.printf("%.0f %s %.0f = %.2f%n",
                x, op, y, op.calculate(x, y));
        }
        
        
        System.out.println("\n=== 장점 ===");
        System.out.println("✅ switch 문 불필요");
        System.out.println("✅ 각 상수별 구현");
        System.out.println("✅ 새 상수 추가 시 자동 처리");
    }
}
```

### 1.2 복잡한 로직

```java
enum PaymentType {
    CREDIT_CARD("신용카드", 0.03) {
        @Override
        public boolean validate(String input) {
            return input != null && input.length() == 16 && input.matches("\\d+");
        }
        
        @Override
        public String formatDisplay(String input) {
            return input.replaceAll("(\\d{4})", "$1-").replaceAll("-$", "");
        }
    },
    BANK_ACCOUNT("계좌이체", 0.0) {
        @Override
        public boolean validate(String input) {
            return input != null && input.length() >= 10 && input.matches("\\d+");
        }
        
        @Override
        public String formatDisplay(String input) {
            return input.substring(0, 3) + "-" + 
                   input.substring(3, 5) + "-" + 
                   input.substring(5);
        }
    },
    PHONE("휴대폰 결제", 0.05) {
        @Override
        public boolean validate(String input) {
            return input != null && input.matches("01\\d{8,9}");
        }
        
        @Override
        public String formatDisplay(String input) {
            return input.replaceAll("(\\d{3})(\\d{4})(\\d{4})", "$1-$2-$3");
        }
    };
    
    private final String displayName;
    private final double feeRate;
    
    PaymentType(String displayName, double feeRate) {
        this.displayName = displayName;
        this.feeRate = feeRate;
    }
    
    public String getDisplayName() {
        return displayName;
    }
    
    public double calculateFee(double amount) {
        return amount * feeRate;
    }
    
    public abstract boolean validate(String input);
    public abstract String formatDisplay(String input);
}

public class ComplexConstantMethods {
    public static void main(String[] args) {
        System.out.println("=== 결제 수단별 처리 ===\n");
        
        String cardNumber = "1234567890123456";
        String accountNumber = "1234567890123";
        String phoneNumber = "01012345678";
        
        // 신용카드
        PaymentType card = PaymentType.CREDIT_CARD;
        System.out.println(card.getDisplayName());
        System.out.println("유효: " + card.validate(cardNumber));
        System.out.println("표시: " + card.formatDisplay(cardNumber));
        
        // 계좌이체
        System.out.println("\n" + PaymentType.BANK_ACCOUNT.getDisplayName());
        System.out.println("표시: " + 
            PaymentType.BANK_ACCOUNT.formatDisplay(accountNumber));
    }
}
```

---

## 2. Strategy Pattern 심화

### 2.1 동적 전략 선택

```java
import java.util.*;

interface PricingStrategy {
    double calculatePrice(double basePrice);
    String getDescription();
}

enum CustomerType implements PricingStrategy {
    REGULAR("일반 고객", 0.0) {
        @Override
        public double calculatePrice(double basePrice) {
            return basePrice;
        }
    },
    MEMBER("회원", 0.10) {
        @Override
        public double calculatePrice(double basePrice) {
            return basePrice * (1 - discountRate);
        }
    },
    VIP("VIP", 0.20) {
        @Override
        public double calculatePrice(double basePrice) {
            double discounted = basePrice * (1 - discountRate);
            // VIP 추가 혜택
            if (basePrice >= 100000) {
                discounted *= 0.95;  // 10만원 이상 추가 5% 할인
            }
            return discounted;
        }
    },
    PREMIUM("프리미엄", 0.30) {
        @Override
        public double calculatePrice(double basePrice) {
            double discounted = basePrice * (1 - discountRate);
            // 프리미엄 추가 혜택
            if (basePrice >= 50000) {
                discounted -= 5000;  // 5만원 이상 5천원 추가 할인
            }
            return Math.max(0, discounted);
        }
    };
    
    private final String displayName;
    protected final double discountRate;
    
    CustomerType(String displayName, double discountRate) {
        this.displayName = displayName;
        this.discountRate = discountRate;
    }
    
    @Override
    public String getDescription() {
        return displayName + " (기본 " + (int)(discountRate * 100) + "% 할인)";
    }
}

class Order {
    private double basePrice;
    private CustomerType customerType;
    
    Order(double basePrice, CustomerType customerType) {
        this.basePrice = basePrice;
        this.customerType = customerType;
    }
    
    public double getFinalPrice() {
        return customerType.calculatePrice(basePrice);
    }
    
    public void printReceipt() {
        System.out.printf("%s%n", customerType.getDescription());
        System.out.printf("정가: %,원%n", (int)basePrice);
        System.out.printf("최종: %,d원%n", (int)getFinalPrice());
        System.out.printf("절약: %,d원%n%n", 
            (int)(basePrice - getFinalPrice()));
    }
}

public class DynamicStrategySelection {
    public static void main(String[] args) {
        System.out.println("=== 고객 등급별 가격 ===\n");
        
        double price = 120000;
        
        for (CustomerType type : CustomerType.values()) {
            Order order = new Order(price, type);
            order.printReceipt();
        }
    }
}
```

### 2.2 전략 체이닝

```java
interface Filter {
    boolean apply(String text);
}

enum TextFilter implements Filter {
    PROFANITY("욕설 필터") {
        @Override
        public boolean apply(String text) {
            String[] badWords = {"욕설1", "욕설2"};
            for (String word : badWords) {
                if (text.contains(word)) return false;
            }
            return true;
        }
    },
    LENGTH("길이 제한") {
        @Override
        public boolean apply(String text) {
            return text.length() >= 5 && text.length() <= 500;
        }
    },
    SPAM("스팸 필터") {
        @Override
        public boolean apply(String text) {
            return !text.matches(".*\\d{3}-\\d{4}-\\d{4}.*");  // 전화번호 패턴
        }
    },
    SPECIAL_CHARS("특수문자 제한") {
        @Override
        public boolean apply(String text) {
            long specialCount = text.chars()
                .filter(ch -> !Character.isLetterOrDigit(ch) && !Character.isWhitespace(ch))
                .count();
            return specialCount < text.length() * 0.3;  // 30% 미만
        }
    };
    
    private final String description;
    
    TextFilter(String description) {
        this.description = description;
    }
    
    public String getDescription() {
        return description;
    }
    
    public static boolean applyAll(String text, TextFilter... filters) {
        for (TextFilter filter : filters) {
            if (!filter.apply(text)) {
                System.out.println("차단: " + filter.getDescription());
                return false;
            }
        }
        return true;
    }
}

public class FilterChaining {
    public static void main(String[] args) {
        System.out.println("=== 텍스트 필터링 ===\n");
        
        String text1 = "안녕하세요 좋은 하루 되세요";
        String text2 = "너무 짧음";
        String text3 = "전화 주세요 010-1234-5678";
        
        System.out.println("텍스트 1:");
        boolean result1 = TextFilter.applyAll(text1, TextFilter.values());
        System.out.println("결과: " + (result1 ? "통과" : "차단") + "\n");
        
        System.out.println("텍스트 2:");
        boolean result2 = TextFilter.applyAll(text2, TextFilter.values());
        System.out.println("결과: " + (result2 ? "통과" : "차단") + "\n");
        
        System.out.println("텍스트 3:");
        boolean result3 = TextFilter.applyAll(text3, TextFilter.values());
        System.out.println("결과: " + (result3 ? "통과" : "차단"));
    }
}
```

---

## 3. Type Safe Heterogeneous Container

### 3.1 기본 컨테이너

```java
import java.util.*;

class TypesafeMap {
    private Map<Class<?>, Object> map = new HashMap<>();
    
    public <T> void put(Class<T> type, T instance) {
        map.put(type, instance);
    }
    
    public <T> T get(Class<T> type) {
        return type.cast(map.get(type));
    }
}

enum ServiceType {
    DATABASE {
        @Override
        public Class<?> getServiceClass() {
            return DatabaseService.class;
        }
    },
    CACHE {
        @Override
        public Class<?> getServiceClass() {
            return CacheService.class;
        }
    },
    LOGGER {
        @Override
        public Class<?> getServiceClass() {
            return LoggerService.class;
        }
    };
    
    public abstract Class<?> getServiceClass();
}

interface DatabaseService {
    void query(String sql);
}

interface CacheService {
    void put(String key, Object value);
}

interface LoggerService {
    void log(String message);
}

public class TypeSafeHeterogeneousContainer {
    public static void main(String[] args) {
        System.out.println("=== Type Safe Container ===\n");
        
        TypesafeMap services = new TypesafeMap();
        
        services.put(DatabaseService.class, 
            sql -> System.out.println("쿼리: " + sql));
        services.put(CacheService.class, 
            (key, value) -> System.out.println("캐시 저장: " + key));
        
        DatabaseService db = services.get(DatabaseService.class);
        db.query("SELECT * FROM users");
    }
}
```

---

## 4. Enum 기반 Visitor 패턴

### 4.1 Visitor 구현

```java
interface NodeVisitor<R> {
    R visit(Node node);
}

enum NodeType {
    TEXT {
        @Override
        public String accept(String content, NodeVisitor<String> visitor) {
            return "Text: " + content;
        }
    },
    BOLD {
        @Override
        public String accept(String content, NodeVisitor<String> visitor) {
            return "<b>" + content + "</b>";
        }
    },
    ITALIC {
        @Override
        public String accept(String content, NodeVisitor<String> visitor) {
            return "<i>" + content + "</i>";
        }
    },
    LINK {
        @Override
        public String accept(String content, NodeVisitor<String> visitor) {
            return "<a href='" + content + "'>" + content + "</a>";
        }
    };
    
    public abstract String accept(String content, NodeVisitor<String> visitor);
}

class Node {
    NodeType type;
    String content;
    
    Node(NodeType type, String content) {
        this.type = type;
        this.content = content;
    }
    
    public String render() {
        return type.accept(content, null);
    }
}

public class VisitorPattern {
    public static void main(String[] args) {
        System.out.println("=== Visitor 패턴 ===\n");
        
        java.util.List<Node> nodes = java.util.Arrays.asList(
            new Node(NodeType.TEXT, "Hello "),
            new Node(NodeType.BOLD, "World"),
            new Node(NodeType.TEXT, "!"),
            new Node(NodeType.LINK, "http://example.com")
        );
        
        for (Node node : nodes) {
            System.out.println(node.render());
        }
    }
}
```

---

## 5. 성능 최적화

### 5.1 EnumMap 활용

```java
import java.util.*;

enum CacheKey {
    USER_DATA, PRODUCT_LIST, CONFIG, SESSION
}

class EnumMapCache {
    private EnumMap<CacheKey, Object> cache;
    
    EnumMapCache() {
        cache = new EnumMap<>(CacheKey.class);
    }
    
    public void put(CacheKey key, Object value) {
        cache.put(key, value);
    }
    
    public Object get(CacheKey key) {
        return cache.get(key);
    }
    
    public void clear(CacheKey key) {
        cache.remove(key);
    }
    
    public int size() {
        return cache.size();
    }
}

public class PerformanceOptimization {
    public static void main(String[] args) {
        System.out.println("=== EnumMap 성능 ===\n");
        
        EnumMapCache cache = new EnumMapCache();
        
        // 캐시 저장
        cache.put(CacheKey.USER_DATA, "User 정보");
        cache.put(CacheKey.PRODUCT_LIST, new ArrayList<>());
        
        // 조회
        System.out.println("캐시 크기: " + cache.size());
        System.out.println("사용자 데이터: " + cache.get(CacheKey.USER_DATA));
        
        
        System.out.println("\n=== EnumMap 장점 ===");
        System.out.println("✅ 배열 기반 (O(1))");
        System.out.println("✅ 메모리 효율적");
        System.out.println("✅ 순서 보장");
    }
}
```

### 5.2 EnumSet 비트 연산

```java
import java.util.*;

enum Permission {
    READ, WRITE, EXECUTE, DELETE, ADMIN
}

class PermissionManager {
    private EnumSet<Permission> permissions;
    
    PermissionManager() {
        permissions = EnumSet.noneOf(Permission.class);
    }
    
    public void grant(Permission permission) {
        permissions.add(permission);
    }
    
    public void revoke(Permission permission) {
        permissions.remove(permission);
    }
    
    public boolean hasPermission(Permission permission) {
        return permissions.contains(permission);
    }
    
    public void grantAll(Permission... perms) {
        permissions.addAll(Arrays.asList(perms));
    }
    
    public EnumSet<Permission> getPermissions() {
        return EnumSet.copyOf(permissions);
    }
}

public class EnumSetPerformance {
    public static void main(String[] args) {
        System.out.println("=== EnumSet 성능 ===\n");
        
        PermissionManager manager = new PermissionManager();
        
        // 권한 부여
        manager.grantAll(Permission.READ, Permission.WRITE);
        
        System.out.println("READ 권한: " + manager.hasPermission(Permission.READ));
        System.out.println("DELETE 권한: " + manager.hasPermission(Permission.DELETE));
        
        
        System.out.println("\n=== EnumSet 장점 ===");
        System.out.println("✅ 비트 벡터 (매우 빠름)");
        System.out.println("✅ 집합 연산 최적화");
        System.out.println("✅ 메모리 효율 (long 배열)");
    }
}
```

---

## 6. Enum vs Alternatives

### 6.1 Enum vs 상수

```java
public class EnumVsConstants {
    // ❌ 상수 방식
    public static final int MONDAY = 1;
    public static final int TUESDAY = 2;
    public static final int WEDNESDAY = 3;
    
    // ✅ Enum 방식
    enum Day {
        MONDAY, TUESDAY, WEDNESDAY
    }
    
    public static void main(String[] args) {
        System.out.println("=== Enum vs 상수 비교 ===\n");
        
        System.out.println("상수의 문제점:");
        System.out.println("❌ 타입 안전성 없음");
        System.out.println("❌ 네임스페이스 오염");
        System.out.println("❌ 의미 없는 연산 가능");
        System.out.println("❌ 출력 시 숫자만 표시\n");
        
        System.out.println("Enum의 장점:");
        System.out.println("✅ 타입 안전");
        System.out.println("✅ 네임스페이스 제공");
        System.out.println("✅ 메서드/필드 추가 가능");
        System.out.println("✅ switch 문 최적화");
    }
}
```

### 6.2 Enum vs String

```java
public class EnumVsString {
    // ❌ String 방식
    static void processStatusBad(String status) {
        if ("PENDING".equals(status)) {
            // 오타 위험: "PENDIN", "pending"
        }
    }
    
    // ✅ Enum 방식
    enum Status {
        PENDING, APPROVED, REJECTED
    }
    
    static void processStatusGood(Status status) {
        switch (status) {
            case PENDING:
                // 컴파일 타임 체크
                break;
            case APPROVED:
                break;
            case REJECTED:
                break;
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Enum vs String ===\n");
        
        System.out.println("String 문제점:");
        System.out.println("❌ 오타 위험");
        System.out.println("❌ 대소문자 구분");
        System.out.println("❌ 컴파일 체크 없음");
        System.out.println("❌ 자동완성 없음\n");
        
        System.out.println("Enum 장점:");
        System.out.println("✅ 컴파일 타임 체크");
        System.out.println("✅ 자동완성 지원");
        System.out.println("✅ 리팩토링 안전");
        System.out.println("✅ 타입 안전");
    }
}
```

---

## 7. 실전 시나리오

### 7.1 API 응답 코드

```java
enum ApiResponse {
    SUCCESS(200, "성공", true),
    CREATED(201, "생성됨", true),
    BAD_REQUEST(400, "잘못된 요청", false) {
        @Override
        public String buildErrorMessage(String detail) {
            return "요청을 확인해주세요: " + detail;
        }
    },
    UNAUTHORIZED(401, "인증 실패", false) {
        @Override
        public String buildErrorMessage(String detail) {
            return "로그인이 필요합니다: " + detail;
        }
    },
    NOT_FOUND(404, "없음", false) {
        @Override
        public String buildErrorMessage(String detail) {
            return "리소스를 찾을 수 없습니다: " + detail;
        }
    },
    SERVER_ERROR(500, "서버 오류", false) {
        @Override
        public String buildErrorMessage(String detail) {
            return "서버 오류가 발생했습니다: " + detail;
        }
    };
    
    private final int code;
    private final String message;
    private final boolean success;
    
    ApiResponse(int code, String message, boolean success) {
        this.code = code;
        this.message = message;
        this.success = success;
    }
    
    public int getCode() {
        return code;
    }
    
    public String getMessage() {
        return message;
    }
    
    public boolean isSuccess() {
        return success;
    }
    
    public String buildErrorMessage(String detail) {
        return message + ": " + detail;
    }
    
    public static ApiResponse fromCode(int code) {
        for (ApiResponse response : values()) {
            if (response.code == code) {
                return response;
            }
        }
        return SERVER_ERROR;
    }
}

class Response<T> {
    private ApiResponse status;
    private T data;
    private String error;
    
    private Response(ApiResponse status, T data, String error) {
        this.status = status;
        this.data = data;
        this.error = error;
    }
    
    public static <T> Response<T> success(T data) {
        return new Response<>(ApiResponse.SUCCESS, data, null);
    }
    
    public static <T> Response<T> error(ApiResponse status, String error) {
        return new Response<>(status, null, error);
    }
    
    public void print() {
        System.out.printf("[%d] %s%n", status.getCode(), status.getMessage());
        if (status.isSuccess()) {
            System.out.println("데이터: " + data);
        } else {
            System.out.println("오류: " + error);
        }
    }
}

public class ApiResponseScenario {
    public static void main(String[] args) {
        System.out.println("=== API 응답 처리 ===\n");
        
        Response<String> success = Response.success("사용자 정보");
        success.print();
        
        System.out.println();
        
        Response<Object> error = Response.error(
            ApiResponse.NOT_FOUND, 
            ApiResponse.NOT_FOUND.buildErrorMessage("사용자 ID 123")
        );
        error.print();
    }
}
```

### 7.2 워크플로우 엔진

```java
import java.util.*;

enum WorkflowState {
    DRAFT("작성 중") {
        @Override
        public WorkflowState submit() {
            return PENDING_REVIEW;
        }
        
        @Override
        public List<WorkflowState> getNextStates() {
            return Arrays.asList(PENDING_REVIEW, CANCELLED);
        }
    },
    PENDING_REVIEW("검토 대기") {
        @Override
        public WorkflowState approve() {
            return APPROVED;
        }
        
        @Override
        public WorkflowState reject() {
            return REJECTED;
        }
        
        @Override
        public List<WorkflowState> getNextStates() {
            return Arrays.asList(APPROVED, REJECTED, CANCELLED);
        }
    },
    APPROVED("승인됨") {
        @Override
        public List<WorkflowState> getNextStates() {
            return Collections.emptyList();
        }
    },
    REJECTED("거부됨") {
        @Override
        public WorkflowState submit() {
            return PENDING_REVIEW;
        }
        
        @Override
        public List<WorkflowState> getNextStates() {
            return Arrays.asList(PENDING_REVIEW);
        }
    },
    CANCELLED("취소됨") {
        @Override
        public List<WorkflowState> getNextStates() {
            return Collections.emptyList();
        }
    };
    
    private final String description;
    
    WorkflowState(String description) {
        this.description = description;
    }
    
    public String getDescription() {
        return description;
    }
    
    public WorkflowState submit() {
        throw new IllegalStateException("Cannot submit from " + this);
    }
    
    public WorkflowState approve() {
        throw new IllegalStateException("Cannot approve from " + this);
    }
    
    public WorkflowState reject() {
        throw new IllegalStateException("Cannot reject from " + this);
    }
    
    public abstract List<WorkflowState> getNextStates();
    
    public boolean canTransitionTo(WorkflowState target) {
        return getNextStates().contains(target);
    }
}

class Workflow {
    private WorkflowState state;
    
    Workflow() {
        this.state = WorkflowState.DRAFT;
    }
    
    public void submit() {
        state = state.submit();
        System.out.println("→ " + state.getDescription());
    }
    
    public void approve() {
        state = state.approve();
        System.out.println("→ " + state.getDescription());
    }
    
    public void reject() {
        state = state.reject();
        System.out.println("→ " + state.getDescription());
    }
    
    public WorkflowState getState() {
        return state;
    }
    
    public void printPossibleTransitions() {
        System.out.println("가능한 전환:");
        for (WorkflowState next : state.getNextStates()) {
            System.out.println("  - " + next.getDescription());
        }
    }
}

public class WorkflowEngine {
    public static void main(String[] args) {
        System.out.println("=== 워크플로우 엔진 ===\n");
        
        Workflow wf = new Workflow();
        System.out.println("초기: " + wf.getState().getDescription());
        
        wf.printPossibleTransitions();
        
        System.out.println("\n작업 진행:");
        wf.submit();
        wf.approve();
        
        wf.printPossibleTransitions();
    }
}
```

---

## 8. Common Pitfalls

### 8.1 ordinal() 의존

```java
enum Priority {
    LOW, MEDIUM, HIGH
}

public class OrdinalPitfall {
    public static void main(String[] args) {
        System.out.println("=== ordinal() 위험성 ===\n");
        
        // ❌ 나쁜 예 - ordinal() 의존
        Priority p = Priority.MEDIUM;
        int level = p.ordinal();  // 순서에 의존
        
        System.out.println("현재 ordinal: " + level);
        System.out.println("→ 상수 추가/제거 시 깨짐!\n");
        
        // ✅ 좋은 예 - 명시적 값
        System.out.println("해결책:");
        System.out.println("enum에 level 필드 추가");
        System.out.println("ordinal() 사용 금지");
    }
}
```

### 8.2 Enum 직렬화

```java
import java.io.*;

enum Status implements Serializable {
    PENDING, APPROVED, REJECTED
}

public class SerializationPitfall {
    public static void main(String[] args) {
        System.out.println("=== Enum 직렬화 ===\n");
        
        System.out.println("✅ 자동 안전");
        System.out.println("- 싱글톤 보장");
        System.out.println("- 순서 변경 OK");
        System.out.println("- Reflection 안전\n");
        
        System.out.println("⚠️ 주의사항");
        System.out.println("- 상수 제거 시 문제");
        System.out.println("- 하위 호환성 고려");
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: HTTP 메서드

```java
// HTTP 메서드 Enum 완성
enum HttpMethod {
    GET, POST, PUT, DELETE;
    
    // Safe? (데이터 변경 안 함)
    public boolean isSafe() {
        // 구현
        return false;
    }
    
    // Idempotent? (여러 번 실행해도 같은 결과)
    public boolean isIdempotent() {
        // 구현
        return false;
    }
}

public class Problem1 {
    public static void main(String[] args) {
        System.out.println("GET safe? " + HttpMethod.GET.isSafe());        // true
        System.out.println("POST safe? " + HttpMethod.POST.isSafe());      // false
        System.out.println("PUT idempotent? " + HttpMethod.PUT.isIdempotent());  // true
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
enum HttpMethod {
    GET, POST, PUT, DELETE;
    
    public boolean isSafe() {
        return this == GET;
    }
    
    public boolean isIdempotent() {
        return this != POST;
    }
}
```
</details>

---

### 문제 2: 파일 형식 검증

```java
// 파일 확장자별 검증
enum FileType {
    IMAGE("image/*", new String[]{"jpg", "png", "gif"}),
    DOCUMENT("application/*", new String[]{"pdf", "docx", "txt"}),
    VIDEO("video/*", new String[]{"mp4", "avi", "mov"});
    
    private final String mimeType;
    private final String[] extensions;
    
    FileType(String mimeType, String[] extensions) {
        this.mimeType = mimeType;
        this.extensions = extensions;
    }
    
    // 확장자로 FileType 찾기
    public static FileType fromExtension(String ext) {
        // 구현
        return null;
    }
    
    // 확장자가 유효한지 검증
    public boolean isValidExtension(String ext) {
        // 구현
        return false;
    }
}

public class Problem2 {
    public static void main(String[] args) {
        FileType type = FileType.fromExtension("jpg");
        System.out.println(type);  // IMAGE
        
        System.out.println(FileType.IMAGE.isValidExtension("png"));  // true
        System.out.println(FileType.IMAGE.isValidExtension("pdf"));  // false
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static FileType fromExtension(String ext) {
    for (FileType type : values()) {
        if (type.isValidExtension(ext)) {
            return type;
        }
    }
    return null;
}

public boolean isValidExtension(String ext) {
    for (String e : extensions) {
        if (e.equalsIgnoreCase(ext)) {
            return true;
        }
    }
    return false;
}
```
</details>

---

### 문제 3: 전략 패턴

```java
// 압축 전략
enum CompressionStrategy {
    ZIP {
        public byte[] compress(byte[] data) {
            // 구현
            return data;
        }
    },
    GZIP {
        public byte[] compress(byte[] data) {
            // 구현
            return data;
        }
    },
    NONE {
        public byte[] compress(byte[] data) {
            return data;
        }
    };
    
    public abstract byte[] compress(byte[] data);
    
    public int getCompressionLevel() {
        // 구현
        return 0;
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
enum CompressionStrategy {
    ZIP(9) {
        public byte[] compress(byte[] data) {
            // ZIP 압축 로직
            System.out.println("ZIP 압축: " + compressionLevel);
            return data;  // 실제로는 압축된 데이터 반환
        }
    },
    GZIP(6) {
        public byte[] compress(byte[] data) {
            // GZIP 압축 로직
            System.out.println("GZIP 압축: " + compressionLevel);
            return data;
        }
    },
    NONE(0) {
        public byte[] compress(byte[] data) {
            return data;
        }
    };
    
    protected final int compressionLevel;
    
    CompressionStrategy(int level) {
        this.compressionLevel = level;
    }
    
    public abstract byte[] compress(byte[] data);
    
    public int getCompressionLevel() {
        return compressionLevel;
    }
}
```
</details>

---

## 📌 핵심 정리

### Constant Specific Method
```java
enum Type {
    A {
        public void method() { }
    },
    B {
        public void method() { }
    };
    
    public abstract void method();
}
```

### 성능 최적화
```java
EnumMap<Key, Value>     // O(1), 배열 기반
EnumSet<Element>        // 비트 연산, 매우 빠름
```

### Best Practices
```
✅ ordinal() 사용 금지
✅ EnumMap/EnumSet 활용
✅ Constant Specific Method
✅ 불변성 유지
❌ switch 문보다 추상 메서드
```

### Common Pitfalls
```
❌ ordinal() 의존
❌ 상수 제거 시 호환성
❌ toString() 오버라이드 주의
✅ 명시적 필드 사용
✅ 하위 호환성 고려
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. Enum Advanced](./Enum-02-Advanced.md)**

</div>
