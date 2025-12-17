# Generics 01. Generic 기본 완전 정복

> 타입 안전성과 재사용성 - Generic의 기초부터 실전까지  
> Java Generics

---

## 📑 목차

1. [Generics 소개](#1-generics-소개)
2. [Generic 클래스](#2-generic-클래스)
3. [Generic 메서드](#3-generic-메서드)
4. [제한된 타입 매개변수](#4-제한된-타입-매개변수)
5. [다중 타입 매개변수](#5-다중-타입-매개변수)
6. [Raw Type](#6-raw-type)
7. [Type Erasure](#7-type-erasure)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Generics 소개

### 1.1 왜 Generics인가?

```java
import java.util.*;

public class WhyGenerics {
    public static void main(String[] args) {
        System.out.println("=== Generic 이전 (Java 5 이전) ===\n");
        
        // Object 사용 - 타입 안전하지 않음
        List list = new ArrayList();
        list.add("Hello");
        list.add(123);
        list.add(new Date());
        
        // 캐스팅 필요
        String str = (String) list.get(0);
        System.out.println(str);
        
        // 런타임 에러!
        try {
            String str2 = (String) list.get(1);  // ClassCastException!
        } catch (ClassCastException e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        
        System.out.println("\n=== Generic 사용 (Java 5+) ===\n");
        
        // 타입 안전
        List<String> stringList = new ArrayList<>();
        stringList.add("Hello");
        // stringList.add(123);  // 컴파일 에러!
        
        // 캐스팅 불필요
        String s = stringList.get(0);
        System.out.println(s);
        
        
        System.out.println("\n=== Generic의 장점 ===");
        System.out.println("✅ 타입 안전성 (컴파일 타임)");
        System.out.println("✅ 캐스팅 불필요");
        System.out.println("✅ 코드 재사용성");
        System.out.println("✅ 가독성 향상");
    }
}
```

### 1.2 Generic 기본 문법

```java
public class GenericSyntax {
    public static void main(String[] args) {
        System.out.println("=== Generic 문법 ===\n");
        
        System.out.println("📌 타입 매개변수 명명 규칙");
        System.out.println("E - Element (컬렉션)");
        System.out.println("K - Key (맵)");
        System.out.println("V - Value (맵)");
        System.out.println("N - Number");
        System.out.println("T - Type");
        System.out.println("S, U, V - 2nd, 3rd, 4th types\n");
        
        System.out.println("📌 사용 형태");
        System.out.println("클래스:   class Box<T> { }");
        System.out.println("인터페이스: interface List<E> { }");
        System.out.println("메서드:   <T> T method(T param) { }\n");
        
        System.out.println("📌 제약");
        System.out.println("extends:  <T extends Number>");
        System.out.println("super:    <T super Integer>");
        System.out.println("와일드카드: <? extends Number>");
    }
}
```

---

## 2. Generic 클래스

### 2.1 단순 Generic 클래스

```java
// Generic 클래스 정의
class Box<T> {
    private T content;
    
    public void set(T content) {
        this.content = content;
    }
    
    public T get() {
        return content;
    }
    
    @Override
    public String toString() {
        return "Box[" + content + "]";
    }
}

public class GenericClassBasic {
    public static void main(String[] args) {
        System.out.println("=== Generic 클래스 ===\n");
        
        // String 타입
        Box<String> stringBox = new Box<>();
        stringBox.set("Hello");
        String str = stringBox.get();  // 캐스팅 불필요
        System.out.println("String Box: " + stringBox);
        
        // Integer 타입
        Box<Integer> intBox = new Box<>();
        intBox.set(42);
        int num = intBox.get();
        System.out.println("Integer Box: " + intBox);
        
        // 커스텀 타입
        Box<java.util.Date> dateBox = new Box<>();
        dateBox.set(new java.util.Date());
        System.out.println("Date Box: " + dateBox);
        
        
        System.out.println("\n=== 타입 안전성 ===");
        Box<String> box = new Box<>();
        box.set("Test");
        // box.set(123);  // 컴파일 에러!
    }
}
```

### 2.2 Generic 클래스 활용

```java
// Pair 클래스
class Pair<K, V> {
    private K key;
    private V value;
    
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() {
        return key;
    }
    
    public V getValue() {
        return value;
    }
    
    @Override
    public String toString() {
        return key + "=" + value;
    }
}

// Generic 스택
class Stack<E> {
    private java.util.List<E> elements = new java.util.ArrayList<>();
    
    public void push(E element) {
        elements.add(element);
    }
    
    public E pop() {
        if (isEmpty()) {
            throw new java.util.EmptyStackException();
        }
        return elements.remove(elements.size() - 1);
    }
    
    public E peek() {
        if (isEmpty()) {
            throw new java.util.EmptyStackException();
        }
        return elements.get(elements.size() - 1);
    }
    
    public boolean isEmpty() {
        return elements.isEmpty();
    }
    
    public int size() {
        return elements.size();
    }
}

public class GenericClassExamples {
    public static void main(String[] args) {
        // Pair 사용
        System.out.println("=== Pair ===");
        Pair<String, Integer> age = new Pair<>("Alice", 25);
        System.out.println(age);
        
        Pair<Integer, String> idName = new Pair<>(1, "Bob");
        System.out.println(idName);
        
        
        // Stack 사용
        System.out.println("\n=== Stack ===");
        Stack<String> stack = new Stack<>();
        stack.push("A");
        stack.push("B");
        stack.push("C");
        
        System.out.println("Size: " + stack.size());
        System.out.println("Peek: " + stack.peek());
        System.out.println("Pop: " + stack.pop());
        System.out.println("Pop: " + stack.pop());
    }
}
```

### 2.3 제네릭 상속

```java
// 부모 클래스
class Container<T> {
    private T value;
    
    public Container(T value) {
        this.value = value;
    }
    
    public T getValue() {
        return value;
    }
    
    public void setValue(T value) {
        this.value = value;
    }
}

// 자식 클래스 - 타입 매개변수 유지
class SpecialContainer<T> extends Container<T> {
    private String description;
    
    public SpecialContainer(T value, String description) {
        super(value);
        this.description = description;
    }
    
    public String getDescription() {
        return description;
    }
}

// 자식 클래스 - 타입 고정
class StringContainer extends Container<String> {
    public StringContainer(String value) {
        super(value);
    }
    
    public int getLength() {
        return getValue().length();
    }
}

public class GenericInheritance {
    public static void main(String[] args) {
        System.out.println("=== Generic 상속 ===\n");
        
        // 타입 매개변수 유지
        SpecialContainer<Integer> special = 
            new SpecialContainer<>(42, "Important number");
        System.out.println("Value: " + special.getValue());
        System.out.println("Description: " + special.getDescription());
        
        
        // 타입 고정
        System.out.println("\n=== 타입 고정 ===");
        StringContainer strContainer = new StringContainer("Hello");
        System.out.println("Value: " + strContainer.getValue());
        System.out.println("Length: " + strContainer.getLength());
    }
}
```

---

## 3. Generic 메서드

### 3.1 기본 Generic 메서드

```java
public class GenericMethodBasic {
    // Generic 메서드
    public static <T> void printArray(T[] array) {
        for (T element : array) {
            System.out.print(element + " ");
        }
        System.out.println();
    }
    
    // 반환 타입이 있는 Generic 메서드
    public static <T> T getFirst(T[] array) {
        if (array.length == 0) {
            return null;
        }
        return array[0];
    }
    
    // 다중 타입 매개변수
    public static <K, V> void printPair(K key, V value) {
        System.out.println(key + " = " + value);
    }
    
    public static void main(String[] args) {
        System.out.println("=== Generic 메서드 ===\n");
        
        // Integer 배열
        Integer[] intArray = {1, 2, 3, 4, 5};
        System.out.print("Integer 배열: ");
        printArray(intArray);
        
        // String 배열
        String[] strArray = {"A", "B", "C"};
        System.out.print("String 배열: ");
        printArray(strArray);
        
        
        // getFirst
        System.out.println("\n첫 번째 요소");
        System.out.println("Integer: " + getFirst(intArray));
        System.out.println("String: " + getFirst(strArray));
        
        
        // 다중 타입
        System.out.println("\n다중 타입");
        printPair("name", "Alice");
        printPair(1, "First");
    }
}
```

### 3.2 Generic 메서드 활용

```java
import java.util.*;

public class GenericMethodExamples {
    // 리스트 역순
    public static <T> List<T> reverse(List<T> list) {
        List<T> result = new ArrayList<>();
        for (int i = list.size() - 1; i >= 0; i--) {
            result.add(list.get(i));
        }
        return result;
    }
    
    // 두 리스트 합치기
    public static <T> List<T> merge(List<T> list1, List<T> list2) {
        List<T> result = new ArrayList<>(list1);
        result.addAll(list2);
        return result;
    }
    
    // 최대값 찾기
    public static <T extends Comparable<T>> T max(T a, T b) {
        return a.compareTo(b) > 0 ? a : b;
    }
    
    // 리스트에서 최대값
    public static <T extends Comparable<T>> T max(List<T> list) {
        if (list.isEmpty()) {
            return null;
        }
        T max = list.get(0);
        for (T element : list) {
            if (element.compareTo(max) > 0) {
                max = element;
            }
        }
        return max;
    }
    
    public static void main(String[] args) {
        // reverse
        System.out.println("=== 역순 ===");
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        System.out.println("원본: " + numbers);
        System.out.println("역순: " + reverse(numbers));
        
        
        // merge
        System.out.println("\n=== 합치기 ===");
        List<String> list1 = Arrays.asList("A", "B");
        List<String> list2 = Arrays.asList("C", "D");
        System.out.println("합침: " + merge(list1, list2));
        
        
        // max
        System.out.println("\n=== 최대값 ===");
        System.out.println("max(10, 20): " + max(10, 20));
        System.out.println("max('A', 'Z'): " + max('A', 'Z'));
        System.out.println("리스트 최대값: " + max(numbers));
    }
}
```

---

## 4. 제한된 타입 매개변수

### 4.1 상한 제한 (extends)

```java
// Number의 하위 타입만 허용
class NumberBox<T extends Number> {
    private T value;
    
    public NumberBox(T value) {
        this.value = value;
    }
    
    public double doubleValue() {
        return value.doubleValue();
    }
    
    public T getValue() {
        return value;
    }
}

// Comparable 구현 타입만 허용
class SortedBox<T extends Comparable<T>> {
    private java.util.List<T> items = new java.util.ArrayList<>();
    
    public void add(T item) {
        items.add(item);
        items.sort(Comparable::compareTo);
    }
    
    public T getFirst() {
        return items.isEmpty() ? null : items.get(0);
    }
    
    public java.util.List<T> getAll() {
        return new java.util.ArrayList<>(items);
    }
}

public class BoundedTypeParameter {
    public static void main(String[] args) {
        System.out.println("=== 상한 제한 (extends) ===\n");
        
        // NumberBox
        NumberBox<Integer> intBox = new NumberBox<>(42);
        NumberBox<Double> doubleBox = new NumberBox<>(3.14);
        // NumberBox<String> strBox = new NumberBox<>("Hi");  // 컴파일 에러!
        
        System.out.println("Integer: " + intBox.doubleValue());
        System.out.println("Double: " + doubleBox.doubleValue());
        
        
        // SortedBox
        System.out.println("\n=== SortedBox ===");
        SortedBox<Integer> sortedBox = new SortedBox<>();
        sortedBox.add(5);
        sortedBox.add(2);
        sortedBox.add(8);
        sortedBox.add(1);
        
        System.out.println("정렬됨: " + sortedBox.getAll());
        System.out.println("최소값: " + sortedBox.getFirst());
    }
}
```

### 4.2 다중 제약

```java
// 다중 인터페이스 구현
interface Printable {
    void print();
}

interface Closeable {
    void close();
}

// 여러 타입 제약
class Resource<T extends Printable & Closeable> {
    private T resource;
    
    public Resource(T resource) {
        this.resource = resource;
    }
    
    public void use() {
        resource.print();
        resource.close();
    }
}

class Document implements Printable, Closeable {
    private String content;
    
    Document(String content) {
        this.content = content;
    }
    
    @Override
    public void print() {
        System.out.println("Printing: " + content);
    }
    
    @Override
    public void close() {
        System.out.println("Closing document");
    }
}

public class MultipleBounds {
    // 메서드도 다중 제약 가능
    public static <T extends Comparable<T> & Printable> void processAndSort(
        java.util.List<T> list
    ) {
        list.sort(Comparable::compareTo);
        list.forEach(Printable::print);
    }
    
    public static void main(String[] args) {
        System.out.println("=== 다중 제약 ===\n");
        
        Document doc = new Document("Hello World");
        Resource<Document> resource = new Resource<>(doc);
        resource.use();
        
        
        System.out.println("\n📌 제약 순서");
        System.out.println("클래스가 먼저, 인터페이스가 나중");
        System.out.println("<T extends Class & Interface1 & Interface2>");
    }
}
```

---

## 5. 다중 타입 매개변수

### 5.1 2개 타입 매개변수

```java
class Pair<K, V> {
    private K key;
    private V value;
    
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() { return key; }
    public V getValue() { return value; }
    
    public void setKey(K key) { this.key = key; }
    public void setValue(V value) { this.value = value; }
    
    @Override
    public String toString() {
        return "(" + key + ", " + value + ")";
    }
}

public class MultipleTypeParameters {
    // 두 값 교환
    public static <T, U> Pair<U, T> swap(Pair<T, U> pair) {
        return new Pair<>(pair.getValue(), pair.getKey());
    }
    
    public static void main(String[] args) {
        System.out.println("=== 다중 타입 매개변수 ===\n");
        
        Pair<String, Integer> p1 = new Pair<>("Age", 25);
        System.out.println("원본: " + p1);
        
        Pair<Integer, String> p2 = swap(p1);
        System.out.println("교환: " + p2);
        
        
        // 다양한 조합
        Pair<Integer, String> idName = new Pair<>(1, "Alice");
        Pair<String, Double> productPrice = new Pair<>("Apple", 1.99);
        
        System.out.println("\n" + idName);
        System.out.println(productPrice);
    }
}
```

### 5.2 3개 이상 타입 매개변수

```java
class Triple<A, B, C> {
    private A first;
    private B second;
    private C third;
    
    public Triple(A first, B second, C third) {
        this.first = first;
        this.second = second;
        this.third = third;
    }
    
    public A getFirst() { return first; }
    public B getSecond() { return second; }
    public C getThird() { return third; }
    
    @Override
    public String toString() {
        return "(" + first + ", " + second + ", " + third + ")";
    }
}

public class TripleTypeParameters {
    public static void main(String[] args) {
        System.out.println("=== 3개 타입 매개변수 ===\n");
        
        Triple<String, Integer, Boolean> t1 = 
            new Triple<>("Alice", 25, true);
        System.out.println(t1);
        
        Triple<Integer, Double, String> t2 = 
            new Triple<>(1, 99.99, "Product");
        System.out.println(t2);
        
        
        System.out.println("\n실전에서는 2개까지가 일반적");
        System.out.println("3개 이상은 가독성 저하");
        System.out.println("→ 커스텀 클래스 사용 권장");
    }
}
```

---

## 6. Raw Type

### 6.1 Raw Type이란?

```java
import java.util.*;

public class RawTypeExample {
    public static void main(String[] args) {
        System.out.println("=== Raw Type ===\n");
        
        System.out.println("정의: 타입 매개변수 없이 사용");
        System.out.println("예: List list = new ArrayList();\n");
        
        
        // Raw Type (피해야 함)
        List rawList = new ArrayList();
        rawList.add("String");
        rawList.add(123);
        rawList.add(new Date());
        
        // 캐스팅 필요, 런타임 에러 가능
        try {
            String s = (String) rawList.get(1);  // ClassCastException
        } catch (ClassCastException e) {
            System.out.println("오류: " + e.getMessage());
        }
        
        
        // Generic Type (권장)
        List<String> genericList = new ArrayList<>();
        genericList.add("String");
        // genericList.add(123);  // 컴파일 에러!
        
        String s = genericList.get(0);  // 캐스팅 불필요
        
        
        System.out.println("\n⚠️ Raw Type 문제점");
        System.out.println("1. 타입 안전성 상실");
        System.out.println("2. 런타임 에러 위험");
        System.out.println("3. 캐스팅 필요");
        System.out.println("\n❌ 사용 금지! (하위 호환성 목적으로만 존재)");
    }
}
```

### 6.2 Raw Type 경고

```java
import java.util.*;

public class RawTypeWarnings {
    // Raw Type 사용 시 경고
    @SuppressWarnings("rawtypes")
    public static void badExample() {
        List list = new ArrayList();  // Warning!
        list.add("Hello");
    }
    
    // 올바른 사용
    public static void goodExample() {
        List<String> list = new ArrayList<>();  // OK
        list.add("Hello");
    }
    
    public static void main(String[] args) {
        System.out.println("=== Raw Type 경고 ===\n");
        
        System.out.println("컴파일 시 경고:");
        System.out.println("Note: uses unchecked or unsafe operations.");
        System.out.println("Note: Recompile with -Xlint:unchecked\n");
        
        System.out.println("해결 방법:");
        System.out.println("1. Generic 사용");
        System.out.println("2. @SuppressWarnings(\"unchecked\") (최후 수단)");
        
        badExample();
        goodExample();
    }
}
```

---

## 7. Type Erasure

### 7.1 Type Erasure란?

```java
import java.util.*;

public class TypeErasureExplanation {
    public static void main(String[] args) {
        System.out.println("=== Type Erasure ===\n");
        
        System.out.println("📌 정의");
        System.out.println("컴파일 시 제네릭 타입 정보 제거");
        System.out.println("런타임에는 타입 정보 없음\n");
        
        System.out.println("📌 과정");
        System.out.println("1. 제네릭 타입 → Object (또는 bound)");
        System.out.println("2. 타입 캐스팅 삽입");
        System.out.println("3. Bridge 메서드 생성\n");
        
        System.out.println("📌 예시");
        System.out.println("컴파일 전: List<String>");
        System.out.println("컴파일 후: List (Object)\n");
        
        System.out.println("📌 제약");
        System.out.println("new T() - 불가");
        System.out.println("T.class - 불가");
        System.out.println("instanceof T - 불가");
        System.out.println("T[] 배열 생성 - 제한적");
    }
}
```

### 7.2 Type Erasure 제약

```java
import java.util.*;

public class TypeErasureConstraints {
    static class Box<T> {
        // ❌ 불가능
        // public void create() {
        //     T obj = new T();  // 컴파일 에러!
        // }
        
        // ❌ 불가능
        // public void checkType(Object obj) {
        //     if (obj instanceof T) { }  // 컴파일 에러!
        // }
        
        // ⚠️ 제한적
        // private T[] array = new T[10];  // 컴파일 에러!
        
        // ✅ 가능 (우회)
        private List<T> list = new ArrayList<>();
        
        @SuppressWarnings("unchecked")
        public T[] createArray(int size) {
            return (T[]) new Object[size];  // 경고
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Type Erasure 제약 ===\n");
        
        List<String> list1 = new ArrayList<>();
        List<Integer> list2 = new ArrayList<>();
        
        // 런타임에는 같은 타입!
        System.out.println("같은 클래스? " + 
            (list1.getClass() == list2.getClass()));  // true
        
        System.out.println("\n클래스: " + list1.getClass().getName());
        System.out.println("타입 정보 없음!");
    }
}
```

---

## 8. 실전 예제

### 8.1 Generic Repository

```java
import java.util.*;

interface Repository<T, ID> {
    void save(T entity);
    Optional<T> findById(ID id);
    List<T> findAll();
    void delete(ID id);
}

class User {
    private Long id;
    private String name;
    
    User(Long id, String name) {
        this.id = id;
        this.name = name;
    }
    
    Long getId() { return id; }
    String getName() { return name; }
    
    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + "'}";
    }
}

class UserRepository implements Repository<User, Long> {
    private Map<Long, User> storage = new HashMap<>();
    
    @Override
    public void save(User user) {
        storage.put(user.getId(), user);
    }
    
    @Override
    public Optional<User> findById(Long id) {
        return Optional.ofNullable(storage.get(id));
    }
    
    @Override
    public List<User> findAll() {
        return new ArrayList<>(storage.values());
    }
    
    @Override
    public void delete(Long id) {
        storage.remove(id);
    }
}

public class GenericRepositoryExample {
    public static void main(String[] args) {
        UserRepository repo = new UserRepository();
        
        // 저장
        repo.save(new User(1L, "Alice"));
        repo.save(new User(2L, "Bob"));
        repo.save(new User(3L, "Charlie"));
        
        // 조회
        System.out.println("=== 전체 조회 ===");
        repo.findAll().forEach(System.out::println);
        
        // ID로 조회
        System.out.println("\n=== ID 조회 ===");
        repo.findById(2L).ifPresent(System.out::println);
        
        // 삭제
        repo.delete(2L);
        System.out.println("\n=== 삭제 후 ===");
        repo.findAll().forEach(System.out::println);
    }
}
```

### 8.2 Generic Builder

```java
class Builder<T> {
    private T object;
    
    public Builder(T object) {
        this.object = object;
    }
    
    public <U> Builder<T> with(
        java.util.function.BiConsumer<T, U> setter, 
        U value
    ) {
        setter.accept(object, value);
        return this;
    }
    
    public T build() {
        return object;
    }
    
    public static <T> Builder<T> of(java.util.function.Supplier<T> supplier) {
        return new Builder<>(supplier.get());
    }
}

class Person {
    private String name;
    private int age;
    private String email;
    
    public void setName(String name) { this.name = name; }
    public void setAge(int age) { this.age = age; }
    public void setEmail(String email) { this.email = email; }
    
    @Override
    public String toString() {
        return "Person{name='" + name + "', age=" + age + ", email='" + email + "'}";
    }
}

public class GenericBuilderExample {
    public static void main(String[] args) {
        Person person = Builder.of(Person::new)
            .with(Person::setName, "Alice")
            .with(Person::setAge, 25)
            .with(Person::setEmail, "alice@example.com")
            .build();
        
        System.out.println(person);
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: Generic Stack

```java
// Generic Stack 구현
class Stack<E> {
    // 구현
    
    public void push(E element) { }
    public E pop() { return null; }
    public E peek() { return null; }
    public boolean isEmpty() { return true; }
    public int size() { return 0; }
}

public class Problem1 {
    public static void main(String[] args) {
        Stack<Integer> stack = new Stack<>();
        stack.push(1);
        stack.push(2);
        stack.push(3);
        
        System.out.println(stack.pop());  // 3
        System.out.println(stack.peek()); // 2
        System.out.println(stack.size()); // 2
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
class Stack<E> {
    private java.util.List<E> elements = new java.util.ArrayList<>();
    
    public void push(E element) {
        elements.add(element);
    }
    
    public E pop() {
        if (isEmpty()) {
            throw new java.util.EmptyStackException();
        }
        return elements.remove(elements.size() - 1);
    }
    
    public E peek() {
        if (isEmpty()) {
            throw new java.util.EmptyStackException();
        }
        return elements.get(elements.size() - 1);
    }
    
    public boolean isEmpty() {
        return elements.isEmpty();
    }
    
    public int size() {
        return elements.size();
    }
}
```
</details>

---

### 문제 2: 최대값 찾기

```java
// Generic 메서드로 최대값 찾기
public class Problem2 {
    public static <T extends Comparable<T>> T findMax(T[] array) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        Integer[] numbers = {3, 1, 4, 1, 5, 9, 2, 6};
        System.out.println("Max: " + findMax(numbers));  // 9
        
        String[] words = {"apple", "banana", "cherry"};
        System.out.println("Max: " + findMax(words));  // cherry
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static <T extends Comparable<T>> T findMax(T[] array) {
    if (array == null || array.length == 0) {
        return null;
    }
    
    T max = array[0];
    for (int i = 1; i < array.length; i++) {
        if (array[i].compareTo(max) > 0) {
            max = array[i];
        }
    }
    return max;
}
```
</details>

---

### 문제 3: Generic Pair

```java
// Pair 클래스 완성
class Pair<K, V> {
    // 구현
}

public class Problem3 {
    // swap 메서드 구현
    public static <K, V> Pair<V, K> swap(Pair<K, V> pair) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        Pair<String, Integer> p1 = new Pair<>("Age", 25);
        Pair<Integer, String> p2 = swap(p1);
        
        System.out.println(p2.getKey() + " = " + p2.getValue());  // 25 = Age
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
class Pair<K, V> {
    private K key;
    private V value;
    
    public Pair(K key, V value) {
        this.key = key;
        this.value = value;
    }
    
    public K getKey() { return key; }
    public V getValue() { return value; }
}

public static <K, V> Pair<V, K> swap(Pair<K, V> pair) {
    return new Pair<>(pair.getValue(), pair.getKey());
}
```
</details>

---

## 📌 핵심 정리

### Generic 클래스
```java
class Box<T> {
    private T value;
    public T get() { return value; }
}
```

### Generic 메서드
```java
public static <T> void print(T value) { }
public static <T extends Comparable<T>> T max(T a, T b) { }
```

### 제약
```java
<T extends Number>              // 상한 제한
<T extends Class & Interface>   // 다중 제약
```

### 다중 타입
```java
class Pair<K, V> { }
public <T, U> void method(T t, U u) { }
```

### 주의사항
```
❌ Raw Type 사용 금지
❌ new T() 불가
❌ T.class 불가
❌ instanceof T 불가
✅ 컴파일 타임 타입 체크
✅ 런타임에는 타입 정보 없음
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Wildcard →](./Generics-02-Wildcard.md)**

</div>
