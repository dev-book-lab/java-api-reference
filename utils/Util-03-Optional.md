# Util 03. Optional 완전 정복

> null 안전 프로그래밍 - Optional 마스터하기  
> Java API Reference

---

## 📑 목차

1. [Optional 기본 개념](#1-optional-기본-개념)
2. [Optional 생성](#2-optional-생성)
3. [값 확인과 추출](#3-값-확인과-추출)
4. [값 변환](#4-값-변환)
5. [필터링](#5-필터링)
6. [기본값 처리](#6-기본값-처리)
7. [Optional 체이닝](#7-optional-체이닝)
8. [실전 활용](#8-실전-활용)
9. [안티패턴과 Best Practice](#9-안티패턴과-best-practice)
10. [실전 연습 문제](#10-실전-연습-문제)

---

## 1. Optional 기본 개념

### 1.1 Optional이란?

```java
import java.util.*;

public class OptionalIntro {
    public static void main(String[] args) {
        System.out.println("=== Optional이란? ===\n");
        
        System.out.println("1. null을 다루는 컨테이너");
        System.out.println("   - 값이 있을 수도, 없을 수도");
        System.out.println("   - NullPointerException 방지\n");
        
        System.out.println("2. Java 8 도입");
        System.out.println("   - 함수형 프로그래밍");
        System.out.println("   - Stream API와 연계\n");
        
        System.out.println("3. 명시적 처리");
        System.out.println("   - null 체크 강제");
        System.out.println("   - 의도 명확화");
    }
}
```

### 1.2 null 처리 비교

```java
public class NullHandlingComparison {
    static class User {
        String name;
        String email;
        
        User(String name, String email) {
            this.name = name;
            this.email = email;
        }
    }
    
    // ❌ 전통적 null 처리
    public static String getEmailOld(User user) {
        if (user != null) {
            String email = user.email;
            if (email != null) {
                return email.toUpperCase();
            }
        }
        return "NO EMAIL";
    }
    
    // ✅ Optional 사용
    public static String getEmailNew(Optional<User> user) {
        return user
            .map(u -> u.email)
            .map(String::toUpperCase)
            .orElse("NO EMAIL");
    }
    
    public static void main(String[] args) {
        User user1 = new User("Alice", "alice@example.com");
        User user2 = new User("Bob", null);
        User user3 = null;
        
        System.out.println("=== 전통적 방식 ===");
        System.out.println(getEmailOld(user1));
        System.out.println(getEmailOld(user2));
        System.out.println(getEmailOld(user3));
        
        System.out.println("\n=== Optional 방식 ===");
        System.out.println(getEmailNew(Optional.ofNullable(user1)));
        System.out.println(getEmailNew(Optional.ofNullable(user2)));
        System.out.println(getEmailNew(Optional.ofNullable(user3)));
    }
}
```

### 1.3 Optional의 장점

```java
public class OptionalAdvantages {
    public static void main(String[] args) {
        System.out.println("=== Optional의 장점 ===\n");
        
        System.out.println("1. NullPointerException 방지");
        System.out.println("   - 명시적 null 처리");
        System.out.println("   - 컴파일 타임 체크\n");
        
        System.out.println("2. API 명확성");
        System.out.println("   - 반환값이 없을 수 있음 표현");
        System.out.println("   - 문서화 효과\n");
        
        System.out.println("3. 함수형 스타일");
        System.out.println("   - map, flatMap, filter");
        System.out.println("   - Stream과 일관성\n");
        
        System.out.println("4. 기본값 처리");
        System.out.println("   - orElse, orElseGet");
        System.out.println("   - 우아한 fallback");
    }
}
```

---

## 2. Optional 생성

### 2.1 of / ofNullable / empty

```java
public class OptionalCreation {
    public static void main(String[] args) {
        // Optional.of (null 불가)
        Optional<String> opt1 = Optional.of("Hello");
        System.out.println("of: " + opt1);
        
        try {
            Optional<String> opt2 = Optional.of(null);  // NPE!
        } catch (NullPointerException e) {
            System.out.println("of(null): NullPointerException!");
        }
        
        
        // Optional.ofNullable (null 가능)
        Optional<String> opt3 = Optional.ofNullable("Hello");
        System.out.println("\nofNullable(value): " + opt3);
        
        Optional<String> opt4 = Optional.ofNullable(null);
        System.out.println("ofNullable(null): " + opt4);
        
        
        // Optional.empty (빈 Optional)
        Optional<String> opt5 = Optional.empty();
        System.out.println("\nempty: " + opt5);
        
        
        System.out.println("\n=== 생성 메서드 정리 ===");
        System.out.println("of(value):          값이 확실히 있을 때");
        System.out.println("ofNullable(value):  null 가능성 있을 때 (권장)");
        System.out.println("empty():            명시적으로 빈 Optional");
    }
}
```

### 2.2 실전 예제

```java
public class OptionalCreationExample {
    static class User {
        String name;
        String email;
        
        User(String name, String email) {
            this.name = name;
            this.email = email;
        }
    }
    
    // DB에서 사용자 조회 (없을 수 있음)
    public static Optional<User> findUserById(int id) {
        if (id == 1) {
            return Optional.of(new User("Alice", "alice@example.com"));
        } else if (id == 2) {
            return Optional.of(new User("Bob", null));
        }
        return Optional.empty();
    }
    
    // 사용자 이메일 조회
    public static Optional<String> getEmailById(int id) {
        return findUserById(id)
            .map(user -> user.email);  // null이면 empty 반환
    }
    
    public static void main(String[] args) {
        System.out.println("User 1: " + findUserById(1));
        System.out.println("User 2: " + findUserById(2));
        System.out.println("User 3: " + findUserById(3));
        
        System.out.println("\nEmail 1: " + getEmailById(1));
        System.out.println("Email 2: " + getEmailById(2));
        System.out.println("Email 3: " + getEmailById(3));
    }
}
```

---

## 3. 값 확인과 추출

### 3.1 isPresent / isEmpty

```java
public class OptionalCheck {
    public static void main(String[] args) {
        Optional<String> opt1 = Optional.of("Hello");
        Optional<String> opt2 = Optional.empty();
        
        // isPresent (값이 있는지)
        System.out.println("opt1.isPresent(): " + opt1.isPresent());  // true
        System.out.println("opt2.isPresent(): " + opt2.isPresent());  // false
        
        
        // isEmpty (Java 11+)
        System.out.println("\nopt1.isEmpty(): " + opt1.isEmpty());  // false
        System.out.println("opt2.isEmpty(): " + opt2.isEmpty());  // true
        
        
        // 전통적 사용 (권장하지 않음)
        if (opt1.isPresent()) {
            System.out.println("\n값: " + opt1.get());
        }
    }
}
```

### 3.2 get

```java
public class OptionalGet {
    public static void main(String[] args) {
        Optional<String> opt1 = Optional.of("Hello");
        Optional<String> opt2 = Optional.empty();
        
        // get (값이 있을 때만)
        String value1 = opt1.get();
        System.out.println("opt1.get(): " + value1);
        
        
        // get (빈 Optional) - NoSuchElementException!
        try {
            String value2 = opt2.get();
        } catch (NoSuchElementException e) {
            System.out.println("opt2.get(): NoSuchElementException!");
        }
        
        
        System.out.println("\n=== get() 주의사항 ===");
        System.out.println("❌ get()만 사용: 위험! (예외 발생 가능)");
        System.out.println("✅ orElse 계열 사용: 안전");
    }
}
```

### 3.3 ifPresent / ifPresentOrElse

```java
public class OptionalIfPresent {
    public static void main(String[] args) {
        Optional<String> opt1 = Optional.of("Hello");
        Optional<String> opt2 = Optional.empty();
        
        // ifPresent (값이 있으면 실행)
        System.out.println("=== ifPresent ===");
        opt1.ifPresent(value -> System.out.println("값: " + value));
        opt2.ifPresent(value -> System.out.println("값: " + value));  // 실행 안 됨
        
        
        // ifPresentOrElse (Java 9+)
        System.out.println("\n=== ifPresentOrElse ===");
        opt1.ifPresentOrElse(
            value -> System.out.println("값 있음: " + value),
            () -> System.out.println("값 없음")
        );
        
        opt2.ifPresentOrElse(
            value -> System.out.println("값 있음: " + value),
            () -> System.out.println("값 없음")
        );
    }
}
```

---

## 4. 값 변환

### 4.1 map

```java
public class OptionalMap {
    public static void main(String[] args) {
        Optional<String> opt = Optional.of("hello");
        
        // 대문자 변환
        Optional<String> upper = opt.map(String::toUpperCase);
        System.out.println("대문자: " + upper);
        
        
        // 길이 변환
        Optional<Integer> length = opt.map(String::length);
        System.out.println("길이: " + length);
        
        
        // 빈 Optional
        Optional<String> empty = Optional.empty();
        Optional<String> result = empty.map(String::toUpperCase);
        System.out.println("빈 Optional map: " + result);  // empty
        
        
        // 체이닝
        String finalResult = Optional.of("  hello  ")
            .map(String::trim)
            .map(String::toUpperCase)
            .orElse("DEFAULT");
        System.out.println("\n체이닝 결과: " + finalResult);
    }
}
```

### 4.2 flatMap

```java
public class OptionalFlatMap {
    static class User {
        String name;
        Optional<String> email;
        
        User(String name, String email) {
            this.name = name;
            this.email = Optional.ofNullable(email);
        }
        
        Optional<String> getEmail() {
            return email;
        }
    }
    
    public static void main(String[] args) {
        Optional<User> user1 = Optional.of(new User("Alice", "alice@example.com"));
        Optional<User> user2 = Optional.of(new User("Bob", null));
        Optional<User> user3 = Optional.empty();
        
        // map 사용 시 (중첩 Optional)
        Optional<Optional<String>> nested = user1.map(User::getEmail);
        System.out.println("map 사용: " + nested);  // Optional[Optional[alice@...]]
        
        
        // flatMap 사용 (평탄화)
        Optional<String> email1 = user1.flatMap(User::getEmail);
        System.out.println("\nflatMap 사용: " + email1);
        
        Optional<String> email2 = user2.flatMap(User::getEmail);
        System.out.println("flatMap (null): " + email2);
        
        Optional<String> email3 = user3.flatMap(User::getEmail);
        System.out.println("flatMap (empty): " + email3);
    }
}
```

### 4.3 map vs flatMap

```java
public class MapVsFlatMap {
    public static void main(String[] args) {
        System.out.println("=== map vs flatMap ===\n");
        
        System.out.println("map:");
        System.out.println("- Function<T, R>");
        System.out.println("- 일반 값 반환");
        System.out.println("- Optional<R> 생성\n");
        
        System.out.println("flatMap:");
        System.out.println("- Function<T, Optional<R>>");
        System.out.println("- Optional 반환");
        System.out.println("- 중첩 방지\n");
        
        // 예제
        Optional<String> opt = Optional.of("hello");
        
        // map: String -> Integer
        Optional<Integer> mapped = opt.map(String::length);
        System.out.println("map 결과: " + mapped);
        
        // flatMap: String -> Optional<Integer>
        Optional<Integer> flatMapped = opt.flatMap(s -> 
            s.isEmpty() ? Optional.empty() : Optional.of(s.length())
        );
        System.out.println("flatMap 결과: " + flatMapped);
    }
}
```

---

## 5. 필터링

### 5.1 filter

```java
public class OptionalFilter {
    public static void main(String[] args) {
        Optional<Integer> opt1 = Optional.of(10);
        Optional<Integer> opt2 = Optional.of(3);
        Optional<Integer> opt3 = Optional.empty();
        
        // 10 이상 필터
        Optional<Integer> result1 = opt1.filter(n -> n >= 10);
        System.out.println("10 이상 (10): " + result1);  // Optional[10]
        
        Optional<Integer> result2 = opt2.filter(n -> n >= 10);
        System.out.println("10 이상 (3): " + result2);   // Optional.empty
        
        Optional<Integer> result3 = opt3.filter(n -> n >= 10);
        System.out.println("10 이상 (empty): " + result3);  // Optional.empty
        
        
        // 짝수 필터
        System.out.println("\n짝수 필터:");
        Optional.of(4).filter(n -> n % 2 == 0)
            .ifPresent(n -> System.out.println(n + "은 짝수"));
        
        Optional.of(5).filter(n -> n % 2 == 0)
            .ifPresent(n -> System.out.println(n + "은 짝수"));  // 실행 안 됨
    }
}
```

### 5.2 실전 예제

```java
public class OptionalFilterExample {
    static class User {
        String name;
        int age;
        
        User(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + "세)";
        }
    }
    
    // 성인만 조회
    public static Optional<User> getAdult(Optional<User> user) {
        return user.filter(u -> u.age >= 19);
    }
    
    // 특정 나이대 조회
    public static Optional<User> getAgeRange(Optional<User> user, int min, int max) {
        return user.filter(u -> u.age >= min && u.age <= max);
    }
    
    public static void main(String[] args) {
        Optional<User> user1 = Optional.of(new User("Alice", 25));
        Optional<User> user2 = Optional.of(new User("Bob", 17));
        
        System.out.println("=== 성인 필터 ===");
        System.out.println("Alice: " + getAdult(user1));
        System.out.println("Bob: " + getAdult(user2));
        
        System.out.println("\n=== 20대 필터 ===");
        System.out.println("Alice: " + getAgeRange(user1, 20, 29));
        System.out.println("Bob: " + getAgeRange(user2, 20, 29));
    }
}
```

---

## 6. 기본값 처리

### 6.1 orElse

```java
public class OptionalOrElse {
    public static void main(String[] args) {
        Optional<String> opt1 = Optional.of("Hello");
        Optional<String> opt2 = Optional.empty();
        
        // orElse (기본값)
        String result1 = opt1.orElse("Default");
        System.out.println("값 있음: " + result1);  // Hello
        
        String result2 = opt2.orElse("Default");
        System.out.println("값 없음: " + result2);  // Default
        
        
        // 주의: orElse는 항상 실행!
        System.out.println("\n=== orElse 실행 확인 ===");
        String result3 = opt1.orElse(getDefaultValue());
        // "getDefaultValue 호출!" 출력됨
    }
    
    static String getDefaultValue() {
        System.out.println("getDefaultValue 호출!");
        return "Default";
    }
}
```

### 6.2 orElseGet

```java
public class OptionalOrElseGet {
    public static void main(String[] args) {
        Optional<String> opt1 = Optional.of("Hello");
        Optional<String> opt2 = Optional.empty();
        
        // orElseGet (Supplier)
        String result1 = opt1.orElseGet(() -> "Default");
        System.out.println("값 있음: " + result1);  // Hello
        
        String result2 = opt2.orElseGet(() -> "Default");
        System.out.println("값 없음: " + result2);  // Default
        
        
        // orElseGet은 필요할 때만 실행
        System.out.println("\n=== orElseGet 실행 확인 ===");
        String result3 = opt1.orElseGet(() -> getDefaultValue());
        // "getDefaultValue 호출!" 출력 안 됨!
        
        System.out.println("\n빈 Optional:");
        String result4 = opt2.orElseGet(() -> getDefaultValue());
        // "getDefaultValue 호출!" 출력됨
    }
    
    static String getDefaultValue() {
        System.out.println("getDefaultValue 호출!");
        return "Default";
    }
}
```

### 6.3 orElse vs orElseGet

```java
public class OrElseVsOrElseGet {
    public static void main(String[] args) {
        System.out.println("=== orElse vs orElseGet ===\n");
        
        System.out.println("orElse:");
        System.out.println("- 값 즉시 평가");
        System.out.println("- 항상 실행");
        System.out.println("- 간단한 값\n");
        
        System.out.println("orElseGet:");
        System.out.println("- 지연 평가 (Lazy)");
        System.out.println("- 필요할 때만 실행");
        System.out.println("- 복잡한 계산, 비용 큰 연산\n");
        
        
        // 성능 비교
        Optional<String> opt = Optional.of("Hello");
        
        long start = System.nanoTime();
        for (int i = 0; i < 1000000; i++) {
            opt.orElse(expensiveOperation());
        }
        long time1 = System.nanoTime() - start;
        
        start = System.nanoTime();
        for (int i = 0; i < 1000000; i++) {
            opt.orElseGet(() -> expensiveOperation());
        }
        long time2 = System.nanoTime() - start;
        
        System.out.println("orElse: " + time1 / 1000000 + "ms");
        System.out.println("orElseGet: " + time2 / 1000000 + "ms");
    }
    
    static String expensiveOperation() {
        return "Expensive";
    }
}
```

### 6.4 orElseThrow

```java
public class OptionalOrElseThrow {
    public static void main(String[] args) {
        Optional<String> opt1 = Optional.of("Hello");
        Optional<String> opt2 = Optional.empty();
        
        // orElseThrow (기본 예외)
        String result1 = opt1.orElseThrow();
        System.out.println("값 있음: " + result1);
        
        try {
            String result2 = opt2.orElseThrow();
        } catch (NoSuchElementException e) {
            System.out.println("값 없음: NoSuchElementException");
        }
        
        
        // orElseThrow (커스텀 예외)
        try {
            String result3 = opt2.orElseThrow(
                () -> new IllegalArgumentException("값이 없습니다!")
            );
        } catch (IllegalArgumentException e) {
            System.out.println("커스텀 예외: " + e.getMessage());
        }
    }
}
```

---

## 7. Optional 체이닝

### 7.1 복합 체이닝

```java
public class OptionalChaining {
    static class Address {
        String city;
        String zipCode;
        
        Address(String city, String zipCode) {
            this.city = city;
            this.zipCode = zipCode;
        }
    }
    
    static class User {
        String name;
        Address address;
        
        User(String name, Address address) {
            this.name = name;
            this.address = address;
        }
        
        Optional<Address> getAddress() {
            return Optional.ofNullable(address);
        }
    }
    
    // 전통적 null 체크
    public static String getCityOld(User user) {
        if (user != null) {
            Address address = user.address;
            if (address != null) {
                String city = address.city;
                if (city != null) {
                    return city.toUpperCase();
                }
            }
        }
        return "UNKNOWN";
    }
    
    // Optional 체이닝
    public static String getCityNew(Optional<User> user) {
        return user
            .flatMap(User::getAddress)
            .map(a -> a.city)
            .map(String::toUpperCase)
            .orElse("UNKNOWN");
    }
    
    public static void main(String[] args) {
        User user1 = new User("Alice", new Address("Seoul", "12345"));
        User user2 = new User("Bob", new Address(null, "67890"));
        User user3 = new User("Charlie", null);
        
        System.out.println("=== 전통적 방식 ===");
        System.out.println(getCityOld(user1));
        System.out.println(getCityOld(user2));
        System.out.println(getCityOld(user3));
        
        System.out.println("\n=== Optional 체이닝 ===");
        System.out.println(getCityNew(Optional.of(user1)));
        System.out.println(getCityNew(Optional.of(user2)));
        System.out.println(getCityNew(Optional.of(user3)));
    }
}
```

### 7.2 복잡한 체이닝

```java
public class ComplexChaining {
    static class Company {
        String name;
        CEO ceo;
        
        Company(String name, CEO ceo) {
            this.name = name;
            this.ceo = ceo;
        }
        
        Optional<CEO> getCEO() {
            return Optional.ofNullable(ceo);
        }
    }
    
    static class CEO {
        String name;
        int age;
        Car car;
        
        CEO(String name, int age, Car car) {
            this.name = name;
            this.age = age;
            this.car = car;
        }
        
        Optional<Car> getCar() {
            return Optional.ofNullable(car);
        }
    }
    
    static class Car {
        String model;
        Insurance insurance;
        
        Car(String model, Insurance insurance) {
            this.model = model;
            this.insurance = insurance;
        }
        
        Optional<Insurance> getInsurance() {
            return Optional.ofNullable(insurance);
        }
    }
    
    static class Insurance {
        String name;
        
        Insurance(String name) {
            this.name = name;
        }
    }
    
    // CEO 차량 보험사 조회
    public static String getInsuranceName(Optional<Company> company) {
        return company
            .flatMap(Company::getCEO)
            .filter(ceo -> ceo.age >= 30)  // 30세 이상만
            .flatMap(CEO::getCar)
            .flatMap(Car::getInsurance)
            .map(insurance -> insurance.name)
            .orElse("NO INSURANCE");
    }
    
    public static void main(String[] args) {
        Company c1 = new Company("TechCorp",
            new CEO("Alice", 35,
                new Car("Tesla",
                    new Insurance("SafeInsurance"))));
        
        Company c2 = new Company("StartUp",
            new CEO("Bob", 25,
                new Car("BMW", null)));
        
        Company c3 = new Company("BigCorp", null);
        
        System.out.println("c1 보험: " + getInsuranceName(Optional.of(c1)));
        System.out.println("c2 보험: " + getInsuranceName(Optional.of(c2)));
        System.out.println("c3 보험: " + getInsuranceName(Optional.of(c3)));
    }
}
```

---

## 8. 실전 활용

### 8.1 Repository 패턴

```java
public class RepositoryPattern {
    static class User {
        int id;
        String name;
        
        User(int id, String name) {
            this.id = id;
            this.name = name;
        }
        
        @Override
        public String toString() {
            return "User(" + id + ", " + name + ")";
        }
    }
    
    static class UserRepository {
        private List<User> users = Arrays.asList(
            new User(1, "Alice"),
            new User(2, "Bob"),
            new User(3, "Charlie")
        );
        
        // Optional 반환
        public Optional<User> findById(int id) {
            return users.stream()
                .filter(u -> u.id == id)
                .findFirst();
        }
        
        public Optional<User> findByName(String name) {
            return users.stream()
                .filter(u -> u.name.equals(name))
                .findFirst();
        }
    }
    
    public static void main(String[] args) {
        UserRepository repo = new UserRepository();
        
        // 존재하는 사용자
        repo.findById(1)
            .ifPresentOrElse(
                user -> System.out.println("찾음: " + user),
                () -> System.out.println("없음")
            );
        
        // 존재하지 않는 사용자
        User user = repo.findById(99)
            .orElse(new User(0, "Guest"));
        System.out.println("결과: " + user);
        
        // 이름으로 조회 후 ID 출력
        repo.findByName("Bob")
            .map(u -> u.id)
            .ifPresent(id -> System.out.println("Bob의 ID: " + id));
    }
}
```

### 8.2 설정값 처리

```java
public class ConfigurationHandling {
    static class Config {
        Map<String, String> properties = new HashMap<>();
        
        Config() {
            properties.put("db.host", "localhost");
            properties.put("db.port", "3306");
        }
        
        public Optional<String> get(String key) {
            return Optional.ofNullable(properties.get(key));
        }
        
        public int getInt(String key, int defaultValue) {
            return get(key)
                .map(Integer::parseInt)
                .orElse(defaultValue);
        }
        
        public boolean getBoolean(String key, boolean defaultValue) {
            return get(key)
                .map(Boolean::parseBoolean)
                .orElse(defaultValue);
        }
    }
    
    public static void main(String[] args) {
        Config config = new Config();
        
        // 문자열 설정
        String host = config.get("db.host")
            .orElse("127.0.0.1");
        System.out.println("DB Host: " + host);
        
        // 숫자 설정
        int port = config.getInt("db.port", 5432);
        System.out.println("DB Port: " + port);
        
        // 없는 설정
        String user = config.get("db.user")
            .orElse("root");
        System.out.println("DB User: " + user);
        
        // boolean 설정
        boolean ssl = config.getBoolean("db.ssl", false);
        System.out.println("SSL: " + ssl);
    }
}
```

### 8.3 캐시 구현

```java
public class CacheImplementation {
    static class Cache<K, V> {
        private Map<K, V> cache = new HashMap<>();
        
        public Optional<V> get(K key) {
            return Optional.ofNullable(cache.get(key));
        }
        
        public void put(K key, V value) {
            cache.put(key, value);
        }
        
        public V getOrCompute(K key, java.util.function.Supplier<V> supplier) {
            return get(key).orElseGet(() -> {
                V value = supplier.get();
                put(key, value);
                return value;
            });
        }
    }
    
    public static void main(String[] args) {
        Cache<String, String> cache = new Cache<>();
        
        // 캐시에 없으면 계산
        String result1 = cache.getOrCompute("user:1", () -> {
            System.out.println("DB 조회...");
            return "Alice";
        });
        System.out.println("결과: " + result1);
        
        // 캐시에 있으면 바로 반환
        String result2 = cache.getOrCompute("user:1", () -> {
            System.out.println("DB 조회...");  // 실행 안 됨
            return "Alice";
        });
        System.out.println("결과: " + result2);
    }
}
```

---

## 9. 안티패턴과 Best Practice

### 9.1 안티패턴

```java
public class OptionalAntiPatterns {
    public static void main(String[] args) {
        Optional<String> opt = Optional.of("Hello");
        
        System.out.println("=== 안티패턴 ===\n");
        
        // ❌ 1. isPresent + get
        System.out.println("❌ isPresent + get:");
        if (opt.isPresent()) {
            System.out.println(opt.get());
        }
        System.out.println("✅ 대신: ifPresent, orElse 사용\n");
        
        
        // ❌ 2. Optional을 필드로
        System.out.println("❌ Optional 필드:");
        System.out.println("class User {");
        System.out.println("    Optional<String> email;  // NO!");
        System.out.println("}");
        System.out.println("✅ 대신: 메서드 반환값으로만\n");
        
        
        // ❌ 3. Optional을 파라미터로
        System.out.println("❌ Optional 파라미터:");
        System.out.println("void method(Optional<String> param) { }  // NO!");
        System.out.println("✅ 대신: null 허용 파라미터 or 오버로딩\n");
        
        
        // ❌ 4. Optional.of(null)
        System.out.println("❌ Optional.of(null):");
        System.out.println("✅ 대신: Optional.ofNullable()\n");
        
        
        // ❌ 5. Optional을 컬렉션에
        System.out.println("❌ Optional 컬렉션:");
        System.out.println("List<Optional<String>> list;  // NO!");
        System.out.println("✅ 대신: null 아닌 값만 추가");
    }
}
```

### 9.2 Best Practice

```java
public class OptionalBestPractices {
    static class User {
        String name;
        String email;
        
        User(String name, String email) {
            this.name = name;
            this.email = email;
        }
        
        // ✅ Optional 반환
        public Optional<String> getEmail() {
            return Optional.ofNullable(email);
        }
    }
    
    // ✅ 메서드 반환값
    public static Optional<User> findUser(int id) {
        if (id == 1) {
            return Optional.of(new User("Alice", "alice@example.com"));
        }
        return Optional.empty();
    }
    
    // ✅ orElseGet 사용 (비용 큰 연산)
    public static String getUsername(Optional<User> user) {
        return user
            .map(u -> u.name)
            .orElseGet(() -> fetchDefaultName());
    }
    
    static String fetchDefaultName() {
        System.out.println("기본 이름 조회...");
        return "Guest";
    }
    
    // ✅ Stream과 함께
    public static void printAllEmails(List<User> users) {
        users.stream()
            .map(User::getEmail)
            .flatMap(Optional::stream)  // Java 9+
            .forEach(System.out::println);
    }
    
    public static void main(String[] args) {
        System.out.println("=== Best Practices ===\n");
        
        // 1. 메서드 반환값
        Optional<User> user = findUser(1);
        System.out.println("사용자: " + user);
        
        // 2. orElseGet
        System.out.println("\norElseGet:");
        System.out.println(getUsername(Optional.of(new User("Alice", null))));
        System.out.println(getUsername(Optional.empty()));
        
        // 3. Stream과 함께
        System.out.println("\nStream:");
        List<User> users = Arrays.asList(
            new User("Alice", "alice@example.com"),
            new User("Bob", null),
            new User("Charlie", "charlie@example.com")
        );
        printAllEmails(users);
    }
}
```

### 9.3 언제 사용할까?

```java
public class WhenToUseOptional {
    public static void main(String[] args) {
        System.out.println("=== Optional 사용 시점 ===\n");
        
        System.out.println("✅ 사용해야 할 때:");
        System.out.println("1. 메서드 반환값");
        System.out.println("   - 값이 없을 수 있음을 명시");
        System.out.println("2. Stream 종료 연산");
        System.out.println("   - findFirst, findAny");
        System.out.println("3. 복잡한 null 체크");
        System.out.println("   - 체이닝 필요\n");
        
        System.out.println("❌ 사용하지 말아야 할 때:");
        System.out.println("1. 필드");
        System.out.println("2. 파라미터");
        System.out.println("3. 컬렉션 원소");
        System.out.println("4. 배열");
        System.out.println("5. 기본형 (OptionalInt 사용)");
    }
}
```

---

## 10. 실전 연습 문제

### 문제 1: 사용자 정보 추출

```java
// 사용자의 이메일 도메인 추출
public class Problem1 {
    static class User {
        String name;
        String email;
        
        User(String name, String email) {
            this.name = name;
            this.email = email;
        }
    }
    
    // email에서 도메인 추출 (@ 뒤 부분)
    public static Optional<String> getEmailDomain(Optional<User> user) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        Optional<User> user1 = Optional.of(new User("Alice", "alice@example.com"));
        Optional<User> user2 = Optional.of(new User("Bob", null));
        Optional<User> user3 = Optional.empty();
        
        System.out.println(getEmailDomain(user1));  // Optional[example.com]
        System.out.println(getEmailDomain(user2));  // Optional.empty
        System.out.println(getEmailDomain(user3));  // Optional.empty
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static Optional<String> getEmailDomain(Optional<User> user) {
    return user
        .map(u -> u.email)
        .filter(email -> email.contains("@"))
        .map(email -> email.substring(email.indexOf("@") + 1));
}
```
</details>

---

### 문제 2: 최대값 찾기

```java
// 리스트에서 최대값을 Optional로 반환
public class Problem2 {
    public static Optional<Integer> findMax(List<Integer> numbers) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        List<Integer> list1 = Arrays.asList(1, 5, 3, 9, 2);
        List<Integer> list2 = Arrays.asList();
        
        System.out.println(findMax(list1));  // Optional[9]
        System.out.println(findMax(list2));  // Optional.empty
        
        // 기본값과 함께
        int max1 = findMax(list1).orElse(0);
        System.out.println("최대값: " + max1);  // 9
        
        int max2 = findMax(list2).orElse(0);
        System.out.println("최대값: " + max2);  // 0
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static Optional<Integer> findMax(List<Integer> numbers) {
    return numbers.stream()
        .max(Integer::compareTo);
}

// 또는
public static Optional<Integer> findMax2(List<Integer> numbers) {
    if (numbers.isEmpty()) {
        return Optional.empty();
    }
    return Optional.of(
        numbers.stream()
            .max(Integer::compareTo)
            .get()
    );
}
```
</details>

---

### 문제 3: 안전한 나눗셈

```java
// 0으로 나누기 안전 처리
public class Problem3 {
    public static Optional<Double> safeDivide(double a, double b) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        System.out.println(safeDivide(10, 2));   // Optional[5.0]
        System.out.println(safeDivide(10, 0));   // Optional.empty
        System.out.println(safeDivide(0, 5));    // Optional[0.0]
        
        // 기본값과 함께
        double result = safeDivide(10, 0).orElse(0.0);
        System.out.println("결과: " + result);  // 0.0
        
        // 예외 처리
        try {
            double result2 = safeDivide(10, 0)
                .orElseThrow(() -> new ArithmeticException("0으로 나눌 수 없음"));
        } catch (ArithmeticException e) {
            System.out.println("예외: " + e.getMessage());
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static Optional<Double> safeDivide(double a, double b) {
    if (b == 0) {
        return Optional.empty();
    }
    return Optional.of(a / b);
}

// 또는
public static Optional<Double> safeDivide2(double a, double b) {
    return b == 0 ? Optional.empty() : Optional.of(a / b);
}
```
</details>

---

## 📌 핵심 정리

### 생성
```java
Optional.of(value)          // null 불가
Optional.ofNullable(value)  // null 가능 (권장)
Optional.empty()            // 빈 Optional
```

### 값 확인
```java
isPresent()                 // 값 있는지
isEmpty()                   // 비어있는지 (Java 11+)
ifPresent(consumer)         // 있으면 실행
ifPresentOrElse(c1, c2)     // 있으면/없으면 (Java 9+)
```

### 값 추출
```java
get()                       // 값 추출 (위험)
orElse(value)               // 기본값
orElseGet(supplier)         // 기본값 (지연)
orElseThrow()               // 예외 발생
```

### 변환
```java
map(function)               // 값 변환
flatMap(function)           // Optional 반환 함수
filter(predicate)           // 필터링
```

### Best Practice
```
✅ 메서드 반환값으로만
✅ orElseGet (비용 큰 연산)
✅ Stream과 함께

❌ 필드로 사용 금지
❌ 파라미터로 사용 금지
❌ isPresent + get 금지
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. Stream API](./Util-02-Stream.md) | [다음: 04. 정규표현식 →](./Util-04-Regex.md)**

</div>
