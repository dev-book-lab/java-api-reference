# Util 01. Comparator & Comparable 완전 정복

> 정렬과 비교의 모든 것 - Comparator와 Comparable 마스터하기  
> Java API Reference

---

## 📑 목차

1. [기본 개념](#1-기본-개념)
2. [Comparable 인터페이스](#2-comparable-인터페이스)
3. [Comparator 인터페이스](#3-comparator-인터페이스)
4. [Comparator 메서드 체이닝](#4-comparator-메서드-체이닝)
5. [null 처리](#5-null-처리)
6. [실전 활용](#6-실전-활용)
7. [성능 최적화](#7-성능-최적화)
8. [실전 연습 문제](#8-실전-연습-문제)

---

## 1. 기본 개념

### 1.1 왜 필요한가?

```java
import java.util.*;

public class WhyComparator {
    public static void main(String[] args) {
        System.out.println("=== 비교의 필요성 ===\n");
        
        System.out.println("1. 정렬");
        System.out.println("   - 어떤 기준으로 정렬?");
        System.out.println("   - 오름차순? 내림차순?\n");
        
        System.out.println("2. 탐색");
        System.out.println("   - 이진 탐색 전 정렬 필요");
        System.out.println("   - 비교 기준 통일\n");
        
        System.out.println("3. 중복 제거");
        System.out.println("   - TreeSet, TreeMap");
        System.out.println("   - 어떤 기준으로 같다고 볼 것인가?\n");
        
        System.out.println("=== 두 가지 방법 ===");
        System.out.println("Comparable: 자연스러운 순서 (Natural Order)");
        System.out.println("Comparator: 다양한 정렬 기준");
    }
}
```

### 1.2 차이점

```java
public class ComparableVsComparator {
    public static void main(String[] args) {
        System.out.println("=== Comparable vs Comparator ===\n");
        
        System.out.println("📌 Comparable");
        System.out.println("- 위치: java.lang");
        System.out.println("- 메서드: compareTo(T o)");
        System.out.println("- 사용: 클래스 자체에 구현");
        System.out.println("- 목적: 기본 정렬 기준");
        System.out.println("- 개수: 1개 (자연 순서)\n");
        
        System.out.println("📌 Comparator");
        System.out.println("- 위치: java.util");
        System.out.println("- 메서드: compare(T o1, T o2)");
        System.out.println("- 사용: 별도 클래스/람다");
        System.out.println("- 목적: 다양한 정렬 기준");
        System.out.println("- 개수: 여러 개 가능\n");
        
        System.out.println("=== 선택 기준 ===");
        System.out.println("Comparable: 명확한 기본 순서가 있을 때");
        System.out.println("Comparator: 여러 정렬 방식이 필요할 때");
    }
}
```

---

## 2. Comparable 인터페이스

### 2.1 기본 구현

```java
public class ComparableBasic {
    static class Student implements Comparable<Student> {
        String name;
        int score;
        
        Student(String name, int score) {
            this.name = name;
            this.score = score;
        }
        
        @Override
        public int compareTo(Student other) {
            // 점수 기준 오름차순
            return this.score - other.score;
        }
        
        @Override
        public String toString() {
            return name + "(" + score + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Student> students = new ArrayList<>();
        students.add(new Student("Alice", 85));
        students.add(new Student("Bob", 92));
        students.add(new Student("Charlie", 78));
        
        System.out.println("정렬 전: " + students);
        
        Collections.sort(students);
        System.out.println("정렬 후: " + students);
        // [Charlie(78), Alice(85), Bob(92)]
    }
}
```

### 2.2 compareTo 반환값 규칙

```java
public class CompareToRules {
    public static void main(String[] args) {
        System.out.println("=== compareTo 반환값 ===\n");
        
        System.out.println("음수 (< 0):");
        System.out.println("  - this < other");
        System.out.println("  - this가 앞으로\n");
        
        System.out.println("0:");
        System.out.println("  - this == other");
        System.out.println("  - 순서 변경 없음\n");
        
        System.out.println("양수 (> 0):");
        System.out.println("  - this > other");
        System.out.println("  - this가 뒤로\n");
        
        System.out.println("=== 예제 ===");
        System.out.println("오름차순: return this.value - other.value");
        System.out.println("내림차순: return other.value - this.value");
    }
}
```

### 2.3 다양한 구현 패턴

```java
public class ComparablePatterns {
    // 패턴 1: 숫자 비교
    static class Product implements Comparable<Product> {
        String name;
        int price;
        
        Product(String name, int price) {
            this.name = name;
            this.price = price;
        }
        
        @Override
        public int compareTo(Product other) {
            // Integer.compare 사용 (오버플로우 안전)
            return Integer.compare(this.price, other.price);
        }
        
        @Override
        public String toString() {
            return name + "(" + price + "원)";
        }
    }
    
    // 패턴 2: 문자열 비교
    static class Book implements Comparable<Book> {
        String title;
        String author;
        
        Book(String title, String author) {
            this.title = title;
            this.author = author;
        }
        
        @Override
        public int compareTo(Book other) {
            // 문자열은 compareTo 사용
            return this.title.compareTo(other.title);
        }
        
        @Override
        public String toString() {
            return title + " by " + author;
        }
    }
    
    // 패턴 3: 복합 비교
    static class Person implements Comparable<Person> {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public int compareTo(Person other) {
            // 나이 비교, 같으면 이름 비교
            int ageCompare = Integer.compare(this.age, other.age);
            if (ageCompare != 0) {
                return ageCompare;
            }
            return this.name.compareTo(other.name);
        }
        
        @Override
        public String toString() {
            return name + "(" + age + "세)";
        }
    }
    
    public static void main(String[] args) {
        // Product 테스트
        List<Product> products = Arrays.asList(
            new Product("Apple", 1000),
            new Product("Banana", 500),
            new Product("Cherry", 1500)
        );
        Collections.sort(products);
        System.out.println("가격순: " + products);
        
        
        // Book 테스트
        List<Book> books = Arrays.asList(
            new Book("Java", "Author A"),
            new Book("Python", "Author B"),
            new Book("C++", "Author C")
        );
        Collections.sort(books);
        System.out.println("\n제목순: " + books);
        
        
        // Person 테스트
        List<Person> people = Arrays.asList(
            new Person("Alice", 25),
            new Person("Bob", 25),
            new Person("Charlie", 20)
        );
        Collections.sort(people);
        System.out.println("\n나이/이름순: " + people);
    }
}
```

### 2.4 Comparable 주의사항

```java
public class ComparableCautions {
    // ❌ 잘못된 예: 오버플로우
    static class BadExample implements Comparable<BadExample> {
        int value;
        
        BadExample(int value) {
            this.value = value;
        }
        
        @Override
        public int compareTo(BadExample other) {
            // 큰 수에서 오버플로우 위험!
            return this.value - other.value;
        }
    }
    
    // ✅ 올바른 예: Integer.compare 사용
    static class GoodExample implements Comparable<GoodExample> {
        int value;
        
        GoodExample(int value) {
            this.value = value;
        }
        
        @Override
        public int compareTo(GoodExample other) {
            return Integer.compare(this.value, other.value);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 주의사항 ===\n");
        
        System.out.println("1. 오버플로우");
        System.out.println("   ❌ return a - b");
        System.out.println("   ✅ return Integer.compare(a, b)\n");
        
        System.out.println("2. null 처리");
        System.out.println("   - null 체크 필요");
        System.out.println("   - NullPointerException 방지\n");
        
        System.out.println("3. 일관성");
        System.out.println("   - compareTo와 equals 일관성");
        System.out.println("   - TreeSet, TreeMap 사용 시 중요");
    }
}
```

---

## 3. Comparator 인터페이스

### 3.1 기본 사용법

```java
public class ComparatorBasic {
    static class Student {
        String name;
        int score;
        
        Student(String name, int score) {
            this.name = name;
            this.score = score;
        }
        
        @Override
        public String toString() {
            return name + "(" + score + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", 85),
            new Student("Bob", 92),
            new Student("Charlie", 78)
        );
        
        System.out.println("원본: " + students);
        
        
        // 방법 1: 익명 클래스
        Collections.sort(students, new Comparator<Student>() {
            @Override
            public int compare(Student s1, Student s2) {
                return Integer.compare(s1.score, s2.score);
            }
        });
        System.out.println("점수 오름차순: " + students);
        
        
        // 방법 2: 람다
        Collections.sort(students, (s1, s2) -> 
            Integer.compare(s2.score, s1.score));
        System.out.println("점수 내림차순: " + students);
        
        
        // 방법 3: Comparator 정적 메서드
        Collections.sort(students, 
            Comparator.comparingInt(s -> s.score));
        System.out.println("점수 오름차순: " + students);
    }
}
```

### 3.2 Comparator 정적 메서드

```java
public class ComparatorStaticMethods {
    static class Person {
        String name;
        int age;
        double height;
        
        Person(String name, int age, double height) {
            this.name = name;
            this.age = age;
            this.height = height;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + "세, " + height + "cm)";
        }
    }
    
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Alice", 25, 165.5),
            new Person("Bob", 30, 175.0),
            new Person("Charlie", 25, 170.0)
        );
        
        // comparingInt
        Comparator<Person> byAge = Comparator.comparingInt(p -> p.age);
        System.out.println("나이순: " + people.stream()
            .sorted(byAge)
            .toList());
        
        
        // comparingDouble
        Comparator<Person> byHeight = Comparator.comparingDouble(p -> p.height);
        System.out.println("키순: " + people.stream()
            .sorted(byHeight)
            .toList());
        
        
        // comparing (일반)
        Comparator<Person> byName = Comparator.comparing(p -> p.name);
        System.out.println("이름순: " + people.stream()
            .sorted(byName)
            .toList());
        
        
        // naturalOrder
        List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9);
        System.out.println("\n자연 순서: " + numbers.stream()
            .sorted(Comparator.naturalOrder())
            .toList());
        
        
        // reverseOrder
        System.out.println("역순: " + numbers.stream()
            .sorted(Comparator.reverseOrder())
            .toList());
    }
}
```

### 3.3 역순 정렬

```java
public class ReverseOrder {
    static class Student {
        String name;
        int score;
        
        Student(String name, int score) {
            this.name = name;
            this.score = score;
        }
        
        @Override
        public String toString() {
            return name + "(" + score + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", 85),
            new Student("Bob", 92),
            new Student("Charlie", 78)
        );
        
        // 방법 1: reversed()
        Comparator<Student> byScore = Comparator.comparingInt(s -> s.score);
        students.sort(byScore.reversed());
        System.out.println("reversed(): " + students);
        
        
        // 방법 2: 음수 반환
        students.sort((s1, s2) -> Integer.compare(s2.score, s1.score));
        System.out.println("음수 반환: " + students);
        
        
        // 방법 3: Collections.reverseOrder
        students.sort(Collections.reverseOrder(byScore));
        System.out.println("reverseOrder: " + students);
    }
}
```

---

## 4. Comparator 메서드 체이닝

### 4.1 thenComparing

```java
public class ThenComparing {
    static class Student {
        String name;
        int score;
        int age;
        
        Student(String name, int score, int age) {
            this.name = name;
            this.score = score;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + score + "점, " + age + "세)";
        }
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", 90, 25),
            new Student("Bob", 90, 22),
            new Student("Charlie", 85, 23),
            new Student("David", 90, 24)
        );
        
        // 점수 높은 순, 같으면 나이 어린 순
        Comparator<Student> comp = Comparator
            .comparingInt((Student s) -> s.score)
            .reversed()
            .thenComparingInt(s -> s.age);
        
        students.sort(comp);
        System.out.println("점수 내림차순, 나이 오름차순:");
        students.forEach(System.out::println);
        
        
        // 3단계 정렬
        comp = Comparator
            .comparingInt((Student s) -> -s.score)  // 점수 내림차순
            .thenComparingInt(s -> s.age)           // 나이 오름차순
            .thenComparing(s -> s.name);            // 이름 오름차순
        
        students.sort(comp);
        System.out.println("\n3단계 정렬:");
        students.forEach(System.out::println);
    }
}
```

### 4.2 복잡한 체이닝

```java
public class ComplexChaining {
    static class Employee {
        String name;
        String department;
        int salary;
        int experience;
        
        Employee(String name, String department, int salary, int experience) {
            this.name = name;
            this.department = department;
            this.salary = salary;
            this.experience = experience;
        }
        
        @Override
        public String toString() {
            return String.format("%s (%s, %d만원, %d년)",
                name, department, salary, experience);
        }
    }
    
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Alice", "Dev", 5000, 5),
            new Employee("Bob", "Dev", 5000, 3),
            new Employee("Charlie", "Sales", 4500, 5),
            new Employee("David", "Dev", 5000, 5)
        );
        
        // 부서 → 연봉 내림차순 → 경력 내림차순 → 이름 오름차순
        Comparator<Employee> comp = Comparator
            .comparing((Employee e) -> e.department)
            .thenComparing(Comparator.comparingInt((Employee e) -> e.salary).reversed())
            .thenComparing(Comparator.comparingInt((Employee e) -> e.experience).reversed())
            .thenComparing(e -> e.name);
        
        employees.sort(comp);
        
        System.out.println("=== 정렬 결과 ===");
        employees.forEach(System.out::println);
    }
}
```

---

## 5. null 처리

### 5.1 nullsFirst / nullsLast

```java
public class NullHandling {
    static class Product {
        String name;
        Integer price;  // null 가능
        
        Product(String name, Integer price) {
            this.name = name;
            this.price = price;
        }
        
        @Override
        public String toString() {
            return name + "(" + (price != null ? price + "원" : "가격미정") + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Product> products = Arrays.asList(
            new Product("Apple", 1000),
            new Product("Banana", null),
            new Product("Cherry", 1500),
            new Product("Durian", null),
            new Product("Elderberry", 500)
        );
        
        System.out.println("원본: " + products);
        
        
        // nullsFirst (null이 앞으로)
        Comparator<Product> comp1 = Comparator
            .nullsFirst(Comparator.comparingInt((Product p) -> p.price));
        products.sort(comp1);
        System.out.println("\nnullsFirst: " + products);
        
        
        // nullsLast (null이 뒤로)
        Comparator<Product> comp2 = Comparator
            .comparing(p -> p.price, Comparator.nullsLast(Comparator.naturalOrder()));
        products.sort(comp2);
        System.out.println("nullsLast: " + products);
    }
}
```

### 5.2 안전한 비교

```java
public class SafeComparison {
    static class Person {
        String name;
        Integer age;
        String city;
        
        Person(String name, Integer age, String city) {
            this.name = name;
            this.age = age;
            this.city = city;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + "세, " + city + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Person> people = Arrays.asList(
            new Person("Alice", 25, "Seoul"),
            new Person("Bob", null, "Busan"),
            new Person("Charlie", 30, null),
            new Person("David", null, null)
        );
        
        // 여러 필드에 null 처리
        Comparator<Person> comp = Comparator
            .comparing((Person p) -> p.age, 
                Comparator.nullsLast(Comparator.naturalOrder()))
            .thenComparing(p -> p.city,
                Comparator.nullsLast(Comparator.naturalOrder()))
            .thenComparing(p -> p.name);
        
        people.sort(comp);
        
        System.out.println("=== null 안전 정렬 ===");
        people.forEach(System.out::println);
    }
}
```

---

## 6. 실전 활용

### 6.1 다양한 정렬 기준

```java
public class MultipleComparators {
    static class Student {
        String name;
        int korean;
        int english;
        int math;
        
        Student(String name, int korean, int english, int math) {
            this.name = name;
            this.korean = korean;
            this.english = english;
            this.math = math;
        }
        
        int total() {
            return korean + english + math;
        }
        
        double average() {
            return total() / 3.0;
        }
        
        @Override
        public String toString() {
            return String.format("%s (총점:%d, 평균:%.1f)",
                name, total(), average());
        }
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", 90, 85, 88),
            new Student("Bob", 85, 90, 92),
            new Student("Charlie", 88, 88, 88),
            new Student("David", 92, 85, 87)
        );
        
        // 1. 총점 순
        System.out.println("=== 총점 순 ===");
        students.sort(Comparator.comparingInt((Student s) -> s.total()).reversed());
        students.forEach(System.out::println);
        
        
        // 2. 평균 순
        System.out.println("\n=== 평균 순 ===");
        students.sort(Comparator.comparingDouble((Student s) -> s.average()).reversed());
        students.forEach(System.out::println);
        
        
        // 3. 국어 점수 순
        System.out.println("\n=== 국어 점수 순 ===");
        students.sort(Comparator.comparingInt((Student s) -> s.korean).reversed());
        students.forEach(System.out::println);
        
        
        // 4. 수학 높은 순, 같으면 영어 높은 순
        System.out.println("\n=== 수학/영어 순 ===");
        students.sort(Comparator
            .comparingInt((Student s) -> s.math).reversed()
            .thenComparingInt(s -> s.english).reversed());
        students.forEach(System.out::println);
    }
}
```

### 6.2 동적 정렬

```java
public class DynamicSorting {
    static class Product {
        String name;
        int price;
        int stock;
        double rating;
        
        Product(String name, int price, int stock, double rating) {
            this.name = name;
            this.price = price;
            this.stock = stock;
            this.rating = rating;
        }
        
        @Override
        public String toString() {
            return String.format("%s (%d원, 재고:%d, 평점:%.1f)",
                name, price, stock, rating);
        }
    }
    
    public static void main(String[] args) {
        List<Product> products = Arrays.asList(
            new Product("Product A", 10000, 50, 4.5),
            new Product("Product B", 8000, 30, 4.8),
            new Product("Product C", 12000, 20, 4.2),
            new Product("Product D", 9000, 40, 4.7)
        );
        
        // 정렬 기준을 사용자 입력으로 결정
        String sortBy = "rating";  // price, stock, rating
        
        Comparator<Product> comp = getComparator(sortBy);
        products.sort(comp);
        
        System.out.println("=== " + sortBy + " 기준 정렬 ===");
        products.forEach(System.out::println);
    }
    
    static Comparator<Product> getComparator(String sortBy) {
        switch (sortBy) {
            case "price":
                return Comparator.comparingInt(p -> p.price);
            case "stock":
                return Comparator.comparingInt((Product p) -> p.stock).reversed();
            case "rating":
                return Comparator.comparingDouble((Product p) -> p.rating).reversed();
            default:
                return Comparator.comparing(p -> p.name);
        }
    }
}
```

### 6.3 우선순위 큐 활용

```java
public class PriorityQueueComparator {
    static class Task {
        String name;
        int priority;
        long deadline;
        
        Task(String name, int priority, long deadline) {
            this.name = name;
            this.priority = priority;
            this.deadline = deadline;
        }
        
        @Override
        public String toString() {
            return name + "(우선순위:" + priority + ")";
        }
    }
    
    public static void main(String[] args) {
        // 우선순위 높은 순, 같으면 마감일 빠른 순
        Comparator<Task> comp = Comparator
            .comparingInt((Task t) -> t.priority).reversed()
            .thenComparingLong(t -> t.deadline);
        
        PriorityQueue<Task> pq = new PriorityQueue<>(comp);
        
        pq.offer(new Task("Task A", 3, 1000));
        pq.offer(new Task("Task B", 1, 2000));
        pq.offer(new Task("Task C", 3, 500));
        pq.offer(new Task("Task D", 2, 1500));
        
        System.out.println("=== 처리 순서 ===");
        while (!pq.isEmpty()) {
            System.out.println(pq.poll());
        }
    }
}
```

---

## 7. 성능 최적화

### 7.1 Comparator 캐싱

```java
public class ComparatorCaching {
    static class Student {
        String name;
        int score;
        
        Student(String name, int score) {
            this.name = name;
            this.score = score;
        }
        
        @Override
        public String toString() {
            return name + "(" + score + ")";
        }
    }
    
    // ❌ 나쁜 예: 매번 생성
    public static void sortBad(List<Student> students) {
        students.sort(Comparator.comparingInt(s -> s.score));
    }
    
    // ✅ 좋은 예: 재사용
    private static final Comparator<Student> BY_SCORE = 
        Comparator.comparingInt(s -> s.score);
    
    public static void sortGood(List<Student> students) {
        students.sort(BY_SCORE);
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", 85),
            new Student("Bob", 92),
            new Student("Charlie", 78)
        );
        
        // 벤치마크
        int iterations = 100000;
        
        long start = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            List<Student> copy = new ArrayList<>(students);
            sortBad(copy);
        }
        long time1 = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < iterations; i++) {
            List<Student> copy = new ArrayList<>(students);
            sortGood(copy);
        }
        long time2 = System.currentTimeMillis() - start;
        
        System.out.println("매번 생성: " + time1 + "ms");
        System.out.println("재사용: " + time2 + "ms");
        System.out.println("차이: " + (time1 - time2) + "ms");
    }
}
```

### 7.2 복잡한 비교 최적화

```java
public class ComparisonOptimization {
    static class Data {
        String text;
        int cachedHash;
        
        Data(String text) {
            this.text = text;
            this.cachedHash = text.hashCode();  // 미리 계산
        }
        
        @Override
        public String toString() {
            return text;
        }
    }
    
    public static void main(String[] args) {
        List<Data> data = Arrays.asList(
            new Data("Long text A"),
            new Data("Long text B"),
            new Data("Long text C")
        );
        
        // ❌ 비효율: 매번 계산
        Comparator<Data> bad = Comparator.comparingInt(d -> d.text.hashCode());
        
        // ✅ 효율: 캐시 사용
        Comparator<Data> good = Comparator.comparingInt(d -> d.cachedHash);
        
        System.out.println("=== 최적화 팁 ===");
        System.out.println("1. 비용 큰 계산은 미리");
        System.out.println("2. Comparator 재사용");
        System.out.println("3. 불필요한 객체 생성 피하기");
    }
}
```

---

## 8. 실전 연습 문제

### 문제 1: 학생 성적 처리

```java
// 총점 높은 순, 같으면 평균 높은 순, 같으면 이름 사전순
public class Problem1 {
    static class Student {
        String name;
        int korean;
        int english;
        int math;
        
        Student(String name, int korean, int english, int math) {
            this.name = name;
            this.korean = korean;
            this.english = english;
            this.math = math;
        }
        
        int total() {
            return korean + english + math;
        }
        
        double average() {
            return total() / 3.0;
        }
        
        @Override
        public String toString() {
            return name + "(총:" + total() + ", 평:" + average() + ")";
        }
    }
    
    public static Comparator<Student> createComparator() {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", 90, 85, 90),  // 총 265
            new Student("Bob", 88, 89, 88),    // 총 265
            new Student("Charlie", 85, 90, 95) // 총 270
        );
        
        students.sort(createComparator());
        students.forEach(System.out::println);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static Comparator<Student> createComparator() {
    return Comparator
        .comparingInt((Student s) -> s.total()).reversed()
        .thenComparingDouble((Student s) -> s.average()).reversed()
        .thenComparing(s -> s.name);
}
```
</details>

---

### 문제 2: 파일 정렬

```java
// 확장자별 그룹, 같은 확장자는 크기 큰 순, 같으면 이름 사전순
public class Problem2 {
    static class MyFile {
        String name;
        String extension;
        long size;
        
        MyFile(String name, String extension, long size) {
            this.name = name;
            this.extension = extension;
            this.size = size;
        }
        
        @Override
        public String toString() {
            return name + "." + extension + " (" + size + "KB)";
        }
    }
    
    public static Comparator<MyFile> createComparator() {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        List<MyFile> files = Arrays.asList(
            new MyFile("doc1", "txt", 100),
            new MyFile("image1", "jpg", 500),
            new MyFile("doc2", "txt", 200),
            new MyFile("image2", "jpg", 300)
        );
        
        files.sort(createComparator());
        files.forEach(System.out::println);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static Comparator<MyFile> createComparator() {
    return Comparator
        .comparing((MyFile f) -> f.extension)
        .thenComparingLong((MyFile f) -> f.size).reversed()
        .thenComparing(f -> f.name);
}
```
</details>

---

### 문제 3: 복합 조건 정렬

```java
// 부서별, 같은 부서는 직급 높은 순, 같으면 연봉 높은 순
public class Problem3 {
    static class Employee {
        String name;
        String department;
        int rank;  // 1=사원, 2=대리, 3=과장, 4=부장
        int salary;
        
        Employee(String name, String department, int rank, int salary) {
            this.name = name;
            this.department = department;
            this.rank = rank;
            this.salary = salary;
        }
        
        String getRankName() {
            String[] ranks = {"", "사원", "대리", "과장", "부장"};
            return ranks[rank];
        }
        
        @Override
        public String toString() {
            return name + "(" + department + ", " + 
                   getRankName() + ", " + salary + "만원)";
        }
    }
    
    public static Comparator<Employee> createComparator() {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        List<Employee> employees = Arrays.asList(
            new Employee("Alice", "개발", 3, 5000),
            new Employee("Bob", "영업", 2, 4000),
            new Employee("Charlie", "개발", 3, 5500),
            new Employee("David", "개발", 4, 7000)
        );
        
        employees.sort(createComparator());
        employees.forEach(System.out::println);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static Comparator<Employee> createComparator() {
    return Comparator
        .comparing((Employee e) -> e.department)
        .thenComparingInt((Employee e) -> e.rank).reversed()
        .thenComparingInt((Employee e) -> e.salary).reversed();
}
```
</details>

---

## 📌 핵심 정리

### Comparable vs Comparator
```java
// Comparable: 클래스에 구현
class Student implements Comparable<Student> {
    @Override
    public int compareTo(Student other) {
        return Integer.compare(this.score, other.score);
    }
}

// Comparator: 별도 구현
Comparator<Student> byScore = Comparator.comparingInt(s -> s.score);
```

### 주요 메서드
```java
// 생성
Comparator.comparing(keyExtractor)
Comparator.comparingInt(keyExtractor)
Comparator.comparingDouble(keyExtractor)
Comparator.naturalOrder()
Comparator.reverseOrder()

// 체이닝
comparator.reversed()
comparator.thenComparing(other)
comparator.thenComparingInt(keyExtractor)

// null 처리
Comparator.nullsFirst(comparator)
Comparator.nullsLast(comparator)
```

### 반환값 규칙
```
음수 (< 0): this < other (this가 앞)
0:          this == other
양수 (> 0): this > other (this가 뒤)
```

### 주의사항
```java
// ❌ 오버플로우 위험
return a - b;

// ✅ 안전
return Integer.compare(a, b);

// ✅ Comparator 재사용
private static final Comparator<Student> BY_SCORE = 
    Comparator.comparingInt(s -> s.score);
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Stream API →](./Util-02-Stream.md)**

</div>
