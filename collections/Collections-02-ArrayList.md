# Collections 02. ArrayList 완전 정복

> 동적 배열의 모든 것 - ArrayList 마스터하기  
> Java API Reference

---

## 📑 목차

1. [ArrayList 기본](#1-arraylist-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [조회와 검색](#4-조회와-검색)
5. [수정과 정렬](#5-수정과-정렬)
6. [순회 방법](#6-순회-방법)
7. [내부 구조와 성능](#7-내부-구조와-성능)
8. [실전 활용](#8-실전-활용)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. ArrayList 기본

### 1.1 특징

```java
import java.util.*;

public class ArrayListBasic {
    public static void main(String[] args) {
        System.out.println("=== ArrayList 특징 ===\n");
        
        System.out.println("1. 동적 배열");
        System.out.println("   - 크기 자동 조절");
        System.out.println("   - 초기 용량 10");
        System.out.println("   - 50% 확장\n");
        
        System.out.println("2. 성능");
        System.out.println("   - 조회: O(1) 매우 빠름!");
        System.out.println("   - 끝에 추가: O(1)");
        System.out.println("   - 중간 추가/삭제: O(n)\n");
        
        System.out.println("3. 특성");
        System.out.println("   - 순서 유지");
        System.out.println("   - 중복 허용");
        System.out.println("   - null 허용");
        System.out.println("   - 동기화X (멀티스레드 안전X)");
    }
}
```

### 1.2 배열 vs ArrayList

```java
public class ArrayVsArrayList {
    public static void main(String[] args) {
        // 배열
        System.out.println("=== 배열 ===");
        int[] arr = new int[5];
        arr[0] = 10;
        System.out.println("크기 고정: " + arr.length);
        // arr[5] = 20;  // 에러!
        
        
        // ArrayList
        System.out.println("\n=== ArrayList ===");
        ArrayList<Integer> list = new ArrayList<>();
        list.add(10);
        list.add(20);
        list.add(30);
        System.out.println("크기 동적: " + list.size());
        System.out.println("내용: " + list);
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("배열: 고정 크기, 빠름");
        System.out.println("ArrayList: 동적 크기, 편리");
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class ArrayListCreation {
    public static void main(String[] args) {
        // 기본 생성 (용량 10)
        ArrayList<String> list1 = new ArrayList<>();
        System.out.println("기본 생성: " + list1);
        
        
        // 초기 용량 지정
        ArrayList<String> list2 = new ArrayList<>(100);
        System.out.println("용량 100: " + list2);
        
        
        // 다른 컬렉션으로 생성
        ArrayList<String> list3 = new ArrayList<>(Arrays.asList("A", "B", "C"));
        System.out.println("복사 생성: " + list3);
        
        
        // List 인터페이스로 (권장)
        List<String> list4 = new ArrayList<>();
        System.out.println("List 타입: " + list4);
    }
}
```

### 2.2 초기화 방법들

```java
public class ArrayListInit {
    public static void main(String[] args) {
        // 방법 1: add
        List<String> list1 = new ArrayList<>();
        list1.add("Apple");
        list1.add("Banana");
        System.out.println("add: " + list1);
        
        
        // 방법 2: Arrays.asList
        List<String> list2 = new ArrayList<>(
            Arrays.asList("Apple", "Banana", "Cherry")
        );
        System.out.println("Arrays.asList: " + list2);
        
        
        // 방법 3: List.of (Java 9+, 불변)
        List<String> immutable = List.of("Apple", "Banana");
        List<String> list3 = new ArrayList<>(immutable);
        System.out.println("List.of: " + list3);
        
        
        // 방법 4: 이중 중괄호 (비권장)
        List<String> list4 = new ArrayList<>() {{
            add("Apple");
            add("Banana");
        }};
        System.out.println("이중 중괄호: " + list4);
        
        
        // 방법 5: Stream (Java 8+)
        List<Integer> list5 = Stream.of(1, 2, 3, 4, 5)
                                   .collect(Collectors.toList());
        System.out.println("Stream: " + list5);
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (add)

```java
public class ArrayListAdd {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        
        // 끝에 추가
        list.add("Apple");
        list.add("Banana");
        System.out.println("추가 후: " + list);
        
        
        // 특정 위치에 추가
        list.add(1, "Cherry");
        System.out.println("중간 추가: " + list);
        // [Apple, Cherry, Banana]
        
        
        // 여러 개 추가
        list.addAll(Arrays.asList("Durian", "Elderberry"));
        System.out.println("여러 개: " + list);
        
        
        // 특정 위치에 여러 개
        list.addAll(2, Arrays.asList("Fig", "Grape"));
        System.out.println("중간 여러 개: " + list);
    }
}
```

### 3.2 삭제 (remove)

```java
public class ArrayListRemove {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 인덱스로 삭제
        list.remove(1);  // "B" 삭제
        System.out.println("인덱스 삭제: " + list);
        
        
        // 객체로 삭제
        list.remove("D");
        System.out.println("객체 삭제: " + list);
        
        
        // 전체 삭제
        list.clear();
        System.out.println("전체 삭제: " + list);
        
        
        // Integer 리스트 주의!
        List<Integer> nums = new ArrayList<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        nums.remove(1);  // 인덱스 1 삭제 (2가 삭제됨)
        System.out.println("인덱스: " + nums);
        
        nums.remove(Integer.valueOf(4));  // 값 4 삭제
        System.out.println("값: " + nums);
    }
}
```

### 3.3 조건부 삭제

```java
public class ArrayListConditionalRemove {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(
            Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
        );
        
        // removeIf (Java 8+)
        list.removeIf(n -> n % 2 == 0);  // 짝수 삭제
        System.out.println("짝수 삭제: " + list);
        
        
        // Iterator 사용
        List<String> fruits = new ArrayList<>(
            Arrays.asList("Apple", "Apricot", "Banana", "Avocado")
        );
        
        Iterator<String> it = fruits.iterator();
        while (it.hasNext()) {
            if (it.next().startsWith("A")) {
                it.remove();
            }
        }
        System.out.println("A 시작 삭제: " + fruits);
    }
}
```

---

## 4. 조회와 검색

### 4.1 조회 (get)

```java
public class ArrayListGet {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 인덱스로 조회
        String first = list.get(0);
        String last = list.get(list.size() - 1);
        
        System.out.println("첫 번째: " + first);
        System.out.println("마지막: " + last);
        
        
        // 범위 체크
        try {
            String item = list.get(10);  // 에러!
        } catch (IndexOutOfBoundsException e) {
            System.out.println("인덱스 초과!");
        }
        
        
        // 안전한 조회
        int index = 10;
        if (index >= 0 && index < list.size()) {
            System.out.println(list.get(index));
        } else {
            System.out.println("유효하지 않은 인덱스");
        }
    }
}
```

### 4.2 검색 (indexOf, contains)

```java
public class ArrayListSearch {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "C", "B", "D")
        );
        
        // 포함 여부
        boolean hasB = list.contains("B");
        boolean hasZ = list.contains("Z");
        
        System.out.println("B 포함? " + hasB);  // true
        System.out.println("Z 포함? " + hasZ);  // false
        
        
        // 인덱스 찾기
        int index1 = list.indexOf("B");  // 첫 번째
        int index2 = list.lastIndexOf("B");  // 마지막
        int index3 = list.indexOf("Z");  // -1 (없음)
        
        System.out.println("B 첫 인덱스: " + index1);  // 1
        System.out.println("B 마지막: " + index2);     // 3
        System.out.println("Z 인덱스: " + index3);     // -1
        
        
        // 빈 리스트 확인
        boolean empty = list.isEmpty();
        int size = list.size();
        
        System.out.println("비어있음? " + empty);
        System.out.println("크기: " + size);
    }
}
```

---

## 5. 수정과 정렬

### 5.1 수정 (set)

```java
public class ArrayListSet {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "C", "D")
        );
        
        System.out.println("원본: " + list);
        
        // 특정 위치 수정
        String old = list.set(1, "Z");
        
        System.out.println("수정 후: " + list);
        System.out.println("이전 값: " + old);
        
        
        // 모든 요소 수정
        list.replaceAll(s -> s.toLowerCase());
        System.out.println("소문자: " + list);
    }
}
```

### 5.2 정렬 (sort)

```java
public class ArrayListSort {
    public static void main(String[] args) {
        // 숫자 정렬
        List<Integer> nums = new ArrayList<>(
            Arrays.asList(5, 2, 8, 1, 9, 3)
        );
        
        Collections.sort(nums);  // 오름차순
        System.out.println("오름차순: " + nums);
        
        Collections.sort(nums, Collections.reverseOrder());  // 내림차순
        System.out.println("내림차순: " + nums);
        
        
        // 문자열 정렬
        List<String> words = new ArrayList<>(
            Arrays.asList("banana", "apple", "cherry")
        );
        
        Collections.sort(words);
        System.out.println("사전순: " + words);
        
        
        // List.sort (Java 8+)
        words.sort(Comparator.naturalOrder());
        System.out.println("자연순: " + words);
        
        words.sort(Comparator.reverseOrder());
        System.out.println("역순: " + words);
        
        
        // 커스텀 정렬
        words.sort((a, b) -> Integer.compare(a.length(), b.length()));
        System.out.println("길이순: " + words);
    }
}
```

---

## 6. 순회 방법

### 6.1 for 루프

```java
public class ArrayListIteration {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 일반 for
        System.out.println("=== 일반 for ===");
        for (int i = 0; i < list.size(); i++) {
            System.out.print(list.get(i) + " ");
        }
        System.out.println();
        
        
        // 향상된 for
        System.out.println("\n=== 향상된 for ===");
        for (String item : list) {
            System.out.print(item + " ");
        }
        System.out.println();
        
        
        // 역순
        System.out.println("\n=== 역순 ===");
        for (int i = list.size() - 1; i >= 0; i--) {
            System.out.print(list.get(i) + " ");
        }
        System.out.println();
    }
}
```

### 6.2 Iterator

```java
public class ArrayListIterator {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "C", "D")
        );
        
        // Iterator
        System.out.println("=== Iterator ===");
        Iterator<String> it = list.iterator();
        while (it.hasNext()) {
            System.out.print(it.next() + " ");
        }
        System.out.println();
        
        
        // ListIterator (양방향)
        System.out.println("\n=== ListIterator ===");
        ListIterator<String> lit = list.listIterator();
        
        while (lit.hasNext()) {
            System.out.print(lit.next() + " ");
        }
        System.out.println();
        
        while (lit.hasPrevious()) {
            System.out.print(lit.previous() + " ");
        }
        System.out.println();
    }
}
```

### 6.3 Stream과 forEach

```java
public class ArrayListForEach {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        // forEach
        System.out.println("=== forEach ===");
        list.forEach(n -> System.out.print(n + " "));
        System.out.println();
        
        
        // 메서드 참조
        System.out.println("\n=== 메서드 참조 ===");
        list.forEach(System.out::println);
        
        
        // Stream
        System.out.println("\n=== Stream ===");
        list.stream()
           .filter(n -> n % 2 == 0)
           .map(n -> n * 2)
           .forEach(System.out::println);
    }
}
```

---

## 7. 내부 구조와 성능

### 7.1 내부 배열

```java
public class ArrayListInternal {
    public static void main(String[] args) {
        System.out.println("=== 내부 구조 ===\n");
        
        System.out.println("1. 초기 용량: 10");
        System.out.println("2. 확장: 1.5배 (50% 증가)");
        System.out.println("3. 복사: System.arraycopy");
        System.out.println("4. 요소: Object[] 배열\n");
        
        
        // 용량 확인 (리플렉션)
        List<Integer> list = new ArrayList<>();
        System.out.println("초기 size: " + list.size());
        
        for (int i = 0; i < 15; i++) {
            list.add(i);
            if (i == 9 || i == 14) {
                System.out.println("size " + list.size() + 
                                 "일 때 확장됨");
            }
        }
    }
}
```

### 7.2 시간 복잡도

```java
public class ArrayListComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("get(i):       O(1)");
        System.out.println("  -> 배열 인덱스 접근\n");
        
        System.out.println("add(e):       O(1) 평균");
        System.out.println("  -> 끝에 추가");
        System.out.println("  -> 용량 부족 시 O(n)\n");
        
        System.out.println("add(i, e):    O(n)");
        System.out.println("  -> 뒤 요소 이동\n");
        
        System.out.println("remove(i):    O(n)");
        System.out.println("  -> 뒤 요소 이동\n");
        
        System.out.println("contains(e):  O(n)");
        System.out.println("  -> 선형 탐색\n");
        
        System.out.println("clear():      O(n)");
        System.out.println("  -> 모든 참조 해제");
    }
}
```

### 7.3 성능 측정

```java
public class ArrayListPerformance {
    public static void main(String[] args) {
        int n = 100000;
        
        // 끝에 추가 (빠름)
        List<Integer> list1 = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            list1.add(i);
        }
        long time1 = System.currentTimeMillis() - start;
        System.out.println("끝에 추가: " + time1 + "ms");
        
        
        // 앞에 추가 (느림)
        List<Integer> list2 = new ArrayList<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < 1000; i++) {
            list2.add(0, i);
        }
        long time2 = System.currentTimeMillis() - start;
        System.out.println("앞에 추가: " + time2 + "ms");
        
        
        // 조회 (매우 빠름)
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            list1.get(i);
        }
        long time3 = System.currentTimeMillis() - start;
        System.out.println("조회: " + time3 + "ms");
    }
}
```

---

## 8. 실전 활용

### 8.1 중복 제거

```java
public class RemoveDuplicates {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(
            Arrays.asList(1, 2, 2, 3, 3, 3, 4)
        );
        
        // 방법 1: Set 사용
        List<Integer> result1 = new ArrayList<>(new HashSet<>(list));
        System.out.println("Set: " + result1);
        
        
        // 방법 2: Stream distinct
        List<Integer> result2 = list.stream()
                                   .distinct()
                                   .collect(Collectors.toList());
        System.out.println("Stream: " + result2);
    }
}
```

### 8.2 필터링

```java
public class Filtering {
    public static void main(String[] args) {
        List<Integer> nums = new ArrayList<>(
            Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
        );
        
        // 짝수만
        List<Integer> evens = nums.stream()
                                 .filter(n -> n % 2 == 0)
                                 .collect(Collectors.toList());
        System.out.println("짝수: " + evens);
        
        
        // 5보다 큰 수
        List<Integer> greaterThan5 = nums.stream()
                                        .filter(n -> n > 5)
                                        .collect(Collectors.toList());
        System.out.println("5 초과: " + greaterThan5);
    }
}
```

### 8.3 변환

```java
public class Transformation {
    public static void main(String[] args) {
        List<String> words = new ArrayList<>(
            Arrays.asList("apple", "banana", "cherry")
        );
        
        // 대문자 변환
        List<String> upper = words.stream()
                                 .map(String::toUpperCase)
                                 .collect(Collectors.toList());
        System.out.println("대문자: " + upper);
        
        
        // 길이 변환
        List<Integer> lengths = words.stream()
                                    .map(String::length)
                                    .collect(Collectors.toList());
        System.out.println("길이: " + lengths);
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 최대값 찾기

```java
// 리스트에서 최대값 찾기
public class Problem1 {
    public static int findMax(List<Integer> list) {
        // 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        List<Integer> nums = Arrays.asList(5, 2, 8, 1, 9, 3);
        System.out.println("Max: " + findMax(nums));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    // 방법 1: 반복문
    public static int findMax1(List<Integer> list) {
        if (list.isEmpty()) {
            throw new IllegalArgumentException("Empty");
        }
        
        int max = list.get(0);
        for (int num : list) {
            if (num > max) max = num;
        }
        return max;
    }
    
    // 방법 2: Collections
    public static int findMax2(List<Integer> list) {
        return Collections.max(list);
    }
    
    // 방법 3: Stream
    public static int findMax3(List<Integer> list) {
        return list.stream()
                  .max(Integer::compareTo)
                  .orElseThrow();
    }
}
```
</details>

---

### 문제 2: 리스트 역순

```java
// 리스트 역순으로 만들기
public class Problem2 {
    public static List<Integer> reverse(List<Integer> list) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);
        System.out.println("Reversed: " + reverse(nums));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    // 방법 1: Collections.reverse
    public static List<Integer> reverse1(List<Integer> list) {
        List<Integer> result = new ArrayList<>(list);
        Collections.reverse(result);
        return result;
    }
    
    // 방법 2: 역순 추가
    public static List<Integer> reverse2(List<Integer> list) {
        List<Integer> result = new ArrayList<>();
        for (int i = list.size() - 1; i >= 0; i--) {
            result.add(list.get(i));
        }
        return result;
    }
    
    // 방법 3: Stream
    public static List<Integer> reverse3(List<Integer> list) {
        List<Integer> result = new ArrayList<>(list);
        Collections.reverse(result);
        return result;
    }
}
```
</details>

---

### 문제 3: 부분 리스트 합

```java
// 부분 리스트의 합 계산
public class Problem3 {
    public static List<Integer> partialSums(List<Integer> list) {
        // 누적 합 리스트 반환
        // [1,2,3,4,5] -> [1,3,6,10,15]
        return null;
    }
    
    public static void main(String[] args) {
        List<Integer> nums = Arrays.asList(1, 2, 3, 4, 5);
        System.out.println("Partial sums: " + partialSums(nums));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    // 방법 1: 반복문
    public static List<Integer> partialSums1(List<Integer> list) {
        List<Integer> result = new ArrayList<>();
        int sum = 0;
        
        for (int num : list) {
            sum += num;
            result.add(sum);
        }
        
        return result;
    }
    
    // 방법 2: Stream
    public static List<Integer> partialSums2(List<Integer> list) {
        List<Integer> result = new ArrayList<>();
        list.stream()
           .reduce(0, (sum, n) -> {
               int newSum = sum + n;
               result.add(newSum);
               return newSum;
           });
        return result;
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// 동적 배열
List<String> list = new ArrayList<>();
list.add("A");  // 자동 확장
```

### 주요 메서드
```java
// 추가
list.add("A");
list.add(0, "B");
list.addAll(Arrays.asList("C", "D"));

// 삭제
list.remove(0);
list.remove("A");
list.clear();

// 조회
list.get(0);
list.contains("A");
list.indexOf("A");

// 수정
list.set(0, "Z");
Collections.sort(list);

// 크기
list.size();
list.isEmpty();
```

### 시간 복잡도
```
get:       O(1)
add (끝):   O(1)
add (중간): O(n)
remove:    O(n)
contains:  O(n)
```

### 사용 시기
```
✅ 조회가 많을 때
✅ 끝에 추가할 때
✅ 순서가 중요할 때
❌ 중간 삽입/삭제 많을 때
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Collections 개요](./Collections-01-Overview.md) | [다음: 03. LinkedList →](./Collections-03-LinkedList.md)**

</div>
