# Lambda 01. Functional Interface 완전 정복

> 함수형 인터페이스의 모든 것 - Supplier, Consumer, Function, Predicate 마스터하기  
> Java Lambda & Functional Programming

---

## 📑 목차

1. [함수형 인터페이스 기본](#1-함수형-인터페이스-기본)
2. [Supplier](#2-supplier)
3. [Consumer](#3-consumer)
4. [Function](#4-function)
5. [Predicate](#5-predicate)
6. [BiFunction과 BiConsumer](#6-bifunction과-biconsumer)
7. [UnaryOperator와 BinaryOperator](#7-unaryoperator와-binaryoperator)
8. [기본형 특화 인터페이스](#8-기본형-특화-인터페이스)
9. [실전 예제](#9-실전-예제)
10. [실전 연습 문제](#10-실전-연습-문제)

---

## 1. 함수형 인터페이스 기본

### 1.1 함수형 인터페이스란?

```java
@FunctionalInterface
interface MyFunction {
    int apply(int x);  // 단 하나의 추상 메서드
    
    // default 메서드는 여러 개 가능
    default int applyTwice(int x) {
        return apply(apply(x));
    }
    
    // static 메서드도 여러 개 가능
    static int identity(int x) {
        return x;
    }
}

public class FunctionalInterfaceBasic {
    public static void main(String[] args) {
        System.out.println("=== 함수형 인터페이스 ===\n");
        
        System.out.println("📌 정의");
        System.out.println("- 단 하나의 추상 메서드");
        System.out.println("- @FunctionalInterface 애노테이션 (선택)");
        System.out.println("- default, static 메서드는 여러 개 가능\n");
        
        System.out.println("📌 특징");
        System.out.println("- 람다 표현식으로 구현 가능");
        System.out.println("- 메서드 참조 가능");
        System.out.println("- Stream API와 함께 사용\n");
        
        
        // 람다로 구현
        MyFunction doubler = x -> x * 2;
        System.out.println("2배: " + doubler.apply(5));
        System.out.println("4배: " + doubler.applyTwice(5));
    }
}
```

### 1.2 주요 함수형 인터페이스

```java
import java.util.function.*;

public class StandardFunctionalInterfaces {
    public static void main(String[] args) {
        System.out.println("=== 표준 함수형 인터페이스 ===\n");
        
        System.out.println("📌 핵심 4가지");
        System.out.println("Supplier<T>:     () -> T");
        System.out.println("Consumer<T>:     T -> void");
        System.out.println("Function<T, R>:  T -> R");
        System.out.println("Predicate<T>:    T -> boolean\n");
        
        System.out.println("📌 확장형");
        System.out.println("BiConsumer<T, U>:        (T, U) -> void");
        System.out.println("BiFunction<T, U, R>:     (T, U) -> R");
        System.out.println("BiPredicate<T, U>:       (T, U) -> boolean");
        System.out.println("UnaryOperator<T>:        T -> T");
        System.out.println("BinaryOperator<T>:       (T, T) -> T\n");
        
        System.out.println("📌 기본형 특화");
        System.out.println("IntSupplier, IntConsumer, IntFunction");
        System.out.println("LongSupplier, DoubleConsumer, ToIntFunction");
        System.out.println("등등...");
    }
}
```

---

## 2. Supplier

### 2.1 Supplier 기본

```java
import java.util.function.*;

public class SupplierBasic {
    public static void main(String[] args) {
        System.out.println("=== Supplier<T> ===\n");
        
        System.out.println("시그니처: () -> T");
        System.out.println("메서드: T get()");
        System.out.println("역할: 값을 공급(생성)\n");
        
        
        // 간단한 Supplier
        Supplier<String> stringSupplier = () -> "Hello World";
        System.out.println(stringSupplier.get());
        
        
        // 랜덤 값
        Supplier<Integer> randomSupplier = () -> (int) (Math.random() * 100);
        System.out.println("랜덤 1: " + randomSupplier.get());
        System.out.println("랜덤 2: " + randomSupplier.get());
        
        
        // 현재 시간
        Supplier<Long> timeSupplier = () -> System.currentTimeMillis();
        System.out.println("시간: " + timeSupplier.get());
    }
}
```

### 2.2 Supplier 활용

```java
import java.util.function.*;
import java.util.*;

public class SupplierUseCases {
    static class User {
        String name;
        int age;
        
        User(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        // 객체 생성 팩토리
        Supplier<User> userFactory = () -> new User("Default", 0);
        User user1 = userFactory.get();
        User user2 = userFactory.get();
        System.out.println("User 1: " + user1);
        System.out.println("User 2: " + user2);
        
        
        // Lazy Evaluation
        System.out.println("\n=== Lazy Evaluation ===");
        Supplier<List<String>> lazyList = () -> {
            System.out.println("리스트 생성 중...");
            return Arrays.asList("A", "B", "C");
        };
        
        System.out.println("Supplier 생성됨 (아직 실행 안 됨)");
        List<String> list = lazyList.get();  // 여기서 실행
        System.out.println("리스트: " + list);
        
        
        // 기본값 제공
        System.out.println("\n=== 기본값 제공 ===");
        String value = getValueOrDefault(null, () -> "기본값");
        System.out.println("결과: " + value);
    }
    
    static String getValueOrDefault(String value, Supplier<String> defaultSupplier) {
        return value != null ? value : defaultSupplier.get();
    }
}
```

### 2.3 기본형 Supplier

```java
import java.util.function.*;

public class PrimitiveSuppliers {
    public static void main(String[] args) {
        System.out.println("=== 기본형 Supplier ===\n");
        
        // IntSupplier
        IntSupplier intSupplier = () -> 42;
        System.out.println("int: " + intSupplier.getAsInt());
        
        // LongSupplier
        LongSupplier longSupplier = () -> 123456789L;
        System.out.println("long: " + longSupplier.getAsLong());
        
        // DoubleSupplier
        DoubleSupplier doubleSupplier = () -> Math.PI;
        System.out.println("double: " + doubleSupplier.getAsDouble());
        
        // BooleanSupplier
        BooleanSupplier boolSupplier = () -> true;
        System.out.println("boolean: " + boolSupplier.getAsBoolean());
        
        
        System.out.println("\n=== 박싱/언박싱 방지 ===");
        System.out.println("Supplier<Integer> → IntSupplier (성능 향상)");
    }
}
```

---

## 3. Consumer

### 3.1 Consumer 기본

```java
import java.util.function.*;

public class ConsumerBasic {
    public static void main(String[] args) {
        System.out.println("=== Consumer<T> ===\n");
        
        System.out.println("시그니처: T -> void");
        System.out.println("메서드: void accept(T t)");
        System.out.println("역할: 값을 소비(사용)\n");
        
        
        // 출력
        Consumer<String> printer = s -> System.out.println("출력: " + s);
        printer.accept("Hello");
        
        
        // 리스트 추가
        Consumer<Integer> listAdder = num -> {
            System.out.println(num + "을(를) 처리");
        };
        listAdder.accept(10);
        listAdder.accept(20);
        
        
        // 객체 수정
        Consumer<StringBuilder> appender = sb -> sb.append(" World");
        StringBuilder sb = new StringBuilder("Hello");
        appender.accept(sb);
        System.out.println("결과: " + sb);
    }
}
```

### 3.2 Consumer 체이닝

```java
import java.util.function.*;

public class ConsumerChaining {
    static class User {
        String name;
        int age;
        
        User(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Consumer 체이닝 ===\n");
        
        Consumer<User> printName = user -> 
            System.out.println("이름: " + user.name);
        
        Consumer<User> printAge = user -> 
            System.out.println("나이: " + user.age);
        
        Consumer<User> validate = user -> {
            if (user.age < 0) {
                System.out.println("나이 오류!");
            }
        };
        
        // andThen으로 연결
        Consumer<User> pipeline = printName
            .andThen(printAge)
            .andThen(validate);
        
        User user = new User("Alice", 25);
        pipeline.accept(user);
        
        
        System.out.println("\n=== andThen 특징 ===");
        System.out.println("- 순차 실행");
        System.out.println("- 여러 Consumer 연결");
    }
}
```

### 3.3 Consumer 활용

```java
import java.util.function.*;
import java.util.*;

public class ConsumerUseCases {
    public static void main(String[] args) {
        // forEach
        System.out.println("=== forEach ===");
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
        names.forEach(name -> System.out.println("Hello, " + name));
        
        
        // 로깅
        System.out.println("\n=== 로깅 ===");
        Consumer<String> logger = msg -> 
            System.out.println("[LOG] " + msg);
        
        logger.accept("애플리케이션 시작");
        logger.accept("데이터 로드 완료");
        
        
        // 조건부 처리
        System.out.println("\n=== 조건부 처리 ===");
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        Consumer<Integer> evenPrinter = num -> {
            if (num % 2 == 0) {
                System.out.println("짝수: " + num);
            }
        };
        numbers.forEach(evenPrinter);
        
        
        // 기본형 Consumer
        System.out.println("\n=== 기본형 Consumer ===");
        IntConsumer intConsumer = value -> System.out.println("int: " + value);
        intConsumer.accept(42);
        
        DoubleConsumer doubleConsumer = value -> 
            System.out.println("double: " + value);
        doubleConsumer.accept(3.14);
    }
}
```

---

## 4. Function

### 4.1 Function 기본

```java
import java.util.function.*;

public class FunctionBasic {
    public static void main(String[] args) {
        System.out.println("=== Function<T, R> ===\n");
        
        System.out.println("시그니처: T -> R");
        System.out.println("메서드: R apply(T t)");
        System.out.println("역할: 입력을 출력으로 변환\n");
        
        
        // 문자열 길이
        Function<String, Integer> length = s -> s.length();
        System.out.println("길이: " + length.apply("Hello"));
        
        
        // 제곱
        Function<Integer, Integer> square = x -> x * x;
        System.out.println("제곱: " + square.apply(5));
        
        
        // 문자열 변환
        Function<String, String> upper = s -> s.toUpperCase();
        System.out.println("대문자: " + upper.apply("hello"));
        
        
        // 객체 변환
        Function<String, User> createUser = name -> new User(name, 0);
        User user = createUser.apply("Alice");
        System.out.println("사용자: " + user);
    }
    
    static class User {
        String name;
        int age;
        
        User(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
}
```

### 4.2 Function 합성

```java
import java.util.function.*;

public class FunctionComposition {
    public static void main(String[] args) {
        System.out.println("=== Function 합성 ===\n");
        
        Function<Integer, Integer> multiply2 = x -> x * 2;
        Function<Integer, Integer> add10 = x -> x + 10;
        
        
        // compose - 먼저 적용
        System.out.println("=== compose (먼저) ===");
        Function<Integer, Integer> composed1 = multiply2.compose(add10);
        System.out.println("compose: " + composed1.apply(5));  // (5 + 10) * 2 = 30
        
        
        // andThen - 나중에 적용
        System.out.println("\n=== andThen (나중에) ===");
        Function<Integer, Integer> composed2 = multiply2.andThen(add10);
        System.out.println("andThen: " + composed2.apply(5));  // (5 * 2) + 10 = 20
        
        
        // 여러 단계 합성
        System.out.println("\n=== 여러 단계 ===");
        Function<String, String> trim = String::trim;
        Function<String, String> upper = String::toUpperCase;
        Function<String, String> addPrefix = s -> ">>> " + s;
        
        Function<String, String> pipeline = trim
            .andThen(upper)
            .andThen(addPrefix);
        
        System.out.println(pipeline.apply("  hello  "));
        
        
        // identity
        System.out.println("\n=== identity ===");
        Function<String, String> identity = Function.identity();
        System.out.println("identity: " + identity.apply("test"));
    }
}
```

### 4.3 특화 Function

```java
import java.util.function.*;

public class SpecializedFunctions {
    public static void main(String[] args) {
        System.out.println("=== 특화 Function ===\n");
        
        // IntFunction - int를 받아 T 반환
        IntFunction<String> intToString = i -> "숫자: " + i;
        System.out.println(intToString.apply(42));
        
        
        // ToIntFunction - T를 받아 int 반환
        ToIntFunction<String> stringLength = s -> s.length();
        System.out.println("길이: " + stringLength.applyAsInt("Hello"));
        
        
        // DoubleFunction
        DoubleFunction<String> doubleToString = d -> 
            String.format("%.2f", d);
        System.out.println(doubleToString.apply(3.14159));
        
        
        // IntToDoubleFunction
        IntToDoubleFunction intToDouble = i -> i / 2.0;
        System.out.println("절반: " + intToDouble.applyAsDouble(5));
        
        
        System.out.println("\n=== 박싱/언박싱 방지 ===");
        System.out.println("Function<Integer, Integer> → IntUnaryOperator");
        System.out.println("성능 향상!");
    }
}
```

---

## 5. Predicate

### 5.1 Predicate 기본

```java
import java.util.function.*;

public class PredicateBasic {
    public static void main(String[] args) {
        System.out.println("=== Predicate<T> ===\n");
        
        System.out.println("시그니처: T -> boolean");
        System.out.println("메서드: boolean test(T t)");
        System.out.println("역할: 조건 검사\n");
        
        
        // 양수 검사
        Predicate<Integer> isPositive = n -> n > 0;
        System.out.println("5는 양수? " + isPositive.test(5));
        System.out.println("-3은 양수? " + isPositive.test(-3));
        
        
        // 짝수 검사
        Predicate<Integer> isEven = n -> n % 2 == 0;
        System.out.println("4는 짝수? " + isEven.test(4));
        
        
        // 문자열 검사
        Predicate<String> isEmpty = s -> s.isEmpty();
        System.out.println("빈 문자열? " + isEmpty.test(""));
        System.out.println("Hello? " + isEmpty.test("Hello"));
        
        
        // 길이 검사
        Predicate<String> isLongString = s -> s.length() > 5;
        System.out.println("HelloWorld 긴가? " + isLongString.test("HelloWorld"));
    }
}
```

### 5.2 Predicate 조합

```java
import java.util.function.*;

public class PredicateCombination {
    public static void main(String[] args) {
        System.out.println("=== Predicate 조합 ===\n");
        
        Predicate<Integer> isPositive = n -> n > 0;
        Predicate<Integer> isEven = n -> n % 2 == 0;
        Predicate<Integer> lessThan100 = n -> n < 100;
        
        
        // and - 모두 참
        System.out.println("=== and ===");
        Predicate<Integer> positiveEven = isPositive.and(isEven);
        System.out.println("4: " + positiveEven.test(4));    // true
        System.out.println("3: " + positiveEven.test(3));    // false
        System.out.println("-2: " + positiveEven.test(-2));  // false
        
        
        // or - 하나라도 참
        System.out.println("\n=== or ===");
        Predicate<Integer> positiveOrEven = isPositive.or(isEven);
        System.out.println("3: " + positiveOrEven.test(3));    // true (양수)
        System.out.println("-2: " + positiveOrEven.test(-2));  // true (짝수)
        System.out.println("-3: " + positiveOrEven.test(-3));  // false
        
        
        // negate - 부정
        System.out.println("\n=== negate ===");
        Predicate<Integer> isNegative = isPositive.negate();
        System.out.println("-5: " + isNegative.test(-5));  // true
        System.out.println("5: " + isNegative.test(5));    // false
        
        
        // 복잡한 조합
        System.out.println("\n=== 복잡한 조합 ===");
        Predicate<Integer> complex = isPositive
            .and(isEven)
            .and(lessThan100);
        
        System.out.println("50: " + complex.test(50));   // true
        System.out.println("150: " + complex.test(150)); // false
    }
}
```

### 5.3 Predicate 활용

```java
import java.util.function.*;
import java.util.*;
import java.util.stream.*;

public class PredicateUseCases {
    static class User {
        String name;
        int age;
        
        User(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        // 필터링
        System.out.println("=== 필터링 ===");
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        Predicate<Integer> isEven = n -> n % 2 == 0;
        List<Integer> evenNumbers = numbers.stream()
            .filter(isEven)
            .collect(Collectors.toList());
        System.out.println("짝수: " + evenNumbers);
        
        
        // 유효성 검사
        System.out.println("\n=== 유효성 검사 ===");
        Predicate<User> isAdult = user -> user.age >= 18;
        
        User user1 = new User("Alice", 25);
        User user2 = new User("Bob", 15);
        
        System.out.println(user1 + " 성인? " + isAdult.test(user1));
        System.out.println(user2 + " 성인? " + isAdult.test(user2));
        
        
        // removeIf
        System.out.println("\n=== removeIf ===");
        List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
        list.removeIf(n -> n % 2 == 0);
        System.out.println("홀수만: " + list);
        
        
        // 기본형 Predicate
        System.out.println("\n=== 기본형 Predicate ===");
        IntPredicate isPositive = n -> n > 0;
        System.out.println("5 양수? " + isPositive.test(5));
        
        DoublePredicate isNaN = Double::isNaN;
        System.out.println("NaN? " + isNaN.test(Double.NaN));
    }
}
```

---

## 6. BiFunction과 BiConsumer

### 6.1 BiFunction

```java
import java.util.function.*;

public class BiFunctionExample {
    public static void main(String[] args) {
        System.out.println("=== BiFunction<T, U, R> ===\n");
        
        System.out.println("시그니처: (T, U) -> R");
        System.out.println("메서드: R apply(T t, U u)\n");
        
        
        // 덧셈
        BiFunction<Integer, Integer, Integer> add = (a, b) -> a + b;
        System.out.println("3 + 5 = " + add.apply(3, 5));
        
        
        // 문자열 결합
        BiFunction<String, String, String> concat = (s1, s2) -> s1 + s2;
        System.out.println(concat.apply("Hello", "World"));
        
        
        // 최대값
        BiFunction<Integer, Integer, Integer> max = (a, b) -> a > b ? a : b;
        System.out.println("max(10, 20) = " + max.apply(10, 20));
        
        
        // andThen
        System.out.println("\n=== andThen ===");
        BiFunction<Integer, Integer, Integer> multiply = (a, b) -> a * b;
        Function<Integer, String> toString = n -> "결과: " + n;
        
        BiFunction<Integer, Integer, String> combined = multiply.andThen(toString);
        System.out.println(combined.apply(3, 4));
    }
}
```

### 6.2 BiConsumer

```java
import java.util.function.*;
import java.util.*;

public class BiConsumerExample {
    public static void main(String[] args) {
        System.out.println("=== BiConsumer<T, U> ===\n");
        
        System.out.println("시그니처: (T, U) -> void");
        System.out.println("메서드: void accept(T t, U u)\n");
        
        
        // 출력
        BiConsumer<String, Integer> print = (name, age) -> 
            System.out.println(name + "는 " + age + "세");
        print.accept("Alice", 25);
        
        
        // Map 처리
        System.out.println("\n=== Map forEach ===");
        Map<String, Integer> scores = new HashMap<>();
        scores.put("Alice", 90);
        scores.put("Bob", 85);
        scores.put("Charlie", 95);
        
        BiConsumer<String, Integer> printScore = (name, score) ->
            System.out.println(name + ": " + score + "점");
        
        scores.forEach(printScore);
        
        
        // andThen
        System.out.println("\n=== andThen ===");
        BiConsumer<String, Integer> logger = (name, age) ->
            System.out.println("[LOG] " + name + ", " + age);
        
        BiConsumer<String, Integer> combined = print.andThen(logger);
        combined.accept("Bob", 30);
    }
}
```

### 6.3 BiPredicate

```java
import java.util.function.*;

public class BiPredicateExample {
    public static void main(String[] args) {
        System.out.println("=== BiPredicate<T, U> ===\n");
        
        System.out.println("시그니처: (T, U) -> boolean");
        System.out.println("메서드: boolean test(T t, U u)\n");
        
        
        // 크기 비교
        BiPredicate<Integer, Integer> isGreater = (a, b) -> a > b;
        System.out.println("5 > 3? " + isGreater.test(5, 3));
        System.out.println("2 > 5? " + isGreater.test(2, 5));
        
        
        // 문자열 포함
        BiPredicate<String, String> contains = (s1, s2) -> s1.contains(s2);
        System.out.println("'Hello' contains 'll'? " + 
            contains.test("Hello", "ll"));
        
        
        // 범위 검사
        BiPredicate<Integer, Integer> inRange = (value, max) -> 
            value >= 0 && value <= max;
        System.out.println("50은 0~100 범위? " + inRange.test(50, 100));
        
        
        // 조합
        System.out.println("\n=== 조합 ===");
        BiPredicate<Integer, Integer> isEqual = (a, b) -> a.equals(b);
        BiPredicate<Integer, Integer> notEqual = isEqual.negate();
        System.out.println("5 != 3? " + notEqual.test(5, 3));
    }
}
```

---

## 7. UnaryOperator와 BinaryOperator

### 7.1 UnaryOperator

```java
import java.util.function.*;

public class UnaryOperatorExample {
    public static void main(String[] args) {
        System.out.println("=== UnaryOperator<T> ===\n");
        
        System.out.println("시그니처: T -> T");
        System.out.println("Function<T, T>의 특수 케이스\n");
        
        
        // 제곱
        UnaryOperator<Integer> square = x -> x * x;
        System.out.println("5² = " + square.apply(5));
        
        
        // 대문자 변환
        UnaryOperator<String> upper = s -> s.toUpperCase();
        System.out.println(upper.apply("hello"));
        
        
        // 절대값
        UnaryOperator<Integer> abs = x -> Math.abs(x);
        System.out.println("|-5| = " + abs.apply(-5));
        
        
        // List replaceAll
        System.out.println("\n=== replaceAll ===");
        java.util.List<Integer> numbers = new java.util.ArrayList<>(
            java.util.Arrays.asList(1, 2, 3, 4, 5)
        );
        numbers.replaceAll(n -> n * 2);
        System.out.println("2배: " + numbers);
        
        
        // 기본형
        System.out.println("\n=== 기본형 ===");
        IntUnaryOperator increment = x -> x + 1;
        System.out.println("5 + 1 = " + increment.applyAsInt(5));
        
        DoubleUnaryOperator sqrt = Math::sqrt;
        System.out.println("√16 = " + sqrt.applyAsDouble(16));
    }
}
```

### 7.2 BinaryOperator

```java
import java.util.function.*;
import java.util.*;

public class BinaryOperatorExample {
    public static void main(String[] args) {
        System.out.println("=== BinaryOperator<T> ===\n");
        
        System.out.println("시그니처: (T, T) -> T");
        System.out.println("BiFunction<T, T, T>의 특수 케이스\n");
        
        
        // 덧셈
        BinaryOperator<Integer> add = (a, b) -> a + b;
        System.out.println("3 + 5 = " + add.apply(3, 5));
        
        
        // 곱셈
        BinaryOperator<Integer> multiply = (a, b) -> a * b;
        System.out.println("3 × 5 = " + multiply.apply(3, 5));
        
        
        // 최대/최소
        System.out.println("\n=== minBy / maxBy ===");
        BinaryOperator<Integer> max = BinaryOperator.maxBy(Integer::compareTo);
        BinaryOperator<Integer> min = BinaryOperator.minBy(Integer::compareTo);
        
        System.out.println("max(10, 20) = " + max.apply(10, 20));
        System.out.println("min(10, 20) = " + min.apply(10, 20));
        
        
        // Stream reduce
        System.out.println("\n=== Stream reduce ===");
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        int sum = numbers.stream().reduce(0, add);
        System.out.println("합계: " + sum);
        
        int product = numbers.stream().reduce(1, multiply);
        System.out.println("곱: " + product);
        
        
        // 기본형
        System.out.println("\n=== 기본형 ===");
        IntBinaryOperator intAdd = (a, b) -> a + b;
        System.out.println("10 + 20 = " + intAdd.applyAsInt(10, 20));
        
        DoubleBinaryOperator doubleMax = Math::max;
        System.out.println("max(3.14, 2.71) = " + 
            doubleMax.applyAsDouble(3.14, 2.71));
    }
}
```

---

## 8. 기본형 특화 인터페이스

### 8.1 기본형 인터페이스 종류

```java
import java.util.function.*;

public class PrimitiveInterfaces {
    public static void main(String[] args) {
        System.out.println("=== 기본형 특화 인터페이스 ===\n");
        
        System.out.println("📌 Supplier 계열");
        System.out.println("IntSupplier:     () -> int");
        System.out.println("LongSupplier:    () -> long");
        System.out.println("DoubleSupplier:  () -> double");
        System.out.println("BooleanSupplier: () -> boolean\n");
        
        System.out.println("📌 Consumer 계열");
        System.out.println("IntConsumer:    int -> void");
        System.out.println("LongConsumer:   long -> void");
        System.out.println("DoubleConsumer: double -> void");
        System.out.println("ObjIntConsumer<T>: (T, int) -> void\n");
        
        System.out.println("📌 Function 계열");
        System.out.println("IntFunction<R>:      int -> R");
        System.out.println("ToIntFunction<T>:    T -> int");
        System.out.println("IntToDoubleFunction: int -> double");
        System.out.println("IntUnaryOperator:    int -> int\n");
        
        System.out.println("📌 Predicate 계열");
        System.out.println("IntPredicate:    int -> boolean");
        System.out.println("LongPredicate:   long -> boolean");
        System.out.println("DoublePredicate: double -> boolean\n");
        
        System.out.println("⚠️ 박싱/언박싱 비용 절감!");
    }
}
```

### 8.2 성능 비교

```java
import java.util.function.*;

public class BoxingPerformance {
    public static void main(String[] args) {
        int iterations = 100_000_000;
        
        // 박싱 있음
        System.out.println("=== 박싱 O ===");
        long start1 = System.currentTimeMillis();
        
        Function<Integer, Integer> boxed = x -> x + 1;
        int sum1 = 0;
        for (int i = 0; i < iterations; i++) {
            sum1 += boxed.apply(i);
        }
        
        long time1 = System.currentTimeMillis() - start1;
        System.out.println("시간: " + time1 + "ms");
        
        
        // 박싱 없음
        System.out.println("\n=== 박싱 X ===");
        long start2 = System.currentTimeMillis();
        
        IntUnaryOperator unboxed = x -> x + 1;
        int sum2 = 0;
        for (int i = 0; i < iterations; i++) {
            sum2 += unboxed.applyAsInt(i);
        }
        
        long time2 = System.currentTimeMillis() - start2;
        System.out.println("시간: " + time2 + "ms");
        
        System.out.println("\n성능 향상: " + (time1 / (double) time2) + "배");
    }
}
```

---

## 9. 실전 예제

### 9.1 유효성 검증 프레임워크

```java
import java.util.function.*;
import java.util.*;

public class ValidationFramework {
    static class Validator<T> {
        private List<Predicate<T>> rules = new ArrayList<>();
        private List<String> messages = new ArrayList<>();
        
        public Validator<T> addRule(Predicate<T> rule, String message) {
            rules.add(rule);
            messages.add(message);
            return this;
        }
        
        public List<String> validate(T value) {
            List<String> errors = new ArrayList<>();
            for (int i = 0; i < rules.size(); i++) {
                if (!rules.get(i).test(value)) {
                    errors.add(messages.get(i));
                }
            }
            return errors;
        }
    }
    
    public static void main(String[] args) {
        // 문자열 검증
        Validator<String> passwordValidator = new Validator<>();
        passwordValidator
            .addRule(s -> s.length() >= 8, "8자 이상이어야 합니다")
            .addRule(s -> s.matches(".*[A-Z].*"), "대문자를 포함해야 합니다")
            .addRule(s -> s.matches(".*[0-9].*"), "숫자를 포함해야 합니다")
            .addRule(s -> s.matches(".*[!@#$%^&*].*"), "특수문자를 포함해야 합니다");
        
        String password = "abc123";
        List<String> errors = passwordValidator.validate(password);
        
        if (errors.isEmpty()) {
            System.out.println("비밀번호 유효");
        } else {
            System.out.println("오류:");
            errors.forEach(System.out::println);
        }
    }
}
```

### 9.2 데이터 변환 파이프라인

```java
import java.util.function.*;
import java.util.*;

public class DataPipeline {
    static class Pipeline<T> {
        private T data;
        
        Pipeline(T data) {
            this.data = data;
        }
        
        public <R> Pipeline<R> map(Function<T, R> mapper) {
            return new Pipeline<>(mapper.apply(data));
        }
        
        public Pipeline<T> filter(Predicate<T> predicate) {
            if (predicate.test(data)) {
                return this;
            }
            throw new RuntimeException("Filter failed");
        }
        
        public Pipeline<T> peek(Consumer<T> action) {
            action.accept(data);
            return this;
        }
        
        public T get() {
            return data;
        }
    }
    
    public static void main(String[] args) {
        String result = new Pipeline<>("  hello world  ")
            .peek(s -> System.out.println("원본: '" + s + "'"))
            .map(String::trim)
            .peek(s -> System.out.println("trim: '" + s + "'"))
            .map(String::toUpperCase)
            .peek(s -> System.out.println("upper: '" + s + "'"))
            .filter(s -> s.length() > 5)
            .get();
        
        System.out.println("\n최종: " + result);
    }
}
```

### 9.3 이벤트 핸들러 시스템

```java
import java.util.function.*;
import java.util.*;

public class EventSystem {
    static class EventBus {
        private Map<String, List<Consumer<String>>> handlers = new HashMap<>();
        
        public void subscribe(String event, Consumer<String> handler) {
            handlers.computeIfAbsent(event, k -> new ArrayList<>()).add(handler);
        }
        
        public void publish(String event, String data) {
            List<Consumer<String>> eventHandlers = handlers.get(event);
            if (eventHandlers != null) {
                eventHandlers.forEach(handler -> handler.accept(data));
            }
        }
    }
    
    public static void main(String[] args) {
        EventBus bus = new EventBus();
        
        // 핸들러 등록
        bus.subscribe("user.login", user -> 
            System.out.println("[LOG] " + user + " 로그인"));
        
        bus.subscribe("user.login", user -> 
            System.out.println("[EMAIL] " + user + "님 환영합니다"));
        
        bus.subscribe("user.logout", user -> 
            System.out.println("[LOG] " + user + " 로그아웃"));
        
        // 이벤트 발행
        bus.publish("user.login", "Alice");
        bus.publish("user.logout", "Alice");
    }
}
```

---

## 10. 실전 연습 문제

### 문제 1: 계산기 만들기

```java
// BinaryOperator로 계산기 구현
import java.util.function.*;
import java.util.*;

public class Problem1 {
    public static void main(String[] args) {
        Map<String, BinaryOperator<Integer>> calculator = new HashMap<>();
        
        // 사칙연산 구현
        // calculator.put("+", ...);
        // calculator.put("-", ...);
        // calculator.put("*", ...);
        // calculator.put("/", ...);
        
        // 테스트
        System.out.println("10 + 5 = " + calculator.get("+").apply(10, 5));
        System.out.println("10 - 5 = " + calculator.get("-").apply(10, 5));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
calculator.put("+", (a, b) -> a + b);
calculator.put("-", (a, b) -> a - b);
calculator.put("*", (a, b) -> a * b);
calculator.put("/", (a, b) -> a / b);
```
</details>

---

### 문제 2: 필터 체이닝

```java
// Predicate로 여러 조건 체이닝
import java.util.function.*;
import java.util.*;

public class Problem2 {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // 조건: 짝수 && 5보다 큼 && 10 미만
        Predicate<Integer> isEven = n -> n % 2 == 0;
        // Predicate 체이닝 구현
        
        List<Integer> result = numbers.stream()
            .filter(/* 여기 */)
            .collect(java.util.stream.Collectors.toList());
        
        System.out.println(result);  // [6, 8]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
Predicate<Integer> greaterThan5 = n -> n > 5;
Predicate<Integer> lessThan10 = n -> n < 10;

Predicate<Integer> combined = isEven
    .and(greaterThan5)
    .and(lessThan10);

List<Integer> result = numbers.stream()
    .filter(combined)
    .collect(Collectors.toList());
```
</details>

---

### 문제 3: 변환 파이프라인

```java
// Function 합성으로 데이터 변환
import java.util.function.*;

public class Problem3 {
    public static void main(String[] args) {
        String input = "  hello world  ";
        
        // trim -> upper -> "RESULT: " 추가
        Function<String, String> trim = String::trim;
        // Function 합성 구현
        
        String result = /* 여기 */.apply(input);
        System.out.println(result);  // RESULT: HELLO WORLD
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
Function<String, String> upper = String::toUpperCase;
Function<String, String> addPrefix = s -> "RESULT: " + s;

Function<String, String> pipeline = trim
    .andThen(upper)
    .andThen(addPrefix);

String result = pipeline.apply(input);
```
</details>

---

## 📌 핵심 정리

### 핵심 4가지
```java
Supplier<T>     () -> T              // 공급
Consumer<T>     T -> void            // 소비
Function<T, R>  T -> R               // 변환
Predicate<T>    T -> boolean         // 검사
```

### 2개 입력
```java
BiConsumer<T, U>    (T, U) -> void
BiFunction<T, U, R> (T, U) -> R
BiPredicate<T, U>   (T, U) -> boolean
```

### 동일 타입
```java
UnaryOperator<T>    T -> T           // Function<T, T>
BinaryOperator<T>   (T, T) -> T      // BiFunction<T, T, T>
```

### 기본형 특화
```java
IntSupplier, IntConsumer, IntFunction
IntPredicate, IntUnaryOperator, IntBinaryOperator
(Long, Double도 동일)
```

### 조합 메서드
```java
// Function
compose()    // 먼저 적용
andThen()    // 나중에 적용
identity()   // 항등 함수

// Predicate
and()        // &&
or()         // ||
negate()     // !

// Consumer
andThen()    // 순차 실행
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Method Reference →](./Lambda-02-MethodReference.md)**

</div>
