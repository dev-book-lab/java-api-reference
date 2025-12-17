# Concurrency 05. Atomic Variables 완전 정복

> 락 없는 동기화 - AtomicInteger, CAS 알고리즘 마스터하기  
> Java Concurrency API

---

## 📑 목차

1. [Atomic 변수 개요](#1-atomic-변수-개요)
2. [AtomicInteger](#2-atomicinteger)
3. [AtomicLong](#3-atomiclong)
4. [AtomicBoolean](#4-atomicboolean)
5. [AtomicReference](#5-atomicreference)
6. [CAS 알고리즘](#6-cas-알고리즘)
7. [성능 비교](#7-성능-비교)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Atomic 변수 개요

### 1.1 왜 Atomic 변수인가?

```java
import java.util.concurrent.atomic.*;

public class WhyAtomic {
    // 일반 변수
    static int count1 = 0;
    
    // volatile
    static volatile int count2 = 0;
    
    // AtomicInteger
    static AtomicInteger count3 = new AtomicInteger(0);
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 동시성 문제 ===\n");
        
        System.out.println("❌ 일반 변수 (int)");
        System.out.println("- 가시성 문제");
        System.out.println("- 원자성 문제");
        System.out.println("- count++ 는 3단계 (읽기-수정-쓰기)\n");
        
        System.out.println("⚠️ volatile");
        System.out.println("- 가시성 보장 ✅");
        System.out.println("- 원자성 보장 ❌");
        System.out.println("- count++ 는 여전히 위험\n");
        
        System.out.println("✅ AtomicInteger");
        System.out.println("- 가시성 보장 ✅");
        System.out.println("- 원자성 보장 ✅");
        System.out.println("- 락 없이 동기화 (CAS)");
        System.out.println("- 고성능\n");
        
        System.out.println("=== 성능 ===");
        System.out.println("synchronized > Atomic > volatile > plain");
    }
}
```

### 1.2 Atomic 변수 종류

```java
import java.util.concurrent.atomic.*;

public class AtomicTypes {
    public static void main(String[] args) {
        System.out.println("=== Atomic 변수 종류 ===\n");
        
        System.out.println("📌 기본형");
        System.out.println("AtomicInteger");
        System.out.println("AtomicLong");
        System.out.println("AtomicBoolean\n");
        
        System.out.println("📌 참조형");
        System.out.println("AtomicReference<V>");
        System.out.println("AtomicStampedReference<V>");
        System.out.println("AtomicMarkableReference<V>\n");
        
        System.out.println("📌 배열");
        System.out.println("AtomicIntegerArray");
        System.out.println("AtomicLongArray");
        System.out.println("AtomicReferenceArray<E>\n");
        
        System.out.println("📌 필드 업데이터");
        System.out.println("AtomicIntegerFieldUpdater");
        System.out.println("AtomicLongFieldUpdater");
        System.out.println("AtomicReferenceFieldUpdater");
    }
}
```

---

## 2. AtomicInteger

### 2.1 기본 사용법

```java
import java.util.concurrent.atomic.*;

public class AtomicIntegerBasic {
    public static void main(String[] args) throws InterruptedException {
        AtomicInteger counter = new AtomicInteger(0);
        
        // 여러 스레드가 동시에 증가
        Thread[] threads = new Thread[100];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    counter.incrementAndGet();
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("결과: " + counter.get());
        System.out.println("예상: 100000");
        System.out.println("→ 정확히 일치!");
        
        
        System.out.println("\n=== 생성 방법 ===");
        AtomicInteger ai1 = new AtomicInteger();      // 0
        AtomicInteger ai2 = new AtomicInteger(10);    // 10
        System.out.println("ai1: " + ai1.get());
        System.out.println("ai2: " + ai2.get());
    }
}
```

### 2.2 주요 메서드

```java
import java.util.concurrent.atomic.*;

public class AtomicIntegerMethods {
    public static void main(String[] args) {
        AtomicInteger ai = new AtomicInteger(10);
        
        // get/set
        System.out.println("=== get/set ===");
        System.out.println("get: " + ai.get());
        ai.set(20);
        System.out.println("set(20): " + ai.get());
        
        
        // getAndSet - 설정 후 이전 값 반환
        System.out.println("\n=== getAndSet ===");
        int old = ai.getAndSet(30);
        System.out.println("이전 값: " + old);
        System.out.println("현재 값: " + ai.get());
        
        
        // incrementAndGet / getAndIncrement
        System.out.println("\n=== increment ===");
        System.out.println("incrementAndGet: " + ai.incrementAndGet());  // ++i
        System.out.println("getAndIncrement: " + ai.getAndIncrement());  // i++
        System.out.println("현재 값: " + ai.get());
        
        
        // decrementAndGet / getAndDecrement
        System.out.println("\n=== decrement ===");
        System.out.println("decrementAndGet: " + ai.decrementAndGet());  // --i
        System.out.println("getAndDecrement: " + ai.getAndDecrement());  // i--
        System.out.println("현재 값: " + ai.get());
        
        
        // addAndGet / getAndAdd
        System.out.println("\n=== add ===");
        System.out.println("addAndGet(5): " + ai.addAndGet(5));
        System.out.println("getAndAdd(10): " + ai.getAndAdd(10));
        System.out.println("현재 값: " + ai.get());
    }
}
```

### 2.3 compareAndSet (CAS)

```java
import java.util.concurrent.atomic.*;

public class AtomicIntegerCAS {
    public static void main(String[] args) {
        AtomicInteger ai = new AtomicInteger(10);
        
        System.out.println("=== compareAndSet ===");
        System.out.println("초기값: " + ai.get());
        
        // CAS 성공
        boolean success1 = ai.compareAndSet(10, 20);
        System.out.println("\ncompareAndSet(10, 20): " + success1);
        System.out.println("현재 값: " + ai.get());
        
        // CAS 실패
        boolean success2 = ai.compareAndSet(10, 30);
        System.out.println("\ncompareAndSet(10, 30): " + success2);
        System.out.println("현재 값: " + ai.get());
        
        
        System.out.println("\n=== CAS 동작 ===");
        System.out.println("1. 현재 값 읽기");
        System.out.println("2. 예상 값과 비교");
        System.out.println("3. 일치하면 새 값으로 변경");
        System.out.println("4. 불일치하면 실패 반환");
        
        
        // 실전 예제: 재시도 패턴
        System.out.println("\n=== 재시도 패턴 ===");
        AtomicInteger counter = new AtomicInteger(0);
        
        int expected, newValue;
        do {
            expected = counter.get();
            newValue = expected + 5;
        } while (!counter.compareAndSet(expected, newValue));
        
        System.out.println("결과: " + counter.get());
    }
}
```

### 2.4 updateAndGet / getAndUpdate

```java
import java.util.concurrent.atomic.*;

public class AtomicIntegerUpdate {
    public static void main(String[] args) {
        AtomicInteger ai = new AtomicInteger(10);
        
        // updateAndGet - 함수 적용 후 값 반환
        System.out.println("=== updateAndGet ===");
        int result1 = ai.updateAndGet(x -> x * 2);
        System.out.println("2배: " + result1);
        
        // getAndUpdate - 값 반환 후 함수 적용
        System.out.println("\n=== getAndUpdate ===");
        int result2 = ai.getAndUpdate(x -> x + 10);
        System.out.println("이전 값: " + result2);
        System.out.println("현재 값: " + ai.get());
        
        
        // accumulateAndGet - 두 값으로 계산
        System.out.println("\n=== accumulateAndGet ===");
        int result3 = ai.accumulateAndGet(5, (x, y) -> x + y);
        System.out.println("+5: " + result3);
        
        // getAndAccumulate
        int result4 = ai.getAndAccumulate(10, (x, y) -> x * y);
        System.out.println("이전 값: " + result4);
        System.out.println("*10: " + ai.get());
    }
}
```

---

## 3. AtomicLong

### 3.1 기본 사용법

```java
import java.util.concurrent.atomic.*;

public class AtomicLongBasic {
    public static void main(String[] args) throws InterruptedException {
        AtomicLong counter = new AtomicLong(0);
        
        // 대용량 카운터
        Thread[] threads = new Thread[100];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 10000; j++) {
                    counter.incrementAndGet();
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("결과: " + counter.get());
        System.out.println("예상: 1000000");
        
        
        System.out.println("\n=== AtomicLong vs AtomicInteger ===");
        System.out.println("AtomicInteger: int 범위 (-2^31 ~ 2^31-1)");
        System.out.println("AtomicLong:    long 범위 (-2^63 ~ 2^63-1)");
        System.out.println("사용: 대용량 카운터, ID 생성 등");
    }
}
```

### 3.2 LongAdder (고성능 카운터)

```java
import java.util.concurrent.atomic.*;

public class LongAdderExample {
    public static void main(String[] args) throws InterruptedException {
        LongAdder adder = new LongAdder();
        
        // 매우 높은 경합
        Thread[] threads = new Thread[100];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 10000; j++) {
                    adder.increment();
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("결과: " + adder.sum());
        
        
        System.out.println("\n=== LongAdder ===");
        System.out.println("- 내부적으로 여러 셀 사용");
        System.out.println("- 경합 시 셀 분산");
        System.out.println("- sum()으로 합산");
        System.out.println("- 매우 높은 경합 시 유리");
        System.out.println("- 읽기는 느림 (합산 필요)");
    }
}
```

---

## 4. AtomicBoolean

### 4.1 기본 사용법

```java
import java.util.concurrent.atomic.*;

public class AtomicBooleanBasic {
    public static void main(String[] args) throws InterruptedException {
        AtomicBoolean flag = new AtomicBoolean(false);
        
        // 여러 스레드가 플래그 설정 시도
        Thread[] threads = new Thread[10];
        for (int i = 0; i < threads.length; i++) {
            final int threadId = i;
            threads[i] = new Thread(() -> {
                // compareAndSet으로 한 번만 실행
                if (flag.compareAndSet(false, true)) {
                    System.out.println("스레드 " + threadId + " 성공!");
                } else {
                    System.out.println("스레드 " + threadId + " 실패");
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("\n최종 값: " + flag.get());
        System.out.println("→ 한 스레드만 성공");
    }
}
```

### 4.2 활용 예제

```java
import java.util.concurrent.atomic.*;

public class AtomicBooleanUseCase {
    static class OnceExecutor {
        private AtomicBoolean executed = new AtomicBoolean(false);
        
        public void execute(Runnable task) {
            if (executed.compareAndSet(false, true)) {
                task.run();
            } else {
                System.out.println("이미 실행됨");
            }
        }
    }
    
    static class ShutdownHandler {
        private AtomicBoolean shutdown = new AtomicBoolean(false);
        
        public void shutdown() {
            if (shutdown.compareAndSet(false, true)) {
                System.out.println("종료 중...");
                // 종료 로직
            }
        }
        
        public boolean isShutdown() {
            return shutdown.get();
        }
    }
    
    public static void main(String[] args) {
        // 한 번만 실행
        System.out.println("=== 한 번만 실행 ===");
        OnceExecutor executor = new OnceExecutor();
        executor.execute(() -> System.out.println("첫 실행"));
        executor.execute(() -> System.out.println("두 번째 시도"));
        
        
        // 종료 플래그
        System.out.println("\n=== 종료 플래그 ===");
        ShutdownHandler handler = new ShutdownHandler();
        handler.shutdown();
        handler.shutdown();  // 무시됨
    }
}
```

---

## 5. AtomicReference

### 5.1 기본 사용법

```java
import java.util.concurrent.atomic.*;

public class AtomicReferenceBasic {
    static class User {
        String name;
        int age;
        
        User(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + "세)";
        }
    }
    
    public static void main(String[] args) {
        AtomicReference<User> userRef = new AtomicReference<>(
            new User("Alice", 25)
        );
        
        System.out.println("초기: " + userRef.get());
        
        // set
        userRef.set(new User("Bob", 30));
        System.out.println("set: " + userRef.get());
        
        // compareAndSet
        User expected = userRef.get();
        User newUser = new User("Charlie", 35);
        boolean success = userRef.compareAndSet(expected, newUser);
        System.out.println("\nCAS 성공: " + success);
        System.out.println("현재: " + userRef.get());
        
        
        // getAndSet
        User old = userRef.getAndSet(new User("David", 40));
        System.out.println("\n이전: " + old);
        System.out.println("현재: " + userRef.get());
    }
}
```

### 5.2 불변 객체 업데이트

```java
import java.util.concurrent.atomic.*;

public class AtomicReferenceImmutable {
    static class ImmutablePoint {
        final int x;
        final int y;
        
        ImmutablePoint(int x, int y) {
            this.x = x;
            this.y = y;
        }
        
        ImmutablePoint move(int dx, int dy) {
            return new ImmutablePoint(x + dx, y + dy);
        }
        
        @Override
        public String toString() {
            return "(" + x + ", " + y + ")";
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        AtomicReference<ImmutablePoint> pointRef = 
            new AtomicReference<>(new ImmutablePoint(0, 0));
        
        // 여러 스레드가 동시에 이동
        Thread[] threads = new Thread[10];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 100; j++) {
                    pointRef.updateAndGet(p -> p.move(1, 1));
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("최종 위치: " + pointRef.get());
        System.out.println("예상: (1000, 1000)");
    }
}
```

### 5.3 스택 구현 (락 프리)

```java
import java.util.concurrent.atomic.*;

public class LockFreeStack<E> {
    private static class Node<E> {
        final E item;
        Node<E> next;
        
        Node(E item, Node<E> next) {
            this.item = item;
            this.next = next;
        }
    }
    
    private AtomicReference<Node<E>> top = new AtomicReference<>();
    
    public void push(E item) {
        Node<E> newHead = new Node<>(item, null);
        Node<E> oldHead;
        
        do {
            oldHead = top.get();
            newHead.next = oldHead;
        } while (!top.compareAndSet(oldHead, newHead));
    }
    
    public E pop() {
        Node<E> oldHead;
        Node<E> newHead;
        
        do {
            oldHead = top.get();
            if (oldHead == null) {
                return null;
            }
            newHead = oldHead.next;
        } while (!top.compareAndSet(oldHead, newHead));
        
        return oldHead.item;
    }
    
    public static void main(String[] args) throws InterruptedException {
        LockFreeStack<Integer> stack = new LockFreeStack<>();
        
        // 생산자
        Thread producer = new Thread(() -> {
            for (int i = 1; i <= 10; i++) {
                stack.push(i);
                System.out.println("Push: " + i);
            }
        });
        
        // 소비자
        Thread consumer = new Thread(() -> {
            try {
                Thread.sleep(500);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            for (int i = 1; i <= 10; i++) {
                Integer value = stack.pop();
                System.out.println("Pop: " + value);
            }
        });
        
        producer.start();
        consumer.start();
        
        producer.join();
        consumer.join();
    }
}
```

---

## 6. CAS 알고리즘

### 6.1 CAS 원리

```java
import java.util.concurrent.atomic.*;

public class CASPrinciple {
    public static void main(String[] args) {
        System.out.println("=== CAS (Compare-And-Swap) ===\n");
        
        System.out.println("1. CPU 명령어 (하드웨어 지원)");
        System.out.println("2. 원자적 연산 (나눌 수 없음)");
        System.out.println("3. 락 없는 동기화\n");
        
        System.out.println("=== 의사 코드 ===");
        System.out.println("boolean compareAndSet(expected, newValue) {");
        System.out.println("  if (currentValue == expected) {");
        System.out.println("    currentValue = newValue;");
        System.out.println("    return true;");
        System.out.println("  }");
        System.out.println("  return false;");
        System.out.println("}\n");
        
        System.out.println("=== 재시도 패턴 ===");
        System.out.println("do {");
        System.out.println("  expected = get();");
        System.out.println("  newValue = compute(expected);");
        System.out.println("} while (!compareAndSet(expected, newValue));");
    }
}
```

### 6.2 ABA 문제

```java
import java.util.concurrent.atomic.*;

public class ABAProblem {
    public static void main(String[] args) {
        System.out.println("=== ABA 문제 ===\n");
        
        System.out.println("상황:");
        System.out.println("1. 스레드1: A 값 읽음");
        System.out.println("2. 스레드2: A → B 변경");
        System.out.println("3. 스레드2: B → A 변경");
        System.out.println("4. 스레드1: CAS(A, C) 성공!");
        System.out.println("   → A가 중간에 변했는지 모름\n");
        
        System.out.println("해결:");
        System.out.println("AtomicStampedReference");
        System.out.println("- 값 + 버전 스탬프");
        System.out.println("- 버전도 함께 비교\n");
        
        
        // AtomicStampedReference 사용
        AtomicStampedReference<String> ref = 
            new AtomicStampedReference<>("A", 0);
        
        int[] stampHolder = new int[1];
        String value = ref.get(stampHolder);
        int stamp = stampHolder[0];
        
        System.out.println("값: " + value + ", 스탬프: " + stamp);
        
        // CAS with stamp
        boolean success = ref.compareAndSet(
            "A", "B",     // 값 변경
            0, 1          // 스탬프 변경
        );
        System.out.println("CAS 성공: " + success);
        
        value = ref.get(stampHolder);
        stamp = stampHolder[0];
        System.out.println("값: " + value + ", 스탬프: " + stamp);
    }
}
```

---

## 7. 성능 비교

### 7.1 synchronized vs Atomic

```java
import java.util.concurrent.atomic.*;

public class PerformanceComparison {
    static int syncCounter = 0;
    static AtomicInteger atomicCounter = new AtomicInteger(0);
    
    public static void main(String[] args) throws InterruptedException {
        int threadCount = 100;
        int iterations = 10000;
        
        // synchronized 테스트
        long start = System.currentTimeMillis();
        Thread[] threads1 = new Thread[threadCount];
        for (int i = 0; i < threads1.length; i++) {
            threads1[i] = new Thread(() -> {
                for (int j = 0; j < iterations; j++) {
                    synchronized(PerformanceComparison.class) {
                        syncCounter++;
                    }
                }
            });
            threads1[i].start();
        }
        for (Thread t : threads1) {
            t.join();
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // AtomicInteger 테스트
        start = System.currentTimeMillis();
        Thread[] threads2 = new Thread[threadCount];
        for (int i = 0; i < threads2.length; i++) {
            threads2[i] = new Thread(() -> {
                for (int j = 0; j < iterations; j++) {
                    atomicCounter.incrementAndGet();
                }
            });
            threads2[i].start();
        }
        for (Thread t : threads2) {
            t.join();
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        System.out.println("=== 성능 비교 ===");
        System.out.println("synchronized:    " + time1 + "ms");
        System.out.println("AtomicInteger:   " + time2 + "ms");
        System.out.println("속도 향상:       " + (time1 / (double) time2) + "배");
    }
}
```

---

## 8. 실전 예제

### 8.1 ID 생성기

```java
import java.util.concurrent.atomic.*;

public class IDGenerator {
    private static AtomicLong idGenerator = new AtomicLong(0);
    
    public static long generateID() {
        return idGenerator.incrementAndGet();
    }
    
    public static void main(String[] args) throws InterruptedException {
        Thread[] threads = new Thread[10];
        
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 5; j++) {
                    long id = generateID();
                    System.out.println(Thread.currentThread().getName() + 
                        ": ID-" + id);
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("\n총 생성된 ID: " + idGenerator.get());
    }
}
```

### 8.2 통계 수집

```java
import java.util.concurrent.atomic.*;

public class Statistics {
    private AtomicLong count = new AtomicLong(0);
    private AtomicLong sum = new AtomicLong(0);
    private AtomicLong min = new AtomicLong(Long.MAX_VALUE);
    private AtomicLong max = new AtomicLong(Long.MIN_VALUE);
    
    public void add(long value) {
        count.incrementAndGet();
        sum.addAndGet(value);
        
        // min 업데이트
        long currentMin;
        do {
            currentMin = min.get();
            if (value >= currentMin) break;
        } while (!min.compareAndSet(currentMin, value));
        
        // max 업데이트
        long currentMax;
        do {
            currentMax = max.get();
            if (value <= currentMax) break;
        } while (!max.compareAndSet(currentMax, value));
    }
    
    public double getAverage() {
        long c = count.get();
        return c == 0 ? 0 : sum.get() / (double) c;
    }
    
    public long getMin() {
        return min.get();
    }
    
    public long getMax() {
        return max.get();
    }
    
    public static void main(String[] args) throws InterruptedException {
        Statistics stats = new Statistics();
        
        // 여러 스레드가 값 추가
        Thread[] threads = new Thread[10];
        for (int i = 0; i < threads.length; i++) {
            final int threadId = i;
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 100; j++) {
                    stats.add(threadId * 100 + j);
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("평균: " + stats.getAverage());
        System.out.println("최소: " + stats.getMin());
        System.out.println("최대: " + stats.getMax());
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 스레드 안전 카운터

```java
// AtomicInteger로 카운터 구현
public class Problem1 {
    static class Counter {
        // 구현
        
        public void increment() {
            // 구현
        }
        
        public void decrement() {
            // 구현
        }
        
        public int get() {
            // 구현
            return 0;
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
static class Counter {
    private AtomicInteger count = new AtomicInteger(0);
    
    public void increment() {
        count.incrementAndGet();
    }
    
    public void decrement() {
        count.decrementAndGet();
    }
    
    public int get() {
        return count.get();
    }
}
```
</details>

---

### 문제 2: 최대값 추적

```java
// AtomicInteger로 최대값 추적
public class Problem2 {
    static class MaxTracker {
        // 구현
        
        public void update(int value) {
            // CAS로 최대값 업데이트
        }
        
        public int getMax() {
            // 구현
            return 0;
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
static class MaxTracker {
    private AtomicInteger max = new AtomicInteger(Integer.MIN_VALUE);
    
    public void update(int value) {
        int currentMax;
        do {
            currentMax = max.get();
            if (value <= currentMax) return;
        } while (!max.compareAndSet(currentMax, value));
    }
    
    public int getMax() {
        return max.get();
    }
}
```
</details>

---

### 문제 3: 상태 전환

```java
// AtomicReference로 상태 관리
public class Problem3 {
    enum State { IDLE, RUNNING, STOPPED }
    
    static class StateMachine {
        // 구현
        
        public boolean start() {
            // IDLE → RUNNING
            return false;
        }
        
        public boolean stop() {
            // RUNNING → STOPPED
            return false;
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
static class StateMachine {
    private AtomicReference<State> state = 
        new AtomicReference<>(State.IDLE);
    
    public boolean start() {
        return state.compareAndSet(State.IDLE, State.RUNNING);
    }
    
    public boolean stop() {
        return state.compareAndSet(State.RUNNING, State.STOPPED);
    }
}
```
</details>

---

## 📌 핵심 정리

### 주요 클래스
```java
AtomicInteger      // int 원자 연산
AtomicLong         // long 원자 연산
AtomicBoolean      // boolean 원자 연산
AtomicReference<V> // 참조 원자 연산
LongAdder          // 고성능 카운터
```

### 주요 메서드
```java
get()                      // 값 읽기
set(value)                 // 값 설정
getAndSet(value)           // 값 변경 후 이전 값 반환
incrementAndGet()          // ++i
getAndIncrement()          // i++
compareAndSet(exp, new)    // CAS
updateAndGet(func)         // 함수 적용
```

### CAS 패턴
```java
do {
    expected = get();
    newValue = compute(expected);
} while (!compareAndSet(expected, newValue));
```

### 선택 기준
```
단순 카운터:       AtomicInteger/Long
높은 경합:         LongAdder
플래그:            AtomicBoolean
객체 참조:         AtomicReference
```

### 장점
```
✅ 락 없는 동기화
✅ 고성능
✅ 데드락 없음
✅ 확장성
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 04. Concurrent Collections](./Concurrency-04-Concurrent.md) | [다음: 06. CompletableFuture →](./Concurrency-06-Future.md)**

</div>
