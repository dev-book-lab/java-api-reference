# Reflection 03. Reflection Advanced 완전 정복

> 고급 Reflection 활용 - Proxy, Dynamic Loading, 성능 최적화  
> Java Reflection

---

## 📑 목차

1. [Dynamic Proxy](#1-dynamic-proxy)
2. [ClassLoader](#2-classloader)
3. [MethodHandle](#3-methodhandle)
4. [성능 최적화](#4-성능-최적화)
5. [보안 이슈](#5-보안-이슈)
6. [실전 패턴](#6-실전-패턴)
7. [프레임워크 구현](#7-프레임워크-구현)
8. [Best Practices](#8-best-practices)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Dynamic Proxy

### 1.1 JDK Dynamic Proxy

```java
import java.lang.reflect.*;

interface Calculator {
    int add(int a, int b);
    int subtract(int a, int b);
}

class CalculatorImpl implements Calculator {
    @Override
    public int add(int a, int b) {
        return a + b;
    }
    
    @Override
    public int subtract(int a, int b) {
        return a - b;
    }
}

class LoggingHandler implements InvocationHandler {
    private final Object target;
    
    public LoggingHandler(Object target) {
        this.target = target;
    }
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("호출: " + method.getName());
        System.out.println("파라미터: " + java.util.Arrays.toString(args));
        
        Object result = method.invoke(target, args);
        
        System.out.println("결과: " + result);
        System.out.println();
        
        return result;
    }
}

public class JDKDynamicProxy {
    public static void main(String[] args) {
        System.out.println("=== JDK Dynamic Proxy ===\n");
        
        Calculator calculator = new CalculatorImpl();
        
        // 프록시 생성
        Calculator proxy = (Calculator) Proxy.newProxyInstance(
            Calculator.class.getClassLoader(),
            new Class<?>[] { Calculator.class },
            new LoggingHandler(calculator)
        );
        
        // 프록시를 통한 호출
        proxy.add(10, 5);
        proxy.subtract(20, 8);
        
        
        System.out.println("📌 특징");
        System.out.println("- 인터페이스 필수");
        System.out.println("- InvocationHandler 구현");
        System.out.println("- 런타임에 프록시 생성");
    }
}
```

### 1.2 다양한 InvocationHandler

```java
import java.lang.reflect.*;
import java.util.*;

interface UserService {
    String getUser(Long id);
    void saveUser(String name);
}

class UserServiceImpl implements UserService {
    @Override
    public String getUser(Long id) {
        return "User-" + id;
    }
    
    @Override
    public void saveUser(String name) {
        System.out.println("저장: " + name);
    }
}

// 실행 시간 측정
class TimingHandler implements InvocationHandler {
    private final Object target;
    
    public TimingHandler(Object target) {
        this.target = target;
    }
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        long start = System.nanoTime();
        Object result = method.invoke(target, args);
        long end = System.nanoTime();
        
        System.out.printf("%s: %.2f ms%n", 
            method.getName(), (end - start) / 1_000_000.0);
        
        return result;
    }
}

// 트랜잭션
class TransactionHandler implements InvocationHandler {
    private final Object target;
    
    public TransactionHandler(Object target) {
        this.target = target;
    }
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("트랜잭션 시작");
        
        try {
            Object result = method.invoke(target, args);
            System.out.println("커밋");
            return result;
        } catch (Exception e) {
            System.out.println("롤백");
            throw e;
        }
    }
}

// 캐싱
class CachingHandler implements InvocationHandler {
    private final Object target;
    private final Map<String, Object> cache = new HashMap<>();
    
    public CachingHandler(Object target) {
        this.target = target;
    }
    
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        String key = method.getName() + Arrays.toString(args);
        
        if (cache.containsKey(key)) {
            System.out.println("캐시 히트: " + key);
            return cache.get(key);
        }
        
        Object result = method.invoke(target, args);
        cache.put(key, result);
        System.out.println("캐시 저장: " + key);
        
        return result;
    }
}

public class VariousHandlers {
    public static void main(String[] args) {
        System.out.println("=== 다양한 Handler ===\n");
        
        UserService service = new UserServiceImpl();
        
        // 타이밍
        System.out.println("=== Timing ===");
        UserService timingProxy = (UserService) Proxy.newProxyInstance(
            UserService.class.getClassLoader(),
            new Class<?>[] { UserService.class },
            new TimingHandler(service)
        );
        timingProxy.getUser(1L);
        
        
        // 트랜잭션
        System.out.println("\n=== Transaction ===");
        UserService txProxy = (UserService) Proxy.newProxyInstance(
            UserService.class.getClassLoader(),
            new Class<?>[] { UserService.class },
            new TransactionHandler(service)
        );
        txProxy.saveUser("Alice");
        
        
        // 캐싱
        System.out.println("\n=== Caching ===");
        UserService cacheProxy = (UserService) Proxy.newProxyInstance(
            UserService.class.getClassLoader(),
            new Class<?>[] { UserService.class },
            new CachingHandler(service)
        );
        cacheProxy.getUser(1L);
        cacheProxy.getUser(1L);  // 캐시 히트
    }
}
```

---

## 2. ClassLoader

### 2.1 ClassLoader 기본

```java
public class ClassLoaderBasics {
    public static void main(String[] args) {
        System.out.println("=== ClassLoader ===\n");
        
        // 현재 클래스의 ClassLoader
        ClassLoader classLoader = ClassLoaderBasics.class.getClassLoader();
        System.out.println("현재 ClassLoader: " + classLoader);
        
        // 부모 ClassLoader
        ClassLoader parent = classLoader.getParent();
        System.out.println("부모 ClassLoader: " + parent);
        
        // 시스템 ClassLoader
        ClassLoader systemLoader = ClassLoader.getSystemClassLoader();
        System.out.println("시스템 ClassLoader: " + systemLoader);
        
        
        System.out.println("\n=== ClassLoader 계층 ===");
        System.out.println("Bootstrap ClassLoader");
        System.out.println("  ↓");
        System.out.println("Extension/Platform ClassLoader");
        System.out.println("  ↓");
        System.out.println("Application/System ClassLoader");
        System.out.println("  ↓");
        System.out.println("Custom ClassLoader");
    }
}
```

### 2.2 동적 클래스 로딩

```java
public class DynamicLoading {
    public static void main(String[] args) {
        System.out.println("=== 동적 클래스 로딩 ===\n");
        
        try {
            // 클래스 로딩
            Class<?> clazz = Class.forName("java.util.ArrayList");
            System.out.println("로딩: " + clazz.getName());
            
            // 인스턴스 생성
            Object obj = clazz.getDeclaredConstructor().newInstance();
            System.out.println("생성: " + obj.getClass().getName());
            
            
            // 존재하지 않는 클래스
            System.out.println("\n=== 존재하지 않는 클래스 ===");
            try {
                Class.forName("com.example.NonExistent");
            } catch (ClassNotFoundException e) {
                System.out.println("오류: " + e.getMessage());
            }
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 2.3 커스텀 ClassLoader

```java
import java.io.*;

class SimpleClassLoader extends ClassLoader {
    private String classPath;
    
    public SimpleClassLoader(String classPath) {
        this.classPath = classPath;
    }
    
    @Override
    protected Class<?> findClass(String name) throws ClassNotFoundException {
        try {
            String fileName = name.replace('.', '/') + ".class";
            String fullPath = classPath + "/" + fileName;
            
            byte[] classData = loadClassData(fullPath);
            return defineClass(name, classData, 0, classData.length);
        } catch (IOException e) {
            throw new ClassNotFoundException(name);
        }
    }
    
    private byte[] loadClassData(String path) throws IOException {
        File file = new File(path);
        byte[] data = new byte[(int) file.length()];
        
        try (FileInputStream fis = new FileInputStream(file)) {
            fis.read(data);
        }
        
        return data;
    }
}

public class CustomClassLoader {
    public static void main(String[] args) {
        System.out.println("=== 커스텀 ClassLoader ===\n");
        
        System.out.println("📌 용도");
        System.out.println("- 플러그인 시스템");
        System.out.println("- 핫 스왑");
        System.out.println("- 격리된 클래스 로딩");
        System.out.println("- 암호화된 클래스");
    }
}
```

---

## 3. MethodHandle

### 3.1 MethodHandle 기본

```java
import java.lang.invoke.*;

public class MethodHandleBasic {
    public static int add(int a, int b) {
        return a + b;
    }
    
    public void instanceMethod(String msg) {
        System.out.println("Instance: " + msg);
    }
    
    public static void main(String[] args) throws Throwable {
        System.out.println("=== MethodHandle ===\n");
        
        MethodHandles.Lookup lookup = MethodHandles.lookup();
        
        // static 메서드
        MethodHandle addHandle = lookup.findStatic(
            MethodHandleBasic.class,
            "add",
            MethodType.methodType(int.class, int.class, int.class)
        );
        
        int result = (int) addHandle.invokeExact(10, 5);
        System.out.println("10 + 5 = " + result);
        
        
        // 인스턴스 메서드
        System.out.println("\n=== 인스턴스 메서드 ===");
        MethodHandleBasic obj = new MethodHandleBasic();
        
        MethodHandle instanceHandle = lookup.findVirtual(
            MethodHandleBasic.class,
            "instanceMethod",
            MethodType.methodType(void.class, String.class)
        );
        
        instanceHandle.invokeExact(obj, "Hello");
        
        
        System.out.println("\n📌 Reflection vs MethodHandle");
        System.out.println("Reflection: 느림, 유연함");
        System.out.println("MethodHandle: 빠름, 타입 체크");
    }
}
```

### 3.2 MethodHandle 성능

```java
import java.lang.invoke.*;
import java.lang.reflect.*;

public class MethodHandlePerformance {
    public static int calculate(int a, int b) {
        return a + b;
    }
    
    public static void main(String[] args) throws Throwable {
        System.out.println("=== 성능 비교 ===\n");
        
        int iterations = 10_000_000;
        
        // 직접 호출
        long start = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            calculate(10, 5);
        }
        long directTime = System.nanoTime() - start;
        
        
        // Reflection
        Method method = MethodHandlePerformance.class.getMethod(
            "calculate", int.class, int.class);
        
        start = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            method.invoke(null, 10, 5);
        }
        long reflectionTime = System.nanoTime() - start;
        
        
        // MethodHandle
        MethodHandles.Lookup lookup = MethodHandles.lookup();
        MethodHandle handle = lookup.findStatic(
            MethodHandlePerformance.class,
            "calculate",
            MethodType.methodType(int.class, int.class, int.class)
        );
        
        start = System.nanoTime();
        for (int i = 0; i < iterations; i++) {
            handle.invokeExact(10, 5);
        }
        long handleTime = System.nanoTime() - start;
        
        
        System.out.printf("직접 호출:     %d ms%n", directTime / 1_000_000);
        System.out.printf("Reflection:   %d ms%n", reflectionTime / 1_000_000);
        System.out.printf("MethodHandle: %d ms%n", handleTime / 1_000_000);
        
        System.out.println("\n📌 결과");
        System.out.println("직접 호출 > MethodHandle >> Reflection");
    }
}
```

---

## 4. 성능 최적화

### 4.1 캐싱

```java
import java.lang.reflect.*;
import java.util.*;
import java.util.concurrent.*;

class ReflectionCache {
    private static final Map<String, Method> methodCache = new ConcurrentHashMap<>();
    private static final Map<String, Field> fieldCache = new ConcurrentHashMap<>();
    
    public static Method getMethod(Class<?> clazz, String methodName, Class<?>... paramTypes) {
        String key = clazz.getName() + "#" + methodName + 
                     Arrays.toString(paramTypes);
        
        return methodCache.computeIfAbsent(key, k -> {
            try {
                return clazz.getMethod(methodName, paramTypes);
            } catch (NoSuchMethodException e) {
                throw new RuntimeException(e);
            }
        });
    }
    
    public static Field getField(Class<?> clazz, String fieldName) {
        String key = clazz.getName() + "#" + fieldName;
        
        return fieldCache.computeIfAbsent(key, k -> {
            try {
                Field field = clazz.getDeclaredField(fieldName);
                field.setAccessible(true);
                return field;
            } catch (NoSuchFieldException e) {
                throw new RuntimeException(e);
            }
        });
    }
}

public class CachingOptimization {
    static class Sample {
        private String name = "test";
        
        public void method() {
            System.out.println("method called");
        }
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== 캐싱 최적화 ===\n");
        
        Sample sample = new Sample();
        
        // 캐시 사용
        long start = System.nanoTime();
        for (int i = 0; i < 100_000; i++) {
            Method method = ReflectionCache.getMethod(Sample.class, "method");
            method.invoke(sample);
        }
        long cached = System.nanoTime() - start;
        
        
        // 캐시 미사용
        start = System.nanoTime();
        for (int i = 0; i < 100_000; i++) {
            Method method = Sample.class.getMethod("method");
            method.invoke(sample);
        }
        long uncached = System.nanoTime() - start;
        
        
        System.out.printf("캐시 사용:   %d ms%n", cached / 1_000_000);
        System.out.printf("캐시 미사용: %d ms%n", uncached / 1_000_000);
        System.out.println("\n캐싱으로 " + (uncached / cached) + "배 빠름!");
    }
}
```

### 4.2 setAccessible 최적화

```java
import java.lang.reflect.*;

public class AccessibleOptimization {
    static class Sample {
        private int value1;
        private int value2;
        private int value3;
    }
    
    public static void main(String[] args) throws Exception {
        System.out.println("=== setAccessible 최적화 ===\n");
        
        Sample sample = new Sample();
        Field[] fields = Sample.class.getDeclaredFields();
        
        // ❌ 나쁜 예 - 매번 setAccessible
        long start = System.nanoTime();
        for (int i = 0; i < 1_000_000; i++) {
            for (Field field : fields) {
                field.setAccessible(true);
                field.setInt(sample, 10);
            }
        }
        long badTime = System.nanoTime() - start;
        
        
        // ✅ 좋은 예 - 한 번만 setAccessible
        for (Field field : fields) {
            field.setAccessible(true);
        }
        
        start = System.nanoTime();
        for (int i = 0; i < 1_000_000; i++) {
            for (Field field : fields) {
                field.setInt(sample, 10);
            }
        }
        long goodTime = System.nanoTime() - start;
        
        
        System.out.printf("나쁜 예: %d ms%n", badTime / 1_000_000);
        System.out.printf("좋은 예: %d ms%n", goodTime / 1_000_000);
        System.out.println("\n한 번만 호출하기!");
    }
}
```

---

## 5. 보안 이슈

### 5.1 SecurityManager

```java
public class SecurityIssues {
    static class SecureClass {
        private String secret = "비밀정보";
        
        private void secretMethod() {
            System.out.println("비밀 메서드");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 보안 이슈 ===\n");
        
        System.out.println("📌 위험 요소");
        System.out.println("1. private 접근");
        System.out.println("2. 캡슐화 파괴");
        System.out.println("3. 불변성 위반");
        System.out.println("4. 보안 정책 우회\n");
        
        System.out.println("📌 대응 방법");
        System.out.println("1. SecurityManager 설정");
        System.out.println("2. ReflectPermission 체크");
        System.out.println("3. 입력 검증");
        System.out.println("4. 최소 권한 원칙\n");
        
        System.out.println("⚠️ Reflection 사용 시 주의");
        System.out.println("- 신뢰할 수 있는 코드만");
        System.out.println("- 프레임워크 내부에서만");
        System.out.println("- 사용자 입력으로 클래스명 생성 금지");
    }
}
```

### 5.2 방어 코딩

```java
import java.lang.reflect.*;

public class DefensiveCoding {
    public static Object safeInvoke(Object obj, String methodName, Object... args) {
        try {
            // 1. null 체크
            if (obj == null || methodName == null) {
                throw new IllegalArgumentException("null not allowed");
            }
            
            // 2. 메서드명 검증
            if (!methodName.matches("[a-zA-Z_][a-zA-Z0-9_]*")) {
                throw new IllegalArgumentException("Invalid method name");
            }
            
            // 3. 메서드 찾기
            Class<?> clazz = obj.getClass();
            Method method = null;
            
            for (Method m : clazz.getMethods()) {
                if (m.getName().equals(methodName)) {
                    method = m;
                    break;
                }
            }
            
            if (method == null) {
                throw new NoSuchMethodException(methodName);
            }
            
            // 4. 호출
            return method.invoke(obj, args);
            
        } catch (Exception e) {
            System.err.println("오류: " + e.getMessage());
            return null;
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 방어 코딩 ===\n");
        
        System.out.println("📌 체크리스트");
        System.out.println("✅ null 체크");
        System.out.println("✅ 입력 검증");
        System.out.println("✅ 예외 처리");
        System.out.println("✅ 권한 체크");
        System.out.println("✅ 로깅");
    }
}
```

---

## 6. 실전 패턴

### 6.1 Object Mapper

```java
import java.lang.reflect.*;
import java.util.*;

class ObjectMapper {
    public static <T> T map(Map<String, Object> source, Class<T> targetClass) 
            throws Exception {
        T target = targetClass.getDeclaredConstructor().newInstance();
        
        for (Field field : targetClass.getDeclaredFields()) {
            field.setAccessible(true);
            String fieldName = field.getName();
            
            if (source.containsKey(fieldName)) {
                Object value = source.get(fieldName);
                
                // 타입 변환
                if (value != null) {
                    Class<?> fieldType = field.getType();
                    
                    if (fieldType == int.class || fieldType == Integer.class) {
                        value = Integer.valueOf(value.toString());
                    } else if (fieldType == String.class) {
                        value = value.toString();
                    }
                    
                    field.set(target, value);
                }
            }
        }
        
        return target;
    }
}

class User {
    private String name;
    private int age;
    private String email;
    
    @Override
    public String toString() {
        return "User{name='" + name + "', age=" + age + ", email='" + email + "'}";
    }
}

public class ObjectMapperPattern {
    public static void main(String[] args) throws Exception {
        System.out.println("=== Object Mapper ===\n");
        
        Map<String, Object> data = new HashMap<>();
        data.put("name", "Alice");
        data.put("age", 25);
        data.put("email", "alice@example.com");
        
        User user = ObjectMapper.map(data, User.class);
        System.out.println(user);
    }
}
```

### 6.2 Bean Validation

```java
import java.lang.annotation.*;
import java.lang.reflect.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface NotNull {
    String message() default "Field cannot be null";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Size {
    int min() default 0;
    int max() default Integer.MAX_VALUE;
    String message() default "Size violation";
}

class BeanValidator {
    public static void validate(Object obj) throws Exception {
        Class<?> clazz = obj.getClass();
        
        for (Field field : clazz.getDeclaredFields()) {
            field.setAccessible(true);
            Object value = field.get(obj);
            
            // @NotNull
            if (field.isAnnotationPresent(NotNull.class)) {
                if (value == null) {
                    NotNull ann = field.getAnnotation(NotNull.class);
                    throw new IllegalArgumentException(
                        field.getName() + ": " + ann.message());
                }
            }
            
            // @Size
            if (field.isAnnotationPresent(Size.class)) {
                Size ann = field.getAnnotation(Size.class);
                
                if (value instanceof String) {
                    String str = (String) value;
                    if (str.length() < ann.min() || str.length() > ann.max()) {
                        throw new IllegalArgumentException(
                            field.getName() + ": " + ann.message());
                    }
                }
            }
        }
    }
}

class UserForm {
    @NotNull(message = "이름은 필수입니다")
    private String name;
    
    @NotNull
    @Size(min = 8, max = 20, message = "비밀번호는 8-20자")
    private String password;
    
    public UserForm(String name, String password) {
        this.name = name;
        this.password = password;
    }
}

public class BeanValidationPattern {
    public static void main(String[] args) {
        System.out.println("=== Bean Validation ===\n");
        
        try {
            UserForm valid = new UserForm("Alice", "password123");
            BeanValidator.validate(valid);
            System.out.println("✅ 유효함");
        } catch (Exception e) {
            System.out.println("❌ " + e.getMessage());
        }
        
        try {
            UserForm invalid = new UserForm("Bob", "123");
            BeanValidator.validate(invalid);
            System.out.println("✅ 유효함");
        } catch (Exception e) {
            System.out.println("❌ " + e.getMessage());
        }
    }
}
```

---

## 7. 프레임워크 구현

### 7.1 간단한 ORM

```java
import java.lang.annotation.*;
import java.lang.reflect.*;
import java.util.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Table {
    String name();
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Column {
    String name() default "";
}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Id {}

@Table(name = "users")
class UserEntity {
    @Id
    @Column(name = "user_id")
    private Long id;
    
    @Column(name = "user_name")
    private String name;
    
    @Column
    private int age;
    
    public UserEntity(Long id, String name, int age) {
        this.id = id;
        this.name = name;
        this.age = age;
    }
}

class SimpleORM {
    public static String generateInsert(Object entity) throws Exception {
        Class<?> clazz = entity.getClass();
        
        // 테이블명
        Table table = clazz.getAnnotation(Table.class);
        String tableName = table.name();
        
        // 컬럼과 값
        List<String> columns = new ArrayList<>();
        List<String> values = new ArrayList<>();
        
        for (Field field : clazz.getDeclaredFields()) {
            if (field.isAnnotationPresent(Column.class)) {
                field.setAccessible(true);
                
                Column column = field.getAnnotation(Column.class);
                String columnName = column.name().isEmpty() 
                    ? field.getName() 
                    : column.name();
                
                Object value = field.get(entity);
                
                columns.add(columnName);
                values.add(value instanceof String 
                    ? "'" + value + "'" 
                    : String.valueOf(value));
            }
        }
        
        return String.format("INSERT INTO %s (%s) VALUES (%s)",
            tableName,
            String.join(", ", columns),
            String.join(", ", values));
    }
}

public class SimpleORMExample {
    public static void main(String[] args) throws Exception {
        System.out.println("=== 간단한 ORM ===\n");
        
        UserEntity user = new UserEntity(1L, "Alice", 25);
        String sql = SimpleORM.generateInsert(user);
        
        System.out.println(sql);
    }
}
```

### 7.2 간단한 DI 컨테이너

```java
import java.lang.annotation.*;
import java.lang.reflect.*;
import java.util.*;

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
@interface Component {}

@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.FIELD)
@interface Autowired {}

class DIContainer {
    private Map<Class<?>, Object> instances = new HashMap<>();
    
    public void register(Class<?> clazz) throws Exception {
        if (!clazz.isAnnotationPresent(Component.class)) {
            throw new IllegalArgumentException("Not a component");
        }
        
        Object instance = clazz.getDeclaredConstructor().newInstance();
        instances.put(clazz, instance);
    }
    
    public void inject() throws Exception {
        for (Object instance : instances.values()) {
            Class<?> clazz = instance.getClass();
            
            for (Field field : clazz.getDeclaredFields()) {
                if (field.isAnnotationPresent(Autowired.class)) {
                    field.setAccessible(true);
                    Class<?> fieldType = field.getType();
                    
                    Object dependency = instances.get(fieldType);
                    if (dependency == null) {
                        throw new IllegalStateException(
                            "Dependency not found: " + fieldType);
                    }
                    
                    field.set(instance, dependency);
                }
            }
        }
    }
    
    @SuppressWarnings("unchecked")
    public <T> T get(Class<T> clazz) {
        return (T) instances.get(clazz);
    }
}

@Component
class RepositoryImpl {
    public String getData() {
        return "Data from repository";
    }
}

@Component
class ServiceImpl {
    @Autowired
    private RepositoryImpl repository;
    
    public void process() {
        System.out.println("Service: " + repository.getData());
    }
}

public class DIContainerExample {
    public static void main(String[] args) throws Exception {
        System.out.println("=== DI 컨테이너 ===\n");
        
        DIContainer container = new DIContainer();
        
        // 컴포넌트 등록
        container.register(RepositoryImpl.class);
        container.register(ServiceImpl.class);
        
        // 의존성 주입
        container.inject();
        
        // 사용
        ServiceImpl service = container.get(ServiceImpl.class);
        service.process();
    }
}
```

---

## 8. Best Practices

### 8.1 언제 사용하나?

```java
public class WhenToUse {
    public static void main(String[] args) {
        System.out.println("=== Reflection 사용 시기 ===\n");
        
        System.out.println("✅ 사용해야 할 때");
        System.out.println("- 프레임워크 개발");
        System.out.println("- 플러그인 시스템");
        System.out.println("- 테스트 유틸리티");
        System.out.println("- 직렬화/역직렬화");
        System.out.println("- 동적 프록시\n");
        
        System.out.println("❌ 사용하지 말아야 할 때");
        System.out.println("- 일반 애플리케이션 코드");
        System.out.println("- 성능이 중요한 경우");
        System.out.println("- 타입 안전성이 중요한 경우");
        System.out.println("- 대안이 있는 경우\n");
        
        System.out.println("📌 대안");
        System.out.println("- 인터페이스");
        System.out.println("- 제네릭");
        System.out.println("- 람다");
        System.out.println("- 디자인 패턴");
    }
}
```

### 8.2 성능 고려사항

```java
public class PerformanceConsiderations {
    public static void main(String[] args) {
        System.out.println("=== 성능 고려사항 ===\n");
        
        System.out.println("📌 최적화 팁");
        System.out.println("1. Method/Field 캐싱");
        System.out.println("2. setAccessible 한 번만");
        System.out.println("3. MethodHandle 사용 고려");
        System.out.println("4. 불필요한 getDeclared* 피하기");
        System.out.println("5. 예외 처리 최소화\n");
        
        System.out.println("📊 성능 순서");
        System.out.println("직접 호출 > MethodHandle > Reflection\n");
        
        System.out.println("⚠️ 주의");
        System.out.println("- Reflection은 느림");
        System.out.println("- JIT 최적화 어려움");
        System.out.println("- 캐싱으로 개선 가능");
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: Deep Copy

```java
// Reflection으로 깊은 복사
import java.lang.reflect.*;

class Person {
    private String name;
    private int age;
    
    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }
}

public class Problem1 {
    public static <T> T deepCopy(T source) throws Exception {
        // 구현: 모든 필드를 복사
        return null;
    }
    
    public static void main(String[] args) throws Exception {
        Person p1 = new Person("Alice", 25);
        Person p2 = deepCopy(p1);
        
        System.out.println(p1 != p2);  // true (다른 객체)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
@SuppressWarnings("unchecked")
public static <T> T deepCopy(T source) throws Exception {
    Class<?> clazz = source.getClass();
    T copy = (T) clazz.getDeclaredConstructor().newInstance();
    
    for (Field field : clazz.getDeclaredFields()) {
        field.setAccessible(true);
        Object value = field.get(source);
        field.set(copy, value);
    }
    
    return copy;
}
```
</details>

---

### 문제 2: Method Logger

```java
// Dynamic Proxy로 메서드 로깅
import java.lang.reflect.*;

interface Calculator {
    int add(int a, int b);
}

class CalculatorImpl implements Calculator {
    public int add(int a, int b) {
        return a + b;
    }
}

public class Problem2 {
    public static <T> T createLoggingProxy(T target, Class<T> interfaceType) {
        // 구현: 메서드 호출 전후 로그
        return null;
    }
    
    public static void main(String[] args) {
        Calculator calc = new CalculatorImpl();
        Calculator proxy = createLoggingProxy(calc, Calculator.class);
        
        proxy.add(10, 5);
        // [BEFORE] add(10, 5)
        // [AFTER] add = 15
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
@SuppressWarnings("unchecked")
public static <T> T createLoggingProxy(T target, Class<T> interfaceType) {
    return (T) Proxy.newProxyInstance(
        interfaceType.getClassLoader(),
        new Class<?>[] { interfaceType },
        (proxy, method, args) -> {
            System.out.println("[BEFORE] " + method.getName() + 
                             Arrays.toString(args));
            Object result = method.invoke(target, args);
            System.out.println("[AFTER] " + method.getName() + " = " + result);
            return result;
        }
    );
}
```
</details>

---

### 문제 3: Builder 생성기

```java
// Reflection으로 Builder 패턴 구현
import java.lang.reflect.*;

class User {
    private String name;
    private int age;
    
    public User() {}
}

public class Problem3 {
    static class Builder<T> {
        private T instance;
        
        public Builder(Class<T> clazz) throws Exception {
            instance = clazz.getDeclaredConstructor().newInstance();
        }
        
        public Builder<T> set(String fieldName, Object value) throws Exception {
            // 구현: 필드 설정
            return this;
        }
        
        public T build() {
            return instance;
        }
    }
    
    public static void main(String[] args) throws Exception {
        User user = new Builder<>(User.class)
            .set("name", "Alice")
            .set("age", 25)
            .build();
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public Builder<T> set(String fieldName, Object value) throws Exception {
    Field field = instance.getClass().getDeclaredField(fieldName);
    field.setAccessible(true);
    field.set(instance, value);
    return this;
}
```
</details>

---

## 📌 핵심 정리

### Dynamic Proxy
```java
Proxy.newProxyInstance(
    classLoader,
    interfaces,
    invocationHandler
)
```

### MethodHandle
```java
MethodHandles.Lookup lookup = MethodHandles.lookup();
MethodHandle handle = lookup.findStatic(...);
handle.invokeExact(args);
```

### 성능 최적화
```java
// 캐싱
Map<String, Method> cache = new HashMap<>();

// setAccessible 한 번만
field.setAccessible(true);
```

### Best Practices
```
✅ 프레임워크에서만 사용
✅ Method/Field 캐싱
✅ MethodHandle 고려
✅ 예외 처리
❌ 일반 코드에서 사용
❌ 성능 critical path
```

### 보안
```
⚠️ private 접근 가능
⚠️ 캡슐화 파괴
⚠️ 보안 위험
✅ 입력 검증
✅ 권한 체크
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. Annotation](./Reflection-02-Annotation.md)**

</div>
