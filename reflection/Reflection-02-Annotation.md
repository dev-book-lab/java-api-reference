# Reflection 02. Annotation 완전 정복

> 메타데이터 활용하기 - Annotation과 Reflection  
> Java Annotation

---

## 📑 목차

1. [Annotation 소개](#1-annotation-소개)
2. [표준 Annotation](#2-표준-annotation)
3. [커스텀 Annotation](#3-커스텀-annotation)
4. [Meta Annotation](#4-meta-annotation)
5. [Annotation 처리](#5-annotation-처리)
6. [실전 패턴](#6-실전-패턴)
7. [프레임워크 스타일](#7-프레임워크-스타일)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Annotation 소개

### 1.1 왜 Annotation인가?

```java
public class WhyAnnotation {
    // ❌ 주석으로 메타데이터 표현 (처리 불가)
    // @author Alice
    // @version 1.0
    // @deprecated
    public void oldMethod() {}
    
    // ✅ Annotation으로 메타데이터 표현 (처리 가능)
    @Deprecated
    @SuppressWarnings("unused")
    public void newMethod() {}
    
    public static void main(String[] args) {
        System.out.println("=== Annotation이란? ===\n");
        
        System.out.println("📌 정의");
        System.out.println("코드에 메타데이터를 추가하는 방법\n");
        
        System.out.println("📌 용도");
        System.out.println("✅ 컴파일러에 정보 제공");
        System.out.println("✅ 빌드/배포 시 처리");
        System.out.println("✅ 런타임 처리\n");
        
        System.out.println("📌 장점");
        System.out.println("- 코드와 메타데이터 통합");
        System.out.println("- 타입 안전");
        System.out.println("- 도구로 처리 가능");
        System.out.println("- 가독성 향상\n");
        
        System.out.println("📌 사용 예");
        System.out.println("@Override - 오버라이드 검증");
        System.out.println("@Deprecated - 사용 중단 표시");
        System.out.println("@Entity - JPA 엔티티");
        System.out.println("@Controller - Spring MVC");
        System.out.println("@Test - JUnit 테스트");
    }
}
```

### 1.2 Annotation 구조

```java
import java.lang.annotation.*;

public class AnnotationStructure {
    public static void main(String[] args) {
        System.out.println("=== Annotation 구조 ===\n");
        
        System.out.println("📌 기본 형태");
        System.out.println("@interface AnnotationName {");
        System.out.println("    타입 요소명() [default 값];");
        System.out.println("}\n");
        
        System.out.println("📌 적용 위치");
        System.out.println("- 클래스, 인터페이스");
        System.out.println("- 메서드");
        System.out.println("- 필드");
        System.out.println("- 파라미터");
        System.out.println("- 생성자");
        System.out.println("- 로컬 변수");
        System.out.println("- 패키지\n");
        
        System.out.println("📌 보존 정책 (@Retention)");
        System.out.println("SOURCE: 소스 코드만");
        System.out.println("CLASS: 클래스 파일까지 (기본값)");
        System.out.println("RUNTIME: 런타임까지");
    }
}
```

---

## 2. 표준 Annotation

### 2.1 @Override

```java
public class OverrideAnnotation {
    static class Parent {
        public void method() {
            System.out.println("부모");
        }
    }
    
    static class Child extends Parent {
        // ✅ 올바른 오버라이드
        @Override
        public void method() {
            System.out.println("자식");
        }
        
        // ❌ 컴파일 에러 - 오타
        // @Override
        // public void metod() {  // method가 아님!
        // }
    }
    
    public static void main(String[] args) {
        System.out.println("=== @Override ===\n");
        
        System.out.println("📌 용도");
        System.out.println("- 오버라이드 검증");
        System.out.println("- 실수 방지\n");
        
        Child child = new Child();
        child.method();
        
        System.out.println("\n✅ 항상 사용 권장!");
    }
}
```

### 2.2 @Deprecated

```java
public class DeprecatedAnnotation {
    @Deprecated
    public static void oldMethod() {
        System.out.println("구버전 메서드");
    }
    
    @Deprecated(since = "2.0", forRemoval = true)
    public static void veryOldMethod() {
        System.out.println("곧 제거될 메서드");
    }
    
    public static void newMethod() {
        System.out.println("새 메서드");
    }
    
    public static void main(String[] args) {
        System.out.println("=== @Deprecated ===\n");
        
        System.out.println("📌 용도");
        System.out.println("- 사용 중단 표시");
        System.out.println("- 경고 메시지 생성\n");
        
        oldMethod();  // 경고 발생
        newMethod();
    }
}
```

### 2.3 @SuppressWarnings

```java
import java.util.*;

public class SuppressWarningsAnnotation {
    @SuppressWarnings("unchecked")
    public static void uncheckedOperation() {
        List list = new ArrayList();  // raw type
        list.add("String");
    }
    
    @SuppressWarnings({"unused", "deprecation"})
    public static void multipleWarnings() {
        int unused = 10;
        Date date = new Date(2024, 1, 1);  // deprecated
    }
    
    public static void main(String[] args) {
        System.out.println("=== @SuppressWarnings ===\n");
        
        System.out.println("📌 주요 값");
        System.out.println("unchecked - 제네릭 경고");
        System.out.println("deprecation - deprecated 경고");
        System.out.println("unused - 미사용 경고");
        System.out.println("rawtypes - raw type 경고");
        System.out.println("all - 모든 경고\n");
        
        System.out.println("⚠️ 신중하게 사용!");
    }
}
```

### 2.4 @FunctionalInterface

```java
@FunctionalInterface
interface Calculator {
    int calculate(int a, int b);
    
    // default, static 메서드는 OK
    default void print() {
        System.out.println("Calculator");
    }
    
    // ❌ 추상 메서드 2개는 에러
    // int another(int x);
}

public class FunctionalInterfaceAnnotation {
    public static void main(String[] args) {
        System.out.println("=== @FunctionalInterface ===\n");
        
        System.out.println("📌 용도");
        System.out.println("- 함수형 인터페이스 검증");
        System.out.println("- 추상 메서드 1개만 허용\n");
        
        Calculator add = (a, b) -> a + b;
        System.out.println("10 + 5 = " + add.calculate(10, 5));
    }
}
```

---

## 3. 커스텀 Annotation

### 3.1 간단한 Annotation

```java
import java.lang.annotation.*;

// Marker Annotation (요소 없음)
@interface Marker {}

// Single-Value Annotation
@interface Version {
    String value();
}

// Multi-Value Annotation
@interface Author {
    String name();
    String date();
}

// Default 값
@interface Info {
    String value() default "Unknown";
    int version() default 1;
}

public class CustomAnnotationBasic {
    @Marker
    @Version("1.0")
    @Author(name = "Alice", date = "2024-01-01")
    @Info
    public void method1() {}
    
    @Info("Custom")
    public void method2() {}
    
    @Info(value = "Test", version = 2)
    public void method3() {}
    
    public static void main(String[] args) {
        System.out.println("=== 커스텀 Annotation ===\n");
        
        System.out.println("📌 종류");
        System.out.println("Marker: 요소 없음");
        System.out.println("Single-Value: 하나의 요소");
        System.out.println("Multi-Value: 여러 요소\n");
        
        System.out.println("📌 value()");
        System.out.println("요소명이 value면 이름 생략 가능");
        System.out.println("@Info(\"Test\") == @Info(value=\"Test\")");
    }
}
```

### 3.2 타입별 요소

```java
import java.lang.annotation.*;

@interface Config {
    // 기본 타입
    int intValue();
    long longValue();
    double doubleValue();
    boolean boolValue();
    
    // String
    String stringValue();
    
    // Class
    Class<?> classValue();
    
    // Enum
    Priority priority();
    
    // Annotation
    Author author();
    
    // 배열
    String[] tags();
}

enum Priority {
    LOW, MEDIUM, HIGH
}

@interface Author {
    String name();
}

public class AnnotationElementTypes {
    @Config(
        intValue = 10,
        longValue = 100L,
        doubleValue = 3.14,
        boolValue = true,
        stringValue = "test",
        classValue = String.class,
        priority = Priority.HIGH,
        author = @Author(name = "Alice"),
        tags = {"tag1", "tag2"}
    )
    public void method() {}
    
    public static void main(String[] args) {
        System.out.println("=== Annotation 요소 타입 ===\n");
        
        System.out.println("✅ 허용되는 타입");
        System.out.println("- 기본 타입");
        System.out.println("- String");
        System.out.println("- Class");
        System.out.println("- Enum");
        System.out.println("- Annotation");
        System.out.println("- 위 타입의 배열\n");
        
        System.out.println("❌ 허용 안 됨");
        System.out.println("- Object");
        System.out.println("- 일반 클래스");
        System.out.println("- 제네릭");
    }
}
```

---

## 4. Meta Annotation

### 4.1 @Retention

```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.SOURCE)
@interface SourceOnly {}

@Retention(RetentionPolicy.CLASS)
@interface ClassLevel {}

@Retention(RetentionPolicy.RUNTIME)
@interface RuntimeLevel {}

public class RetentionAnnotation {
    @SourceOnly
    @ClassLevel
    @RuntimeLevel
    public void method() {}
    
    public static void main(String[] args) {
        System.out.println("=== @Retention ===\n");
        
        System.out.println("📌 RetentionPolicy.SOURCE");
        System.out.println("- 소스 코드에만 존재");
        System.out.println("- 컴파일 후 제거");
        System.out.println("- 예: @Override, @SuppressWarnings\n");
        
        System.out.println("📌 RetentionPolicy.CLASS (기본값)");
        System.out.println("- 클래스 파일까지 유지");
        System.out.println("- 런타임에는 없음\n");
        
        System.out.println("📌 RetentionPolicy.RUNTIME");
        System.out.println("- 런타임까지 유지");
        System.out.println("- Reflection으로 읽기 가능");
        System.out.println("- 예: @Entity, @Controller");
    }
}
```

### 4.2 @Target

```java
import java.lang.annotation.*;

@Target(ElementType.TYPE)
@interface ClassOnly {}

@Target(ElementType.METHOD)
@interface MethodOnly {}

@Target(ElementType.FIELD)
@interface FieldOnly {}

@Target({ElementType.METHOD, ElementType.FIELD})
@interface MethodOrField {}

@ClassOnly
class Sample {
    @FieldOnly
    private int field;
    
    @MethodOnly
    @MethodOrField
    public void method() {}
}

public class TargetAnnotation {
    public static void main(String[] args) {
        System.out.println("=== @Target ===\n");
        
        System.out.println("📌 ElementType");
        System.out.println("TYPE: 클래스, 인터페이스");
        System.out.println("FIELD: 필드");
        System.out.println("METHOD: 메서드");
        System.out.println("PARAMETER: 파라미터");
        System.out.println("CONSTRUCTOR: 생성자");
        System.out.println("LOCAL_VARIABLE: 지역 변수");
        System.out.println("ANNOTATION_TYPE: Annotation");
        System.out.println("PACKAGE: 패키지");
    }
}
```

### 4.3 기타 Meta Annotation

```java
import java.lang.annotation.*;

@Documented
@Retention(RetentionPolicy.RUNTIME)
@interface DocumentedAnnotation {
    String value();
}

@Inherited
@Retention(RetentionPolicy.RUNTIME)
@interface InheritedAnnotation {}

@Repeatable(Tags.class)
@Retention(RetentionPolicy.RUNTIME)
@interface Tag {
    String value();
}

@Retention(RetentionPolicy.RUNTIME)
@interface Tags {
    Tag[] value();
}

@InheritedAnnotation
class Parent {}

class Child extends Parent {}  // @InheritedAnnotation 상속됨

public class OtherMetaAnnotations {
    @Tag("tag1")
    @Tag("tag2")
    @DocumentedAnnotation("test")
    public void method() {}
    
    public static void main(String[] args) {
        System.out.println("=== 기타 Meta Annotation ===\n");
        
        System.out.println("📌 @Documented");
        System.out.println("- Javadoc에 포함\n");
        
        System.out.println("📌 @Inherited");
        System.out.println("- 하위 클래스 상속\n");
        
        System.out.println("📌 @Repeatable");
        System.out.println("- 같은 Annotation 반복 사용");
    }
}
```

---

## 5. Annotation 처리

### 5.1 Reflection으로 읽기

```java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Test {
    String name() default "";
    int timeout() default 0;
}

public class AnnotationProcessing {
    @Test(name = "test1", timeout = 1000)
    public void testMethod1() {}
    
    @Test
    public void testMethod2() {}
    
    public void normalMethod() {}
    
    public static void main(String[] args) {
        System.out.println("=== Annotation 처리 ===\n");
        
        Class<?> clazz = AnnotationProcessing.class;
        
        for (Method method : clazz.getDeclaredMethods()) {
            // @Test가 있는지 확인
            if (method.isAnnotationPresent(Test.class)) {
                Test test = method.getAnnotation(Test.class);
                
                System.out.println("테스트 메서드: " + method.getName());
                System.out.println("  이름: " + test.name());
                System.out.println("  타임아웃: " + test.timeout());
                System.out.println();
            }
        }
    }
}
```

### 5.2 모든 Annotation 조회

```java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@interface A {
    String value();
}

@Retention(RetentionPolicy.RUNTIME)
@interface B {
    int num();
}

public class GetAllAnnotations {
    @A("test")
    @B(num = 10)
    @Deprecated
    public void method() {}
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== 모든 Annotation ===\n");
        
        Method method = GetAllAnnotations.class.getMethod("method");
        
        // 모든 Annotation
        Annotation[] annotations = method.getAnnotations();
        System.out.println("Annotation 수: " + annotations.length + "\n");
        
        for (Annotation ann : annotations) {
            System.out.println(ann);
        }
        
        
        // 특정 Annotation
        System.out.println("\n=== 특정 Annotation ===");
        if (method.isAnnotationPresent(A.class)) {
            A a = method.getAnnotation(A.class);
            System.out.println("@A value: " + a.value());
        }
    }
}
```

---

## 6. 실전 패턴

### 6.1 Validation

```java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface NotNull {}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Min {
    int value();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Max {
    int value();
}

class User {
    @NotNull
    private String name;
    
    @Min(0)
    @Max(150)
    private int age;
    
    public User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

class Validator {
    public static void validate(Object obj) throws Exception {
        Class<?> clazz = obj.getClass();
        
        for (Field field : clazz.getDeclaredFields()) {
            field.setAccessible(true);
            Object value = field.get(obj);
            
            // @NotNull 체크
            if (field.isAnnotationPresent(NotNull.class)) {
                if (value == null) {
                    throw new IllegalArgumentException(
                        field.getName() + " is null");
                }
            }
            
            // @Min 체크
            if (field.isAnnotationPresent(Min.class)) {
                Min min = field.getAnnotation(Min.class);
                if (value instanceof Integer) {
                    int intValue = (Integer) value;
                    if (intValue < min.value()) {
                        throw new IllegalArgumentException(
                            field.getName() + " < " + min.value());
                    }
                }
            }
            
            // @Max 체크
            if (field.isAnnotationPresent(Max.class)) {
                Max max = field.getAnnotation(Max.class);
                if (value instanceof Integer) {
                    int intValue = (Integer) value;
                    if (intValue > max.value()) {
                        throw new IllegalArgumentException(
                            field.getName() + " > " + max.value());
                    }
                }
            }
        }
    }
}

public class ValidationPattern {
    public static void main(String[] args) {
        System.out.println("=== Validation 패턴 ===\n");
        
        try {
            User user1 = new User("Alice", 25);
            Validator.validate(user1);
            System.out.println("user1: 유효함");
        } catch (Exception e) {
            System.out.println("user1: " + e.getMessage());
        }
        
        try {
            User user2 = new User(null, 25);
            Validator.validate(user2);
            System.out.println("user2: 유효함");
        } catch (Exception e) {
            System.out.println("user2: " + e.getMessage());
        }
        
        try {
            User user3 = new User("Bob", 200);
            Validator.validate(user3);
            System.out.println("user3: 유효함");
        } catch (Exception e) {
            System.out.println("user3: " + e.getMessage());
        }
    }
}
```

### 6.2 Dependency Injection

```java
import java.lang.annotation.*;
import java.lang.reflect.*;
import java.util.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Inject {}

class ServiceA {
    public void doA() {
        System.out.println("Service A");
    }
}

class ServiceB {
    @Inject
    private ServiceA serviceA;
    
    public void doB() {
        System.out.println("Service B");
        serviceA.doA();
    }
}

class SimpleContainer {
    private Map<Class<?>, Object> instances = new HashMap<>();
    
    public <T> T get(Class<T> clazz) throws Exception {
        if (instances.containsKey(clazz)) {
            return clazz.cast(instances.get(clazz));
        }
        
        T instance = clazz.getDeclaredConstructor().newInstance();
        
        // @Inject 처리
        for (Field field : clazz.getDeclaredFields()) {
            if (field.isAnnotationPresent(Inject.class)) {
                field.setAccessible(true);
                Object dependency = get(field.getType());
                field.set(instance, dependency);
            }
        }
        
        instances.put(clazz, instance);
        return instance;
    }
}

public class DIPattern {
    public static void main(String[] args) throws Exception {
        System.out.println("=== DI 패턴 ===\n");
        
        SimpleContainer container = new SimpleContainer();
        
        ServiceB serviceB = container.get(ServiceB.class);
        serviceB.doB();
    }
}
```

---

## 7. 프레임워크 스타일

### 7.1 Spring-Style

```java
import java.lang.annotation.*;

// 컴포넌트 마커
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Component {
    String value() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Service {
    String value() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Repository {
    String value() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Controller {
    String value() default "";
}

// 의존성 주입
@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.FIELD, ElementType.PARAMETER, ElementType.CONSTRUCTOR})
@interface Autowired {}

// 매핑
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface GetMapping {
    String value();
}

@Service
class UserService {
    public String getUser() {
        return "User Data";
    }
}

@Controller
class UserController {
    @Autowired
    private UserService userService;
    
    @GetMapping("/users")
    public String getUsers() {
        return userService.getUser();
    }
}

public class SpringStyleAnnotations {
    public static void main(String[] args) {
        System.out.println("=== Spring 스타일 ===\n");
        
        System.out.println("📌 계층별 Annotation");
        System.out.println("@Controller - 웹 계층");
        System.out.println("@Service - 비즈니스 계층");
        System.out.println("@Repository - 데이터 계층");
        System.out.println("@Component - 일반 컴포넌트\n");
        
        System.out.println("📌 의존성 주입");
        System.out.println("@Autowired");
        System.out.println("@Inject");
        System.out.println("@Resource\n");
        
        System.out.println("📌 매핑");
        System.out.println("@RequestMapping");
        System.out.println("@GetMapping");
        System.out.println("@PostMapping");
    }
}
```

### 7.2 JPA-Style

```java
import java.lang.annotation.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Entity {
    String name() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Table {
    String name();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Id {}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Column {
    String name() default "";
    boolean nullable() default true;
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface GeneratedValue {}

@Entity
@Table(name = "users")
class User {
    @Id
    @GeneratedValue
    @Column(name = "user_id")
    private Long id;
    
    @Column(name = "user_name", nullable = false)
    private String name;
    
    @Column(name = "email")
    private String email;
}

public class JPAStyleAnnotations {
    public static void main(String[] args) {
        System.out.println("=== JPA 스타일 ===\n");
        
        System.out.println("📌 엔티티");
        System.out.println("@Entity - 엔티티 클래스");
        System.out.println("@Table - 테이블 매핑\n");
        
        System.out.println("📌 필드 매핑");
        System.out.println("@Id - 기본 키");
        System.out.println("@Column - 컬럼 매핑");
        System.out.println("@GeneratedValue - 자동 생성\n");
        
        System.out.println("📌 관계");
        System.out.println("@OneToMany");
        System.out.println("@ManyToOne");
        System.out.println("@ManyToMany");
    }
}
```

---

## 8. 실전 예제

### 8.1 간단한 테스트 프레임워크

```java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Test {}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface BeforeEach {}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface AfterEach {}

class TestRunner {
    public static void run(Class<?> testClass) throws Exception {
        Object instance = testClass.getDeclaredConstructor().newInstance();
        
        // @BeforeEach 찾기
        Method beforeEach = null;
        for (Method method : testClass.getDeclaredMethods()) {
            if (method.isAnnotationPresent(BeforeEach.class)) {
                beforeEach = method;
            }
        }
        
        // @AfterEach 찾기
        Method afterEach = null;
        for (Method method : testClass.getDeclaredMethods()) {
            if (method.isAnnotationPresent(AfterEach.class)) {
                afterEach = method;
            }
        }
        
        // @Test 실행
        int total = 0, passed = 0;
        for (Method method : testClass.getDeclaredMethods()) {
            if (method.isAnnotationPresent(Test.class)) {
                total++;
                
                try {
                    if (beforeEach != null) {
                        beforeEach.invoke(instance);
                    }
                    
                    method.invoke(instance);
                    passed++;
                    System.out.println("✅ " + method.getName());
                    
                    if (afterEach != null) {
                        afterEach.invoke(instance);
                    }
                } catch (Exception e) {
                    System.out.println("❌ " + method.getName() + ": " + 
                        e.getCause().getMessage());
                }
            }
        }
        
        System.out.println("\n결과: " + passed + "/" + total + " 통과");
    }
}

class SampleTest {
    @BeforeEach
    public void setUp() {
        System.out.println("  [setup]");
    }
    
    @AfterEach
    public void tearDown() {
        System.out.println("  [teardown]");
    }
    
    @Test
    public void test1() {
        System.out.println("  test1 실행");
    }
    
    @Test
    public void test2() {
        System.out.println("  test2 실행");
        if (true) throw new RuntimeException("실패!");
    }
    
    @Test
    public void test3() {
        System.out.println("  test3 실행");
    }
}

public class SimpleTestFramework {
    public static void main(String[] args) throws Exception {
        System.out.println("=== 테스트 프레임워크 ===\n");
        
        TestRunner.run(SampleTest.class);
    }
}
```

### 8.2 JSON Serializer

```java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface JsonProperty {
    String value() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface JsonIgnore {}

class JsonSerializer {
    public static String toJson(Object obj) throws Exception {
        StringBuilder json = new StringBuilder("{");
        Class<?> clazz = obj.getClass();
        Field[] fields = clazz.getDeclaredFields();
        
        boolean first = true;
        for (Field field : fields) {
            // @JsonIgnore 체크
            if (field.isAnnotationPresent(JsonIgnore.class)) {
                continue;
            }
            
            field.setAccessible(true);
            Object value = field.get(obj);
            
            if (!first) {
                json.append(", ");
            }
            first = false;
            
            // 필드명
            String name = field.getName();
            if (field.isAnnotationPresent(JsonProperty.class)) {
                JsonProperty prop = field.getAnnotation(JsonProperty.class);
                if (!prop.value().isEmpty()) {
                    name = prop.value();
                }
            }
            
            // 값
            json.append("\"").append(name).append("\": ");
            if (value instanceof String) {
                json.append("\"").append(value).append("\"");
            } else {
                json.append(value);
            }
        }
        
        json.append("}");
        return json.toString();
    }
}

class Person {
    @JsonProperty("fullName")
    private String name;
    
    private int age;
    
    @JsonIgnore
    private String password;
    
    public Person(String name, int age, String password) {
        this.name = name;
        this.age = age;
        this.password = password;
    }
}

public class JsonSerializerExample {
    public static void main(String[] args) throws Exception {
        System.out.println("=== JSON Serializer ===\n");
        
        Person person = new Person("Alice", 25, "secret");
        String json = JsonSerializer.toJson(person);
        
        System.out.println(json);
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: @Required 검증

```java
// @Required 필드가 null이면 예외
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Required {}

class Data {
    @Required
    private String name;
    
    private int age;
    
    public Data(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

public class Problem1 {
    public static void validate(Object obj) throws Exception {
        // 구현: @Required 필드가 null이면 예외
    }
    
    public static void main(String[] args) throws Exception {
        Data data1 = new Data("Alice", 25);
        validate(data1);  // OK
        
        Data data2 = new Data(null, 30);
        validate(data2);  // 예외!
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void validate(Object obj) throws Exception {
    Class<?> clazz = obj.getClass();
    
    for (Field field : clazz.getDeclaredFields()) {
        if (field.isAnnotationPresent(Required.class)) {
            field.setAccessible(true);
            Object value = field.get(obj);
            
            if (value == null) {
                throw new IllegalArgumentException(
                    field.getName() + " is required");
            }
        }
    }
}
```
</details>

---

### 문제 2: @Timeout 체크

```java
// @Timeout으로 메서드 실행 시간 제한
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface Timeout {
    long value();  // milliseconds
}

public class Problem2 {
    @Timeout(1000)
    public void fastMethod() throws InterruptedException {
        Thread.sleep(500);
    }
    
    @Timeout(1000)
    public void slowMethod() throws InterruptedException {
        Thread.sleep(2000);
    }
    
    public static void executeWithTimeout(Object obj, String methodName) 
            throws Exception {
        // 구현: 시간 초과 시 예외
    }
    
    public static void main(String[] args) throws Exception {
        Problem2 p = new Problem2();
        executeWithTimeout(p, "fastMethod");  // OK
        executeWithTimeout(p, "slowMethod");  // 예외!
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void executeWithTimeout(Object obj, String methodName) 
        throws Exception {
    Method method = obj.getClass().getMethod(methodName);
    
    if (method.isAnnotationPresent(Timeout.class)) {
        Timeout timeout = method.getAnnotation(Timeout.class);
        long start = System.currentTimeMillis();
        
        method.invoke(obj);
        
        long elapsed = System.currentTimeMillis() - start;
        if (elapsed > timeout.value()) {
            throw new RuntimeException("Timeout: " + elapsed + "ms");
        }
    } else {
        method.invoke(obj);
    }
}
```
</details>

---

### 문제 3: @Default 값 설정

```java
// @Default로 필드 초기값 설정
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Default {
    String value();
}

class Config {
    @Default("localhost")
    private String host;
    
    @Default("8080")
    private int port;
    
    @Default("true")
    private boolean enabled;
}

public class Problem3 {
    public static void setDefaults(Object obj) throws Exception {
        // 구현: @Default 값으로 필드 초기화
    }
    
    public static void main(String[] args) throws Exception {
        Config config = new Config();
        setDefaults(config);
        // host = "localhost", port = 8080, enabled = true
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void setDefaults(Object obj) throws Exception {
    Class<?> clazz = obj.getClass();
    
    for (Field field : clazz.getDeclaredFields()) {
        if (field.isAnnotationPresent(Default.class)) {
            field.setAccessible(true);
            Default def = field.getAnnotation(Default.class);
            String value = def.value();
            
            Class<?> type = field.getType();
            if (type == String.class) {
                field.set(obj, value);
            } else if (type == int.class) {
                field.setInt(obj, Integer.parseInt(value));
            } else if (type == boolean.class) {
                field.setBoolean(obj, Boolean.parseBoolean(value));
            }
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### Annotation 정의
```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.METHOD)
@interface MyAnnotation {
    String value() default "";
    int num() default 0;
}
```

### Meta Annotation
```java
@Retention   // 보존 정책
@Target      // 적용 대상
@Documented  // Javadoc 포함
@Inherited   // 상속
@Repeatable  // 반복 사용
```

### Annotation 읽기
```java
if (method.isAnnotationPresent(Test.class)) {
    Test test = method.getAnnotation(Test.class);
    String value = test.value();
}
```

### 활용 패턴
```
✅ Validation
✅ Dependency Injection
✅ ORM Mapping
✅ Testing
✅ Serialization
```

### 주의사항
```
⚠️ RUNTIME Retention 필요
⚠️ Reflection 성능
⚠️ 타입 안전성
✅ 프레임워크에서 주로 사용
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Reflection Basic](./Reflection-01-Basic.md) | [다음: 03. Advanced →](./Reflection-03-Advanced.md)**

</div>
