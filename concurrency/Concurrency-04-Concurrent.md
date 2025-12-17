# Concurrency 04. Concurrent Collections 완전 정복

> 스레드 안전 컬렉션 - ConcurrentHashMap, CopyOnWriteArrayList 마스터하기  
> Java Concurrency API

---

## 📑 목차

1. [동시성 컬렉션 개요](#1-동시성-컬렉션-개요)
2. [ConcurrentHashMap](#2-concurrenthashmap)
3. [CopyOnWriteArrayList](#3-copyonwritearraylist)
4. [CopyOnWriteArraySet](#4-copyonwritearrayset)
5. [ConcurrentLinkedQueue](#5-concurrentlinkedqueue)
6. [BlockingQueue](#6-blockingqueue)
7. [ConcurrentSkipListMap](#7-concurrentskiplistmap)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. 동시성 컬렉션 개요

### 1.1 일반 컬렉션의 문제

```java
import java.util.*;

public class NonThreadSafeCollection {
    public static void main(String[] args) throws InterruptedException {
        Map<Integer, String> map = new HashMap<>();
        
        // 여러 스레드가 동시에 put
        Thread[] threads = new Thread[10];
        for (int i = 0; i < threads.length; i++) {
            final int threadId = i;
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    map.put(threadId * 1000 + j, "value");
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("예상 크기: 10000");
        System.out.println("실제 크기: " + map.size());
        System.out.println("→ 데이터 손실 또는 예외 발생 가능");
        
        
        System.out.println("\n=== 문제점 ===");
        System.out.println("HashMap: 스레드 안전하지 않음");
        System.out.println("ArrayList: 스레드 안전하지 않음");
        System.out.println("HashSet: 스레드 안전하지 않음");
    }
}
```

### 1.2 동기화 래퍼의 한계

```java
import java.util.*;

public class SynchronizedCollectionLimitation {
    public static void main(String[] args) {
        // Collections.synchronizedMap 사용
        Map<String, Integer> map = Collections.synchronizedMap(new HashMap<>());
        
        map.put("A", 1);
        map.put("B", 2);
        
        // 복합 연산은 여전히 동기화 필요!
        synchronized(map) {
            if (!map.containsKey("C")) {
                map.put("C", 3);
            }
        }
        
        
        System.out.println("=== synchronized 래퍼의 한계 ===");
        System.out.println("✅ 개별 메서드 동기화");
        System.out.println("❌ 복합 연산 동기화 안 됨");
        System.out.println("❌ 전체 락 (성능 저하)");
        System.out.println("❌ 반복 중 수정 불가");
    }
}
```

### 1.3 동시성 컬렉션의 장점

```java
import java.util.concurrent.*;

public class ConcurrentCollectionAdvantages {
    public static void main(String[] args) {
        System.out.println("=== 동시성 컬렉션 ===\n");
        
        System.out.println("✅ 장점");
        System.out.println("1. 스레드 안전");
        System.out.println("   - 내부적으로 동기화 처리");
        System.out.println("2. 고성능");
        System.out.println("   - 세밀한 락 (Lock Striping)");
        System.out.println("   - 락 프리 알고리즘");
        System.out.println("3. 확장성");
        System.out.println("   - 여러 스레드 동시 접근");
        System.out.println("4. 반복 안전");
        System.out.println("   - 약한 일관성 반복자\n");
        
        System.out.println("📌 주요 클래스");
        System.out.println("ConcurrentHashMap");
        System.out.println("CopyOnWriteArrayList");
        System.out.println("CopyOnWriteArraySet");
        System.out.println("ConcurrentLinkedQueue");
        System.out.println("BlockingQueue");
        System.out.println("ConcurrentSkipListMap");
    }
}
```

---

## 2. ConcurrentHashMap

### 2.1 기본 사용법

```java
import java.util.concurrent.*;

public class ConcurrentHashMapBasic {
    public static void main(String[] args) throws InterruptedException {
        ConcurrentHashMap<Integer, String> map = new ConcurrentHashMap<>();
        
        // 여러 스레드가 동시에 put
        Thread[] threads = new Thread[10];
        for (int i = 0; i < threads.length; i++) {
            final int threadId = i;
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    map.put(threadId * 1000 + j, "value-" + j);
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("크기: " + map.size());
        System.out.println("→ 정확히 10000!");
        
        
        System.out.println("\n=== ConcurrentHashMap 특징 ===");
        System.out.println("- 스레드 안전");
        System.out.println("- 락 분할 (Segment 기반)");
        System.out.println("- null 키/값 불가");
        System.out.println("- 약한 일관성");
    }
}
```

### 2.2 원자적 연산

```java
import java.util.concurrent.*;

public class ConcurrentHashMapAtomicOperations {
    public static void main(String[] args) throws InterruptedException {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        map.put("count", 0);
        
        // 여러 스레드가 동시에 증가
        Thread[] threads = new Thread[100];
        for (int i = 0; i < threads.length; i++) {
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 1000; j++) {
                    // 원자적 증가
                    map.compute("count", (k, v) -> v == null ? 1 : v + 1);
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("최종 카운트: " + map.get("count"));
        System.out.println("예상: 100000");
        
        
        System.out.println("\n=== 원자적 메서드 ===");
        System.out.println("putIfAbsent(k, v)");
        System.out.println("remove(k, v)");
        System.out.println("replace(k, old, new)");
        System.out.println("compute(k, func)");
        System.out.println("computeIfAbsent(k, func)");
        System.out.println("computeIfPresent(k, func)");
        System.out.println("merge(k, v, func)");
    }
}
```

### 2.3 고급 메서드

```java
import java.util.concurrent.*;

public class ConcurrentHashMapAdvanced {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        
        // putIfAbsent - 없을 때만 추가
        map.putIfAbsent("A", 1);
        map.putIfAbsent("A", 2);  // 무시됨
        System.out.println("A: " + map.get("A"));  // 1
        
        
        // computeIfAbsent - 없을 때 계산
        map.computeIfAbsent("B", k -> {
            System.out.println("B 계산 중...");
            return 10;
        });
        map.computeIfAbsent("B", k -> 20);  // 실행 안 됨
        System.out.println("B: " + map.get("B"));  // 10
        
        
        // computeIfPresent - 있을 때만 계산
        map.computeIfPresent("A", (k, v) -> v * 2);
        System.out.println("A (2배): " + map.get("A"));  // 2
        
        
        // merge - 값 병합
        map.merge("A", 5, (old, val) -> old + val);
        System.out.println("A (merge): " + map.get("A"));  // 7
        
        
        // replaceAll - 모든 값 변환
        map.replaceAll((k, v) -> v * 10);
        System.out.println("\n변환 후: " + map);
    }
}
```

### 2.4 벌크 연산

```java
import java.util.concurrent.*;

public class ConcurrentHashMapBulkOperations {
    public static void main(String[] args) {
        ConcurrentHashMap<String, Integer> map = new ConcurrentHashMap<>();
        
        for (int i = 1; i <= 100; i++) {
            map.put("key" + i, i);
        }
        
        // forEach - 병렬 처리
        map.forEach(1, (k, v) -> {
            System.out.println(k + " = " + v);
        });
        
        
        // reduce - 합 계산
        int sum = map.reduce(1,
            (k, v) -> v,           // transformer
            (v1, v2) -> v1 + v2    // reducer
        );
        System.out.println("\n합: " + sum);
        
        
        // search - 조건 검색
        String result = map.search(1, (k, v) -> {
            return v > 50 ? k : null;
        });
        System.out.println("50보다 큰 첫 키: " + result);
        
        
        System.out.println("\n=== 벌크 연산 ===");
        System.out.println("parallelismThreshold: 병렬 처리 임계값");
        System.out.println("1: 항상 병렬");
        System.out.println("Long.MAX_VALUE: 항상 순차");
    }
}
```

---

## 3. CopyOnWriteArrayList

### 3.1 기본 사용법

```java
import java.util.concurrent.*;
import java.util.*;

public class CopyOnWriteArrayListBasic {
    public static void main(String[] args) throws InterruptedException {
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        
        // 쓰기 스레드
        Thread writer = new Thread(() -> {
            for (int i = 1; i <= 10; i++) {
                list.add("Item-" + i);
                System.out.println("추가: Item-" + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        // 읽기 스레드
        Thread reader = new Thread(() -> {
            for (int i = 0; i < 10; i++) {
                System.out.println("읽기: " + list);
                try {
                    Thread.sleep(150);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        writer.start();
        Thread.sleep(50);
        reader.start();
        
        writer.join();
        reader.join();
        
        
        System.out.println("\n=== CopyOnWriteArrayList ===");
        System.out.println("- 쓰기 시 복사본 생성");
        System.out.println("- 읽기는 락 없음 (매우 빠름)");
        System.out.println("- 반복 중 수정 안전");
        System.out.println("- 읽기 많고 쓰기 적을 때 적합");
    }
}
```

### 3.2 반복 안전성

```java
import java.util.concurrent.*;
import java.util.*;

public class CopyOnWriteArrayListIteration {
    public static void main(String[] args) {
        CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<>();
        list.add("A");
        list.add("B");
        list.add("C");
        
        // 일반 ArrayList는 ConcurrentModificationException!
        System.out.println("=== 반복 중 수정 ===");
        
        for (String item : list) {
            System.out.println("항목: " + item);
            if (item.equals("B")) {
                list.add("D");  // 안전!
            }
        }
        
        System.out.println("\n최종 리스트: " + list);
        
        
        System.out.println("\n=== 특징 ===");
        System.out.println("- Iterator는 생성 시점의 스냅샷");
        System.out.println("- 반복 중 수정해도 예외 없음");
        System.out.println("- 수정은 다음 반복에 반영");
    }
}
```

### 3.3 성능 비교

```java
import java.util.concurrent.*;
import java.util.*;

public class CopyOnWriteArrayListPerformance {
    public static void main(String[] args) {
        int size = 10000;
        
        // ArrayList + synchronized
        List<Integer> syncList = Collections.synchronizedList(new ArrayList<>());
        
        // CopyOnWriteArrayList
        CopyOnWriteArrayList<Integer> cowList = new CopyOnWriteArrayList<>();
        
        
        // 쓰기 성능
        System.out.println("=== 쓰기 성능 ===");
        
        long start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            syncList.add(i);
        }
        System.out.println("SynchronizedList: " + (System.currentTimeMillis() - start) + "ms");
        
        start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            cowList.add(i);
        }
        System.out.println("CopyOnWriteArrayList: " + (System.currentTimeMillis() - start) + "ms");
        
        
        // 읽기 성능
        System.out.println("\n=== 읽기 성능 ===");
        
        start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            syncList.get(i);
        }
        System.out.println("SynchronizedList: " + (System.currentTimeMillis() - start) + "ms");
        
        start = System.currentTimeMillis();
        for (int i = 0; i < size; i++) {
            cowList.get(i);
        }
        System.out.println("CopyOnWriteArrayList: " + (System.currentTimeMillis() - start) + "ms");
        
        
        System.out.println("\n=== 결론 ===");
        System.out.println("쓰기: CopyOnWriteArrayList 느림 (복사 비용)");
        System.out.println("읽기: CopyOnWriteArrayList 빠름 (락 없음)");
    }
}
```

---

## 4. CopyOnWriteArraySet

### 4.1 기본 사용법

```java
import java.util.concurrent.*;

public class CopyOnWriteArraySetBasic {
    public static void main(String[] args) throws InterruptedException {
        CopyOnWriteArraySet<String> set = new CopyOnWriteArraySet<>();
        
        // 여러 스레드가 동시에 추가
        Thread[] threads = new Thread[10];
        for (int i = 0; i < threads.length; i++) {
            final int threadId = i;
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 100; j++) {
                    set.add("Item-" + (threadId * 100 + j));
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("크기: " + set.size());
        
        
        // 중복 추가 시도
        set.add("Duplicate");
        set.add("Duplicate");
        System.out.println("중복 추가 후: " + set.contains("Duplicate"));
        
        
        System.out.println("\n=== CopyOnWriteArraySet ===");
        System.out.println("- CopyOnWriteArrayList 기반");
        System.out.println("- 중복 불가");
        System.out.println("- 읽기 많고 쓰기 적을 때");
    }
}
```

---

## 5. ConcurrentLinkedQueue

### 5.1 기본 사용법

```java
import java.util.concurrent.*;

public class ConcurrentLinkedQueueBasic {
    public static void main(String[] args) throws InterruptedException {
        ConcurrentLinkedQueue<String> queue = new ConcurrentLinkedQueue<>();
        
        // 생산자
        Thread producer = new Thread(() -> {
            for (int i = 1; i <= 10; i++) {
                queue.offer("Item-" + i);
                System.out.println("생산: Item-" + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        // 소비자
        Thread consumer = new Thread(() -> {
            for (int i = 1; i <= 10; i++) {
                String item;
                while ((item = queue.poll()) == null) {
                    // 대기
                }
                System.out.println("소비: " + item);
                try {
                    Thread.sleep(150);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        producer.start();
        consumer.start();
        
        producer.join();
        consumer.join();
        
        
        System.out.println("\n=== ConcurrentLinkedQueue ===");
        System.out.println("- 무제한 큐");
        System.out.println("- 락 프리 알고리즘");
        System.out.println("- 높은 동시성");
        System.out.println("- 블로킹 없음");
    }
}
```

---

## 6. BlockingQueue

### 6.1 ArrayBlockingQueue

```java
import java.util.concurrent.*;

public class ArrayBlockingQueueExample {
    public static void main(String[] args) {
        // 크기 제한 큐
        BlockingQueue<String> queue = new ArrayBlockingQueue<>(5);
        
        // 생산자
        new Thread(() -> {
            try {
                for (int i = 1; i <= 10; i++) {
                    queue.put("Item-" + i);  // 가득 차면 블로킹
                    System.out.println("생산: Item-" + i + " (크기: " + queue.size() + ")");
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
                    Thread.sleep(300);
                    String item = queue.take();  // 비어있으면 블로킹
                    System.out.println("소비: " + item + " (크기: " + queue.size() + ")");
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }, "Consumer").start();
        
        
        System.out.println("\n=== ArrayBlockingQueue ===");
        System.out.println("- 고정 크기");
        System.out.println("- 배열 기반");
        System.out.println("- put/take 블로킹");
    }
}
```

### 6.2 LinkedBlockingQueue

```java
import java.util.concurrent.*;

public class LinkedBlockingQueueExample {
    public static void main(String[] args) {
        // 무제한 또는 제한 가능
        BlockingQueue<Integer> queue = new LinkedBlockingQueue<>(10);
        
        // 생산자들
        for (int i = 0; i < 3; i++) {
            final int producerId = i;
            new Thread(() -> {
                try {
                    for (int j = 0; j < 10; j++) {
                        int value = producerId * 10 + j;
                        queue.put(value);
                        System.out.println("생산자-" + producerId + ": " + value);
                        Thread.sleep(50);
                    }
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }).start();
        }
        
        // 소비자
        new Thread(() -> {
            try {
                for (int i = 0; i < 30; i++) {
                    int value = queue.take();
                    System.out.println("소비: " + value);
                    Thread.sleep(100);
                }
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }).start();
        
        
        System.out.println("\n=== LinkedBlockingQueue ===");
        System.out.println("- 선택적 크기 제한");
        System.out.println("- 링크드 리스트 기반");
        System.out.println("- put/take 블로킹");
    }
}
```

### 6.3 PriorityBlockingQueue

```java
import java.util.concurrent.*;

public class PriorityBlockingQueueExample {
    static class Task implements Comparable<Task> {
        int priority;
        String name;
        
        Task(int priority, String name) {
            this.priority = priority;
            this.name = name;
        }
        
        @Override
        public int compareTo(Task other) {
            return Integer.compare(other.priority, this.priority);  // 높은 우선순위 먼저
        }
        
        @Override
        public String toString() {
            return name + "(P" + priority + ")";
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        BlockingQueue<Task> queue = new PriorityBlockingQueue<>();
        
        // 무작위 순서로 추가
        queue.put(new Task(1, "Low"));
        queue.put(new Task(5, "High"));
        queue.put(new Task(3, "Medium"));
        queue.put(new Task(5, "High2"));
        queue.put(new Task(1, "Low2"));
        
        // 우선순위 순으로 꺼냄
        System.out.println("=== 우선순위 순서로 처리 ===");
        while (!queue.isEmpty()) {
            System.out.println(queue.take());
        }
        
        
        System.out.println("\n=== PriorityBlockingQueue ===");
        System.out.println("- 우선순위 기반");
        System.out.println("- 무제한 크기");
        System.out.println("- Comparable 필요");
    }
}
```

---

## 7. ConcurrentSkipListMap

### 7.1 기본 사용법

```java
import java.util.concurrent.*;

public class ConcurrentSkipListMapBasic {
    public static void main(String[] args) {
        ConcurrentSkipListMap<Integer, String> map = new ConcurrentSkipListMap<>();
        
        // 무작위 순서로 추가
        map.put(5, "Five");
        map.put(1, "One");
        map.put(3, "Three");
        map.put(7, "Seven");
        map.put(2, "Two");
        
        // 정렬된 순서로 출력
        System.out.println("=== 정렬된 순서 ===");
        map.forEach((k, v) -> System.out.println(k + " = " + v));
        
        
        // 범위 연산
        System.out.println("\n=== 범위 연산 ===");
        System.out.println("3 이상: " + map.tailMap(3));
        System.out.println("5 이하: " + map.headMap(5, true));
        System.out.println("2~6: " + map.subMap(2, true, 6, true));
        
        
        System.out.println("\n=== ConcurrentSkipListMap ===");
        System.out.println("- 정렬된 맵");
        System.out.println("- 스킵 리스트 기반");
        System.out.println("- O(log n) 연산");
        System.out.println("- 범위 연산 지원");
    }
}
```

---

## 8. 실전 예제

### 8.1 캐시 구현

```java
import java.util.concurrent.*;

public class ThreadSafeCache {
    static class Cache<K, V> {
        private ConcurrentHashMap<K, V> cache = new ConcurrentHashMap<>();
        private int maxSize;
        
        Cache(int maxSize) {
            this.maxSize = maxSize;
        }
        
        public V get(K key) {
            return cache.get(key);
        }
        
        public void put(K key, V value) {
            if (cache.size() >= maxSize) {
                // 간단한 LRU: 첫 번째 키 제거
                K firstKey = cache.keys().nextElement();
                cache.remove(firstKey);
            }
            cache.put(key, value);
        }
        
        public V computeIfAbsent(K key, java.util.function.Function<K, V> func) {
            return cache.computeIfAbsent(key, func);
        }
        
        public int size() {
            return cache.size();
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        Cache<String, String> cache = new Cache<>(100);
        
        // 여러 스레드가 캐시 사용
        Thread[] threads = new Thread[10];
        for (int i = 0; i < threads.length; i++) {
            final int threadId = i;
            threads[i] = new Thread(() -> {
                for (int j = 0; j < 50; j++) {
                    String key = "key-" + (threadId * 50 + j);
                    cache.computeIfAbsent(key, k -> {
                        // 비용 큰 계산
                        return "value-" + k;
                    });
                }
            });
            threads[i].start();
        }
        
        for (Thread t : threads) {
            t.join();
        }
        
        System.out.println("캐시 크기: " + cache.size());
    }
}
```

### 8.2 이벤트 리스너

```java
import java.util.concurrent.*;

public class EventListenerSystem {
    static interface EventListener {
        void onEvent(String event);
    }
    
    static class EventBus {
        private CopyOnWriteArrayList<EventListener> listeners = 
            new CopyOnWriteArrayList<>();
        
        public void register(EventListener listener) {
            listeners.add(listener);
        }
        
        public void unregister(EventListener listener) {
            listeners.remove(listener);
        }
        
        public void publish(String event) {
            for (EventListener listener : listeners) {
                listener.onEvent(event);
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        EventBus bus = new EventBus();
        
        // 리스너 등록
        bus.register(event -> System.out.println("Listener-1: " + event));
        bus.register(event -> System.out.println("Listener-2: " + event));
        
        // 이벤트 발행 (다른 스레드에서)
        Thread publisher = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                bus.publish("Event-" + i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        publisher.start();
        publisher.join();
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 작업 큐

```java
// BlockingQueue로 작업 큐 구현
public class Problem1 {
    static class WorkQueue {
        // BlockingQueue 사용
        
        public void submit(Runnable task) {
            // 구현
        }
        
        public Runnable take() throws InterruptedException {
            // 구현
            return null;
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
static class WorkQueue {
    private BlockingQueue<Runnable> queue = new LinkedBlockingQueue<>();
    
    public void submit(Runnable task) {
        try {
            queue.put(task);
        } catch (InterruptedException e) {
            Thread.currentThread().interrupt();
        }
    }
    
    public Runnable take() throws InterruptedException {
        return queue.take();
    }
}
```
</details>

---

### 문제 2: 동시 카운터

```java
// ConcurrentHashMap으로 카운터 구현
public class Problem2 {
    static class ConcurrentCounter {
        private ConcurrentHashMap<String, Integer> counts;
        
        public void increment(String key) {
            // 구현
        }
        
        public int get(String key) {
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
static class ConcurrentCounter {
    private ConcurrentHashMap<String, Integer> counts = new ConcurrentHashMap<>();
    
    public void increment(String key) {
        counts.compute(key, (k, v) -> v == null ? 1 : v + 1);
    }
    
    public int get(String key) {
        return counts.getOrDefault(key, 0);
    }
}
```
</details>

---

### 문제 3: 읽기 전용 설정

```java
// CopyOnWriteArrayList로 설정 관리
public class Problem3 {
    static class ConfigManager {
        // 읽기 많고 쓰기 적음
        
        public void addConfig(String config) {
            // 구현
        }
        
        public List<String> getConfigs() {
            // 구현
            return null;
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
static class ConfigManager {
    private CopyOnWriteArrayList<String> configs = new CopyOnWriteArrayList<>();
    
    public void addConfig(String config) {
        configs.add(config);
    }
    
    public List<String> getConfigs() {
        return new ArrayList<>(configs);
    }
}
```
</details>

---

## 📌 핵심 정리

### 주요 컬렉션
```java
// Map
ConcurrentHashMap<K, V>        // 고성능, 락 분할
ConcurrentSkipListMap<K, V>    // 정렬, O(log n)

// List
CopyOnWriteArrayList<E>        // 읽기 많음

// Set
CopyOnWriteArraySet<E>         // 읽기 많음
ConcurrentSkipListSet<E>       // 정렬

// Queue
ConcurrentLinkedQueue<E>       // 락 프리
ArrayBlockingQueue<E>          // 고정 크기
LinkedBlockingQueue<E>         // 선택적 크기
PriorityBlockingQueue<E>       // 우선순위
```

### 선택 기준
```
ConcurrentHashMap:        일반적 Map
CopyOnWriteArrayList:     읽기 >> 쓰기
BlockingQueue:            생산자-소비자
ConcurrentSkipListMap:    정렬 필요
```

### 주의사항
```
❌ null 키/값 불가 (대부분)
❌ 약한 일관성 (size, isEmpty)
✅ 반복 중 수정 가능
✅ 복합 연산은 원자적 메서드 사용
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 03. ExecutorService](./Concurrency-03-Executor.md) | [다음: 05. Atomic Variables →](./Concurrency-05-Atomic.md)**

</div>
