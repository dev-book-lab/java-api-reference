# Util 02. Stream API 완전 정복

> Java 8 함수형 프로그래밍 - Stream API 마스터하기  
> Java API Reference

---

## 📑 목차

1. [Stream 기본 개념](#1-stream-기본-개념)
2. [Stream 생성](#2-stream-생성)
3. [중간 연산 (Intermediate)](#3-중간-연산-intermediate)
4. [최종 연산 (Terminal)](#4-최종-연산-terminal)
5. [Collector](#5-collector)
6. [병렬 스트림](#6-병렬-스트림)
7. [실전 활용](#7-실전-활용)
8. [실전 연습 문제](#8-실전-연습-문제)

---

## 1. Stream 기본 개념

### 1.1 Stream이란?

```java
import java.util.*;
import java.util.stream.*;

public class StreamIntro {
    public static void main(String[] args) {
        System.out.println("=== Stream API란? ===\n");
        
        System.out.println("1. 데이터 처리 파이프라인");
        System.out.println("   - 데이터를 선언적으로 처리");
        System.out.println("   - 컬렉션, 배열 등의 데이터 소스\n");
        
        System.out.println("2. 함수형 프로그래밍");
        System.out.println("   - 람다 표현식 활용");
        System.out.println("   - 불변성, 부수효과 최소화\n");
        
        System.out.println("3. 특징");
        System.out.println("   - 원본 데이터 변경 안 함");
        System.out.println("   - 일회용 (한 번만 사용)");
        System.out.println("   - 내부 반복");
        System.out.println("   - 지연 연산 (Lazy Evaluation)");
    }
}
```

### 1.2 전통적 방식 vs Stream

```java
public class TraditionalVsStream {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // ❌ 전통적 방식
        System.out.println("=== 전통적 방식 ===");
        List<Integer> result1 = new ArrayList<>();
        for (Integer num : numbers) {
            if (num % 2 == 0) {
                result1.add(num * num);
            }
        }
        System.out.println("짝수의 제곱: " + result1);
        
        
        // ✅ Stream 방식
        System.out.println("\n=== Stream 방식 ===");
        List<Integer> result2 = numbers.stream()
            .filter(n -> n % 2 == 0)
            .map(n -> n * n)
            .collect(Collectors.toList());
        System.out.println("짝수의 제곱: " + result2);
        
        
        System.out.println("\n=== Stream의 장점 ===");
        System.out.println("1. 가독성 향상");
        System.out.println("2. 간결한 코드");
        System.out.println("3. 병렬 처리 쉬움");
    }
}
```

### 1.3 Stream 파이프라인

```java
public class StreamPipeline {
    public static void main(String[] args) {
        List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "David");
        
        // Stream 파이프라인
        List<String> result = names.stream()        // 1. 생성
            .filter(s -> s.length() > 3)            // 2. 중간 연산
            .map(String::toUpperCase)               // 3. 중간 연산
            .sorted()                               // 4. 중간 연산
            .collect(Collectors.toList());          // 5. 최종 연산
        
        System.out.println("결과: " + result);
        
        
        System.out.println("\n=== 파이프라인 구조 ===");
        System.out.println("1. Stream 생성");
        System.out.println("2. 중간 연산 (0개 이상)");
        System.out.println("   - filter, map, sorted 등");
        System.out.println("   - 지연 실행");
        System.out.println("3. 최종 연산 (1개)");
        System.out.println("   - collect, forEach, count 등");
        System.out.println("   - 실제 실행");
    }
}
```

---

## 2. Stream 생성

### 2.1 컬렉션에서 생성

```java
public class StreamFromCollection {
    public static void main(String[] args) {
        // List
        List<String> list = Arrays.asList("A", "B", "C");
        Stream<String> stream1 = list.stream();
        System.out.println("List stream: ");
        stream1.forEach(System.out::println);
        
        
        // Set
        Set<Integer> set = new HashSet<>(Arrays.asList(1, 2, 3));
        Stream<Integer> stream2 = set.stream();
        System.out.println("\nSet stream: ");
        stream2.forEach(System.out::println);
        
        
        // Map
        Map<String, Integer> map = new HashMap<>();
        map.put("A", 1);
        map.put("B", 2);
        
        System.out.println("\nMap key stream: ");
        map.keySet().stream().forEach(System.out::println);
        
        System.out.println("Map value stream: ");
        map.values().stream().forEach(System.out::println);
        
        System.out.println("Map entry stream: ");
        map.entrySet().stream()
            .forEach(e -> System.out.println(e.getKey() + "=" + e.getValue()));
    }
}
```

### 2.2 배열에서 생성

```java
public class StreamFromArray {
    public static void main(String[] args) {
        // 배열로부터
        String[] arr = {"A", "B", "C"};
        
        Stream<String> stream1 = Arrays.stream(arr);
        System.out.println("Arrays.stream():");
        stream1.forEach(System.out::println);
        
        
        // 부분 배열
        Stream<String> stream2 = Arrays.stream(arr, 0, 2);
        System.out.println("\n부분 배열 (0~1):");
        stream2.forEach(System.out::println);
        
        
        // 기본형 배열
        int[] numbers = {1, 2, 3, 4, 5};
        IntStream intStream = Arrays.stream(numbers);
        System.out.println("\nIntStream sum: " + intStream.sum());
    }
}
```

### 2.3 직접 생성

```java
public class StreamCreation {
    public static void main(String[] args) {
        // Stream.of()
        Stream<String> stream1 = Stream.of("A", "B", "C");
        System.out.println("Stream.of():");
        stream1.forEach(System.out::println);
        
        
        // Stream.empty()
        Stream<String> empty = Stream.empty();
        System.out.println("\nEmpty count: " + empty.count());
        
        
        // Stream.generate() (무한)
        Stream<Double> random = Stream.generate(Math::random)
            .limit(5);
        System.out.println("\nRandom 5개:");
        random.forEach(System.out::println);
        
        
        // Stream.iterate() (무한)
        Stream<Integer> even = Stream.iterate(0, n -> n + 2)
            .limit(10);
        System.out.println("\n짝수 10개:");
        even.forEach(n -> System.out.print(n + " "));
        
        
        // IntStream.range()
        System.out.println("\n\nIntStream.range(1, 5):");
        IntStream.range(1, 5).forEach(System.out::println);  // 1,2,3,4
        
        
        // IntStream.rangeClosed()
        System.out.println("\nIntStream.rangeClosed(1, 5):");
        IntStream.rangeClosed(1, 5).forEach(System.out::println);  // 1,2,3,4,5
    }
}
```

### 2.4 파일과 문자열에서 생성

```java
public class StreamFromFile {
    public static void main(String[] args) {
        // 문자열 분리
        String text = "A,B,C,D,E";
        Stream<String> stream1 = Arrays.stream(text.split(","));
        System.out.println("문자열 분리:");
        stream1.forEach(System.out::println);
        
        
        // 문자 스트림
        IntStream chars = "Hello".chars();
        System.out.println("\n문자 스트림:");
        chars.forEach(c -> System.out.print((char)c + " "));
        
        
        // 파일 읽기 (예제)
        System.out.println("\n\n파일 스트림 예제:");
        System.out.println("try (Stream<String> lines = Files.lines(path)) {");
        System.out.println("    lines.forEach(System.out::println);");
        System.out.println("}");
    }
}
```

---

## 3. 중간 연산 (Intermediate)

### 3.1 filter - 필터링

```java
public class StreamFilter {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // 짝수만
        System.out.println("짝수:");
        numbers.stream()
            .filter(n -> n % 2 == 0)
            .forEach(System.out::println);
        
        
        // 5보다 큰 수
        System.out.println("\n5보다 큰 수:");
        numbers.stream()
            .filter(n -> n > 5)
            .forEach(System.out::println);
        
        
        // 여러 조건
        System.out.println("\n짝수이면서 5보다 큰 수:");
        numbers.stream()
            .filter(n -> n % 2 == 0)
            .filter(n -> n > 5)
            .forEach(System.out::println);
    }
}
```

### 3.2 map - 변환

```java
public class StreamMap {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("apple", "banana", "cherry");
        
        // 대문자 변환
        System.out.println("대문자:");
        words.stream()
            .map(String::toUpperCase)
            .forEach(System.out::println);
        
        
        // 길이로 변환
        System.out.println("\n문자열 길이:");
        words.stream()
            .map(String::length)
            .forEach(System.out::println);
        
        
        // 객체 변환
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        System.out.println("\n제곱:");
        numbers.stream()
            .map(n -> n * n)
            .forEach(System.out::println);
    }
}
```

### 3.3 flatMap - 평탄화

```java
public class StreamFlatMap {
    public static void main(String[] args) {
        // 2차원 리스트 평탄화
        List<List<Integer>> nested = Arrays.asList(
            Arrays.asList(1, 2, 3),
            Arrays.asList(4, 5),
            Arrays.asList(6, 7, 8, 9)
        );
        
        System.out.println("평탄화:");
        nested.stream()
            .flatMap(List::stream)
            .forEach(System.out::println);
        
        
        // 문자열 분리 후 평탄화
        List<String> sentences = Arrays.asList(
            "Hello World",
            "Java Stream",
            "API Tutorial"
        );
        
        System.out.println("\n모든 단어:");
        sentences.stream()
            .flatMap(s -> Arrays.stream(s.split(" ")))
            .forEach(System.out::println);
        
        
        // 문자로 분리
        System.out.println("\n모든 문자:");
        Arrays.asList("ABC", "DEF")
            .stream()
            .flatMap(s -> s.chars().mapToObj(c -> (char)c))
            .forEach(System.out::println);
    }
}
```

### 3.4 distinct - 중복 제거

```java
public class StreamDistinct {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 2, 3, 3, 3, 4, 5, 5);
        
        System.out.println("원본: " + numbers);
        
        System.out.println("\n중복 제거:");
        numbers.stream()
            .distinct()
            .forEach(System.out::println);
        
        
        // 문자열
        List<String> words = Arrays.asList("apple", "banana", "apple", "cherry");
        System.out.println("\n중복 제거:");
        words.stream()
            .distinct()
            .forEach(System.out::println);
    }
}
```

### 3.5 sorted - 정렬

```java
public class StreamSorted {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9, 3, 7);
        
        // 오름차순
        System.out.println("오름차순:");
        numbers.stream()
            .sorted()
            .forEach(System.out::println);
        
        
        // 내림차순
        System.out.println("\n내림차순:");
        numbers.stream()
            .sorted(Comparator.reverseOrder())
            .forEach(System.out::println);
        
        
        // 커스텀 정렬
        List<String> words = Arrays.asList("apple", "pie", "banana", "cat");
        System.out.println("\n길이순:");
        words.stream()
            .sorted(Comparator.comparingInt(String::length))
            .forEach(System.out::println);
    }
}
```

### 3.6 limit / skip - 제한

```java
public class StreamLimitSkip {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // limit (처음 N개)
        System.out.println("처음 5개:");
        numbers.stream()
            .limit(5)
            .forEach(System.out::println);
        
        
        // skip (처음 N개 건너뛰기)
        System.out.println("\n처음 5개 건너뛰기:");
        numbers.stream()
            .skip(5)
            .forEach(System.out::println);
        
        
        // 조합 (페이징)
        System.out.println("\n3번째부터 5개:");
        numbers.stream()
            .skip(2)
            .limit(5)
            .forEach(System.out::println);
    }
}
```

### 3.7 peek - 디버깅

```java
public class StreamPeek {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        // 각 단계 확인
        System.out.println("=== 파이프라인 추적 ===");
        List<Integer> result = numbers.stream()
            .peek(n -> System.out.println("원본: " + n))
            .filter(n -> n % 2 == 0)
            .peek(n -> System.out.println("필터 후: " + n))
            .map(n -> n * n)
            .peek(n -> System.out.println("제곱 후: " + n))
            .collect(Collectors.toList());
        
        System.out.println("\n최종 결과: " + result);
    }
}
```

---

## 4. 최종 연산 (Terminal)

### 4.1 forEach / forEachOrdered

```java
public class StreamForEach {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("A", "B", "C", "D", "E");
        
        // forEach
        System.out.println("forEach:");
        words.stream()
            .forEach(System.out::println);
        
        
        // forEachOrdered (병렬에서도 순서 보장)
        System.out.println("\nforEachOrdered (병렬):");
        words.parallelStream()
            .forEachOrdered(System.out::println);
    }
}
```

### 4.2 collect - 수집

```java
public class StreamCollect {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("apple", "banana", "cherry");
        
        // List로 수집
        List<String> list = words.stream()
            .map(String::toUpperCase)
            .collect(Collectors.toList());
        System.out.println("List: " + list);
        
        
        // Set으로 수집
        Set<String> set = words.stream()
            .collect(Collectors.toSet());
        System.out.println("Set: " + set);
        
        
        // 문자열 결합
        String joined = words.stream()
            .collect(Collectors.joining(", "));
        System.out.println("Joined: " + joined);
        
        
        // Map으로 수집
        Map<String, Integer> map = words.stream()
            .collect(Collectors.toMap(
                w -> w,           // key
                String::length    // value
            ));
        System.out.println("Map: " + map);
    }
}
```

### 4.3 reduce - 축소

```java
public class StreamReduce {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        // 합계
        int sum = numbers.stream()
            .reduce(0, (a, b) -> a + b);
        System.out.println("합계: " + sum);
        
        
        // 곱셈
        int product = numbers.stream()
            .reduce(1, (a, b) -> a * b);
        System.out.println("곱셈: " + product);
        
        
        // 최대값
        Optional<Integer> max = numbers.stream()
            .reduce((a, b) -> a > b ? a : b);
        System.out.println("최대값: " + max.orElse(0));
        
        
        // 문자열 결합
        List<String> words = Arrays.asList("Java", "Stream", "API");
        String result = words.stream()
            .reduce("", (a, b) -> a + " " + b).trim();
        System.out.println("결합: " + result);
    }
}
```

### 4.4 count / sum / average

```java
public class StreamStatistics {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        // count
        long count = numbers.stream()
            .filter(n -> n % 2 == 0)
            .count();
        System.out.println("짝수 개수: " + count);
        
        
        // sum (IntStream)
        int sum = numbers.stream()
            .mapToInt(Integer::intValue)
            .sum();
        System.out.println("합계: " + sum);
        
        
        // average (IntStream)
        OptionalDouble avg = numbers.stream()
            .mapToInt(Integer::intValue)
            .average();
        System.out.println("평균: " + avg.orElse(0));
        
        
        // 통계 (IntStream)
        IntSummaryStatistics stats = numbers.stream()
            .mapToInt(Integer::intValue)
            .summaryStatistics();
        
        System.out.println("\n=== 통계 ===");
        System.out.println("개수: " + stats.getCount());
        System.out.println("합계: " + stats.getSum());
        System.out.println("평균: " + stats.getAverage());
        System.out.println("최소: " + stats.getMin());
        System.out.println("최대: " + stats.getMax());
    }
}
```

### 4.5 min / max

```java
public class StreamMinMax {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(5, 2, 8, 1, 9, 3, 7);
        
        // min
        Optional<Integer> min = numbers.stream()
            .min(Integer::compareTo);
        System.out.println("최소값: " + min.orElse(0));
        
        
        // max
        Optional<Integer> max = numbers.stream()
            .max(Integer::compareTo);
        System.out.println("최대값: " + max.orElse(0));
        
        
        // 문자열 길이 기준
        List<String> words = Arrays.asList("apple", "pie", "banana");
        
        Optional<String> shortest = words.stream()
            .min(Comparator.comparingInt(String::length));
        System.out.println("가장 짧은 단어: " + shortest.orElse(""));
        
        Optional<String> longest = words.stream()
            .max(Comparator.comparingInt(String::length));
        System.out.println("가장 긴 단어: " + longest.orElse(""));
    }
}
```

### 4.6 anyMatch / allMatch / noneMatch

```java
public class StreamMatch {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(2, 4, 6, 8, 10);
        
        // anyMatch (하나라도)
        boolean hasEven = numbers.stream()
            .anyMatch(n -> n % 2 == 0);
        System.out.println("짝수 있음: " + hasEven);
        
        
        // allMatch (모두)
        boolean allEven = numbers.stream()
            .allMatch(n -> n % 2 == 0);
        System.out.println("모두 짝수: " + allEven);
        
        
        // noneMatch (없음)
        boolean noOdd = numbers.stream()
            .noneMatch(n -> n % 2 == 1);
        System.out.println("홀수 없음: " + noOdd);
    }
}
```

### 4.7 findFirst / findAny

```java
public class StreamFind {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("apple", "banana", "cherry", "date");
        
        // findFirst (첫 번째)
        Optional<String> first = words.stream()
            .filter(w -> w.startsWith("c"))
            .findFirst();
        System.out.println("c로 시작하는 첫 단어: " + first.orElse("없음"));
        
        
        // findAny (아무거나 - 병렬에서 유리)
        Optional<String> any = words.parallelStream()
            .filter(w -> w.length() > 5)
            .findAny();
        System.out.println("길이 5 초과 단어: " + any.orElse("없음"));
    }
}
```

---

## 5. Collector

### 5.1 기본 Collector

```java
public class BasicCollectors {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("apple", "banana", "cherry", "date");
        
        // toList
        List<String> list = words.stream()
            .collect(Collectors.toList());
        System.out.println("toList: " + list);
        
        
        // toSet
        Set<Integer> set = Arrays.asList(1, 2, 2, 3, 3, 3).stream()
            .collect(Collectors.toSet());
        System.out.println("toSet: " + set);
        
        
        // toCollection (특정 컬렉션)
        LinkedList<String> linkedList = words.stream()
            .collect(Collectors.toCollection(LinkedList::new));
        System.out.println("toCollection: " + linkedList);
        
        
        // toMap
        Map<String, Integer> map = words.stream()
            .collect(Collectors.toMap(
                w -> w,
                String::length
            ));
        System.out.println("toMap: " + map);
    }
}
```

### 5.2 joining - 문자열 결합

```java
public class JoiningCollector {
    public static void main(String[] args) {
        List<String> words = Arrays.asList("Java", "Stream", "API");
        
        // 기본 결합
        String result1 = words.stream()
            .collect(Collectors.joining());
        System.out.println("기본: " + result1);
        
        
        // 구분자
        String result2 = words.stream()
            .collect(Collectors.joining(", "));
        System.out.println("구분자: " + result2);
        
        
        // 접두사, 구분자, 접미사
        String result3 = words.stream()
            .collect(Collectors.joining(", ", "[", "]"));
        System.out.println("접두/접미사: " + result3);
    }
}
```

### 5.3 groupingBy - 그룹화

```java
public class GroupingByCollector {
    static class Student {
        String name;
        int grade;
        int score;
        
        Student(String name, int grade, int score) {
            this.name = name;
            this.grade = grade;
            this.score = score;
        }
        
        @Override
        public String toString() {
            return name + "(" + score + ")";
        }
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", 1, 85),
            new Student("Bob", 2, 92),
            new Student("Charlie", 1, 78),
            new Student("David", 2, 88),
            new Student("Eve", 1, 95)
        );
        
        // 학년별 그룹화
        Map<Integer, List<Student>> byGrade = students.stream()
            .collect(Collectors.groupingBy(s -> s.grade));
        
        System.out.println("=== 학년별 그룹 ===");
        byGrade.forEach((grade, list) -> 
            System.out.println(grade + "학년: " + list));
        
        
        // 학년별 학생 수
        Map<Integer, Long> countByGrade = students.stream()
            .collect(Collectors.groupingBy(
                s -> s.grade,
                Collectors.counting()
            ));
        System.out.println("\n학년별 학생 수: " + countByGrade);
        
        
        // 학년별 평균 점수
        Map<Integer, Double> avgByGrade = students.stream()
            .collect(Collectors.groupingBy(
                s -> s.grade,
                Collectors.averagingInt(s -> s.score)
            ));
        System.out.println("학년별 평균: " + avgByGrade);
    }
}
```

### 5.4 partitioningBy - 분할

```java
public class PartitioningByCollector {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // 짝수/홀수 분할
        Map<Boolean, List<Integer>> partitioned = numbers.stream()
            .collect(Collectors.partitioningBy(n -> n % 2 == 0));
        
        System.out.println("짝수: " + partitioned.get(true));
        System.out.println("홀수: " + partitioned.get(false));
        
        
        // 5보다 큰/작은 수 분할
        Map<Boolean, List<Integer>> partitioned2 = numbers.stream()
            .collect(Collectors.partitioningBy(n -> n > 5));
        
        System.out.println("\n5보다 큰 수: " + partitioned2.get(true));
        System.out.println("5 이하: " + partitioned2.get(false));
    }
}
```

### 5.5 통계 Collector

```java
public class StatisticsCollector {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        
        // counting
        Long count = numbers.stream()
            .collect(Collectors.counting());
        System.out.println("개수: " + count);
        
        
        // summingInt
        Integer sum = numbers.stream()
            .collect(Collectors.summingInt(Integer::intValue));
        System.out.println("합계: " + sum);
        
        
        // averagingInt
        Double avg = numbers.stream()
            .collect(Collectors.averagingInt(Integer::intValue));
        System.out.println("평균: " + avg);
        
        
        // summarizingInt
        IntSummaryStatistics stats = numbers.stream()
            .collect(Collectors.summarizingInt(Integer::intValue));
        
        System.out.println("\n=== 통계 ===");
        System.out.println(stats);
    }
}
```

---

## 6. 병렬 스트림

### 6.1 병렬 스트림 생성

```java
public class ParallelStreamBasic {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
        
        // 순차 스트림
        System.out.println("=== 순차 스트림 ===");
        numbers.stream()
            .forEach(n -> System.out.println(
                Thread.currentThread().getName() + ": " + n));
        
        
        // 병렬 스트림
        System.out.println("\n=== 병렬 스트림 ===");
        numbers.parallelStream()
            .forEach(n -> System.out.println(
                Thread.currentThread().getName() + ": " + n));
        
        
        // parallel() 메서드
        System.out.println("\n=== parallel() ===");
        numbers.stream()
            .parallel()
            .forEach(n -> System.out.println(
                Thread.currentThread().getName() + ": " + n));
    }
}
```

### 6.2 성능 비교

```java
public class ParallelPerformance {
    public static void main(String[] args) {
        List<Integer> numbers = IntStream.rangeClosed(1, 10000000)
            .boxed()
            .collect(Collectors.toList());
        
        // 순차 스트림
        long start = System.currentTimeMillis();
        long sum1 = numbers.stream()
            .mapToLong(Integer::longValue)
            .sum();
        long time1 = System.currentTimeMillis() - start;
        
        
        // 병렬 스트림
        start = System.currentTimeMillis();
        long sum2 = numbers.parallelStream()
            .mapToLong(Integer::longValue)
            .sum();
        long time2 = System.currentTimeMillis() - start;
        
        
        System.out.println("순차: " + time1 + "ms (sum=" + sum1 + ")");
        System.out.println("병렬: " + time2 + "ms (sum=" + sum2 + ")");
        System.out.println("속도 향상: " + (time1 - time2) + "ms");
    }
}
```

### 6.3 병렬 스트림 주의사항

```java
public class ParallelCautions {
    public static void main(String[] args) {
        System.out.println("=== 병렬 스트림 주의사항 ===\n");
        
        System.out.println("1. 스레드 안전하지 않은 연산");
        System.out.println("   ❌ 외부 변수 수정");
        System.out.println("   ❌ 공유 자원 접근\n");
        
        System.out.println("2. 순서 보장 안 됨");
        System.out.println("   - forEachOrdered 사용\n");
        
        System.out.println("3. 적합한 경우");
        System.out.println("   ✅ 대량 데이터");
        System.out.println("   ✅ 독립적인 연산");
        System.out.println("   ✅ CPU 집약적 작업\n");
        
        System.out.println("4. 부적합한 경우");
        System.out.println("   ❌ 소량 데이터");
        System.out.println("   ❌ 순서 의존 연산");
        System.out.println("   ❌ IO 작업");
        
        
        // 잘못된 예
        List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
        List<Integer> result = new ArrayList<>();
        
        // ❌ 위험! (스레드 안전하지 않음)
        // numbers.parallelStream().forEach(result::add);
        
        // ✅ 안전
        List<Integer> result2 = numbers.parallelStream()
            .collect(Collectors.toList());
    }
}
```

---

## 7. 실전 활용

### 7.1 데이터 변환과 필터링

```java
public class DataTransformation {
    static class Product {
        String name;
        String category;
        int price;
        
        Product(String name, String category, int price) {
            this.name = name;
            this.category = category;
            this.price = price;
        }
        
        @Override
        public String toString() {
            return name + "(" + category + ", " + price + "원)";
        }
    }
    
    public static void main(String[] args) {
        List<Product> products = Arrays.asList(
            new Product("노트북", "전자", 1500000),
            new Product("마우스", "전자", 30000),
            new Product("책상", "가구", 200000),
            new Product("의자", "가구", 150000),
            new Product("키보드", "전자", 80000)
        );
        
        // 전자 제품만 가격순 정렬
        System.out.println("=== 전자 제품 (가격순) ===");
        products.stream()
            .filter(p -> p.category.equals("전자"))
            .sorted(Comparator.comparingInt(p -> p.price))
            .forEach(System.out::println);
        
        
        // 카테고리별 평균 가격
        System.out.println("\n=== 카테고리별 평균 가격 ===");
        products.stream()
            .collect(Collectors.groupingBy(
                p -> p.category,
                Collectors.averagingInt(p -> p.price)
            ))
            .forEach((cat, avg) -> 
                System.out.println(cat + ": " + avg + "원"));
        
        
        // 10만원 이상 제품 이름
        List<String> expensive = products.stream()
            .filter(p -> p.price >= 100000)
            .map(p -> p.name)
            .collect(Collectors.toList());
        System.out.println("\n10만원 이상: " + expensive);
    }
}
```

### 7.2 복잡한 데이터 처리

```java
public class ComplexDataProcessing {
    static class Order {
        String customer;
        String product;
        int quantity;
        int price;
        
        Order(String customer, String product, int quantity, int price) {
            this.customer = customer;
            this.product = product;
            this.quantity = quantity;
            this.price = price;
        }
        
        int total() {
            return quantity * price;
        }
    }
    
    public static void main(String[] args) {
        List<Order> orders = Arrays.asList(
            new Order("Alice", "노트북", 1, 1500000),
            new Order("Bob", "마우스", 2, 30000),
            new Order("Alice", "키보드", 1, 80000),
            new Order("Charlie", "모니터", 1, 300000),
            new Order("Bob", "키보드", 1, 80000)
        );
        
        // 고객별 총 구매액
        System.out.println("=== 고객별 총 구매액 ===");
        orders.stream()
            .collect(Collectors.groupingBy(
                o -> o.customer,
                Collectors.summingInt(Order::total)
            ))
            .forEach((customer, total) -> 
                System.out.println(customer + ": " + total + "원"));
        
        
        // 가장 많이 팔린 상품
        System.out.println("\n=== 상품별 판매량 ===");
        orders.stream()
            .collect(Collectors.groupingBy(
                o -> o.product,
                Collectors.summingInt(o -> o.quantity)
            ))
            .entrySet().stream()
            .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
            .forEach(e -> 
                System.out.println(e.getKey() + ": " + e.getValue() + "개"));
        
        
        // 100만원 이상 구매 고객
        Set<String> vip = orders.stream()
            .collect(Collectors.groupingBy(
                o -> o.customer,
                Collectors.summingInt(Order::total)
            ))
            .entrySet().stream()
            .filter(e -> e.getValue() >= 1000000)
            .map(Map.Entry::getKey)
            .collect(Collectors.toSet());
        
        System.out.println("\nVIP 고객: " + vip);
    }
}
```

### 7.3 실시간 데이터 처리

```java
public class RealTimeProcessing {
    static class LogEntry {
        String level;
        String message;
        long timestamp;
        
        LogEntry(String level, String message, long timestamp) {
            this.level = level;
            this.message = message;
            this.timestamp = timestamp;
        }
        
        @Override
        public String toString() {
            return "[" + level + "] " + message;
        }
    }
    
    public static void main(String[] args) {
        List<LogEntry> logs = Arrays.asList(
            new LogEntry("INFO", "서버 시작", 1000),
            new LogEntry("ERROR", "연결 실패", 2000),
            new LogEntry("WARN", "메모리 부족", 3000),
            new LogEntry("ERROR", "타임아웃", 4000),
            new LogEntry("INFO", "요청 처리", 5000)
        );
        
        // 에러 로그만 추출
        System.out.println("=== 에러 로그 ===");
        logs.stream()
            .filter(log -> log.level.equals("ERROR"))
            .forEach(System.out::println);
        
        
        // 로그 레벨별 개수
        System.out.println("\n=== 레벨별 개수 ===");
        logs.stream()
            .collect(Collectors.groupingBy(
                log -> log.level,
                Collectors.counting()
            ))
            .forEach((level, count) -> 
                System.out.println(level + ": " + count));
    }
}
```

---

## 8. 실전 연습 문제

### 문제 1: 학생 성적 분석

```java
// 학년별 평균 점수, 최고 점수 학생
public class Problem1 {
    static class Student {
        String name;
        int grade;
        int score;
        
        Student(String name, int grade, int score) {
            this.name = name;
            this.grade = grade;
            this.score = score;
        }
        
        @Override
        public String toString() {
            return name + "(" + score + ")";
        }
    }
    
    public static void solve(List<Student> students) {
        // 1. 학년별 평균 점수
        
        // 2. 각 학년 최고 점수 학생
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", 1, 85),
            new Student("Bob", 2, 92),
            new Student("Charlie", 1, 95),
            new Student("David", 2, 88)
        );
        
        solve(students);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void solve(List<Student> students) {
    // 1. 학년별 평균 점수
    System.out.println("=== 학년별 평균 ===");
    students.stream()
        .collect(Collectors.groupingBy(
            s -> s.grade,
            Collectors.averagingInt(s -> s.score)
        ))
        .forEach((grade, avg) -> 
            System.out.println(grade + "학년: " + avg));
    
    // 2. 각 학년 최고 점수 학생
    System.out.println("\n=== 학년별 최고 점수 ===");
    students.stream()
        .collect(Collectors.groupingBy(
            s -> s.grade,
            Collectors.maxBy(Comparator.comparingInt(s -> s.score))
        ))
        .forEach((grade, student) -> 
            System.out.println(grade + "학년: " + student.orElse(null)));
}
```
</details>

---

### 문제 2: 단어 빈도 분석

```java
// 문장에서 단어 빈도 계산, 상위 3개
public class Problem2 {
    public static void solve(String text) {
        // 구현
    }
    
    public static void main(String[] args) {
        String text = "apple banana apple cherry banana apple date";
        solve(text);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void solve(String text) {
    System.out.println("=== 단어 빈도 Top 3 ===");
    Arrays.stream(text.split(" "))
        .collect(Collectors.groupingBy(
            word -> word,
            Collectors.counting()
        ))
        .entrySet().stream()
        .sorted(Map.Entry.<String, Long>comparingByValue().reversed())
        .limit(3)
        .forEach(e -> 
            System.out.println(e.getKey() + ": " + e.getValue() + "회"));
}
```
</details>

---

### 문제 3: 거래 데이터 분석

```java
// 연도별 총 거래액, 가장 많이 거래된 통화
public class Problem3 {
    static class Transaction {
        int year;
        String currency;
        int amount;
        
        Transaction(int year, String currency, int amount) {
            this.year = year;
            this.currency = currency;
            this.amount = amount;
        }
    }
    
    public static void solve(List<Transaction> transactions) {
        // 1. 연도별 총 거래액
        
        // 2. 가장 많이 거래된 통화
    }
    
    public static void main(String[] args) {
        List<Transaction> transactions = Arrays.asList(
            new Transaction(2020, "USD", 1000),
            new Transaction(2020, "EUR", 1500),
            new Transaction(2021, "USD", 2000),
            new Transaction(2021, "USD", 1000),
            new Transaction(2021, "EUR", 500)
        );
        
        solve(transactions);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void solve(List<Transaction> transactions) {
    // 1. 연도별 총 거래액
    System.out.println("=== 연도별 총 거래액 ===");
    transactions.stream()
        .collect(Collectors.groupingBy(
            t -> t.year,
            Collectors.summingInt(t -> t.amount)
        ))
        .forEach((year, total) -> 
            System.out.println(year + "년: " + total));
    
    // 2. 가장 많이 거래된 통화
    System.out.println("\n=== 통화별 거래액 ===");
    String topCurrency = transactions.stream()
        .collect(Collectors.groupingBy(
            t -> t.currency,
            Collectors.summingInt(t -> t.amount)
        ))
        .entrySet().stream()
        .max(Map.Entry.comparingByValue())
        .map(Map.Entry::getKey)
        .orElse("N/A");
    
    System.out.println("최다 거래 통화: " + topCurrency);
}
```
</details>

---

## 📌 핵심 정리

### Stream 파이프라인
```java
list.stream()           // 생성
    .filter(...)        // 중간 연산
    .map(...)           // 중간 연산
    .collect(...);      // 최종 연산
```

### 주요 중간 연산
```java
filter(predicate)       // 필터링
map(function)           // 변환
flatMap(function)       // 평탄화
distinct()              // 중복 제거
sorted()                // 정렬
limit(n)                // 제한
skip(n)                 // 건너뛰기
peek(consumer)          // 디버깅
```

### 주요 최종 연산
```java
collect(collector)      // 수집
forEach(consumer)       // 순회
reduce(identity, op)    // 축소
count()                 // 개수
min/max(comparator)     // 최소/최대
anyMatch(predicate)     // 일치 여부
findFirst/Any()         // 찾기
```

### 주요 Collector
```java
toList/Set/Map()        // 컬렉션
joining()               // 문자열 결합
groupingBy()            // 그룹화
partitioningBy()        // 분할
counting()              // 개수
summingInt()            // 합계
averagingInt()          // 평균
```

### 병렬 스트림
```java
// 생성
list.parallelStream()
list.stream().parallel()

// 주의사항
- 대량 데이터에 유리
- 스레드 안전 필요
- 순서 보장 안 됨
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Comparator](./Util-01-Comparator.md) | [다음: 03. Optional →](./Util-03-Optional.md)**

</div>
