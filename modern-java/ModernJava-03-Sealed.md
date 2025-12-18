# Modern Java 03. Sealed Class 완전 정복

> 제한된 상속으로 안전한 계층 구조 만들기  
> Java 15+ (Preview), Java 17+ (Standard)

---

## 📑 목차

1. [Sealed Class 소개](#1-sealed-class-소개)
2. [기본 문법](#2-기본-문법)
3. [Sealed Interface](#3-sealed-interface)
4. [permits와 상속](#4-permits와-상속)
5. [패턴 매칭과 함께](#5-패턴-매칭과-함께)
6. [실전 활용 패턴](#6-실전-활용-패턴)
7. [Best Practices](#7-best-practices)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Sealed Class 소개

### 1.1 왜 Sealed Class인가?

```java
// ❌ 기존 방식 - 제어 불가능
class Shape {
    // 누구나 상속 가능!
}

class Circle extends Shape {}
class Rectangle extends Shape {}
// 외부에서 Unknown extends Shape {} 가능

// ✅ Sealed Class - 제어 가능
sealed class ShapeSealed permits CircleSealed, RectangleSealed {
    // Circle과 Rectangle만 상속 가능
}

final class CircleSealed extends ShapeSealed {}
final class RectangleSealed extends ShapeSealed {}
// 외부에서 상속 불가능!

public class WhySealedClass {
    public static void main(String[] args) {
        System.out.println("=== Sealed Class 장점 ===\n");
        
        System.out.println("📌 제한된 상속");
        System.out.println("- 허용된 하위 클래스만 지정");
        System.out.println("- 계층 구조 통제\n");
        
        System.out.println("📌 완전성 보장");
        System.out.println("- switch 문에서 default 불필요");
        System.out.println("- 모든 케이스 컴파일 타임 체크\n");
        
        System.out.println("📌 도메인 모델링");
        System.out.println("- 명확한 타입 정의");
        System.out.println("- 외부 확장 차단\n");
        
        System.out.println("📌 API 설계");
        System.out.println("- 안정적인 인터페이스");
        System.out.println("- 하위 호환성 유지");
    }
}
```

### 1.2 Sealed Class 특징

```java
sealed class Animal permits Dog, Cat {
    String name;
    
    Animal(String name) {
        this.name = name;
    }
}

// 하위 클래스는 반드시 다음 중 하나:
// 1. final - 더 이상 상속 불가
final class Dog extends Animal {
    Dog(String name) {
        super(name);
    }
}

// 2. sealed - 제한된 상속 가능
sealed class Cat extends Animal permits Persian, Siamese {
    Cat(String name) {
        super(name);
    }
}

// 3. non-sealed - 자유롭게 상속 가능
final class Persian extends Cat {
    Persian() {
        super("Persian");
    }
}

non-sealed class Siamese extends Cat {
    Siamese() {
        super("Siamese");
    }
}

// Siamese는 non-sealed이므로 확장 가능
class BlackSiamese extends Siamese {}

public class SealedCharacteristics {
    public static void main(String[] args) {
        System.out.println("=== Sealed Class 특징 ===\n");
        
        System.out.println("📌 하위 클래스 키워드");
        System.out.println("1. final: 더 이상 상속 불가");
        System.out.println("2. sealed: 제한된 상속");
        System.out.println("3. non-sealed: 자유 상속\n");
        
        System.out.println("📌 제약사항");
        System.out.println("- 같은 모듈/패키지");
        System.out.println("- permits 명시 필수 (일부 예외)");
    }
}
```

---

## 2. 기본 문법

### 2.1 간단한 Sealed Class

```java
// 기본 형태
sealed class Result permits Success, Failure {}

final class Success extends Result {
    private final Object value;
    
    Success(Object value) {
        this.value = value;
    }
    
    public Object getValue() {
        return value;
    }
}

final class Failure extends Result {
    private final String error;
    
    Failure(String error) {
        this.error = error;
    }
    
    public String getError() {
        return error;
    }
}

public class BasicSealedClass {
    public static Result divide(int a, int b) {
        if (b == 0) {
            return new Failure("Division by zero");
        }
        return new Success(a / b);
    }
    
    public static void main(String[] args) {
        System.out.println("=== 기본 Sealed Class ===\n");
        
        Result r1 = divide(10, 2);
        Result r2 = divide(10, 0);
        
        // switch에서 완전성 체크
        String message1 = switch (r1) {
            case Success s -> "결과: " + s.getValue();
            case Failure f -> "오류: " + f.getError();
        };
        
        String message2 = switch (r2) {
            case Success s -> "결과: " + s.getValue();
            case Failure f -> "오류: " + f.getError();
        };
        
        System.out.println(message1);
        System.out.println(message2);
    }
}
```

### 2.2 같은 파일에 정의

```java
// 같은 파일에 정의하면 permits 생략 가능
sealed class Vehicle {}

final class Car extends Vehicle {
    private final int doors;
    
    Car(int doors) {
        this.doors = doors;
    }
}

final class Motorcycle extends Vehicle {
    private final boolean hasSidecar;
    
    Motorcycle(boolean hasSidecar) {
        this.hasSidecar = hasSidecar;
    }
}

final class Truck extends Vehicle {
    private final double capacity;
    
    Truck(double capacity) {
        this.capacity = capacity;
    }
}

public class SameFileSealed {
    public static String describe(Vehicle vehicle) {
        return switch (vehicle) {
            case Car c -> "자동차";
            case Motorcycle m -> "오토바이";
            case Truck t -> "트럭";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 같은 파일 정의 ===\n");
        
        Vehicle car = new Car(4);
        Vehicle bike = new Motorcycle(false);
        Vehicle truck = new Truck(10.5);
        
        System.out.println(describe(car));
        System.out.println(describe(bike));
        System.out.println(describe(truck));
    }
}
```

---

## 3. Sealed Interface

### 3.1 기본 Sealed Interface

```java
sealed interface Payment permits CreditCard, BankTransfer, Cash {}

record CreditCard(String number, String cvv) implements Payment {}
record BankTransfer(String account, String bank) implements Payment {}
record Cash(double amount) implements Payment {}

public class SealedInterfaceBasic {
    public static String process(Payment payment) {
        return switch (payment) {
            case CreditCard cc -> "카드 결제: " + cc.number();
            case BankTransfer bt -> "계좌 이체: " + bt.bank();
            case Cash c -> "현금: " + c.amount() + "원";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Sealed Interface ===\n");
        
        Payment card = new CreditCard("1234-5678", "123");
        Payment transfer = new BankTransfer("123-456-789", "KB");
        Payment cash = new Cash(10000);
        
        System.out.println(process(card));
        System.out.println(process(transfer));
        System.out.println(process(cash));
    }
}
```

### 3.2 Record와 함께

```java
sealed interface Shape permits Circle, Rectangle, Triangle {}

record Circle(double radius) implements Shape {
    public double area() {
        return Math.PI * radius * radius;
    }
}

record Rectangle(double width, double height) implements Shape {
    public double area() {
        return width * height;
    }
}

record Triangle(double base, double height) implements Shape {
    public double area() {
        return base * height / 2;
    }
}

public class SealedWithRecord {
    public static double calculateArea(Shape shape) {
        return switch (shape) {
            case Circle c -> c.area();
            case Rectangle r -> r.area();
            case Triangle t -> t.area();
        };
    }
    
    public static String describe(Shape shape) {
        return switch (shape) {
            case Circle(double r) -> "원 (반지름: " + r + ")";
            case Rectangle(double w, double h) -> "사각형 (" + w + "×" + h + ")";
            case Triangle(double b, double h) -> "삼각형 (밑변: " + b + ", 높이: " + h + ")";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Record + Sealed ===\n");
        
        Shape circle = new Circle(5);
        Shape rect = new Rectangle(4, 6);
        Shape triangle = new Triangle(3, 4);
        
        System.out.println(describe(circle) + " - 면적: " + calculateArea(circle));
        System.out.println(describe(rect) + " - 면적: " + calculateArea(rect));
        System.out.println(describe(triangle) + " - 면적: " + calculateArea(triangle));
    }
}
```

---

## 4. permits와 상속

### 4.1 계층 구조

```java
// 최상위
sealed interface Expression permits Value, BinaryOp {}

// 중간 계층
sealed interface BinaryOp extends Expression permits Add, Multiply {}

// 리프 노드
record Value(int value) implements Expression {}
record Add(Expression left, Expression right) implements BinaryOp {}
record Multiply(Expression left, Expression right) implements BinaryOp {}

public class HierarchyExample {
    public static int evaluate(Expression expr) {
        return switch (expr) {
            case Value(int v) -> v;
            case Add(Expression left, Expression right) -> 
                evaluate(left) + evaluate(right);
            case Multiply(Expression left, Expression right) -> 
                evaluate(left) * evaluate(right);
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 계층 구조 ===\n");
        
        // (2 + 3) * 4
        Expression expr = new Multiply(
            new Add(new Value(2), new Value(3)),
            new Value(4)
        );
        
        System.out.println("결과: " + evaluate(expr));  // 20
    }
}
```

### 4.2 Non-Sealed 활용

```java
sealed interface Animal permits Dog, Cat, Bird {}

final class Dog implements Animal {
    private final String breed;
    
    Dog(String breed) {
        this.breed = breed;
    }
    
    public String getBreed() {
        return breed;
    }
}

non-sealed class Cat implements Animal {
    private final String color;
    
    Cat(String color) {
        this.color = color;
    }
}

// Cat은 non-sealed이므로 확장 가능
class PersianCat extends Cat {
    PersianCat() {
        super("White");
    }
}

class SiameseCat extends Cat {
    SiameseCat() {
        super("Brown");
    }
}

final class Bird implements Animal {}

public class NonSealedExample {
    public static String classify(Animal animal) {
        return switch (animal) {
            case Dog d -> "개: " + d.getBreed();
            case Cat c -> "고양이";  // 하위 타입까지 포함
            case Bird b -> "새";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Non-Sealed ===\n");
        
        Animal dog = new Dog("Poodle");
        Animal cat = new PersianCat();
        Animal bird = new Bird();
        
        System.out.println(classify(dog));
        System.out.println(classify(cat));
        System.out.println(classify(bird));
    }
}
```

---

## 5. 패턴 매칭과 함께

### 5.1 Record Pattern (Java 19+)

```java
sealed interface JsonValue permits JsonString, JsonNumber, JsonArray {}

record JsonString(String value) implements JsonValue {}
record JsonNumber(double value) implements JsonValue {}
record JsonArray(java.util.List<JsonValue> values) implements JsonValue {}

public class RecordPatternExample {
    public static String stringify(JsonValue json) {
        return switch (json) {
            case JsonString(String s) -> "\"" + s + "\"";
            case JsonNumber(double n) -> String.valueOf(n);
            case JsonArray(var values) -> {
                String items = values.stream()
                    .map(RecordPatternExample::stringify)
                    .collect(java.util.stream.Collectors.joining(", "));
                yield "[" + items + "]";
            }
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Record Pattern ===\n");
        
        JsonValue str = new JsonString("hello");
        JsonValue num = new JsonNumber(42);
        JsonValue arr = new JsonArray(java.util.List.of(
            new JsonString("a"),
            new JsonNumber(1),
            new JsonString("b")
        ));
        
        System.out.println(stringify(str));
        System.out.println(stringify(num));
        System.out.println(stringify(arr));
    }
}
```

### 5.2 Guarded Pattern

```java
sealed interface Status permits Pending, Processing, Completed, Failed {}

record Pending(java.time.Instant createdAt) implements Status {}
record Processing(int progress) implements Status {}
record Completed(java.time.Instant finishedAt) implements Status {}
record Failed(String reason) implements Status {}

public class GuardedPatternExample {
    public static String describe(Status status) {
        return switch (status) {
            case Pending p when isPast24Hours(p.createdAt()) -> 
                "지연됨 (24시간 초과)";
            case Pending p -> "대기 중";
            case Processing(int p) when p > 90 -> 
                "거의 완료 (" + p + "%)";
            case Processing(int p) -> 
                "처리 중 (" + p + "%)";
            case Completed c -> "완료됨";
            case Failed(String reason) -> 
                "실패: " + reason;
        };
    }
    
    static boolean isPast24Hours(java.time.Instant time) {
        return java.time.Duration.between(time, java.time.Instant.now())
            .toHours() > 24;
    }
    
    public static void main(String[] args) {
        System.out.println("=== Guarded Pattern ===\n");
        
        Status pending = new Pending(java.time.Instant.now().minusSeconds(3600 * 48));
        Status processing = new Processing(95);
        Status completed = new Completed(java.time.Instant.now());
        Status failed = new Failed("Connection timeout");
        
        System.out.println(describe(pending));
        System.out.println(describe(processing));
        System.out.println(describe(completed));
        System.out.println(describe(failed));
    }
}
```

---

## 6. 실전 활용 패턴

### 6.1 Result/Either 패턴

```java
sealed interface Result<T, E> permits Success, Failure {}

record Success<T, E>(T value) implements Result<T, E> {
    public <U> Result<U, E> map(java.util.function.Function<T, U> mapper) {
        return new Success<>(mapper.apply(value));
    }
}

record Failure<T, E>(E error) implements Result<T, E> {
    @SuppressWarnings("unchecked")
    public <U> Result<U, E> map(java.util.function.Function<T, U> mapper) {
        return (Result<U, E>) this;
    }
}

public class ResultPattern {
    public static Result<Integer, String> divide(int a, int b) {
        if (b == 0) {
            return new Failure<>("Division by zero");
        }
        return new Success<>(a / b);
    }
    
    public static Result<Integer, String> safeParse(String s) {
        try {
            return new Success<>(Integer.parseInt(s));
        } catch (NumberFormatException e) {
            return new Failure<>("Invalid number: " + s);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Result 패턴 ===\n");
        
        // 성공 케이스
        Result<Integer, String> r1 = divide(10, 2);
        String msg1 = switch (r1) {
            case Success(Integer v) -> "결과: " + v;
            case Failure(String e) -> "오류: " + e;
        };
        System.out.println(msg1);
        
        // 실패 케이스
        Result<Integer, String> r2 = divide(10, 0);
        String msg2 = switch (r2) {
            case Success(Integer v) -> "결과: " + v;
            case Failure(String e) -> "오류: " + e;
        };
        System.out.println(msg2);
        
        
        // map 변환
        System.out.println("\n=== map 변환 ===");
        Result<Integer, String> parsed = safeParse("123");
        Result<String, String> formatted = parsed.map(n -> "Number: " + n);
        
        String result = switch (formatted) {
            case Success(String s) -> s;
            case Failure(String e) -> e;
        };
        System.out.println(result);
    }
}
```

### 6.2 ADT (Algebraic Data Type)

```java
// Option 타입
sealed interface Option<T> permits Some, None {}

record Some<T>(T value) implements Option<T> {}
record None<T>() implements Option<T> {
    @SuppressWarnings("rawtypes")
    private static final None INSTANCE = new None();
    
    @SuppressWarnings("unchecked")
    public static <T> None<T> instance() {
        return (None<T>) INSTANCE;
    }
}

public class ADTExample {
    public static <T> Option<T> of(T value) {
        return value != null ? new Some<>(value) : None.instance();
    }
    
    public static <T> T getOrElse(Option<T> option, T defaultValue) {
        return switch (option) {
            case Some(T value) -> value;
            case None<T> n -> defaultValue;
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Option 타입 ===\n");
        
        Option<String> some = new Some<>("Hello");
        Option<String> none = None.instance();
        
        System.out.println("Some: " + getOrElse(some, "Default"));
        System.out.println("None: " + getOrElse(none, "Default"));
        
        
        // 패턴 매칭
        String result = switch (some) {
            case Some(String s) -> "값: " + s;
            case None<String> n -> "값 없음";
        };
        System.out.println(result);
    }
}
```

---

## 7. Best Practices

### 7.1 완전성 활용

```java
sealed interface Command permits Create, Update, Delete {}

record Create(String data) implements Command {}
record Update(String id, String data) implements Command {}
record Delete(String id) implements Command {}

public class ExhaustivenessExample {
    // ✅ 좋은 예 - 모든 케이스 처리
    public static String execute(Command command) {
        return switch (command) {
            case Create(String data) -> "생성: " + data;
            case Update(String id, String data) -> "수정: " + id + " → " + data;
            case Delete(String id) -> "삭제: " + id;
        };
    }
    
    // ❌ 나쁜 예 - default 사용
    public static String executeBad(Command command) {
        return switch (command) {
            case Create(String data) -> "생성: " + data;
            default -> "기타 명령";  // Update, Delete 무시
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 완전성 체크 ===\n");
        
        System.out.println("✅ 장점");
        System.out.println("- 새 타입 추가 시 컴파일 에러");
        System.out.println("- 모든 케이스 명시 강제");
        System.out.println("- 버그 조기 발견\n");
        
        Command cmd = new Create("data");
        System.out.println(execute(cmd));
    }
}
```

### 7.2 도메인 모델링

```java
// 명확한 상태 표현
sealed interface OrderState permits Pending, Confirmed, Shipped, Delivered, Cancelled {}

record Pending(java.time.Instant createdAt) implements OrderState {}
record Confirmed(java.time.Instant confirmedAt) implements OrderState {}
record Shipped(String trackingNumber, java.time.Instant shippedAt) implements OrderState {}
record Delivered(java.time.Instant deliveredAt) implements OrderState {}
record Cancelled(String reason, java.time.Instant cancelledAt) implements OrderState {}

class Order {
    private final String id;
    private OrderState state;
    
    Order(String id) {
        this.id = id;
        this.state = new Pending(java.time.Instant.now());
    }
    
    public void confirm() {
        if (state instanceof Pending) {
            state = new Confirmed(java.time.Instant.now());
        } else {
            throw new IllegalStateException("Cannot confirm from " + state);
        }
    }
    
    public void ship(String trackingNumber) {
        if (state instanceof Confirmed) {
            state = new Shipped(trackingNumber, java.time.Instant.now());
        } else {
            throw new IllegalStateException("Cannot ship from " + state);
        }
    }
    
    public String getStatus() {
        return switch (state) {
            case Pending p -> "주문 대기";
            case Confirmed c -> "주문 확인";
            case Shipped(String tracking, var time) -> "배송 중 (" + tracking + ")";
            case Delivered d -> "배송 완료";
            case Cancelled(String reason, var time) -> "취소됨: " + reason;
        };
    }
}

public class DomainModelingExample {
    public static void main(String[] args) {
        System.out.println("=== 도메인 모델링 ===\n");
        
        Order order = new Order("ORD-001");
        System.out.println("초기: " + order.getStatus());
        
        order.confirm();
        System.out.println("확인: " + order.getStatus());
        
        order.ship("TRACK-123");
        System.out.println("배송: " + order.getStatus());
    }
}
```

---

## 8. 실전 예제

### 8.1 AST (Abstract Syntax Tree)

```java
sealed interface Expr permits Const, Var, Add, Mul {}

record Const(int value) implements Expr {}
record Var(String name) implements Expr {}
record Add(Expr left, Expr right) implements Expr {}
record Mul(Expr left, Expr right) implements Expr {}

public class ASTExample {
    public static int eval(Expr expr, java.util.Map<String, Integer> env) {
        return switch (expr) {
            case Const(int value) -> value;
            case Var(String name) -> env.getOrDefault(name, 0);
            case Add(Expr left, Expr right) -> eval(left, env) + eval(right, env);
            case Mul(Expr left, Expr right) -> eval(left, env) * eval(right, env);
        };
    }
    
    public static String toString(Expr expr) {
        return switch (expr) {
            case Const(int value) -> String.valueOf(value);
            case Var(String name) -> name;
            case Add(Expr left, Expr right) -> 
                "(" + toString(left) + " + " + toString(right) + ")";
            case Mul(Expr left, Expr right) -> 
                "(" + toString(left) + " * " + toString(right) + ")";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== AST ===\n");
        
        // (x + 2) * (y + 3)
        Expr expr = new Mul(
            new Add(new Var("x"), new Const(2)),
            new Add(new Var("y"), new Const(3))
        );
        
        System.out.println("수식: " + toString(expr));
        
        java.util.Map<String, Integer> env = java.util.Map.of("x", 5, "y", 7);
        System.out.println("결과 (x=5, y=7): " + eval(expr, env));  // (5+2)*(7+3)=70
    }
}
```

### 8.2 이벤트 소싱

```java
sealed interface Event permits UserCreated, UserUpdated, UserDeleted {}

record UserCreated(String id, String name, String email, java.time.Instant timestamp) implements Event {}
record UserUpdated(String id, String field, String oldValue, String newValue, java.time.Instant timestamp) implements Event {}
record UserDeleted(String id, java.time.Instant timestamp) implements Event {}

class EventStore {
    private final java.util.List<Event> events = new java.util.ArrayList<>();
    
    public void append(Event event) {
        events.add(event);
    }
    
    public java.util.List<Event> getEvents() {
        return java.util.List.copyOf(events);
    }
    
    public void replay() {
        for (Event event : events) {
            String description = switch (event) {
                case UserCreated(String id, String name, var email, var time) ->
                    "사용자 생성: " + name + " (" + id + ")";
                case UserUpdated(String id, String field, String old, String newVal, var time) ->
                    "사용자 수정: " + id + "." + field + " " + old + " → " + newVal;
                case UserDeleted(String id, var time) ->
                    "사용자 삭제: " + id;
            };
            System.out.println(description);
        }
    }
}

public class EventSourcingExample {
    public static void main(String[] args) {
        System.out.println("=== 이벤트 소싱 ===\n");
        
        EventStore store = new EventStore();
        
        store.append(new UserCreated("U1", "Alice", "alice@example.com", 
            java.time.Instant.now()));
        store.append(new UserUpdated("U1", "email", "alice@example.com", 
            "alice@newmail.com", java.time.Instant.now()));
        store.append(new UserCreated("U2", "Bob", "bob@example.com", 
            java.time.Instant.now()));
        store.append(new UserDeleted("U1", java.time.Instant.now()));
        
        System.out.println("이벤트 재생:");
        store.replay();
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: HTTP Method

```java
// HTTP 메서드 Sealed Interface
sealed interface HttpMethod permits GET, POST, PUT, DELETE {}

record GET(String path) implements HttpMethod {}
record POST(String path, String body) implements HttpMethod {}
record PUT(String path, String body) implements HttpMethod {}
record DELETE(String path) implements HttpMethod {}

public class Problem1 {
    public static String describe(HttpMethod method) {
        // 구현: 각 메서드 설명
        return "";
    }
    
    public static void main(String[] args) {
        HttpMethod get = new GET("/users");
        System.out.println(describe(get));  // "GET /users"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static String describe(HttpMethod method) {
    return switch (method) {
        case GET(String path) -> "GET " + path;
        case POST(String path, String body) -> "POST " + path + " (body: " + body + ")";
        case PUT(String path, String body) -> "PUT " + path + " (body: " + body + ")";
        case DELETE(String path) -> "DELETE " + path;
    };
}
```
</details>

---

### 문제 2: 간단한 계산기

```java
// 수식 트리
sealed interface Expr permits Num, Add, Sub {}

record Num(int value) implements Expr {}
record Add(Expr left, Expr right) implements Expr {}
record Sub(Expr left, Expr right) implements Expr {}

public class Problem2 {
    public static int eval(Expr expr) {
        // 구현: 수식 계산
        return 0;
    }
    
    public static void main(String[] args) {
        // (5 + 3) - 2
        Expr expr = new Sub(new Add(new Num(5), new Num(3)), new Num(2));
        System.out.println(eval(expr));  // 6
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static int eval(Expr expr) {
    return switch (expr) {
        case Num(int value) -> value;
        case Add(Expr left, Expr right) -> eval(left) + eval(right);
        case Sub(Expr left, Expr right) -> eval(left) - eval(right);
    };
}
```
</details>

---

### 문제 3: Option 타입

```java
// Option 타입 구현
sealed interface Option<T> permits Some, None {}

record Some<T>(T value) implements Option<T> {}
record None<T>() implements Option<T> {}

public class Problem3 {
    public static <T> T getOrDefault(Option<T> option, T defaultValue) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        Option<String> some = new Some<>("Hello");
        Option<String> none = new None<>();
        
        System.out.println(getOrDefault(some, "Default"));  // "Hello"
        System.out.println(getOrDefault(none, "Default"));  // "Default"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static <T> T getOrDefault(Option<T> option, T defaultValue) {
    return switch (option) {
        case Some(T value) -> value;
        case None<T> n -> defaultValue;
    };
}
```
</details>

---

## 📌 핵심 정리

### 기본 정의
```java
sealed interface Shape permits Circle, Rectangle {}

record Circle(double radius) implements Shape {}
record Rectangle(double width, double height) implements Shape {}
```

### 하위 클래스
```java
sealed class Parent permits Child1, Child2, Child3 {}

final class Child1 extends Parent {}          // 더 이상 상속 불가
sealed class Child2 extends Parent {}         // 제한된 상속
non-sealed class Child3 extends Parent {}     // 자유 상속
```

### Switch Expression
```java
double area = switch (shape) {
    case Circle(double r) -> Math.PI * r * r;
    case Rectangle(double w, double h) -> w * h;
};  // default 불필요!
```

### 활용 패턴
```
✅ Result/Either 타입
✅ Option 타입
✅ ADT (대수적 데이터 타입)
✅ 이벤트 소싱
✅ AST (추상 구문 트리)
```

### Best Practices
```
✅ 완전성 체크 활용
✅ Record와 함께 사용
✅ 명확한 도메인 모델링
✅ switch 패턴 매칭
❌ default 남용
❌ 불필요한 계층
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. Switch Expression](./ModernJava-02-Switch.md)**

</div>
