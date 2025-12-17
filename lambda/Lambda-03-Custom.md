# Lambda 03. Custom Functional Interface 완전 정복

> 커스텀 함수형 인터페이스로 도메인 특화 API 만들기  
> Java Lambda & Functional Programming

---

## 📑 목차

1. [커스텀 함수형 인터페이스 기본](#1-커스텀-함수형-인터페이스-기본)
2. [실무 패턴](#2-실무-패턴)
3. [제네릭 활용](#3-제네릭-활용)
4. [default 메서드](#4-default-메서드)
5. [static 메서드](#5-static-메서드)
6. [예외 처리](#6-예외-처리)
7. [실전 예제](#7-실전-예제)
8. [Best Practices](#8-best-practices)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. 커스텀 함수형 인터페이스 기본

### 1.1 기본 정의

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
}

@FunctionalInterface
interface Validator<T> {
    boolean isValid(T value);
}

@FunctionalInterface
interface Transformer<T, R> {
    R transform(T input);
}

public class CustomFunctionalInterfaceBasic {
    public static void main(String[] args) {
        System.out.println("=== 커스텀 함수형 인터페이스 ===\n");
        
        // Calculator
        Calculator add = (a, b) -> a + b;
        Calculator multiply = (a, b) -> a * b;
        
        System.out.println("3 + 5 = " + add.calculate(3, 5));
        System.out.println("3 × 5 = " + multiply.calculate(3, 5));
        
        
        // Validator
        Validator<String> notEmpty = s -> s != null && !s.isEmpty();
        Validator<Integer> isPositive = n -> n > 0;
        
        System.out.println("\n빈 문자열 검증: " + notEmpty.isValid(""));
        System.out.println("Hello 검증: " + notEmpty.isValid("Hello"));
        System.out.println("5 양수? " + isPositive.isValid(5));
        
        
        // Transformer
        Transformer<String, Integer> toLength = s -> s.length();
        Transformer<Integer, String> toString = n -> "Number: " + n;
        
        System.out.println("\nHello 길이: " + toLength.transform("Hello"));
        System.out.println("42 변환: " + toString.transform(42));
    }
}
```

### 1.2 @FunctionalInterface 애노테이션

```java
@FunctionalInterface
interface MyFunction {
    void execute();
    
    // default 메서드는 OK
    default void beforeExecute() {
        System.out.println("Before");
    }
    
    // static 메서드도 OK
    static void info() {
        System.out.println("MyFunction");
    }
    
    // 두 번째 추상 메서드는 컴파일 에러!
    // void another();
}

public class FunctionalInterfaceAnnotation {
    public static void main(String[] args) {
        System.out.println("=== @FunctionalInterface ===\n");
        
        System.out.println("📌 규칙");
        System.out.println("1. 단 하나의 추상 메서드");
        System.out.println("2. default 메서드 여러 개 OK");
        System.out.println("3. static 메서드 여러 개 OK");
        System.out.println("4. Object 메서드 오버라이드 OK\n");
        
        System.out.println("📌 장점");
        System.out.println("- 컴파일 시점 검증");
        System.out.println("- 의도 명확화");
        System.out.println("- 실수 방지");
        
        
        MyFunction func = () -> System.out.println("Execute!");
        func.beforeExecute();
        func.execute();
        MyFunction.info();
    }
}
```

---

## 2. 실무 패턴

### 2.1 Repository 패턴

```java
import java.util.*;
import java.util.function.*;

@FunctionalInterface
interface Specification<T> {
    boolean isSatisfiedBy(T item);
    
    default Specification<T> and(Specification<T> other) {
        return item -> this.isSatisfiedBy(item) && other.isSatisfiedBy(item);
    }
    
    default Specification<T> or(Specification<T> other) {
        return item -> this.isSatisfiedBy(item) || other.isSatisfiedBy(item);
    }
    
    default Specification<T> not() {
        return item -> !this.isSatisfiedBy(item);
    }
}

class Product {
    String name;
    double price;
    String category;
    
    Product(String name, double price, String category) {
        this.name = name;
        this.price = price;
        this.category = category;
    }
    
    @Override
    public String toString() {
        return name + "($" + price + ")";
    }
}

public class RepositoryPattern {
    public static void main(String[] args) {
        List<Product> products = Arrays.asList(
            new Product("Laptop", 1200, "Electronics"),
            new Product("Mouse", 25, "Electronics"),
            new Product("Desk", 300, "Furniture"),
            new Product("Monitor", 400, "Electronics")
        );
        
        // Specifications
        Specification<Product> isElectronics = 
            p -> p.category.equals("Electronics");
        
        Specification<Product> isExpensive = 
            p -> p.price > 100;
        
        Specification<Product> isCheap = 
            p -> p.price < 100;
        
        // 조합
        Specification<Product> expensiveElectronics = 
            isElectronics.and(isExpensive);
        
        System.out.println("=== 비싼 전자제품 ===");
        products.stream()
            .filter(expensiveElectronics::isSatisfiedBy)
            .forEach(System.out::println);
        
        System.out.println("\n=== 전자제품이 아니거나 싼 제품 ===");
        Specification<Product> spec = isElectronics.not().or(isCheap);
        products.stream()
            .filter(spec::isSatisfiedBy)
            .forEach(System.out::println);
    }
}
```

### 2.2 Strategy 패턴

```java
@FunctionalInterface
interface DiscountStrategy {
    double applyDiscount(double price);
    
    static DiscountStrategy noDiscount() {
        return price -> price;
    }
    
    static DiscountStrategy percentageDiscount(double percentage) {
        return price -> price * (1 - percentage / 100);
    }
    
    static DiscountStrategy fixedDiscount(double amount) {
        return price -> Math.max(0, price - amount);
    }
}

public class StrategyPattern {
    static class Order {
        double amount;
        DiscountStrategy strategy;
        
        Order(double amount) {
            this.amount = amount;
            this.strategy = DiscountStrategy.noDiscount();
        }
        
        void setDiscountStrategy(DiscountStrategy strategy) {
            this.strategy = strategy;
        }
        
        double getFinalPrice() {
            return strategy.applyDiscount(amount);
        }
    }
    
    public static void main(String[] args) {
        Order order = new Order(1000);
        
        System.out.println("원가: $" + order.getFinalPrice());
        
        // 10% 할인
        order.setDiscountStrategy(DiscountStrategy.percentageDiscount(10));
        System.out.println("10% 할인: $" + order.getFinalPrice());
        
        // $100 할인
        order.setDiscountStrategy(DiscountStrategy.fixedDiscount(100));
        System.out.println("$100 할인: $" + order.getFinalPrice());
        
        // 할인 없음
        order.setDiscountStrategy(DiscountStrategy.noDiscount());
        System.out.println("할인 없음: $" + order.getFinalPrice());
    }
}
```

### 2.3 Builder 패턴

```java
@FunctionalInterface
interface Configurator<T> {
    void configure(T object);
    
    default Configurator<T> andThen(Configurator<T> after) {
        return obj -> {
            this.configure(obj);
            after.configure(obj);
        };
    }
}

class Server {
    String host;
    int port;
    int timeout;
    boolean ssl;
    
    @Override
    public String toString() {
        return String.format("Server{host='%s', port=%d, timeout=%d, ssl=%b}", 
            host, port, timeout, ssl);
    }
}

public class BuilderPattern {
    public static Server createServer(Configurator<Server> configurator) {
        Server server = new Server();
        configurator.configure(server);
        return server;
    }
    
    public static void main(String[] args) {
        // 설정 조합
        Configurator<Server> basicConfig = server -> {
            server.host = "localhost";
            server.port = 8080;
        };
        
        Configurator<Server> timeoutConfig = server -> {
            server.timeout = 3000;
        };
        
        Configurator<Server> sslConfig = server -> {
            server.ssl = true;
        };
        
        // 조합
        Server server1 = createServer(basicConfig);
        System.out.println("Basic: " + server1);
        
        Server server2 = createServer(
            basicConfig.andThen(timeoutConfig).andThen(sslConfig)
        );
        System.out.println("Full: " + server2);
    }
}
```

---

## 3. 제네릭 활용

### 3.1 제네릭 함수형 인터페이스

```java
@FunctionalInterface
interface Mapper<T, R> {
    R map(T input);
}

@FunctionalInterface
interface BiMapper<T, U, R> {
    R map(T first, U second);
}

@FunctionalInterface
interface TriFunction<T, U, V, R> {
    R apply(T t, U u, V v);
}

public class GenericFunctionalInterface {
    public static void main(String[] args) {
        // Mapper
        Mapper<String, Integer> stringLength = s -> s.length();
        Mapper<Integer, String> intToHex = i -> Integer.toHexString(i);
        
        System.out.println("Hello 길이: " + stringLength.map("Hello"));
        System.out.println("255 → hex: " + intToHex.map(255));
        
        
        // BiMapper
        BiMapper<String, String, String> concat = (s1, s2) -> s1 + s2;
        BiMapper<Integer, Integer, Double> divide = (a, b) -> a / (double) b;
        
        System.out.println("\nHello + World: " + concat.map("Hello", "World"));
        System.out.println("10 / 3: " + divide.map(10, 3));
        
        
        // TriFunction
        TriFunction<String, Integer, Boolean, String> format = 
            (str, num, flag) -> String.format("%s-%d-%b", str, num, flag);
        
        System.out.println("\n" + format.apply("Test", 42, true));
    }
}
```

### 3.2 타입 제약

```java
@FunctionalInterface
interface Comparable<T extends java.lang.Comparable<T>> {
    int compare(T a, T b);
}

@FunctionalInterface
interface NumberProcessor<T extends Number> {
    double process(T number);
}

@FunctionalInterface
interface CollectionProcessor<T, C extends java.util.Collection<T>> {
    void process(C collection);
}

public class BoundedGenericInterface {
    public static void main(String[] args) {
        // Comparable
        Comparable<Integer> intComparator = (a, b) -> a.compareTo(b);
        System.out.println("5 vs 3: " + intComparator.compare(5, 3));
        
        
        // NumberProcessor
        NumberProcessor<Integer> doubler = n -> n.doubleValue() * 2;
        NumberProcessor<Double> sqrt = n -> Math.sqrt(n.doubleValue());
        
        System.out.println("\n5 * 2: " + doubler.process(5));
        System.out.println("√16: " + sqrt.process(16.0));
        
        
        // CollectionProcessor
        CollectionProcessor<String, java.util.List<String>> listPrinter = 
            list -> list.forEach(System.out::println);
        
        System.out.println("\n리스트:");
        listPrinter.process(java.util.Arrays.asList("A", "B", "C"));
    }
}
```

---

## 4. default 메서드

### 4.1 조합 메서드

```java
@FunctionalInterface
interface Filter<T> {
    boolean test(T value);
    
    default Filter<T> and(Filter<T> other) {
        return value -> this.test(value) && other.test(value);
    }
    
    default Filter<T> or(Filter<T> other) {
        return value -> this.test(value) || other.test(value);
    }
    
    default Filter<T> negate() {
        return value -> !this.test(value);
    }
    
    static <T> Filter<T> always() {
        return value -> true;
    }
    
    static <T> Filter<T> never() {
        return value -> false;
    }
}

public class DefaultMethodCombination {
    public static void main(String[] args) {
        Filter<Integer> isPositive = n -> n > 0;
        Filter<Integer> isEven = n -> n % 2 == 0;
        Filter<Integer> lessThan100 = n -> n < 100;
        
        // 조합
        Filter<Integer> positiveEven = isPositive.and(isEven);
        Filter<Integer> inRange = isPositive.and(lessThan100);
        Filter<Integer> negativeOrOdd = isPositive.negate().or(isEven.negate());
        
        System.out.println("=== 양수 + 짝수 ===");
        System.out.println("4: " + positiveEven.test(4));
        System.out.println("3: " + positiveEven.test(3));
        System.out.println("-2: " + positiveEven.test(-2));
        
        System.out.println("\n=== 범위 (0 < n < 100) ===");
        System.out.println("50: " + inRange.test(50));
        System.out.println("150: " + inRange.test(150));
        System.out.println("-5: " + inRange.test(-5));
    }
}
```

### 4.2 Fluent API

```java
import java.util.*;

@FunctionalInterface
interface Pipeline<T> {
    T execute(T input);
    
    default Pipeline<T> andThen(Pipeline<T> after) {
        return input -> after.execute(this.execute(input));
    }
    
    default Pipeline<T> compose(Pipeline<T> before) {
        return input -> this.execute(before.execute(input));
    }
    
    static <T> Pipeline<T> identity() {
        return input -> input;
    }
}

public class FluentAPI {
    public static void main(String[] args) {
        // String 파이프라인
        Pipeline<String> trim = String::trim;
        Pipeline<String> upper = String::toUpperCase;
        Pipeline<String> addPrefix = s -> ">>> " + s;
        Pipeline<String> addSuffix = s -> s + " <<<";
        
        Pipeline<String> processor = trim
            .andThen(upper)
            .andThen(addPrefix)
            .andThen(addSuffix);
        
        String input = "  hello world  ";
        String result = processor.execute(input);
        
        System.out.println("입력: '" + input + "'");
        System.out.println("출력: '" + result + "'");
        
        
        // 역순
        System.out.println("\n=== 역순 ===");
        Pipeline<String> reversed = addSuffix
            .compose(addPrefix)
            .compose(upper)
            .compose(trim);
        
        System.out.println(reversed.execute(input));
    }
}
```

---

## 5. static 메서드

### 5.1 팩토리 메서드

```java
@FunctionalInterface
interface Converter<T, R> {
    R convert(T input);
    
    static <T> Converter<T, String> toStringConverter() {
        return Object::toString;
    }
    
    static Converter<String, Integer> stringToInt() {
        return Integer::parseInt;
    }
    
    static Converter<String, Double> stringToDouble() {
        return Double::parseDouble;
    }
    
    static <T, R> Converter<T, R> identity() {
        return input -> (R) input;
    }
    
    static <T, R> Converter<T, R> constant(R value) {
        return input -> value;
    }
}

public class StaticFactoryMethods {
    public static void main(String[] args) {
        // 팩토리 메서드 사용
        Converter<Integer, String> toString = Converter.toStringConverter();
        System.out.println("42 → String: " + toString.convert(42));
        
        Converter<String, Integer> toInt = Converter.stringToInt();
        System.out.println("'123' → int: " + toInt.convert("123"));
        
        Converter<String, Double> toDouble = Converter.stringToDouble();
        System.out.println("'3.14' → double: " + toDouble.convert("3.14"));
        
        // constant
        Converter<String, Integer> always42 = Converter.constant(42);
        System.out.println("\nalways 42: " + always42.convert("anything"));
        System.out.println("always 42: " + always42.convert("something"));
    }
}
```

---

## 6. 예외 처리

### 6.1 예외를 던지는 함수형 인터페이스

```java
@FunctionalInterface
interface ThrowingFunction<T, R, E extends Exception> {
    R apply(T input) throws E;
}

@FunctionalInterface
interface ThrowingConsumer<T, E extends Exception> {
    void accept(T input) throws E;
}

@FunctionalInterface
interface ThrowingSupplier<T, E extends Exception> {
    T get() throws E;
}

public class ExceptionHandlingInterface {
    static String readFile(String path) throws java.io.IOException {
        // 파일 읽기 시뮬레이션
        if (path.isEmpty()) {
            throw new java.io.IOException("Invalid path");
        }
        return "Content of " + path;
    }
    
    public static void main(String[] args) {
        // ThrowingFunction
        ThrowingFunction<String, String, java.io.IOException> reader = 
            ExceptionHandlingInterface::readFile;
        
        try {
            String content = reader.apply("file.txt");
            System.out.println(content);
        } catch (java.io.IOException e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        
        // ThrowingConsumer
        ThrowingConsumer<String, java.io.IOException> writer = path -> {
            if (path.isEmpty()) {
                throw new java.io.IOException("Cannot write");
            }
            System.out.println("Writing to " + path);
        };
        
        try {
            writer.accept("output.txt");
        } catch (java.io.IOException e) {
            System.out.println("오류: " + e.getMessage());
        }
    }
}
```

### 6.2 예외 래핑

```java
import java.util.function.*;

@FunctionalInterface
interface UncheckedFunction<T, R> extends Function<T, R> {
    R applyThrows(T input) throws Exception;
    
    @Override
    default R apply(T input) {
        try {
            return applyThrows(input);
        } catch (Exception e) {
            throw new RuntimeException(e);
        }
    }
    
    static <T, R> Function<T, R> unchecked(UncheckedFunction<T, R> func) {
        return func;
    }
}

public class ExceptionWrapping {
    static int parseInteger(String s) throws NumberFormatException {
        return Integer.parseInt(s);
    }
    
    public static void main(String[] args) {
        java.util.List<String> numbers = java.util.Arrays.asList("1", "2", "abc", "4");
        
        // 예외 래핑
        Function<String, Integer> parser = UncheckedFunction.unchecked(s -> {
            return Integer.parseInt(s);
        });
        
        numbers.stream()
            .map(parser)
            .forEach(System.out::println);  // abc에서 RuntimeException
    }
}
```

---

## 7. 실전 예제

### 7.1 유효성 검증 프레임워크

```java
import java.util.*;

@FunctionalInterface
interface ValidationRule<T> {
    ValidationResult validate(T value);
    
    default ValidationRule<T> and(ValidationRule<T> other) {
        return value -> {
            ValidationResult first = this.validate(value);
            return first.isValid() ? other.validate(value) : first;
        };
    }
    
    static <T> ValidationRule<T> of(
        java.util.function.Predicate<T> predicate, 
        String errorMessage
    ) {
        return value -> predicate.test(value) 
            ? ValidationResult.valid() 
            : ValidationResult.invalid(errorMessage);
    }
}

class ValidationResult {
    private boolean valid;
    private String message;
    
    private ValidationResult(boolean valid, String message) {
        this.valid = valid;
        this.message = message;
    }
    
    static ValidationResult valid() {
        return new ValidationResult(true, null);
    }
    
    static ValidationResult invalid(String message) {
        return new ValidationResult(false, message);
    }
    
    boolean isValid() {
        return valid;
    }
    
    String getMessage() {
        return message;
    }
}

public class ValidationFramework {
    public static void main(String[] args) {
        // 이메일 검증
        ValidationRule<String> notNull = ValidationRule.of(
            s -> s != null, "null 불가"
        );
        
        ValidationRule<String> notEmpty = ValidationRule.of(
            s -> !s.isEmpty(), "빈 문자열 불가"
        );
        
        ValidationRule<String> hasAtSign = ValidationRule.of(
            s -> s.contains("@"), "@ 포함 필요"
        );
        
        ValidationRule<String> hasDomain = ValidationRule.of(
            s -> s.contains("."), ". 포함 필요"
        );
        
        ValidationRule<String> emailValidator = notNull
            .and(notEmpty)
            .and(hasAtSign)
            .and(hasDomain);
        
        // 테스트
        String[] emails = {"test@example.com", "invalid", "", null};
        
        for (String email : emails) {
            ValidationResult result = emailValidator.validate(email);
            if (result.isValid()) {
                System.out.println(email + ": 유효");
            } else {
                System.out.println(email + ": " + result.getMessage());
            }
        }
    }
}
```

### 7.2 이벤트 핸들링

```java
import java.util.*;

@FunctionalInterface
interface EventHandler<T> {
    void handle(T event);
    
    default EventHandler<T> andThen(EventHandler<T> after) {
        return event -> {
            this.handle(event);
            after.handle(event);
        };
    }
    
    static <T> EventHandler<T> composite(List<EventHandler<T>> handlers) {
        return event -> handlers.forEach(h -> h.handle(event));
    }
}

class Event {
    String type;
    String data;
    long timestamp;
    
    Event(String type, String data) {
        this.type = type;
        this.data = data;
        this.timestamp = System.currentTimeMillis();
    }
    
    @Override
    public String toString() {
        return type + ": " + data;
    }
}

public class EventHandlingSystem {
    public static void main(String[] args) {
        // 핸들러들
        EventHandler<Event> logger = event -> 
            System.out.println("[LOG] " + event);
        
        EventHandler<Event> metrics = event -> 
            System.out.println("[METRICS] Event recorded at " + event.timestamp);
        
        EventHandler<Event> notification = event -> {
            if (event.type.equals("ERROR")) {
                System.out.println("[ALERT] " + event.data);
            }
        };
        
        // 조합
        EventHandler<Event> pipeline = logger
            .andThen(metrics)
            .andThen(notification);
        
        // 이벤트 처리
        pipeline.handle(new Event("INFO", "Application started"));
        pipeline.handle(new Event("ERROR", "Database connection failed"));
    }
}
```

---

## 8. Best Practices

### 8.1 명확한 이름

```java
// ❌ 나쁜 예
@FunctionalInterface
interface Processor {
    void process(Object obj);
}

// ✅ 좋은 예
@FunctionalInterface
interface UserValidator {
    boolean isValid(User user);
}

@FunctionalInterface
interface EmailSender {
    void sendEmail(String to, String subject, String body);
}

@FunctionalInterface
interface PriceCalculator {
    double calculate(double basePrice, double taxRate);
}

public class NamingBestPractices {
    static class User {
        String email;
        int age;
        
        User(String email, int age) {
            this.email = email;
            this.age = age;
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 명확한 이름 사용 ===\n");
        
        UserValidator ageValidator = user -> user.age >= 18;
        UserValidator emailValidator = user -> 
            user.email != null && user.email.contains("@");
        
        User user = new User("test@example.com", 25);
        
        System.out.println("나이 검증: " + ageValidator.isValid(user));
        System.out.println("이메일 검증: " + emailValidator.isValid(user));
        
        
        PriceCalculator calculator = (base, tax) -> base * (1 + tax);
        System.out.println("\n가격: $" + calculator.calculate(100, 0.1));
    }
}
```

### 8.2 단일 책임

```java
// ❌ 나쁜 예 - 너무 많은 책임
@FunctionalInterface
interface DataProcessor {
    void process(String data);  // 읽기, 변환, 저장 모두?
}

// ✅ 좋은 예 - 단일 책임
@FunctionalInterface
interface DataReader {
    String read();
}

@FunctionalInterface
interface DataTransformer {
    String transform(String input);
}

@FunctionalInterface
interface DataWriter {
    void write(String data);
}

public class SingleResponsibility {
    public static void main(String[] args) {
        DataReader reader = () -> "raw data";
        DataTransformer transformer = data -> data.toUpperCase();
        DataWriter writer = data -> System.out.println("저장: " + data);
        
        // 파이프라인
        String data = reader.read();
        data = transformer.transform(data);
        writer.write(data);
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 조건 조합기

```java
// Condition 인터페이스 완성
@FunctionalInterface
interface Condition<T> {
    boolean test(T value);
    
    // and, or, negate 메서드 구현
}

public class Problem1 {
    public static void main(String[] args) {
        Condition<Integer> isEven = n -> n % 2 == 0;
        Condition<Integer> isPositive = n -> n > 0;
        
        // 양수 + 짝수
        Condition<Integer> combined = /* 구현 */;
        
        System.out.println("4: " + combined.test(4));    // true
        System.out.println("3: " + combined.test(3));    // false
        System.out.println("-2: " + combined.test(-2));  // false
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
@FunctionalInterface
interface Condition<T> {
    boolean test(T value);
    
    default Condition<T> and(Condition<T> other) {
        return value -> this.test(value) && other.test(value);
    }
    
    default Condition<T> or(Condition<T> other) {
        return value -> this.test(value) || other.test(value);
    }
    
    default Condition<T> negate() {
        return value -> !this.test(value);
    }
}

Condition<Integer> combined = isPositive.and(isEven);
```
</details>

---

### 문제 2: 변환 파이프라인

```java
// Transformer 인터페이스 완성
@FunctionalInterface
interface Transformer<T> {
    T transform(T input);
    
    // andThen 메서드 구현
}

public class Problem2 {
    public static void main(String[] args) {
        Transformer<String> trim = String::trim;
        Transformer<String> upper = String::toUpperCase;
        Transformer<String> addPrefix = s -> ">>> " + s;
        
        // 조합
        Transformer<String> pipeline = /* 구현 */;
        
        String result = pipeline.transform("  hello  ");
        System.out.println(result);  // >>> HELLO
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
@FunctionalInterface
interface Transformer<T> {
    T transform(T input);
    
    default Transformer<T> andThen(Transformer<T> after) {
        return input -> after.transform(this.transform(input));
    }
}

Transformer<String> pipeline = trim.andThen(upper).andThen(addPrefix);
```
</details>

---

### 문제 3: 팩토리 메서드

```java
// Builder 인터페이스 완성
@FunctionalInterface
interface Builder<T> {
    T build();
    
    // 팩토리 메서드들 구현
    // static <T> Builder<List<T>> listBuilder()
    // static <T> Builder<Set<T>> setBuilder()
}

public class Problem3 {
    public static void main(String[] args) {
        Builder<java.util.List<String>> listBuilder = /* 구현 */;
        java.util.List<String> list = listBuilder.build();
        
        Builder<java.util.Set<Integer>> setBuilder = /* 구현 */;
        java.util.Set<Integer> set = setBuilder.build();
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
import java.util.*;

@FunctionalInterface
interface Builder<T> {
    T build();
    
    static <T> Builder<List<T>> listBuilder() {
        return ArrayList::new;
    }
    
    static <T> Builder<Set<T>> setBuilder() {
        return HashSet::new;
    }
}

Builder<List<String>> listBuilder = Builder.listBuilder();
Builder<Set<Integer>> setBuilder = Builder.setBuilder();
```
</details>

---

## 📌 핵심 정리

### 정의
```java
@FunctionalInterface
interface MyFunction<T, R> {
    R apply(T input);  // 단 하나의 추상 메서드
    
    default MyFunction<T, R> andThen(...) { }  // default OK
    static MyFunction<T, R> identity() { }     // static OK
}
```

### Best Practices
```
✅ @FunctionalInterface 사용
✅ 명확한 이름
✅ 단일 책임
✅ 제네릭 활용
✅ default로 조합 제공
✅ static으로 팩토리 제공
```

### 실무 패턴
```java
// Specification
Specification<T>.and()/or()/not()

// Strategy
DiscountStrategy.percentageDiscount(10)

// Builder
Configurator<T>.andThen()

// Pipeline
Pipeline<T>.andThen()/compose()
```

### 예외 처리
```java
@FunctionalInterface
interface ThrowingFunction<T, R, E extends Exception> {
    R apply(T input) throws E;
}
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. Method Reference](./Lambda-02-MethodReference.md)**

</div>
