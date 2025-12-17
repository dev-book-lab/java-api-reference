# Lambda 02. Method Reference 완전 정복

> 메서드 참조로 코드 간결화 - :: 연산자 마스터하기  
> Java Lambda & Functional Programming

---

## 📑 목차

1. [메서드 참조 기본](#1-메서드-참조-기본)
2. [정적 메서드 참조](#2-정적-메서드-참조)
3. [인스턴스 메서드 참조](#3-인스턴스-메서드-참조)
4. [특정 객체의 메서드 참조](#4-특정-객체의-메서드-참조)
5. [생성자 참조](#5-생성자-참조)
6. [배열 생성자 참조](#6-배열-생성자-참조)
7. [메서드 참조 활용](#7-메서드-참조-활용)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. 메서드 참조 기본

### 1.1 메서드 참조란?

```java
import java.util.*;
import java.util.function.*;

public class MethodReferenceIntro {
    public static void main(String[] args) {
        System.out.println("=== 메서드 참조 ===\n");
        
        System.out.println("📌 정의");
        System.out.println("람다 표현식을 더 간결하게 표현");
        System.out.println(":: 연산자 사용\n");
        
        System.out.println("📌 종류");
        System.out.println("1. 정적 메서드 참조:        Class::staticMethod");
        System.out.println("2. 인스턴스 메서드 참조:    Class::instanceMethod");
        System.out.println("3. 특정 객체 메서드 참조:   object::instanceMethod");
        System.out.println("4. 생성자 참조:             Class::new\n");
        
        
        // 람다 vs 메서드 참조
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
        
        System.out.println("=== 람다 ===");
        names.forEach(name -> System.out.println(name));
        
        System.out.println("\n=== 메서드 참조 ===");
        names.forEach(System.out::println);
        
        System.out.println("\n→ 동일한 결과, 더 간결!");
    }
}
```

### 1.2 언제 사용하나?

```java
import java.util.*;
import java.util.function.*;

public class WhenToUseMethodReference {
    public static void main(String[] args) {
        System.out.println("=== 메서드 참조 사용 시기 ===\n");
        
        System.out.println("✅ 사용 가능");
        System.out.println("- 람다가 단순히 메서드만 호출");
        System.out.println("- 매개변수를 그대로 전달");
        System.out.println("- 추가 로직 없음\n");
        
        System.out.println("❌ 사용 불가");
        System.out.println("- 추가 연산이 필요한 경우");
        System.out.println("- 매개변수 가공이 필요한 경우\n");
        
        
        // OK
        Function<String, Integer> ok1 = String::length;
        Function<String, String> ok2 = String::toUpperCase;
        
        // NO - 추가 연산 필요
        Function<String, Integer> no1 = s -> s.length() * 2;
        Function<String, String> no2 = s -> "Hello " + s;
        
        System.out.println("length: " + ok1.apply("Hello"));
        System.out.println("upper: " + ok2.apply("hello"));
    }
}
```

---

## 2. 정적 메서드 참조

### 2.1 기본 사용법

```java
import java.util.*;
import java.util.function.*;

public class StaticMethodReference {
    // 정적 메서드들
    static int parseInt(String s) {
        return Integer.parseInt(s);
    }
    
    static double sqrt(double x) {
        return Math.sqrt(x);
    }
    
    static String format(double d) {
        return String.format("%.2f", d);
    }
    
    public static void main(String[] args) {
        System.out.println("=== 정적 메서드 참조 ===\n");
        
        System.out.println("문법: Class::staticMethod\n");
        
        
        // Integer.parseInt
        Function<String, Integer> parser1 = s -> Integer.parseInt(s);
        Function<String, Integer> parser2 = Integer::parseInt;
        
        System.out.println("람다: " + parser1.apply("123"));
        System.out.println("참조: " + parser2.apply("456"));
        
        
        // Math.sqrt
        DoubleUnaryOperator sqrt1 = x -> Math.sqrt(x);
        DoubleUnaryOperator sqrt2 = Math::sqrt;
        
        System.out.println("\nsqrt(16): " + sqrt2.applyAsDouble(16));
        
        
        // String.format
        Function<Double, String> formatter = String::format;
        // 주의: format은 가변인자라 컴파일 에러 가능
    }
}
```

### 2.2 실전 예제

```java
import java.util.*;
import java.util.stream.*;

public class StaticMethodReferenceExamples {
    public static void main(String[] args) {
        // 문자열을 숫자로
        System.out.println("=== 문자열 → 숫자 ===");
        List<String> numbers = Arrays.asList("1", "2", "3", "4", "5");
        
        List<Integer> ints = numbers.stream()
            .map(Integer::parseInt)
            .collect(Collectors.toList());
        System.out.println(ints);
        
        
        // 비교
        System.out.println("\n=== 정렬 (비교) ===");
        List<Integer> values = Arrays.asList(5, 2, 8, 1, 9);
        values.sort(Integer::compare);
        System.out.println(values);
        
        
        // Objects.isNull / nonNull
        System.out.println("\n=== null 검사 ===");
        List<String> items = Arrays.asList("A", null, "B", null, "C");
        
        long nullCount = items.stream()
            .filter(Objects::isNull)
            .count();
        System.out.println("null 개수: " + nullCount);
        
        List<String> nonNulls = items.stream()
            .filter(Objects::nonNull)
            .collect(Collectors.toList());
        System.out.println("non-null: " + nonNulls);
    }
}
```

---

## 3. 인스턴스 메서드 참조

### 3.1 클래스의 인스턴스 메서드

```java
import java.util.*;
import java.util.function.*;

public class InstanceMethodReference {
    public static void main(String[] args) {
        System.out.println("=== 인스턴스 메서드 참조 ===\n");
        
        System.out.println("문법: Class::instanceMethod");
        System.out.println("첫 번째 인자가 메서드의 수신자\n");
        
        
        // String.length
        Function<String, Integer> length1 = s -> s.length();
        Function<String, Integer> length2 = String::length;
        
        System.out.println("람다: " + length1.apply("Hello"));
        System.out.println("참조: " + length2.apply("Hello"));
        
        
        // String.toUpperCase
        Function<String, String> upper1 = s -> s.toUpperCase();
        Function<String, String> upper2 = String::toUpperCase;
        
        System.out.println("\nupper: " + upper2.apply("hello"));
        
        
        // String.startsWith
        BiPredicate<String, String> startsWith1 = (s, prefix) -> s.startsWith(prefix);
        BiPredicate<String, String> startsWith2 = String::startsWith;
        
        System.out.println("\nstarts with 'He': " + 
            startsWith2.test("Hello", "He"));
    }
}
```

### 3.2 실전 활용

```java
import java.util.*;
import java.util.stream.*;

public class InstanceMethodReferenceUsage {
    static class Person {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        String getName() {
            return name;
        }
        
        int getAge() {
            return age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Alice", 25),
            new Person("Bob", 30),
            new Person("Charlie", 20)
        );
        
        
        // 이름 추출
        System.out.println("=== 이름 추출 ===");
        List<String> names = people.stream()
            .map(Person::getName)
            .collect(Collectors.toList());
        System.out.println(names);
        
        
        // 나이로 정렬
        System.out.println("\n=== 나이 정렬 ===");
        people.sort(Comparator.comparing(Person::getAge));
        System.out.println(people);
        
        
        // 문자열 처리
        System.out.println("\n=== 문자열 처리 ===");
        List<String> words = Arrays.asList("  hello  ", "  world  ", "  java  ");
        
        List<String> trimmed = words.stream()
            .map(String::trim)
            .map(String::toUpperCase)
            .collect(Collectors.toList());
        System.out.println(trimmed);
    }
}
```

---

## 4. 특정 객체의 메서드 참조

### 4.1 기본 사용법

```java
import java.util.*;
import java.util.function.*;

public class BoundMethodReference {
    public static void main(String[] args) {
        System.out.println("=== 특정 객체 메서드 참조 ===\n");
        
        System.out.println("문법: object::instanceMethod");
        System.out.println("특정 객체에 바인딩\n");
        
        
        // PrintStream
        Consumer<String> printer1 = s -> System.out.println(s);
        Consumer<String> printer2 = System.out::println;
        
        printer2.accept("Hello");
        
        
        // StringBuilder
        StringBuilder sb = new StringBuilder();
        Consumer<String> appender1 = s -> sb.append(s);
        Consumer<String> appender2 = sb::append;
        
        appender2.accept("Hello");
        appender2.accept(" ");
        appender2.accept("World");
        System.out.println(sb);
        
        
        // 특정 문자열
        String prefix = "Hello, ";
        Function<String, String> greeter = prefix::concat;
        System.out.println(greeter.apply("Alice"));
        System.out.println(greeter.apply("Bob"));
    }
}
```

### 4.2 실전 활용

```java
import java.util.*;
import java.util.function.*;

public class BoundMethodReferenceExamples {
    static class Logger {
        String prefix;
        
        Logger(String prefix) {
            this.prefix = prefix;
        }
        
        void log(String message) {
            System.out.println("[" + prefix + "] " + message);
        }
    }
    
    static class Counter {
        int count = 0;
        
        void increment() {
            count++;
        }
        
        int getCount() {
            return count;
        }
    }
    
    public static void main(String[] args) {
        // Logger
        System.out.println("=== Logger ===");
        Logger errorLogger = new Logger("ERROR");
        Logger infoLogger = new Logger("INFO");
        
        Consumer<String> logError = errorLogger::log;
        Consumer<String> logInfo = infoLogger::log;
        
        logError.accept("파일을 찾을 수 없음");
        logInfo.accept("서버 시작됨");
        
        
        // Counter
        System.out.println("\n=== Counter ===");
        Counter counter = new Counter();
        Runnable increment = counter::increment;
        
        increment.run();
        increment.run();
        increment.run();
        System.out.println("Count: " + counter.getCount());
        
        
        // List operations
        System.out.println("\n=== List ===");
        List<String> list = new ArrayList<>();
        Consumer<String> add = list::add;
        
        add.accept("A");
        add.accept("B");
        add.accept("C");
        System.out.println(list);
    }
}
```

---

## 5. 생성자 참조

### 5.1 기본 생성자

```java
import java.util.*;
import java.util.function.*;

public class ConstructorReference {
    static class User {
        String name;
        int age;
        
        User() {
            this.name = "Unknown";
            this.age = 0;
        }
        
        User(String name) {
            this.name = name;
            this.age = 0;
        }
        
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
        System.out.println("=== 생성자 참조 ===\n");
        
        System.out.println("문법: Class::new\n");
        
        
        // 기본 생성자
        Supplier<User> factory1 = () -> new User();
        Supplier<User> factory2 = User::new;
        
        System.out.println("기본: " + factory2.get());
        
        
        // 1개 매개변수
        Function<String, User> factory3 = name -> new User(name);
        Function<String, User> factory4 = User::new;
        
        System.out.println("이름: " + factory4.apply("Alice"));
        
        
        // 2개 매개변수
        BiFunction<String, Integer, User> factory5 = 
            (name, age) -> new User(name, age);
        BiFunction<String, Integer, User> factory6 = User::new;
        
        System.out.println("이름+나이: " + factory6.apply("Bob", 30));
    }
}
```

### 5.2 생성자 참조 활용

```java
import java.util.*;
import java.util.stream.*;
import java.util.function.*;

public class ConstructorReferenceUsage {
    static class Person {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        // 리스트 변환
        System.out.println("=== 리스트 변환 ===");
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
        
        List<Person> people = names.stream()
            .map(name -> new Person(name, 0))
            .collect(Collectors.toList());
        System.out.println(people);
        
        
        // 팩토리 패턴
        System.out.println("\n=== 팩토리 ===");
        Function<String, Person> personFactory = 
            name -> new Person(name, 25);
        
        Person p1 = personFactory.apply("David");
        Person p2 = personFactory.apply("Eve");
        System.out.println(p1);
        System.out.println(p2);
        
        
        // 복사 생성자
        System.out.println("\n=== 복사 ===");
        List<Integer> original = Arrays.asList(1, 2, 3, 4, 5);
        List<Integer> copy = original.stream()
            .collect(Collectors.toCollection(ArrayList::new));
        System.out.println("복사: " + copy);
        
        
        // HashSet 생성
        Set<String> set = names.stream()
            .collect(Collectors.toCollection(HashSet::new));
        System.out.println("Set: " + set);
    }
}
```

---

## 6. 배열 생성자 참조

### 6.1 배열 생성

```java
import java.util.*;
import java.util.function.*;
import java.util.stream.*;

public class ArrayConstructorReference {
    public static void main(String[] args) {
        System.out.println("=== 배열 생성자 참조 ===\n");
        
        System.out.println("문법: Type[]::new\n");
        
        
        // String 배열
        IntFunction<String[]> arrayFactory1 = size -> new String[size];
        IntFunction<String[]> arrayFactory2 = String[]::new;
        
        String[] arr1 = arrayFactory2.apply(5);
        System.out.println("배열 크기: " + arr1.length);
        
        
        // Stream toArray
        System.out.println("\n=== Stream toArray ===");
        List<String> list = Arrays.asList("A", "B", "C", "D", "E");
        
        // 배열로 변환 (람다)
        String[] arr2 = list.stream().toArray(size -> new String[size]);
        System.out.println("람다: " + Arrays.toString(arr2));
        
        // 배열로 변환 (메서드 참조)
        String[] arr3 = list.stream().toArray(String[]::new);
        System.out.println("참조: " + Arrays.toString(arr3));
        
        
        // Integer 배열
        System.out.println("\n=== Integer 배열 ===");
        Integer[] numbers = IntStream.range(1, 6)
            .boxed()
            .toArray(Integer[]::new);
        System.out.println(Arrays.toString(numbers));
    }
}
```

### 6.2 커스텀 타입 배열

```java
import java.util.*;
import java.util.stream.*;

public class CustomArrayReference {
    static class Person {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Alice", 25),
            new Person("Bob", 30),
            new Person("Charlie", 20)
        );
        
        // Person 배열로 변환
        Person[] array = people.stream()
            .toArray(Person[]::new);
        
        System.out.println("배열: " + Arrays.toString(array));
        System.out.println("크기: " + array.length);
    }
}
```

---

## 7. 메서드 참조 활용

### 7.1 Comparator

```java
import java.util.*;

public class MethodReferenceComparator {
    static class Person {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        String getName() { return name; }
        int getAge() { return age; }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Alice", 25),
            new Person("Bob", 20),
            new Person("Charlie", 30)
        );
        
        System.out.println("원본: " + people);
        
        
        // 이름으로 정렬
        System.out.println("\n=== 이름 정렬 ===");
        people.sort(Comparator.comparing(Person::getName));
        System.out.println(people);
        
        
        // 나이로 정렬
        System.out.println("\n=== 나이 정렬 ===");
        people.sort(Comparator.comparing(Person::getAge));
        System.out.println(people);
        
        
        // 역순
        System.out.println("\n=== 나이 역순 ===");
        people.sort(Comparator.comparing(Person::getAge).reversed());
        System.out.println(people);
        
        
        // 복합 정렬
        System.out.println("\n=== 복합 정렬 ===");
        people.sort(Comparator
            .comparing(Person::getAge)
            .thenComparing(Person::getName));
        System.out.println(people);
    }
}
```

### 7.2 Map 연산

```java
import java.util.*;
import java.util.stream.*;

public class MethodReferenceMap {
    public static void main(String[] args) {
        Map<String, Integer> scores = new HashMap<>();
        scores.put("Alice", 90);
        scores.put("Bob", 85);
        scores.put("Charlie", 95);
        
        
        // forEach
        System.out.println("=== forEach ===");
        scores.forEach((name, score) -> 
            System.out.println(name + ": " + score));
        
        
        // computeIfAbsent
        System.out.println("\n=== computeIfAbsent ===");
        scores.computeIfAbsent("David", k -> 80);
        System.out.println("David 추가: " + scores.get("David"));
        
        
        // merge
        System.out.println("\n=== merge ===");
        scores.merge("Alice", 10, Integer::sum);
        System.out.println("Alice 증가: " + scores.get("Alice"));
        
        
        // replaceAll
        System.out.println("\n=== replaceAll (10% 보너스) ===");
        scores.replaceAll((name, score) -> (int) (score * 1.1));
        scores.forEach((name, score) -> 
            System.out.println(name + ": " + score));
    }
}
```

---

## 8. 실전 예제

### 8.1 데이터 처리 파이프라인

```java
import java.util.*;
import java.util.stream.*;

public class DataProcessingPipeline {
    static class Product {
        String name;
        double price;
        String category;
        
        Product(String name, double price, String category) {
            this.name = name;
            this.price = price;
            this.category = category;
        }
        
        String getName() { return name; }
        double getPrice() { return price; }
        String getCategory() { return category; }
        
        @Override
        public String toString() {
            return name + "($" + price + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Product> products = Arrays.asList(
            new Product("Laptop", 1200.0, "Electronics"),
            new Product("Mouse", 25.0, "Electronics"),
            new Product("Desk", 300.0, "Furniture"),
            new Product("Chair", 150.0, "Furniture"),
            new Product("Monitor", 400.0, "Electronics")
        );
        
        // 카테고리별 평균 가격
        System.out.println("=== 카테고리별 평균 ===");
        Map<String, Double> avgPrices = products.stream()
            .collect(Collectors.groupingBy(
                Product::getCategory,
                Collectors.averagingDouble(Product::getPrice)
            ));
        avgPrices.forEach((cat, avg) -> 
            System.out.printf("%s: $%.2f%n", cat, avg));
        
        
        // 가격 순 정렬
        System.out.println("\n=== 가격 순 ===");
        products.stream()
            .sorted(Comparator.comparing(Product::getPrice))
            .forEach(System.out::println);
        
        
        // 전자제품만 필터링
        System.out.println("\n=== 전자제품 ===");
        products.stream()
            .filter(p -> p.getCategory().equals("Electronics"))
            .map(Product::getName)
            .forEach(System.out::println);
    }
}
```

### 8.2 함수 조합

```java
import java.util.*;
import java.util.function.*;

public class FunctionComposition {
    static class TextProcessor {
        static String trim(String s) {
            return s.trim();
        }
        
        static String removeSpaces(String s) {
            return s.replaceAll("\\s+", "");
        }
        
        static String toLowerCase(String s) {
            return s.toLowerCase();
        }
    }
    
    public static void main(String[] args) {
        // 함수 조합
        Function<String, String> pipeline = 
            ((Function<String, String>) TextProcessor::trim)
                .andThen(TextProcessor::toLowerCase)
                .andThen(TextProcessor::removeSpaces);
        
        String input = "  Hello World  ";
        String result = pipeline.apply(input);
        
        System.out.println("입력: '" + input + "'");
        System.out.println("출력: '" + result + "'");
        
        
        // Predicate 조합
        System.out.println("\n=== Predicate 조합 ===");
        List<String> words = Arrays.asList(
            "apple", "Banana", "CHERRY", "date"
        );
        
        Predicate<String> hasUpperCase = s -> !s.equals(s.toLowerCase());
        Predicate<String> isLongWord = s -> s.length() > 5;
        
        words.stream()
            .filter(hasUpperCase.or(isLongWord))
            .forEach(System.out::println);
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 메서드 참조 변환

```java
// 람다를 메서드 참조로 변환
import java.util.*;
import java.util.stream.*;

public class Problem1 {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("alice", "bob", "charlie");
        
        // 1. 대문자 변환
        List<String> upper = names.stream()
            .map(s -> s.toUpperCase())  // 메서드 참조로 변환
            .collect(Collectors.toList());
        
        // 2. 길이 추출
        List<Integer> lengths = names.stream()
            .map(s -> s.length())  // 메서드 참조로 변환
            .collect(Collectors.toList());
        
        // 3. 출력
        names.forEach(name -> System.out.println(name));  // 메서드 참조로 변환
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
// 1. 대문자 변환
.map(String::toUpperCase)

// 2. 길이 추출
.map(String::length)

// 3. 출력
names.forEach(System.out::println);
```
</details>

---

### 문제 2: 생성자 참조

```java
// 생성자 참조 사용
import java.util.*;
import java.util.stream.*;

class User {
    String name;
    User(String name) { this.name = name; }
    @Override
    public String toString() { return name; }
}

public class Problem2 {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie");
        
        // User 객체 리스트 생성 (생성자 참조 사용)
        List<User> users = names.stream()
            .map(name -> new User(name))  // 생성자 참조로 변환
            .collect(Collectors.toList());
        
        System.out.println(users);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
List<User> users = names.stream()
    .map(User::new)
    .collect(Collectors.toList());
```
</details>

---

### 문제 3: Comparator

```java
// 메서드 참조로 정렬
import java.util.*;

class Person {
    String name;
    int age;
    
    Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    String getName() { return name; }
    int getAge() { return age; }
    
    @Override
    public String toString() {
        return name + "(" + age + ")";
    }
}

public class Problem3 {
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Charlie", 30),
            new Person("Alice", 25),
            new Person("Bob", 25)
        );
        
        // 나이순, 같으면 이름순 정렬 (메서드 참조 사용)
        people.sort(/* 여기 */);
        
        System.out.println(people);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
people.sort(Comparator
    .comparing(Person::getAge)
    .thenComparing(Person::getName));
```
</details>

---

## 📌 핵심 정리

### 메서드 참조 종류
```java
// 정적 메서드
Integer::parseInt           // (String) -> Integer
Math::sqrt                  // (double) -> double

// 인스턴스 메서드 (클래스)
String::length              // (String) -> int
String::toUpperCase         // (String) -> String

// 인스턴스 메서드 (특정 객체)
System.out::println         // (T) -> void
list::add                   // (T) -> boolean

// 생성자
ArrayList::new              // () -> ArrayList
String::new                 // () -> String
String[]::new               // (int) -> String[]
```

### 변환 규칙
```java
// 람다 → 메서드 참조
x -> System.out.println(x)  →  System.out::println
x -> x.length()             →  String::length
x -> new User(x)            →  User::new
```

### 사용 시점
```
✅ 람다가 메서드만 호출
✅ 매개변수를 그대로 전달
❌ 추가 연산이 필요
❌ 매개변수 가공 필요
```

### 실전 활용
```java
// Stream
.map(String::toUpperCase)
.filter(Objects::nonNull)
.forEach(System.out::println)

// Comparator
Comparator.comparing(Person::getAge)

// 생성자
.collect(Collectors.toCollection(ArrayList::new))
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Functional Interface](./Lambda-01-FunctionalInterface.md) | [다음: 03. Custom Functional Interface →](./Lambda-03-Custom.md)**

</div>
