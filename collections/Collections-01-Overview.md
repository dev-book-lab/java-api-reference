# Collections 01. Collections 개요

> Java Collections Framework - 인터페이스 계층구조와 전체 구조  
> Java API Reference

---

## 📑 목차

1. [Collections Framework란?](#1-collections-framework란)
2. [핵심 인터페이스](#2-핵심-인터페이스)
3. [계층 구조](#3-계층-구조)
4. [주요 구현 클래스](#4-주요-구현-클래스)
5. [선택 가이드](#5-선택-가이드)
6. [실전 연습 문제](#6-실전-연습-문제)

---

## 1. Collections Framework란?

### 1.1 정의

```java
/*
Collections Framework:
- 데이터를 저장하고 관리하는 표준화된 방법
- 인터페이스와 구현 클래스로 구성
- java.util 패키지에 포함
*/

public class CollectionsIntro {
    public static void main(String[] args) {
        System.out.println("=== Collections Framework ===");
        System.out.println("목적: 데이터 그룹을 효율적으로 관리");
        System.out.println("장점:");
        System.out.println("  1. 표준화된 API");
        System.out.println("  2. 재사용성");
        System.out.println("  3. 성능 최적화");
        System.out.println("  4. 타입 안정성 (제네릭)");
    }
}
```

### 1.2 배열 vs Collections

```java
import java.util.*;

public class ArrayVsCollections {
    public static void main(String[] args) {
        // 배열의 한계
        System.out.println("=== 배열 ===");
        int[] arr = new int[5];  // 크기 고정!
        arr[0] = 10;
        arr[1] = 20;
        // arr[5] = 30;  // ArrayIndexOutOfBoundsException!
        
        System.out.println("크기: " + arr.length);  // 5 (고정)
        System.out.println("문제점:");
        System.out.println("  1. 크기 고정");
        System.out.println("  2. 삭제 어려움");
        System.out.println("  3. 타입 하나만");
        
        
        // Collections의 장점
        System.out.println("\n=== ArrayList ===");
        List<Integer> list = new ArrayList<>();
        list.add(10);
        list.add(20);
        list.add(30);  // 자동 확장!
        
        System.out.println("크기: " + list.size());  // 3 (동적)
        list.remove(1);  // 쉬운 삭제
        System.out.println("삭제 후: " + list);
        
        System.out.println("장점:");
        System.out.println("  1. 크기 동적 조절");
        System.out.println("  2. 편리한 메서드");
        System.out.println("  3. 제네릭 타입 안정성");
        System.out.println("  4. 다양한 구현체");
    }
}
```

---

## 2. 핵심 인터페이스

### 2.1 Collection 인터페이스

```java
import java.util.*;

public class CollectionInterface {
    public static void main(String[] args) {
        Collection<String> col = new ArrayList<>();
        
        // 공통 메서드
        col.add("Apple");           // 추가
        col.remove("Apple");        // 삭제
        col.contains("Apple");      // 포함 확인
        col.size();                 // 크기
        col.isEmpty();              // 비어있는지
        col.clear();                // 전체 삭제
        
        // 변환
        Object[] arr = col.toArray();
        String[] strArr = col.toArray(new String[0]);
        
        
        System.out.println("=== Collection 인터페이스 ===");
        System.out.println("하위: List, Set, Queue");
        System.out.println("제외: Map (별도 계층)");
    }
}
```

### 2.2 List 인터페이스

```java
import java.util.*;

public class ListInterface {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        
        // List 특화 메서드
        list.add(0, "First");       // 인덱스 지정 추가
        String item = list.get(0);  // 인덱스로 조회
        list.set(0, "New");         // 인덱스로 수정
        list.remove(0);             // 인덱스로 삭제
        
        int index = list.indexOf("Apple");
        int lastIndex = list.lastIndexOf("Apple");
        
        List<String> subList = list.subList(0, 2);
        
        
        System.out.println("=== List 특징 ===");
        System.out.println("1. 순서 유지 (인덱스)");
        System.out.println("2. 중복 허용");
        System.out.println("3. null 허용");
        System.out.println("\n구현체: ArrayList, LinkedList");
    }
}
```

### 2.3 Set 인터페이스

```java
import java.util.*;

public class SetInterface {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();
        
        // Set은 Collection 메서드만 사용
        set.add("Apple");
        set.add("Apple");  // 중복 무시!
        
        System.out.println(set);  // [Apple] (한 번만)
        System.out.println("Size: " + set.size());  // 1
        
        
        System.out.println("=== Set 특징 ===");
        System.out.println("1. 중복 불가");
        System.out.println("2. 순서 없음 (HashSet)");
        System.out.println("3. null 허용 (1개만)");
        System.out.println("\n구현체: HashSet, LinkedHashSet, TreeSet");
    }
}
```

### 2.4 Queue 인터페이스

```java
import java.util.*;

public class QueueInterface {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        
        // Queue 특화 메서드
        queue.offer("First");   // 추가
        queue.offer("Second");
        
        String head = queue.peek();  // 조회
        String removed = queue.poll();  // 조회 + 제거
        
        System.out.println("Head: " + head);
        System.out.println("Removed: " + removed);
        System.out.println("Queue: " + queue);
        
        
        System.out.println("\n=== Queue 특징 ===");
        System.out.println("1. FIFO 순서");
        System.out.println("2. 앞에서 제거, 뒤에서 추가");
        System.out.println("\n구현체: LinkedList, PriorityQueue");
    }
}
```

### 2.5 Map 인터페이스

```java
import java.util.*;

public class MapInterface {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        
        // Map 메서드
        map.put("Apple", 1000);
        int price = map.get("Apple");
        map.remove("Apple");
        
        boolean hasKey = map.containsKey("Apple");
        boolean hasValue = map.containsValue(1000);
        
        // 순회
        for (String key : map.keySet()) {
            System.out.println(key + ": " + map.get(key));
        }
        
        
        System.out.println("\n=== Map 특징 ===");
        System.out.println("1. Key-Value 쌍");
        System.out.println("2. Key는 중복 불가");
        System.out.println("3. Value는 중복 가능");
        System.out.println("\n구현체: HashMap, LinkedHashMap, TreeMap");
    }
}
```

---

## 3. 계층 구조

### 3.1 전체 계층도

```java
public class Hierarchy {
    public static void main(String[] args) {
        System.out.println("=== Collection 계층 ===");
        System.out.println("Iterable<E>");
        System.out.println("  └─ Collection<E>");
        System.out.println("      ├─ List<E>");
        System.out.println("      │   ├─ ArrayList<E>");
        System.out.println("      │   └─ LinkedList<E>");
        System.out.println("      ├─ Set<E>");
        System.out.println("      │   ├─ HashSet<E>");
        System.out.println("      │   ├─ LinkedHashSet<E>");
        System.out.println("      │   └─ TreeSet<E>");
        System.out.println("      └─ Queue<E>");
        System.out.println("          ├─ LinkedList<E>");
        System.out.println("          ├─ PriorityQueue<E>");
        System.out.println("          └─ ArrayDeque<E>");
        
        System.out.println("\n=== Map 계층 (별도) ===");
        System.out.println("Map<K,V>");
        System.out.println("  ├─ HashMap<K,V>");
        System.out.println("  ├─ LinkedHashMap<K,V>");
        System.out.println("  └─ TreeMap<K,V>");
    }
}
```

### 3.2 인터페이스 상속 관계

```java
import java.util.*;

public class InterfaceRelation {
    public static void main(String[] args) {
        // List는 Collection
        List<String> list = new ArrayList<>();
        Collection<String> col1 = list;  // OK
        
        // Set도 Collection
        Set<String> set = new HashSet<>();
        Collection<String> col2 = set;  // OK
        
        // Queue도 Collection
        Queue<String> queue = new LinkedList<>();
        Collection<String> col3 = queue;  // OK
        
        // Map은 Collection 아님!
        Map<String, Integer> map = new HashMap<>();
        // Collection<String> col4 = map;  // 컴파일 에러!
        
        
        System.out.println("=== 공통점 ===");
        System.out.println("List, Set, Queue -> Collection 상속");
        System.out.println("공통 메서드: add, remove, size");
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("Map -> 별도 계층");
    }
}
```

---

## 4. 주요 구현 클래스

### 4.1 List 구현체

```java
import java.util.*;

public class ListImplementations {
    public static void main(String[] args) {
        System.out.println("=== List 구현체 ===\n");
        
        // ArrayList
        System.out.println("1. ArrayList");
        System.out.println("   내부: 동적 배열");
        System.out.println("   조회: O(1) 빠름");
        System.out.println("   추가/삭제: O(n) 느림");
        System.out.println("   사용: 조회 많을 때");
        
        List<String> arrayList = new ArrayList<>();
        arrayList.add("A");
        arrayList.add("B");
        System.out.println("   예시: " + arrayList + "\n");
        
        
        // LinkedList
        System.out.println("2. LinkedList");
        System.out.println("   내부: 이중 연결 리스트");
        System.out.println("   조회: O(n) 느림");
        System.out.println("   추가/삭제: O(1) 빠름");
        System.out.println("   사용: 추가/삭제 많을 때");
        
        List<String> linkedList = new LinkedList<>();
        linkedList.add("A");
        linkedList.add("B");
        System.out.println("   예시: " + linkedList);
    }
}
```

### 4.2 Set 구현체

```java
import java.util.*;

public class SetImplementations {
    public static void main(String[] args) {
        System.out.println("=== Set 구현체 ===\n");
        
        // HashSet
        System.out.println("1. HashSet");
        System.out.println("   내부: 해시 테이블");
        System.out.println("   순서: 무작위");
        System.out.println("   성능: O(1) 가장 빠름");
        
        Set<String> hashSet = new HashSet<>();
        hashSet.add("C");
        hashSet.add("A");
        hashSet.add("B");
        System.out.println("   예시: " + hashSet + "\n");
        
        
        // LinkedHashSet
        System.out.println("2. LinkedHashSet");
        System.out.println("   순서: 삽입 순서 유지");
        System.out.println("   성능: O(1)");
        
        Set<String> linkedHashSet = new LinkedHashSet<>();
        linkedHashSet.add("C");
        linkedHashSet.add("A");
        linkedHashSet.add("B");
        System.out.println("   예시: " + linkedHashSet + "\n");
        
        
        // TreeSet
        System.out.println("3. TreeSet");
        System.out.println("   내부: 레드-블랙 트리");
        System.out.println("   순서: 자동 정렬");
        System.out.println("   성능: O(log n)");
        
        Set<String> treeSet = new TreeSet<>();
        treeSet.add("C");
        treeSet.add("A");
        treeSet.add("B");
        System.out.println("   예시: " + treeSet);
    }
}
```

### 4.3 Map 구현체

```java
import java.util.*;

public class MapImplementations {
    public static void main(String[] args) {
        System.out.println("=== Map 구현체 ===\n");
        
        // HashMap
        System.out.println("1. HashMap");
        System.out.println("   내부: 해시 테이블");
        System.out.println("   순서: 무작위");
        System.out.println("   성능: O(1)");
        
        Map<String, Integer> hashMap = new HashMap<>();
        hashMap.put("C", 3);
        hashMap.put("A", 1);
        hashMap.put("B", 2);
        System.out.println("   예시: " + hashMap + "\n");
        
        
        // LinkedHashMap
        System.out.println("2. LinkedHashMap");
        System.out.println("   순서: 삽입 순서 유지");
        
        Map<String, Integer> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put("C", 3);
        linkedHashMap.put("A", 1);
        linkedHashMap.put("B", 2);
        System.out.println("   예시: " + linkedHashMap + "\n");
        
        
        // TreeMap
        System.out.println("3. TreeMap");
        System.out.println("   순서: Key 기준 정렬");
        System.out.println("   성능: O(log n)");
        
        Map<String, Integer> treeMap = new TreeMap<>();
        treeMap.put("C", 3);
        treeMap.put("A", 1);
        treeMap.put("B", 2);
        System.out.println("   예시: " + treeMap);
    }
}
```

### 4.4 Queue 구현체

```java
import java.util.*;

public class QueueImplementations {
    public static void main(String[] args) {
        System.out.println("=== Queue 구현체 ===\n");
        
        // LinkedList
        System.out.println("1. LinkedList (Queue)");
        System.out.println("   FIFO 큐");
        
        Queue<String> queue = new LinkedList<>();
        queue.offer("A");
        queue.offer("B");
        System.out.println("   예시: " + queue + "\n");
        
        
        // PriorityQueue
        System.out.println("2. PriorityQueue");
        System.out.println("   우선순위 큐");
        System.out.println("   자동 정렬");
        
        Queue<Integer> pq = new PriorityQueue<>();
        pq.offer(3);
        pq.offer(1);
        pq.offer(2);
        System.out.println("   Poll: " + pq.poll() + "\n");
        
        
        // ArrayDeque
        System.out.println("3. ArrayDeque");
        System.out.println("   양방향 큐");
        System.out.println("   Stack보다 빠름");
        
        Deque<String> deque = new ArrayDeque<>();
        deque.offerFirst("A");
        deque.offerLast("B");
        System.out.println("   예시: " + deque);
    }
}
```

---

## 5. 선택 가이드

### 5.1 자료구조 선택 플로우

```java
public class SelectionGuide {
    public static void main(String[] args) {
        System.out.println("=== 자료구조 선택 ===\n");
        
        System.out.println("Q1. Key-Value 쌍?");
        System.out.println("  YES -> Map");
        System.out.println("    순서 불필요: HashMap");
        System.out.println("    삽입 순서: LinkedHashMap");
        System.out.println("    정렬 필요: TreeMap");
        
        System.out.println("\n  NO -> Collection\n");
        
        System.out.println("Q2. 중복 허용?");
        System.out.println("  YES -> List");
        System.out.println("    조회 많음: ArrayList");
        System.out.println("    추가/삭제: LinkedList");
        
        System.out.println("\n  NO -> Set");
        System.out.println("    순서 불필요: HashSet");
        System.out.println("    삽입 순서: LinkedHashSet");
        System.out.println("    정렬 필요: TreeSet");
        
        System.out.println("\nQ3. 대기열?");
        System.out.println("  FIFO: LinkedList");
        System.out.println("  우선순위: PriorityQueue");
        System.out.println("  Stack: ArrayDeque");
    }
}
```

### 5.2 시간 복잡도

```java
public class TimeComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("ArrayList:");
        System.out.println("  get: O(1)");
        System.out.println("  add: O(1) 평균");
        System.out.println("  add(i,e): O(n)");
        System.out.println("  remove: O(n)");
        System.out.println("  contains: O(n)\n");
        
        System.out.println("LinkedList:");
        System.out.println("  get: O(n)");
        System.out.println("  add: O(1)");
        System.out.println("  remove: O(1)");
        System.out.println("  contains: O(n)\n");
        
        System.out.println("HashSet/HashMap:");
        System.out.println("  add/put: O(1)");
        System.out.println("  contains/get: O(1)");
        System.out.println("  remove: O(1)\n");
        
        System.out.println("TreeSet/TreeMap:");
        System.out.println("  add/put: O(log n)");
        System.out.println("  contains/get: O(log n)");
        System.out.println("  remove: O(log n)");
    }
}
```

### 5.3 실전 사용 예시

```java
import java.util.*;

public class RealWorldExamples {
    public static void main(String[] args) {
        System.out.println("=== 실전 사례 ===\n");
        
        // 1. 학생 목록
        List<String> students = new ArrayList<>();
        students.add("Alice");
        students.add("Bob");
        students.add("Alice");
        System.out.println("1. 학생 목록: " + students);
        System.out.println("   -> ArrayList (순서, 중복)\n");
        
        
        // 2. 고유 방문자
        Set<String> visitors = new HashSet<>();
        visitors.add("User1");
        visitors.add("User1");
        visitors.add("User2");
        System.out.println("2. 방문자: " + visitors);
        System.out.println("   -> HashSet (중복 제거)\n");
        
        
        // 3. 사용자 정보
        Map<Integer, String> users = new HashMap<>();
        users.put(1, "Alice");
        users.put(2, "Bob");
        System.out.println("3. 사용자: " + users);
        System.out.println("   -> HashMap (ID->이름)\n");
        
        
        // 4. 대기열
        Queue<String> waitingList = new LinkedList<>();
        waitingList.offer("First");
        waitingList.offer("Second");
        System.out.println("4. 대기열: " + waitingList);
        System.out.println("   -> Queue (FIFO)\n");
        
        
        // 5. 우선순위
        Queue<Integer> tasks = new PriorityQueue<>();
        tasks.offer(3);
        tasks.offer(1);
        tasks.offer(2);
        System.out.println("5. 가장 중요: " + tasks.poll());
        System.out.println("   -> PriorityQueue (우선순위)");
    }
}
```

---

## 6. 실전 연습 문제

### 문제 1: 자료구조 선택

```java
// 각 상황에 맞는 자료구조 선택
public class Problem1 {
    public static void main(String[] args) {
        System.out.println("=== 자료구조 선택 문제 ===\n");
        
        System.out.println("1. 학생 성적 (학번->성적)");
        System.out.println("2. 로또 번호 (중복 없는 6개)");
        System.out.println("3. 브라우저 뒤로가기");
        System.out.println("4. 프린터 대기열");
        System.out.println("5. 영어 단어장 (정렬)");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
import java.util.*;

public class Problem1 {
    public static void main(String[] args) {
        // 1. 학생 성적
        Map<Integer, Integer> scores = new HashMap<>();
        scores.put(20240101, 95);
        scores.put(20240102, 88);
        System.out.println("1. HashMap - Key-Value\n");
        
        
        // 2. 로또 번호
        Set<Integer> lotto = new HashSet<>();
        while (lotto.size() < 6) {
            lotto.add((int)(Math.random() * 45) + 1);
        }
        System.out.println("2. HashSet - 중복 제거");
        System.out.println("   " + lotto + "\n");
        
        
        // 3. 브라우저 뒤로가기
        Deque<String> history = new ArrayDeque<>();
        history.push("google.com");
        history.push("naver.com");
        String back = history.pop();
        System.out.println("3. ArrayDeque - LIFO\n");
        
        
        // 4. 프린터 대기열
        Queue<String> printQueue = new LinkedList<>();
        printQueue.offer("Doc1");
        printQueue.offer("Doc2");
        String next = printQueue.poll();
        System.out.println("4. LinkedList - FIFO\n");
        
        
        // 5. 영어 단어장
        Set<String> dict = new TreeSet<>();
        dict.add("zebra");
        dict.add("apple");
        dict.add("banana");
        System.out.println("5. TreeSet - 정렬");
        System.out.println("   " + dict);
    }
}
```
</details>

---

### 문제 2: 성능 비교

```java
// ArrayList vs LinkedList 성능
import java.util.*;

public class Problem2 {
    public static void main(String[] args) {
        int n = 100000;
        
        // ArrayList
        List<Integer> arrayList = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            arrayList.add(i);
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // LinkedList
        List<Integer> linkedList = new LinkedList<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            linkedList.add(i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        System.out.println("ArrayList: " + time1 + "ms");
        System.out.println("LinkedList: " + time2 + "ms");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
import java.util.*;

public class Problem2 {
    public static void main(String[] args) {
        System.out.println("=== 끝에 추가 ===");
        testAppend(100000);
        
        System.out.println("\n=== 중간 삽입 ===");
        testInsert(1000);
        
        System.out.println("\n=== 인덱스 조회 ===");
        testGet(100000);
    }
    
    static void testAppend(int n) {
        List<Integer> al = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) al.add(i);
        long time1 = System.currentTimeMillis() - start;
        
        List<Integer> ll = new LinkedList<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ll.add(i);
        long time2 = System.currentTimeMillis() - start;
        
        System.out.println("ArrayList: " + time1 + "ms");
        System.out.println("LinkedList: " + time2 + "ms");
        System.out.println("-> ArrayList 빠름");
    }
    
    static void testInsert(int n) {
        List<Integer> al = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) al.add(0, i);
        long time1 = System.currentTimeMillis() - start;
        
        List<Integer> ll = new LinkedList<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ll.add(0, i);
        long time2 = System.currentTimeMillis() - start;
        
        System.out.println("ArrayList: " + time1 + "ms");
        System.out.println("LinkedList: " + time2 + "ms");
        System.out.println("-> LinkedList 빠름");
    }
    
    static void testGet(int n) {
        List<Integer> al = new ArrayList<>();
        for (int i = 0; i < n; i++) al.add(i);
        
        List<Integer> ll = new LinkedList<>();
        for (int i = 0; i < n; i++) ll.add(i);
        
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) al.get(i);
        long time1 = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ll.get(i);
        long time2 = System.currentTimeMillis() - start;
        
        System.out.println("ArrayList: " + time1 + "ms");
        System.out.println("LinkedList: " + time2 + "ms");
        System.out.println("-> ArrayList 압도적");
    }
}
```
</details>

---

### 문제 3: 중복 제거와 정렬

```java
// 리스트 중복 제거 후 정렬
import java.util.*;

public class Problem3 {
    public static List<Integer> solve(List<Integer> list) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        List<Integer> nums = Arrays.asList(5, 2, 8, 2, 9, 1, 5, 8);
        List<Integer> result = solve(nums);
        
        System.out.println("Original: " + nums);
        System.out.println("Result: " + result);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
import java.util.*;
import java.util.stream.*;

public class Problem3 {
    // 방법 1: TreeSet
    public static List<Integer> solve1(List<Integer> list) {
        Set<Integer> set = new TreeSet<>(list);
        return new ArrayList<>(set);
    }
    
    // 방법 2: HashSet + sort
    public static List<Integer> solve2(List<Integer> list) {
        Set<Integer> set = new HashSet<>(list);
        List<Integer> result = new ArrayList<>(set);
        Collections.sort(result);
        return result;
    }
    
    // 방법 3: Stream
    public static List<Integer> solve3(List<Integer> list) {
        return list.stream()
                  .distinct()
                  .sorted()
                  .collect(Collectors.toList());
    }
    
    public static void main(String[] args) {
        List<Integer> nums = Arrays.asList(5, 2, 8, 2, 9, 1, 5, 8);
        
        System.out.println("Original: " + nums);
        System.out.println("Method 1: " + solve1(nums));
        System.out.println("Method 2: " + solve2(nums));
        System.out.println("Method 3: " + solve3(nums));
    }
}
```
</details>

---

## 📌 핵심 정리

### 인터페이스 계층
```
Collection<E>
├─ List<E>      // 순서O, 중복O
├─ Set<E>       // 중복X
└─ Queue<E>     // FIFO

Map<K,V>        // Key-Value
```

### 주요 구현체
```java
// List
ArrayList        // 조회 빠름
LinkedList       // 추가/삭제 빠름

// Set
HashSet          // 가장 빠름
LinkedHashSet    // 순서 유지
TreeSet          // 정렬

// Map
HashMap          // 가장 빠름
LinkedHashMap    // 순서 유지
TreeMap          // 정렬

// Queue
LinkedList       // FIFO
PriorityQueue    // 우선순위
ArrayDeque       // Stack
```

### 선택 기준
```
중복 허용? -> List
  조회 많음 -> ArrayList
  삽입/삭제 -> LinkedList

중복 불가? -> Set
  순서X -> HashSet
  순서O -> LinkedHashSet
  정렬 -> TreeSet

Key-Value? -> Map
  일반 -> HashMap
  순서 -> LinkedHashMap
  정렬 -> TreeMap
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. ArrayList →](./Collections-02-ArrayList.md)**

</div>
