# Concurrency 03. ExecutorService 완전 정복

> 스레드 풀 관리의 핵심 - ExecutorService 마스터하기  
> Java Concurrency API

---

## 📑 목차

1. [ExecutorService 기본](#1-executorservice-기본)
2. [Executors 팩토리](#2-executors-팩토리)
3. [ThreadPoolExecutor](#3-threadpoolexecutor)
4. [작업 제출과 결과](#4-작업-제출과-결과)
5. [Future와 Callable](#5-future와-callable)
6. [ScheduledExecutorService](#6-scheduledexecutorservice)
7. [종료 처리](#7-종료-처리)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. ExecutorService 기본

### 1.1 왜 ExecutorService인가?

```java
import java.util.concurrent.*;

public class WhyExecutorService {
    public static void main(String[] args) {
        System.out.println("=== Thread 직접 생성의 문제점 ===\n");
        
        System.out.println("❌ Thread 직접 생성");
        System.out.println("- 스레드 생성/소멸 비용");
        System.out.println("- 개수 제한 없음 (리소스 고갈)");
        System.out.println("- 관리 어려움");
        System.out.println("- 재사용 불가\n");
        
        System.out.println("✅ ExecutorService 사용");
        System.out.println("- 스레드 풀 (재사용)");
        System.out.println("- 개수 제한");
        System.out.println("- 작업 큐 관리");
        System.out.println("- 라이프사이클 관리");
        System.out.println("- Future 지원\n");
        
        System.out.println("=== 구조 ===");
        System.out.println("작업 제출 → 작업 큐 → 스레드 풀 → 실행");
    }
}
```

### 1.2 간단한 예제

```java
import java.util.concurrent.*;

public class ExecutorServiceBasic {
    public static void main(String[] args) {
        System.out.println("=== ExecutorService 기본 ===\n");
        
        // 스레드 풀 생성 (3개)
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        // 작업 제출
        for (int i = 1; i <= 10; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("작업 " + taskId + " 실행: " + 
                    Thread.currentThread().getName());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        // 종료
        executor.shutdown();
        
        System.out.println("\n10개 작업을 3개 스레드로 처리");
    }
}
```

### 1.3 ExecutorService 계층

```java
public class ExecutorServiceHierarchy {
    public static void main(String[] args) {
        System.out.println("=== 인터페이스 계층 ===\n");
        
        System.out.println("Executor");
        System.out.println("  └─ ExecutorService");
        System.out.println("       ├─ ThreadPoolExecutor");
        System.out.println("       └─ ScheduledExecutorService");
        System.out.println("            └─ ScheduledThreadPoolExecutor\n");
        
        System.out.println("=== 주요 메서드 ===");
        System.out.println("execute():    Runnable 실행");
        System.out.println("submit():     Callable/Runnable 제출");
        System.out.println("shutdown():   새 작업 거부, 기존 완료");
        System.out.println("shutdownNow(): 즉시 종료 시도");
        System.out.println("awaitTermination(): 종료 대기");
    }
}
```

---

## 2. Executors 팩토리

### 2.1 newFixedThreadPool

```java
import java.util.concurrent.*;

public class FixedThreadPoolExample {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== FixedThreadPool ===\n");
        
        // 고정 크기 스레드 풀 (5개)
        ExecutorService executor = Executors.newFixedThreadPool(5);
        
        // 10개 작업 제출
        for (int i = 1; i <= 10; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("작업 " + taskId + ": " + 
                    Thread.currentThread().getName());
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                System.out.println("작업 " + taskId + " 완료");
            });
        }
        
        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.MINUTES);
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("- 고정된 스레드 개수");
        System.out.println("- 무제한 작업 큐");
        System.out.println("- 부하 예측 가능");
        System.out.println("- CPU 집약적 작업에 적합");
    }
}
```

### 2.2 newCachedThreadPool

```java
import java.util.concurrent.*;

public class CachedThreadPoolExample {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== CachedThreadPool ===\n");
        
        // 필요 시 스레드 생성, 60초 후 제거
        ExecutorService executor = Executors.newCachedThreadPool();
        
        // 많은 작업 제출
        for (int i = 1; i <= 100; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("작업 " + taskId + ": " + 
                    Thread.currentThread().getName());
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        Thread.sleep(2000);
        System.out.println("\n모든 작업 완료");
        
        executor.shutdown();
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("- 스레드 개수 무제한");
        System.out.println("- 유휴 스레드 60초 후 제거");
        System.out.println("- 짧은 비동기 작업에 적합");
        System.out.println("- I/O 작업에 적합");
    }
}
```

### 2.3 newSingleThreadExecutor

```java
import java.util.concurrent.*;

public class SingleThreadExecutorExample {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== SingleThreadExecutor ===\n");
        
        // 단일 스레드
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        // 작업 순차 실행
        for (int i = 1; i <= 5; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("작업 " + taskId + " 실행");
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
        }
        
        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.MINUTES);
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("- 단일 작업 스레드");
        System.out.println("- 순차 실행 보장");
        System.out.println("- 이벤트 루프에 적합");
        System.out.println("- 예외 발생 시 새 스레드 생성");
    }
}
```

### 2.4 스레드 풀 비교

```java
import java.util.concurrent.*;

public class ThreadPoolComparison {
    public static void main(String[] args) {
        System.out.println("=== 스레드 풀 비교 ===\n");
        
        System.out.println("📌 FixedThreadPool");
        System.out.println("크기:     고정");
        System.out.println("큐:       무제한");
        System.out.println("적합:     CPU 집약적, 부하 예측 가능\n");
        
        System.out.println("📌 CachedThreadPool");
        System.out.println("크기:     무제한");
        System.out.println("큐:       SynchronousQueue");
        System.out.println("적합:     짧은 비동기, I/O 작업\n");
        
        System.out.println("📌 SingleThreadExecutor");
        System.out.println("크기:     1");
        System.out.println("큐:       무제한");
        System.out.println("적합:     순차 실행, 이벤트 루프\n");
        
        System.out.println("📌 ScheduledThreadPool");
        System.out.println("크기:     고정");
        System.out.println("큐:       DelayedWorkQueue");
        System.out.println("적합:     주기적 작업, 지연 실행");
    }
}
```

---

## 3. ThreadPoolExecutor

### 3.1 직접 생성

```java
import java.util.concurrent.*;

public class ThreadPoolExecutorDirect {
    public static void main(String[] args) {
        System.out.println("=== ThreadPoolExecutor 직접 생성 ===\n");
        
        // 세밀한 설정
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2,                          // corePoolSize
            5,                          // maximumPoolSize
            60L,                        // keepAliveTime
            TimeUnit.SECONDS,           // unit
            new LinkedBlockingQueue<>(10)  // workQueue
        );
        
        // 작업 제출
        for (int i = 1; i <= 20; i++) {
            final int taskId = i;
            try {
                executor.submit(() -> {
                    System.out.println("작업 " + taskId + ": " + 
                        Thread.currentThread().getName());
                    try {
                        Thread.sleep(1000);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                });
            } catch (RejectedExecutionException e) {
                System.out.println("작업 " + taskId + " 거부됨");
            }
        }
        
        executor.shutdown();
        
        
        System.out.println("\n=== 파라미터 ===");
        System.out.println("corePoolSize:      기본 스레드 수");
        System.out.println("maximumPoolSize:   최대 스레드 수");
        System.out.println("keepAliveTime:     유휴 시간");
        System.out.println("workQueue:         작업 큐");
    }
}
```

### 3.2 작업 거부 정책

```java
import java.util.concurrent.*;

public class RejectedExecutionPolicies {
    public static void main(String[] args) {
        System.out.println("=== 거부 정책 ===\n");
        
        // 1. AbortPolicy (기본) - 예외 발생
        ThreadPoolExecutor executor1 = new ThreadPoolExecutor(
            1, 1, 0, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(1),
            new ThreadPoolExecutor.AbortPolicy()
        );
        System.out.println("1. AbortPolicy - 예외 발생");
        
        
        // 2. CallerRunsPolicy - 호출자 스레드에서 실행
        ThreadPoolExecutor executor2 = new ThreadPoolExecutor(
            1, 1, 0, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(1),
            new ThreadPoolExecutor.CallerRunsPolicy()
        );
        System.out.println("2. CallerRunsPolicy - 호출자가 실행");
        
        
        // 3. DiscardPolicy - 조용히 무시
        ThreadPoolExecutor executor3 = new ThreadPoolExecutor(
            1, 1, 0, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(1),
            new ThreadPoolExecutor.DiscardPolicy()
        );
        System.out.println("3. DiscardPolicy - 무시");
        
        
        // 4. DiscardOldestPolicy - 가장 오래된 작업 제거
        ThreadPoolExecutor executor4 = new ThreadPoolExecutor(
            1, 1, 0, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(1),
            new ThreadPoolExecutor.DiscardOldestPolicy()
        );
        System.out.println("4. DiscardOldestPolicy - 가장 오래된 것 제거");
        
        
        executor1.shutdown();
        executor2.shutdown();
        executor3.shutdown();
        executor4.shutdown();
    }
}
```

### 3.3 스레드 풀 모니터링

```java
import java.util.concurrent.*;

public class ThreadPoolMonitoring {
    public static void main(String[] args) throws InterruptedException {
        ThreadPoolExecutor executor = new ThreadPoolExecutor(
            2, 5, 60, TimeUnit.SECONDS,
            new LinkedBlockingQueue<>(10)
        );
        
        // 작업 제출
        for (int i = 1; i <= 15; i++) {
            final int taskId = i;
            executor.submit(() -> {
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            });
            
            // 상태 출력
            System.out.println("=== 작업 " + taskId + " 제출 후 ===");
            System.out.println("활성 스레드:   " + executor.getActiveCount());
            System.out.println("풀 크기:       " + executor.getPoolSize());
            System.out.println("코어 크기:     " + executor.getCorePoolSize());
            System.out.println("최대 크기:     " + executor.getMaximumPoolSize());
            System.out.println("큐 크기:       " + executor.getQueue().size());
            System.out.println("완료 작업:     " + executor.getCompletedTaskCount());
            System.out.println();
            
            Thread.sleep(100);
        }
        
        executor.shutdown();
        executor.awaitTermination(1, TimeUnit.MINUTES);
    }
}
```

---

## 4. 작업 제출과 결과

### 4.1 execute vs submit

```java
import java.util.concurrent.*;

public class ExecuteVsSubmit {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(2);
        
        System.out.println("=== execute ===");
        // execute - Runnable만, 반환값 없음
        executor.execute(() -> {
            System.out.println("execute 실행");
        });
        
        
        System.out.println("\n=== submit ===");
        // submit - Runnable/Callable, Future 반환
        Future<?> future1 = executor.submit(() -> {
            System.out.println("submit (Runnable) 실행");
        });
        
        Future<String> future2 = executor.submit(() -> {
            System.out.println("submit (Callable) 실행");
            return "결과";
        });
        
        try {
            System.out.println("결과: " + future2.get());
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        executor.shutdown();
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("execute:");
        System.out.println("  - Runnable만");
        System.out.println("  - 반환값 없음");
        System.out.println("  - 예외 처리 어려움\n");
        
        System.out.println("submit:");
        System.out.println("  - Runnable/Callable");
        System.out.println("  - Future 반환");
        System.out.println("  - 예외 처리 용이");
    }
}
```

### 4.2 invokeAll

```java
import java.util.concurrent.*;
import java.util.*;

public class InvokeAllExample {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        // 여러 Callable 작업
        List<Callable<Integer>> tasks = new ArrayList<>();
        for (int i = 1; i <= 5; i++) {
            final int taskId = i;
            tasks.add(() -> {
                System.out.println("작업 " + taskId + " 실행");
                Thread.sleep(1000);
                return taskId * 10;
            });
        }
        
        System.out.println("=== invokeAll ===");
        
        // 모든 작업 실행, 완료까지 대기
        List<Future<Integer>> futures = executor.invokeAll(tasks);
        
        System.out.println("\n모든 작업 완료");
        
        // 결과 수집
        for (int i = 0; i < futures.size(); i++) {
            try {
                System.out.println("작업 " + (i + 1) + " 결과: " + 
                    futures.get(i).get());
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
        }
        
        executor.shutdown();
        
        
        System.out.println("\n=== invokeAll 특징 ===");
        System.out.println("- 모든 작업 제출");
        System.out.println("- 모두 완료까지 블로킹");
        System.out.println("- List<Future> 반환");
    }
}
```

### 4.3 invokeAny

```java
import java.util.concurrent.*;
import java.util.*;

public class InvokeAnyExample {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        ExecutorService executor = Executors.newFixedThreadPool(3);
        
        // 여러 Callable 작업
        List<Callable<String>> tasks = new ArrayList<>();
        tasks.add(() -> {
            Thread.sleep(2000);
            return "작업 1 완료";
        });
        tasks.add(() -> {
            Thread.sleep(1000);
            return "작업 2 완료";  // 가장 먼저 완료
        });
        tasks.add(() -> {
            Thread.sleep(3000);
            return "작업 3 완료";
        });
        
        System.out.println("=== invokeAny ===");
        
        // 가장 먼저 완료된 작업 결과 반환
        String result = executor.invokeAny(tasks);
        System.out.println("결과: " + result);
        
        executor.shutdown();
        
        
        System.out.println("\n=== invokeAny 특징 ===");
        System.out.println("- 가장 빠른 하나만 반환");
        System.out.println("- 나머지는 취소");
        System.out.println("- 경쟁 방식");
    }
}
```

---

## 5. Future와 Callable

### 5.1 Callable 기본

```java
import java.util.concurrent.*;

public class CallableBasic {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        // Callable - 반환값 있음, 예외 던질 수 있음
        Callable<Integer> task = () -> {
            System.out.println("계산 중...");
            Thread.sleep(2000);
            return 42;
        };
        
        System.out.println("작업 제출");
        Future<Integer> future = executor.submit(task);
        
        System.out.println("다른 작업 수행 가능");
        
        // 결과 대기
        System.out.println("결과 대기 중...");
        Integer result = future.get();  // 블로킹
        System.out.println("결과: " + result);
        
        executor.shutdown();
        
        
        System.out.println("\n=== Callable vs Runnable ===");
        System.out.println("Runnable: void run()");
        System.out.println("Callable: V call() throws Exception");
    }
}
```

### 5.2 Future 메서드

```java
import java.util.concurrent.*;

public class FutureMethods {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        Future<String> future = executor.submit(() -> {
            Thread.sleep(3000);
            return "완료";
        });
        
        // isDone - 완료 여부
        System.out.println("완료됨? " + future.isDone());
        
        // isCancelled - 취소 여부
        System.out.println("취소됨? " + future.isCancelled());
        
        // get with timeout
        try {
            String result = future.get(1, TimeUnit.SECONDS);
            System.out.println("결과: " + result);
        } catch (TimeoutException e) {
            System.out.println("타임아웃!");
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
        
        // cancel - 취소 시도
        boolean cancelled = future.cancel(true);
        System.out.println("취소 성공? " + cancelled);
        
        executor.shutdown();
        
        
        System.out.println("\n=== Future 메서드 ===");
        System.out.println("get():              결과 대기 (블로킹)");
        System.out.println("get(timeout):       타임아웃 설정");
        System.out.println("isDone():           완료 여부");
        System.out.println("isCancelled():      취소 여부");
        System.out.println("cancel(boolean):    취소 시도");
    }
}
```

### 5.3 예외 처리

```java
import java.util.concurrent.*;

public class FutureExceptionHandling {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        // 예외 발생하는 작업
        Future<Integer> future = executor.submit(() -> {
            System.out.println("계산 시작");
            Thread.sleep(1000);
            if (true) {
                throw new RuntimeException("계산 오류!");
            }
            return 42;
        });
        
        try {
            // get() 시 ExecutionException 발생
            Integer result = future.get();
            System.out.println("결과: " + result);
        } catch (ExecutionException e) {
            System.out.println("작업 실행 중 예외: " + e.getCause().getMessage());
        } catch (InterruptedException e) {
            System.out.println("인터럽트됨");
        }
        
        executor.shutdown();
        
        
        System.out.println("\n=== 예외 처리 ===");
        System.out.println("작업 예외 → ExecutionException");
        System.out.println("getCause()로 원인 확인");
    }
}
```

---

## 6. ScheduledExecutorService

### 6.1 schedule - 지연 실행

```java
import java.util.concurrent.*;

public class ScheduleExample {
    public static void main(String[] args) throws InterruptedException {
        ScheduledExecutorService scheduler = 
            Executors.newScheduledThreadPool(2);
        
        System.out.println("시작: " + System.currentTimeMillis());
        
        // 3초 후 실행
        ScheduledFuture<?> future = scheduler.schedule(() -> {
            System.out.println("3초 후 실행: " + System.currentTimeMillis());
        }, 3, TimeUnit.SECONDS);
        
        // 결과 값 반환
        ScheduledFuture<String> future2 = scheduler.schedule(() -> {
            return "결과";
        }, 2, TimeUnit.SECONDS);
        
        try {
            System.out.println("결과: " + future2.get());
        } catch (ExecutionException e) {
            e.printStackTrace();
        }
        
        Thread.sleep(4000);
        scheduler.shutdown();
    }
}
```

### 6.2 scheduleAtFixedRate - 고정 속도

```java
import java.util.concurrent.*;
import java.time.*;

public class ScheduleAtFixedRateExample {
    public static void main(String[] args) throws InterruptedException {
        ScheduledExecutorService scheduler = 
            Executors.newScheduledThreadPool(1);
        
        System.out.println("=== scheduleAtFixedRate ===");
        System.out.println("시작: " + LocalTime.now());
        
        // 1초 후 시작, 2초마다 실행
        scheduler.scheduleAtFixedRate(() -> {
            System.out.println("실행: " + LocalTime.now());
            try {
                Thread.sleep(500);  // 작업 시간 0.5초
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, 1, 2, TimeUnit.SECONDS);
        
        Thread.sleep(10000);
        scheduler.shutdown();
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("- 고정된 시작 간격");
        System.out.println("- 이전 작업 완료 무관");
        System.out.println("- 작업 시간 > 주기 → 즉시 다음 실행");
    }
}
```

### 6.3 scheduleWithFixedDelay - 고정 지연

```java
import java.util.concurrent.*;
import java.time.*;

public class ScheduleWithFixedDelayExample {
    public static void main(String[] args) throws InterruptedException {
        ScheduledExecutorService scheduler = 
            Executors.newScheduledThreadPool(1);
        
        System.out.println("=== scheduleWithFixedDelay ===");
        System.out.println("시작: " + LocalTime.now());
        
        // 1초 후 시작, 완료 후 2초 대기
        scheduler.scheduleWithFixedDelay(() -> {
            System.out.println("실행: " + LocalTime.now());
            try {
                Thread.sleep(500);  // 작업 시간 0.5초
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("완료: " + LocalTime.now());
        }, 1, 2, TimeUnit.SECONDS);
        
        Thread.sleep(10000);
        scheduler.shutdown();
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("- 이전 작업 완료 후 대기");
        System.out.println("- 고정된 대기 시간");
        System.out.println("- 작업 시간과 무관");
    }
}
```

---

## 7. 종료 처리

### 7.1 정상 종료

```java
import java.util.concurrent.*;

public class GracefulShutdown {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(2);
        
        // 작업 제출
        for (int i = 1; i <= 5; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("작업 " + taskId + " 실행");
                try {
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    System.out.println("작업 " + taskId + " 중단됨");
                    return;
                }
                System.out.println("작업 " + taskId + " 완료");
            });
        }
        
        System.out.println("shutdown 호출");
        executor.shutdown();  // 새 작업 거부, 기존 작업 완료
        
        // 종료 대기
        boolean terminated = executor.awaitTermination(1, TimeUnit.MINUTES);
        
        if (terminated) {
            System.out.println("모든 작업 완료");
        } else {
            System.out.println("타임아웃");
        }
        
        
        System.out.println("\n=== shutdown ===");
        System.out.println("- 새 작업 거부");
        System.out.println("- 대기 중인 작업 실행");
        System.out.println("- 실행 중인 작업 완료");
        System.out.println("- awaitTermination으로 대기");
    }
}
```

### 7.2 강제 종료

```java
import java.util.concurrent.*;
import java.util.*;

public class ForceShutdown {
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(2);
        
        // 작업 제출
        for (int i = 1; i <= 10; i++) {
            final int taskId = i;
            executor.submit(() -> {
                System.out.println("작업 " + taskId + " 실행");
                try {
                    Thread.sleep(5000);
                } catch (InterruptedException e) {
                    System.out.println("작업 " + taskId + " 중단됨");
                    return;
                }
                System.out.println("작업 " + taskId + " 완료");
            });
        }
        
        Thread.sleep(1000);
        
        System.out.println("shutdownNow 호출");
        List<Runnable> notExecuted = executor.shutdownNow();
        
        System.out.println("실행 안 된 작업 수: " + notExecuted.size());
        
        executor.awaitTermination(5, TimeUnit.SECONDS);
        
        
        System.out.println("\n=== shutdownNow ===");
        System.out.println("- 즉시 종료 시도");
        System.out.println("- 실행 중인 작업 인터럽트");
        System.out.println("- 대기 중인 작업 반환");
        System.out.println("- 강제 종료 아님 (협력적)");
    }
}
```

### 7.3 안전한 종료 패턴

```java
import java.util.concurrent.*;

public class SafeShutdownPattern {
    public static void shutdownAndAwait(ExecutorService executor) {
        executor.shutdown();  // 새 작업 거부
        
        try {
            // 1분 대기
            if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                executor.shutdownNow();  // 강제 종료
                
                // 다시 대기
                if (!executor.awaitTermination(60, TimeUnit.SECONDS)) {
                    System.err.println("Executor가 종료되지 않음");
                }
            }
        } catch (InterruptedException e) {
            executor.shutdownNow();
            Thread.currentThread().interrupt();
        }
    }
    
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(2);
        
        // 작업 제출
        for (int i = 1; i <= 5; i++) {
            final int taskId = i;
            executor.submit(() -> {
                try {
                    System.out.println("작업 " + taskId + " 실행");
                    Thread.sleep(2000);
                } catch (InterruptedException e) {
                    System.out.println("작업 " + taskId + " 중단");
                }
            });
        }
        
        // 안전한 종료
        shutdownAndAwait(executor);
        System.out.println("종료 완료");
    }
}
```

---

## 8. 실전 예제

### 8.1 병렬 계산

```java
import java.util.concurrent.*;
import java.util.*;

public class ParallelCalculation {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        // 1부터 1000까지 합 (병렬 계산)
        ExecutorService executor = Executors.newFixedThreadPool(4);
        
        int start = 1;
        int end = 1000;
        int numTasks = 4;
        int rangeSize = (end - start + 1) / numTasks;
        
        List<Callable<Long>> tasks = new ArrayList<>();
        
        for (int i = 0; i < numTasks; i++) {
            final int taskStart = start + i * rangeSize;
            final int taskEnd = (i == numTasks - 1) ? end : taskStart + rangeSize - 1;
            
            tasks.add(() -> {
                long sum = 0;
                for (int j = taskStart; j <= taskEnd; j++) {
                    sum += j;
                }
                System.out.println("범위 " + taskStart + "-" + taskEnd + ": " + sum);
                return sum;
            });
        }
        
        // 모든 작업 실행
        List<Future<Long>> futures = executor.invokeAll(tasks);
        
        // 결과 합산
        long totalSum = 0;
        for (Future<Long> future : futures) {
            totalSum += future.get();
        }
        
        System.out.println("\n총합: " + totalSum);
        System.out.println("정답: " + (1000 * 1001 / 2));
        
        executor.shutdown();
    }
}
```

### 8.2 웹 크롤러

```java
import java.util.concurrent.*;
import java.util.*;

public class WebCrawler {
    static class Page {
        String url;
        
        Page(String url) {
            this.url = url;
        }
        
        String fetch() {
            try {
                Thread.sleep(1000);  // 다운로드 시뮬레이션
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Content of " + url;
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        ExecutorService executor = Executors.newFixedThreadPool(5);
        
        List<String> urls = Arrays.asList(
            "http://example1.com",
            "http://example2.com",
            "http://example3.com",
            "http://example4.com",
            "http://example5.com",
            "http://example6.com",
            "http://example7.com",
            "http://example8.com",
            "http://example9.com",
            "http://example10.com"
        );
        
        System.out.println("크롤링 시작");
        long start = System.currentTimeMillis();
        
        List<Future<String>> futures = new ArrayList<>();
        
        for (String url : urls) {
            Future<String> future = executor.submit(() -> {
                Page page = new Page(url);
                String content = page.fetch();
                System.out.println("다운로드 완료: " + url);
                return content;
            });
            futures.add(future);
        }
        
        // 모든 결과 수집
        for (Future<String> future : futures) {
            try {
                future.get();
            } catch (ExecutionException e) {
                e.printStackTrace();
            }
        }
        
        long end = System.currentTimeMillis();
        System.out.println("\n소요 시간: " + (end - start) + "ms");
        
        executor.shutdown();
    }
}
```

### 8.3 주기적 모니터링

```java
import java.util.concurrent.*;
import java.time.*;

public class PeriodicMonitoring {
    static class SystemMonitor implements Runnable {
        @Override
        public void run() {
            Runtime runtime = Runtime.getRuntime();
            long totalMemory = runtime.totalMemory();
            long freeMemory = runtime.freeMemory();
            long usedMemory = totalMemory - freeMemory;
            
            System.out.println("[" + LocalTime.now() + "] 메모리 사용량: " + 
                (usedMemory / 1024 / 1024) + "MB");
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        ScheduledExecutorService scheduler = 
            Executors.newScheduledThreadPool(1);
        
        System.out.println("시스템 모니터링 시작");
        
        // 5초마다 모니터링
        scheduler.scheduleAtFixedRate(
            new SystemMonitor(),
            0,
            5,
            TimeUnit.SECONDS
        );
        
        // 30초 후 종료
        Thread.sleep(30000);
        scheduler.shutdown();
        
        System.out.println("모니터링 종료");
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 파일 처리

```java
// 여러 파일을 병렬로 처리
public class Problem1 {
    static class FileProcessor implements Callable<Integer> {
        private String filename;
        
        FileProcessor(String filename) {
            this.filename = filename;
        }
        
        @Override
        public Integer call() {
            // 파일 처리 (라인 수 반환)
            return 0;
        }
    }
    
    public static void main(String[] args) {
        String[] files = {"file1.txt", "file2.txt", "file3.txt"};
        // 구현
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static void main(String[] args) throws InterruptedException, ExecutionException {
    ExecutorService executor = Executors.newFixedThreadPool(3);
    
    String[] files = {"file1.txt", "file2.txt", "file3.txt"};
    List<Future<Integer>> futures = new ArrayList<>();
    
    for (String file : files) {
        Future<Integer> future = executor.submit(new FileProcessor(file));
        futures.add(future);
    }
    
    int totalLines = 0;
    for (Future<Integer> future : futures) {
        totalLines += future.get();
    }
    
    System.out.println("총 라인 수: " + totalLines);
    executor.shutdown();
}
```
</details>

---

### 문제 2: 타임아웃

```java
// 5초 안에 완료되지 않으면 취소
public class Problem2 {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        Future<String> future = executor.submit(() -> {
            Thread.sleep(10000);  // 10초 작업
            return "완료";
        });
        
        // 구현 (5초 타임아웃)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
try {
    String result = future.get(5, TimeUnit.SECONDS);
    System.out.println("결과: " + result);
} catch (TimeoutException e) {
    System.out.println("타임아웃!");
    future.cancel(true);
} catch (InterruptedException | ExecutionException e) {
    e.printStackTrace();
} finally {
    executor.shutdown();
}
```
</details>

---

### 문제 3: 배치 처리

```java
// 100개 작업을 10개씩 배치로 처리
public class Problem3 {
    public static void main(String[] args) {
        ExecutorService executor = Executors.newFixedThreadPool(5);
        
        // 100개 작업을 10개씩 배치
        // 구현
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
int totalTasks = 100;
int batchSize = 10;

for (int batch = 0; batch < totalTasks / batchSize; batch++) {
    List<Callable<Integer>> tasks = new ArrayList<>();
    
    for (int i = 0; i < batchSize; i++) {
        final int taskId = batch * batchSize + i;
        tasks.add(() -> {
            System.out.println("작업 " + taskId);
            return taskId;
        });
    }
    
    try {
        List<Future<Integer>> futures = executor.invokeAll(tasks);
        System.out.println("배치 " + batch + " 완료");
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}

executor.shutdown();
```
</details>

---

## 📌 핵심 정리

### 스레드 풀 생성
```java
// 고정 크기
Executors.newFixedThreadPool(n)

// 캐시
Executors.newCachedThreadPool()

// 단일
Executors.newSingleThreadExecutor()

// 스케줄링
Executors.newScheduledThreadPool(n)
```

### 작업 제출
```java
// 반환값 없음
executor.execute(runnable)

// Future 반환
Future<?> future = executor.submit(runnable)
Future<T> future = executor.submit(callable)

// 여러 작업
List<Future<T>> futures = executor.invokeAll(tasks)
T result = executor.invokeAny(tasks)
```

### 종료
```java
executor.shutdown()           // 정상 종료
executor.shutdownNow()        // 강제 종료
executor.awaitTermination()   // 대기
```

### 스케줄링
```java
// 지연 실행
schedule(task, delay, unit)

// 고정 속도
scheduleAtFixedRate(task, initial, period, unit)

// 고정 지연
scheduleWithFixedDelay(task, initial, delay, unit)
```

### Best Practices
```
✅ ExecutorService 사용 (Thread 직접 생성 X)
✅ 적절한 풀 크기 선택
✅ shutdown() 호출
✅ try-finally로 종료 보장
✅ Future 타임아웃 설정
❌ 무제한 작업 큐 주의
❌ 풀 크기 너무 크게 설정 X
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. Synchronization](./Concurrency-02-Sync.md) | [다음: 04. Concurrent Collections →](./Concurrency-04-Concurrent.md)**

</div>
