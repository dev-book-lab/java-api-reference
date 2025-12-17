# Enum 02. Enum Advanced 완전 정복

> 고급 Enum 활용 - 인터페이스, 전략 패턴, Singleton  
> Java Enum

---

## 📑 목차

1. [Enum과 인터페이스](#1-enum과-인터페이스)
2. [전략 패턴](#2-전략-패턴)
3. [Enum 싱글톤](#3-enum-싱글톤)
4. [Enum으로 상태 머신](#4-enum으로-상태-머신)
5. [Enum 그룹화](#5-enum-그룹화)
6. [Enum 확장](#6-enum-확장)
7. [실전 디자인 패턴](#7-실전-디자인-패턴)
8. [Best Practices](#8-best-practices)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Enum과 인터페이스

### 1.1 인터페이스 구현

```java
interface Describable {
    String getDescription();
}

enum Planet implements Describable {
    MERCURY("수성", 0.38),
    VENUS("금성", 0.91),
    EARTH("지구", 1.0),
    MARS("화성", 0.38);
    
    private final String korName;
    private final double gravity;
    
    Planet(String korName, double gravity) {
        this.korName = korName;
        this.gravity = gravity;
    }
    
    @Override
    public String getDescription() {
        return String.format("%s (중력: %.2fg)", korName, gravity);
    }
    
    public String getKorName() {
        return korName;
    }
    
    public double getGravity() {
        return gravity;
    }
}

public class EnumWithInterface {
    public static void printDescription(Describable d) {
        System.out.println(d.getDescription());
    }
    
    public static void main(String[] args) {
        System.out.println("=== Enum 인터페이스 구현 ===\n");
        
        for (Planet planet : Planet.values()) {
            printDescription(planet);
        }
        
        
        System.out.println("\n=== 다형성 ===");
        Describable earth = Planet.EARTH;
        System.out.println(earth.getDescription());
    }
}
```

### 1.2 여러 인터페이스 구현

```java
interface Comparable<T> {
    int compareTo(T other);
}

interface Printable {
    void print();
}

enum Priority implements Comparable<Priority>, Printable {
    LOW(1, "낮음"),
    MEDIUM(2, "보통"),
    HIGH(3, "높음"),
    CRITICAL(4, "긴급");
    
    private final int level;
    private final String korName;
    
    Priority(int level, String korName) {
        this.level = level;
        this.korName = korName;
    }
    
    @Override
    public int compareTo(Priority other) {
        return Integer.compare(this.level, other.level);
    }
    
    @Override
    public void print() {
        System.out.println(korName + " (레벨 " + level + ")");
    }
    
    public int getLevel() {
        return level;
    }
}

public class MultipleInterfaces {
    public static void main(String[] args) {
        System.out.println("=== 여러 인터페이스 ===\n");
        
        for (Priority p : Priority.values()) {
            p.print();
        }
        
        
        System.out.println("\n=== 비교 ===");
        if (Priority.HIGH.compareTo(Priority.LOW) > 0) {
            System.out.println("HIGH가 LOW보다 우선순위 높음");
        }
    }
}
```

---

## 2. 전략 패턴

### 2.1 기본 전략 패턴

```java
// 전략 인터페이스
interface DiscountStrategy {
    double applyDiscount(double price);
}

// Enum으로 전략 구현
enum DiscountType implements DiscountStrategy {
    NO_DISCOUNT {
        @Override
        public double applyDiscount(double price) {
            return price;
        }
    },
    PERCENTAGE_10 {
        @Override
        public double applyDiscount(double price) {
            return price * 0.9;
        }
    },
    PERCENTAGE_20 {
        @Override
        public double applyDiscount(double price) {
            return price * 0.8;
        }
    },
    FIXED_1000 {
        @Override
        public double applyDiscount(double price) {
            return Math.max(0, price - 1000);
        }
    };
}

public class StrategyPatternBasic {
    public static void main(String[] args) {
        System.out.println("=== 할인 전략 ===\n");
        
        double price = 10000;
        
        for (DiscountType discount : DiscountType.values()) {
            System.out.printf("%s: %.0f원 → %.0f원%n",
                discount,
                price,
                discount.applyDiscount(price));
        }
    }
}
```

### 2.2 복잡한 전략 패턴

```java
interface PaymentProcessor {
    boolean processPayment(double amount);
    String getProcessorName();
}

enum PaymentMethod implements PaymentProcessor {
    CREDIT_CARD("신용카드") {
        @Override
        public boolean processPayment(double amount) {
            System.out.println("신용카드 결제: " + amount + "원");
            // 신용카드 처리 로직
            return true;
        }
    },
    BANK_TRANSFER("계좌이체") {
        @Override
        public boolean processPayment(double amount) {
            System.out.println("계좌이체: " + amount + "원");
            // 계좌이체 처리 로직
            return true;
        }
    },
    MOBILE_PAY("모바일 결제") {
        @Override
        public boolean processPayment(double amount) {
            System.out.println("모바일 결제: " + amount + "원");
            // 모바일 결제 처리 로직
            return true;
        }
    },
    CRYPTO("암호화폐") {
        @Override
        public boolean processPayment(double amount) {
            System.out.println("암호화폐 결제: " + amount + "원");
            // 암호화폐 처리 로직
            return true;
        }
    };
    
    private final String displayName;
    
    PaymentMethod(String displayName) {
        this.displayName = displayName;
    }
    
    @Override
    public String getProcessorName() {
        return displayName;
    }
}

public class PaymentProcessorExample {
    public static void main(String[] args) {
        System.out.println("=== 결제 처리 ===\n");
        
        double amount = 50000;
        PaymentMethod method = PaymentMethod.MOBILE_PAY;
        
        System.out.println("결제 수단: " + method.getProcessorName());
        boolean success = method.processPayment(amount);
        System.out.println("결과: " + (success ? "성공" : "실패"));
    }
}
```

---

## 3. Enum 싱글톤

### 3.1 기본 싱글톤

```java
// Enum 싱글톤 (가장 안전하고 간단)
enum Singleton {
    INSTANCE;
    
    private int value;
    
    public void setValue(int value) {
        this.value = value;
    }
    
    public int getValue() {
        return value;
    }
    
    public void doSomething() {
        System.out.println("싱글톤 작업: " + value);
    }
}

public class EnumSingleton {
    public static void main(String[] args) {
        System.out.println("=== Enum 싱글톤 ===\n");
        
        // 인스턴스 가져오기
        Singleton s1 = Singleton.INSTANCE;
        Singleton s2 = Singleton.INSTANCE;
        
        System.out.println("s1 == s2: " + (s1 == s2));
        
        
        // 사용
        s1.setValue(100);
        s2.doSomething();  // 100
        
        System.out.println("s2 값: " + s2.getValue());
        
        
        System.out.println("\n=== 장점 ===");
        System.out.println("✅ 스레드 안전");
        System.out.println("✅ Serialization 안전");
        System.out.println("✅ Reflection 공격 방어");
        System.out.println("✅ 간단한 코드");
    }
}
```

### 3.2 실전 싱글톤

```java
enum DatabaseConnection {
    INSTANCE;
    
    private java.util.Map<String, String> config;
    private boolean connected;
    
    DatabaseConnection() {
        config = new java.util.HashMap<>();
        config.put("url", "jdbc:mysql://localhost:3306/mydb");
        config.put("user", "root");
        config.put("password", "");
        connected = false;
    }
    
    public void connect() {
        if (!connected) {
            System.out.println("DB 연결 중...");
            System.out.println("URL: " + config.get("url"));
            connected = true;
            System.out.println("연결 완료!");
        } else {
            System.out.println("이미 연결됨");
        }
    }
    
    public void disconnect() {
        if (connected) {
            System.out.println("연결 해제");
            connected = false;
        }
    }
    
    public boolean isConnected() {
        return connected;
    }
    
    public void executeQuery(String query) {
        if (connected) {
            System.out.println("쿼리 실행: " + query);
        } else {
            System.out.println("연결되지 않음!");
        }
    }
}

public class DatabaseSingleton {
    public static void main(String[] args) {
        System.out.println("=== DB 싱글톤 ===\n");
        
        DatabaseConnection db = DatabaseConnection.INSTANCE;
        
        db.connect();
        db.executeQuery("SELECT * FROM users");
        db.disconnect();
        
        
        // 다른 곳에서 사용
        DatabaseConnection db2 = DatabaseConnection.INSTANCE;
        System.out.println("\n같은 인스턴스: " + (db == db2));
    }
}
```

---

## 4. Enum으로 상태 머신

### 4.1 기본 상태 머신

```java
enum State {
    IDLE {
        @Override
        public State next() {
            return RUNNING;
        }
    },
    RUNNING {
        @Override
        public State next() {
            return PAUSED;
        }
    },
    PAUSED {
        @Override
        public State next() {
            return RUNNING;
        }
        
        @Override
        public State stop() {
            return STOPPED;
        }
    },
    STOPPED {
        @Override
        public State next() {
            return IDLE;
        }
    };
    
    public abstract State next();
    
    public State stop() {
        return STOPPED;
    }
}

public class StateMachine {
    public static void main(String[] args) {
        System.out.println("=== 상태 머신 ===\n");
        
        State current = State.IDLE;
        System.out.println("초기: " + current);
        
        current = current.next();
        System.out.println("다음: " + current);
        
        current = current.next();
        System.out.println("다음: " + current);
        
        current = current.stop();
        System.out.println("중지: " + current);
    }
}
```

### 4.2 복잡한 상태 머신

```java
enum OrderState {
    PENDING("주문 대기") {
        @Override
        public OrderState confirm() {
            return CONFIRMED;
        }
        
        @Override
        public OrderState cancel() {
            return CANCELLED;
        }
    },
    CONFIRMED("주문 확인") {
        @Override
        public OrderState ship() {
            return SHIPPED;
        }
        
        @Override
        public OrderState cancel() {
            return CANCELLED;
        }
    },
    SHIPPED("배송 중") {
        @Override
        public OrderState deliver() {
            return DELIVERED;
        }
    },
    DELIVERED("배송 완료") {
        @Override
        public OrderState returnOrder() {
            return RETURNED;
        }
    },
    RETURNED("반품"),
    CANCELLED("취소");
    
    private final String description;
    
    OrderState(String description) {
        this.description = description;
    }
    
    public String getDescription() {
        return description;
    }
    
    // 기본 구현 (전환 불가)
    public OrderState confirm() {
        throw new IllegalStateException("Cannot confirm from " + this);
    }
    
    public OrderState ship() {
        throw new IllegalStateException("Cannot ship from " + this);
    }
    
    public OrderState deliver() {
        throw new IllegalStateException("Cannot deliver from " + this);
    }
    
    public OrderState returnOrder() {
        throw new IllegalStateException("Cannot return from " + this);
    }
    
    public OrderState cancel() {
        throw new IllegalStateException("Cannot cancel from " + this);
    }
}

class Order {
    private OrderState state;
    
    Order() {
        this.state = OrderState.PENDING;
    }
    
    public void confirm() {
        state = state.confirm();
        System.out.println("→ " + state.getDescription());
    }
    
    public void ship() {
        state = state.ship();
        System.out.println("→ " + state.getDescription());
    }
    
    public void deliver() {
        state = state.deliver();
        System.out.println("→ " + state.getDescription());
    }
    
    public void cancel() {
        state = state.cancel();
        System.out.println("→ " + state.getDescription());
    }
    
    public OrderState getState() {
        return state;
    }
}

public class OrderStateMachine {
    public static void main(String[] args) {
        System.out.println("=== 주문 상태 머신 ===\n");
        
        Order order = new Order();
        System.out.println("초기: " + order.getState().getDescription());
        
        order.confirm();
        order.ship();
        order.deliver();
        
        
        // 잘못된 전환 시도
        System.out.println("\n=== 잘못된 전환 ===");
        try {
            order.ship();  // DELIVERED에서 ship 불가
        } catch (IllegalStateException e) {
            System.out.println("오류: " + e.getMessage());
        }
    }
}
```

---

## 5. Enum 그룹화

### 5.1 카테고리별 그룹

```java
interface Category {
    String getCategory();
}

enum Product implements Category {
    // 전자제품
    LAPTOP("전자제품", 1_000_000),
    PHONE("전자제품", 800_000),
    TABLET("전자제품", 600_000),
    
    // 가구
    DESK("가구", 200_000),
    CHAIR("가구", 150_000),
    SOFA("가구", 500_000),
    
    // 의류
    SHIRT("의류", 30_000),
    PANTS("의류", 50_000),
    JACKET("의류", 100_000);
    
    private final String category;
    private final int price;
    
    Product(String category, int price) {
        this.category = category;
        this.price = price;
    }
    
    @Override
    public String getCategory() {
        return category;
    }
    
    public int getPrice() {
        return price;
    }
    
    public static java.util.List<Product> getByCategory(String category) {
        java.util.List<Product> result = new java.util.ArrayList<>();
        for (Product p : values()) {
            if (p.category.equals(category)) {
                result.add(p);
            }
        }
        return result;
    }
}

public class EnumGrouping {
    public static void main(String[] args) {
        System.out.println("=== 카테고리별 상품 ===\n");
        
        System.out.println("전자제품:");
        for (Product p : Product.getByCategory("전자제품")) {
            System.out.printf("  %s: %,d원%n", p, p.getPrice());
        }
        
        System.out.println("\n가구:");
        for (Product p : Product.getByCategory("가구")) {
            System.out.printf("  %s: %,d원%n", p, p.getPrice());
        }
    }
}
```

### 5.2 EnumSet으로 그룹

```java
import java.util.*;

enum Permission {
    READ, WRITE, EXECUTE, DELETE, ADMIN
}

enum Role {
    GUEST(EnumSet.of(Permission.READ)),
    USER(EnumSet.of(Permission.READ, Permission.WRITE)),
    MODERATOR(EnumSet.of(Permission.READ, Permission.WRITE, Permission.DELETE)),
    ADMIN(EnumSet.allOf(Permission.class));
    
    private final EnumSet<Permission> permissions;
    
    Role(EnumSet<Permission> permissions) {
        this.permissions = permissions;
    }
    
    public boolean hasPermission(Permission permission) {
        return permissions.contains(permission);
    }
    
    public EnumSet<Permission> getPermissions() {
        return EnumSet.copyOf(permissions);
    }
}

public class RoleBasedPermissions {
    public static void main(String[] args) {
        System.out.println("=== 역할별 권한 ===\n");
        
        for (Role role : Role.values()) {
            System.out.println(role + ":");
            for (Permission p : role.getPermissions()) {
                System.out.println("  - " + p);
            }
        }
        
        
        System.out.println("\n=== 권한 확인 ===");
        Role user = Role.USER;
        System.out.println("USER READ: " + user.hasPermission(Permission.READ));
        System.out.println("USER DELETE: " + user.hasPermission(Permission.DELETE));
    }
}
```

---

## 6. Enum 확장

### 6.1 인터페이스로 확장

```java
interface Operation {
    double apply(double x, double y);
}

enum BasicOperation implements Operation {
    PLUS("+") {
        public double apply(double x, double y) { return x + y; }
    },
    MINUS("-") {
        public double apply(double x, double y) { return x - y; }
    },
    TIMES("*") {
        public double apply(double x, double y) { return x * y; }
    },
    DIVIDE("/") {
        public double apply(double x, double y) { return x / y; }
    };
    
    private final String symbol;
    
    BasicOperation(String symbol) {
        this.symbol = symbol;
    }
    
    public String getSymbol() {
        return symbol;
    }
}

enum ExtendedOperation implements Operation {
    EXP("^") {
        public double apply(double x, double y) { return Math.pow(x, y); }
    },
    REMAINDER("%") {
        public double apply(double x, double y) { return x % y; }
    };
    
    private final String symbol;
    
    ExtendedOperation(String symbol) {
        this.symbol = symbol;
    }
    
    public String getSymbol() {
        return symbol;
    }
}

public class EnumExtension {
    public static void test(Class<? extends Enum<? extends Operation>> opEnumClass,
                           double x, double y) {
        for (Operation op : opEnumClass.getEnumConstants()) {
            System.out.printf("%f %s %f = %f%n",
                x, ((Enum<?>) op).name(), y, op.apply(x, y));
        }
    }
    
    public static void main(String[] args) {
        double x = 10;
        double y = 5;
        
        System.out.println("=== 기본 연산 ===");
        test(BasicOperation.class, x, y);
        
        System.out.println("\n=== 확장 연산 ===");
        test(ExtendedOperation.class, x, y);
    }
}
```

### 6.2 포함으로 확장

```java
class Operation {
    enum Type {
        PLUS, MINUS, TIMES, DIVIDE
    }
    
    private final Type type;
    private final String description;
    
    Operation(Type type, String description) {
        this.type = type;
        this.description = description;
    }
    
    public double apply(double x, double y) {
        switch (type) {
            case PLUS: return x + y;
            case MINUS: return x - y;
            case TIMES: return x * y;
            case DIVIDE: return x / y;
            default: throw new AssertionError();
        }
    }
    
    public String getDescription() {
        return description;
    }
    
    // 미리 정의된 연산들
    public static final Operation ADD = new Operation(Type.PLUS, "덧셈");
    public static final Operation SUBTRACT = new Operation(Type.MINUS, "뺄셈");
    public static final Operation MULTIPLY = new Operation(Type.TIMES, "곱셈");
    public static final Operation DIVIDE_OP = new Operation(Type.DIVIDE, "나눗셈");
}

public class CompositionExtension {
    public static void main(String[] args) {
        Operation op = Operation.ADD;
        System.out.println(op.getDescription() + ": " + op.apply(10, 5));
    }
}
```

---

## 7. 실전 디자인 패턴

### 7.1 Command 패턴

```java
interface Command {
    void execute();
    void undo();
}

enum TextCommand implements Command {
    BOLD {
        @Override
        public void execute() {
            System.out.println("텍스트를 굵게");
        }
        
        @Override
        public void undo() {
            System.out.println("굵게 취소");
        }
    },
    ITALIC {
        @Override
        public void execute() {
            System.out.println("텍스트를 기울임");
        }
        
        @Override
        public void undo() {
            System.out.println("기울임 취소");
        }
    },
    UNDERLINE {
        @Override
        public void execute() {
            System.out.println("텍스트에 밑줄");
        }
        
        @Override
        public void undo() {
            System.out.println("밑줄 취소");
        }
    };
}

public class CommandPattern {
    public static void main(String[] args) {
        System.out.println("=== Command 패턴 ===\n");
        
        Command cmd = TextCommand.BOLD;
        cmd.execute();
        cmd.undo();
    }
}
```

### 7.2 Factory 패턴

```java
interface Shape {
    void draw();
    double area();
}

enum ShapeFactory {
    CIRCLE {
        @Override
        public Shape create(double... params) {
            return new Circle(params[0]);
        }
    },
    RECTANGLE {
        @Override
        public Shape create(double... params) {
            return new Rectangle(params[0], params[1]);
        }
    },
    TRIANGLE {
        @Override
        public Shape create(double... params) {
            return new Triangle(params[0], params[1]);
        }
    };
    
    public abstract Shape create(double... params);
}

class Circle implements Shape {
    private double radius;
    
    Circle(double radius) {
        this.radius = radius;
    }
    
    @Override
    public void draw() {
        System.out.println("원 그리기 (반지름: " + radius + ")");
    }
    
    @Override
    public double area() {
        return Math.PI * radius * radius;
    }
}

class Rectangle implements Shape {
    private double width, height;
    
    Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }
    
    @Override
    public void draw() {
        System.out.println("사각형 그리기 (" + width + " x " + height + ")");
    }
    
    @Override
    public double area() {
        return width * height;
    }
}

class Triangle implements Shape {
    private double base, height;
    
    Triangle(double base, double height) {
        this.base = base;
        this.height = height;
    }
    
    @Override
    public void draw() {
        System.out.println("삼각형 그리기 (밑변: " + base + ", 높이: " + height + ")");
    }
    
    @Override
    public double area() {
        return base * height / 2;
    }
}

public class FactoryPattern {
    public static void main(String[] args) {
        System.out.println("=== Factory 패턴 ===\n");
        
        Shape circle = ShapeFactory.CIRCLE.create(5);
        circle.draw();
        System.out.println("면적: " + circle.area());
        
        Shape rect = ShapeFactory.RECTANGLE.create(4, 6);
        rect.draw();
        System.out.println("면적: " + rect.area());
    }
}
```

---

## 8. Best Practices

### 8.1 명확한 이름

```java
// ❌ 나쁜 예
enum Status {
    S1, S2, S3  // 의미 불명확
}

// ✅ 좋은 예
enum OrderStatus {
    PENDING, CONFIRMED, SHIPPED, DELIVERED, CANCELLED
}

public class NamingBestPractices {
    public static void main(String[] args) {
        System.out.println("=== 명명 규칙 ===\n");
        
        System.out.println("✅ DO");
        System.out.println("- 의미 명확한 이름");
        System.out.println("- 일관된 명명 규칙");
        System.out.println("- 축약 최소화\n");
        
        System.out.println("❌ DON'T");
        System.out.println("- 숫자/약어만 사용");
        System.out.println("- 모호한 이름");
        System.out.println("- 일관성 없는 스타일");
    }
}
```

### 8.2 불변성 유지

```java
// ✅ 좋은 예 - 불변
enum Config {
    DATABASE("jdbc:mysql://localhost", "root");
    
    private final String url;
    private final String user;
    
    Config(String url, String user) {
        this.url = url;
        this.user = user;
    }
    
    // getter만 제공
    public String getUrl() {
        return url;
    }
    
    public String getUser() {
        return user;
    }
}

// ❌ 나쁜 예 - 가변
enum BadConfig {
    INSTANCE;
    
    private String value;  // 가변 필드
    
    public void setValue(String value) {
        this.value = value;  // 피하자
    }
}

public class ImmutabilityBestPractice {
    public static void main(String[] args) {
        System.out.println("=== 불변성 ===\n");
        
        System.out.println("✅ final 필드 사용");
        System.out.println("✅ getter만 제공");
        System.out.println("✅ 상태 변경 금지\n");
        
        System.out.println("예외: 싱글톤 패턴");
        System.out.println("- 상태 관리 필요 시");
        System.out.println("- 스레드 안전성 고려");
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 로그 레벨

```java
// 로그 레벨 Enum 구현
enum LogLevel {
    DEBUG, INFO, WARN, ERROR;
    
    // 현재 레벨보다 높은지 체크하는 메서드
    public boolean isHigherThan(LogLevel other) {
        // 구현
        return false;
    }
}

public class Problem1 {
    public static void main(String[] args) {
        LogLevel current = LogLevel.WARN;
        
        System.out.println(current.isHigherThan(LogLevel.DEBUG));  // true
        System.out.println(current.isHigherThan(LogLevel.ERROR));  // false
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public boolean isHigherThan(LogLevel other) {
    return this.ordinal() > other.ordinal();
}
```
</details>

---

### 문제 2: 상태 전환

```java
// 신호등 상태 머신
enum TrafficLight {
    RED, YELLOW, GREEN;
    
    // 다음 상태로 전환
    public TrafficLight next() {
        // 구현: RED → GREEN → YELLOW → RED
        return null;
    }
}

public class Problem2 {
    public static void main(String[] args) {
        TrafficLight light = TrafficLight.RED;
        
        light = light.next();
        System.out.println(light);  // GREEN
        
        light = light.next();
        System.out.println(light);  // YELLOW
        
        light = light.next();
        System.out.println(light);  // RED
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public TrafficLight next() {
    switch (this) {
        case RED: return GREEN;
        case GREEN: return YELLOW;
        case YELLOW: return RED;
        default: throw new AssertionError();
    }
}
```
</details>

---

### 문제 3: 싱글톤 설정

```java
// 설정 관리 싱글톤
enum AppConfig {
    INSTANCE;
    
    // 설정 저장용 Map
    // get, set 메서드 구현
}

public class Problem3 {
    public static void main(String[] args) {
        AppConfig config = AppConfig.INSTANCE;
        config.set("host", "localhost");
        config.set("port", "8080");
        
        System.out.println(config.get("host"));  // localhost
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
enum AppConfig {
    INSTANCE;
    
    private java.util.Map<String, String> config = new java.util.HashMap<>();
    
    public void set(String key, String value) {
        config.put(key, value);
    }
    
    public String get(String key) {
        return config.get(key);
    }
}
```
</details>

---

## 📌 핵심 정리

### 인터페이스 구현
```java
enum Type implements Interface1, Interface2 {
    VALUE1, VALUE2;
    
    @Override
    public void method() { }
}
```

### 전략 패턴
```java
enum Strategy {
    OPTION1 {
        public void execute() { }
    },
    OPTION2 {
        public void execute() { }
    };
    
    public abstract void execute();
}
```

### 싱글톤
```java
enum Singleton {
    INSTANCE;
    
    public void method() { }
}
```

### 상태 머신
```java
enum State {
    STATE1 {
        public State next() { return STATE2; }
    },
    STATE2 {
        public State next() { return STATE1; }
    };
    
    public abstract State next();
}
```

### Best Practices
```
✅ 명확한 이름
✅ final 필드 (불변)
✅ switch보다 추상 메서드
✅ EnumSet/EnumMap 활용
❌ ordinal() 의존 금지
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Enum Basic](./Enum-01-Basic.md) | [다음: 03. Enum Patterns →](./Enum-03-Patterns.md)**

</div>
