# Concurrency 07. Virtual Threads 완전 정복

> Java 21의 혁신 - Virtual Threads로 동시성 프로그래밍 간소화  
> Java Concurrency API (Java 21+)

---

## 📑 목차

1. [Virtual Threads 소개](#1-virtual-threads-소개)
2. [생성과 실행](#2-생성과-실행)
3. [Platform Threads vs Virtual Threads](#3-platform-threads-vs-virtual-threads)
4. [Structured Concurrency](#4-structured-concurrency)
5. [실전 활용](#5-실전-활용)
6. [성능과 최적화](#6-성능과-최적화)
7. [주의사항](#7-주의사항)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Virtual Threads 소개

### 1.1 왜 Virtual Threads인가?

```java
public class WhyVirtualThreads {
    public static void main(String[] args) {
        System.out.println("=== Platform Threads의 한계 ===\n");
        
        System.out.println("❌ 무거움");
        System.out.println("   - OS 스레드와 1:1 매핑");
        System.out.println("   - 생성 비용 높음 (메모리, 시간)");
        System.out.println("   - 스택 크기: ~1MB\n");
        
        System.out.println("❌ 제한적");
        System.out.println("   - 수천~수만 개 제한");
        System.out.println("   - Context Switching 비용\n");
        
        System.out.println("❌ 블로킹");
        System.out.println("   - I/O 대기 시 스레드 낭비");
        System.out.println("   - 스레드 풀 고갈\n");
        
        
        System.out.println("=== Virtual Threads의 장점 ===\n");
        
        System.out.println("✅ 경량");
        System.out.println("   - JVM이 관리");
        System.out.println("   - 수백만 개 생성 가능");
        System.out.println("   - 메모리 효율적\n");
        
        System.out.println("✅ 블로킹 OK");
        System.out.println("   - I/O 대기 시 다른 작업 실행");
        System.out.println("   - Carrier Thread 효율적 활용\n");
        
        System.out.println("✅ 간단함");
        System.out.println("   - 동기 스타일 코드");
        System.out.println("   - 비동기 성능");
    }
}
```

### 1.2 Virtual Threads 동작 원리

```java
public class VirtualThreadsPrinciple {
    public static void main(String[] args) {
        System.out.println("=== Virtual Threads 구조 ===\n");
        
        System.out.println("Virtual Thread");
        System.out.println("    ↓ mount/unmount");
        System.out.println("Carrier Thread (Platform Thread)");
        System.out.println("    ↓");
        System.out.println("OS Thread\n");
        
        
        System.out.println("=== 동작 방식 ===\n");
        
        System.out.println("1. Virtual Thread 생성");
        System.out.println("   - 매우 저렴 (스택 작음)");
        System.out.println("   - JVM Heap에 저장\n");
        
        System.out.println("2. 실행");
        System.out.println("   - Carrier Thread에 mount");
        System.out.println("   - CPU 작업 수행\n");
        
        System.out.println("3. 블로킹 (I/O, sleep 등)");
        System.out.println("   - Carrier Thread에서 unmount");
        System.out.println("   - Carrier Thread는 다른 Virtual Thread 실행\n");
        
        System.out.println("4. 재개");
        System.out.println("   - I/O 완료 시");
        System.out.println("   - 다시 Carrier Thread에 mount");
    }
}
```

---

## 2. 생성과 실행

### 2.1 기본 생성

```java
public class VirtualThreadCreation {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== Virtual Thread 생성 ===\n");
        
        // 방법 1: Thread.ofVirtual()
        Thread vt1 = Thread.ofVirtual().start(() -> {
            System.out.println("Virtual Thread 1: " + Thread.currentThread());
        });
        
        // 방법 2: Thread.startVirtualThread()
        Thread vt2 = Thread.startVirtualThread(() -> {
            System.out.println("Virtual Thread 2: " + Thread.currentThread());
        });
        
        // 방법 3: Builder 사용
        Thread vt3 = Thread.ofVirtual()
            .name("my-virtual-thread")
            .start(() -> {
                System.out.println("Virtual Thread 3: " + Thread.currentThread());
            });
        
        vt1.join();
        vt2.join();
        vt3.join();
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("isVirtual(): " + vt1.isVirtual());
        System.out.println("isPlatform(): " + !vt1.isVirtual());
    }
}
```

### 2.2 대량 생성

```java
public class MassiveVirtualThreads {
    public static void main(String[] args) throws InterruptedException {
        int count = 100_000;  // 10만 개!
        
        System.out.println("=== " + count + "개 Virtual Threads 생성 ===");
        
        long start = System.currentTimeMillis();
        
        Thread[] threads = new Thread[count];
        for (int i = 0; i < count; i++) {
            final int id = i;
            threads[i] = Thread.startVirtualThread(() -> {
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                if (id % 10000 == 0) {
                    System.out.println("Thread " + id + " 완료");
                }
            });
        }
        
        // 모두 완료 대기
        for (Thread t : threads) {
            t.join();
        }
        
        long time = System.currentTimeMillis() - start;
        System.out.println("\n소요 시간: " + time + "ms");
        System.out.println("→ Platform Thread로는 불가능!");
    }
}
```

### 2.3 ExecutorService 사용

```java
import java.util.concurrent.*;

public class VirtualThreadExecutor {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        // Virtual Thread Executor
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            
            System.out.println("=== Virtual Thread Executor ===\n");
            
            // 작업 제출
            Future<String> future = executor.submit(() -> {
                System.out.println("작업 실행: " + Thread.currentThread());
                Thread.sleep(1000);
                return "완료";
            });
            
            String result = future.get();
            System.out.println("결과: " + result);
            
            
            // 여러 작업
            System.out.println("\n=== 1000개 작업 ===");
            for (int i = 0; i < 1000; i++) {
                final int id = i;
                executor.submit(() -> {
                    Thread.sleep(100);
                    if (id % 100 == 0) {
                        System.out.println("작업 " + id + " 완료");
                    }
                });
            }
            
        }  // try-with-resources로 자동 종료
        
        System.out.println("\nExecutor 종료");
    }
}
```

---

## 3. Platform Threads vs Virtual Threads

### 3.1 성능 비교

```java
import java.util.concurrent.*;

public class PerformanceComparison {
    static void platformThreadTest(int count) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(200);
        
        long start = System.currentTimeMillis();
        
        for (int i = 0; i < count; i++) {
            executor.submit(() -> {
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.MINUTES);
        
        long time = System.currentTimeMillis() - start;
        System.out.println("Platform Threads (" + count + "): " + time + "ms");
    }
    
    static void virtualThreadTest(int count) throws InterruptedException {
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            
            long start = System.currentTimeMillis();
            
            for (int i = 0; i < count; i++) {
                executor.submit(() -> {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                });
            }
            
            executor.shutdown();
            executor.awaitTermination(1, TimeUnit.MINUTES);
            
            long time = System.currentTimeMillis() - start;
            System.out.println("Virtual Threads (" + count + "): " + time + "ms");
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        int[] counts = {1000, 10000};
        
        for (int count : counts) {
            System.out.println("=== " + count + "개 작업 ===");
            platformThreadTest(count);
            virtualThreadTest(count);
            System.out.println();
        }
    }
}
```

### 3.2 메모리 사용량

```java
public class MemoryComparison {
    public static void main(String[] args) throws InterruptedException {
        Runtime runtime = Runtime.getRuntime();
        
        // Platform Threads
        System.out.println("=== Platform Threads ===");
        long before1 = runtime.totalMemory() - runtime.freeMemory();
        
        Thread[] platformThreads = new Thread[1000];
        for (int i = 0; i < platformThreads.length; i++) {
            platformThreads[i] = new Thread(() -> {
                try {
                    Thread.sleep(10000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
            platformThreads[i].start();
        }
        
        Thread.sleep(1000);
        long after1 = runtime.totalMemory() - runtime.freeMemory();
        System.out.println("메모리 증가: " + (after1 - before1) / 1024 / 1024 + "MB");
        
        for (Thread t : platformThreads) {
            t.interrupt();
        }
        
        
        // Virtual Threads
        System.out.println("\n=== Virtual Threads ===");
        Thread.sleep(2000);
        System.gc();
        Thread.sleep(1000);
        
        long before2 = runtime.totalMemory() - runtime.freeMemory();
        
        Thread[] virtualThreads = new Thread[1000];
        for (int i = 0; i < virtualThreads.length; i++) {
            virtualThreads[i] = Thread.startVirtualThread(() -> {
                try {
                    Thread.sleep(10000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        Thread.sleep(1000);
        long after2 = runtime.totalMemory() - runtime.freeMemory();
        System.out.println("메모리 증가: " + (after2 - before2) / 1024 / 1024 + "MB");
        
        System.out.println("\n→ Virtual Thread가 훨씬 경량!");
    }
}
```

---

## 4. Structured Concurrency

### 4.1 기본 개념

```java
import java.util.concurrent.*;

public class StructuredConcurrencyBasic {
    public static void main(String[] args) {
        System.out.println("=== Structured Concurrency ===\n");
        
        System.out.println("📌 개념");
        System.out.println("- 작업의 생명주기 명확히");
        System.out.println("- 부모-자식 관계");
        System.out.println("- 예외 전파");
        System.out.println("- 리소스 누수 방지\n");
        
        System.out.println("📌 장점");
        System.out.println("1. 가독성");
        System.out.println("   - 작업 범위 명확");
        System.out.println("2. 안정성");
        System.out.println("   - 자동 정리");
        System.out.println("3. 취소 전파");
        System.out.println("   - 부모 취소 → 자식도 취소");
    }
}
```

### 4.2 StructuredTaskScope 사용

```java
import java.util.concurrent.*;

public class StructuredTaskScopeExample {
    static String fetchUser() throws InterruptedException {
        Thread.sleep(1000);
        return "User Data";
    }
    
    static String fetchOrders() throws InterruptedException {
        Thread.sleep(800);
        return "Order Data";
    }
    
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        System.out.println("=== StructuredTaskScope ===\n");
        
        // ShutdownOnFailure - 하나라도 실패 시 모두 취소
        try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
            
            // 작업 실행
            StructuredTaskScope.Subtask<String> user = scope.fork(() -> fetchUser());
            StructuredTaskScope.Subtask<String> orders = scope.fork(() -> fetchOrders());
            
            // 모두 완료 또는 실패 대기
            scope.join();
            scope.throwIfFailed();
            
            // 결과
            System.out.println("User: " + user.get());
            System.out.println("Orders: " + orders.get());
            
        }  // 자동 정리
        
        System.out.println("\n작업 완료");
    }
}
```

### 4.3 ShutdownOnSuccess

```java
import java.util.concurrent.*;

public class ShutdownOnSuccessExample {
    static String fetchFromServer1() throws InterruptedException {
        Thread.sleep(2000);
        return "Server 1 Response";
    }
    
    static String fetchFromServer2() throws InterruptedException {
        Thread.sleep(500);
        return "Server 2 Response";
    }
    
    static String fetchFromServer3() throws InterruptedException {
        Thread.sleep(1500);
        return "Server 3 Response";
    }
    
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        System.out.println("=== ShutdownOnSuccess ===");
        System.out.println("가장 빠른 응답 사용\n");
        
        // ShutdownOnSuccess - 하나만 성공해도 나머지 취소
        try (var scope = new StructuredTaskScope.ShutdownOnSuccess<String>()) {
            
            scope.fork(() -> fetchFromServer1());
            scope.fork(() -> fetchFromServer2());
            scope.fork(() -> fetchFromServer3());
            
            scope.join();
            
            String result = scope.result();
            System.out.println("결과: " + result);
            System.out.println("→ 가장 빠른 서버의 응답");
            
        }
    }
}
```

---

## 5. 실전 활용

### 5.1 웹 서버 시뮬레이션

```java
import java.util.concurrent.*;

public class WebServerSimulation {
    static void handleRequest(int requestId) {
        System.out.println("요청 " + requestId + " 처리 시작: " + Thread.currentThread());
        
        try {
            // I/O 작업 시뮬레이션
            Thread.sleep(100);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        System.out.println("요청 " + requestId + " 완료");
    }
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== Virtual Thread 웹 서버 ===\n");
        
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            
            // 10,000개 동시 요청 처리
            for (int i = 1; i <= 10000; i++) {
                final int requestId = i;
                executor.submit(() -> handleRequest(requestId));
            }
            
            executor.shutdown();
            executor.awaitTermination(1, TimeUnit.MINUTES);
        }
        
        System.out.println("\n모든 요청 처리 완료");
        System.out.println("→ Platform Thread로는 불가능!");
    }
}
```

### 5.2 데이터베이스 연결

```java
import java.util.concurrent.*;
import java.util.*;

public class DatabaseConnectionPool {
    static class Connection {
        int id;
        
        Connection(int id) {
            this.id = id;
        }
        
        String query(String sql) throws InterruptedException {
            Thread.sleep(100);  // DB 쿼리 시뮬레이션
            return "Result-" + id;
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== Virtual Thread DB Pool ===\n");
        
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            
            List<Future<String>> futures = new ArrayList<>();
            
            // 1000개 동시 쿼리
            for (int i = 1; i <= 1000; i++) {
                final int id = i;
                Future<String> future = executor.submit(() -> {
                    Connection conn = new Connection(id);
                    return conn.query("SELECT * FROM users");
                });
                futures.add(future);
            }
            
            // 결과 수집
            int count = 0;
            for (Future<String> future : futures) {
                try {
                    future.get();
                    count++;
                } catch (ExecutionException e) {
                    e.printStackTrace();
                }
            }
            
            System.out.println("처리된 쿼리: " + count);
        }
        
        System.out.println("\n→ 연결 풀 크기 걱정 없음!");
    }
}
```

---

## 6. 성능과 최적화

### 6.1 CPU vs I/O 작업

```java
public class CPUvsIO {
    public static void main(String[] args) {
        System.out.println("=== Virtual Thread 적합성 ===\n");
        
        System.out.println("✅ I/O 작업 (매우 적합)");
        System.out.println("- 파일 I/O");
        System.out.println("- 네트워크 I/O");
        System.out.println("- 데이터베이스 쿼리");
        System.out.println("- REST API 호출");
        System.out.println("→ 블로킹 시간 동안 다른 작업 실행\n");
        
        System.out.println("⚠️ CPU 집약적 (부적합)");
        System.out.println("- 복잡한 계산");
        System.out.println("- 암호화/복호화");
        System.out.println("- 이미지 처리");
        System.out.println("→ Carrier Thread 독점, 이점 없음\n");
        
        System.out.println("📌 권장");
        System.out.println("I/O 많은 작업:  Virtual Thread");
        System.out.println("CPU 많은 작업:  Platform Thread / ForkJoinPool");
    }
}
```

### 6.2 Pinning 문제

```java
public class PinningIssue {
    public static void main(String[] args) {
        System.out.println("=== Pinning 문제 ===\n");
        
        System.out.println("❌ Pinning 발생 시");
        System.out.println("- synchronized 블록에서 블로킹");
        System.out.println("- JNI 호출");
        System.out.println("→ Virtual Thread가 Carrier Thread에 고정");
        System.out.println("→ 다른 Virtual Thread 실행 못함\n");
        
        System.out.println("✅ 해결 방법");
        System.out.println("1. ReentrantLock 사용");
        System.out.println("   synchronized → ReentrantLock");
        System.out.println("2. 블로킹 코드 최소화");
        System.out.println("   synchronized 블록 크기 최소화");
        
        
        // 나쁜 예
        System.out.println("\n=== 나쁜 예 ===");
        Object lock = new Object();
        Thread.startVirtualThread(() -> {
            synchronized(lock) {
                try {
                    Thread.sleep(1000);  // Pinning!
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        
        // 좋은 예
        System.out.println("\n=== 좋은 예 ===");
        java.util.concurrent.locks.ReentrantLock reentrantLock = 
            new java.util.concurrent.locks.ReentrantLock();
        
        Thread.startVirtualThread(() -> {
            reentrantLock.lock();
            try {
                Thread.sleep(1000);  // OK!
            } catch (InterruptedException e) {
                e.printStackTrace();
            } finally {
                reentrantLock.unlock();
            }
        });
    }
}
```

---

## 7. 주의사항

### 7.1 ThreadLocal 사용

```java
public class ThreadLocalCaution {
    static ThreadLocal<String> threadLocal = new ThreadLocal<>();
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== ThreadLocal 주의 ===\n");
        
        System.out.println("⚠️ 문제점");
        System.out.println("- Virtual Thread는 짧은 생명주기");
        System.out.println("- ThreadLocal이 많으면 메모리 낭비");
        System.out.println("- 캐싱 목적으로 부적합\n");
        
        System.out.println("✅ 대안");
        System.out.println("- Context 객체 전달");
        System.out.println("- Scoped Values (Java 21)");
        
        
        // 수백만 개 생성 시
        for (int i = 0; i < 1000000; i++) {
            Thread.startVirtualThread(() -> {
                threadLocal.set("Data");  // 메모리 낭비!
                // 작업
                threadLocal.remove();
            });
        }
        
        System.out.println("\n→ ThreadLocal 사용 최소화 권장");
    }
}
```

### 7.2 기존 코드 마이그레이션

```java
public class MigrationGuide {
    public static void main(String[] args) {
        System.out.println("=== 마이그레이션 가이드 ===\n");
        
        System.out.println("✅ 간단한 경우");
        System.out.println("Before:");
        System.out.println("  Executors.newFixedThreadPool(200)");
        System.out.println("After:");
        System.out.println("  Executors.newVirtualThreadPerTaskExecutor()\n");
        
        System.out.println("⚠️ 주의 필요");
        System.out.println("1. synchronized 많은 코드");
        System.out.println("   → ReentrantLock으로 변경");
        System.out.println("2. ThreadLocal 많은 코드");
        System.out.println("   → Context 전달로 변경");
        System.out.println("3. CPU 집약적 작업");
        System.out.println("   → Platform Thread 유지\n");
        
        System.out.println("📌 테스트");
        System.out.println("- 부하 테스트 필수");
        System.out.println("- 성능 모니터링");
        System.out.println("- Pinning 확인");
    }
}
```

---

## 8. 실전 예제

### 8.1 크롤러

```java
import java.util.concurrent.*;
import java.util.*;

public class WebCrawler {
    static String fetchPage(String url) {
        try {
            Thread.sleep((long) (Math.random() * 1000));
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        return "Content of " + url;
    }
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== Virtual Thread 크롤러 ===\n");
        
        List<String> urls = new ArrayList<>();
        for (int i = 1; i <= 10000; i++) {
            urls.add("http://example.com/page" + i);
        }
        
        long start = System.currentTimeMillis();
        
        try (ExecutorService executor = Executors.newVirtualThreadPerTaskExecutor()) {
            
            List<Future<String>> futures = new ArrayList<>();
            
            for (String url : urls) {
                Future<String> future = executor.submit(() -> fetchPage(url));
                futures.add(future);
            }
            
            int count = 0;
            for (Future<String> future : futures) {
                try {
                    future.get();
                    count++;
                } catch (ExecutionException e) {
                    e.printStackTrace();
                }
            }
            
            System.out.println("크롤링 완료: " + count + "페이지");
        }
        
        long time = System.currentTimeMillis() - start;
        System.out.println("소요 시간: " + time + "ms");
    }
}
```

### 8.2 마이크로서비스 통신

```java
import java.util.concurrent.*;

public class MicroservicesCommunication {
    static String callUserService(int userId) throws InterruptedException {
        Thread.sleep(100);
        return "User-" + userId;
    }
    
    static String callOrderService(int userId) throws InterruptedException {
        Thread.sleep(150);
        return "Orders-" + userId;
    }
    
    static String callPaymentService(int userId) throws InterruptedException {
        Thread.sleep(120);
        return "Payments-" + userId;
    }
    
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        System.out.println("=== 마이크로서비스 통합 ===\n");
        
        int userId = 123;
        
        // 병렬 호출
        try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
            
            var userTask = scope.fork(() -> callUserService(userId));
            var orderTask = scope.fork(() -> callOrderService(userId));
            var paymentTask = scope.fork(() -> callPaymentService(userId));
            
            scope.join();
            scope.throwIfFailed();
            
            System.out.println("User: " + userTask.get());
            System.out.println("Orders: " + orderTask.get());
            System.out.println("Payments: " + paymentTask.get());
        }
        
        System.out.println("\n→ 3개 서비스 병렬 호출");
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: Virtual Thread 생성

```java
// 100개 Virtual Thread로 카운터 증가
public class Problem1 {
    static int counter = 0;
    
    public static void main(String[] args) {
        // 100개 Virtual Thread 생성
        // 각각 1000번 counter++
        // 구현
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
import java.util.concurrent.atomic.AtomicInteger;

static AtomicInteger counter = new AtomicInteger(0);

public static void main(String[] args) throws InterruptedException {
    Thread[] threads = new Thread[100];
    
    for (int i = 0; i < 100; i++) {
        threads[i] = Thread.startVirtualThread(() -> {
            for (int j = 0; j < 1000; j++) {
                counter.incrementAndGet();
            }
        });
    }
    
    for (Thread t : threads) {
        t.join();
    }
    
    System.out.println("결과: " + counter.get());
}
```
</details>

---

### 문제 2: StructuredTaskScope

```java
// 두 작업을 병렬로 실행하고 결과 합산
public class Problem2 {
    static int compute1() throws InterruptedException {
        Thread.sleep(1000);
        return 10;
    }
    
    static int compute2() throws InterruptedException {
        Thread.sleep(1000);
        return 20;
    }
    
    public static void main(String[] args) {
        // StructuredTaskScope 사용
        // 구현
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void main(String[] args) throws Exception {
    try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
        
        var task1 = scope.fork(() -> compute1());
        var task2 = scope.fork(() -> compute2());
        
        scope.join();
        scope.throwIfFailed();
        
        int sum = task1.get() + task2.get();
        System.out.println("합계: " + sum);
    }
}
```
</details>

---

## 📌 핵심 정리

### Virtual Thread 생성
```java
// 직접 생성
Thread.startVirtualThread(runnable)
Thread.ofVirtual().start(runnable)

// Executor
Executors.newVirtualThreadPerTaskExecutor()
```

### 특징
```
✅ 경량 (수백만 개 가능)
✅ I/O 최적화
✅ 동기 스타일 코드
❌ CPU 집약적 부적합
❌ synchronized 주의 (Pinning)
```

### Structured Concurrency
```java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {
    var task1 = scope.fork(() -> ...);
    var task2 = scope.fork(() -> ...);
    scope.join();
    scope.throwIfFailed();
}
```

### 마이그레이션
```java
// Before
Executors.newFixedThreadPool(200)

// After
Executors.newVirtualThreadPerTaskExecutor()
```

### 주의사항
```
⚠️ synchronized → ReentrantLock
⚠️ ThreadLocal 최소화
⚠️ CPU 작업은 Platform Thread
✅ I/O 많은 작업에 최적
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 06. CompletableFuture](./Concurrency-06-Future.md)**

</div>
