# Concurrency 02. Synchronization 완전 정복

> 동기화의 모든 것 - synchronized, volatile, Lock 마스터하기  
> Java Concurrency API

---

## 📑 목차

1. [동기화 필요성](#1-동기화-필요성)
2. [synchronized 키워드](#2-synchronized-키워드)
3. [volatile 키워드](#3-volatile-키워드)
4. [Lock 인터페이스](#4-lock-인터페이스)
5. [ReentrantLock](#5-reentrantlock)
6. [ReadWriteLock](#6-readwritelock)
7. [동기화 문제들](#7-동기화-문제들)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. 동기화 필요성

### 1.1 경쟁 조건 (Race Condition)

```java
public class RaceConditionExample {
    static class Counter {
        private int count = 0;
        
        public void increment() {
            count++;  // 원자적이지 않음!
            // 실제로는: 1. count 읽기, 2. +1, 3. count 쓰기
        }
        
        public int getCount() {
            return count;
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        // 1000개 스레드가 각각 1000번씩 증가
        Thread[] threads = new Thread[1000];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    counter.increment();
                }
            });
            threads[i].start();
        }
        
        // 모든 스레드 완료 대기
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("예상 결과: 1000000");
        System.out.println("실제 결과: " + counter.getCount());
        System.out.println("→ 결과가 다름! (경쟁 조건 발생)");
        
        
        System.out.println("\n=== 경쟁 조건이란? ===");
        System.out.println("여러 스레드가 공유 자원에 동시 접근");
        System.out.println("실행 순서에 따라 결과가 달라짐");
        System.out.println("예측 불가능한 결과");
    }
}
```

### 1.2 임계 영역 (Critical Section)

```java
public class CriticalSection {
    public static void main(String[] args) {
        System.out.println("=== 임계 영역 ===\n");
        
        System.out.println("📌 정의");
        System.out.println("- 공유 자원에 접근하는 코드 영역");
        System.out.println("- 한 번에 하나의 스레드만 실행 가능\n");
        
        System.out.println("📌 필요 조건");
        System.out.println("1. 상호 배제 (Mutual Exclusion)");
        System.out.println("   - 한 스레드만 진입");
        System.out.println("2. 진행 (Progress)");
        System.out.println("   - 진입 가능한 스레드 선택");
        System.out.println("3. 한정 대기 (Bounded Waiting)");
        System.out.println("   - 무한 대기 방지\n");
        
        System.out.println("📌 구현 방법");
        System.out.println("- synchronized");
        System.out.println("- Lock");
        System.out.println("- Atomic 변수");
    }
}
```

### 1.3 가시성 문제

```java
public class VisibilityProblem {
    static boolean flag = false;
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 가시성 문제 ===\n");
        
        Thread writer = new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            flag = true;
            System.out.println("flag를 true로 변경");
        });
        
        Thread reader = new Thread(() -> {
            while (!flag) {
                // flag가 true가 될 때까지 대기
                // CPU 캐시 때문에 변경을 못 볼 수도 있음!
            }
            System.out.println("flag가 true임을 확인");
        });
        
        reader.start();
        writer.start();
        
        reader.join(3000);
        if (reader.isAlive()) {
            System.out.println("reader가 멈추지 않음!");
            System.out.println("→ 가시성 문제 발생");
            reader.interrupt();
        }
        
        
        System.out.println("\n=== 가시성 문제란? ===");
        System.out.println("한 스레드의 변경이 다른 스레드에 보이지 않음");
        System.out.println("CPU 캐시 때문에 발생");
        System.out.println("해결: volatile, synchronized");
    }
}
```

---

## 2. synchronized 키워드

### 2.1 synchronized 메서드

```java
public class SynchronizedMethod {
    static class Counter {
        private int count = 0;
        
        // synchronized 메서드
        public synchronized void increment() {
            count++;
        }
        
        public synchronized void decrement() {
            count--;
        }
        
        public synchronized int getCount() {
            return count;
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread[] threads = new Thread[1000];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    counter.increment();
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("결과: " + counter.getCount());
        System.out.println("→ 정확히 1000000!");
        
        
        System.out.println("\n=== synchronized 메서드 ===");
        System.out.println("- 메서드 전체가 임계 영역");
        System.out.println("- 객체의 모니터 락 사용");
        System.out.println("- this 객체를 잠금");
    }
}
```

### 2.2 synchronized 블록

```java
public class SynchronizedBlock {
    static class BankAccount {
        private int balance = 1000;
        private Object lock = new Object();
        
        public void withdraw(int amount) {
            // 임계 영역만 동기화
            synchronized(lock) {
                if (balance >= amount) {
                    System.out.println("출금 시작: " + amount);
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    balance -= amount;
                    System.out.println("출금 완료, 잔액: " + balance);
                } else {
                    System.out.println("잔액 부족");
                }
            }
        }
        
        public int getBalance() {
            synchronized(lock) {
                return balance;
            }
        }
    }
    
    public static void main(String[] args) {
        BankAccount account = new BankAccount();
        
        // 2개 스레드가 동시에 출금
        Thread t1 = new Thread(() -> account.withdraw(600));
        Thread t2 = new Thread(() -> account.withdraw(600));
        
        t1.start();
        t2.start();
        
        
        System.out.println("\n=== synchronized 블록 ===");
        System.out.println("- 특정 코드만 동기화");
        System.out.println("- 임의 객체를 락으로 사용");
        System.out.println("- 더 세밀한 제어");
    }
}
```

### 2.3 static synchronized

```java
public class StaticSynchronized {
    static class Counter {
        private static int count = 0;
        
        // static synchronized 메서드
        public static synchronized void increment() {
            count++;
        }
        
        // 클래스 락 사용
        public static void increment2() {
            synchronized(Counter.class) {
                count++;
            }
        }
        
        public static synchronized int getCount() {
            return count;
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Thread[] threads = new Thread[1000];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    Counter.increment();
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("결과: " + Counter.getCount());
        
        
        System.out.println("\n=== static synchronized ===");
        System.out.println("- 클래스의 모니터 락 사용");
        System.out.println("- 클래스 레벨 동기화");
        System.out.println("- 모든 인스턴스 공유");
    }
}
```

### 2.4 synchronized 특징과 주의사항

```java
public class SynchronizedFeatures {
    public static void main(String[] args) {
        System.out.println("=== synchronized 특징 ===\n");
        
        System.out.println("✅ 장점");
        System.out.println("- 사용 간단");
        System.out.println("- 자동 락 해제 (예외 발생 시에도)");
        System.out.println("- JVM 최적화\n");
        
        System.out.println("❌ 단점");
        System.out.println("- 타임아웃 불가");
        System.out.println("- 인터럽트 불가");
        System.out.println("- 공정성 보장 없음");
        System.out.println("- 읽기/쓰기 구분 없음\n");
        
        System.out.println("⚠️ 주의사항");
        System.out.println("1. null 객체 잠금 불가 (NPE)");
        System.out.println("2. 같은 락 객체 사용 필수");
        System.out.println("3. 최소 범위만 동기화");
        System.out.println("4. 데드락 주의");
    }
}
```

---

## 3. volatile 키워드

### 3.1 volatile 기본

```java
public class VolatileBasic {
    // volatile 없이
    static boolean flag1 = false;
    
    // volatile 사용
    static volatile boolean flag2 = false;
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== volatile 예제 ===\n");
        
        // volatile 사용
        Thread writer = new Thread(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            flag2 = true;
            System.out.println("flag2를 true로 변경");
        });
        
        Thread reader = new Thread(() -> {
            while (!flag2) {
                // CPU 캐시 대신 메인 메모리 읽기
            }
            System.out.println("flag2가 true임을 확인");
        });
        
        reader.start();
        writer.start();
        
        writer.join();
        reader.join();
        
        
        System.out.println("\n=== volatile 특징 ===");
        System.out.println("- 메인 메모리 직접 읽기/쓰기");
        System.out.println("- 가시성 보장");
        System.out.println("- 원자성 보장 안 함!");
        System.out.println("- 단순 플래그 변수에 적합");
    }
}
```

### 3.2 volatile 활용

```java
public class VolatileExample {
    static class Task implements Runnable {
        private volatile boolean running = true;
        
        public void stop() {
            running = false;
        }
        
        @Override
        public void run() {
            System.out.println("작업 시작");
            while (running) {
                // 작업 수행
            }
            System.out.println("작업 종료");
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Task task = new Task();
        Thread worker = new Thread(task);
        
        worker.start();
        Thread.sleep(1000);
        
        System.out.println("종료 요청");
        task.stop();  // volatile이므로 즉시 반영
        
        worker.join();
        System.out.println("완료");
    }
}
```

### 3.3 volatile vs synchronized

```java
public class VolatileVsSynchronized {
    public static void main(String[] args) {
        System.out.println("=== volatile vs synchronized ===\n");
        
        System.out.println("📌 volatile");
        System.out.println("✅ 가시성 보장");
        System.out.println("❌ 원자성 보장 안 함");
        System.out.println("✅ 블로킹 없음 (성능 우수)");
        System.out.println("✅ 단순 읽기/쓰기");
        System.out.println("사용: boolean 플래그, long/double\n");
        
        System.out.println("📌 synchronized");
        System.out.println("✅ 가시성 보장");
        System.out.println("✅ 원자성 보장");
        System.out.println("❌ 블로킹 발생 (성능 저하)");
        System.out.println("✅ 복합 연산");
        System.out.println("사용: 복잡한 임계 영역\n");
        
        System.out.println("=== 선택 기준 ===");
        System.out.println("volatile:      단순 플래그, 상태 변수");
        System.out.println("synchronized:  count++, 복합 연산");
    }
}
```

---

## 4. Lock 인터페이스

### 4.1 Lock 기본

```java
import java.util.concurrent.locks.*;

public class LockBasic {
    static class Counter {
        private int count = 0;
        private Lock lock = new ReentrantLock();
        
        public void increment() {
            lock.lock();
            try {
                count++;
            } finally {
                lock.unlock();  // 반드시 unlock
            }
        }
        
        public int getCount() {
            lock.lock();
            try {
                return count;
            } finally {
                lock.unlock();
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        Thread[] threads = new Thread[1000];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    counter.increment();
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("결과: " + counter.getCount());
        
        
        System.out.println("\n=== Lock 특징 ===");
        System.out.println("- 명시적 잠금/해제");
        System.out.println("- 타임아웃 가능");
        System.out.println("- 인터럽트 가능");
        System.out.println("- 공정성 설정 가능");
        System.out.println("- finally에서 unlock 필수!");
    }
}
```

### 4.2 tryLock - 타임아웃

```java
import java.util.concurrent.locks.*;

public class TryLockExample {
    static class Resource {
        private Lock lock = new ReentrantLock();
        
        public boolean tryUse() {
            if (lock.tryLock()) {  // 즉시 시도
                try {
                    System.out.println(Thread.currentThread().getName() + 
                        ": 리소스 사용 중");
                    Thread.sleep(1000);
                    return true;
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } finally {
                    lock.unlock();
                }
            } else {
                System.out.println(Thread.currentThread().getName() + 
                    ": 리소스 사용 불가");
            }
            return false;
        }
        
        public boolean tryUseWithTimeout() {
            try {
                if (lock.tryLock(500, java.util.concurrent.TimeUnit.MILLISECONDS)) {
                    try {
                        System.out.println(Thread.currentThread().getName() + 
                            ": 타임아웃 내 획득");
                        return true;
                    } finally {
                        lock.unlock();
                    }
                } else {
                    System.out.println(Thread.currentThread().getName() + 
                        ": 타임아웃 발생");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return false;
        }
    }
    
    public static void main(String[] args) {
        Resource resource = new Resource();
        
        Thread t1 = new Thread(() -> resource.tryUse(), "Thread-1");
        Thread t2 = new Thread(() -> resource.tryUse(), "Thread-2");
        
        t1.start();
        t2.start();
    }
}
```

### 4.3 lockInterruptibly - 인터럽트

```java
import java.util.concurrent.locks.*;

public class LockInterruptiblyExample {
    static class InterruptibleTask implements Runnable {
        private Lock lock = new ReentrantLock();
        
        @Override
        public void run() {
            try {
                System.out.println(Thread.currentThread().getName() + 
                    ": 락 대기 중...");
                lock.lockInterruptibly();  // 인터럽트 가능
                
                try {
                    System.out.println(Thread.currentThread().getName() + 
                        ": 락 획득, 작업 중");
                    Thread.sleep(5000);
                } finally {
                    lock.unlock();
                }
            } catch (InterruptedException e) {
                System.out.println(Thread.currentThread().getName() + 
                    ": 인터럽트 됨");
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        InterruptibleTask task = new InterruptibleTask();
        
        Thread t1 = new Thread(task, "Thread-1");
        Thread t2 = new Thread(task, "Thread-2");
        
        t1.start();
        Thread.sleep(100);
        t2.start();
        
        Thread.sleep(1000);
        System.out.println("Thread-2 인터럽트 요청");
        t2.interrupt();
    }
}
```

---

## 5. ReentrantLock

### 5.1 재진입 가능

```java
import java.util.concurrent.locks.*;

public class ReentrantExample {
    static class RecursiveTask {
        private ReentrantLock lock = new ReentrantLock();
        
        public void outer() {
            lock.lock();
            try {
                System.out.println("outer: 락 획득 횟수 = " + lock.getHoldCount());
                inner();
            } finally {
                lock.unlock();
            }
        }
        
        public void inner() {
            lock.lock();  // 같은 스레드가 다시 락 획득 (재진입)
            try {
                System.out.println("inner: 락 획득 횟수 = " + lock.getHoldCount());
            } finally {
                lock.unlock();
            }
        }
    }
    
    public static void main(String[] args) {
        RecursiveTask task = new RecursiveTask();
        task.outer();
        
        
        System.out.println("\n=== ReentrantLock ===");
        System.out.println("- 같은 스레드의 재진입 허용");
        System.out.println("- 획득 횟수 추적");
        System.out.println("- unlock 횟수 = lock 횟수");
    }
}
```

### 5.2 공정성 (Fairness)

```java
import java.util.concurrent.locks.*;

public class FairnessExample {
    public static void testFairness(boolean fair) throws InterruptedException {
        ReentrantLock lock = new ReentrantLock(fair);
        
        Runnable task = () -> {
            for (int i = 0; i < 2; i++) {
                lock.lock();
                try {
                    System.out.println(Thread.currentThread().getName() + 
                        " 실행");
                } finally {
                    lock.unlock();
                }
            }
        };
        
        Thread t1 = new Thread(task, "Thread-1");
        Thread t2 = new Thread(task, "Thread-2");
        Thread t3 = new Thread(task, "Thread-3");
        
        t1.start();
        t2.start();
        t3.start();
        
        t1.join();
        t2.join();
        t3.join();
    }
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 비공정 모드 ===");
        testFairness(false);
        
        System.out.println("\n=== 공정 모드 ===");
        testFairness(true);
        
        
        System.out.println("\n=== 공정성 ===");
        System.out.println("비공정 (false):");
        System.out.println("  - 대기 순서 무시");
        System.out.println("  - 성능 우수");
        System.out.println("  - 기아 가능");
        System.out.println("\n공정 (true):");
        System.out.println("  - FIFO 순서");
        System.out.println("  - 성능 저하");
        System.out.println("  - 기아 방지");
    }
}
```

### 5.3 Condition 변수

```java
import java.util.concurrent.locks.*;

public class ConditionExample {
    static class BoundedBuffer {
        private Lock lock = new ReentrantLock();
        private Condition notFull = lock.newCondition();
        private Condition notEmpty = lock.newCondition();
        
        private int[] buffer = new int[10];
        private int count = 0;
        private int putIndex = 0;
        private int takeIndex = 0;
        
        public void put(int value) throws InterruptedException {
            lock.lock();
            try {
                while (count == buffer.length) {
                    notFull.await();  // 버퍼 가득 참, 대기
                }
                
                buffer[putIndex] = value;
                putIndex = (putIndex + 1) % buffer.length;
                count++;
                
                System.out.println("생산: " + value + " (버퍼: " + count + ")");
                notEmpty.signal();  // 소비자 깨우기
                
            } finally {
                lock.unlock();
            }
        }
        
        public int take() throws InterruptedException {
            lock.lock();
            try {
                while (count == 0) {
                    notEmpty.await();  // 버퍼 비어있음, 대기
                }
                
                int value = buffer[takeIndex];
                takeIndex = (takeIndex + 1) % buffer.length;
                count--;
                
                System.out.println("소비: " + value + " (버퍼: " + count + ")");
                notFull.signal();  // 생산자 깨우기
                
                return value;
                
            } finally {
                lock.unlock();
            }
        }
    }
    
    public static void main(String[] args) {
        BoundedBuffer buffer = new BoundedBuffer();
        
        // 생산자
        Thread producer = new Thread(() -> {
            try {
                for (int i = 1; i <= 20; i++) {
                    buffer.put(i);
                    Thread.sleep(100);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        // 소비자
        Thread consumer = new Thread(() -> {
            try {
                for (int i = 1; i <= 20; i++) {
                    buffer.take();
                    Thread.sleep(200);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

---

## 6. ReadWriteLock

### 6.1 ReadWriteLock 기본

```java
import java.util.concurrent.locks.*;

public class ReadWriteLockBasic {
    static class SharedData {
        private int value = 0;
        private ReadWriteLock rwLock = new ReentrantReadWriteLock();
        private Lock readLock = rwLock.readLock();
        private Lock writeLock = rwLock.writeLock();
        
        public int read() {
            readLock.lock();
            try {
                System.out.println(Thread.currentThread().getName() + 
                    " 읽기: " + value);
                Thread.sleep(100);
                return value;
            } catch (InterruptedException e) {
                e.printStackTrace();
                return -1;
            } finally {
                readLock.unlock();
            }
        }
        
        public void write(int newValue) {
            writeLock.lock();
            try {
                System.out.println(Thread.currentThread().getName() + 
                    " 쓰기: " + newValue);
                value = newValue;
                Thread.sleep(100);
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                writeLock.unlock();
            }
        }
    }
    
    public static void main(String[] args) {
        SharedData data = new SharedData();
        
        // 여러 읽기 스레드 (동시 실행 가능)
        for (int i = 1; i <= 3; i++) {
            new Thread(() -> data.read(), "Reader-" + i).start();
        }
        
        // 쓰기 스레드 (배타적 실행)
        new Thread(() -> data.write(100), "Writer").start();
        
        
        System.out.println("\n=== ReadWriteLock ===");
        System.out.println("읽기 락: 여러 스레드 동시 허용");
        System.out.println("쓰기 락: 배타적 (다른 읽기/쓰기 차단)");
        System.out.println("읽기 많은 경우 성능 향상");
    }
}
```

### 6.2 캐시 구현

```java
import java.util.concurrent.locks.*;
import java.util.*;

public class CacheWithReadWriteLock {
    static class Cache<K, V> {
        private Map<K, V> map = new HashMap<>();
        private ReadWriteLock rwLock = new ReentrantReadWriteLock();
        
        public V get(K key) {
            rwLock.readLock().lock();
            try {
                System.out.println("캐시 읽기: " + key);
                return map.get(key);
            } finally {
                rwLock.readLock().unlock();
            }
        }
        
        public void put(K key, V value) {
            rwLock.writeLock().lock();
            try {
                System.out.println("캐시 쓰기: " + key + " = " + value);
                map.put(key, value);
            } finally {
                rwLock.writeLock().unlock();
            }
        }
        
        public V computeIfAbsent(K key, java.util.function.Function<K, V> func) {
            // 읽기 락으로 먼저 확인
            rwLock.readLock().lock();
            V value = map.get(key);
            rwLock.readLock().unlock();
            
            if (value != null) {
                return value;
            }
            
            // 없으면 쓰기 락으로 생성
            rwLock.writeLock().lock();
            try {
                // 다시 확인 (다른 스레드가 만들었을 수도)
                value = map.get(key);
                if (value == null) {
                    value = func.apply(key);
                    map.put(key, value);
                }
                return value;
            } finally {
                rwLock.writeLock().unlock();
            }
        }
    }
    
    public static void main(String[] args) {
        Cache<String, String> cache = new Cache<>();
        
        // 캐시 초기화
        cache.put("key1", "value1");
        
        // 여러 스레드가 동시에 읽기
        for (int i = 1; i <= 5; i++) {
            new Thread(() -> {
                String value = cache.get("key1");
                System.out.println("읽은 값: " + value);
            }).start();
        }
    }
}
```

---

## 7. 동기화 문제들

### 7.1 데드락 (Deadlock)

```java
public class DeadlockExample {
    static class Resource {
        String name;
        
        Resource(String name) {
            this.name = name;
        }
    }
    
    public static void main(String[] args) {
        Resource r1 = new Resource("Resource-1");
        Resource r2 = new Resource("Resource-2");
        
        // Thread-1: r1 → r2 순서로 획득
        Thread t1 = new Thread(() -> {
            synchronized(r1) {
                System.out.println("Thread-1: r1 획득");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                
                System.out.println("Thread-1: r2 대기 중...");
                synchronized(r2) {
                    System.out.println("Thread-1: r2 획득");
                }
            }
        });
        
        // Thread-2: r2 → r1 순서로 획득
        Thread t2 = new Thread(() -> {
            synchronized(r2) {
                System.out.println("Thread-2: r2 획득");
                try { Thread.sleep(100); } catch (InterruptedException e) {}
                
                System.out.println("Thread-2: r1 대기 중...");
                synchronized(r1) {
                    System.out.println("Thread-2: r1 획득");
                }
            }
        });
        
        t1.start();
        t2.start();
        
        
        System.out.println("\n=== 데드락 ===");
        System.out.println("두 스레드가 서로의 락을 기다림");
        System.out.println("영원히 진행 불가");
        System.out.println("\n예방:");
        System.out.println("- 락 순서 통일");
        System.out.println("- 타임아웃 사용");
        System.out.println("- tryLock 사용");
    }
}
```

### 7.2 라이브락 (Livelock)

```java
public class LivelockExample {
    static class Person {
        private String name;
        private boolean isPolite = true;
        
        Person(String name) {
            this.name = name;
        }
        
        public void passThrough(Person other) {
            while (isPolite && other.isPolite) {
                System.out.println(name + ": 양보합니다");
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    break;
                }
                // 둘 다 양보해서 진행 안 됨!
            }
            System.out.println(name + ": 통과!");
        }
    }
    
    public static void main(String[] args) {
        Person p1 = new Person("Person-1");
        Person p2 = new Person("Person-2");
        
        new Thread(() -> p1.passThrough(p2)).start();
        new Thread(() -> p2.passThrough(p1)).start();
        
        
        System.out.println("\n=== 라이브락 ===");
        System.out.println("서로 양보하느라 진행 안 됨");
        System.out.println("CPU는 사용하지만 작업 진행 없음");
    }
}
```

### 7.3 기아 (Starvation)

```java
public class StarvationExample {
    public static void main(String[] args) {
        Object lock = new Object();
        
        // 높은 우선순위 스레드들
        for (int i = 0; i < 5; i++) {
            Thread t = new Thread(() -> {
                while (true) {
                    synchronized(lock) {
                        System.out.println("우선순위 높음: 실행 중");
                        try {
                            Thread.sleep(10);
                        } catch (InterruptedException e) {
                            break;
                        }
                    }
                }
            });
            t.setPriority(Thread.MAX_PRIORITY);
            t.start();
        }
        
        // 낮은 우선순위 스레드
        Thread lowPriority = new Thread(() -> {
            synchronized(lock) {
                System.out.println("우선순위 낮음: 실행!");
            }
        });
        lowPriority.setPriority(Thread.MIN_PRIORITY);
        lowPriority.start();
        
        
        System.out.println("\n=== 기아 ===");
        System.out.println("특정 스레드가 계속 실행 기회를 못 얻음");
        System.out.println("\n예방:");
        System.out.println("- 공정한 락 사용");
        System.out.println("- 우선순위 조정");
    }
}
```

---

## 8. 실전 예제

### 8.1 스레드 안전 카운터

```java
import java.util.concurrent.locks.*;

public class ThreadSafeCounter {
    static class Counter {
        private int count = 0;
        private Lock lock = new ReentrantLock();
        
        public void increment() {
            lock.lock();
            try {
                count++;
            } finally {
                lock.unlock();
            }
        }
        
        public void decrement() {
            lock.lock();
            try {
                count--;
            } finally {
                lock.unlock();
            }
        }
        
        public int get() {
            lock.lock();
            try {
                return count;
            } finally {
                lock.unlock();
            }
        }
        
        public void reset() {
            lock.lock();
            try {
                count = 0;
            } finally {
                lock.unlock();
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Counter counter = new Counter();
        
        // 증가 스레드들
        Thread[] incrementers = new Thread[100];
        for (int i = 0; i < incrementers.length; i++) {
            incrementers[i] = new Thread(() -> {
                for (int j = 0; j < 100; j++) {
                    counter.increment();
                }
            });
            incrementers[i].start();
        }
        
        // 감소 스레드들
        Thread[] decrementers = new Thread[100];
        for (int i = 0; i < decrementers.length; i++) {
            decrementers[i] = new Thread(() -> {
                for (int j = 0; j < 50; j++) {
                    counter.decrement();
                }
            });
            decrementers[i].start();
        }
        
        // 모든 스레드 완료 대기
        for (Thread t : incrementers) t.join();
        for (Thread t : decrementers) t.join();
        
        System.out.println("최종 카운트: " + counter.get());
        System.out.println("예상 값: " + (100 * 100 - 100 * 50));
    }
}
```

### 8.2 생산자-소비자 (Lock)

```java
import java.util.concurrent.locks.*;
import java.util.*;

public class ProducerConsumerLock {
    static class BoundedQueue<T> {
        private Queue<T> queue = new LinkedList<>();
        private int capacity;
        private Lock lock = new ReentrantLock();
        private Condition notFull = lock.newCondition();
        private Condition notEmpty = lock.newCondition();
        
        BoundedQueue(int capacity) {
            this.capacity = capacity;
        }
        
        public void put(T item) throws InterruptedException {
            lock.lock();
            try {
                while (queue.size() == capacity) {
                    notFull.await();
                }
                queue.offer(item);
                System.out.println("생산: " + item + " (큐 크기: " + queue.size() + ")");
                notEmpty.signal();
            } finally {
                lock.unlock();
            }
        }
        
        public T take() throws InterruptedException {
            lock.lock();
            try {
                while (queue.isEmpty()) {
                    notEmpty.await();
                }
                T item = queue.poll();
                System.out.println("소비: " + item + " (큐 크기: " + queue.size() + ")");
                notFull.signal();
                return item;
            } finally {
                lock.unlock();
            }
        }
    }
    
    public static void main(String[] args) {
        BoundedQueue<Integer> queue = new BoundedQueue<>(5);
        
        // 생산자
        new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    queue.put(i);
                    Thread.sleep(100);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "Producer").start();
        
        // 소비자
        new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    queue.take();
                    Thread.sleep(300);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "Consumer").start();
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 스레드 안전 Stack

```java
// Stack을 스레드 안전하게 구현
public class Problem1 {
    static class ThreadSafeStack<T> {
        private List<T> list = new ArrayList<>();
        
        public void push(T item) {
            // synchronized로 구현
        }
        
        public T pop() {
            // synchronized로 구현
            return null;
        }
        
        public boolean isEmpty() {
            // synchronized로 구현
            return false;
        }
    }
    
    public static void main(String[] args) {
        ThreadSafeStack<Integer> stack = new ThreadSafeStack<>();
        // 테스트
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public synchronized void push(T item) {
    list.add(item);
}

public synchronized T pop() {
    if (list.isEmpty()) {
        throw new EmptyStackException();
    }
    return list.remove(list.size() - 1);
}

public synchronized boolean isEmpty() {
    return list.isEmpty();
}
```
</details>

---

### 문제 2: 싱글톤 (Thread-Safe)

```java
// 스레드 안전한 싱글톤 구현
public class Problem2 {
    static class Singleton {
        // 구현
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
static class Singleton {
    private static volatile Singleton instance;
    
    private Singleton() {}
    
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```
</details>

---

### 문제 3: 읽기/쓰기 통계

```java
// ReadWriteLock으로 통계 관리
public class Problem3 {
    static class Statistics {
        private int count = 0;
        private int sum = 0;
        
        public void add(int value) {
            // 쓰기 락
        }
        
        public double getAverage() {
            // 읽기 락
            return 0.0;
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
private ReadWriteLock rwLock = new ReentrantReadWriteLock();

public void add(int value) {
    rwLock.writeLock().lock();
    try {
        count++;
        sum += value;
    } finally {
        rwLock.writeLock().unlock();
    }
}

public double getAverage() {
    rwLock.readLock().lock();
    try {
        return count == 0 ? 0 : (double) sum / count;
    } finally {
        rwLock.readLock().unlock();
    }
}
```
</details>

---

## 📌 핵심 정리

### synchronized
```java
// 메서드
public synchronized void method() { }

// 블록
synchronized(lock) { }

// static
public static synchronized void method() { }
```

### volatile
```java
private volatile boolean flag;
// 가시성 보장, 원자성 X
```

### Lock
```java
Lock lock = new ReentrantLock();
lock.lock();
try {
    // 임계 영역
} finally {
    lock.unlock();
}
```

### ReadWriteLock
```java
ReadWriteLock rwLock = new ReentrantReadWriteLock();
Lock readLock = rwLock.readLock();
Lock writeLock = rwLock.writeLock();
```

### 선택 기준
```
단순 플래그:        volatile
복합 연산:          synchronized
세밀한 제어:        Lock
읽기 많음:          ReadWriteLock
```

### 주의사항
```
❌ 데드락: 락 순서 통일
❌ 라이브락: 랜덤 대기
❌ 기아: 공정한 락
✅ finally에서 unlock
✅ 최소 범위만 동기화
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Thread](./Concurrency-01-Thread.md) | [다음: 03. ExecutorService →](./Concurrency-03-Executor.md)**

</div>
