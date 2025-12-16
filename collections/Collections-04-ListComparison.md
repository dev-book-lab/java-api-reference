# Collections 04. List 비교와 선택

> ArrayList vs LinkedList - 완벽 비교 가이드  
> Java API Reference

---

## 📑 목차

1. [구조 비교](#1-구조-비교)
2. [성능 비교](#2-성능-비교)
3. [메모리 비교](#3-메모리-비교)
4. [선택 가이드](#4-선택-가이드)
5. [실전 벤치마크](#5-실전-벤치마크)
6. [사용 사례](#6-사용-사례)
7. [실전 연습 문제](#7-실전-연습-문제)

---

## 1. 구조 비교

### 1.1 내부 구조

```java
public class StructureComparison {
    public static void main(String[] args) {
        System.out.println("=== ArrayList ===");
        System.out.println("내부: Object[] 배열");
        System.out.println("구조: [A][B][C][D][E]");
        System.out.println("특징: 연속 메모리\n");
        
        System.out.println("=== LinkedList ===");
        System.out.println("내부: Node 연결");
        System.out.println("구조: [A]<->[B]<->[C]<->[D]<->[E]");
        System.out.println("특징: 분산 메모리\n");
        
        System.out.println("=== 차이점 ===");
        System.out.println("ArrayList: 배열 기반, 연속 공간");
        System.out.println("LinkedList: 노드 기반, 분산 공간");
    }
}
```

### 1.2 요소 접근

```java
public class ElementAccess {
    public static void main(String[] args) {
        System.out.println("=== ArrayList 접근 ===");
        System.out.println("get(2) -> 배열[2]");
        System.out.println("시간: O(1) - 즉시 접근\n");
        
        System.out.println("=== LinkedList 접근 ===");
        System.out.println("get(2) -> head부터 순회");
        System.out.println("시간: O(n) - 순차 탐색\n");
        
        // 예시
        List<String> arrayList = new ArrayList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        List<String> linkedList = new LinkedList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 인덱스 접근
        long start = System.nanoTime();
        String item1 = arrayList.get(2);
        long time1 = System.nanoTime() - start;
        
        start = System.nanoTime();
        String item2 = linkedList.get(2);
        long time2 = System.nanoTime() - start;
        
        System.out.println("ArrayList: " + time1 + "ns");
        System.out.println("LinkedList: " + time2 + "ns");
    }
}
```

---

## 2. 성능 비교

### 2.1 시간 복잡도 표

```java
public class TimeComplexityTable {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 비교 ===\n");
        
        System.out.println("연산                ArrayList    LinkedList");
        System.out.println("-----------------------------------------------");
        System.out.println("get(i)              O(1)         O(n)");
        System.out.println("add(e) - 끝         O(1)*        O(1)");
        System.out.println("add(0, e) - 앞      O(n)         O(1)");
        System.out.println("add(i, e) - 중간    O(n)         O(n)");
        System.out.println("remove(0) - 앞      O(n)         O(1)");
        System.out.println("remove(i) - 중간    O(n)         O(n)");
        System.out.println("contains(e)         O(n)         O(n)");
        System.out.println("size()              O(1)         O(1)");
        
        System.out.println("\n* 용량 초과 시 O(n)");
    }
}
```

### 2.2 끝에 추가 비교

```java
public class AddLastComparison {
    public static void main(String[] args) {
        int n = 100000;
        
        // ArrayList
        List<Integer> arrayList = new ArrayList<>();
        long start = System.currentTimeMillis();
        
        for (int i = 0; i < n; i++) {
            arrayList.add(i);
        }
        
        long time1 = System.currentTimeMillis() - start;
        System.out.println("ArrayList 끝 추가: " + time1 + "ms");
        
        
        // LinkedList
        List<Integer> linkedList = new LinkedList<>();
        start = System.currentTimeMillis();
        
        for (int i = 0; i < n; i++) {
            linkedList.add(i);
        }
        
        long time2 = System.currentTimeMillis() - start;
        System.out.println("LinkedList 끝 추가: " + time2 + "ms");
        
        
        System.out.println("\n결과: ArrayList가 빠름");
        System.out.println("이유: 배열 연속 공간, 캐시 효율");
    }
}
```

### 2.3 앞에 추가 비교

```java
public class AddFirstComparison {
    public static void main(String[] args) {
        int n = 10000;
        
        // ArrayList
        List<Integer> arrayList = new ArrayList<>();
        long start = System.currentTimeMillis();
        
        for (int i = 0; i < n; i++) {
            arrayList.add(0, i);  // 앞에 추가
        }
        
        long time1 = System.currentTimeMillis() - start;
        System.out.println("ArrayList 앞 추가: " + time1 + "ms");
        
        
        // LinkedList
        List<Integer> linkedList = new LinkedList<>();
        start = System.currentTimeMillis();
        
        for (int i = 0; i < n; i++) {
            linkedList.add(0, i);  // 앞에 추가
        }
        
        long time2 = System.currentTimeMillis() - start;
        System.out.println("LinkedList 앞 추가: " + time2 + "ms");
        
        
        System.out.println("\n결과: LinkedList가 압도적으로 빠름");
        System.out.println("이유: ArrayList는 모든 요소 이동");
    }
}
```

### 2.4 인덱스 조회 비교

```java
public class GetComparison {
    public static void main(String[] args) {
        int n = 100000;
        
        // 데이터 준비
        List<Integer> arrayList = new ArrayList<>();
        List<Integer> linkedList = new LinkedList<>();
        
        for (int i = 0; i < n; i++) {
            arrayList.add(i);
            linkedList.add(i);
        }
        
        
        // ArrayList 조회
        long start = System.currentTimeMillis();
        
        for (int i = 0; i < n; i++) {
            arrayList.get(i);
        }
        
        long time1 = System.currentTimeMillis() - start;
        System.out.println("ArrayList 조회: " + time1 + "ms");
        
        
        // LinkedList 조회
        start = System.currentTimeMillis();
        
        for (int i = 0; i < n; i++) {
            linkedList.get(i);
        }
        
        long time2 = System.currentTimeMillis() - start;
        System.out.println("LinkedList 조회: " + time2 + "ms");
        
        
        System.out.println("\n결과: ArrayList가 압도적으로 빠름");
        System.out.println("이유: O(1) vs O(n)");
    }
}
```

---

## 3. 메모리 비교

### 3.1 메모리 사용량

```java
public class MemoryUsage {
    public static void main(String[] args) {
        System.out.println("=== ArrayList 메모리 ===");
        System.out.println("요소: Object[] 배열");
        System.out.println("오버헤드: 배열 크기");
        System.out.println("예시: 10개 → 배열[10]\n");
        
        System.out.println("=== LinkedList 메모리 ===");
        System.out.println("요소: Node 객체");
        System.out.println("오버헤드: prev + next 참조");
        System.out.println("예시: 10개 → Node * 10 + 참조 * 20\n");
        
        System.out.println("=== 비교 ===");
        System.out.println("ArrayList: 작은 메모리");
        System.out.println("LinkedList: 큰 메모리 (약 2~3배)");
    }
}
```

### 3.2 메모리 예시

```java
public class MemoryExample {
    public static void main(String[] args) {
        // 정수 100만 개 저장 가정
        int n = 1000000;
        
        System.out.println("=== 100만 개 정수 ===\n");
        
        System.out.println("ArrayList:");
        System.out.println("  데이터: " + (n * 4) + " bytes");
        System.out.println("  배열: " + (n * 8) + " bytes");
        System.out.println("  합계: ~" + (n * 12 / 1024 / 1024) + "MB\n");
        
        System.out.println("LinkedList:");
        System.out.println("  데이터: " + (n * 4) + " bytes");
        System.out.println("  참조: " + (n * 16) + " bytes");
        System.out.println("  노드: " + (n * 24) + " bytes");
        System.out.println("  합계: ~" + (n * 44 / 1024 / 1024) + "MB\n");
        
        System.out.println("LinkedList가 약 3.7배 더 큼");
    }
}
```

---

## 4. 선택 가이드

### 4.1 선택 플로우

```java
public class SelectionGuide {
    public static void main(String[] args) {
        System.out.println("=== List 선택 가이드 ===\n");
        
        System.out.println("Q1. 조회가 많은가?");
        System.out.println("  YES → ArrayList");
        System.out.println("    - get(i) 많이 사용");
        System.out.println("    - 랜덤 액세스\n");
        
        System.out.println("Q2. 앞/뒤 추가/삭제가 많은가?");
        System.out.println("  YES → LinkedList");
        System.out.println("    - addFirst/Last");
        System.out.println("    - removeFirst/Last\n");
        
        System.out.println("Q3. 중간 삽입이 많은가?");
        System.out.println("  작은 크기 → ArrayList");
        System.out.println("  큰 크기 → 고민 필요\n");
        
        System.out.println("Q4. 메모리가 제한적인가?");
        System.out.println("  YES → ArrayList");
        System.out.println("    - LinkedList는 2~3배\n");
        
        System.out.println("Q5. Queue/Stack으로 사용?");
        System.out.println("  YES → LinkedList (또는 ArrayDeque)");
    }
}
```

### 4.2 사용 패턴

```java
public class UsagePatterns {
    public static void main(String[] args) {
        System.out.println("=== ArrayList 추천 ===");
        System.out.println("1. 데이터 조회 중심");
        System.out.println("2. 끝에만 추가");
        System.out.println("3. 크기 변동 적음");
        System.out.println("4. 메모리 효율 중요");
        System.out.println("5. 일반적인 리스트");
        
        System.out.println("\n=== LinkedList 추천 ===");
        System.out.println("1. 앞/뒤 삽입/삭제");
        System.out.println("2. Queue/Deque 구현");
        System.out.println("3. 중간 삽입 많음");
        System.out.println("4. 순차 접근만");
        System.out.println("5. Iterator 사용");
    }
}
```

---

## 5. 실전 벤치마크

### 5.1 종합 성능 테스트

```java
public class ComprehensiveBenchmark {
    public static void main(String[] args) {
        int[] sizes = {1000, 10000, 100000};
        
        for (int size : sizes) {
            System.out.println("\n=== Size: " + size + " ===");
            testAddLast(size);
            testAddFirst(size);
            testGet(size);
            testIterate(size);
        }
    }
    
    static void testAddLast(int n) {
        List<Integer> al = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) al.add(i);
        long time1 = System.currentTimeMillis() - start;
        
        List<Integer> ll = new LinkedList<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ll.add(i);
        long time2 = System.currentTimeMillis() - start;
        
        System.out.println("Add Last - AL: " + time1 + "ms, LL: " + time2 + "ms");
    }
    
    static void testAddFirst(int n) {
        if (n > 10000) return;  // 너무 느림
        
        List<Integer> al = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) al.add(0, i);
        long time1 = System.currentTimeMillis() - start;
        
        List<Integer> ll = new LinkedList<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ll.add(0, i);
        long time2 = System.currentTimeMillis() - start;
        
        System.out.println("Add First - AL: " + time1 + "ms, LL: " + time2 + "ms");
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
        
        System.out.println("Get - AL: " + time1 + "ms, LL: " + time2 + "ms");
    }
    
    static void testIterate(int n) {
        List<Integer> al = new ArrayList<>();
        for (int i = 0; i < n; i++) al.add(i);
        
        List<Integer> ll = new LinkedList<>();
        for (int i = 0; i < n; i++) ll.add(i);
        
        long start = System.currentTimeMillis();
        for (int num : al) { }
        long time1 = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int num : ll) { }
        long time2 = System.currentTimeMillis() - start;
        
        System.out.println("Iterate - AL: " + time1 + "ms, LL: " + time2 + "ms");
    }
}
```

---

## 6. 사용 사례

### 6.1 ArrayList 사례

```java
public class ArrayListUseCases {
    public static void main(String[] args) {
        // 1. 학생 명단 (조회 많음)
        List<String> students = new ArrayList<>();
        students.add("Alice");
        students.add("Bob");
        String first = students.get(0);  // 빠름
        
        
        // 2. 설정 목록 (변경 적음)
        List<String> settings = new ArrayList<>(
            Arrays.asList("Setting1", "Setting2")
        );
        
        
        // 3. 데이터 분석 (전체 조회)
        List<Integer> data = new ArrayList<>();
        for (int i = 0; i < 1000; i++) {
            data.add(i);
        }
        
        for (int value : data) {
            // 분석...
        }
        
        
        System.out.println("ArrayList 사용 케이스:");
        System.out.println("- 학생 명단");
        System.out.println("- 설정 목록");
        System.out.println("- 데이터 분석");
    }
}
```

### 6.2 LinkedList 사례

```java
public class LinkedListUseCases {
    public static void main(String[] args) {
        // 1. 작업 큐 (FIFO)
        Queue<String> taskQueue = new LinkedList<>();
        taskQueue.offer("Task1");
        taskQueue.offer("Task2");
        String nextTask = taskQueue.poll();
        
        
        // 2. 브라우저 히스토리 (LIFO)
        Deque<String> history = new LinkedList<>();
        history.push("page1.com");
        history.push("page2.com");
        String back = history.pop();
        
        
        // 3. 플레이리스트 (중간 삽입)
        LinkedList<String> playlist = new LinkedList<>();
        playlist.add("Song1");
        playlist.add("Song2");
        playlist.add(1, "Favorite");  // 중간 삽입
        
        
        System.out.println("LinkedList 사용 케이스:");
        System.out.println("- 작업 큐");
        System.out.println("- 브라우저 히스토리");
        System.out.println("- 플레이리스트");
    }
}
```

---

## 7. 실전 연습 문제

### 문제 1: 적합한 List 선택

```java
// 각 상황에 맞는 List 선택
public class Problem1 {
    public static void main(String[] args) {
        System.out.println("=== List 선택 ===\n");
        
        System.out.println("1. 1만 개 데이터, 조회만");
        System.out.println("2. Queue 구현, FIFO");
        System.out.println("3. 100개 데이터, 중간 삽입 빈번");
        System.out.println("4. Stack 구현, LIFO");
        System.out.println("5. 로그 기록, 끝에만 추가");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void main(String[] args) {
        // 1. ArrayList - 조회 많음
        List<Integer> data = new ArrayList<>();
        
        // 2. LinkedList - Queue
        Queue<String> queue = new LinkedList<>();
        
        // 3. ArrayList - 크기 작음
        List<String> small = new ArrayList<>();
        
        // 4. LinkedList - Stack (Deque)
        Deque<String> stack = new LinkedList<>();
        
        // 5. ArrayList - 끝에 추가
        List<String> logs = new ArrayList<>();
        
        System.out.println("1. ArrayList");
        System.out.println("2. LinkedList");
        System.out.println("3. ArrayList");
        System.out.println("4. LinkedList");
        System.out.println("5. ArrayList");
    }
}
```
</details>

---

### 문제 2: 성능 차이 측정

```java
// 두 List의 성능 차이 측정
public class Problem2 {
    public static void measurePerformance(int n) {
        // ArrayList vs LinkedList
        // 1. 끝에 추가
        // 2. 앞에 추가
        // 3. 인덱스 조회
    }
    
    public static void main(String[] args) {
        measurePerformance(10000);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static void measurePerformance(int n) {
        System.out.println("=== n = " + n + " ===\n");
        
        // 1. 끝에 추가
        List<Integer> al = new ArrayList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) al.add(i);
        long alAdd = System.currentTimeMillis() - start;
        
        List<Integer> ll = new LinkedList<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ll.add(i);
        long llAdd = System.currentTimeMillis() - start;
        
        System.out.println("끝 추가:");
        System.out.println("  ArrayList: " + alAdd + "ms");
        System.out.println("  LinkedList: " + llAdd + "ms");
        System.out.println("  Winner: " + (alAdd < llAdd ? "ArrayList" : "LinkedList") + "\n");
        
        
        // 2. 앞에 추가 (작은 n으로)
        if (n <= 10000) {
            al = new ArrayList<>();
            start = System.currentTimeMillis();
            for (int i = 0; i < n; i++) al.add(0, i);
            long alAddFirst = System.currentTimeMillis() - start;
            
            ll = new LinkedList<>();
            start = System.currentTimeMillis();
            for (int i = 0; i < n; i++) ll.add(0, i);
            long llAddFirst = System.currentTimeMillis() - start;
            
            System.out.println("앞 추가:");
            System.out.println("  ArrayList: " + alAddFirst + "ms");
            System.out.println("  LinkedList: " + llAddFirst + "ms");
            System.out.println("  Winner: LinkedList\n");
        }
        
        
        // 3. 조회
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) al.get(i);
        long alGet = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ll.get(i);
        long llGet = System.currentTimeMillis() - start;
        
        System.out.println("인덱스 조회:");
        System.out.println("  ArrayList: " + alGet + "ms");
        System.out.println("  LinkedList: " + llGet + "ms");
        System.out.println("  Winner: ArrayList");
    }
}
```
</details>

---

### 문제 3: 최적 List 구현

```java
// 상황에 맞는 최적 List 구현
public class Problem3 {
    // 로그 시스템: 끝에만 추가, 전체 조회
    public static class LogSystem {
        private List<String> logs;
        
        public LogSystem() {
            // 어떤 List?
        }
        
        public void log(String message) {
            logs.add(message);
        }
        
        public void printAll() {
            for (String log : logs) {
                System.out.println(log);
            }
        }
    }
    
    // 작업 큐: FIFO
    public static class TaskQueue {
        private Queue<String> queue;
        
        public TaskQueue() {
            // 어떤 List?
        }
        
        public void addTask(String task) {
            queue.offer(task);
        }
        
        public String nextTask() {
            return queue.poll();
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    // 로그 시스템: ArrayList (끝 추가, 전체 조회)
    public static class LogSystem {
        private List<String> logs;
        
        public LogSystem() {
            this.logs = new ArrayList<>();
        }
        
        public void log(String message) {
            logs.add(message);
        }
        
        public void printAll() {
            for (String log : logs) {
                System.out.println(log);
            }
        }
    }
    
    // 작업 큐: LinkedList (FIFO)
    public static class TaskQueue {
        private Queue<String> queue;
        
        public TaskQueue() {
            this.queue = new LinkedList<>();
        }
        
        public void addTask(String task) {
            queue.offer(task);
        }
        
        public String nextTask() {
            return queue.poll();
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 구조
```
ArrayList:  [A][B][C][D][E]
LinkedList: [A]<->[B]<->[C]<->[D]<->[E]
```

### 성능 비교표
```
연산            ArrayList    LinkedList
get(i)          O(1) ✅      O(n) ❌
add (끝)        O(1) ✅      O(1) ✅
add (앞)        O(n) ❌      O(1) ✅
remove (앞)     O(n) ❌      O(1) ✅
Iterator        빠름 ✅       빠름 ✅
메모리          작음 ✅       큼 ❌
```

### 선택 기준
```java
// ArrayList 사용
- 조회 많음 (get)
- 끝에 추가 (add)
- 크기 작음
- 메모리 제한

// LinkedList 사용
- 앞/뒤 삽입/삭제
- Queue/Stack 구현
- 순차 접근만
- 중간 삽입 많음 (큰 데이터)
```

### 일반 권장사항
```
대부분의 경우: ArrayList ✅

99%는 ArrayList로 충분!
특수한 경우만 LinkedList 고려
```

---

## 🎉 List 시리즈 완료!

<div align="center">

### 📚 List 시리즈

| Chapter | 주제 |
|:-------:|------|
| [01. Collections 개요](./Collections-01-Overview.md) | 계층 구조, 인터페이스 |
| [02. ArrayList](./Collections-02-ArrayList.md) | 동적 배열 완전 정복 |
| [03. LinkedList](./Collections-03-LinkedList.md) | 연결 리스트 완전 정복 |
| [04. List 비교](./Collections-04-ListComparison.md) ⭐ | 성능, 선택 가이드 |

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 03. LinkedList](./Collections-03-LinkedList.md)**

</div>
