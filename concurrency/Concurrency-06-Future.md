# Concurrency 06. CompletableFuture 완전 정복

> 비동기 프로그래밍의 꽃 - CompletableFuture 마스터하기  
> Java Concurrency API

---

## 📑 목차

1. [CompletableFuture 기본](#1-completablefuture-기본)
2. [비동기 작업 생성](#2-비동기-작업-생성)
3. [작업 연결](#3-작업-연결)
4. [작업 조합](#4-작업-조합)
5. [예외 처리](#5-예외-처리)
6. [비동기 실행](#6-비동기-실행)
7. [타임아웃과 취소](#7-타임아웃과-취소)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. CompletableFuture 기본

### 1.1 Future의 한계

```java
import java.util.concurrent.*;

public class FutureLimitations {
    public static void main(String[] args) throws Exception {
        ExecutorService executor = Executors.newSingleThreadExecutor();
        
        Future<String> future = executor.submit(() -> {
            Thread.sleep(1000);
            return "결과";
        });
        
        System.out.println("=== Future의 한계 ===\n");
        
        System.out.println("❌ 블로킹만 가능");
        System.out.println("   - get()만 가능");
        System.out.println("   - 콜백 불가\n");
        
        System.out.println("❌ 작업 조합 불가");
        System.out.println("   - 여러 Future 조합 어려움");
        System.out.println("   - 순차 실행 복잡\n");
        
        System.out.println("❌ 예외 처리 불편");
        System.out.println("   - ExecutionException 처리 필요\n");
        
        System.out.println("❌ 수동 완료 불가");
        System.out.println("   - 외부에서 완료 못함");
        
        String result = future.get();  // 블로킹
        System.out.println("\n결과: " + result);
        
        executor.shutdown();
    }
}
```

### 1.2 CompletableFuture 소개

```java
import java.util.concurrent.*;

public class CompletableFutureIntro {
    public static void main(String[] args) throws Exception {
        System.out.println("=== CompletableFuture 장점 ===\n");
        
        System.out.println("✅ 비블로킹 콜백");
        System.out.println("   - thenApply, thenAccept 등");
        System.out.println("✅ 작업 조합");
        System.out.println("   - thenCompose, thenCombine");
        System.out.println("✅ 예외 처리");
        System.out.println("   - exceptionally, handle");
        System.out.println("✅ 수동 완료");
        System.out.println("   - complete, completeExceptionally\n");
        
        
        // 간단한 예제
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Hello";
        });
        
        future.thenAccept(result -> {
            System.out.println("결과: " + result);
        });
        
        System.out.println("비동기 작업 시작됨 (블로킹 없음)");
        
        Thread.sleep(2000);  // 완료 대기
    }
}
```

---

## 2. 비동기 작업 생성

### 2.1 runAsync / supplyAsync

```java
import java.util.concurrent.*;

public class AsyncCreation {
    public static void main(String[] args) throws Exception {
        // runAsync - 반환값 없음
        System.out.println("=== runAsync ===");
        CompletableFuture<Void> future1 = CompletableFuture.runAsync(() -> {
            System.out.println("작업 실행: " + Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("작업 완료");
        });
        
        future1.get();  // 완료 대기
        
        
        // supplyAsync - 반환값 있음
        System.out.println("\n=== supplyAsync ===");
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
            System.out.println("계산 중: " + Thread.currentThread().getName());
            try {
                Thread.sleep(1000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "결과값";
        });
        
        String result = future2.get();
        System.out.println("결과: " + result);
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("runAsync:    Runnable, 반환값 없음");
        System.out.println("supplyAsync: Supplier<T>, 반환값 있음");
    }
}
```

### 2.2 completedFuture / failedFuture

```java
import java.util.concurrent.*;

public class ImmediateFuture {
    public static void main(String[] args) {
        // 이미 완료된 Future
        CompletableFuture<String> completed = 
            CompletableFuture.completedFuture("즉시 완료");
        
        System.out.println("완료됨? " + completed.isDone());
        System.out.println("결과: " + completed.join());
        
        
        // 이미 실패한 Future (Java 9+)
        CompletableFuture<String> failed = 
            CompletableFuture.failedFuture(new RuntimeException("오류"));
        
        try {
            failed.join();
        } catch (CompletionException e) {
            System.out.println("\n예외: " + e.getCause().getMessage());
        }
        
        
        System.out.println("\n=== 용도 ===");
        System.out.println("- 테스트");
        System.out.println("- 캐시된 값 반환");
        System.out.println("- 에러 케이스 처리");
    }
}
```

### 2.3 수동 완료

```java
import java.util.concurrent.*;

public class ManualCompletion {
    public static void main(String[] args) throws Exception {
        CompletableFuture<String> future = new CompletableFuture<>();
        
        // 다른 스레드에서 완료
        new Thread(() -> {
            try {
                Thread.sleep(2000);
                future.complete("수동 완료!");
            } catch (InterruptedException e) {
                future.completeExceptionally(e);
            }
        }).start();
        
        System.out.println("완료 대기 중...");
        String result = future.get();
        System.out.println("결과: " + result);
        
        
        // 예외로 완료
        CompletableFuture<String> future2 = new CompletableFuture<>();
        future2.completeExceptionally(new RuntimeException("오류 발생"));
        
        try {
            future2.get();
        } catch (ExecutionException e) {
            System.out.println("예외: " + e.getCause().getMessage());
        }
    }
}
```

---

## 3. 작업 연결

### 3.1 thenApply

```java
import java.util.concurrent.*;

public class ThenApplyExample {
    public static void main(String[] args) throws Exception {
        // thenApply - 변환
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            System.out.println("1. 숫자 계산");
            return 10;
        }).thenApply(n -> {
            System.out.println("2. 2배로: " + n);
            return n * 2;
        }).thenApply(n -> {
            System.out.println("3. 문자열로: " + n);
            return "결과: " + n;
        });
        
        System.out.println("최종: " + future.get());
        
        
        System.out.println("\n=== thenApply ===");
        System.out.println("- 이전 결과를 받아 변환");
        System.out.println("- Function<T, R>");
        System.out.println("- 새로운 값 반환");
    }
}
```

### 3.2 thenAccept / thenRun

```java
import java.util.concurrent.*;

public class ThenAcceptAndRun {
    public static void main(String[] args) throws Exception {
        // thenAccept - 소비
        CompletableFuture<Void> future1 = CompletableFuture.supplyAsync(() -> {
            return "Hello";
        }).thenAccept(result -> {
            System.out.println("결과 처리: " + result);
            // 반환값 없음
        });
        
        future1.get();
        
        
        // thenRun - 실행만
        CompletableFuture<Void> future2 = CompletableFuture.supplyAsync(() -> {
            return "World";
        }).thenRun(() -> {
            System.out.println("후속 작업 실행");
            // 이전 결과 사용 안 함
        });
        
        future2.get();
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("thenApply:   T -> R (변환)");
        System.out.println("thenAccept:  T -> void (소비)");
        System.out.println("thenRun:     void -> void (실행만)");
    }
}
```

### 3.3 thenCompose

```java
import java.util.concurrent.*;

public class ThenComposeExample {
    static CompletableFuture<Integer> getUser(String id) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println("사용자 조회: " + id);
            return 100;  // userId
        });
    }
    
    static CompletableFuture<String> getEmail(int userId) {
        return CompletableFuture.supplyAsync(() -> {
            System.out.println("이메일 조회: " + userId);
            return "user" + userId + "@example.com";
        });
    }
    
    public static void main(String[] args) throws Exception {
        // thenCompose - 평탄화
        CompletableFuture<String> future = getUser("alice")
            .thenCompose(userId -> getEmail(userId));
        
        System.out.println("이메일: " + future.get());
        
        
        System.out.println("\n=== thenCompose ===");
        System.out.println("- 중첩된 CompletableFuture 평탄화");
        System.out.println("- Function<T, CompletableFuture<R>>");
        System.out.println("- Stream의 flatMap과 유사");
    }
}
```

---

## 4. 작업 조합

### 4.1 thenCombine

```java
import java.util.concurrent.*;

public class ThenCombineExample {
    public static void main(String[] args) throws Exception {
        CompletableFuture<Integer> future1 = CompletableFuture.supplyAsync(() -> {
            System.out.println("작업 1 시작");
            try { Thread.sleep(1000); } catch (InterruptedException e) {}
            return 10;
        });
        
        CompletableFuture<Integer> future2 = CompletableFuture.supplyAsync(() -> {
            System.out.println("작업 2 시작");
            try { Thread.sleep(1500); } catch (InterruptedException e) {}
            return 20;
        });
        
        // 두 결과 조합
        CompletableFuture<Integer> combined = future1.thenCombine(future2, (r1, r2) -> {
            System.out.println("결과 조합: " + r1 + " + " + r2);
            return r1 + r2;
        });
        
        System.out.println("합계: " + combined.get());
        
        
        System.out.println("\n=== thenCombine ===");
        System.out.println("- 두 Future 결과 조합");
        System.out.println("- 둘 다 완료되면 실행");
        System.out.println("- BiFunction<T, U, R>");
    }
}
```

### 4.2 allOf / anyOf

```java
import java.util.concurrent.*;
import java.util.*;

public class AllOfAnyOf {
    public static void main(String[] args) throws Exception {
        // 여러 작업
        CompletableFuture<String> future1 = CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(1000); } catch (InterruptedException e) {}
            return "작업 1";
        });
        
        CompletableFuture<String> future2 = CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(500); } catch (InterruptedException e) {}
            return "작업 2";
        });
        
        CompletableFuture<String> future3 = CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(1500); } catch (InterruptedException e) {}
            return "작업 3";
        });
        
        
        // allOf - 모두 완료 대기
        System.out.println("=== allOf ===");
        CompletableFuture<Void> allOf = CompletableFuture.allOf(future1, future2, future3);
        allOf.get();  // 모두 완료까지 대기
        
        System.out.println("모든 작업 완료");
        System.out.println("결과 1: " + future1.get());
        System.out.println("결과 2: " + future2.get());
        System.out.println("결과 3: " + future3.get());
        
        
        // anyOf - 하나라도 완료 시
        System.out.println("\n=== anyOf ===");
        CompletableFuture<Object> anyOf = CompletableFuture.anyOf(
            CompletableFuture.supplyAsync(() -> {
                try { Thread.sleep(1000); } catch (InterruptedException e) {}
                return "느림";
            }),
            CompletableFuture.supplyAsync(() -> {
                try { Thread.sleep(500); } catch (InterruptedException e) {}
                return "빠름";
            })
        );
        
        System.out.println("가장 빠른 결과: " + anyOf.get());
    }
}
```

### 4.3 실전 조합 패턴

```java
import java.util.concurrent.*;
import java.util.*;
import java.util.stream.*;

public class CombinationPatterns {
    static CompletableFuture<Integer> fetchPrice(String product) {
        return CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(1000); } catch (InterruptedException e) {}
            return (int) (Math.random() * 1000);
        });
    }
    
    public static void main(String[] args) throws Exception {
        List<String> products = Arrays.asList("A", "B", "C", "D", "E");
        
        // 병렬로 가격 조회
        List<CompletableFuture<Integer>> futures = products.stream()
            .map(CombinationPatterns::fetchPrice)
            .collect(Collectors.toList());
        
        // 모두 완료 대기
        CompletableFuture<Void> allDone = CompletableFuture.allOf(
            futures.toArray(new CompletableFuture[0])
        );
        
        // 결과 수집
        CompletableFuture<List<Integer>> allPrices = allDone.thenApply(v ->
            futures.stream()
                .map(CompletableFuture::join)
                .collect(Collectors.toList())
        );
        
        List<Integer> prices = allPrices.get();
        System.out.println("가격들: " + prices);
        
        int total = prices.stream().mapToInt(Integer::intValue).sum();
        System.out.println("총합: " + total);
    }
}
```

---

## 5. 예외 처리

### 5.1 exceptionally

```java
import java.util.concurrent.*;

public class ExceptionallyExample {
    public static void main(String[] args) throws Exception {
        // 예외 발생
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            if (Math.random() > 0.5) {
                throw new RuntimeException("오류 발생!");
            }
            return "성공";
        }).exceptionally(ex -> {
            System.out.println("예외 처리: " + ex.getMessage());
            return "기본값";
        });
        
        System.out.println("결과: " + future.get());
        
        
        System.out.println("\n=== exceptionally ===");
        System.out.println("- 예외 발생 시 실행");
        System.out.println("- 기본값 반환");
        System.out.println("- Function<Throwable, T>");
    }
}
```

### 5.2 handle

```java
import java.util.concurrent.*;

public class HandleExample {
    public static void main(String[] args) throws Exception {
        // handle - 성공/실패 모두 처리
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            if (Math.random() > 0.5) {
                throw new RuntimeException("오류!");
            }
            return "성공";
        }).handle((result, ex) -> {
            if (ex != null) {
                System.out.println("예외 처리: " + ex.getMessage());
                return "기본값";
            } else {
                System.out.println("성공: " + result);
                return result.toUpperCase();
            }
        });
        
        System.out.println("결과: " + future.get());
        
        
        System.out.println("\n=== handle vs exceptionally ===");
        System.out.println("exceptionally: 예외만");
        System.out.println("handle:        성공/예외 둘 다");
        System.out.println("handle:        BiFunction<T, Throwable, R>");
    }
}
```

### 5.3 whenComplete

```java
import java.util.concurrent.*;

public class WhenCompleteExample {
    public static void main(String[] args) throws Exception {
        // whenComplete - 완료 시 실행 (결과 변경 안 함)
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            return "결과";
        }).whenComplete((result, ex) -> {
            if (ex != null) {
                System.out.println("실패: " + ex.getMessage());
            } else {
                System.out.println("성공: " + result);
            }
            // 결과는 그대로 전달됨
        });
        
        System.out.println("최종: " + future.get());
        
        
        System.out.println("\n=== whenComplete ===");
        System.out.println("- 성공/실패 관찰만");
        System.out.println("- 결과 변경 안 함");
        System.out.println("- 로깅, 정리 작업에 적합");
    }
}
```

---

## 6. 비동기 실행

### 6.1 Async 변형

```java
import java.util.concurrent.*;

public class AsyncVariants {
    public static void main(String[] args) throws Exception {
        // 동기 버전
        CompletableFuture<String> sync = CompletableFuture.supplyAsync(() -> "Hello")
            .thenApply(s -> {
                System.out.println("thenApply: " + Thread.currentThread().getName());
                return s.toUpperCase();
            });
        
        
        // 비동기 버전
        CompletableFuture<String> async = CompletableFuture.supplyAsync(() -> "Hello")
            .thenApplyAsync(s -> {
                System.out.println("thenApplyAsync: " + Thread.currentThread().getName());
                return s.toUpperCase();
            });
        
        sync.get();
        async.get();
        
        
        System.out.println("\n=== Async 변형 ===");
        System.out.println("thenApply:      같은 스레드");
        System.out.println("thenApplyAsync: 다른 스레드 (ForkJoinPool)");
        System.out.println("\n모든 메서드에 Async 버전 존재");
        System.out.println("- thenApplyAsync");
        System.out.println("- thenAcceptAsync");
        System.out.println("- thenRunAsync");
        System.out.println("등등...");
    }
}
```

### 6.2 커스텀 Executor

```java
import java.util.concurrent.*;

public class CustomExecutor {
    public static void main(String[] args) throws Exception {
        // 커스텀 스레드 풀
        ExecutorService executor = Executors.newFixedThreadPool(4);
        
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            System.out.println("작업 1: " + Thread.currentThread().getName());
            return "Hello";
        }, executor).thenApplyAsync(s -> {
            System.out.println("작업 2: " + Thread.currentThread().getName());
            return s.toUpperCase();
        }, executor);
        
        System.out.println("결과: " + future.get());
        
        executor.shutdown();
        
        
        System.out.println("\n=== 커스텀 Executor ===");
        System.out.println("- 스레드 풀 크기 제어");
        System.out.println("- 우선순위 설정");
        System.out.println("- 모니터링");
    }
}
```

---

## 7. 타임아웃과 취소

### 7.1 orTimeout (Java 9+)

```java
import java.util.concurrent.*;

public class OrTimeoutExample {
    public static void main(String[] args) {
        // 타임아웃
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "완료";
        }).orTimeout(2, TimeUnit.SECONDS);
        
        try {
            String result = future.get();
            System.out.println("결과: " + result);
        } catch (TimeoutException e) {
            System.out.println("타임아웃!");
        } catch (Exception e) {
            System.out.println("예외: " + e.getCause());
        }
        
        
        System.out.println("\n=== orTimeout ===");
        System.out.println("- 지정 시간 내 완료 안 되면 예외");
        System.out.println("- TimeoutException");
    }
}
```

### 7.2 completeOnTimeout (Java 9+)

```java
import java.util.concurrent.*;

public class CompleteOnTimeoutExample {
    public static void main(String[] args) throws Exception {
        // 타임아웃 시 기본값
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(3000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "완료";
        }).completeOnTimeout("타임아웃 기본값", 2, TimeUnit.SECONDS);
        
        String result = future.get();
        System.out.println("결과: " + result);
        
        
        System.out.println("\n=== completeOnTimeout ===");
        System.out.println("- 타임아웃 시 기본값 반환");
        System.out.println("- 예외 발생 안 함");
    }
}
```

### 7.3 cancel

```java
import java.util.concurrent.*;

public class CancelExample {
    public static void main(String[] args) throws Exception {
        CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> {
            try {
                Thread.sleep(5000);
            } catch (InterruptedException e) {
                System.out.println("작업 중단됨");
            }
            return "완료";
        });
        
        Thread.sleep(1000);
        
        // 취소
        boolean cancelled = future.cancel(true);
        System.out.println("취소됨: " + cancelled);
        System.out.println("취소 상태: " + future.isCancelled());
        
        try {
            future.get();
        } catch (CancellationException e) {
            System.out.println("CancellationException 발생");
        }
    }
}
```

---

## 8. 실전 예제

### 8.1 병렬 API 호출

```java
import java.util.concurrent.*;
import java.util.*;

public class ParallelAPICall {
    static CompletableFuture<String> fetchUser(int id) {
        return CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(1000); } catch (InterruptedException e) {}
            return "User-" + id;
        });
    }
    
    static CompletableFuture<String> fetchProfile(String user) {
        return CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(500); } catch (InterruptedException e) {}
            return user + "'s Profile";
        });
    }
    
    static CompletableFuture<String> fetchPosts(String user) {
        return CompletableFuture.supplyAsync(() -> {
            try { Thread.sleep(800); } catch (InterruptedException e) {}
            return user + "'s Posts";
        });
    }
    
    public static void main(String[] args) throws Exception {
        long start = System.currentTimeMillis();
        
        // 사용자 조회 후 프로필과 포스트 병렬 조회
        CompletableFuture<String> result = fetchUser(1)
            .thenCompose(user -> {
                CompletableFuture<String> profile = fetchProfile(user);
                CompletableFuture<String> posts = fetchPosts(user);
                
                return profile.thenCombine(posts, (p, po) -> 
                    user + "\n" + p + "\n" + po
                );
            });
        
        System.out.println(result.get());
        
        long time = System.currentTimeMillis() - start;
        System.out.println("\n소요 시간: " + time + "ms");
        System.out.println("(순차: 2300ms, 병렬: ~1500ms)");
    }
}
```

### 8.2 가격 비교 서비스

```java
import java.util.concurrent.*;
import java.util.*;
import java.util.stream.*;

public class PriceComparison {
    static class Shop {
        String name;
        
        Shop(String name) {
            this.name = name;
        }
        
        double getPrice(String product) {
            // 네트워크 지연 시뮬레이션
            try {
                Thread.sleep((long) (Math.random() * 1000));
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return Math.random() * 100;
        }
        
        CompletableFuture<Double> getPriceAsync(String product) {
            return CompletableFuture.supplyAsync(() -> getPrice(product));
        }
    }
    
    public static void main(String[] args) throws Exception {
        List<Shop> shops = Arrays.asList(
            new Shop("Shop-A"),
            new Shop("Shop-B"),
            new Shop("Shop-C"),
            new Shop("Shop-D"),
            new Shop("Shop-E")
        );
        
        String product = "iPhone";
        
        // 병렬 가격 조회
        long start = System.currentTimeMillis();
        
        List<CompletableFuture<String>> futures = shops.stream()
            .map(shop -> shop.getPriceAsync(product)
                .thenApply(price -> String.format("%s: $%.2f", shop.name, price))
            )
            .collect(Collectors.toList());
        
        CompletableFuture<Void> allDone = CompletableFuture.allOf(
            futures.toArray(new CompletableFuture[0])
        );
        
        allDone.thenRun(() -> {
            futures.forEach(f -> System.out.println(f.join()));
        }).join();
        
        long time = System.currentTimeMillis() - start;
        System.out.println("\n소요 시간: " + time + "ms");
    }
}
```

### 8.3 비동기 캐시

```java
import java.util.concurrent.*;
import java.util.*;

public class AsyncCache {
    static class Cache<K, V> {
        private Map<K, CompletableFuture<V>> cache = new ConcurrentHashMap<>();
        
        public CompletableFuture<V> get(K key, 
                java.util.function.Function<K, V> loader) {
            return cache.computeIfAbsent(key, k -> 
                CompletableFuture.supplyAsync(() -> {
                    System.out.println("캐시 미스: " + k);
                    return loader.apply(k);
                })
            );
        }
        
        public void invalidate(K key) {
            cache.remove(key);
        }
    }
    
    public static void main(String[] args) throws Exception {
        Cache<String, String> cache = new Cache<>();
        
        // 비용 큰 계산
        java.util.function.Function<String, String> expensiveOp = key -> {
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            return "Value-" + key;
        };
        
        // 첫 호출 (캐시 미스)
        CompletableFuture<String> f1 = cache.get("key1", expensiveOp);
        System.out.println("결과 1: " + f1.get());
        
        // 두 번째 호출 (캐시 히트)
        CompletableFuture<String> f2 = cache.get("key1", expensiveOp);
        System.out.println("결과 2: " + f2.get());  // 즉시 반환
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 비동기 변환

```java
// 동기 코드를 비동기로 변환
public class Problem1 {
    static String fetchData() {
        try { Thread.sleep(1000); } catch (InterruptedException e) {}
        return "Data";
    }
    
    static String process(String data) {
        return data.toUpperCase();
    }
    
    public static void main(String[] args) {
        // 동기 버전
        String data = fetchData();
        String result = process(data);
        System.out.println(result);
        
        // 비동기 버전으로 변환
        // 구현
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
CompletableFuture<String> future = CompletableFuture.supplyAsync(() -> fetchData())
    .thenApply(data -> process(data));

future.thenAccept(System.out::println);
```
</details>

---

### 문제 2: 예외 처리

```java
// 예외 발생 시 기본값 반환
public class Problem2 {
    public static void main(String[] args) {
        CompletableFuture<Integer> future = CompletableFuture.supplyAsync(() -> {
            if (Math.random() > 0.5) {
                throw new RuntimeException("Error");
            }
            return 100;
        });
        
        // 예외 시 0 반환하도록 구현
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
CompletableFuture<Integer> handled = future.exceptionally(ex -> {
    System.out.println("예외 처리: " + ex.getMessage());
    return 0;
});

System.out.println("결과: " + handled.join());
```
</details>

---

### 문제 3: 병렬 합산

```java
// 여러 CompletableFuture 결과 합산
public class Problem3 {
    public static void main(String[] args) {
        List<CompletableFuture<Integer>> futures = Arrays.asList(
            CompletableFuture.supplyAsync(() -> 10),
            CompletableFuture.supplyAsync(() -> 20),
            CompletableFuture.supplyAsync(() -> 30)
        );
        
        // 모든 결과를 합산 (allOf 사용)
        // 구현
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
CompletableFuture<Integer> sum = CompletableFuture.allOf(
    futures.toArray(new CompletableFuture[0])
).thenApply(v ->
    futures.stream()
        .map(CompletableFuture::join)
        .mapToInt(Integer::intValue)
        .sum()
);

System.out.println("합계: " + sum.join());
```
</details>

---

## 📌 핵심 정리

### 생성
```java
CompletableFuture.runAsync(runnable)
CompletableFuture.supplyAsync(supplier)
CompletableFuture.completedFuture(value)
new CompletableFuture<>()  // 수동 완료
```

### 변환
```java
thenApply(func)       // T -> R
thenAccept(consumer)  // T -> void
thenRun(runnable)     // void -> void
thenCompose(func)     // T -> CF<R> (평탄화)
```

### 조합
```java
thenCombine(other, biFunc)  // 두 결과 조합
allOf(futures...)           // 모두 완료
anyOf(futures...)           // 하나라도 완료
```

### 예외 처리
```java
exceptionally(func)      // 예외 시 실행
handle(biFunc)           // 성공/실패 둘 다
whenComplete(biConsumer) // 관찰만
```

### 비동기
```java
thenApplyAsync(func)          // 다른 스레드
thenApplyAsync(func, executor) // 커스텀 Executor
```

### Best Practices
```
✅ Async 변형 사용 (블로킹 방지)
✅ 예외 처리 (exceptionally, handle)
✅ 커스텀 Executor (리소스 제어)
❌ get() 남발 (블로킹)
❌ join() 예외 처리 누락
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 05. Atomic Variables](./Concurrency-05-Atomic.md) | [다음: 07. Virtual Threads →](./Concurrency-07-Virtual.md)**

</div>
