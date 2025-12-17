# Collections 16. Collections 유틸 클래스

> Collections 유틸리티 메서드 완전 정복  
> Java API Reference

---

## 📑 목차

1. [Collections 클래스 소개](#1-collections-클래스-소개)
2. [정렬](#2-정렬)
3. [검색](#3-검색)
4. [변환](#4-변환)
5. [동기화](#5-동기화)
6. [불변 컬렉션](#6-불변-컬렉션)
7. [기타 유틸](#7-기타-유틸)
8. [실전 연습 문제](#8-실전-연습-문제)

---

## 1. Collections 클래스 소개

### 1.1 개요

```java
import java.util.*;

public class CollectionsIntro {
    public static void main(String[] args) {
        System.out.println("=== Collections 클래스 ===\n");
        
        System.out.println("1. 정적 메서드 모음");
        System.out.println("   - 인스턴스 생성 불가");
        System.out.println("   - 모두 static 메서드\n");
        
        System.out.println("2. 주요 기능");
        System.out.println("   - 정렬 (sort, reverse)");
        System.out.println("   - 검색 (binarySearch, min, max)");
        System.out.println("   - 변환 (shuffle, rotate, swap)");
        System.out.println("   - 동기화 (synchronized*)");
        System.out.println("   - 불변 (unmodifiable*)\n");
        
        System.out.println("3. 위치");
        System.out.println("   - java.util.Collections");
    }
}
```

---

## 2. 정렬

### 2.1 기본 정렬

```java
public class Sorting {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(
            Arrays.asList(5, 2, 8, 1, 9, 3, 7)
        );
        
        System.out.println("원본: " + list);
        
        
        // 오름차순 정렬
        Collections.sort(list);
        System.out.println("정렬: " + list);
        // [1, 2, 3, 5, 7, 8, 9]
        
        
        // 내림차순 정렬
        Collections.sort(list, Collections.reverseOrder());
        System.out.println("역순: " + list);
        // [9, 8, 7, 5, 3, 2, 1]
        
        
        // 역순 변환
        Collections.reverse(list);
        System.out.println("reverse: " + list);
        // [1, 2, 3, 5, 7, 8, 9]
    }
}
```

### 2.2 커스텀 정렬

```java
public class CustomSorting {
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
        List<Student> students = new ArrayList<>();
        students.add(new Student("Alice", 85));
        students.add(new Student("Bob", 92));
        students.add(new Student("Charlie", 78));
        
        System.out.println("원본: " + students);
        
        
        // 점수순 정렬
        Collections.sort(students, 
            Comparator.comparingInt(s -> s.score));
        System.out.println("점수순: " + students);
        
        
        // 점수 역순
        Collections.sort(students, 
            Comparator.comparingInt((Student s) -> s.score).reversed());
        System.out.println("점수 역순: " + students);
    }
}
```

---

## 3. 검색

### 3.1 이진 검색

```java
public class BinarySearch {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(1, 3, 5, 7, 9, 11, 13);
        
        System.out.println("List: " + list);
        
        
        // binarySearch (정렬 필수!)
        int index1 = Collections.binarySearch(list, 7);
        System.out.println("7의 인덱스: " + index1);  // 3
        
        
        // 없는 값 (음수 반환)
        int index2 = Collections.binarySearch(list, 6);
        System.out.println("6의 인덱스: " + index2);  // -4
        // -(insertion point) - 1
        
        
        // Comparator와 함께
        List<String> words = Arrays.asList("a", "c", "e", "g");
        int index3 = Collections.binarySearch(words, "c", 
            String.CASE_INSENSITIVE_ORDER);
        System.out.println("c의 인덱스: " + index3);  // 1
    }
}
```

### 3.2 최소/최대값

```java
public class MinMax {
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(5, 2, 8, 1, 9, 3, 7);
        
        // 최소값
        int min = Collections.min(list);
        System.out.println("최소: " + min);  // 1
        
        
        // 최대값
        int max = Collections.max(list);
        System.out.println("최대: " + max);  // 9
        
        
        // Comparator와 함께
        List<String> words = Arrays.asList("apple", "banana", "cherry");
        
        String minWord = Collections.min(words, 
            Comparator.comparingInt(String::length));
        System.out.println("가장 짧은 단어: " + minWord);  // apple
        
        String maxWord = Collections.max(words, 
            Comparator.comparingInt(String::length));
        System.out.println("가장 긴 단어: " + maxWord);  // banana
    }
}
```

### 3.3 빈도와 교체

```java
public class FrequencyAndReplace {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "A", "C", "A", "B")
        );
        
        // 빈도
        int freq = Collections.frequency(list, "A");
        System.out.println("A 빈도: " + freq);  // 3
        
        
        // 교체
        Collections.replaceAll(list, "A", "X");
        System.out.println("교체 후: " + list);
        // [X, B, X, C, X, B]
    }
}
```

---

## 4. 변환

### 4.1 섞기와 회전

```java
public class ShuffleAndRotate {
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        System.out.println("원본: " + list);
        
        
        // shuffle (무작위)
        Collections.shuffle(list);
        System.out.println("shuffle: " + list);
        
        
        // rotate (회전)
        list = new ArrayList<>(Arrays.asList(1, 2, 3, 4, 5));
        Collections.rotate(list, 2);  // 오른쪽으로 2칸
        System.out.println("rotate(2): " + list);
        // [4, 5, 1, 2, 3]
        
        
        Collections.rotate(list, -2);  // 왼쪽으로 2칸
        System.out.println("rotate(-2): " + list);
        // [1, 2, 3, 4, 5]
    }
}
```

### 4.2 교환과 채우기

```java
public class SwapAndFill {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        System.out.println("원본: " + list);
        
        
        // swap (인덱스 교환)
        Collections.swap(list, 0, 4);
        System.out.println("swap(0,4): " + list);
        // [E, B, C, D, A]
        
        
        // fill (모두 채우기)
        Collections.fill(list, "X");
        System.out.println("fill(X): " + list);
        // [X, X, X, X, X]
    }
}
```

### 4.3 복사와 추가

```java
public class CopyAndAdd {
    public static void main(String[] args) {
        List<String> src = Arrays.asList("A", "B", "C");
        List<String> dest = new ArrayList<>(
            Arrays.asList("1", "2", "3", "4", "5")
        );
        
        System.out.println("src: " + src);
        System.out.println("dest: " + dest);
        
        
        // copy (덮어쓰기)
        Collections.copy(dest, src);
        System.out.println("copy 후: " + dest);
        // [A, B, C, 4, 5]
        
        
        // addAll (여러 개 추가)
        List<Integer> list = new ArrayList<>();
        Collections.addAll(list, 1, 2, 3, 4, 5);
        System.out.println("\naddAll: " + list);
        // [1, 2, 3, 4, 5]
    }
}
```

---

## 5. 동기화

### 5.1 동기화 컬렉션

```java
public class SynchronizedCollections {
    public static void main(String[] args) {
        // 동기화 List
        List<String> list = new ArrayList<>();
        List<String> syncList = Collections.synchronizedList(list);
        
        
        // 동기화 Set
        Set<String> set = new HashSet<>();
        Set<String> syncSet = Collections.synchronizedSet(set);
        
        
        // 동기화 Map
        Map<String, Integer> map = new HashMap<>();
        Map<String, Integer> syncMap = Collections.synchronizedMap(map);
        
        
        System.out.println("=== 동기화 컬렉션 ===");
        System.out.println("Thread-safe 보장");
        System.out.println("순회 시 수동 동기화 필요\n");
        
        
        // 순회 시 동기화
        synchronized (syncList) {
            for (String s : syncList) {
                System.out.println(s);
            }
        }
    }
}
```

---

## 6. 불변 컬렉션

### 6.1 unmodifiable 컬렉션

```java
public class UnmodifiableCollections {
    public static void main(String[] args) {
        // 불변 List
        List<String> list = new ArrayList<>(
            Arrays.asList("A", "B", "C")
        );
        List<String> unmodList = Collections.unmodifiableList(list);
        
        System.out.println("unmodList: " + unmodList);
        
        try {
            unmodList.add("D");  // 예외!
        } catch (UnsupportedOperationException e) {
            System.out.println("수정 불가!");
        }
        
        
        // 원본 수정 시 영향
        list.add("D");
        System.out.println("원본 수정 후: " + unmodList);
        // [A, B, C, D] (원본에 의존)
        
        
        // 완전 불변 (Java 9+)
        List<String> immutable = List.of("X", "Y", "Z");
        System.out.println("immutable: " + immutable);
    }
}
```

### 6.2 빈 컬렉션

```java
public class EmptyCollections {
    public static void main(String[] args) {
        // 빈 List (불변)
        List<String> emptyList = Collections.emptyList();
        System.out.println("emptyList: " + emptyList);
        
        
        // 빈 Set (불변)
        Set<String> emptySet = Collections.emptySet();
        System.out.println("emptySet: " + emptySet);
        
        
        // 빈 Map (불변)
        Map<String, Integer> emptyMap = Collections.emptyMap();
        System.out.println("emptyMap: " + emptyMap);
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("1. 불변");
        System.out.println("2. 싱글톤");
        System.out.println("3. null 안전");
    }
}
```

### 6.3 단일 요소 컬렉션

```java
public class SingletonCollections {
    public static void main(String[] args) {
        // 단일 List
        List<String> singleList = Collections.singletonList("A");
        System.out.println("singletonList: " + singleList);
        
        
        // 단일 Set
        Set<String> singleSet = Collections.singleton("B");
        System.out.println("singletonSet: " + singleSet);
        
        
        // 단일 Map
        Map<String, Integer> singleMap = Collections.singletonMap("C", 1);
        System.out.println("singletonMap: " + singleMap);
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("1. 불변");
        System.out.println("2. 메모리 효율적");
        System.out.println("3. 읽기 전용");
    }
}
```

---

## 7. 기타 유틸

### 7.1 체크 컬렉션

```java
public class CheckedCollections {
    public static void main(String[] args) {
        // 타입 체크 List
        List<String> list = new ArrayList<>();
        List<String> checkedList = Collections.checkedList(
            list, String.class
        );
        
        checkedList.add("A");
        checkedList.add("B");
        
        System.out.println("checkedList: " + checkedList);
        
        
        // 런타임 타입 체크
        List rawList = checkedList;
        try {
            rawList.add(123);  // ClassCastException!
        } catch (ClassCastException e) {
            System.out.println("타입 오류!");
        }
    }
}
```

### 7.2 disjoint

```java
public class DisjointCheck {
    public static void main(String[] args) {
        Collection<Integer> c1 = Arrays.asList(1, 2, 3);
        Collection<Integer> c2 = Arrays.asList(4, 5, 6);
        Collection<Integer> c3 = Arrays.asList(3, 4, 5);
        
        // 교집합 없는지 확인
        boolean disjoint1 = Collections.disjoint(c1, c2);
        System.out.println("c1, c2 교집합 없음? " + disjoint1);  // true
        
        boolean disjoint2 = Collections.disjoint(c1, c3);
        System.out.println("c1, c3 교집합 없음? " + disjoint2);  // false
    }
}
```

### 7.3 nCopies

```java
public class NCopies {
    public static void main(String[] args) {
        // n개 복사본 (불변)
        List<String> copies = Collections.nCopies(5, "A");
        System.out.println("nCopies: " + copies);
        // [A, A, A, A, A]
        
        
        // 리스트 초기화에 활용
        List<Integer> list = new ArrayList<>(
            Collections.nCopies(10, 0)
        );
        System.out.println("초기화: " + list);
        // [0, 0, 0, 0, 0, 0, 0, 0, 0, 0]
    }
}
```

---

## 8. 실전 연습 문제

### 문제 1: 리스트 회전

```java
// 리스트를 k만큼 회전
public class Problem1 {
    public static void rotateList(List<Integer> list, int k) {
        // Collections.rotate 사용
    }
    
    public static void main(String[] args) {
        List<Integer> list = new ArrayList<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        rotateList(list, 2);
        System.out.println(list);
        // [4, 5, 1, 2, 3]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void rotateList(List<Integer> list, int k) {
        Collections.rotate(list, k);
    }
    
    // 수동 구현
    public static void rotateManual(List<Integer> list, int k) {
        int n = list.size();
        k = k % n;
        if (k < 0) k += n;
        
        Collections.reverse(list);
        Collections.reverse(list.subList(0, k));
        Collections.reverse(list.subList(k, n));
    }
}
```
</details>

---

### 문제 2: Top K 빈도 단어

```java
// 가장 빈도 높은 K개 단어
public class Problem2 {
    public static List<String> topKFrequent(String[] words, int k) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        String[] words = {"a", "b", "a", "c", "a", "b"};
        int k = 2;
        
        List<String> top = topKFrequent(words, k);
        System.out.println("Top " + k + ": " + top);
        // [a, b]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static List<String> topKFrequent(String[] words, int k) {
        // 빈도 계산
        Map<String, Integer> freq = new HashMap<>();
        for (String word : words) {
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        
        // 정렬 (빈도 높은 순, 같으면 사전순)
        List<String> result = new ArrayList<>(freq.keySet());
        Collections.sort(result, (w1, w2) -> {
            int f1 = freq.get(w1);
            int f2 = freq.get(w2);
            
            if (f1 != f2) {
                return f2 - f1;  // 빈도 높은 순
            } else {
                return w1.compareTo(w2);  // 사전순
            }
        });
        
        return result.subList(0, Math.min(k, result.size()));
    }
}
```
</details>

---

### 문제 3: 중복 제거 및 정렬

```java
// 중복 제거하고 정렬된 리스트 반환
public class Problem3 {
    public static List<Integer> removeDuplicatesAndSort(List<Integer> list) {
        // Collections 메서드 활용
        return null;
    }
    
    public static void main(String[] args) {
        List<Integer> list = Arrays.asList(5, 2, 8, 2, 1, 9, 5, 3);
        List<Integer> result = removeDuplicatesAndSort(list);
        
        System.out.println("원본: " + list);
        System.out.println("결과: " + result);
        // [1, 2, 3, 5, 8, 9]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static List<Integer> removeDuplicatesAndSort(List<Integer> list) {
        // Set으로 중복 제거
        Set<Integer> set = new HashSet<>(list);
        
        // List로 변환
        List<Integer> result = new ArrayList<>(set);
        
        // 정렬
        Collections.sort(result);
        
        return result;
    }
    
    // 불변 버전
    public static List<Integer> removeDuplicatesAndSortImmutable(List<Integer> list) {
        List<Integer> result = removeDuplicatesAndSort(list);
        return Collections.unmodifiableList(result);
    }
}
```
</details>

---

## 📌 핵심 정리

### 정렬
```java
Collections.sort(list);
Collections.sort(list, comparator);
Collections.reverse(list);
```

### 검색
```java
Collections.binarySearch(list, key);
Collections.min(list);
Collections.max(list);
Collections.frequency(list, element);
```

### 변환
```java
Collections.shuffle(list);
Collections.rotate(list, distance);
Collections.swap(list, i, j);
Collections.fill(list, value);
```

### 동기화
```java
Collections.synchronizedList(list);
Collections.synchronizedSet(set);
Collections.synchronizedMap(map);
```

### 불변
```java
Collections.unmodifiableList(list);
Collections.emptyList();
Collections.singletonList(element);
```

### 주요 메서드
```
정렬:    sort, reverse
검색:    binarySearch, min, max, frequency
변환:    shuffle, rotate, swap, fill
동기화:  synchronized*
불변:    unmodifiable*, empty*, singleton*
```

---

<div align="center">

### 📚 Queue & Util 시리즈

| Chapter | 주제 |
|:-------:|------|
| [13. Queue & Deque](./Collections-13-QueueDeque.md) | 큐와 덱 |
| [14. PriorityQueue](./Collections-14-PriorityQueue.md) | 우선순위 큐 |
| [15. Stack](./Collections-15-Stack.md) | 스택 구현 |
| [16. Collections 유틸](./Collections-16-CollectionsUtil.md) | 유틸리티 |

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 15. Stack](./Collections-15-Stack.md)**

</div>
