# Reflection 01. Reflection 기본 완전 정복

> 런타임에 클래스 정보 조작하기 - Reflection API의 기초  
> Java Reflection

---

## 📑 목차

1. [Reflection 소개](#1-reflection-소개)
2. [Class 객체](#2-class-객체)
3. [필드 조작](#3-필드-조작)
4. [메서드 호출](#4-메서드-호출)
5. [생성자 사용](#5-생성자-사용)
6. [타입 정보](#6-타입-정보)
7. [배열 처리](#7-배열-처리)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Reflection 소개

### 1.1 왜 Reflection인가?

```java
public class WhyReflection {
    static class User {
        private String name;
        private int age;
        
        public User(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        private void secretMethod() {
            System.out.println("비밀 메서드!");
        }
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== Reflection이란? ===\n");
        
        System.out.println("📌 정의");
        System.out.println("런타임에 클래스 정보를 조사하고 조작하는 API\n");
        
        System.out.println("📌 가능한 것들");
        System.out.println("✅ 클래스 정보 조회");
        System.out.println("✅ private 필드 접근");
        System.out.println("✅ private 메서드 호출");
        System.out.println("✅ 동적 인스턴스 생성");
        System.out.println("✅ 애너테이션 읽기\n");
        
        
        // Reflection 예제
        User user = new User("Alice", 25);
        Class<?> clazz = user.getClass();
        
        // private 필드 접근
        java.lang.reflect.Field nameField = clazz.getDeclaredField("name");
        nameField.setAccessible(true);
        System.out.println("이름: " + nameField.get(user));
        
        // private 메서드 호출
        java.lang.reflect.Method secretMethod = clazz.getDeclaredMethod("secretMethod");
        secretMethod.setAccessible(true);
        secretMethod.invoke(user);
        
        
        System.out.println("\n📌 사용 사례");
        System.out.println("- 프레임워크 (Spring, Hibernate)");
        System.out.println("- 테스트 라이브러리");
        System.out.println("- DI 컨테이너");
        System.out.println("- ORM 매핑");
        System.out.println("- 직렬화/역직렬화\n");
        
        System.out.println("⚠️ 주의사항");
        System.out.println("- 성능 오버헤드");
        System.out.println("- 타입 안전성 상실");
        System.out.println("- 보안 위험");
        System.out.println("- 캡슐화 파괴");
    }
}
```

### 1.2 Reflection API 구조

```java
public class ReflectionAPI {
    public static void main(String[] args) {
        System.out.println("=== Reflection API ===\n");
        
        System.out.println("📦 주요 클래스");
        System.out.println("java.lang.Class");
        System.out.println("  - 클래스/인터페이스 표현\n");
        
        System.out.println("java.lang.reflect.Field");
        System.out.println("  - 필드 정보\n");
        
        System.out.println("java.lang.reflect.Method");
        System.out.println("  - 메서드 정보\n");
        
        System.out.println("java.lang.reflect.Constructor");
        System.out.println("  - 생성자 정보\n");
        
        System.out.println("java.lang.reflect.Modifier");
        System.out.println("  - 접근 제어자 정보\n");
        
        System.out.println("java.lang.reflect.Array");
        System.out.println("  - 배열 조작\n");
    }
}
```

---

## 2. Class 객체

### 2.1 Class 객체 얻기

```java
public class GetClassObject {
    static class Person {
        String name;
    }
    
    public static void main(String[] args) throws ClassNotFoundException {
        System.out.println("=== Class 객체 얻기 ===\n");
        
        // 1. .class 리터럴
        Class<Person> clazz1 = Person.class;
        System.out.println("1. .class: " + clazz1.getName());
        
        // 2. getClass()
        Person person = new Person();
        Class<?> clazz2 = person.getClass();
        System.out.println("2. getClass(): " + clazz2.getName());
        
        // 3. Class.forName()
        Class<?> clazz3 = Class.forName("GetClassObject$Person");
        System.out.println("3. forName(): " + clazz3.getName());
        
        
        // 같은 객체
        System.out.println("\n같은 객체? " + (clazz1 == clazz2));
        System.out.println("같은 객체? " + (clazz2 == clazz3));
        
        
        // 기본 타입
        System.out.println("\n=== 기본 타입 ===");
        Class<?> intClass = int.class;
        Class<?> stringClass = String.class;
        System.out.println("int: " + intClass);
        System.out.println("String: " + stringClass);
    }
}
```

### 2.2 Class 정보 조회

```java
import java.lang.reflect.*;

public class ClassInformation {
    static class User {
        private String name;
        public int age;
        
        public User() {}
        public User(String name) { this.name = name; }
        
        public void publicMethod() {}
        private void privateMethod() {}
    }
    
    public static void main(String[] args) {
        System.out.println("=== Class 정보 ===\n");
        
        Class<User> clazz = User.class;
        
        // 기본 정보
        System.out.println("클래스명: " + clazz.getName());
        System.out.println("단순명: " + clazz.getSimpleName());
        System.out.println("패키지: " + clazz.getPackage());
        System.out.println("모디파이어: " + Modifier.toString(clazz.getModifiers()));
        
        
        // 필드 수
        System.out.println("\n=== 필드 ===");
        Field[] fields = clazz.getDeclaredFields();
        System.out.println("필드 수: " + fields.length);
        for (Field field : fields) {
            System.out.println("  " + field.getName() + ": " + field.getType());
        }
        
        
        // 메서드 수
        System.out.println("\n=== 메서드 ===");
        Method[] methods = clazz.getDeclaredMethods();
        System.out.println("메서드 수: " + methods.length);
        for (Method method : methods) {
            System.out.println("  " + method.getName());
        }
        
        
        // 생성자
        System.out.println("\n=== 생성자 ===");
        Constructor<?>[] constructors = clazz.getDeclaredConstructors();
        System.out.println("생성자 수: " + constructors.length);
        for (Constructor<?> constructor : constructors) {
            System.out.println("  " + constructor);
        }
    }
}
```

---

## 3. 필드 조작

### 3.1 필드 읽기/쓰기

```java
import java.lang.reflect.*;

public class FieldAccess {
    static class Person {
        public String publicField = "public";
        private String privateField = "private";
        protected String protectedField = "protected";
        String defaultField = "default";
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== 필드 접근 ===\n");
        
        Person person = new Person();
        Class<?> clazz = Person.class;
        
        // public 필드 - 직접 접근 가능
        Field publicField = clazz.getField("publicField");
        System.out.println("public: " + publicField.get(person));
        
        
        // private 필드 - setAccessible 필요
        System.out.println("\n=== private 필드 ===");
        Field privateField = clazz.getDeclaredField("privateField");
        privateField.setAccessible(true);  // 접근 허용
        System.out.println("private: " + privateField.get(person));
        
        // 값 변경
        privateField.set(person, "modified");
        System.out.println("변경 후: " + privateField.get(person));
        
        
        // 모든 필드
        System.out.println("\n=== 모든 필드 ===");
        Field[] allFields = clazz.getDeclaredFields();
        for (Field field : allFields) {
            field.setAccessible(true);
            System.out.printf("%s = %s%n", field.getName(), field.get(person));
        }
    }
}
```

### 3.2 필드 정보

```java
import java.lang.reflect.*;

public class FieldInformation {
    static class Sample {
        public static final String CONSTANT = "CONST";
        private int privateInt;
        public String publicString;
        protected double protectedDouble;
        transient int transientInt;
        volatile boolean volatileBool;
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== 필드 정보 ===\n");
        
        Class<?> clazz = Sample.class;
        
        for (Field field : clazz.getDeclaredFields()) {
            System.out.println("필드: " + field.getName());
            System.out.println("  타입: " + field.getType());
            System.out.println("  모디파이어: " + Modifier.toString(field.getModifiers()));
            
            // 속성 체크
            if (Modifier.isStatic(field.getModifiers())) {
                System.out.println("  → static");
            }
            if (Modifier.isFinal(field.getModifiers())) {
                System.out.println("  → final");
            }
            if (Modifier.isTransient(field.getModifiers())) {
                System.out.println("  → transient");
            }
            if (Modifier.isVolatile(field.getModifiers())) {
                System.out.println("  → volatile");
            }
            System.out.println();
        }
    }
}
```

---

## 4. 메서드 호출

### 4.1 메서드 실행

```java
import java.lang.reflect.*;

public class MethodInvocation {
    static class Calculator {
        public int add(int a, int b) {
            return a + b;
        }
        
        private int multiply(int a, int b) {
            return a * b;
        }
        
        public static String greet(String name) {
            return "Hello, " + name;
        }
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== 메서드 호출 ===\n");
        
        Calculator calc = new Calculator();
        Class<?> clazz = Calculator.class;
        
        // public 메서드 호출
        Method addMethod = clazz.getMethod("add", int.class, int.class);
        Object result = addMethod.invoke(calc, 10, 5);
        System.out.println("add(10, 5) = " + result);
        
        
        // private 메서드 호출
        System.out.println("\n=== private 메서드 ===");
        Method multiplyMethod = clazz.getDeclaredMethod("multiply", int.class, int.class);
        multiplyMethod.setAccessible(true);
        Object result2 = multiplyMethod.invoke(calc, 10, 5);
        System.out.println("multiply(10, 5) = " + result2);
        
        
        // static 메서드 호출
        System.out.println("\n=== static 메서드 ===");
        Method greetMethod = clazz.getMethod("greet", String.class);
        Object result3 = greetMethod.invoke(null, "Alice");  // static은 null
        System.out.println(result3);
    }
}
```

### 4.2 메서드 정보

```java
import java.lang.reflect.*;

public class MethodInformation {
    static class Sample {
        public void publicMethod() {}
        private int privateMethod(String s) { return 0; }
        protected static void protectedStaticMethod(int a, int b) {}
        public final void finalMethod() {}
    }
    
    public static void main(String[] args) {
        System.out.println("=== 메서드 정보 ===\n");
        
        Class<?> clazz = Sample.class;
        
        for (Method method : clazz.getDeclaredMethods()) {
            System.out.println("메서드: " + method.getName());
            System.out.println("  반환 타입: " + method.getReturnType());
            System.out.println("  모디파이어: " + Modifier.toString(method.getModifiers()));
            
            // 파라미터
            Class<?>[] paramTypes = method.getParameterTypes();
            if (paramTypes.length > 0) {
                System.out.print("  파라미터: ");
                for (Class<?> paramType : paramTypes) {
                    System.out.print(paramType.getSimpleName() + " ");
                }
                System.out.println();
            }
            
            // 예외
            Class<?>[] exceptionTypes = method.getExceptionTypes();
            if (exceptionTypes.length > 0) {
                System.out.print("  예외: ");
                for (Class<?> exType : exceptionTypes) {
                    System.out.print(exType.getSimpleName() + " ");
                }
                System.out.println();
            }
            
            System.out.println();
        }
    }
}
```

---

## 5. 생성자 사용

### 5.1 인스턴스 생성

```java
import java.lang.reflect.*;

public class InstanceCreation {
    static class Person {
        private String name;
        private int age;
        
        public Person() {
            this("Unknown", 0);
        }
        
        public Person(String name) {
            this(name, 0);
        }
        
        public Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return "Person{name='" + name + "', age=" + age + "}";
        }
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== 인스턴스 생성 ===\n");
        
        Class<Person> clazz = Person.class;
        
        // 기본 생성자
        Person p1 = clazz.getDeclaredConstructor().newInstance();
        System.out.println("기본 생성자: " + p1);
        
        
        // 파라미터 있는 생성자
        Constructor<Person> constructor = clazz.getConstructor(String.class, int.class);
        Person p2 = constructor.newInstance("Alice", 25);
        System.out.println("파라미터 생성자: " + p2);
        
        
        // Class.newInstance() (deprecated)
        System.out.println("\n⚠️ Class.newInstance()는 deprecated");
        System.out.println("대신 getDeclaredConstructor().newInstance() 사용");
    }
}
```

### 5.2 생성자 정보

```java
import java.lang.reflect.*;

public class ConstructorInformation {
    static class Sample {
        public Sample() {}
        public Sample(int a) {}
        private Sample(String s) {}
        protected Sample(int a, String s) {}
    }
    
    public static void main(String[] args) {
        System.out.println("=== 생성자 정보 ===\n");
        
        Class<?> clazz = Sample.class;
        
        Constructor<?>[] constructors = clazz.getDeclaredConstructors();
        System.out.println("생성자 수: " + constructors.length + "\n");
        
        for (Constructor<?> constructor : constructors) {
            System.out.println("생성자: " + constructor.getName());
            System.out.println("  모디파이어: " + Modifier.toString(constructor.getModifiers()));
            
            Class<?>[] paramTypes = constructor.getParameterTypes();
            if (paramTypes.length > 0) {
                System.out.print("  파라미터: ");
                for (Class<?> paramType : paramTypes) {
                    System.out.print(paramType.getSimpleName() + " ");
                }
                System.out.println();
            }
            System.out.println();
        }
    }
}
```

---

## 6. 타입 정보

### 6.1 인터페이스와 상속

```java
import java.lang.reflect.*;

interface Drawable {
    void draw();
}

interface Movable {
    void move();
}

class Shape {
    String color;
}

class Circle extends Shape implements Drawable, Movable {
    double radius;
    
    @Override
    public void draw() {}
    
    @Override
    public void move() {}
}

public class TypeInformation {
    public static void main(String[] args) {
        System.out.println("=== 타입 정보 ===\n");
        
        Class<Circle> clazz = Circle.class;
        
        // 슈퍼클래스
        Class<?> superclass = clazz.getSuperclass();
        System.out.println("슈퍼클래스: " + superclass.getName());
        
        
        // 인터페이스
        System.out.println("\n=== 인터페이스 ===");
        Class<?>[] interfaces = clazz.getInterfaces();
        for (Class<?> intf : interfaces) {
            System.out.println("  " + intf.getName());
        }
        
        
        // 타입 체크
        System.out.println("\n=== 타입 체크 ===");
        System.out.println("Drawable? " + Drawable.class.isAssignableFrom(clazz));
        System.out.println("Shape? " + Shape.class.isAssignableFrom(clazz));
        System.out.println("String? " + String.class.isAssignableFrom(clazz));
        
        
        // 클래스 종류
        System.out.println("\n=== 클래스 종류 ===");
        System.out.println("인터페이스? " + clazz.isInterface());
        System.out.println("배열? " + clazz.isArray());
        System.out.println("Enum? " + clazz.isEnum());
        System.out.println("프리미티브? " + clazz.isPrimitive());
    }
}
```

### 6.2 제네릭 타입

```java
import java.lang.reflect.*;
import java.util.*;

public class GenericTypeInfo {
    static class Container<T> {
        private List<T> items;
        
        public List<T> getItems() {
            return items;
        }
        
        public void setItems(List<T> items) {
            this.items = items;
        }
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== 제네릭 타입 ===\n");
        
        Class<?> clazz = Container.class;
        
        // 필드의 제네릭 타입
        Field itemsField = clazz.getDeclaredField("items");
        Type genericType = itemsField.getGenericType();
        
        System.out.println("필드 타입: " + genericType);
        
        if (genericType instanceof ParameterizedType) {
            ParameterizedType paramType = (ParameterizedType) genericType;
            Type[] typeArgs = paramType.getActualTypeArguments();
            
            System.out.println("타입 파라미터:");
            for (Type typeArg : typeArgs) {
                System.out.println("  " + typeArg);
            }
        }
        
        
        // 메서드의 제네릭 타입
        System.out.println("\n=== 메서드 타입 ===");
        Method getItemsMethod = clazz.getMethod("getItems");
        Type returnType = getItemsMethod.getGenericReturnType();
        System.out.println("반환 타입: " + returnType);
    }
}
```

---

## 7. 배열 처리

### 7.1 배열 생성과 조작

```java
import java.lang.reflect.*;

public class ArrayHandling {
    public static void main(String[] args) {
        System.out.println("=== 배열 처리 ===\n");
        
        // 배열 생성
        int[] intArray = (int[]) Array.newInstance(int.class, 5);
        System.out.println("배열 길이: " + Array.getLength(intArray));
        
        // 값 설정
        for (int i = 0; i < 5; i++) {
            Array.setInt(intArray, i, i * 10);
        }
        
        // 값 읽기
        System.out.println("\n배열 내용:");
        for (int i = 0; i < Array.getLength(intArray); i++) {
            System.out.println("  [" + i + "] = " + Array.getInt(intArray, i));
        }
        
        
        // 다차원 배열
        System.out.println("\n=== 다차원 배열 ===");
        int[][] matrix = (int[][]) Array.newInstance(int.class, 3, 3);
        Array.setInt(Array.get(matrix, 0), 0, 1);
        Array.setInt(Array.get(matrix, 1), 1, 2);
        Array.setInt(Array.get(matrix, 2), 2, 3);
        
        System.out.println("matrix[0][0] = " + Array.getInt(Array.get(matrix, 0), 0));
        System.out.println("matrix[1][1] = " + Array.getInt(Array.get(matrix, 1), 1));
        System.out.println("matrix[2][2] = " + Array.getInt(Array.get(matrix, 2), 2));
        
        
        // 객체 배열
        System.out.println("\n=== 객체 배열 ===");
        String[] strArray = (String[]) Array.newInstance(String.class, 3);
        Array.set(strArray, 0, "A");
        Array.set(strArray, 1, "B");
        Array.set(strArray, 2, "C");
        
        for (int i = 0; i < Array.getLength(strArray); i++) {
            System.out.println("  " + Array.get(strArray, i));
        }
    }
}
```

---

## 8. 실전 예제

### 8.1 간단한 DI 컨테이너

```java
import java.lang.reflect.*;
import java.util.*;

// 애너테이션
@interface Inject {}

class UserService {
    @Inject
    private UserRepository repository;
    
    public void printInfo() {
        System.out.println("UserService with " + repository);
    }
}

class UserRepository {
    public UserRepository() {
        System.out.println("UserRepository 생성");
    }
}

class SimpleContainer {
    private Map<Class<?>, Object> instances = new HashMap<>();
    
    public <T> T getInstance(Class<T> clazz) throws Exception {
        // 이미 생성된 인스턴스 반환
        if (instances.containsKey(clazz)) {
            return clazz.cast(instances.get(clazz));
        }
        
        // 새 인스턴스 생성
        T instance = clazz.getDeclaredConstructor().newInstance();
        
        // @Inject 필드 주입
        for (Field field : clazz.getDeclaredFields()) {
            if (field.isAnnotationPresent(Inject.class)) {
                field.setAccessible(true);
                Object dependency = getInstance(field.getType());
                field.set(instance, dependency);
            }
        }
        
        instances.put(clazz, instance);
        return instance;
    }
}

public class SimpleDIContainer {
    public static void main(String[] args) throws Exception {
        System.out.println("=== 간단한 DI 컨테이너 ===\n");
        
        SimpleContainer container = new SimpleContainer();
        
        UserService service = container.getInstance(UserService.class);
        service.printInfo();
    }
}
```

### 8.2 객체 복사기

```java
import java.lang.reflect.*;

public class ObjectCopier {
    static class Person {
        private String name;
        private int age;
        private String email;
        
        public Person(String name, int age, String email) {
            this.name = name;
            this.age = age;
            this.email = email;
        }
        
        @Override
        public String toString() {
            return "Person{name='" + name + "', age=" + age + ", email='" + email + "'}";
        }
    }
    
    public static <T> T copy(T source) throws Exception {
        Class<?> clazz = source.getClass();
        
        // 새 인스턴스 생성
        @SuppressWarnings("unchecked")
        T target = (T) clazz.getDeclaredConstructor().newInstance();
        
        // 모든 필드 복사
        for (Field field : clazz.getDeclaredFields()) {
            field.setAccessible(true);
            Object value = field.get(source);
            field.set(target, value);
        }
        
        return target;
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== 객체 복사 ===\n");
        
        Person original = new Person("Alice", 25, "alice@example.com");
        System.out.println("원본: " + original);
        
        Person copied = copy(original);
        System.out.println("복사: " + copied);
        
        System.out.println("\n다른 객체? " + (original != copied));
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: toString 생성기

```java
// Reflection으로 toString() 생성
import java.lang.reflect.*;

public class Problem1 {
    static class User {
        private String name = "Alice";
        private int age = 25;
        private String email = "alice@example.com";
    }
    
    public static String toStringReflection(Object obj) throws Exception {
        // 구현: 모든 필드를 "ClassName{field=value, ...}" 형식으로
        return "";
    }
    
    public static void main(String[] args) throws Exception {
        User user = new User();
        System.out.println(toStringReflection(user));
        // User{name=Alice, age=25, email=alice@example.com}
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static String toStringReflection(Object obj) throws Exception {
    Class<?> clazz = obj.getClass();
    StringBuilder sb = new StringBuilder(clazz.getSimpleName()).append("{");
    
    Field[] fields = clazz.getDeclaredFields();
    for (int i = 0; i < fields.length; i++) {
        Field field = fields[i];
        field.setAccessible(true);
        
        sb.append(field.getName()).append("=").append(field.get(obj));
        
        if (i < fields.length - 1) {
            sb.append(", ");
        }
    }
    
    return sb.append("}").toString();
}
```
</details>

---

### 문제 2: Getter 호출기

```java
// 모든 getter 메서드 찾아서 호출
import java.lang.reflect.*;

public class Problem2 {
    static class Person {
        private String name = "Bob";
        private int age = 30;
        
        public String getName() { return name; }
        public int getAge() { return age; }
        public void setName(String name) { this.name = name; }
    }
    
    public static void callAllGetters(Object obj) throws Exception {
        // 구현: get으로 시작하는 메서드만 호출하고 출력
    }
    
    public static void main(String[] args) throws Exception {
        Person person = new Person();
        callAllGetters(person);
        // getName() = Bob
        // getAge() = 30
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void callAllGetters(Object obj) throws Exception {
    Class<?> clazz = obj.getClass();
    
    for (Method method : clazz.getDeclaredMethods()) {
        if (method.getName().startsWith("get") && 
            method.getParameterCount() == 0) {
            
            Object result = method.invoke(obj);
            System.out.println(method.getName() + "() = " + result);
        }
    }
}
```
</details>

---

### 문제 3: 필드 초기화

```java
// 모든 필드를 기본값으로 초기화
import java.lang.reflect.*;

public class Problem3 {
    static class Data {
        private String name = "Test";
        private int count = 100;
        private boolean flag = true;
    }
    
    public static void resetFields(Object obj) throws Exception {
        // 구현: String→null, int→0, boolean→false
    }
    
    public static void main(String[] args) throws Exception {
        Data data = new Data();
        System.out.println("초기화 전: " + data.name + ", " + data.count + ", " + data.flag);
        
        resetFields(data);
        System.out.println("초기화 후: " + data.name + ", " + data.count + ", " + data.flag);
        // null, 0, false
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void resetFields(Object obj) throws Exception {
    Class<?> clazz = obj.getClass();
    
    for (Field field : clazz.getDeclaredFields()) {
        field.setAccessible(true);
        
        Class<?> type = field.getType();
        if (type == int.class) {
            field.setInt(obj, 0);
        } else if (type == boolean.class) {
            field.setBoolean(obj, false);
        } else if (!type.isPrimitive()) {
            field.set(obj, null);
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### Class 객체 얻기
```java
Class<?> clazz = Object.class;           // .class
Class<?> clazz = obj.getClass();         // getClass()
Class<?> clazz = Class.forName("Name");  // forName()
```

### 필드 조작
```java
Field field = clazz.getDeclaredField("name");
field.setAccessible(true);        // private 접근
Object value = field.get(obj);    // 읽기
field.set(obj, "new value");      // 쓰기
```

### 메서드 호출
```java
Method method = clazz.getMethod("methodName", ParamType.class);
Object result = method.invoke(obj, args);  // 호출
```

### 생성자 사용
```java
Constructor<?> ctor = clazz.getDeclaredConstructor(ParamType.class);
Object obj = ctor.newInstance(args);
```

### 주의사항
```
⚠️ 성능: Reflection은 느림
⚠️ 보안: 캡슐화 파괴
⚠️ 타입: 컴파일 타임 체크 없음
✅ 프레임워크에서만 사용 권장
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Annotation →](./Reflection-02-Annotation.md)**

</div>
