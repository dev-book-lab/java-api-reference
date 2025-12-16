# Collections 03. LinkedList 완전 정복

> 연결 리스트의 모든 것 - LinkedList 마스터하기  
> Java API Reference

---

## 📑 목차

1. [LinkedList 기본](#1-linkedlist-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [조회와 검색](#4-조회와-검색)
5. [Queue/Deque 기능](#5-queuedeque-기능)
6. [순회 방법](#6-순회-방법)
7. [내부 구조와 성능](#7-내부-구조와-성능)
8. [실전 활용](#8-실전-활용)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. LinkedList 기본

### 1.1 특징

```java
import java.util.*;

public class LinkedListBasic {
    public static void main(String[] args) {
        System.out.println("=== LinkedList 특징 ===\n");
        
        System.out.println("1. 이중 연결 리스트");
        System.out.println("   - 노드로 연결");
        System.out.println("   - 이전/다음 참조\n");
        
        System.out.println("2. 성능");
        System.out.println("   - 조회: O(n) 느림");
        System.out.println("   - 추가/삭제: O(1) 빠름");
        System.out.println("   - 메모리: 추가 공간 필요\n");
        
        System.out.println("3. 특성");
        System.out.println("   - 순서 유지");
        System.out.println("   - 중복 허용");
        System.out.println("   - null 허용");
        System.out.println("   - Queue/Deque 구현");
    }
}
```

### 1.2 내부 구조

```java
public class LinkedListStructure {
    public static void main(String[] args) {
        System.out.println("=== 내부 노드 구조 ===\n");
        
        System.out.println("class Node {");
        System.out.println("    E item;");
        System.out.println("    Node<E> next;");
        System.out.println("    Node<E> prev;");
        System.out.println("}\n");
        
        System.out.println("=== 연결 예시 ===");
        System.out.println("null <- [A] <-> [B] <-> [C] -> null");
        System.out.println("       head          tail\n");
        
        System.out.println("장점: 삽입/삭제 빠름");
        System.out.println("단점: 인덱스 접근 느림");
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class LinkedListCreation {
    public static void main(String[] args) {
        // 기본 생성
        LinkedList<String> list1 = new LinkedList<>();
        System.out.println("기본 생성: " + list1);
        
        
        // 다른 컬렉션으로
        LinkedList<String> list2 = new LinkedList<>(
            Arrays.asList("A", "B", "C")
        );
        System.out.println("복사 생성: " + list2);
        
        
        // List 인터페이스로 (권장)
        List<String> list3 = new LinkedList<>();
        System.out.println("List 타입: " + list3);
        
        
        // Queue로 사용
        Queue<String> queue = new LinkedList<>();
        System.out.println("Queue 타입: " + queue);
        
        
        // Deque로 사용
        Deque<String> deque = new LinkedList<>();
        System.out.println("Deque 타입: " + deque);
    }
}
```

### 2.2 초기화 방법

```java
public class LinkedListInit {
    public static void main(String[] args) {
        // 방법 1: add
        LinkedList<String> list1 = new LinkedList<>();
        list1.add("Apple");
        list1.add("Banana");
        System.out.println("add: " + list1);
        
        
        // 방법 2: addAll
        LinkedList<String> list2 = new LinkedList<>();
        list2.addAll(Arrays.asList("A", "B", "C"));
        System.out.println("addAll: " + list2);
        
        
        // 방법 3: 생성자
        LinkedList<String> list3 = new LinkedList<>(
            Arrays.asList("X", "Y", "Z")
        );
        System.out.println("생성자: " + list3);
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (add)

```java
public class LinkedListAdd {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>();
        
        // 끝에 추가
        list.add("A");
        list.add("B");
        System.out.println("추가: " + list);
        
        
        // 특정 위치
        list.add(1, "C");
        System.out.println("중간 추가: " + list);
        
        
        // 맨 앞 추가 (빠름!)
        list.addFirst("Z");
        System.out.println("앞 추가: " + list);
        
        
        // 맨 뒤 추가
        list.addLast("Y");
        System.out.println("뒤 추가: " + list);
    }
}
```

### 3.2 삭제 (remove)

```java
public class LinkedListRemove {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 인덱스로 삭제
        list.remove(1);
        System.out.println("인덱스 삭제: " + list);
        
        
        // 객체로 삭제
        list.remove("D");
        System.out.println("객체 삭제: " + list);
        
        
        // 맨 앞 삭제 (빠름!)
        list.removeFirst();
        System.out.println("앞 삭제: " + list);
        
        
        // 맨 뒤 삭제
        list.removeLast();
        System.out.println("뒤 삭제: " + list);
        
        
        // 전체 삭제
        list.clear();
        System.out.println("전체 삭제: " + list);
    }
}
```

---

## 4. 조회와 검색

### 4.1 조회 (get)

```java
public class LinkedListGet {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 인덱스 조회 (느림)
        String item = list.get(2);
        System.out.println("get(2): " + item);
        
        
        // 맨 앞 조회 (빠름)
        String first = list.getFirst();
        System.out.println("First: " + first);
        
        
        // 맨 뒤 조회 (빠름)
        String last = list.getLast();
        System.out.println("Last: " + last);
        
        
        // peek (Queue)
        String peek = list.peek();
        System.out.println("Peek: " + peek);
    }
}
```

### 4.2 검색

```java
public class LinkedListSearch {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>(
            Arrays.asList("A", "B", "C", "B", "D")
        );
        
        // 포함 여부
        boolean hasB = list.contains("B");
        System.out.println("B 포함? " + hasB);
        
        
        // 인덱스
        int index1 = list.indexOf("B");
        int index2 = list.lastIndexOf("B");
        
        System.out.println("B 첫 인덱스: " + index1);
        System.out.println("B 마지막: " + index2);
        
        
        // 크기
        int size = list.size();
        boolean empty = list.isEmpty();
        
        System.out.println("크기: " + size);
        System.out.println("비어있음? " + empty);
    }
}
```

---

## 5. Queue/Deque 기능

### 5.1 Queue 메서드

```java
public class LinkedListQueue {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        
        // offer: 추가
        queue.offer("First");
        queue.offer("Second");
        queue.offer("Third");
        System.out.println("Queue: " + queue);
        
        
        // peek: 조회 (제거X)
        String head = queue.peek();
        System.out.println("Peek: " + head);
        System.out.println("Queue: " + queue);
        
        
        // poll: 조회 + 제거
        String removed = queue.poll();
        System.out.println("Poll: " + removed);
        System.out.println("Queue: " + queue);
        
        
        // 빈 큐 처리
        Queue<String> emptyQueue = new LinkedList<>();
        System.out.println("Empty peek: " + emptyQueue.peek());  // null
        System.out.println("Empty poll: " + emptyQueue.poll());  // null
    }
}
```

### 5.2 Deque 메서드

```java
public class LinkedListDeque {
    public static void main(String[] args) {
        Deque<String> deque = new LinkedList<>();
        
        // 앞에 추가/제거
        deque.offerFirst("A");
        deque.offerFirst("B");
        System.out.println("Deque: " + deque);  // [B, A]
        
        String first = deque.pollFirst();
        System.out.println("Poll first: " + first);  // B
        
        
        // 뒤에 추가/제거
        deque.offerLast("C");
        deque.offerLast("D");
        System.out.println("Deque: " + deque);  // [A, C, D]
        
        String last = deque.pollLast();
        System.out.println("Poll last: " + last);  // D
        
        
        // 조회
        System.out.println("Peek first: " + deque.peekFirst());
        System.out.println("Peek last: " + deque.peekLast());
    }
}
```

### 5.3 Stack 기능

```java
public class LinkedListStack {
    public static void main(String[] args) {
        Deque<String> stack = new LinkedList<>();
        
        // push: 추가
        stack.push("A");
        stack.push("B");
        stack.push("C");
        System.out.println("Stack: " + stack);  // [C, B, A]
        
        
        // peek: 조회
        String top = stack.peek();
        System.out.println("Peek: " + top);  // C
        
        
        // pop: 조회 + 제거
        String popped = stack.pop();
        System.out.println("Pop: " + popped);  // C
        System.out.println("Stack: " + stack);  // [B, A]
        
        
        System.out.println("\n=== Stack vs Deque ===");
        System.out.println("Stack 클래스: 레거시 (비권장)");
        System.out.println("Deque 사용 권장!");
    }
}
```

---

## 6. 순회 방법

### 6.1 for 루프

```java
public class LinkedListIteration {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 향상된 for (권장)
        System.out.println("=== 향상된 for ===");
        for (String item : list) {
            System.out.print(item + " ");
        }
        System.out.println();
        
        
        // 일반 for (비권장 - 느림)
        System.out.println("\n=== 일반 for (느림) ===");
        for (int i = 0; i < list.size(); i++) {
            System.out.print(list.get(i) + " ");
        }
        System.out.println();
    }
}
```

### 6.2 Iterator

```java
public class LinkedListIterator {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>(
            Arrays.asList("A", "B", "C", "D")
        );
        
        // Iterator (효율적)
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
        
        
        // descendingIterator (역순)
        System.out.println("\n=== 역순 ===");
        Iterator<String> desc = list.descendingIterator();
        while (desc.hasNext()) {
            System.out.print(desc.next() + " ");
        }
        System.out.println();
    }
}
```

### 6.3 forEach와 Stream

```java
public class LinkedListForEach {
    public static void main(String[] args) {
        LinkedList<Integer> list = new LinkedList<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        // forEach
        System.out.println("=== forEach ===");
        list.forEach(n -> System.out.print(n + " "));
        System.out.println();
        
        
        // Stream
        System.out.println("\n=== Stream ===");
        list.stream()
           .filter(n -> n % 2 == 0)
           .forEach(System.out::println);
    }
}
```

---

## 7. 내부 구조와 성능

### 7.1 노드 구조

```java
public class LinkedListNode {
    public static void main(String[] args) {
        System.out.println("=== 노드 구조 ===\n");
        
        System.out.println("private static class Node<E> {");
        System.out.println("    E item;");
        System.out.println("    Node<E> next;");
        System.out.println("    Node<E> prev;");
        System.out.println("}\n");
        
        System.out.println("예시:");
        System.out.println("null <- [A] <-> [B] <-> [C] -> null");
        System.out.println("       first         last\n");
        
        System.out.println("메모리:");
        System.out.println("- 데이터 + 참조 2개");
        System.out.println("- ArrayList보다 많음");
    }
}
```

### 7.2 시간 복잡도

```java
public class LinkedListComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("addFirst/Last:  O(1)");
        System.out.println("  -> 노드 연결만\n");
        
        System.out.println("add(i, e):      O(n)");
        System.out.println("  -> 인덱스 탐색\n");
        
        System.out.println("removeFirst/Last: O(1)");
        System.out.println("  -> 참조 변경\n");
        
        System.out.println("get(i):         O(n)");
        System.out.println("  -> 순차 탐색\n");
        
        System.out.println("contains:       O(n)");
        System.out.println("  -> 선형 탐색");
    }
}
```

### 7.3 성능 측정

```java
public class LinkedListPerformance {
    public static void main(String[] args) {
        int n = 100000;
        LinkedList<Integer> list = new LinkedList<>();
        
        // 앞에 추가 (빠름)
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            list.addFirst(i);
        }
        long time1 = System.currentTimeMillis() - start;
        System.out.println("앞에 추가: " + time1 + "ms");
        
        
        // 조회 (느림)
        list.clear();
        for (int i = 0; i < n; i++) list.add(i);
        
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            list.get(i);
        }
        long time2 = System.currentTimeMillis() - start;
        System.out.println("인덱스 조회: " + time2 + "ms");
        
        
        // Iterator (빠름)
        start = System.currentTimeMillis();
        for (Integer num : list) {
            // 순회
        }
        long time3 = System.currentTimeMillis() - start;
        System.out.println("Iterator: " + time3 + "ms");
    }
}
```

---

## 8. 실전 활용

### 8.1 양방향 탐색

```java
public class BidirectionalSearch {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 앞에서 뒤로
        System.out.println("=== 정방향 ===");
        ListIterator<String> it = list.listIterator();
        while (it.hasNext()) {
            System.out.print(it.next() + " ");
        }
        System.out.println();
        
        
        // 뒤에서 앞으로
        System.out.println("\n=== 역방향 ===");
        while (it.hasPrevious()) {
            System.out.print(it.previous() + " ");
        }
        System.out.println();
    }
}
```

### 8.2 중간 삽입

```java
public class MiddleInsertion {
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>(
            Arrays.asList("A", "C", "E")
        );
        
        System.out.println("Before: " + list);
        
        // Iterator로 중간 삽입
        ListIterator<String> it = list.listIterator();
        
        while (it.hasNext()) {
            String current = it.next();
            
            if (current.equals("C")) {
                it.add("B");  // C 다음에 B 추가
            }
            
            if (current.equals("E")) {
                it.add("D");  // E 다음에 D 추가
            }
        }
        
        System.out.println("After: " + list);
    }
}
```

### 8.3 LRU 캐시

```java
public class LRUCache {
    private LinkedList<String> cache;
    private int capacity;
    
    public LRUCache(int capacity) {
        this.cache = new LinkedList<>();
        this.capacity = capacity;
    }
    
    public void access(String page) {
        // 이미 있으면 제거
        cache.remove(page);
        
        // 맨 앞에 추가
        cache.addFirst(page);
        
        // 용량 초과 시 마지막 제거
        if (cache.size() > capacity) {
            cache.removeLast();
        }
    }
    
    public void display() {
        System.out.println("Cache: " + cache);
    }
    
    public static void main(String[] args) {
        LRUCache lru = new LRUCache(3);
        
        lru.access("A");
        lru.display();  // [A]
        
        lru.access("B");
        lru.display();  // [B, A]
        
        lru.access("C");
        lru.display();  // [C, B, A]
        
        lru.access("A");  // A가 최신으로
        lru.display();  // [A, C, B]
        
        lru.access("D");  // B 제거됨
        lru.display();  // [D, A, C]
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 회문 판별

```java
// LinkedList로 회문 판별
public class Problem1 {
    public static boolean isPalindrome(LinkedList<Character> list) {
        // 코드 작성
        return false;
    }
    
    public static void main(String[] args) {
        LinkedList<Character> list1 = new LinkedList<>(
            Arrays.asList('r', 'a', 'c', 'e', 'c', 'a', 'r')
        );
        System.out.println("Palindrome? " + isPalindrome(list1));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static boolean isPalindrome(LinkedList<Character> list) {
        Deque<Character> deque = new LinkedList<>(list);
        
        while (deque.size() > 1) {
            if (!deque.pollFirst().equals(deque.pollLast())) {
                return false;
            }
        }
        
        return true;
    }
}
```
</details>

---

### 문제 2: 역순 출력

```java
// LinkedList 역순 출력 (원본 유지)
public class Problem2 {
    public static void printReverse(LinkedList<String> list) {
        // 코드 작성
    }
    
    public static void main(String[] args) {
        LinkedList<String> list = new LinkedList<>(
            Arrays.asList("A", "B", "C", "D")
        );
        printReverse(list);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    // 방법 1: descendingIterator
    public static void printReverse1(LinkedList<String> list) {
        Iterator<String> it = list.descendingIterator();
        while (it.hasNext()) {
            System.out.print(it.next() + " ");
        }
        System.out.println();
    }
    
    // 방법 2: ListIterator
    public static void printReverse2(LinkedList<String> list) {
        ListIterator<String> it = list.listIterator(list.size());
        while (it.hasPrevious()) {
            System.out.print(it.previous() + " ");
        }
        System.out.println();
    }
}
```
</details>

---

### 문제 3: 원형 큐

```java
// LinkedList로 원형 큐 구현
public class Problem3 {
    public static class CircularQueue {
        private LinkedList<Integer> queue;
        private int capacity;
        
        public CircularQueue(int capacity) {
            // 코드 작성
        }
        
        public void enqueue(int value) {
            // 코드 작성
        }
        
        public int dequeue() {
            // 코드 작성
            return -1;
        }
    }
    
    public static void main(String[] args) {
        CircularQueue cq = new CircularQueue(3);
        cq.enqueue(1);
        cq.enqueue(2);
        cq.enqueue(3);
        cq.enqueue(4);  // 1이 제거되고 4 추가
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static class CircularQueue {
        private LinkedList<Integer> queue;
        private int capacity;
        
        public CircularQueue(int capacity) {
            this.queue = new LinkedList<>();
            this.capacity = capacity;
        }
        
        public void enqueue(int value) {
            if (queue.size() >= capacity) {
                queue.removeFirst();
            }
            queue.addLast(value);
            System.out.println("Queue: " + queue);
        }
        
        public int dequeue() {
            if (queue.isEmpty()) {
                return -1;
            }
            return queue.removeFirst();
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// 이중 연결 리스트
LinkedList<String> list = new LinkedList<>();
list.addFirst("A");  // O(1)
list.addLast("B");   // O(1)
```

### 주요 메서드
```java
// 추가
list.addFirst("A");
list.addLast("B");
list.add(1, "C");

// 삭제
list.removeFirst();
list.removeLast();
list.remove(1);

// 조회
list.getFirst();
list.getLast();
list.get(1);  // 느림!

// Queue
list.offer("A");
list.poll();
list.peek();

// Deque
list.offerFirst("A");
list.pollFirst();
list.offerLast("B");
list.pollLast();
```

### 시간 복잡도
```
addFirst/Last:    O(1)
removeFirst/Last: O(1)
add(i):           O(n)
get(i):           O(n)
contains:         O(n)
```

### 사용 시기
```
✅ 앞/뒤 추가/삭제
✅ Queue/Stack 구현
✅ 순차 접근
❌ 인덱스 조회
❌ 랜덤 액세스
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. ArrayList](./Collections-02-ArrayList.md) | [다음: 04. List 비교 →](./Collections-04-ListComparison.md)**

</div>
