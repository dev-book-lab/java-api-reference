# Collections 14. PriorityQueue 완전 정복

> 우선순위 큐 - PriorityQueue 마스터하기  
> Java API Reference

---

## 📑 목차

1. [PriorityQueue 기본](#1-priorityqueue-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [우선순위 정렬](#4-우선순위-정렬)
5. [힙 구조](#5-힙-구조)
6. [실전 활용](#6-실전-활용)
7. [실전 연습 문제](#7-실전-연습-문제)

---

## 1. PriorityQueue 기본

### 1.1 특징

```java
import java.util.*;

public class PriorityQueueBasic {
    public static void main(String[] args) {
        System.out.println("=== PriorityQueue 특징 ===\n");
        
        System.out.println("1. 우선순위 기반");
        System.out.println("   - 자동 정렬");
        System.out.println("   - 최소값 먼저 (기본)\n");
        
        System.out.println("2. 힙 구조");
        System.out.println("   - 완전 이진 트리");
        System.out.println("   - 배열 기반\n");
        
        System.out.println("3. 성능");
        System.out.println("   - offer: O(log n)");
        System.out.println("   - poll: O(log n)");
        System.out.println("   - peek: O(1)\n");
        
        System.out.println("4. 순서 없음");
        System.out.println("   - 순회 시 정렬X");
        System.out.println("   - poll만 정렬 보장");
    }
}
```

### 1.2 기본 사용 예제

```java
public class PriorityQueueExample {
    public static void main(String[] args) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        // 추가 (자동 정렬)
        pq.offer(5);
        pq.offer(2);
        pq.offer(8);
        pq.offer(1);
        pq.offer(9);
        
        System.out.println("PriorityQueue: " + pq);
        // [1, 2, 8, 5, 9] (힙 순서)
        
        
        // 최소값 조회
        System.out.println("Peek: " + pq.peek());  // 1
        
        
        // 제거 (작은 것부터)
        System.out.println("\n=== 제거 순서 ===");
        while (!pq.isEmpty()) {
            System.out.println(pq.poll());
        }
        // 1, 2, 5, 8, 9
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class PriorityQueueCreation {
    public static void main(String[] args) {
        // 기본 생성 (최소 힙)
        PriorityQueue<Integer> pq1 = new PriorityQueue<>();
        System.out.println("기본: " + pq1);
        
        
        // 초기 용량 지정
        PriorityQueue<Integer> pq2 = new PriorityQueue<>(100);
        System.out.println("용량 100: " + pq2);
        
        
        // 다른 컬렉션으로
        PriorityQueue<Integer> pq3 = new PriorityQueue<>(
            Arrays.asList(5, 2, 8, 1, 9)
        );
        System.out.println("컬렉션: " + pq3);
        System.out.println("최소값: " + pq3.peek());  // 1
        
        
        // Comparator로 (최대 힙)
        PriorityQueue<Integer> pq4 = new PriorityQueue<>(
            Collections.reverseOrder()
        );
        pq4.addAll(Arrays.asList(5, 2, 8, 1, 9));
        System.out.println("\n최대 힙: " + pq4);
        System.out.println("최대값: " + pq4.peek());  // 9
    }
}
```

### 2.2 커스텀 우선순위

```java
public class CustomPriority {
    static class Task {
        String name;
        int priority;  // 낮을수록 우선
        
        Task(String name, int priority) {
            this.name = name;
            this.priority = priority;
        }
        
        @Override
        public String toString() {
            return name + "(P" + priority + ")";
        }
    }
    
    public static void main(String[] args) {
        // 우선순위 낮은 순
        PriorityQueue<Task> pq = new PriorityQueue<>(
            Comparator.comparingInt(t -> t.priority)
        );
        
        pq.offer(new Task("Task A", 3));
        pq.offer(new Task("Task B", 1));
        pq.offer(new Task("Task C", 2));
        
        System.out.println("=== 처리 순서 ===");
        while (!pq.isEmpty()) {
            System.out.println(pq.poll());
        }
        // Task B(P1), Task C(P2), Task A(P3)
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (offer)

```java
public class PriorityQueueOffer {
    public static void main(String[] args) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        // offer
        pq.offer(5);
        pq.offer(2);
        pq.offer(8);
        
        System.out.println("PQ: " + pq);
        System.out.println("최소: " + pq.peek());  // 2
        
        
        // add (동일)
        pq.add(1);
        System.out.println("add 후: " + pq);
        System.out.println("최소: " + pq.peek());  // 1
        
        
        // addAll
        pq.addAll(Arrays.asList(9, 3, 7));
        System.out.println("addAll 후: " + pq);
        System.out.println("최소: " + pq.peek());  // 1
    }
}
```

### 3.2 제거 (poll)

```java
public class PriorityQueuePoll {
    public static void main(String[] args) {
        PriorityQueue<Integer> pq = new PriorityQueue<>(
            Arrays.asList(5, 2, 8, 1, 9, 3, 7)
        );
        
        System.out.println("원본: " + pq);
        
        
        // poll (최소값 제거)
        System.out.println("\n=== poll ===");
        System.out.println(pq.poll());  // 1
        System.out.println(pq.poll());  // 2
        System.out.println(pq.poll());  // 3
        
        System.out.println("남은 것: " + pq);
        
        
        // remove (특정 값 제거)
        pq.remove(8);
        System.out.println("8 제거: " + pq);
        
        
        // clear
        pq.clear();
        System.out.println("clear: " + pq);
    }
}
```

---

## 4. 우선순위 정렬

### 4.1 최소 힙 (기본)

```java
public class MinHeap {
    public static void main(String[] args) {
        // 최소 힙 (작은 값 우선)
        PriorityQueue<Integer> minHeap = new PriorityQueue<>();
        
        minHeap.addAll(Arrays.asList(5, 2, 8, 1, 9, 3, 7));
        
        System.out.println("=== 최소 힙 ===");
        while (!minHeap.isEmpty()) {
            System.out.print(minHeap.poll() + " ");
        }
        System.out.println();
        // 1 2 3 5 7 8 9
    }
}
```

### 4.2 최대 힙

```java
public class MaxHeap {
    public static void main(String[] args) {
        // 최대 힙 (큰 값 우선)
        PriorityQueue<Integer> maxHeap = new PriorityQueue<>(
            Collections.reverseOrder()
        );
        
        maxHeap.addAll(Arrays.asList(5, 2, 8, 1, 9, 3, 7));
        
        System.out.println("=== 최대 힙 ===");
        while (!maxHeap.isEmpty()) {
            System.out.print(maxHeap.poll() + " ");
        }
        System.out.println();
        // 9 8 7 5 3 2 1
    }
}
```

### 4.3 복합 정렬

```java
public class ComplexSort {
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
        // 점수 높은 순, 같으면 나이 어린 순
        PriorityQueue<Student> pq = new PriorityQueue<>(
            Comparator.comparingInt((Student s) -> -s.score)
                     .thenComparingInt(s -> s.age)
        );
        
        pq.offer(new Student("Alice", 90, 25));
        pq.offer(new Student("Bob", 95, 22));
        pq.offer(new Student("Charlie", 90, 20));
        pq.offer(new Student("David", 95, 23));
        
        System.out.println("=== 순위 ===");
        int rank = 1;
        while (!pq.isEmpty()) {
            System.out.println(rank++ + ". " + pq.poll());
        }
        // 1. Bob(95점, 22세)
        // 2. David(95점, 23세)
        // 3. Charlie(90점, 20세)
        // 4. Alice(90점, 25세)
    }
}
```

---

## 5. 힙 구조

### 5.1 힙의 특성

```java
public class HeapStructure {
    public static void main(String[] args) {
        System.out.println("=== 힙 구조 ===\n");
        
        System.out.println("1. 완전 이진 트리");
        System.out.println("   - 마지막 레벨 제외 모두 채워짐");
        System.out.println("   - 왼쪽부터 채워짐\n");
        
        System.out.println("2. 힙 속성");
        System.out.println("   - 최소 힙: 부모 <= 자식");
        System.out.println("   - 최대 힙: 부모 >= 자식\n");
        
        System.out.println("3. 배열 표현");
        System.out.println("   - 부모: (i-1)/2");
        System.out.println("   - 왼쪽 자식: 2*i+1");
        System.out.println("   - 오른쪽 자식: 2*i+2\n");
        
        System.out.println("4. 연산");
        System.out.println("   - 삽입: O(log n)");
        System.out.println("   - 삭제: O(log n)");
        System.out.println("   - 조회: O(1)");
    }
}
```

### 5.2 힙 시각화

```java
public class HeapVisualization {
    public static void main(String[] args) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        System.out.println("=== 삽입 과정 ===\n");
        
        int[] values = {5, 2, 8, 1, 9};
        for (int val : values) {
            pq.offer(val);
            System.out.println(val + " 삽입 후: " + pq);
        }
        
        System.out.println("\n힙 구조 (최소 힙):");
        System.out.println("       1");
        System.out.println("      / \\");
        System.out.println("     2   5");
        System.out.println("    / \\");
        System.out.println("   8   9");
    }
}
```

---

## 6. 실전 활용

### 6.1 Top K 문제

```java
public class TopK {
    public static List<Integer> findTopK(int[] nums, int k) {
        // 최소 힙 사용 (크기 K 유지)
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        for (int num : nums) {
            pq.offer(num);
            
            if (pq.size() > k) {
                pq.poll();  // 최소값 제거
            }
        }
        
        return new ArrayList<>(pq);
    }
    
    public static void main(String[] args) {
        int[] nums = {3, 2, 1, 5, 6, 4};
        int k = 3;
        
        List<Integer> topK = findTopK(nums, k);
        Collections.sort(topK, Collections.reverseOrder());
        
        System.out.println("배열: " + Arrays.toString(nums));
        System.out.println("Top " + k + ": " + topK);
        // [6, 5, 4]
    }
}
```

### 6.2 작업 스케줄러

```java
public class TaskScheduler {
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
            return name + "(P" + priority + ")";
        }
    }
    
    private PriorityQueue<Task> taskQueue;
    
    public TaskScheduler() {
        this.taskQueue = new PriorityQueue<>(
            Comparator.comparingInt((Task t) -> t.priority)
                     .thenComparingLong(t -> t.deadline)
        );
    }
    
    public void addTask(String name, int priority, long deadline) {
        taskQueue.offer(new Task(name, priority, deadline));
        System.out.println("추가: " + name);
    }
    
    public void processNext() {
        Task task = taskQueue.poll();
        if (task != null) {
            System.out.println("처리: " + task);
        } else {
            System.out.println("작업 없음");
        }
    }
    
    public void showQueue() {
        System.out.println("대기: " + taskQueue.size() + "개");
    }
    
    public static void main(String[] args) {
        TaskScheduler scheduler = new TaskScheduler();
        
        scheduler.addTask("Task A", 3, 1000);
        scheduler.addTask("Task B", 1, 2000);
        scheduler.addTask("Task C", 2, 1500);
        
        scheduler.showQueue();
        
        System.out.println("\n=== 처리 순서 ===");
        while (scheduler.taskQueue.size() > 0) {
            scheduler.processNext();
        }
    }
}
```

### 6.3 중앙값 찾기

```java
public class MedianFinder {
    private PriorityQueue<Integer> maxHeap;  // 작은 쪽
    private PriorityQueue<Integer> minHeap;  // 큰 쪽
    
    public MedianFinder() {
        this.maxHeap = new PriorityQueue<>(Collections.reverseOrder());
        this.minHeap = new PriorityQueue<>();
    }
    
    public void addNum(int num) {
        // maxHeap에 먼저 추가
        maxHeap.offer(num);
        
        // maxHeap의 최대값을 minHeap으로
        minHeap.offer(maxHeap.poll());
        
        // 크기 균형
        if (maxHeap.size() < minHeap.size()) {
            maxHeap.offer(minHeap.poll());
        }
    }
    
    public double findMedian() {
        if (maxHeap.size() == minHeap.size()) {
            return (maxHeap.peek() + minHeap.peek()) / 2.0;
        } else {
            return maxHeap.peek();
        }
    }
    
    public static void main(String[] args) {
        MedianFinder mf = new MedianFinder();
        
        mf.addNum(1);
        System.out.println("중앙값: " + mf.findMedian());  // 1
        
        mf.addNum(2);
        System.out.println("중앙값: " + mf.findMedian());  // 1.5
        
        mf.addNum(3);
        System.out.println("중앙값: " + mf.findMedian());  // 2
        
        mf.addNum(4);
        System.out.println("중앙값: " + mf.findMedian());  // 2.5
    }
}
```

---

## 7. 실전 연습 문제

### 문제 1: K번째 큰 수

```java
// 배열에서 K번째로 큰 수
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
        // 최소 힙 (크기 k 유지)
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        for (int num : nums) {
            pq.offer(num);
            
            if (pq.size() > k) {
                pq.poll();
            }
        }
        
        return pq.peek();
    }
    
    // 최대 힙 버전
    public static int findKthLargest2(int[] nums, int k) {
        PriorityQueue<Integer> pq = new PriorityQueue<>(
            Collections.reverseOrder()
        );
        
        for (int num : nums) {
            pq.offer(num);
        }
        
        for (int i = 1; i < k; i++) {
            pq.poll();
        }
        
        return pq.peek();
    }
}
```
</details>

---

### 문제 2: 가장 가까운 K개 점

```java
// 원점에서 가장 가까운 K개 점
public class Problem2 {
    static class Point {
        int x, y;
        
        Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
        
        double distance() {
            return Math.sqrt(x * x + y * y);
        }
        
        @Override
        public String toString() {
            return "(" + x + "," + y + ")";
        }
    }
    
    public static List<Point> kClosest(Point[] points, int k) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        Point[] points = {
            new Point(1, 3),
            new Point(-2, 2),
            new Point(5, 8),
            new Point(0, 1)
        };
        
        List<Point> closest = kClosest(points, 2);
        System.out.println("가장 가까운 2개: " + closest);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static List<Point> kClosest(Point[] points, int k) {
        // 최대 힙 (거리 기준)
        PriorityQueue<Point> pq = new PriorityQueue<>(
            (p1, p2) -> Double.compare(p2.distance(), p1.distance())
        );
        
        for (Point point : points) {
            pq.offer(point);
            
            if (pq.size() > k) {
                pq.poll();
            }
        }
        
        return new ArrayList<>(pq);
    }
}
```
</details>

---

### 문제 3: 병합 K개 정렬 배열

```java
// K개의 정렬된 배열을 하나로 병합
public class Problem3 {
    static class Element {
        int value;
        int arrayIndex;
        int elementIndex;
        
        Element(int value, int arrayIndex, int elementIndex) {
            this.value = value;
            this.arrayIndex = arrayIndex;
            this.elementIndex = elementIndex;
        }
    }
    
    public static List<Integer> mergeKArrays(int[][] arrays) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[][] arrays = {
            {1, 4, 7},
            {2, 5, 8},
            {3, 6, 9}
        };
        
        List<Integer> merged = mergeKArrays(arrays);
        System.out.println("병합: " + merged);
        // [1, 2, 3, 4, 5, 6, 7, 8, 9]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static List<Integer> mergeKArrays(int[][] arrays) {
        List<Integer> result = new ArrayList<>();
        
        PriorityQueue<Element> pq = new PriorityQueue<>(
            Comparator.comparingInt(e -> e.value)
        );
        
        // 각 배열의 첫 요소 추가
        for (int i = 0; i < arrays.length; i++) {
            if (arrays[i].length > 0) {
                pq.offer(new Element(arrays[i][0], i, 0));
            }
        }
        
        // 최소값 추출 & 다음 요소 추가
        while (!pq.isEmpty()) {
            Element e = pq.poll();
            result.add(e.value);
            
            // 다음 요소가 있으면 추가
            if (e.elementIndex + 1 < arrays[e.arrayIndex].length) {
                int nextValue = arrays[e.arrayIndex][e.elementIndex + 1];
                pq.offer(new Element(nextValue, e.arrayIndex, e.elementIndex + 1));
            }
        }
        
        return result;
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// 우선순위 자동 정렬
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.offer(5);
pq.offer(2);
pq.offer(8);
System.out.println(pq.poll());  // 2 (최소값)
```

### 주요 메서드
```java
// 추가
pq.offer(5);        // O(log n)
pq.add(5);          // 동일

// 조회
pq.peek();          // O(1)

// 제거
pq.poll();          // O(log n)
pq.remove(5);       // O(n)
```

### 최소/최대 힙
```java
// 최소 힙 (기본)
PriorityQueue<Integer> minHeap = new PriorityQueue<>();

// 최대 힙
PriorityQueue<Integer> maxHeap = new PriorityQueue<>(
    Collections.reverseOrder()
);
```

### 시간 복잡도
```
offer:  O(log n)
poll:   O(log n)
peek:   O(1)
remove: O(n)
```

### 사용 시기
```
✅ Top K 문제
✅ 우선순위 작업
✅ 중앙값 찾기
✅ 병합 정렬 배열
❌ 순회 필요 (정렬 안 됨)
❌ 인덱스 접근
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 13. Queue & Deque](./Collections-13-QueueDeque.md) | [다음: 15. Stack 구현 →](./Collections-15-Stack.md)**

</div>
