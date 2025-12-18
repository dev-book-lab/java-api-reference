# Modern Java 01. Record 완전 정복

> 불변 데이터 클래스 - Record로 간결한 코드 작성하기  
> Java 14+ (Preview), Java 16+ (Standard)

---

## 📑 목차

1. [Record 소개](#1-record-소개)
2. [Record 기본 사용법](#2-record-기본-사용법)
3. [Record 생성자](#3-record-생성자)
4. [Record 메서드](#4-record-메서드)
5. [Record와 인터페이스](#5-record와-인터페이스)
6. [Record 제약사항](#6-record-제약사항)
7. [Record 활용 패턴](#7-record-활용-패턴)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Record 소개

### 1.1 왜 Record인가?

```java
// ❌ Record 이전 (Java 15 이전)
class PersonOld {
    private final String name;
    private final int age;
    
    public PersonOld(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public String getName() {
        return name;
    }
    
    public int getAge() {
        return age;
    }
    
    @Override
    public boolean equals(Object o) {
        if (this == o) return true;
        if (o == null || getClass() != o.getClass()) return false;
        PersonOld person = (PersonOld) o;
        return age == person.age && name.equals(person.name);
    }
    
    @Override
    public int hashCode() {
        return java.util.Objects.hash(name, age);
    }
    
    @Override
    public String toString() {
        return "PersonOld{name='" + name + "', age=" + age + "}";
    }
}

// ✅ Record 사용 (Java 16+)
record Person(String name, int age) {}

public class WhyRecord {
    public static void main(String[] args) {
        System.out.println("=== Record의 장점 ===\n");
        
        System.out.println("📌 간결성");
        System.out.println("50줄 → 1줄");
        System.out.println("보일러플레이트 코드 제거\n");
        
        System.out.println("📌 자동 생성");
        System.out.println("- 생성자");
        System.out.println("- getter");
        System.out.println("- equals/hashCode");
        System.out.println("- toString\n");
        
        System.out.println("📌 불변성");
        System.out.println("- final 필드");
        System.out.println("- setter 없음");
        System.out.println("- 스레드 안전\n");
        
        
        // 사용
        Person person = new Person("Alice", 25);
        System.out.println(person);
        System.out.println("이름: " + person.name());
        System.out.println("나이: " + person.age());
    }
}
```

### 1.2 Record 특징

```java
record Point(int x, int y) {}

public class RecordCharacteristics {
    public static void main(String[] args) {
        System.out.println("=== Record 특징 ===\n");
        
        System.out.println("📌 클래스");
        System.out.println("- final class");
        System.out.println("- 상속 불가");
        System.out.println("- 암묵적으로 java.lang.Record 상속\n");
        
        System.out.println("📌 필드");
        System.out.println("- private final");
        System.out.println("- 자동 생성");
        System.out.println("- 불변\n");
        
        System.out.println("📌 메서드");
        System.out.println("- 접근자 (getter) 자동");
        System.out.println("- equals/hashCode 자동");
        System.out.println("- toString 자동\n");
        
        
        Point p1 = new Point(1, 2);
        Point p2 = new Point(1, 2);
        Point p3 = new Point(3, 4);
        
        System.out.println("=== 동작 확인 ===");
        System.out.println("p1: " + p1);
        System.out.println("p1.x(): " + p1.x());
        System.out.println("p1 equals p2: " + p1.equals(p2));
        System.out.println("p1 equals p3: " + p1.equals(p3));
    }
}
```

---

## 2. Record 기본 사용법

### 2.1 간단한 Record

```java
// 기본 Record
record User(String username, String email) {}

record Product(String name, double price, int stock) {}

record Coordinate(double latitude, double longitude) {}

public class BasicRecord {
    public static void main(String[] args) {
        System.out.println("=== 기본 Record ===\n");
        
        // User
        User user = new User("alice", "alice@example.com");
        System.out.println("사용자: " + user);
        System.out.println("이름: " + user.username());
        System.out.println("이메일: " + user.email());
        
        
        // Product
        System.out.println("\n=== 상품 ===");
        Product product = new Product("노트북", 1_500_000, 10);
        System.out.println(product);
        System.out.printf("가격: %,d원%n", (int)product.price());
        System.out.println("재고: " + product.stock());
        
        
        // Coordinate
        System.out.println("\n=== 좌표 ===");
        Coordinate seoul = new Coordinate(37.5665, 126.9780);
        System.out.println(seoul);
    }
}
```

### 2.2 Compact Constructor

```java
// Compact Constructor (유효성 검증)
record Person(String name, int age) {
    // Compact Constructor
    public Person {
        if (name == null || name.isBlank()) {
            throw new IllegalArgumentException("이름은 필수입니다");
        }
        if (age < 0 || age > 150) {
            throw new IllegalArgumentException("나이가 유효하지 않습니다");
        }
        // 정규화
        name = name.trim();
    }
}

record Email(String address) {
    public Email {
        if (address == null || !address.contains("@")) {
            throw new IllegalArgumentException("유효하지 않은 이메일");
        }
        address = address.toLowerCase();
    }
}

public class CompactConstructor {
    public static void main(String[] args) {
        System.out.println("=== Compact Constructor ===\n");
        
        // 정상 생성
        Person person = new Person("  Alice  ", 25);
        System.out.println("이름: '" + person.name() + "'");  // trim됨
        
        
        // 유효성 검증
        System.out.println("\n=== 유효성 검증 ===");
        try {
            Person invalid = new Person("", 25);
        } catch (IllegalArgumentException e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        try {
            Person invalid = new Person("Bob", -5);
        } catch (IllegalArgumentException e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        
        // Email
        System.out.println("\n=== Email ===");
        Email email = new Email("Alice@Example.Com");
        System.out.println("정규화: " + email.address());  // 소문자
    }
}
```

---

## 3. Record 생성자

### 3.1 Canonical Constructor

```java
record Point(int x, int y) {
    // Canonical Constructor (명시적)
    public Point(int x, int y) {
        this.x = x;
        this.y = y;
        System.out.println("Point 생성: (" + x + ", " + y + ")");
    }
}

record Range(int start, int end) {
    public Range(int start, int end) {
        if (start > end) {
            throw new IllegalArgumentException("start > end");
        }
        this.start = start;
        this.end = end;
    }
}

public class CanonicalConstructor {
    public static void main(String[] args) {
        System.out.println("=== Canonical Constructor ===\n");
        
        Point p = new Point(10, 20);
        
        
        System.out.println("\n=== Range 검증 ===");
        Range valid = new Range(1, 10);
        System.out.println("유효: " + valid);
        
        try {
            Range invalid = new Range(10, 1);
        } catch (IllegalArgumentException e) {
            System.out.println("오류: " + e.getMessage());
        }
    }
}
```

### 3.2 추가 생성자

```java
record Person(String name, int age, String email) {
    // Compact Constructor
    public Person {
        if (name == null) throw new IllegalArgumentException("name null");
        if (age < 0) throw new IllegalArgumentException("age < 0");
    }
    
    // 추가 생성자 - 이메일 없이
    public Person(String name, int age) {
        this(name, age, "");
    }
    
    // 추가 생성자 - 나이 기본값
    public Person(String name) {
        this(name, 0, "");
    }
}

record Product(String name, double price, int stock) {
    // 추가 생성자들
    public Product(String name, double price) {
        this(name, price, 0);
    }
    
    public Product(String name) {
        this(name, 0.0, 0);
    }
}

public class AdditionalConstructors {
    public static void main(String[] args) {
        System.out.println("=== 추가 생성자 ===\n");
        
        Person p1 = new Person("Alice", 25, "alice@example.com");
        Person p2 = new Person("Bob", 30);
        Person p3 = new Person("Charlie");
        
        System.out.println(p1);
        System.out.println(p2);
        System.out.println(p3);
        
        
        System.out.println("\n=== Product ===");
        Product prod1 = new Product("노트북", 1_000_000, 10);
        Product prod2 = new Product("마우스", 30_000);
        Product prod3 = new Product("키보드");
        
        System.out.println(prod1);
        System.out.println(prod2);
        System.out.println(prod3);
    }
}
```

---

## 4. Record 메서드

### 4.1 인스턴스 메서드

```java
record Rectangle(double width, double height) {
    // 인스턴스 메서드
    public double area() {
        return width * height;
    }
    
    public double perimeter() {
        return 2 * (width + height);
    }
    
    public boolean isSquare() {
        return width == height;
    }
    
    public Rectangle scale(double factor) {
        return new Rectangle(width * factor, height * factor);
    }
}

record Money(double amount, String currency) {
    public Money add(Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("통화가 다릅니다");
        }
        return new Money(amount + other.amount, currency);
    }
    
    public Money multiply(double factor) {
        return new Money(amount * factor, currency);
    }
    
    public String format() {
        return String.format("%.2f %s", amount, currency);
    }
}

public class InstanceMethods {
    public static void main(String[] args) {
        System.out.println("=== 인스턴스 메서드 ===\n");
        
        // Rectangle
        Rectangle rect = new Rectangle(10, 5);
        System.out.println("면적: " + rect.area());
        System.out.println("둘레: " + rect.perimeter());
        System.out.println("정사각형? " + rect.isSquare());
        
        Rectangle scaled = rect.scale(2);
        System.out.println("2배 확대: " + scaled);
        
        
        // Money
        System.out.println("\n=== Money ===");
        Money m1 = new Money(1000, "USD");
        Money m2 = new Money(500, "USD");
        
        Money sum = m1.add(m2);
        System.out.println("합계: " + sum.format());
        
        Money doubled = m1.multiply(2);
        System.out.println("2배: " + doubled.format());
    }
}
```

### 4.2 정적 메서드

```java
record Point(int x, int y) {
    // 정적 팩토리 메서드
    public static Point origin() {
        return new Point(0, 0);
    }
    
    public static Point of(int x, int y) {
        return new Point(x, y);
    }
    
    // 정적 유틸리티 메서드
    public static double distance(Point p1, Point p2) {
        int dx = p1.x - p2.x;
        int dy = p1.y - p2.y;
        return Math.sqrt(dx * dx + dy * dy);
    }
}

record Temperature(double value, char unit) {
    public static Temperature celsius(double value) {
        return new Temperature(value, 'C');
    }
    
    public static Temperature fahrenheit(double value) {
        return new Temperature(value, 'F');
    }
    
    public Temperature toCelsius() {
        if (unit == 'C') return this;
        return celsius((value - 32) * 5 / 9);
    }
    
    public Temperature toFahrenheit() {
        if (unit == 'F') return this;
        return fahrenheit(value * 9 / 5 + 32);
    }
}

public class StaticMethods {
    public static void main(String[] args) {
        System.out.println("=== 정적 메서드 ===\n");
        
        // 팩토리 메서드
        Point origin = Point.origin();
        Point p1 = Point.of(3, 4);
        
        System.out.println("원점: " + origin);
        System.out.println("점: " + p1);
        System.out.println("거리: " + Point.distance(origin, p1));
        
        
        // Temperature
        System.out.println("\n=== Temperature ===");
        Temperature c = Temperature.celsius(100);
        Temperature f = c.toFahrenheit();
        
        System.out.println("섭씨: " + c);
        System.out.println("화씨: " + f);
    }
}
```

---

## 5. Record와 인터페이스

### 5.1 인터페이스 구현

```java
interface Drawable {
    void draw();
}

record Circle(double radius) implements Drawable {
    @Override
    public void draw() {
        System.out.println("원 그리기 (반지름: " + radius + ")");
    }
    
    public double area() {
        return Math.PI * radius * radius;
    }
}

interface Comparable<T> {
    int compareTo(T other);
}

record Student(String name, int score) implements Comparable<Student> {
    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.score, other.score);
    }
}

public class RecordWithInterface {
    public static void main(String[] args) {
        System.out.println("=== Record 인터페이스 ===\n");
        
        // Drawable
        Circle circle = new Circle(5);
        circle.draw();
        System.out.println("면적: " + circle.area());
        
        
        // Comparable
        System.out.println("\n=== Student 비교 ===");
        Student s1 = new Student("Alice", 90);
        Student s2 = new Student("Bob", 85);
        
        int result = s1.compareTo(s2);
        System.out.println(s1 + " vs " + s2);
        System.out.println("결과: " + (result > 0 ? "Alice 점수 높음" : "Bob 점수 높음"));
        
        
        // 정렬
        java.util.List<Student> students = new java.util.ArrayList<>();
        students.add(new Student("Charlie", 75));
        students.add(s1);
        students.add(s2);
        
        java.util.Collections.sort(students);
        System.out.println("\n정렬됨:");
        students.forEach(System.out::println);
    }
}
```

### 5.2 여러 인터페이스

```java
interface Identifiable {
    long getId();
}

interface Timestamped {
    java.time.Instant getCreatedAt();
}

record Post(long id, String title, String content, java.time.Instant createdAt) 
    implements Identifiable, Timestamped {
    
    @Override
    public long getId() {
        return id;
    }
    
    @Override
    public java.time.Instant getCreatedAt() {
        return createdAt;
    }
    
    public String summary() {
        return title + " (" + content.substring(0, Math.min(50, content.length())) + "...)";
    }
}

public class MultipleInterfaces {
    public static void main(String[] args) {
        System.out.println("=== 여러 인터페이스 ===\n");
        
        Post post = new Post(
            1L,
            "Record 소개",
            "Record는 Java 16에서 정식으로 도입된 불변 데이터 클래스입니다.",
            java.time.Instant.now()
        );
        
        System.out.println("ID: " + post.getId());
        System.out.println("제목: " + post.title());
        System.out.println("요약: " + post.summary());
        System.out.println("작성: " + post.getCreatedAt());
    }
}
```

---

## 6. Record 제약사항

### 6.1 불가능한 것들

```java
public class RecordConstraints {
    public static void main(String[] args) {
        System.out.println("=== Record 제약사항 ===\n");
        
        System.out.println("❌ 불가능");
        System.out.println("1. 다른 클래스 상속");
        System.out.println("   - java.lang.Record 상속 고정");
        System.out.println("   - extends 사용 불가\n");
        
        System.out.println("2. 인스턴스 필드 추가");
        System.out.println("   - 컴포넌트 외 필드 선언 불가");
        System.out.println("   - 모든 필드는 final\n");
        
        System.out.println("3. abstract 선언");
        System.out.println("   - final class 고정");
        System.out.println("   - 상속 불가\n");
        
        System.out.println("4. native 메서드");
        System.out.println("   - JNI 메서드 선언 불가\n");
        
        
        System.out.println("✅ 가능");
        System.out.println("1. 인터페이스 구현 (여러 개)");
        System.out.println("2. 정적 필드/메서드");
        System.out.println("3. 인스턴스 메서드");
        System.out.println("4. 중첩 클래스/Record");
        System.out.println("5. Generic 사용");
    }
}
```

### 6.2 우회 방법

```java
// 가변 필드가 필요한 경우 → 일반 클래스 사용
class MutablePoint {
    private int x, y;
    
    public void setX(int x) { this.x = x; }
    public void setY(int y) { this.y = y; }
}

// 상속이 필요한 경우 → 일반 클래스 사용
class Shape {
    protected String color;
}

class Circle extends Shape {
    private double radius;
}

// Record는 불변 데이터에만 사용
record ImmutablePoint(int x, int y) {}

public class RecordWorkarounds {
    public static void main(String[] args) {
        System.out.println("=== Record 우회 ===\n");
        
        System.out.println("📌 언제 Record를 사용하나?");
        System.out.println("✅ 불변 데이터");
        System.out.println("✅ DTO (Data Transfer Object)");
        System.out.println("✅ Value Object");
        System.out.println("✅ Key (Map)");
        System.out.println("✅ 튜플\n");
        
        System.out.println("📌 언제 일반 클래스를 사용하나?");
        System.out.println("✅ 가변 상태");
        System.out.println("✅ 상속 필요");
        System.out.println("✅ 복잡한 로직");
        System.out.println("✅ 초기화 후 변경");
    }
}
```

---

## 7. Record 활용 패턴

### 7.1 DTO (Data Transfer Object)

```java
// API 요청/응답
record LoginRequest(String username, String password) {
    public LoginRequest {
        if (username == null || username.isBlank()) {
            throw new IllegalArgumentException("username 필수");
        }
        if (password == null || password.length() < 8) {
            throw new IllegalArgumentException("password는 8자 이상");
        }
    }
}

record LoginResponse(boolean success, String token, String message) {
    public static LoginResponse success(String token) {
        return new LoginResponse(true, token, "로그인 성공");
    }
    
    public static LoginResponse failure(String message) {
        return new LoginResponse(false, null, message);
    }
}

record UserDto(long id, String username, String email, java.time.LocalDateTime createdAt) {}

public class DTOPattern {
    public static void main(String[] args) {
        System.out.println("=== DTO 패턴 ===\n");
        
        // 로그인 요청
        LoginRequest request = new LoginRequest("alice", "password123");
        System.out.println("요청: " + request);
        
        // 로그인 응답
        LoginResponse response = LoginResponse.success("abc123token");
        System.out.println("응답: " + response);
        
        LoginResponse failure = LoginResponse.failure("비밀번호 오류");
        System.out.println("실패: " + failure);
        
        
        // User DTO
        System.out.println("\n=== User DTO ===");
        UserDto user = new UserDto(1L, "alice", "alice@example.com", 
            java.time.LocalDateTime.now());
        System.out.println(user);
    }
}
```

### 7.2 Value Object

```java
record Money(double amount, String currency) {
    public Money {
        if (amount < 0) {
            throw new IllegalArgumentException("금액은 음수 불가");
        }
        if (currency == null || currency.length() != 3) {
            throw new IllegalArgumentException("통화 코드는 3자");
        }
    }
    
    public Money add(Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("통화 불일치");
        }
        return new Money(amount + other.amount, currency);
    }
    
    public Money subtract(Money other) {
        if (!currency.equals(other.currency)) {
            throw new IllegalArgumentException("통화 불일치");
        }
        return new Money(amount - other.amount, currency);
    }
}

record Email(String value) {
    public Email {
        if (value == null || !value.matches("^[A-Za-z0-9+_.-]+@(.+)$")) {
            throw new IllegalArgumentException("유효하지 않은 이메일");
        }
        value = value.toLowerCase();
    }
    
    public String domain() {
        return value.substring(value.indexOf('@') + 1);
    }
}

public class ValueObjectPattern {
    public static void main(String[] args) {
        System.out.println("=== Value Object ===\n");
        
        // Money
        Money m1 = new Money(1000, "USD");
        Money m2 = new Money(500, "USD");
        Money sum = m1.add(m2);
        
        System.out.println("합계: " + sum);
        
        
        // Email
        System.out.println("\n=== Email ===");
        Email email = new Email("Alice@Example.Com");
        System.out.println("이메일: " + email.value());
        System.out.println("도메인: " + email.domain());
    }
}
```

### 7.3 튜플

```java
record Pair<T, U>(T first, U second) {
    public static <T, U> Pair<T, U> of(T first, U second) {
        return new Pair<>(first, second);
    }
}

record Triple<A, B, C>(A first, B second, C third) {
    public static <A, B, C> Triple<A, B, C> of(A first, B second, C third) {
        return new Triple<>(first, second, third);
    }
}

public class TuplePattern {
    // 여러 값을 반환
    public static Pair<Integer, Integer> divMod(int a, int b) {
        return Pair.of(a / b, a % b);
    }
    
    public static Triple<String, Integer, String> getUserInfo(long id) {
        // DB 조회 시뮬레이션
        return Triple.of("Alice", 25, "alice@example.com");
    }
    
    public static void main(String[] args) {
        System.out.println("=== 튜플 패턴 ===\n");
        
        // 몫과 나머지
        Pair<Integer, Integer> result = divMod(17, 5);
        System.out.println("17 / 5 = " + result.first() + " ... " + result.second());
        
        
        // 여러 값 반환
        System.out.println("\n=== 사용자 정보 ===");
        Triple<String, Integer, String> user = getUserInfo(1L);
        System.out.println("이름: " + user.first());
        System.out.println("나이: " + user.second());
        System.out.println("이메일: " + user.third());
    }
}
```

---

## 8. 실전 예제

### 8.1 불변 설정

```java
record DatabaseConfig(
    String host,
    int port,
    String database,
    String username,
    String password,
    int maxConnections
) {
    public DatabaseConfig {
        if (host == null || host.isBlank()) {
            throw new IllegalArgumentException("host 필수");
        }
        if (port < 1 || port > 65535) {
            throw new IllegalArgumentException("포트 범위: 1-65535");
        }
        if (maxConnections < 1) {
            throw new IllegalArgumentException("최소 연결 수: 1");
        }
    }
    
    public static DatabaseConfig local() {
        return new DatabaseConfig(
            "localhost",
            5432,
            "mydb",
            "root",
            "",
            10
        );
    }
    
    public static DatabaseConfig production(String host, String database) {
        return new DatabaseConfig(
            host,
            5432,
            database,
            System.getenv("DB_USER"),
            System.getenv("DB_PASSWORD"),
            100
        );
    }
    
    public String connectionString() {
        return String.format("jdbc:postgresql://%s:%d/%s", host, port, database);
    }
}

public class ConfigExample {
    public static void main(String[] args) {
        System.out.println("=== 설정 관리 ===\n");
        
        DatabaseConfig localConfig = DatabaseConfig.local();
        System.out.println("로컬: " + localConfig.connectionString());
        System.out.println("최대 연결: " + localConfig.maxConnections());
    }
}
```

### 8.2 이벤트 소싱

```java
import java.time.Instant;
import java.util.*;

sealed interface Event permits OrderCreated, OrderShipped, OrderDelivered, OrderCancelled {}

record OrderCreated(String orderId, String customerId, List<String> items, Instant timestamp) implements Event {}
record OrderShipped(String orderId, String trackingNumber, Instant timestamp) implements Event {}
record OrderDelivered(String orderId, Instant timestamp) implements Event {}
record OrderCancelled(String orderId, String reason, Instant timestamp) implements Event {}

class EventStore {
    private List<Event> events = new ArrayList<>();
    
    public void save(Event event) {
        events.add(event);
    }
    
    public List<Event> getEvents() {
        return List.copyOf(events);
    }
}

public class EventSourcingExample {
    public static void main(String[] args) {
        System.out.println("=== 이벤트 소싱 ===\n");
        
        EventStore store = new EventStore();
        
        // 주문 생성
        OrderCreated created = new OrderCreated(
            "ORD-001",
            "CUST-001",
            List.of("상품A", "상품B"),
            Instant.now()
        );
        store.save(created);
        System.out.println("주문 생성: " + created);
        
        // 배송 시작
        OrderShipped shipped = new OrderShipped(
            "ORD-001",
            "TRACK-123",
            Instant.now()
        );
        store.save(shipped);
        System.out.println("배송 시작: " + shipped);
        
        
        // 이벤트 조회
        System.out.println("\n=== 이벤트 히스토리 ===");
        for (Event event : store.getEvents()) {
            System.out.println(event);
        }
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 좌표계산

```java
// Point Record 완성
record Point(int x, int y) {
    // 두 점 사이 거리 계산
    public double distanceTo(Point other) {
        // 구현
        return 0;
    }
    
    // 원점으로부터 거리
    public double distanceFromOrigin() {
        // 구현
        return 0;
    }
}

public class Problem1 {
    public static void main(String[] args) {
        Point p1 = new Point(0, 0);
        Point p2 = new Point(3, 4);
        
        System.out.println("거리: " + p1.distanceTo(p2));  // 5.0
        System.out.println("원점 거리: " + p2.distanceFromOrigin());  // 5.0
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public double distanceTo(Point other) {
    int dx = this.x - other.x;
    int dy = this.y - other.y;
    return Math.sqrt(dx * dx + dy * dy);
}

public double distanceFromOrigin() {
    return Math.sqrt(x * x + y * y);
}
```
</details>

---

### 문제 2: 범위 검사

```java
// Range Record 완성
record Range(int start, int end) {
    // Compact Constructor로 검증
    public Range {
        // 구현
    }
    
    // 값이 범위 안에 있는지
    public boolean contains(int value) {
        // 구현
        return false;
    }
    
    // 범위 길이
    public int length() {
        // 구현
        return 0;
    }
}

public class Problem2 {
    public static void main(String[] args) {
        Range range = new Range(1, 10);
        
        System.out.println(range.contains(5));   // true
        System.out.println(range.contains(15));  // false
        System.out.println(range.length());      // 9
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
record Range(int start, int end) {
    public Range {
        if (start > end) {
            throw new IllegalArgumentException("start > end");
        }
    }
    
    public boolean contains(int value) {
        return value >= start && value <= end;
    }
    
    public int length() {
        return end - start + 1;
    }
}
```
</details>

---

### 문제 3: 불변 리스트

```java
// Task Record 완성
record Task(String title, List<String> tags) {
    // Compact Constructor로 방어적 복사
    public Task {
        // 구현
    }
    
    // 태그 추가한 새 Task 반환
    public Task addTag(String tag) {
        // 구현
        return null;
    }
}

public class Problem3 {
    public static void main(String[] args) {
        Task task = new Task("Study", List.of("Java", "Record"));
        Task updated = task.addTag("Modern");
        
        System.out.println("원본: " + task.tags());      // [Java, Record]
        System.out.println("수정: " + updated.tags());   // [Java, Record, Modern]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
record Task(String title, List<String> tags) {
    public Task {
        tags = List.copyOf(tags);  // 방어적 복사
    }
    
    public Task addTag(String tag) {
        List<String> newTags = new ArrayList<>(tags);
        newTags.add(tag);
        return new Task(title, newTags);
    }
}
```
</details>

---

## 📌 핵심 정리

### 기본 정의
```java
record Person(String name, int age) {}
```

### Compact Constructor
```java
record Person(String name, int age) {
    public Person {
        if (age < 0) throw new IllegalArgumentException();
        name = name.trim();
    }
}
```

### 메서드 추가
```java
record Point(int x, int y) {
    // 인스턴스 메서드
    public double distanceFromOrigin() {
        return Math.sqrt(x * x + y * y);
    }
    
    // 정적 메서드
    public static Point origin() {
        return new Point(0, 0);
    }
}
```

### 인터페이스 구현
```java
record Circle(double radius) implements Drawable {
    @Override
    public void draw() { }
}
```

### 활용 패턴
```
✅ DTO (Data Transfer Object)
✅ Value Object
✅ 튜플
✅ 불변 설정
✅ 이벤트
```

### 제약사항
```
❌ 다른 클래스 상속 불가
❌ 인스턴스 필드 추가 불가
❌ abstract 불가
✅ 인터페이스 구현
✅ 정적 필드/메서드
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Switch Expression →](./ModernJava-02-Switch.md)**

</div>
