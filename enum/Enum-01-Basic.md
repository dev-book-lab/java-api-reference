# Enum 01. Enum 기본 완전 정복

> 타입 안전한 상수 - Enum의 기초부터 활용까지  
> Java Enum

---

## 📑 목차

1. [Enum 소개](#1-enum-소개)
2. [Enum 기본 사용법](#2-enum-기본-사용법)
3. [Enum 필드와 메서드](#3-enum-필드와-메서드)
4. [Enum 생성자](#4-enum-생성자)
5. [Enum 내장 메서드](#5-enum-내장-메서드)
6. [EnumSet과 EnumMap](#6-enumset과-enummap)
7. [Enum 비교](#7-enum-비교)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Enum 소개

### 1.1 왜 Enum인가?

```java
public class WhyEnum {
    // ❌ 상수 사용 (Enum 이전)
    public static final int MONDAY = 1;
    public static final int TUESDAY = 2;
    public static final int WEDNESDAY = 3;
    
    // 문제점
    public static void badExample() {
        int day = 100;  // 유효하지 않은 값!
        day = MONDAY + TUESDAY;  // 의미 없는 연산
        
        // 타입 안전성 없음
        processDay(999);  // 컴파일 OK, 런타임 문제
    }
    
    static void processDay(int day) {
        // 검증 필요
        if (day < 1 || day > 7) {
            System.out.println("잘못된 요일");
        }
    }
    
    
    // ✅ Enum 사용
    enum Day {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
    }
    
    public static void goodExample() {
        Day day = Day.MONDAY;
        // day = 100;  // 컴파일 에러!
        // day = Day.MONDAY + Day.TUESDAY;  // 컴파일 에러!
        
        // 타입 안전
        processDayEnum(Day.MONDAY);
        // processDayEnum(999);  // 컴파일 에러!
    }
    
    static void processDayEnum(Day day) {
        // 검증 불필요 - 항상 유효한 값
        System.out.println("요일: " + day);
    }
    
    
    public static void main(String[] args) {
        System.out.println("=== Enum의 장점 ===\n");
        
        System.out.println("✅ 타입 안전성");
        System.out.println("   - 컴파일 타임 체크");
        System.out.println("   - 잘못된 값 사용 불가\n");
        
        System.out.println("✅ 명확한 의미");
        System.out.println("   - 코드 가독성 향상");
        System.out.println("   - 자기 문서화\n");
        
        System.out.println("✅ 네임스페이스");
        System.out.println("   - 상수 그룹화");
        System.out.println("   - 충돌 방지\n");
        
        System.out.println("✅ 풍부한 기능");
        System.out.println("   - 메서드, 필드 추가 가능");
        System.out.println("   - 인터페이스 구현 가능");
    }
}
```

### 1.2 Enum 특징

```java
enum Color {
    RED, GREEN, BLUE
}

public class EnumCharacteristics {
    public static void main(String[] args) {
        System.out.println("=== Enum 특징 ===\n");
        
        System.out.println("📌 클래스");
        System.out.println("- Enum은 특별한 클래스");
        System.out.println("- java.lang.Enum 상속");
        System.out.println("- final class (상속 불가)\n");
        
        System.out.println("📌 싱글톤");
        System.out.println("- 각 상수는 인스턴스 1개");
        System.out.println("- JVM이 보장");
        System.out.println("- Serializable 구현\n");
        
        System.out.println("📌 안전한 비교");
        System.out.println("- == 사용 가능");
        System.out.println("- null 안전");
        
        
        Color red1 = Color.RED;
        Color red2 = Color.RED;
        
        System.out.println("\n=== 동일성 ===");
        System.out.println("red1 == red2: " + (red1 == red2));
        System.out.println("같은 인스턴스!");
    }
}
```

---

## 2. Enum 기본 사용법

### 2.1 간단한 Enum

```java
// 기본 Enum
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

enum Season {
    SPRING, SUMMER, FALL, WINTER
}

enum Status {
    PENDING, APPROVED, REJECTED
}

public class BasicEnumUsage {
    public static void main(String[] args) {
        System.out.println("=== 기본 Enum 사용 ===\n");
        
        // 선언
        Day today = Day.MONDAY;
        Season season = Season.SPRING;
        Status status = Status.PENDING;
        
        System.out.println("오늘: " + today);
        System.out.println("계절: " + season);
        System.out.println("상태: " + status);
        
        
        // switch
        System.out.println("\n=== switch 문 ===");
        switch (today) {
            case MONDAY:
                System.out.println("월요일입니다");
                break;
            case FRIDAY:
                System.out.println("불금!");
                break;
            case SATURDAY:
            case SUNDAY:
                System.out.println("주말!");
                break;
            default:
                System.out.println("평일");
        }
        
        
        // null 체크
        Day day = null;
        if (day == Day.MONDAY) {  // NullPointerException!
            System.out.println("월요일");
        }
    }
}
```

### 2.2 Enum 순회

```java
enum Color {
    RED, GREEN, BLUE, YELLOW, BLACK, WHITE
}

public class EnumIteration {
    public static void main(String[] args) {
        System.out.println("=== Enum 순회 ===\n");
        
        // values() - 모든 상수 배열
        System.out.println("모든 색상:");
        for (Color color : Color.values()) {
            System.out.println(color);
        }
        
        
        // 개수
        System.out.println("\n색상 개수: " + Color.values().length);
        
        
        // 인덱스 접근
        System.out.println("\n=== 인덱스로 접근 ===");
        Color[] colors = Color.values();
        System.out.println("첫 번째: " + colors[0]);
        System.out.println("두 번째: " + colors[1]);
    }
}
```

---

## 3. Enum 필드와 메서드

### 3.1 필드 추가

```java
enum Planet {
    MERCURY(3.303e+23, 2.4397e6),
    VENUS(4.869e+24, 6.0518e6),
    EARTH(5.976e+24, 6.37814e6),
    MARS(6.421e+23, 3.3972e6);
    
    private final double mass;   // kg
    private final double radius; // m
    
    Planet(double mass, double radius) {
        this.mass = mass;
        this.radius = radius;
    }
    
    public double getMass() {
        return mass;
    }
    
    public double getRadius() {
        return radius;
    }
    
    // 표면 중력
    public double surfaceGravity() {
        final double G = 6.67300E-11;
        return G * mass / (radius * radius);
    }
}

public class EnumWithFields {
    public static void main(String[] args) {
        System.out.println("=== Enum 필드 ===\n");
        
        for (Planet planet : Planet.values()) {
            System.out.printf("%s: 질량=%.3e kg, 중력=%.2f m/s²%n",
                planet, planet.getMass(), planet.surfaceGravity());
        }
    }
}
```

### 3.2 메서드 추가

```java
enum Operation {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    TIMES("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    };
    
    private final String symbol;
    
    Operation(String symbol) {
        this.symbol = symbol;
    }
    
    public String getSymbol() {
        return symbol;
    }
    
    // 추상 메서드
    public abstract double apply(double x, double y);
}

public class EnumWithMethods {
    public static void main(String[] args) {
        System.out.println("=== Enum 메서드 ===\n");
        
        double x = 10;
        double y = 5;
        
        for (Operation op : Operation.values()) {
            System.out.printf("%f %s %f = %f%n",
                x, op.getSymbol(), y, op.apply(x, y));
        }
    }
}
```

---

## 4. Enum 생성자

### 4.1 생성자 기본

```java
enum Size {
    SMALL(90),
    MEDIUM(100),
    LARGE(110),
    XLARGE(120);
    
    private final int value;
    
    // private 생성자
    Size(int value) {
        this.value = value;
        System.out.println("생성: " + this.name());
    }
    
    public int getValue() {
        return value;
    }
}

public class EnumConstructor {
    public static void main(String[] args) {
        System.out.println("=== Enum 생성자 ===\n");
        
        System.out.println("생성자 특징:");
        System.out.println("- private만 가능");
        System.out.println("- 클래스 로드 시 한 번만 실행");
        System.out.println("- 각 상수마다 호출\n");
        
        // 처음 접근 시 모든 상수 생성
        Size size = Size.MEDIUM;
        System.out.println("\n선택한 사이즈: " + size);
        System.out.println("값: " + size.getValue());
    }
}
```

### 4.2 복잡한 생성자

```java
enum PaymentMethod {
    CREDIT_CARD("신용카드", 0.03, true),
    DEBIT_CARD("체크카드", 0.01, true),
    CASH("현금", 0.0, false),
    BANK_TRANSFER("계좌이체", 0.0, false);
    
    private final String korName;
    private final double fee;
    private final boolean needsValidation;
    
    PaymentMethod(String korName, double fee, boolean needsValidation) {
        this.korName = korName;
        this.fee = fee;
        this.needsValidation = needsValidation;
    }
    
    public String getKorName() {
        return korName;
    }
    
    public double calculateFee(double amount) {
        return amount * fee;
    }
    
    public boolean needsValidation() {
        return needsValidation;
    }
}

public class ComplexEnumConstructor {
    public static void main(String[] args) {
        System.out.println("=== 결제 수단 ===\n");
        
        double amount = 10000;
        
        for (PaymentMethod method : PaymentMethod.values()) {
            System.out.printf("%s: 수수료 %.0f원%s%n",
                method.getKorName(),
                method.calculateFee(amount),
                method.needsValidation() ? " (검증 필요)" : "");
        }
    }
}
```

---

## 5. Enum 내장 메서드

### 5.1 기본 메서드

```java
enum Direction {
    NORTH, SOUTH, EAST, WEST
}

public class EnumBuiltinMethods {
    public static void main(String[] args) {
        System.out.println("=== Enum 내장 메서드 ===\n");
        
        Direction dir = Direction.NORTH;
        
        // name() - 상수 이름
        System.out.println("name(): " + dir.name());
        
        // ordinal() - 순서 (0부터)
        System.out.println("ordinal(): " + dir.ordinal());
        
        // toString() - 기본은 name()과 동일
        System.out.println("toString(): " + dir.toString());
        
        
        // values() - 모든 상수
        System.out.println("\n=== values() ===");
        for (Direction d : Direction.values()) {
            System.out.println(d.ordinal() + ": " + d.name());
        }
        
        
        // valueOf() - 문자열로 변환
        System.out.println("\n=== valueOf() ===");
        Direction east = Direction.valueOf("EAST");
        System.out.println("valueOf('EAST'): " + east);
        
        try {
            Direction invalid = Direction.valueOf("INVALID");
        } catch (IllegalArgumentException e) {
            System.out.println("예외: 존재하지 않는 상수");
        }
        
        
        // compareTo() - 순서 비교
        System.out.println("\n=== compareTo() ===");
        System.out.println("NORTH vs SOUTH: " + 
            Direction.NORTH.compareTo(Direction.SOUTH));
        System.out.println("SOUTH vs NORTH: " + 
            Direction.SOUTH.compareTo(Direction.NORTH));
    }
}
```

### 5.2 Enum 변환

```java
enum Status {
    PENDING("대기중"),
    PROCESSING("처리중"),
    COMPLETED("완료"),
    FAILED("실패");
    
    private final String korName;
    
    Status(String korName) {
        this.korName = korName;
    }
    
    public String getKorName() {
        return korName;
    }
    
    // 한글명으로 찾기
    public static Status fromKorName(String korName) {
        for (Status status : values()) {
            if (status.korName.equals(korName)) {
                return status;
            }
        }
        throw new IllegalArgumentException("Unknown: " + korName);
    }
}

public class EnumConversion {
    public static void main(String[] args) {
        System.out.println("=== Enum 변환 ===\n");
        
        // String → Enum
        Status status1 = Status.valueOf("PENDING");
        System.out.println("영문: " + status1);
        
        Status status2 = Status.fromKorName("완료");
        System.out.println("한글: " + status2);
        
        
        // Enum → String
        String name = Status.COMPLETED.name();
        String korean = Status.COMPLETED.getKorName();
        System.out.println("\n" + name + " = " + korean);
    }
}
```

---

## 6. EnumSet과 EnumMap

### 6.1 EnumSet

```java
import java.util.*;

enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

public class EnumSetExample {
    public static void main(String[] args) {
        System.out.println("=== EnumSet ===\n");
        
        // 생성
        EnumSet<Day> weekdays = EnumSet.range(Day.MONDAY, Day.FRIDAY);
        EnumSet<Day> weekend = EnumSet.of(Day.SATURDAY, Day.SUNDAY);
        EnumSet<Day> all = EnumSet.allOf(Day.class);
        EnumSet<Day> none = EnumSet.noneOf(Day.class);
        
        System.out.println("평일: " + weekdays);
        System.out.println("주말: " + weekend);
        System.out.println("전체: " + all);
        
        
        // 연산
        System.out.println("\n=== 집합 연산 ===");
        EnumSet<Day> union = EnumSet.copyOf(weekdays);
        union.addAll(weekend);
        System.out.println("합집합: " + union);
        
        EnumSet<Day> complement = EnumSet.complementOf(weekdays);
        System.out.println("여집합: " + complement);
        
        
        // 성능
        System.out.println("\n=== 특징 ===");
        System.out.println("- 비트 벡터로 구현");
        System.out.println("- 매우 빠른 연산");
        System.out.println("- 메모리 효율적");
    }
}
```

### 6.2 EnumMap

```java
import java.util.*;

enum Fruit {
    APPLE, BANANA, ORANGE, GRAPE
}

public class EnumMapExample {
    public static void main(String[] args) {
        System.out.println("=== EnumMap ===\n");
        
        // 생성
        EnumMap<Fruit, Integer> prices = new EnumMap<>(Fruit.class);
        
        // 추가
        prices.put(Fruit.APPLE, 1000);
        prices.put(Fruit.BANANA, 1500);
        prices.put(Fruit.ORANGE, 2000);
        prices.put(Fruit.GRAPE, 3000);
        
        // 조회
        System.out.println("사과 가격: " + prices.get(Fruit.APPLE));
        
        
        // 순회
        System.out.println("\n=== 가격표 ===");
        for (Map.Entry<Fruit, Integer> entry : prices.entrySet()) {
            System.out.printf("%s: %d원%n", 
                entry.getKey(), entry.getValue());
        }
        
        
        // 특징
        System.out.println("\n=== 특징 ===");
        System.out.println("- 배열 기반 구현");
        System.out.println("- 순서 보장 (선언 순서)");
        System.out.println("- 매우 빠름");
        System.out.println("- null 키 불가");
    }
}
```

---

## 7. Enum 비교

### 7.1 == vs equals

```java
enum Color {
    RED, GREEN, BLUE
}

public class EnumComparison {
    public static void main(String[] args) {
        System.out.println("=== Enum 비교 ===\n");
        
        Color red1 = Color.RED;
        Color red2 = Color.RED;
        Color blue = Color.BLUE;
        
        // == 사용 (권장)
        System.out.println("== 비교:");
        System.out.println("red1 == red2: " + (red1 == red2));
        System.out.println("red1 == blue: " + (red1 == blue));
        
        
        // equals() 사용 (불필요)
        System.out.println("\nequals() 비교:");
        System.out.println("red1.equals(red2): " + red1.equals(red2));
        
        
        // null 안전성
        System.out.println("\n=== null 처리 ===");
        Color nullColor = null;
        
        // == 는 null 안전
        System.out.println("null == RED: " + (nullColor == Color.RED));
        
        // equals()는 NPE 위험
        try {
            System.out.println(nullColor.equals(Color.RED));
        } catch (NullPointerException e) {
            System.out.println("NPE 발생!");
        }
        
        
        System.out.println("\n📌 권장: == 사용");
        System.out.println("- 빠름");
        System.out.println("- null 안전");
        System.out.println("- 명확함");
    }
}
```

### 7.2 순서 비교

```java
enum Priority {
    LOW, MEDIUM, HIGH, CRITICAL
}

public class EnumOrdering {
    public static void main(String[] args) {
        System.out.println("=== Enum 순서 비교 ===\n");
        
        Priority p1 = Priority.LOW;
        Priority p2 = Priority.HIGH;
        
        // compareTo()
        int result = p1.compareTo(p2);
        System.out.println("LOW vs HIGH: " + result);
        System.out.println("(음수 = p1이 앞)");
        
        
        // 순서 비교
        if (p2.ordinal() > p1.ordinal()) {
            System.out.println("\n" + p2 + "가 " + p1 + "보다 우선순위 높음");
        }
        
        
        // 정렬
        System.out.println("\n=== 정렬 ===");
        List<Priority> priorities = Arrays.asList(
            Priority.HIGH,
            Priority.LOW,
            Priority.CRITICAL,
            Priority.MEDIUM
        );
        
        System.out.println("정렬 전: " + priorities);
        Collections.sort(priorities);
        System.out.println("정렬 후: " + priorities);
    }
}
```

---

## 8. 실전 예제

### 8.1 HTTP Status

```java
enum HttpStatus {
    OK(200, "OK"),
    CREATED(201, "Created"),
    BAD_REQUEST(400, "Bad Request"),
    UNAUTHORIZED(401, "Unauthorized"),
    FORBIDDEN(403, "Forbidden"),
    NOT_FOUND(404, "Not Found"),
    INTERNAL_SERVER_ERROR(500, "Internal Server Error");
    
    private final int code;
    private final String message;
    
    HttpStatus(int code, String message) {
        this.code = code;
        this.message = message;
    }
    
    public int getCode() {
        return code;
    }
    
    public String getMessage() {
        return message;
    }
    
    public boolean isSuccess() {
        return code >= 200 && code < 300;
    }
    
    public boolean isClientError() {
        return code >= 400 && code < 500;
    }
    
    public boolean isServerError() {
        return code >= 500 && code < 600;
    }
    
    public static HttpStatus fromCode(int code) {
        for (HttpStatus status : values()) {
            if (status.code == code) {
                return status;
            }
        }
        throw new IllegalArgumentException("Unknown code: " + code);
    }
}

public class HttpStatusExample {
    public static void main(String[] args) {
        System.out.println("=== HTTP Status ===\n");
        
        HttpStatus status = HttpStatus.OK;
        System.out.println(status.getCode() + " " + status.getMessage());
        System.out.println("성공? " + status.isSuccess());
        
        HttpStatus notFound = HttpStatus.fromCode(404);
        System.out.println("\n" + notFound.getCode() + " " + notFound.getMessage());
        System.out.println("클라이언트 오류? " + notFound.isClientError());
    }
}
```

### 8.2 계절별 할인율

```java
enum Season {
    SPRING(0.10, "봄 신상품 세일"),
    SUMMER(0.15, "여름 대세일"),
    FALL(0.05, "가을 할인"),
    WINTER(0.20, "겨울 특가");
    
    private final double discountRate;
    private final String promotion;
    
    Season(double discountRate, String promotion) {
        this.discountRate = discountRate;
        this.promotion = promotion;
    }
    
    public double getDiscountRate() {
        return discountRate;
    }
    
    public String getPromotion() {
        return promotion;
    }
    
    public double calculatePrice(double originalPrice) {
        return originalPrice * (1 - discountRate);
    }
    
    public static Season fromMonth(int month) {
        if (month >= 3 && month <= 5) return SPRING;
        if (month >= 6 && month <= 8) return SUMMER;
        if (month >= 9 && month <= 11) return FALL;
        return WINTER;
    }
}

public class SeasonDiscountExample {
    public static void main(String[] args) {
        double price = 10000;
        
        System.out.println("=== 계절별 할인 ===\n");
        System.out.printf("정가: %.0f원%n%n", price);
        
        for (Season season : Season.values()) {
            System.out.printf("%s: %s%n", season, season.getPromotion());
            System.out.printf("할인율: %.0f%%%n", season.getDiscountRate() * 100);
            System.out.printf("판매가: %.0f원%n%n", season.calculatePrice(price));
        }
        
        // 현재 계절
        int currentMonth = java.time.LocalDate.now().getMonthValue();
        Season currentSeason = Season.fromMonth(currentMonth);
        System.out.println("현재 계절: " + currentSeason);
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 요일 판별

```java
// 주중/주말 판별하는 메서드 추가
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY;
    
    // 주말 여부 메서드 구현
    public boolean isWeekend() {
        // 구현
        return false;
    }
}

public class Problem1 {
    public static void main(String[] args) {
        for (Day day : Day.values()) {
            System.out.println(day + ": " + 
                (day.isWeekend() ? "주말" : "평일"));
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public boolean isWeekend() {
    return this == SATURDAY || this == SUNDAY;
}
```
</details>

---

### 문제 2: 사칙연산

```java
// 각 연산을 구현하는 추상 메서드 apply() 추가
enum Calculator {
    PLUS("+"),
    MINUS("-"),
    MULTIPLY("*"),
    DIVIDE("/");
    
    private final String symbol;
    
    Calculator(String symbol) {
        this.symbol = symbol;
    }
    
    // 추상 메서드 선언 및 각 상수에서 구현
}

public class Problem2 {
    public static void main(String[] args) {
        System.out.println(Calculator.PLUS.apply(10, 5));      // 15
        System.out.println(Calculator.MINUS.apply(10, 5));     // 5
        System.out.println(Calculator.MULTIPLY.apply(10, 5));  // 50
        System.out.println(Calculator.DIVIDE.apply(10, 5));    // 2
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
enum Calculator {
    PLUS("+") {
        public double apply(double x, double y) {
            return x + y;
        }
    },
    MINUS("-") {
        public double apply(double x, double y) {
            return x - y;
        }
    },
    MULTIPLY("*") {
        public double apply(double x, double y) {
            return x * y;
        }
    },
    DIVIDE("/") {
        public double apply(double x, double y) {
            return x / y;
        }
    };
    
    private final String symbol;
    
    Calculator(String symbol) {
        this.symbol = symbol;
    }
    
    public abstract double apply(double x, double y);
}
```
</details>

---

### 문제 3: 색상 코드

```java
// RGB 값을 가지는 Color Enum 완성
enum Color {
    RED(/* RGB 값 */),
    GREEN(/* RGB 값 */),
    BLUE(/* RGB 값 */);
    
    // 필드, 생성자, 메서드 구현
    
    public String toHex() {
        // RGB를 16진수로 변환 (#RRGGBB)
        return null;
    }
}

public class Problem3 {
    public static void main(String[] args) {
        System.out.println(Color.RED.toHex());    // #FF0000
        System.out.println(Color.GREEN.toHex());  // #00FF00
        System.out.println(Color.BLUE.toHex());   // #0000FF
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
enum Color {
    RED(255, 0, 0),
    GREEN(0, 255, 0),
    BLUE(0, 0, 255);
    
    private final int r, g, b;
    
    Color(int r, int g, int b) {
        this.r = r;
        this.g = g;
        this.b = b;
    }
    
    public String toHex() {
        return String.format("#%02X%02X%02X", r, g, b);
    }
}
```
</details>

---

## 📌 핵심 정리

### 기본 정의
```java
enum Day {
    MONDAY, TUESDAY, WEDNESDAY
}
```

### 필드와 메서드
```java
enum Size {
    SMALL(90), MEDIUM(100), LARGE(110);
    
    private final int value;
    
    Size(int value) {
        this.value = value;
    }
    
    public int getValue() {
        return value;
    }
}
```

### 추상 메서드
```java
enum Operation {
    PLUS {
        public double apply(double x, double y) {
            return x + y;
        }
    };
    
    public abstract double apply(double x, double y);
}
```

### 내장 메서드
```java
name()       // 상수 이름
ordinal()    // 순서 (0부터)
valueOf()    // 문자열 → Enum
values()     // 모든 상수 배열
compareTo()  // 순서 비교
```

### 비교
```java
enum1 == enum2        // 권장 (빠름, null 안전)
enum1.equals(enum2)   // 가능 (불필요)
```

### 컬렉션
```java
EnumSet<Day>            // 비트 벡터 기반
EnumMap<Day, Integer>   // 배열 기반
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Enum Advanced →](./Enum-02-Advanced.md)**

</div>
