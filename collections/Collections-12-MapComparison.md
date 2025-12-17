# Collections 12. Map 비교와 선택

> HashMap vs LinkedHashMap vs TreeMap - 완벽 비교 가이드  
> Java API Reference

---

## 📑 목차

1. [특징 비교](#1-특징-비교)
2. [성능 비교](#2-성능-비교)
3. [메모리 비교](#3-메모리-비교)
4. [선택 가이드](#4-선택-가이드)
5. [실전 벤치마크](#5-실전-벤치마크)
6. [사용 사례](#6-사용-사례)
7. [실전 연습 문제](#7-실전-연습-문제)

---

## 1. 특징 비교

### 1.1 기본 특성

```java
import java.util.*;

public class BasicComparison {
    public static void main(String[] args) {
        System.out.println("=== Map 구현체 비교 ===\n");
        
        System.out.println("HashMap:");
        System.out.println("  순서: 없음");
        System.out.println("  정렬: 없음");
        System.out.println("  성능: O(1)");
        System.out.println("  null Key: 1개 허용\n");
        
        System.out.println("LinkedHashMap:");
        System.out.println("  순서: 삽입 순서");
        System.out.println("  정렬: 없음");
        System.out.println("  성능: O(1)");
        System.out.println("  null Key: 1개 허용\n");
        
        System.out.println("TreeMap:");
        System.out.println("  순서: 정렬 순서");
        System.out.println("  정렬: Key 기준");
        System.out.println("  성능: O(log n)");
        System.out.println("  null Key: 불가");
    }
}
```

### 1.2 순서 차이

```java
public class OrderComparison {
    public static void main(String[] args) {
        Map<Integer, String> data = new HashMap<>();
        data.put(5, "Five");
        data.put(2, "Two");
        data.put(8, "Eight");
        data.put(1, "One");
        
        // HashMap (무작위)
        Map<Integer, String> hashMap = new HashMap<>(data);
        System.out.println("HashMap:       " + hashMap);
        // 순서 예측 불가
        
        
        // LinkedHashMap (삽입 순서)
        Map<Integer, String> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put(5, "Five");
        linkedHashMap.put(2, "Two");
        linkedHashMap.put(8, "Eight");
        linkedHashMap.put(1, "One");
        System.out.println("LinkedHashMap: " + linkedHashMap);
        // {5=Five, 2=Two, 8=Eight, 1=One}
        
        
        // TreeMap (정렬)
        Map<Integer, String> treeMap = new TreeMap<>(data);
        System.out.println("TreeMap:       " + treeMap);
        // {1=One, 2=Two, 5=Five, 8=Eight}
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
        
        System.out.println("연산          HashMap    LinkedHashMap    TreeMap");
        System.out.println("----------------------------------------------------");
        System.out.println("put           O(1)       O(1)             O(log n)");
        System.out.println("get           O(1)       O(1)             O(log n)");
        System.out.println("remove        O(1)       O(1)             O(log n)");
        System.out.println("containsKey   O(1)       O(1)             O(log n)");
        System.out.println("순회          O(n)       O(n)             O(n)");
        System.out.println("firstKey      X          X                O(log n)");
        System.out.println("범위 연산     X          X                O(log n)");
        
        System.out.println("\n=== 결론 ===");
        System.out.println("HashMap: 가장 빠름");
        System.out.println("LinkedHashMap: 약간 느림");
        System.out.println("TreeMap: 느림 (하지만 정렬!)");
    }
}
```

### 2.2 추가 성능

```java
public class PutPerformance {
    public static void main(String[] args) {
        int n = 100000;
        
        // HashMap
        Map<Integer, String> hashMap = new HashMap<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            hashMap.put(i, "Value" + i);
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // LinkedHashMap
        Map<Integer, String> linkedHashMap = new LinkedHashMap<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            linkedHashMap.put(i, "Value" + i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        // TreeMap
        Map<Integer, String> treeMap = new TreeMap<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            treeMap.put(i, "Value" + i);
        }
        long time3 = System.currentTimeMillis() - start;
        
        
        System.out.println("=== " + n + "개 추가 ===");
        System.out.println("HashMap:       " + time1 + "ms");
        System.out.println("LinkedHashMap: " + time2 + "ms");
        System.out.println("TreeMap:       " + time3 + "ms");
    }
}
```

### 2.3 조회 성능

```java
public class GetPerformance {
    public static void main(String[] args) {
        int n = 100000;
        
        // 데이터 준비
        Map<Integer, String> hashMap = new HashMap<>();
        Map<Integer, String> linkedHashMap = new LinkedHashMap<>();
        Map<Integer, String> treeMap = new TreeMap<>();
        
        for (int i = 0; i < n; i++) {
            hashMap.put(i, "Value" + i);
            linkedHashMap.put(i, "Value" + i);
            treeMap.put(i, "Value" + i);
        }
        
        
        // HashMap 조회
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            hashMap.get(i);
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // LinkedHashMap 조회
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            linkedHashMap.get(i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        // TreeMap 조회
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            treeMap.get(i);
        }
        long time3 = System.currentTimeMillis() - start;
        
        
        System.out.println("=== " + n + "번 조회 ===");
        System.out.println("HashMap:       " + time1 + "ms");
        System.out.println("LinkedHashMap: " + time2 + "ms");
        System.out.println("TreeMap:       " + time3 + "ms");
    }
}
```

---

## 3. 메모리 비교

### 3.1 메모리 사용량

```java
public class MemoryUsage {
    public static void main(String[] args) {
        System.out.println("=== 메모리 사용량 ===\n");
        
        System.out.println("HashMap:");
        System.out.println("  구조: 해시 테이블");
        System.out.println("  오버헤드: 중간\n");
        
        System.out.println("LinkedHashMap:");
        System.out.println("  구조: 해시 + 이중 연결 리스트");
        System.out.println("  오버헤드: 큼 (before/after 참조)\n");
        
        System.out.println("TreeMap:");
        System.out.println("  구조: 레드-블랙 트리");
        System.out.println("  오버헤드: 중간-큼 (parent/left/right)\n");
        
        System.out.println("=== 순서 ===");
        System.out.println("HashMap < TreeMap < LinkedHashMap");
    }
}
```

---

## 4. 선택 가이드

### 4.1 선택 플로우

```java
public class SelectionGuide {
    public static void main(String[] args) {
        System.out.println("=== Map 선택 가이드 ===\n");
        
        System.out.println("Q1. Key 기준 정렬이 필요한가?");
        System.out.println("  YES → TreeMap");
        System.out.println("    - 자동 정렬");
        System.out.println("    - 범위 연산");
        System.out.println("    - 최소/최대 접근\n");
        
        System.out.println("  NO → Q2로\n");
        
        System.out.println("Q2. 삽입 순서 유지가 필요한가?");
        System.out.println("  YES → LinkedHashMap");
        System.out.println("    - 삽입 순서");
        System.out.println("    - 예측 가능한 순회");
        System.out.println("    - LRU 캐시\n");
        
        System.out.println("  NO → HashMap");
        System.out.println("    - 가장 빠름");
        System.out.println("    - 일반적 선택");
    }
}
```

### 4.2 기능 비교표

```java
public class FeatureComparison {
    public static void main(String[] args) {
        System.out.println("=== 기능 비교 ===\n");
        
        System.out.println("기능              HashMap  LinkedHashMap  TreeMap");
        System.out.println("---------------------------------------------------");
        System.out.println("Key-Value         O        O              O");
        System.out.println("null Key          O        O              X");
        System.out.println("null Value        O        O              O");
        System.out.println("순서 유지         X        O (삽입)       O (정렬)");
        System.out.println("성능 (일반)       최고     중간           낮음");
        System.out.println("메모리            중간     많음           중간");
        System.out.println("firstKey/lastKey  X        X              O");
        System.out.println("범위 연산         X        X              O");
        System.out.println("근접 Key 찾기     X        X              O");
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
            testPut(size);
            testGet(size);
            testIterate(size);
        }
    }
    
    static void testPut(int n) {
        long t1 = measurePut(new HashMap<>(), n);
        long t2 = measurePut(new LinkedHashMap<>(), n);
        long t3 = measurePut(new TreeMap<>(), n);
        
        System.out.println("Put - HM: " + t1 + "ms, LHM: " + t2 + "ms, TM: " + t3 + "ms");
    }
    
    static void testGet(int n) {
        Map<Integer, String> hm = new HashMap<>();
        Map<Integer, String> lhm = new LinkedHashMap<>();
        Map<Integer, String> tm = new TreeMap<>();
        
        for (int i = 0; i < n; i++) {
            hm.put(i, "V" + i);
            lhm.put(i, "V" + i);
            tm.put(i, "V" + i);
        }
        
        long t1 = measureGet(hm, n);
        long t2 = measureGet(lhm, n);
        long t3 = measureGet(tm, n);
        
        System.out.println("Get - HM: " + t1 + "ms, LHM: " + t2 + "ms, TM: " + t3 + "ms");
    }
    
    static void testIterate(int n) {
        Map<Integer, String> hm = new HashMap<>();
        Map<Integer, String> lhm = new LinkedHashMap<>();
        Map<Integer, String> tm = new TreeMap<>();
        
        for (int i = 0; i < n; i++) {
            hm.put(i, "V" + i);
            lhm.put(i, "V" + i);
            tm.put(i, "V" + i);
        }
        
        long t1 = measureIterate(hm);
        long t2 = measureIterate(lhm);
        long t3 = measureIterate(tm);
        
        System.out.println("Iterate - HM: " + t1 + "ms, LHM: " + t2 + "ms, TM: " + t3 + "ms");
    }
    
    static long measurePut(Map<Integer, String> map, int n) {
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) map.put(i, "V" + i);
        return System.currentTimeMillis() - start;
    }
    
    static long measureGet(Map<Integer, String> map, int n) {
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) map.get(i);
        return System.currentTimeMillis() - start;
    }
    
    static long measureIterate(Map<Integer, String> map) {
        long start = System.currentTimeMillis();
        for (Map.Entry<Integer, String> e : map.entrySet()) { }
        return System.currentTimeMillis() - start;
    }
}
```

---

## 6. 사용 사례

### 6.1 HashMap 사례

```java
public class HashMapUseCases {
    public static void main(String[] args) {
        System.out.println("=== HashMap 사용 사례 ===\n");
        
        // 1. 단어 빈도 카운트
        Map<String, Integer> frequency = new HashMap<>();
        String text = "apple banana apple cherry";
        for (String word : text.split(" ")) {
            frequency.put(word, frequency.getOrDefault(word, 0) + 1);
        }
        System.out.println("1. 빈도: " + frequency + "\n");
        
        
        // 2. 사용자 세션 (ID → 정보)
        Map<String, String> sessions = new HashMap<>();
        sessions.put("session123", "user_alice");
        sessions.put("session456", "user_bob");
        System.out.println("2. 세션: " + sessions + "\n");
        
        
        // 3. 캐시
        Map<String, Object> cache = new HashMap<>();
        cache.put("user:1", "Alice");
        cache.put("user:2", "Bob");
        System.out.println("3. 캐시: " + cache);
    }
}
```

### 6.2 LinkedHashMap 사례

```java
public class LinkedHashMapUseCases {
    public static void main(String[] args) {
        System.out.println("=== LinkedHashMap 사용 사례 ===\n");
        
        // 1. 설정 파일 (순서 유지)
        Map<String, String> config = new LinkedHashMap<>();
        config.put("host", "localhost");
        config.put("port", "8080");
        config.put("database", "mydb");
        
        System.out.println("1. 설정:");
        config.forEach((k, v) -> System.out.println("   " + k + " = " + v));
        
        
        // 2. 최근 검색어
        Map<String, Long> recentSearches = new LinkedHashMap<>();
        recentSearches.put("Java", System.currentTimeMillis());
        recentSearches.put("Python", System.currentTimeMillis());
        System.out.println("\n2. 최근 검색: " + recentSearches.keySet() + "\n");
        
        
        // 3. JSON 순서 유지
        Map<String, Object> json = new LinkedHashMap<>();
        json.put("name", "Alice");
        json.put("age", 25);
        json.put("city", "Seoul");
        System.out.println("3. JSON: " + json);
    }
}
```

### 6.3 TreeMap 사례

```java
public class TreeMapUseCases {
    public static void main(String[] args) {
        System.out.println("=== TreeMap 사용 사례 ===\n");
        
        // 1. 순위표 (점수 → 이름)
        TreeMap<Integer, String> leaderboard = new TreeMap<>(
            Collections.reverseOrder()
        );
        leaderboard.put(95, "Alice");
        leaderboard.put(87, "Bob");
        leaderboard.put(92, "Charlie");
        
        System.out.println("1. 순위표:");
        int rank = 1;
        for (Map.Entry<Integer, String> e : leaderboard.entrySet()) {
            System.out.println("   " + rank++ + ". " + e.getValue() + " - " + e.getKey());
        }
        
        
        // 2. 시간별 이벤트
        TreeMap<Long, String> events = new TreeMap<>();
        events.put(System.currentTimeMillis(), "Event1");
        events.put(System.currentTimeMillis() + 1000, "Event2");
        System.out.println("\n2. 이벤트: " + events.values() + "\n");
        
        
        // 3. 가격대별 상품
        TreeMap<Integer, String> products = new TreeMap<>();
        products.put(10000, "Product A");
        products.put(20000, "Product B");
        products.put(30000, "Product C");
        
        System.out.println("3. 2만원 이하:");
        System.out.println("   " + products.headMap(20000, true));
    }
}
```

---

## 7. 실전 연습 문제

### 문제 1: 적절한 Map 선택

```java
// 각 상황에 맞는 Map 선택
public class Problem1 {
    public static void main(String[] args) {
        System.out.println("=== Map 선택 문제 ===\n");
        
        System.out.println("1. 학생 ID → 이름 (빠른 조회)");
        System.out.println("2. 로그 타임스탬프 → 메시지 (시간순)");
        System.out.println("3. 설정 파일 (순서 유지)");
        System.out.println("4. 단어 빈도 카운트 (순서 불필요)");
        System.out.println("5. 점수 → 학생 (높은 점수부터)");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void main(String[] args) {
        // 1. HashMap - 빠른 조회
        Map<Integer, String> students = new HashMap<>();
        
        // 2. TreeMap - 시간순 정렬
        Map<Long, String> logs = new TreeMap<>();
        
        // 3. LinkedHashMap - 순서 유지
        Map<String, String> config = new LinkedHashMap<>();
        
        // 4. HashMap - 가장 빠름
        Map<String, Integer> frequency = new HashMap<>();
        
        // 5. TreeMap - 점수 정렬
        Map<Integer, String> scores = new TreeMap<>(
            Collections.reverseOrder()
        );
        
        System.out.println("1. HashMap");
        System.out.println("2. TreeMap");
        System.out.println("3. LinkedHashMap");
        System.out.println("4. HashMap");
        System.out.println("5. TreeMap");
    }
}
```
</details>

---

### 문제 2: 성능 차이 체험

```java
// 세 Map의 성능 차이 직접 측정
public class Problem2 {
    public static void measurePerformance(int n) {
        // HashMap, LinkedHashMap, TreeMap
        // 1. 추가 성능
        // 2. 조회 성능
        // 3. 순회 성능
    }
    
    public static void main(String[] args) {
        measurePerformance(100000);
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
        
        // 1. 추가
        Map<Integer, String> hm = new HashMap<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) hm.put(i, "V" + i);
        long t1 = System.currentTimeMillis() - start;
        
        Map<Integer, String> lhm = new LinkedHashMap<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) lhm.put(i, "V" + i);
        long t2 = System.currentTimeMillis() - start;
        
        Map<Integer, String> tm = new TreeMap<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) tm.put(i, "V" + i);
        long t3 = System.currentTimeMillis() - start;
        
        System.out.println("추가:");
        System.out.println("  HashMap:       " + t1 + "ms");
        System.out.println("  LinkedHashMap: " + t2 + "ms");
        System.out.println("  TreeMap:       " + t3 + "ms");
        System.out.println("  Winner: HashMap\n");
        
        
        // 2. 조회
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) hm.get(i);
        t1 = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) lhm.get(i);
        t2 = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) tm.get(i);
        t3 = System.currentTimeMillis() - start;
        
        System.out.println("조회:");
        System.out.println("  HashMap:       " + t1 + "ms");
        System.out.println("  LinkedHashMap: " + t2 + "ms");
        System.out.println("  TreeMap:       " + t3 + "ms");
        System.out.println("  Winner: HashMap");
    }
}
```
</details>

---

### 문제 3: 최적 Map 구현

```java
// 상황별 최적 Map 구현
public class Problem3 {
    // 단어 빈도 (순서 불필요)
    public static Map<String, Integer> wordFrequency(String text) {
        // 어떤 Map?
        return null;
    }
    
    // LRU 캐시
    public static class LRUCache extends LinkedHashMap<String, String> {
        private int capacity;
        
        public LRUCache(int capacity) {
            // 어떤 생성자?
        }
        
        @Override
        protected boolean removeEldestEntry(Map.Entry<String, String> eldest) {
            // 구현
            return false;
        }
    }
    
    // 실시간 순위 Top 10
    public static class TopRanking {
        private TreeMap<Integer, String> scores;
        
        public TopRanking() {
            // 어떤 정렬?
        }
        
        public void addScore(String player, int score) {
            // 구현
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    // HashMap - 빠른 속도
    public static Map<String, Integer> wordFrequency(String text) {
        Map<String, Integer> freq = new HashMap<>();
        
        for (String word : text.split("\\s+")) {
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        
        return freq;
    }
    
    // LinkedHashMap - 액세스 순서
    public static class LRUCache extends LinkedHashMap<String, String> {
        private int capacity;
        
        public LRUCache(int capacity) {
            super(capacity, 0.75f, true);  // 액세스 순서
            this.capacity = capacity;
        }
        
        @Override
        protected boolean removeEldestEntry(Map.Entry<String, String> eldest) {
            return size() > capacity;
        }
    }
    
    // TreeMap - 자동 정렬
    public static class TopRanking {
        private TreeMap<Integer, String> scores;
        private static final int MAX_SIZE = 10;
        
        public TopRanking() {
            this.scores = new TreeMap<>(Collections.reverseOrder());
        }
        
        public void addScore(String player, int score) {
            scores.put(score, player);
            
            // 10개 초과 시 최하위 제거
            if (scores.size() > MAX_SIZE) {
                scores.pollLastEntry();
            }
        }
        
        public List<Map.Entry<Integer, String>> getTop10() {
            return new ArrayList<>(scores.entrySet());
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 비교표
```
특성           HashMap    LinkedHashMap    TreeMap
--------------------------------------------------
순서           없음       삽입 순서        정렬 순서
성능           O(1)       O(1)             O(log n)
메모리         중간       많음             중간
null Key       O          O                X
firstKey       X          X                O
범위 연산      X          X                O
```

### 선택 기준
```java
// 99% 경우: HashMap
Map<String, Integer> map = new HashMap<>();

// 순서 유지: LinkedHashMap
Map<String, Integer> ordered = new LinkedHashMap<>();

// 정렬 필요: TreeMap
Map<Integer, String> sorted = new TreeMap<>();
```

### 일반 권장사항
```
대부분: HashMap ✅
- 가장 빠름
- 메모리 효율적
- 순서 불필요

순서 중요: LinkedHashMap
- 삽입 순서 유지
- 예측 가능한 순회
- LRU 캐시

정렬 필요: TreeMap
- Key 기준 정렬
- 범위 연산
- 최소/최대
```

---

## 🎉 Map 시리즈 완료!

<div align="center">

### 📚 Map 시리즈

| Chapter | 주제 |
|:-------:|------|
| [09. HashMap](./Collections-09-HashMap.md) | 해시 기반 맵 |
| [10. LinkedHashMap](./Collections-10-LinkedHashMap.md) | 순서 유지 맵 |
| [11. TreeMap](./Collections-11-TreeMap.md) | 정렬된 맵 |
| [12. Map 비교](./Collections-12-MapComparison.md) ⭐ | 성능, 선택 가이드 |

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 11. TreeMap](./Collections-11-TreeMap.md)**

</div>
