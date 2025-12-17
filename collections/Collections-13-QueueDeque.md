# Collections 13. Queue & Deque 기본

> 큐와 덱의 모든 것 - Queue & Deque 마스터하기  
> Java API Reference

---

## 📑 목차

1. [Queue 기본](#1-queue-기본)
2. [Deque 기본](#2-deque-기본)
3. [Queue 메서드](#3-queue-메서드)
4. [Deque 메서드](#4-deque-메서드)
5. [구현체 비교](#5-구현체-비교)
6. [Stack 대체](#6-stack-대체)
7. [실전 활용](#7-실전-활용)
8. [실전 연습 문제](#8-실전-연습-문제)

---

## 1. Queue 기본

### 1.1 Queue 개념

```java
import java.util.*;

public class QueueBasic {
    public static void main(String[] args) {
        System.out.println("=== Queue 특징 ===\n");
        
        System.out.println("1. FIFO (First In First Out)");
        System.out.println("   - 먼저 들어간 것이 먼저 나옴");
        System.out.println("   - 대기열, 작업 큐\n");
        
        System.out.println("2. 주요 연산");
        System.out.println("   - offer: 끝에 추가");
        System.out.println("   - poll: 앞에서 제거");
        System.out.println("   - peek: 앞 요소 확인\n");
        
        System.out.println("3. 구현체");
        System.out.println("   - LinkedList");
        System.out.println("   - ArrayDeque");
        System.out.println("   - PriorityQueue");
    }
}
```

### 1.2 기본 사용 예제

```java
public class QueueExample {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        
        // 추가 (끝에)
        queue.offer("First");
        queue.offer("Second");
        queue.offer("Third");
        
        System.out.println("Queue: " + queue);
        // [First, Second, Third]
        
        
        // 조회 (앞 요소)
        String front = queue.peek();
        System.out.println("Peek: " + front);  // First
        System.out.println("Queue: " + queue);  // 변화 없음
        
        
        // 제거 (앞에서)
        String removed = queue.poll();
        System.out.println("Poll: " + removed);  // First
        System.out.println("Queue: " + queue);  // [Second, Third]
        
        
        // 모두 제거
        while (!queue.isEmpty()) {
            System.out.println("Poll: " + queue.poll());
        }
        System.out.println("Empty: " + queue.isEmpty());
    }
}
```

---

## 2. Deque 기본

### 2.1 Deque 개념

```java
public class DequeBasic {
    public static void main(String[] args) {
        System.out.println("=== Deque 특징 ===\n");
        
        System.out.println("1. Double-Ended Queue");
        System.out.println("   - 양쪽 끝에서 삽입/삭제");
        System.out.println("   - Queue + Stack 기능\n");
        
        System.out.println("2. 주요 연산");
        System.out.println("   - offerFirst/Last");
        System.out.println("   - pollFirst/Last");
        System.out.println("   - peekFirst/Last\n");
        
        System.out.println("3. 사용처");
        System.out.println("   - Stack 대체 (권장)");
        System.out.println("   - 양방향 큐");
        System.out.println("   - 브라우저 히스토리");
    }
}
```

### 2.2 기본 사용 예제

```java
public class DequeExample {
    public static void main(String[] args) {
        Deque<String> deque = new ArrayDeque<>();
        
        // 앞에 추가
        deque.offerFirst("A");
        deque.offerFirst("B");
        System.out.println("앞 추가: " + deque);
        // [B, A]
        
        
        // 뒤에 추가
        deque.offerLast("C");
        deque.offerLast("D");
        System.out.println("뒤 추가: " + deque);
        // [B, A, C, D]
        
        
        // 앞에서 제거
        String first = deque.pollFirst();
        System.out.println("앞 제거: " + first);  // B
        
        
        // 뒤에서 제거
        String last = deque.pollLast();
        System.out.println("뒤 제거: " + last);  // D
        
        System.out.println("최종: " + deque);
        // [A, C]
    }
}
```

---

## 3. Queue 메서드

### 3.1 추가 메서드

```java
public class QueueAdd {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        
        // offer (성공 시 true, 실패 시 false)
        boolean added1 = queue.offer("A");
        boolean added2 = queue.offer("B");
        
        System.out.println("offer 성공: " + added1);
        System.out.println("Queue: " + queue);
        
        
        // add (실패 시 예외)
        queue.add("C");
        System.out.println("add 후: " + queue);
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("offer: 실패 시 false 반환");
        System.out.println("add: 실패 시 예외 발생");
    }
}
```

### 3.2 조회 메서드

```java
public class QueuePeek {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        queue.offer("First");
        queue.offer("Second");
        queue.offer("Third");
        
        // peek (제거 안 함, 빈 큐 시 null)
        String p1 = queue.peek();
        System.out.println("Peek: " + p1);  // First
        System.out.println("Queue: " + queue);  // 변화 없음
        
        
        // element (제거 안 함, 빈 큐 시 예외)
        String e1 = queue.element();
        System.out.println("Element: " + e1);  // First
        
        
        // 빈 Queue
        Queue<String> empty = new LinkedList<>();
        
        String p2 = empty.peek();
        System.out.println("\n빈 큐 peek: " + p2);  // null
        
        try {
            empty.element();
        } catch (NoSuchElementException ex) {
            System.out.println("빈 큐 element: 예외!");
        }
    }
}
```

### 3.3 제거 메서드

```java
public class QueueRemove {
    public static void main(String[] args) {
        Queue<String> queue = new LinkedList<>();
        queue.offer("A");
        queue.offer("B");
        queue.offer("C");
        
        // poll (제거, 빈 큐 시 null)
        String r1 = queue.poll();
        System.out.println("Poll: " + r1);  // A
        System.out.println("Queue: " + queue);
        
        
        // remove (제거, 빈 큐 시 예외)
        String r2 = queue.remove();
        System.out.println("Remove: " + r2);  // B
        System.out.println("Queue: " + queue);
        
        
        // 빈 Queue
        queue.clear();
        
        String r3 = queue.poll();
        System.out.println("\n빈 큐 poll: " + r3);  // null
        
        try {
            queue.remove();
        } catch (NoSuchElementException ex) {
            System.out.println("빈 큐 remove: 예외!");
        }
    }
}
```

---

## 4. Deque 메서드

### 4.1 앞쪽 연산

```java
public class DequeFirst {
    public static void main(String[] args) {
        Deque<String> deque = new ArrayDeque<>();
        
        // 앞에 추가
        deque.offerFirst("A");
        deque.offerFirst("B");
        deque.offerFirst("C");
        System.out.println("offerFirst: " + deque);
        // [C, B, A]
        
        
        // 앞 조회
        String peek = deque.peekFirst();
        System.out.println("peekFirst: " + peek);  // C
        
        
        // 앞에서 제거
        String poll = deque.pollFirst();
        System.out.println("pollFirst: " + poll);  // C
        System.out.println("After: " + deque);
        // [B, A]
    }
}
```

### 4.2 뒤쪽 연산

```java
public class DequeLast {
    public static void main(String[] args) {
        Deque<String> deque = new ArrayDeque<>();
        
        // 뒤에 추가
        deque.offerLast("A");
        deque.offerLast("B");
        deque.offerLast("C");
        System.out.println("offerLast: " + deque);
        // [A, B, C]
        
        
        // 뒤 조회
        String peek = deque.peekLast();
        System.out.println("peekLast: " + peek);  // C
        
        
        // 뒤에서 제거
        String poll = deque.pollLast();
        System.out.println("pollLast: " + poll);  // C
        System.out.println("After: " + deque);
        // [A, B]
    }
}
```

### 4.3 Stack 메서드

```java
public class DequeStack {
    public static void main(String[] args) {
        Deque<String> stack = new ArrayDeque<>();
        
        // push (앞에 추가)
        stack.push("A");
        stack.push("B");
        stack.push("C");
        System.out.println("push: " + stack);
        // [C, B, A]
        
        
        // peek (앞 조회)
        String top = stack.peek();
        System.out.println("peek: " + top);  // C
        
        
        // pop (앞에서 제거)
        String popped = stack.pop();
        System.out.println("pop: " + popped);  // C
        System.out.println("After: " + stack);
        // [B, A]
        
        
        System.out.println("\n=== Stack 메서드 매핑 ===");
        System.out.println("push(e) = offerFirst(e)");
        System.out.println("pop() = pollFirst()");
        System.out.println("peek() = peekFirst()");
    }
}
```

---

## 5. 구현체 비교

### 5.1 LinkedList vs ArrayDeque

```java
public class ImplementationComparison {
    public static void main(String[] args) {
        System.out.println("=== LinkedList ===");
        System.out.println("구조: 이중 연결 리스트");
        System.out.println("특징: 노드 기반");
        System.out.println("장점: 중간 삽입/삭제");
        System.out.println("단점: 느린 인덱스 접근\n");
        
        System.out.println("=== ArrayDeque ===");
        System.out.println("구조: 동적 배열");
        System.out.println("특징: 순환 배열");
        System.out.println("장점: 빠른 양끝 연산");
        System.out.println("단점: 중간 연산 느림\n");
        
        System.out.println("=== 권장 ===");
        System.out.println("Queue/Deque: ArrayDeque");
        System.out.println("List 필요: LinkedList");
    }
}
```

### 5.2 성능 비교

```java
public class PerformanceComparison {
    public static void main(String[] args) {
        int n = 100000;
        
        // LinkedList
        Deque<Integer> linkedList = new LinkedList<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            linkedList.offerLast(i);
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // ArrayDeque
        Deque<Integer> arrayDeque = new ArrayDeque<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            arrayDeque.offerLast(i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        System.out.println("=== " + n + "개 추가 ===");
        System.out.println("LinkedList: " + time1 + "ms");
        System.out.println("ArrayDeque: " + time2 + "ms");
        System.out.println("\nArrayDeque가 더 빠름!");
    }
}
```

---

## 6. Stack 대체

### 6.1 Stack 클래스의 문제

```java
public class StackProblem {
    public static void main(String[] args) {
        System.out.println("=== Stack 클래스 문제점 ===\n");
        
        System.out.println("1. 레거시 클래스");
        System.out.println("   - Java 1.0 시절");
        System.out.println("   - Vector 상속\n");
        
        System.out.println("2. 동기화 오버헤드");
        System.out.println("   - 모든 메서드 synchronized");
        System.out.println("   - 단일 스레드에서도 느림\n");
        
        System.out.println("3. Vector 상속 문제");
        System.out.println("   - 중간 삽입/삭제 가능");
        System.out.println("   - Stack 의미 위반\n");
        
        System.out.println("=== 해결책 ===");
        System.out.println("Deque 사용 권장!");
    }
}
```

### 6.2 Deque로 Stack 구현

```java
public class DequeAsStack {
    public static void main(String[] args) {
        // ArrayDeque로 Stack
        Deque<String> stack = new ArrayDeque<>();
        
        // push
        stack.push("First");
        stack.push("Second");
        stack.push("Third");
        
        System.out.println("Stack: " + stack);
        // [Third, Second, First]
        
        
        // peek
        System.out.println("Top: " + stack.peek());  // Third
        
        
        // pop
        while (!stack.isEmpty()) {
            System.out.println("Pop: " + stack.pop());
        }
        
        
        System.out.println("\n=== 장점 ===");
        System.out.println("1. 빠름 (동기화X)");
        System.out.println("2. 메모리 효율적");
        System.out.println("3. 일관된 API");
    }
}
```

---

## 7. 실전 활용

### 7.1 작업 큐

```java
public class TaskQueue {
    private Queue<String> tasks;
    
    public TaskQueue() {
        this.tasks = new ArrayDeque<>();
    }
    
    public void addTask(String task) {
        tasks.offer(task);
        System.out.println("추가: " + task);
    }
    
    public void processNext() {
        String task = tasks.poll();
        if (task != null) {
            System.out.println("처리: " + task);
        } else {
            System.out.println("작업 없음");
        }
    }
    
    public void showQueue() {
        System.out.println("대기 작업: " + tasks);
    }
    
    public static void main(String[] args) {
        TaskQueue queue = new TaskQueue();
        
        queue.addTask("Task 1");
        queue.addTask("Task 2");
        queue.addTask("Task 3");
        
        queue.showQueue();
        
        queue.processNext();
        queue.processNext();
        queue.showQueue();
    }
}
```

### 7.2 브라우저 히스토리

```java
public class BrowserHistory {
    private Deque<String> backStack;
    private Deque<String> forwardStack;
    private String current;
    
    public BrowserHistory(String homepage) {
        this.backStack = new ArrayDeque<>();
        this.forwardStack = new ArrayDeque<>();
        this.current = homepage;
    }
    
    public void visit(String url) {
        if (current != null) {
            backStack.push(current);
        }
        current = url;
        forwardStack.clear();
        System.out.println("방문: " + url);
    }
    
    public void back() {
        if (backStack.isEmpty()) {
            System.out.println("뒤로 갈 페이지 없음");
            return;
        }
        
        forwardStack.push(current);
        current = backStack.pop();
        System.out.println("뒤로: " + current);
    }
    
    public void forward() {
        if (forwardStack.isEmpty()) {
            System.out.println("앞으로 갈 페이지 없음");
            return;
        }
        
        backStack.push(current);
        current = forwardStack.pop();
        System.out.println("앞으로: " + current);
    }
    
    public static void main(String[] args) {
        BrowserHistory history = new BrowserHistory("home.com");
        
        history.visit("page1.com");
        history.visit("page2.com");
        history.visit("page3.com");
        
        history.back();
        history.back();
        history.forward();
    }
}
```

### 7.3 슬라이딩 윈도우

```java
public class SlidingWindow {
    public static List<Integer> maxInWindow(int[] nums, int k) {
        List<Integer> result = new ArrayList<>();
        Deque<Integer> deque = new ArrayDeque<>();
        
        for (int i = 0; i < nums.length; i++) {
            // 범위 벗어난 인덱스 제거
            if (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
                deque.pollFirst();
            }
            
            // 현재 값보다 작은 값들 제거
            while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
                deque.pollLast();
            }
            
            deque.offerLast(i);
            
            // 윈도우 완성 시 최대값 추가
            if (i >= k - 1) {
                result.add(nums[deque.peekFirst()]);
            }
        }
        
        return result;
    }
    
    public static void main(String[] args) {
        int[] nums = {1, 3, -1, -3, 5, 3, 6, 7};
        int k = 3;
        
        List<Integer> result = maxInWindow(nums, k);
        System.out.println("원본: " + Arrays.toString(nums));
        System.out.println("윈도우 크기: " + k);
        System.out.println("각 윈도우 최대값: " + result);
        // [3, 3, 5, 5, 6, 7]
    }
}
```

---

## 8. 실전 연습 문제

### 문제 1: 괄호 검증

```java
// 괄호가 올바른지 검증
public class Problem1 {
    public static boolean isValid(String s) {
        // (), [], {} 검증
        return false;
    }
    
    public static void main(String[] args) {
        System.out.println(isValid("()"));        // true
        System.out.println(isValid("()[]{}"));    // true
        System.out.println(isValid("(]"));        // false
        System.out.println(isValid("([)]"));      // false
        System.out.println(isValid("{[]}"));      // true
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        
        for (char c : s.toCharArray()) {
            if (c == '(' || c == '[' || c == '{') {
                stack.push(c);
            } else {
                if (stack.isEmpty()) return false;
                
                char top = stack.pop();
                if (c == ')' && top != '(') return false;
                if (c == ']' && top != '[') return false;
                if (c == '}' && top != '{') return false;
            }
        }
        
        return stack.isEmpty();
    }
}
```
</details>

---

### 문제 2: 최근 K개 요소

```java
// 최근 K개 요소만 유지하는 큐
public class Problem2 {
    static class RecentQueue {
        private Deque<Integer> queue;
        private int k;
        
        public RecentQueue(int k) {
            // 구현
        }
        
        public void add(int value) {
            // K개 유지
        }
        
        public List<Integer> getRecent() {
            // 최근 순서대로 반환
            return null;
        }
    }
    
    public static void main(String[] args) {
        RecentQueue queue = new RecentQueue(3);
        
        queue.add(1);
        queue.add(2);
        queue.add(3);
        System.out.println(queue.getRecent());  // [1, 2, 3]
        
        queue.add(4);
        System.out.println(queue.getRecent());  // [2, 3, 4]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    static class RecentQueue {
        private Deque<Integer> queue;
        private int k;
        
        public RecentQueue(int k) {
            this.queue = new ArrayDeque<>();
            this.k = k;
        }
        
        public void add(int value) {
            if (queue.size() >= k) {
                queue.pollFirst();  // 가장 오래된 것 제거
            }
            queue.offerLast(value);
        }
        
        public List<Integer> getRecent() {
            return new ArrayList<>(queue);
        }
    }
}
```
</details>

---

### 문제 3: 주식 가격 문제

```java
// 각 날짜별로 며칠 후에 더 높은 가격이 나오는지
public class Problem3 {
    public static int[] solution(int[] prices) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] prices = {73, 74, 75, 71, 69, 72, 76, 73};
        int[] result = solution(prices);
        
        System.out.println("가격: " + Arrays.toString(prices));
        System.out.println("대기 일수: " + Arrays.toString(result));
        // [1, 1, 4, 2, 1, 1, 0, 0]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int[] solution(int[] prices) {
        int n = prices.length;
        int[] result = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && prices[stack.peek()] < prices[i]) {
                int idx = stack.pop();
                result[idx] = i - idx;
            }
            stack.push(i);
        }
        
        return result;
    }
}
```
</details>

---

## 📌 핵심 정리

### Queue
```java
// FIFO
Queue<String> queue = new ArrayDeque<>();
queue.offer("A");      // 추가
queue.peek();          // 조회
queue.poll();          // 제거
```

### Deque
```java
// 양방향
Deque<String> deque = new ArrayDeque<>();
deque.offerFirst("A"); // 앞 추가
deque.offerLast("B");  // 뒤 추가
deque.pollFirst();     // 앞 제거
deque.pollLast();      // 뒤 제거
```

### Stack (Deque 사용)
```java
// LIFO
Deque<String> stack = new ArrayDeque<>();
stack.push("A");       // 추가
stack.peek();          // 조회
stack.pop();           // 제거
```

### 메서드 비교
```
          예외X     예외O
추가      offer    add
조회      peek     element
제거      poll     remove
```

### 권장 사항
```
Queue: ArrayDeque ✅
Deque: ArrayDeque ✅
Stack: ArrayDeque ✅ (Stack 클래스 대신)
List: LinkedList
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 12. Map 비교](./Collections-12-MapComparison.md) | [다음: 14. PriorityQueue →](./Collections-14-PriorityQueue.md)**

</div>
