# Generics 02. Wildcard 완전 정복

> 와일드카드로 유연한 제네릭 활용하기 - ?, extends, super 마스터하기  
> Java Generics

---

## 📑 목차

1. [Wildcard 소개](#1-wildcard-소개)
2. [Unbounded Wildcard](#2-unbounded-wildcard)
3. [Upper Bounded Wildcard](#3-upper-bounded-wildcard)
4. [Lower Bounded Wildcard](#4-lower-bounded-wildcard)
5. [PECS 원칙](#5-pecs-원칙)
6. [Wildcard vs Type Parameter](#6-wildcard-vs-type-parameter)
7. [실전 예제](#7-실전-예제)
8. [Best Practices](#8-best-practices)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Wildcard 소개

### 1.1 왜 Wildcard인가?

```java
import java.util.*;

public class WhyWildcard {
    // 타입 매개변수 사용 - 엄격함
    public static void printListStrict(List<Object> list) {
        for (Object obj : list) {
            System.out.println(obj);
        }
    }
    
    // Wildcard 사용 - 유연함
    public static void printListFlexible(List<?> list) {
        for (Object obj : list) {
            System.out.println(obj);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Wildcard 필요성 ===\n");
        
        List<String> strings = Arrays.asList("A", "B", "C");
        List<Integer> integers = Arrays.asList(1, 2, 3);
        
        // printListStrict(strings);  // 컴파일 에러!
        // List<String>은 List<Object>의 하위 타입이 아님
        
        // Wildcard는 OK
        System.out.println("String 리스트:");
        printListFlexible(strings);
        
        System.out.println("\nInteger 리스트:");
        printListFlexible(integers);
        
        
        System.out.println("\n📌 핵심");
        System.out.println("List<String>은 List<Object>의 하위 타입이 아님!");
        System.out.println("→ Wildcard로 해결");
    }
}
```

### 1.2 Wildcard 종류

```java
import java.util.*;

public class WildcardTypes {
    public static void main(String[] args) {
        System.out.println("=== Wildcard 종류 ===\n");
        
        System.out.println("1️⃣ Unbounded Wildcard");
        System.out.println("   List<?>           - 모든 타입");
        System.out.println("   읽기만 가능 (Object로)\n");
        
        System.out.println("2️⃣ Upper Bounded Wildcard");
        System.out.println("   List<? extends Number>  - Number와 하위");
        System.out.println("   읽기 가능, 쓰기 제한\n");
        
        System.out.println("3️⃣ Lower Bounded Wildcard");
        System.out.println("   List<? super Integer>   - Integer와 상위");
        System.out.println("   쓰기 가능, 읽기 제한\n");
        
        System.out.println("📌 PECS 원칙");
        System.out.println("Producer Extends, Consumer Super");
        System.out.println("- 데이터를 읽기만: extends");
        System.out.println("- 데이터를 쓰기만: super");
    }
}
```

---

## 2. Unbounded Wildcard

### 2.1 기본 사용법

```java
import java.util.*;

public class UnboundedWildcardBasic {
    // 모든 타입의 리스트 출력
    public static void printList(List<?> list) {
        for (Object element : list) {
            System.out.println(element);
        }
    }
    
    // 리스트 크기
    public static int size(List<?> list) {
        return list.size();
    }
    
    // 리스트가 비었는지
    public static boolean isEmpty(Collection<?> collection) {
        return collection.isEmpty();
    }
    
    public static void main(String[] args) {
        System.out.println("=== Unbounded Wildcard <?> ===\n");
        
        List<String> strings = Arrays.asList("A", "B", "C");
        List<Integer> integers = Arrays.asList(1, 2, 3);
        List<Double> doubles = Arrays.asList(1.1, 2.2, 3.3);
        
        System.out.println("=== String 리스트 ===");
        printList(strings);
        
        System.out.println("\n=== Integer 리스트 ===");
        printList(integers);
        
        System.out.println("\n크기: " + size(strings));
        System.out.println("비었나? " + isEmpty(doubles));
    }
}
```

### 2.2 제약사항

```java
import java.util.*;

public class UnboundedWildcardConstraints {
    public static void example(List<?> list) {
        // ✅ 읽기 (Object로)
        Object obj = list.get(0);
        int size = list.size();
        boolean empty = list.isEmpty();
        
        // ❌ 쓰기 불가 (null 제외)
        // list.add("String");  // 컴파일 에러!
        // list.add(123);       // 컴파일 에러!
        list.add(null);  // OK (null만 가능)
        
        // ✅ 제거는 가능
        list.clear();
        // list.remove(0);  // 인덱스 제거 가능
    }
    
    public static void main(String[] args) {
        System.out.println("=== Unbounded Wildcard 제약 ===\n");
        
        System.out.println("✅ 가능한 연산");
        System.out.println("- get() → Object");
        System.out.println("- size(), isEmpty()");
        System.out.println("- clear(), remove()");
        System.out.println("- add(null)\n");
        
        System.out.println("❌ 불가능한 연산");
        System.out.println("- add(element) (null 제외)");
        System.out.println("- set(index, element)");
        System.out.println("\n이유: 실제 타입을 알 수 없음");
    }
}
```

---

## 3. Upper Bounded Wildcard

### 3.1 기본 사용법

```java
import java.util.*;

public class UpperBoundedWildcardBasic {
    // Number와 하위 타입만
    public static double sum(List<? extends Number> list) {
        double total = 0;
        for (Number num : list) {
            total += num.doubleValue();
        }
        return total;
    }
    
    // 최대값
    public static <T extends Comparable<T>> T max(List<? extends T> list) {
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
        System.out.println("=== Upper Bounded Wildcard ===\n");
        
        List<Integer> integers = Arrays.asList(1, 2, 3, 4, 5);
        List<Double> doubles = Arrays.asList(1.1, 2.2, 3.3);
        // List<String> strings = Arrays.asList("A", "B");  // 컴파일 에러!
        
        System.out.println("Integer 합: " + sum(integers));
        System.out.println("Double 합: " + sum(doubles));
        
        System.out.println("\nInteger 최대: " + max(integers));
        System.out.println("Double 최대: " + max(doubles));
    }
}
```

### 3.2 읽기와 쓰기

```java
import java.util.*;

public class UpperBoundedReadWrite {
    public static void example(List<? extends Number> list) {
        // ✅ 읽기 (Number로)
        Number num = list.get(0);
        double value = num.doubleValue();
        
        // ❌ 쓰기 불가
        // list.add(123);        // 컴파일 에러!
        // list.add(3.14);       // 컴파일 에러!
        // list.add(new Integer(1));  // 컴파일 에러!
        
        // 이유: 실제 타입이 Integer인지 Double인지 모름
    }
    
    public static void main(String[] args) {
        System.out.println("=== Upper Bounded 읽기/쓰기 ===\n");
        
        System.out.println("✅ 읽기");
        System.out.println("- Number로 읽기 가능");
        System.out.println("- doubleValue() 등 호출 가능\n");
        
        System.out.println("❌ 쓰기");
        System.out.println("- Integer? Double? Long?");
        System.out.println("- 정확한 타입을 알 수 없음");
        System.out.println("- add() 불가\n");
        
        System.out.println("📌 Producer Extends");
        System.out.println("데이터를 생산(제공)만 하는 경우");
    }
}
```

### 3.3 실전 활용

```java
import java.util.*;

public class UpperBoundedPractical {
    // 숫자 리스트 복사
    public static void copy(
        List<? extends Number> source,
        List<? super Number> dest
    ) {
        for (Number num : source) {
            dest.add(num);
        }
    }
    
    // 컬렉션 합치기
    public static <T> void addAll(
        Collection<? extends T> source,
        Collection<T> dest
    ) {
        for (T element : source) {
            dest.add(element);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 실전 활용 ===\n");
        
        // 숫자 복사
        List<Integer> integers = Arrays.asList(1, 2, 3);
        List<Number> numbers = new ArrayList<>();
        copy(integers, numbers);
        System.out.println("복사: " + numbers);
        
        
        // 컬렉션 합치기
        List<String> source = Arrays.asList("A", "B", "C");
        List<String> dest = new ArrayList<>(Arrays.asList("X", "Y"));
        addAll(source, dest);
        System.out.println("합침: " + dest);
    }
}
```

---

## 4. Lower Bounded Wildcard

### 4.1 기본 사용법

```java
import java.util.*;

public class LowerBoundedWildcardBasic {
    // Integer와 상위 타입
    public static void addIntegers(List<? super Integer> list) {
        list.add(1);
        list.add(2);
        list.add(3);
    }
    
    // 숫자 추가
    public static void addNumbers(List<? super Integer> list) {
        for (int i = 1; i <= 5; i++) {
            list.add(i);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Lower Bounded Wildcard ===\n");
        
        List<Integer> integers = new ArrayList<>();
        List<Number> numbers = new ArrayList<>();
        List<Object> objects = new ArrayList<>();
        
        // 모두 OK
        addIntegers(integers);
        addIntegers(numbers);
        addIntegers(objects);
        
        System.out.println("Integer 리스트: " + integers);
        System.out.println("Number 리스트: " + numbers);
        System.out.println("Object 리스트: " + objects);
    }
}
```

### 4.2 읽기와 쓰기

```java
import java.util.*;

public class LowerBoundedReadWrite {
    public static void example(List<? super Integer> list) {
        // ✅ 쓰기 (Integer와 하위)
        list.add(123);
        list.add(new Integer(456));
        
        // ❌ 읽기 제한 (Object로만)
        Object obj = list.get(0);
        // Integer num = list.get(0);  // 컴파일 에러!
        // Number num = list.get(0);   // 컴파일 에러!
        
        // 캐스팅 필요
        if (list.get(0) instanceof Integer) {
            Integer num = (Integer) list.get(0);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Lower Bounded 읽기/쓰기 ===\n");
        
        System.out.println("✅ 쓰기");
        System.out.println("- Integer 추가 가능");
        System.out.println("- Integer 하위도 가능\n");
        
        System.out.println("❌ 읽기");
        System.out.println("- Object로만 읽기");
        System.out.println("- 정확한 타입 모름");
        System.out.println("- Integer? Number? Object?\n");
        
        System.out.println("📌 Consumer Super");
        System.out.println("데이터를 소비(저장)만 하는 경우");
    }
}
```

### 4.3 실전 활용

```java
import java.util.*;

public class LowerBoundedPractical {
    // 리스트에 숫자 채우기
    public static void fill(List<? super Integer> list, int count) {
        for (int i = 1; i <= count; i++) {
            list.add(i);
        }
    }
    
    // 리스트에 요소 추가
    public static <T> void addElements(
        List<? super T> dest,
        T... elements
    ) {
        for (T element : elements) {
            dest.add(element);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 실전 활용 ===\n");
        
        // 숫자 채우기
        List<Number> numbers = new ArrayList<>();
        fill(numbers, 5);
        System.out.println("숫자: " + numbers);
        
        // 요소 추가
        List<Object> objects = new ArrayList<>();
        addElements(objects, "A", "B", "C");
        System.out.println("문자: " + objects);
        
        addElements(objects, 1, 2, 3);
        System.out.println("혼합: " + objects);
    }
}
```

---

## 5. PECS 원칙

### 5.1 PECS란?

```java
import java.util.*;

public class PECSPrinciple {
    public static void main(String[] args) {
        System.out.println("=== PECS 원칙 ===\n");
        
        System.out.println("Producer Extends, Consumer Super\n");
        
        System.out.println("📌 Producer (생산자)");
        System.out.println("- 데이터를 제공 (읽기)");
        System.out.println("- extends 사용");
        System.out.println("- List<? extends T>");
        System.out.println("- 예: 소스 리스트\n");
        
        System.out.println("📌 Consumer (소비자)");
        System.out.println("- 데이터를 받음 (쓰기)");
        System.out.println("- super 사용");
        System.out.println("- List<? super T>");
        System.out.println("- 예: 목적지 리스트\n");
        
        System.out.println("📌 모두 사용");
        System.out.println("- 읽기도 쓰기도");
        System.out.println("- 정확한 타입 사용");
        System.out.println("- List<T>");
    }
}
```

### 5.2 PECS 적용 예제

```java
import java.util.*;

public class PECSExamples {
    // Producer - 소스에서 읽기
    public static <T> void copy(
        List<? extends T> source,    // Producer Extends
        List<? super T> dest         // Consumer Super
    ) {
        for (T element : source) {
            dest.add(element);
        }
    }
    
    // Producer - 여러 소스 병합
    public static <T> List<T> merge(
        List<? extends T> list1,
        List<? extends T> list2
    ) {
        List<T> result = new ArrayList<>();
        result.addAll(list1);
        result.addAll(list2);
        return result;
    }
    
    // Consumer - 목적지에 추가
    public static <T> void addAll(
        List<? super T> dest,
        T... elements
    ) {
        for (T element : elements) {
            dest.add(element);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== PECS 적용 ===\n");
        
        // copy
        List<Integer> source = Arrays.asList(1, 2, 3);
        List<Number> dest = new ArrayList<>();
        copy(source, dest);
        System.out.println("복사: " + dest);
        
        
        // merge
        List<Integer> list1 = Arrays.asList(1, 2);
        List<Integer> list2 = Arrays.asList(3, 4);
        List<Integer> merged = merge(list1, list2);
        System.out.println("병합: " + merged);
        
        
        // addAll
        List<Object> objects = new ArrayList<>();
        addAll(objects, "A", "B", "C");
        System.out.println("추가: " + objects);
    }
}
```

### 5.3 Collections.copy 분석

```java
import java.util.*;

public class CollectionsCopyAnalysis {
    // Collections.copy 시그니처 분석
    public static <T> void copy(
        List<? super T> dest,      // Consumer Super - 쓰기
        List<? extends T> src      // Producer Extends - 읽기
    ) {
        for (T element : src) {
            dest.add(element);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Collections.copy 분석 ===\n");
        
        System.out.println("시그니처:");
        System.out.println("copy(List<? super T> dest, List<? extends T> src)\n");
        
        System.out.println("📌 dest: List<? super T>");
        System.out.println("- Consumer (받는 쪽)");
        System.out.println("- 쓰기 작업");
        System.out.println("- super 사용\n");
        
        System.out.println("📌 src: List<? extends T>");
        System.out.println("- Producer (주는 쪽)");
        System.out.println("- 읽기 작업");
        System.out.println("- extends 사용\n");
        
        
        // 예제
        List<Integer> source = Arrays.asList(1, 2, 3);
        List<Number> dest = new ArrayList<>(Arrays.asList(0, 0, 0));
        Collections.copy(dest, source);
        System.out.println("결과: " + dest);
    }
}
```

---

## 6. Wildcard vs Type Parameter

### 6.1 언제 무엇을 사용하나?

```java
import java.util.*;

public class WildcardVsTypeParameter {
    // 타입 매개변수 - 반환 타입에 사용
    public static <T> T getFirst(List<T> list) {
        return list.isEmpty() ? null : list.get(0);
    }
    
    // Wildcard - 반환 타입 사용 안 함
    public static void printList(List<?> list) {
        for (Object obj : list) {
            System.out.println(obj);
        }
    }
    
    // 타입 매개변수 - 여러 곳에 사용
    public static <T> void copy(List<T> source, List<T> dest) {
        dest.addAll(source);
    }
    
    // Wildcard - 유연성
    public static void copyFlexible(
        List<? extends Number> source,
        List<? super Number> dest
    ) {
        for (Number num : source) {
            dest.add(num);
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Wildcard vs Type Parameter ===\n");
        
        System.out.println("📌 타입 매개변수 사용");
        System.out.println("- 반환 타입에 사용");
        System.out.println("- 여러 곳에서 같은 타입");
        System.out.println("- 타입 관계 명확\n");
        
        System.out.println("📌 Wildcard 사용");
        System.out.println("- 반환 타입 필요 없음");
        System.out.println("- 유연성 필요");
        System.out.println("- PECS 적용");
    }
}
```

### 6.2 변환 예제

```java
import java.util.*;

public class ConversionExamples {
    // 타입 매개변수 버전
    public static <T> boolean contains1(List<T> list, T element) {
        return list.contains(element);
    }
    
    // Wildcard 버전 (더 유연)
    public static boolean contains2(List<?> list, Object element) {
        return list.contains(element);
    }
    
    
    // 타입 매개변수 필요
    public static <T> List<T> filter1(List<T> list, java.util.function.Predicate<T> pred) {
        List<T> result = new ArrayList<>();
        for (T element : list) {
            if (pred.test(element)) {
                result.add(element);
            }
        }
        return result;
    }
    
    // Wildcard로는 불가능 (T를 반환 타입에 사용)
    
    
    public static void main(String[] args) {
        List<String> strings = Arrays.asList("A", "B", "C");
        
        System.out.println(contains1(strings, "B"));
        System.out.println(contains2(strings, "B"));
        
        List<String> filtered = filter1(strings, s -> s.compareTo("B") > 0);
        System.out.println(filtered);
    }
}
```

---

## 7. 실전 예제

### 7.1 유연한 컬렉션 유틸리티

```java
import java.util.*;
import java.util.function.*;

public class FlexibleCollectionUtils {
    // 변환
    public static <T, R> List<R> map(
        List<? extends T> source,
        Function<? super T, ? extends R> mapper
    ) {
        List<R> result = new ArrayList<>();
        for (T element : source) {
            result.add(mapper.apply(element));
        }
        return result;
    }
    
    // 필터
    public static <T> List<T> filter(
        List<? extends T> source,
        Predicate<? super T> predicate
    ) {
        List<T> result = new ArrayList<>();
        for (T element : source) {
            if (predicate.test(element)) {
                result.add(element);
            }
        }
        return result;
    }
    
    // 정렬된 복사
    public static <T extends Comparable<? super T>> List<T> sortedCopy(
        List<? extends T> source
    ) {
        List<T> result = new ArrayList<>(source);
        Collections.sort(result);
        return result;
    }
    
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        // 변환
        List<String> strings = map(numbers, n -> "Number: " + n);
        System.out.println("변환: " + strings);
        
        // 필터
        List<Integer> evens = filter(numbers, n -> n % 2 == 0);
        System.out.println("짝수: " + evens);
        
        // 정렬
        List<Integer> unsorted = Arrays.asList(5, 2, 8, 1, 9);
        List<Integer> sorted = sortedCopy(unsorted);
        System.out.println("정렬: " + sorted);
    }
}
```

### 7.2 타입 안전 Builder

```java
import java.util.*;

class Query<T> {
    private List<T> results = new ArrayList<>();
    
    // Producer Extends
    public Query<T> addAll(Collection<? extends T> items) {
        results.addAll(items);
        return this;
    }
    
    // Consumer Super
    public void copyTo(Collection<? super T> dest) {
        dest.addAll(results);
    }
    
    public List<T> execute() {
        return new ArrayList<>(results);
    }
}

public class TypeSafeBuilder {
    public static void main(String[] args) {
        // Integer Query
        Query<Number> query = new Query<>();
        
        List<Integer> ints = Arrays.asList(1, 2, 3);
        List<Double> doubles = Arrays.asList(1.1, 2.2);
        
        query.addAll(ints)
             .addAll(doubles);
        
        List<Object> objects = new ArrayList<>();
        query.copyTo(objects);
        
        System.out.println("결과: " + objects);
    }
}
```

---

## 8. Best Practices

### 8.1 API 설계

```java
import java.util.*;

public class APIDesignBestPractices {
    // ❌ 나쁜 예 - 너무 제한적
    public static void processBad(List<Number> list) {
        // Integer, Double 리스트 사용 불가
    }
    
    // ✅ 좋은 예 - 유연함
    public static void processGood(List<? extends Number> list) {
        // 모든 Number 하위 타입 가능
    }
    
    
    // ❌ 나쁜 예 - 불필요한 제약
    public static <T extends Number> void bad(List<T> list) {
        // T를 반환에 사용 안 함
    }
    
    // ✅ 좋은 예 - Wildcard 사용
    public static void good(List<? extends Number> list) {
        // 더 간단하고 유연
    }
    
    
    public static void main(String[] args) {
        System.out.println("=== API 설계 원칙 ===\n");
        
        System.out.println("1. 입력은 가능한 유연하게");
        System.out.println("   → extends 사용\n");
        
        System.out.println("2. 출력은 구체적으로");
        System.out.println("   → 정확한 타입\n");
        
        System.out.println("3. PECS 적용");
        System.out.println("   → Producer Extends");
        System.out.println("   → Consumer Super");
    }
}
```

### 8.2 가독성

```java
import java.util.*;

public class ReadabilityBestPractices {
    // ❌ 복잡함
    public static <T extends Comparable<? super T>> void sortBad(
        List<? extends T> list,
        Comparator<? super T> comparator
    ) {
        // 너무 복잡
    }
    
    // ✅ 간단함
    public static <T> void sortGood(List<T> list, Comparator<? super T> c) {
        Collections.sort(list, c);
    }
    
    
    public static void main(String[] args) {
        System.out.println("=== 가독성 ===\n");
        
        System.out.println("📌 원칙");
        System.out.println("1. 필요한 만큼만 제약");
        System.out.println("2. 간단한 시그니처");
        System.out.println("3. 명확한 의도\n");
        
        System.out.println("⚠️ 주의");
        System.out.println("- Wildcard 남용 금지");
        System.out.println("- 중첩 Wildcard 지양");
        System.out.println("- 가독성 우선");
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 리스트 합치기

```java
// PECS를 적용하여 merge 메서드 작성
import java.util.*;

public class Problem1 {
    public static <T> List<T> merge(/* 파라미터 */) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        List<Integer> ints = Arrays.asList(1, 2, 3);
        List<Number> nums = Arrays.asList(1.1, 2.2);
        
        List<Number> merged = merge(ints, nums);
        System.out.println(merged);  // [1, 2, 3, 1.1, 2.2]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static <T> List<T> merge(
    List<? extends T> list1,
    List<? extends T> list2
) {
    List<T> result = new ArrayList<>();
    result.addAll(list1);
    result.addAll(list2);
    return result;
}
```
</details>

---

### 문제 2: 최대값 찾기

```java
// Upper Bounded Wildcard로 최대값 찾기
import java.util.*;

public class Problem2 {
    public static <T extends Comparable<? super T>> T max(/* 파라미터 */) {
        // 구현
        return null;
    }
    
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(3, 1, 4, 1, 5, 9);
        System.out.println("Max: " + max(numbers));  // 9
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static <T extends Comparable<? super T>> T max(
    List<? extends T> list
) {
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
```
</details>

---

### 문제 3: 리스트 채우기

```java
// Lower Bounded Wildcard로 채우기
import java.util.*;

public class Problem3 {
    public static void fill(/* 파라미터 */, int count) {
        // 구현
    }
    
    public static void main(String[] args) {
        List<Number> numbers = new ArrayList<>();
        fill(numbers, 5);
        System.out.println(numbers);  // [1, 2, 3, 4, 5]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void fill(List<? super Integer> list, int count) {
    for (int i = 1; i <= count; i++) {
        list.add(i);
    }
}
```
</details>

---

## 📌 핵심 정리

### Wildcard 종류
```java
<?>                    // Unbounded - 모든 타입
<? extends T>          // Upper Bounded - T와 하위
<? super T>            // Lower Bounded - T와 상위
```

### PECS 원칙
```java
Producer Extends       // 읽기 → extends
Consumer Super         // 쓰기 → super

// 예제
<T> void copy(
    List<? extends T> source,    // Producer
    List<? super T> dest         // Consumer
)
```

### 사용 시기
```
Unbounded (?)
- 타입 무관 작업
- size(), isEmpty()

Upper Bounded (extends)
- 읽기 작업
- Producer

Lower Bounded (super)
- 쓰기 작업
- Consumer
```

### Wildcard vs Type Parameter
```
Type Parameter: 반환 타입, 타입 관계
Wildcard: 유연성, 읽기/쓰기 구분
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Generic Basic](./Generics-01-Basic.md)**

</div>
