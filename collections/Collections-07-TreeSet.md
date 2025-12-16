# Collections 07. TreeSet 완전 정복

> 정렬된 집합 - TreeSet 마스터하기  
> Java API Reference

---

## 📑 목차

1. [TreeSet 기본](#1-treeset-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [정렬과 탐색](#4-정렬과-탐색)
5. [범위 연산](#5-범위-연산)
6. [순회 방법](#6-순회-방법)
7. [내부 구조와 성능](#7-내부-구조와-성능)
8. [실전 활용](#8-실전-활용)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. TreeSet 기본

### 1.1 특징

```java
import java.util.*;

public class TreeSetBasic {
    public static void main(String[] args) {
        System.out.println("=== TreeSet 특징 ===\n");
        
        System.out.println("1. 중복 불가");
        System.out.println("   - Set 인터페이스\n");
        
        System.out.println("2. 자동 정렬");
        System.out.println("   - 오름차순 정렬");
        System.out.println("   - Comparable 또는 Comparator\n");
        
        System.out.println("3. 성능");
        System.out.println("   - add: O(log n)");
        System.out.println("   - contains: O(log n)");
        System.out.println("   - remove: O(log n)\n");
        
        System.out.println("4. null 불가");
        System.out.println("   - NullPointerException");
    }
}
```

### 1.2 자동 정렬 예제

```java
public class AutoSort {
    public static void main(String[] args) {
        // TreeSet (자동 정렬)
        Set<Integer> treeSet = new TreeSet<>();
        treeSet.add(5);
        treeSet.add(2);
        treeSet.add(8);
        treeSet.add(1);
        treeSet.add(9);
        
        System.out.println("TreeSet: " + treeSet);
        // [1, 2, 5, 8, 9] (정렬됨)
        
        
        // HashSet (순서 없음)
        Set<Integer> hashSet = new HashSet<>();
        hashSet.add(5);
        hashSet.add(2);
        hashSet.add(8);
        hashSet.add(1);
        hashSet.add(9);
        
        System.out.println("HashSet: " + hashSet);
        // 무작위 순서
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("TreeSet: 자동 정렬");
        System.out.println("HashSet: 순서 없음");
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class TreeSetCreation {
    public static void main(String[] args) {
        // 기본 생성 (자연 순서)
        TreeSet<Integer> set1 = new TreeSet<>();
        System.out.println("기본: " + set1);
        
        
        // 다른 컬렉션으로
        TreeSet<Integer> set2 = new TreeSet<>(
            Arrays.asList(5, 2, 8, 1, 9)
        );
        System.out.println("복사: " + set2);
        // [1, 2, 5, 8, 9]
        
        
        // Comparator로 (역순)
        TreeSet<Integer> set3 = new TreeSet<>(
            Collections.reverseOrder()
        );
        set3.addAll(Arrays.asList(1, 2, 3, 4, 5));
        System.out.println("역순: " + set3);
        // [5, 4, 3, 2, 1]
        
        
        // SortedSet 타입 (권장)
        SortedSet<Integer> set4 = new TreeSet<>();
        System.out.println("SortedSet 타입: " + set4);
    }
}
```

### 2.2 커스텀 정렬

```java
public class CustomSort {
    static class Person {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        // 나이순 정렬
        TreeSet<Person> set1 = new TreeSet<>(
            (p1, p2) -> Integer.compare(p1.age, p2.age)
        );
        
        set1.add(new Person("Alice", 25));
        set1.add(new Person("Bob", 20));
        set1.add(new Person("Charlie", 30));
        
        System.out.println("나이순: " + set1);
        // [Bob(20), Alice(25), Charlie(30)]
        
        
        // 이름순 정렬
        TreeSet<Person> set2 = new TreeSet<>(
            Comparator.comparing(p -> p.name)
        );
        
        set2.add(new Person("Charlie", 30));
        set2.add(new Person("Alice", 25));
        set2.add(new Person("Bob", 20));
        
        System.out.println("이름순: " + set2);
        // [Alice(25), Bob(20), Charlie(30)]
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (add)

```java
public class TreeSetAdd {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>();
        
        // 추가 (자동 정렬)
        set.add(5);
        set.add(2);
        set.add(8);
        
        System.out.println("추가 후: " + set);
        // [2, 5, 8]
        
        
        // 중복 추가 (무시)
        set.add(5);
        System.out.println("중복 후: " + set);
        // [2, 5, 8]
        
        
        // 여러 개 추가
        set.addAll(Arrays.asList(1, 9, 3));
        System.out.println("여러 개: " + set);
        // [1, 2, 3, 5, 8, 9]
        
        
        // null 추가 (에러!)
        try {
            set.add(null);
        } catch (NullPointerException e) {
            System.out.println("\nnull 불가!");
        }
    }
}
```

### 3.2 삭제 (remove)

```java
public class TreeSetRemove {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(
            Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9)
        );
        
        System.out.println("원본: " + set);
        
        
        // 요소 삭제
        set.remove(5);
        System.out.println("5 삭제: " + set);
        
        
        // 첫 요소 삭제
        int first = set.pollFirst();
        System.out.println("pollFirst: " + first);
        System.out.println("After: " + set);
        
        
        // 마지막 요소 삭제
        int last = set.pollLast();
        System.out.println("pollLast: " + last);
        System.out.println("After: " + set);
    }
}
```

---

## 4. 정렬과 탐색

### 4.1 첫/마지막 요소

```java
public class TreeSetFirstLast {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(
            Arrays.asList(5, 2, 8, 1, 9, 3)
        );
        
        System.out.println("Set: " + set);
        // [1, 2, 3, 5, 8, 9]
        
        
        // 첫 요소
        int first = set.first();
        System.out.println("First: " + first);  // 1
        
        
        // 마지막 요소
        int last = set.last();
        System.out.println("Last: " + last);  // 9
        
        
        // 빈 Set
        TreeSet<Integer> empty = new TreeSet<>();
        try {
            empty.first();
        } catch (NoSuchElementException e) {
            System.out.println("\n빈 Set에서 first() 에러!");
        }
    }
}
```

### 4.2 근접 요소 찾기

```java
public class TreeSetNearest {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(
            Arrays.asList(1, 3, 5, 7, 9)
        );
        
        System.out.println("Set: " + set);
        
        
        // lower: 작은 값 중 가장 큰 것
        System.out.println("\nlower(5): " + set.lower(5));  // 3
        System.out.println("lower(6): " + set.lower(6));    // 5
        
        
        // floor: 작거나 같은 값 중 가장 큰 것
        System.out.println("\nfloor(5): " + set.floor(5));  // 5
        System.out.println("floor(6): " + set.floor(6));    // 5
        
        
        // ceiling: 크거나 같은 값 중 가장 작은 것
        System.out.println("\nceiling(5): " + set.ceiling(5));  // 5
        System.out.println("ceiling(6): " + set.ceiling(6));    // 7
        
        
        // higher: 큰 값 중 가장 작은 것
        System.out.println("\nhigher(5): " + set.higher(5));  // 7
        System.out.println("higher(6): " + set.higher(6));    // 7
    }
}
```

---

## 5. 범위 연산

### 5.1 부분 집합 (subSet)

```java
public class TreeSetSubSet {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(
            Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
        );
        
        System.out.println("전체: " + set);
        
        
        // subSet (from 포함, to 미포함)
        SortedSet<Integer> sub1 = set.subSet(3, 8);
        System.out.println("subSet(3, 8): " + sub1);
        // [3, 4, 5, 6, 7]
        
        
        // subSet (포함 여부 지정)
        SortedSet<Integer> sub2 = set.subSet(3, true, 8, true);
        System.out.println("subSet(3, true, 8, true): " + sub2);
        // [3, 4, 5, 6, 7, 8]
        
        
        // headSet (처음부터 to 미포함)
        SortedSet<Integer> head = set.headSet(5);
        System.out.println("headSet(5): " + head);
        // [1, 2, 3, 4]
        
        
        // tailSet (from 포함부터 끝까지)
        SortedSet<Integer> tail = set.tailSet(7);
        System.out.println("tailSet(7): " + tail);
        // [7, 8, 9, 10]
    }
}
```

### 5.2 범위 삭제

```java
public class TreeSetRangeRemove {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(
            Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
        );
        
        System.out.println("원본: " + set);
        
        
        // 3~7 삭제
        set.subSet(3, true, 7, true).clear();
        
        System.out.println("3~7 삭제: " + set);
        // [1, 2, 8, 9, 10]
    }
}
```

---

## 6. 순회 방법

### 6.1 오름차순 순회

```java
public class TreeSetAscending {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(
            Arrays.asList(5, 2, 8, 1, 9, 3)
        );
        
        // 향상된 for (오름차순)
        System.out.println("=== 오름차순 ===");
        for (int num : set) {
            System.out.print(num + " ");
        }
        System.out.println();
        // 1 2 3 5 8 9
        
        
        // Iterator (오름차순)
        System.out.println("\n=== Iterator ===");
        Iterator<Integer> it = set.iterator();
        while (it.hasNext()) {
            System.out.print(it.next() + " ");
        }
        System.out.println();
    }
}
```

### 6.2 내림차순 순회

```java
public class TreeSetDescending {
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(
            Arrays.asList(5, 2, 8, 1, 9, 3)
        );
        
        // descendingSet (내림차순 뷰)
        System.out.println("=== 내림차순 Set ===");
        NavigableSet<Integer> desc = set.descendingSet();
        System.out.println(desc);
        // [9, 8, 5, 3, 2, 1]
        
        
        // descendingIterator
        System.out.println("\n=== descendingIterator ===");
        Iterator<Integer> it = set.descendingIterator();
        while (it.hasNext()) {
            System.out.print(it.next() + " ");
        }
        System.out.println();
        // 9 8 5 3 2 1
    }
}
```

---

## 7. 내부 구조와 성능

### 7.1 레드-블랙 트리

```java
public class TreeSetStructure {
    public static void main(String[] args) {
        System.out.println("=== TreeSet 구조 ===\n");
        
        System.out.println("내부: TreeMap 사용");
        System.out.println("구조: 레드-블랙 트리");
        System.out.println("특징: 자가 균형 이진 탐색 트리\n");
        
        System.out.println("=== 균형 유지 ===");
        System.out.println("삽입/삭제 시 자동 균형");
        System.out.println("높이: O(log n) 유지\n");
        
        System.out.println("=== 정렬 보장 ===");
        System.out.println("중위 순회 = 정렬 순서");
        System.out.println("Comparable 또는 Comparator");
    }
}
```

### 7.2 시간 복잡도

```java
public class TreeSetComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("add:       O(log n)");
        System.out.println("  - 트리 탐색 + 삽입\n");
        
        System.out.println("contains:  O(log n)");
        System.out.println("  - 이진 탐색\n");
        
        System.out.println("remove:    O(log n)");
        System.out.println("  - 탐색 + 삭제 + 균형\n");
        
        System.out.println("first/last: O(log n)");
        System.out.println("  - 트리 순회\n");
        
        System.out.println("HashSet보다 느림");
        System.out.println("하지만 정렬 보장!");
    }
}
```

### 7.3 성능 비교

```java
public class PerformanceComparison {
    public static void main(String[] args) {
        int n = 100000;
        
        // HashSet
        Set<Integer> hashSet = new HashSet<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            hashSet.add(i);
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // TreeSet
        Set<Integer> treeSet = new TreeSet<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            treeSet.add(i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        System.out.println("HashSet: " + time1 + "ms");
        System.out.println("TreeSet: " + time2 + "ms");
        System.out.println("\nTreeSet이 느림");
        System.out.println("하지만 정렬됨!");
    }
}
```

---

## 8. 실전 활용

### 8.1 상위 N개 요소

```java
public class TopN {
    public static void main(String[] args) {
        int[] scores = {85, 92, 78, 95, 88, 76, 90, 82};
        
        // 상위 3개
        TreeSet<Integer> topScores = new TreeSet<>(
            Collections.reverseOrder()
        );
        
        for (int score : scores) {
            topScores.add(score);
            if (topScores.size() > 3) {
                topScores.pollLast();  // 가장 작은 것 제거
            }
        }
        
        System.out.println("상위 3개: " + topScores);
        // [95, 92, 90]
    }
}
```

### 8.2 범위 내 데이터

```java
public class RangeQuery {
    public static void main(String[] args) {
        TreeSet<Integer> ages = new TreeSet<>(
            Arrays.asList(15, 23, 31, 19, 45, 28, 37, 52)
        );
        
        System.out.println("전체: " + ages);
        
        
        // 20대 (20~29)
        SortedSet<Integer> twenties = ages.subSet(20, 30);
        System.out.println("20대: " + twenties);
        // [23, 28]
        
        
        // 30세 이상
        SortedSet<Integer> over30 = ages.tailSet(30);
        System.out.println("30세 이상: " + over30);
        // [31, 37, 45, 52]
    }
}
```

### 8.3 순위 시스템

```java
public class RankingSystem {
    private TreeSet<Integer> scores;
    
    public RankingSystem() {
        this.scores = new TreeSet<>(Collections.reverseOrder());
    }
    
    public void addScore(int score) {
        scores.add(score);
    }
    
    public int getRank(int score) {
        int rank = 1;
        for (int s : scores) {
            if (s == score) break;
            rank++;
        }
        return rank;
    }
    
    public void printTop5() {
        System.out.println("=== Top 5 ===");
        int count = 0;
        for (int score : scores) {
            System.out.println((++count) + ". " + score);
            if (count >= 5) break;
        }
    }
    
    public static void main(String[] args) {
        RankingSystem ranking = new RankingSystem();
        
        ranking.addScore(95);
        ranking.addScore(87);
        ranking.addScore(92);
        ranking.addScore(78);
        ranking.addScore(88);
        ranking.addScore(91);
        
        ranking.printTop5();
        
        System.out.println("\n92점의 순위: " + ranking.getRank(92));
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: K번째 큰 수

```java
// 배열에서 K번째로 큰 수 찾기
public class Problem1 {
    public static int findKthLargest(int[] nums, int k) {
        // 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        int[] nums = {3, 2, 1, 5, 6, 4};
        int k = 2;
        
        System.out.println(k + "번째 큰 수: " + findKthLargest(nums, k));
        // 5
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static int findKthLargest(int[] nums, int k) {
        TreeSet<Integer> set = new TreeSet<>(
            Collections.reverseOrder()
        );
        
        for (int num : nums) {
            set.add(num);
        }
        
        int count = 0;
        for (int num : set) {
            count++;
            if (count == k) {
                return num;
            }
        }
        
        return -1;
    }
    
    // 간단 버전
    public static int findKthLargest2(int[] nums, int k) {
        TreeSet<Integer> set = new TreeSet<>(
            Collections.reverseOrder()
        );
        
        for (int num : nums) {
            set.add(num);
        }
        
        Iterator<Integer> it = set.iterator();
        for (int i = 1; i < k; i++) {
            it.next();
        }
        
        return it.next();
    }
}
```
</details>

---

### 문제 2: 범위 합

```java
// 범위 내 숫자의 합
public class Problem2 {
    public static int rangeSum(TreeSet<Integer> set, int from, int to) {
        // 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        TreeSet<Integer> set = new TreeSet<>(
            Arrays.asList(1, 3, 5, 7, 9, 11, 13, 15)
        );
        
        int sum = rangeSum(set, 5, 12);
        System.out.println("5~12 합: " + sum);
        // 5 + 7 + 9 + 11 = 32
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int rangeSum(TreeSet<Integer> set, int from, int to) {
        SortedSet<Integer> range = set.subSet(from, true, to, true);
        
        int sum = 0;
        for (int num : range) {
            sum += num;
        }
        
        return sum;
    }
    
    // Stream 버전
    public static int rangeSum2(TreeSet<Integer> set, int from, int to) {
        return set.subSet(from, true, to, true)
                 .stream()
                 .mapToInt(Integer::intValue)
                 .sum();
    }
}
```
</details>

---

### 문제 3: 중복 없는 최소 K개

```java
// 스트림에서 최소 K개 유지 (중복 제거)
public class Problem3 {
    static class MinKTracker {
        private TreeSet<Integer> set;
        private int k;
        
        public MinKTracker(int k) {
            // 코드 작성
        }
        
        public void add(int num) {
            // 코드 작성
        }
        
        public List<Integer> getMinK() {
            // 코드 작성
            return null;
        }
    }
    
    public static void main(String[] args) {
        MinKTracker tracker = new MinKTracker(3);
        
        int[] stream = {5, 2, 8, 2, 1, 9, 3, 1, 4};
        for (int num : stream) {
            tracker.add(num);
        }
        
        System.out.println("최소 3개: " + tracker.getMinK());
        // [1, 2, 3]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    static class MinKTracker {
        private TreeSet<Integer> set;
        private int k;
        
        public MinKTracker(int k) {
            this.set = new TreeSet<>();
            this.k = k;
        }
        
        public void add(int num) {
            set.add(num);
            
            // K개 초과 시 가장 큰 것 제거
            if (set.size() > k) {
                set.pollLast();
            }
        }
        
        public List<Integer> getMinK() {
            return new ArrayList<>(set);
        }
        
        public void printCurrent() {
            System.out.println("Current: " + set);
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// 중복 불가 + 자동 정렬
TreeSet<Integer> set = new TreeSet<>();
set.add(5);
set.add(2);
set.add(8);
System.out.println(set);  // [2, 5, 8]
```

### 주요 메서드
```java
// 추가/삭제
set.add(5);         // O(log n)
set.remove(5);      // O(log n)

// 첫/마지막
set.first();        // 최소
set.last();         // 최대
set.pollFirst();    // 최소 + 제거
set.pollLast();     // 최대 + 제거

// 근접 값
set.lower(5);       // < 5
set.floor(5);       // <= 5
set.ceiling(5);     // >= 5
set.higher(5);      // > 5

// 범위
set.subSet(3, 8);   // [3, 8)
set.headSet(5);     // [처음, 5)
set.tailSet(7);     // [7, 끝]
```

### 시간 복잡도
```
add:      O(log n)
contains: O(log n)
remove:   O(log n)
first/last: O(log n)
```

### 사용 시기
```
✅ 자동 정렬 필요
✅ 범위 연산
✅ 최소/최대 빠른 접근
✅ 순위 시스템
❌ 빠른 검색만 (HashSet)
❌ 순서 유지만 (LinkedHashSet)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 06. LinkedHashSet](./Collections-06-LinkedHashSet.md) | [다음: 08. Set 비교 →](./Collections-08-SetComparison.md)**

</div>
