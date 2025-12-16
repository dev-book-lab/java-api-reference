# Arrays 05. 변환 (stream, asList)

> 배열과 다른 자료구조 간 변환 - Stream, List, Set 활용  
> Java API Reference

---

## 📑 목차

1. [asList() - 배열을 List로](#1-aslist---배열을-list로)
2. [stream() - 배열을 Stream으로](#2-stream---배열을-stream으로)
3. [List/Set을 배열로](#3-listset을-배열로)
4. [기본 타입 변환](#4-기본-타입-변환)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. asList() - 배열을 List로

### 1.1 기본 사용법

```java
import java.util.Arrays;
import java.util.List;

public class AsListBasic {
    public static void main(String[] args) {
        String[] arr = {"apple", "banana", "cherry"};
        
        // 배열을 List로 변환
        List<String> list = Arrays.asList(arr);
        System.out.println(list);
        // [apple, banana, cherry]
        
        
        // 인라인으로 생성
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        System.out.println(numbers);
        // [1, 2, 3, 4, 5]
        
        
        // get, size 등 사용 가능
        System.out.println(list.get(0));     // apple
        System.out.println(list.size());     // 3
        System.out.println(list.contains("banana"));  // true
    }
}
```

### 1.2 고정 크기 List

```java
public class FixedSizeList {
    public static void main(String[] args) {
        String[] arr = {"a", "b", "c"};
        List<String> list = Arrays.asList(arr);
        
        // ✅ 원소 수정 가능
        list.set(0, "A");
        System.out.println(list);  // [A, b, c]
        System.out.println(Arrays.toString(arr));  // [A, b, c] (원본도 변경!)
        
        
        // ❌ 추가/삭제 불가 (UnsupportedOperationException)
        try {
            list.add("d");
        } catch (UnsupportedOperationException e) {
            System.out.println("추가 불가!");
        }
        
        try {
            list.remove(0);
        } catch (UnsupportedOperationException e) {
            System.out.println("삭제 불가!");
        }
    }
}
```

**asList()의 특징:**
- ✅ 읽기, 수정 가능
- ❌ 추가, 삭제 불가 (고정 크기)
- ⚠️ 원본 배열과 연결됨 (같은 메모리 공유)

### 1.3 가변 크기 List 만들기

```java
import java.util.ArrayList;

public class MutableList {
    public static void main(String[] args) {
        String[] arr = {"a", "b", "c"};
        
        // 방법 1: ArrayList 생성자
        List<String> list1 = new ArrayList<>(Arrays.asList(arr));
        
        // 방법 2: List.of()로 먼저 만들고 ArrayList로 감싸기 (Java 9+)
        List<String> list2 = new ArrayList<>(List.of("a", "b", "c"));
        
        
        // 이제 추가/삭제 가능!
        list1.add("d");
        list1.remove(0);
        System.out.println(list1);  // [b, c, d]
        
        // 원본 배열은 영향 없음
        System.out.println(Arrays.toString(arr));  // [a, b, c]
    }
}
```

### 1.4 기본 타입 배열 주의사항

```java
public class PrimitiveArrayIssue {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        
        // ❌ 기본 타입 배열은 asList() 안 됨!
        List<int[]> wrong = Arrays.asList(arr);
        System.out.println(wrong.size());  // 1 (배열 자체가 하나의 원소!)
        System.out.println(wrong.get(0));  // [I@... (배열 주소)
        
        
        // ✅ Wrapper 클래스 사용
        Integer[] boxed = {1, 2, 3, 4, 5};
        List<Integer> correct = Arrays.asList(boxed);
        System.out.println(correct);  // [1, 2, 3, 4, 5]
        
        
        // ✅ Stream 활용 (Java 8+)
        List<Integer> list = Arrays.stream(arr)
                                   .boxed()
                                   .collect(Collectors.toList());
        System.out.println(list);  // [1, 2, 3, 4, 5]
    }
}
```

---

## 2. stream() - 배열을 Stream으로

### 2.1 기본 사용법

```java
import java.util.Arrays;
import java.util.stream.Stream;

public class StreamBasic {
    public static void main(String[] args) {
        String[] arr = {"apple", "banana", "cherry"};
        
        // 배열을 Stream으로
        Stream<String> stream = Arrays.stream(arr);
        
        // forEach로 출력
        stream.forEach(System.out::println);
        // apple
        // banana
        // cherry
        
        
        // 한 줄로
        Arrays.stream(arr)
              .forEach(s -> System.out.println(s.toUpperCase()));
        // APPLE
        // BANANA
        // CHERRY
    }
}
```

### 2.2 Stream 연산

```java
import java.util.List;
import java.util.stream.Collectors;

public class StreamOperations {
    public static void main(String[] args) {
        String[] words = {"apple", "pie", "banana", "cat", "dog"};
        
        // 필터링
        List<String> longWords = Arrays.stream(words)
                                       .filter(s -> s.length() > 3)
                                       .collect(Collectors.toList());
        System.out.println(longWords);  // [apple, banana]
        
        
        // 변환
        List<Integer> lengths = Arrays.stream(words)
                                      .map(String::length)
                                      .collect(Collectors.toList());
        System.out.println(lengths);  // [5, 3, 6, 3, 3]
        
        
        // 정렬
        List<String> sorted = Arrays.stream(words)
                                    .sorted()
                                    .collect(Collectors.toList());
        System.out.println(sorted);  // [apple, banana, cat, dog, pie]
        
        
        // 중복 제거
        String[] withDups = {"a", "b", "a", "c", "b"};
        List<String> unique = Arrays.stream(withDups)
                                    .distinct()
                                    .collect(Collectors.toList());
        System.out.println(unique);  // [a, b, c]
    }
}
```

### 2.3 기본 타입 Stream

```java
import java.util.stream.IntStream;
import java.util.stream.DoubleStream;

public class PrimitiveStream {
    public static void main(String[] args) {
        int[] numbers = {1, 2, 3, 4, 5};
        
        // IntStream
        IntStream intStream = Arrays.stream(numbers);
        
        // 합계
        int sum = Arrays.stream(numbers).sum();
        System.out.println("Sum: " + sum);  // 15
        
        // 평균
        double avg = Arrays.stream(numbers).average().orElse(0);
        System.out.println("Average: " + avg);  // 3.0
        
        // 최대값
        int max = Arrays.stream(numbers).max().orElse(0);
        System.out.println("Max: " + max);  // 5
        
        // 최소값
        int min = Arrays.stream(numbers).min().orElse(0);
        System.out.println("Min: " + min);  // 1
        
        
        // double 배열
        double[] doubles = {3.14, 2.71, 1.41};
        DoubleStream doubleStream = Arrays.stream(doubles);
        double sum2 = doubleStream.sum();
        System.out.println("Sum: " + sum2);  // 7.26
    }
}
```

### 2.4 부분 Stream

```java
public class StreamRange {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        
        // 범위 지정 (start ~ end-1)
        int sum = Arrays.stream(arr, 2, 7).sum();
        System.out.println("Sum [2~6]: " + sum);  // 25 (3+4+5+6+7)
        
        
        // 필터와 조합
        long count = Arrays.stream(arr, 0, 5)
                           .filter(n -> n % 2 == 0)
                           .count();
        System.out.println("Even count [0~4]: " + count);  // 2 (2,4)
    }
}
```

### 2.5 Stream to 배열

```java
public class StreamToArray {
    public static void main(String[] args) {
        List<String> list = Arrays.asList("apple", "banana", "cherry");
        
        // Stream → 배열
        String[] arr = list.stream()
                          .toArray(String[]::new);
        System.out.println(Arrays.toString(arr));
        // [apple, banana, cherry]
        
        
        // 필터 후 배열로
        String[] longWords = list.stream()
                                 .filter(s -> s.length() > 5)
                                 .toArray(String[]::new);
        System.out.println(Arrays.toString(longWords));
        // [banana, cherry]
        
        
        // int Stream → int[]
        int[] numbers = IntStream.range(1, 6).toArray();
        System.out.println(Arrays.toString(numbers));
        // [1, 2, 3, 4, 5]
    }
}
```

---

## 3. List/Set을 배열로

### 3.1 List를 배열로

```java
import java.util.ArrayList;
import java.util.List;

public class ListToArray {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        list.add("apple");
        list.add("banana");
        list.add("cherry");
        
        // 방법 1: toArray(T[])
        String[] arr1 = list.toArray(new String[0]);
        System.out.println(Arrays.toString(arr1));
        // [apple, banana, cherry]
        
        
        // 방법 2: 크기 지정
        String[] arr2 = list.toArray(new String[list.size()]);
        System.out.println(Arrays.toString(arr2));
        // [apple, banana, cherry]
        
        
        // 방법 3: Stream 사용
        String[] arr3 = list.stream().toArray(String[]::new);
        System.out.println(Arrays.toString(arr3));
        // [apple, banana, cherry]
    }
}
```

**크기 0 vs 정확한 크기:**
```java
// 크기 0 (권장 - 내부에서 자동 생성)
String[] arr = list.toArray(new String[0]);

// 정확한 크기 (약간 더 빠를 수 있음)
String[] arr = list.toArray(new String[list.size()]);

// 큰 배열 (남은 공간은 null)
String[] arr = list.toArray(new String[10]);
// [apple, banana, cherry, null, null, ...]
```

### 3.2 Set을 배열로

```java
import java.util.HashSet;
import java.util.Set;

public class SetToArray {
    public static void main(String[] args) {
        Set<Integer> set = new HashSet<>();
        set.add(5);
        set.add(2);
        set.add(8);
        set.add(1);
        
        // Set → 배열
        Integer[] arr = set.toArray(new Integer[0]);
        System.out.println(Arrays.toString(arr));
        // [1, 2, 5, 8] (순서는 보장 안 됨)
        
        
        // 정렬된 배열로
        Integer[] sorted = set.stream()
                             .sorted()
                             .toArray(Integer[]::new);
        System.out.println(Arrays.toString(sorted));
        // [1, 2, 5, 8] (정렬 보장)
    }
}
```

### 3.3 기본 타입으로 변환

```java
public class ToPrimitiveArray {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);
        
        // List<Integer> → int[]
        int[] arr = list.stream()
                       .mapToInt(Integer::intValue)
                       .toArray();
        System.out.println(Arrays.toString(arr));
        // [1, 2, 3, 4, 5]
        
        
        // List<Double> → double[]
        List<Double> doubles = Arrays.asList(1.1, 2.2, 3.3);
        double[] dArr = doubles.stream()
                              .mapToDouble(Double::doubleValue)
                              .toArray();
        System.out.println(Arrays.toString(dArr));
        // [1.1, 2.2, 3.3]
    }
}
```

---

## 4. 기본 타입 변환

### 4.1 int[] ↔ Integer[]

```java
public class IntegerConversion {
    public static void main(String[] args) {
        // int[] → Integer[]
        int[] primitives = {1, 2, 3, 4, 5};
        Integer[] boxed = Arrays.stream(primitives)
                               .boxed()
                               .toArray(Integer[]::new);
        System.out.println(Arrays.toString(boxed));
        // [1, 2, 3, 4, 5]
        
        
        // Integer[] → int[]
        Integer[] integers = {1, 2, 3, 4, 5};
        int[] unboxed = Arrays.stream(integers)
                             .mapToInt(Integer::intValue)
                             .toArray();
        System.out.println(Arrays.toString(unboxed));
        // [1, 2, 3, 4, 5]
    }
}
```

### 4.2 다양한 변환

```java
public class VariousConversions {
    public static void main(String[] args) {
        // String[] → int[]
        String[] strs = {"1", "2", "3", "4", "5"};
        int[] ints = Arrays.stream(strs)
                          .mapToInt(Integer::parseInt)
                          .toArray();
        System.out.println(Arrays.toString(ints));
        // [1, 2, 3, 4, 5]
        
        
        // int[] → String[]
        int[] numbers = {1, 2, 3, 4, 5};
        String[] strings = Arrays.stream(numbers)
                                 .mapToObj(String::valueOf)
                                 .toArray(String[]::new);
        System.out.println(Arrays.toString(strings));
        // [1, 2, 3, 4, 5]
        
        
        // double[] → int[] (반올림)
        double[] doubles = {1.1, 2.5, 3.9};
        int[] rounded = Arrays.stream(doubles)
                             .mapToInt(d -> (int)Math.round(d))
                             .toArray();
        System.out.println(Arrays.toString(rounded));
        // [1, 3, 4]
    }
}
```

### 4.3 2D 배열 변환

```java
public class TwoDConversion {
    public static void main(String[] args) {
        // 2D List → 2D 배열
        List<List<Integer>> list2D = new ArrayList<>();
        list2D.add(Arrays.asList(1, 2, 3));
        list2D.add(Arrays.asList(4, 5, 6));
        list2D.add(Arrays.asList(7, 8, 9));
        
        int[][] arr2D = list2D.stream()
                             .map(row -> row.stream()
                                           .mapToInt(Integer::intValue)
                                           .toArray())
                             .toArray(int[][]::new);
        
        System.out.println(Arrays.deepToString(arr2D));
        // [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
        
        
        // 2D 배열 → 1D 배열 (평탄화)
        int[][] matrix = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
        int[] flat = Arrays.stream(matrix)
                          .flatMapToInt(Arrays::stream)
                          .toArray();
        System.out.println(Arrays.toString(flat));
        // [1, 2, 3, 4, 5, 6, 7, 8, 9]
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 중복 제거 및 정렬

```java
// 배열에서 중복을 제거하고 정렬된 배열 반환
public class Problem1 {
    public static int[] uniqueSorted(int[] arr) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] arr = {5, 2, 8, 2, 9, 1, 5, 3};
        int[] result = uniqueSorted(arr);
        System.out.println(Arrays.toString(result));
        // [1, 2, 3, 5, 8, 9]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static int[] uniqueSorted(int[] arr) {
        return Arrays.stream(arr)
                    .distinct()      // 중복 제거
                    .sorted()        // 정렬
                    .toArray();
    }
    
    // Set 사용 버전
    public static int[] uniqueSorted2(int[] arr) {
        Set<Integer> set = new TreeSet<>();  // TreeSet은 자동 정렬
        for (int num : arr) {
            set.add(num);
        }
        
        return set.stream()
                 .mapToInt(Integer::intValue)
                 .toArray();
    }
}
```
</details>

---

### 문제 2: 조건 필터링

```java
// 배열에서 짝수만 선택하여 제곱한 결과 반환
public class Problem2 {
    public static int[] evenSquares(int[] arr) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        int[] result = evenSquares(arr);
        System.out.println(Arrays.toString(result));
        // [4, 16, 36, 64, 100]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int[] evenSquares(int[] arr) {
        return Arrays.stream(arr)
                    .filter(n -> n % 2 == 0)    // 짝수만
                    .map(n -> n * n)             // 제곱
                    .toArray();
    }
}
```
</details>

---

### 문제 3: 배열 분할

```java
// 배열을 n 크기의 청크로 분할
// 예: [1,2,3,4,5,6,7], n=3 → [[1,2,3], [4,5,6], [7]]
public class Problem3 {
    public static int[][] chunk(int[] arr, int n) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6, 7};
        int[][] result = chunk(arr, 3);
        System.out.println(Arrays.deepToString(result));
        // [[1, 2, 3], [4, 5, 6], [7]]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int[][] chunk(int[] arr, int n) {
        int chunks = (arr.length + n - 1) / n;  // 올림
        int[][] result = new int[chunks][];
        
        for (int i = 0; i < chunks; i++) {
            int start = i * n;
            int end = Math.min(start + n, arr.length);
            result[i] = Arrays.copyOfRange(arr, start, end);
        }
        
        return result;
    }
    
    // Stream 버전
    public static List<List<Integer>> chunkStream(int[] arr, int n) {
        List<List<Integer>> result = new ArrayList<>();
        
        for (int i = 0; i < arr.length; i += n) {
            int end = Math.min(i + n, arr.length);
            List<Integer> chunk = Arrays.stream(arr, i, end)
                                       .boxed()
                                       .collect(Collectors.toList());
            result.add(chunk);
        }
        
        return result;
    }
}
```
</details>

---

## 📌 핵심 정리

### 배열 → List
```java
// 고정 크기 (원본과 연결)
List<String> list = Arrays.asList(arr);

// 가변 크기 (독립적)
List<String> list = new ArrayList<>(Arrays.asList(arr));

// Stream 사용 (기본 타입)
List<Integer> list = Arrays.stream(arr).boxed()
                           .collect(Collectors.toList());
```

### 배열 → Stream
```java
// 객체 배열
Arrays.stream(arr)

// 기본 타입 배열
Arrays.stream(intArr)     // IntStream
Arrays.stream(doubleArr)  // DoubleStream
```

### List/Set → 배열
```java
// List → 배열
String[] arr = list.toArray(new String[0]);
int[] arr = list.stream().mapToInt(i -> i).toArray();

// Set → 배열 (정렬)
Integer[] arr = set.stream().sorted().toArray(Integer[]::new);
```

### 기본 타입 변환
```java
// int[] → Integer[]
Arrays.stream(intArr).boxed().toArray(Integer[]::new)

// Integer[] → int[]
Arrays.stream(integerArr).mapToInt(i -> i).toArray()

// String[] → int[]
Arrays.stream(strArr).mapToInt(Integer::parseInt).toArray()
```

### 주의사항
```java
// ❌ 기본 타입 배열
int[] arr = {1, 2, 3};
List<int[]> wrong = Arrays.asList(arr);  // 배열 자체가 원소!

// ✅ Wrapper 사용
Integer[] arr = {1, 2, 3};
List<Integer> correct = Arrays.asList(arr);

// ✅ asList()는 고정 크기
List<String> list = Arrays.asList("a", "b");
// list.add("c");  // UnsupportedOperationException!
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 04. Arrays 비교와 복사](./Arrays-04-비교와복사.md) | [다음: 06. 다차원 배열 →](./Arrays-06-다차원배열.md)**

</div>
