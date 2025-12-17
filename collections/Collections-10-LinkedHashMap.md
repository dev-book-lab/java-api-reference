# Collections 10. LinkedHashMap 완전 정복

> 순서 유지 맵 - LinkedHashMap 마스터하기  
> Java API Reference

---

## 📑 목차

1. [LinkedHashMap 기본](#1-linkedhashmap-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [순서 유지](#4-순서-유지)
5. [순회 방법](#5-순회-방법)
6. [액세스 순서 모드](#6-액세스-순서-모드)
7. [내부 구조와 성능](#7-내부-구조와-성능)
8. [실전 활용](#8-실전-활용)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. LinkedHashMap 기본

### 1.1 특징

```java
import java.util.*;

public class LinkedHashMapBasic {
    public static void main(String[] args) {
        System.out.println("=== LinkedHashMap 특징 ===\n");
        
        System.out.println("1. Key-Value 쌍");
        System.out.println("   - HashMap과 동일\n");
        
        System.out.println("2. 삽입 순서 유지");
        System.out.println("   - 추가한 순서대로");
        System.out.println("   - 이중 연결 리스트\n");
        
        System.out.println("3. 성능");
        System.out.println("   - put: O(1)");
        System.out.println("   - get: O(1)");
        System.out.println("   - HashMap보다 약간 느림\n");
        
        System.out.println("4. null 허용");
        System.out.println("   - Key: null 1개");
        System.out.println("   - Value: null 여러 개");
    }
}
```

### 1.2 HashMap vs LinkedHashMap

```java
public class HashMapVsLinkedHashMap {
    public static void main(String[] args) {
        // HashMap (순서 없음)
        Map<String, Integer> hashMap = new HashMap<>();
        hashMap.put("C", 3);
        hashMap.put("A", 1);
        hashMap.put("B", 2);
        
        System.out.println("HashMap: " + hashMap);
        // 순서 무작위
        
        
        // LinkedHashMap (순서 유지)
        Map<String, Integer> linkedHashMap = new LinkedHashMap<>();
        linkedHashMap.put("C", 3);
        linkedHashMap.put("A", 1);
        linkedHashMap.put("B", 2);
        
        System.out.println("LinkedHashMap: " + linkedHashMap);
        // {C=3, A=1, B=2}
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("HashMap: 빠름, 순서X");
        System.out.println("LinkedHashMap: 약간 느림, 순서O");
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class LinkedHashMapCreation {
    public static void main(String[] args) {
        // 기본 생성 (삽입 순서)
        Map<String, Integer> map1 = new LinkedHashMap<>();
        System.out.println("기본: " + map1);
        
        
        // 초기 용량 지정
        Map<String, Integer> map2 = new LinkedHashMap<>(100);
        System.out.println("용량 100: " + map2);
        
        
        // 다른 Map으로
        Map<String, Integer> map3 = new LinkedHashMap<>();
        map3.put("A", 1);
        map3.put("B", 2);
        
        Map<String, Integer> map4 = new LinkedHashMap<>(map3);
        System.out.println("복사: " + map4);
        
        
        // Map 인터페이스로 (권장)
        Map<String, Integer> map5 = new LinkedHashMap<>();
        System.out.println("Map 타입: " + map5);
    }
}
```

### 2.2 초기화 방법들

```java
public class LinkedHashMapInit {
    public static void main(String[] args) {
        // 방법 1: put (순서대로)
        Map<String, Integer> map1 = new LinkedHashMap<>();
        map1.put("Apple", 1000);
        map1.put("Banana", 1500);
        map1.put("Cherry", 2000);
        System.out.println("put: " + map1);
        // {Apple=1000, Banana=1500, Cherry=2000}
        
        
        // 방법 2: Map.of + LinkedHashMap
        Map<String, Integer> map2 = new LinkedHashMap<>(
            Map.of("C", 3, "A", 1, "B", 2)
        );
        System.out.println("Map.of: " + map2);
        // 순서 유지 안 될 수 있음 (Map.of의 순서는 보장 안 됨)
        
        
        // 방법 3: Stream
        Map<String, Integer> map3 = Stream.of(
            new AbstractMap.SimpleEntry<>("First", 1),
            new AbstractMap.SimpleEntry<>("Second", 2),
            new AbstractMap.SimpleEntry<>("Third", 3)
        ).collect(Collectors.toMap(
            Map.Entry::getKey,
            Map.Entry::getValue,
            (e1, e2) -> e1,
            LinkedHashMap::new
        ));
        System.out.println("Stream: " + map3);
        // {First=1, Second=2, Third=3}
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (put)

```java
public class LinkedHashMapPut {
    public static void main(String[] args) {
        Map<String, Integer> map = new LinkedHashMap<>();
        
        // 순서대로 추가
        map.put("First", 1);
        map.put("Second", 2);
        map.put("Third", 3);
        
        System.out.println("추가 후: " + map);
        // {First=1, Second=2, Third=3}
        
        
        // 중간에 추가 (끝에 추가됨)
        map.put("Fourth", 4);
        System.out.println("Fourth 추가: " + map);
        
        
        // 기존 Key 수정 (순서 유지)
        map.put("Second", 22);
        System.out.println("Second 수정: " + map);
        // {First=1, Second=22, Third=3, Fourth=4}
        
        
        // putIfAbsent
        map.putIfAbsent("Fifth", 5);
        map.putIfAbsent("First", 99);  // 무시
        System.out.println("putIfAbsent: " + map);
    }
}
```

### 3.2 삭제 (remove)

```java
public class LinkedHashMapRemove {
    public static void main(String[] args) {
        Map<String, Integer> map = new LinkedHashMap<>();
        map.put("A", 1);
        map.put("B", 2);
        map.put("C", 3);
        map.put("D", 4);
        map.put("E", 5);
        
        System.out.println("원본: " + map);
        // {A=1, B=2, C=3, D=4, E=5}
        
        
        // 중간 삭제
        map.remove("C");
        System.out.println("C 삭제: " + map);
        // {A=1, B=2, D=4, E=5}
        
        
        // 첫 요소 삭제
        map.remove("A");
        System.out.println("A 삭제: " + map);
        // {B=2, D=4, E=5}
    }
}
```

---

## 4. 순서 유지

### 4.1 삽입 순서 확인

```java
public class InsertionOrder {
    public static void main(String[] args) {
        Map<Integer, String> map = new LinkedHashMap<>();
        
        // 랜덤하게 추가
        map.put(5, "Five");
        map.put(2, "Two");
        map.put(8, "Eight");
        map.put(1, "One");
        
        System.out.println("삽입 순서: " + map);
        // {5=Five, 2=Two, 8=Eight, 1=One}
        
        
        // 기존 Key 수정 (순서 변화 없음)
        map.put(2, "TWO");
        System.out.println("수정 후: " + map);
        // {5=Five, 2=TWO, 8=Eight, 1=One}
        
        
        // 삭제 후 재추가 (끝에 추가)
        map.remove(2);
        System.out.println("2 삭제: " + map);
        
        map.put(2, "Two");
        System.out.println("2 재추가: " + map);
        // {5=Five, 8=Eight, 1=One, 2=Two}
    }
}
```

### 4.2 순회 순서

```java
public class IterationOrder {
    public static void main(String[] args) {
        Map<String, Integer> map = new LinkedHashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        map.put("Durian", 2500);
        
        // 순회 = 삽입 순서
        System.out.println("=== EntrySet 순회 ===");
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
        // Apple, Banana, Cherry, Durian
        
        
        // KeySet도 순서 유지
        System.out.println("\n=== KeySet 순회 ===");
        for (String key : map.keySet()) {
            System.out.println(key);
        }
    }
}
```

---

## 5. 순회 방법

### 5.1 EntrySet 순회

```java
public class LinkedHashMapEntrySet {
    public static void main(String[] args) {
        Map<String, Integer> map = new LinkedHashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        
        // EntrySet (순서 보장)
        System.out.println("=== EntrySet ===");
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
    }
}
```

### 5.2 forEach와 Stream

```java
public class LinkedHashMapForEach {
    public static void main(String[] args) {
        Map<String, Integer> map = new LinkedHashMap<>();
        map.put("First", 1);
        map.put("Second", 2);
        map.put("Third", 3);
        
        // forEach (순서 보장)
        System.out.println("=== forEach ===");
        map.forEach((key, value) -> {
            System.out.println(key + ": " + value);
        });
        
        
        // Stream (순서 보장)
        System.out.println("\n=== Stream ===");
        map.entrySet().stream()
           .forEach(e -> System.out.println(e.getKey() + ": " + e.getValue()));
    }
}
```

---

## 6. 액세스 순서 모드

### 6.1 액세스 순서 생성

```java
public class AccessOrderMode {
    public static void main(String[] args) {
        // 삽입 순서 (기본)
        Map<String, Integer> insertionOrder = new LinkedHashMap<>();
        insertionOrder.put("A", 1);
        insertionOrder.put("B", 2);
        insertionOrder.put("C", 3);
        
        insertionOrder.get("A");  // 조회
        System.out.println("삽입 순서: " + insertionOrder);
        // {A=1, B=2, C=3} (변화 없음)
        
        
        // 액세스 순서 (true)
        Map<String, Integer> accessOrder = new LinkedHashMap<>(16, 0.75f, true);
        accessOrder.put("A", 1);
        accessOrder.put("B", 2);
        accessOrder.put("C", 3);
        
        accessOrder.get("A");  // 조회 → A가 끝으로 이동
        System.out.println("액세스 순서: " + accessOrder);
        // {B=2, C=3, A=1}
    }
}
```

### 6.2 LRU 캐시 구현

```java
public class LRUCacheWithLinkedHashMap {
    static class LRUCache<K, V> extends LinkedHashMap<K, V> {
        private final int capacity;
        
        public LRUCache(int capacity) {
            super(capacity, 0.75f, true);  // 액세스 순서
            this.capacity = capacity;
        }
        
        @Override
        protected boolean removeEldestEntry(Map.Entry<K, V> eldest) {
            return size() > capacity;
        }
    }
    
    public static void main(String[] args) {
        LRUCache<Integer, String> cache = new LRUCache<>(3);
        
        cache.put(1, "One");
        cache.put(2, "Two");
        cache.put(3, "Three");
        System.out.println("Cache: " + cache);
        // {1=One, 2=Two, 3=Three}
        
        
        cache.get(1);  // 1 사용
        System.out.println("1 사용 후: " + cache);
        // {2=Two, 3=Three, 1=One}
        
        
        cache.put(4, "Four");  // 2 제거됨 (가장 오래됨)
        System.out.println("4 추가 후: " + cache);
        // {3=Three, 1=One, 4=Four}
    }
}
```

---

## 7. 내부 구조와 성능

### 7.1 내부 구조

```java
public class LinkedHashMapStructure {
    public static void main(String[] args) {
        System.out.println("=== LinkedHashMap 구조 ===\n");
        
        System.out.println("내부: HashMap + 이중 연결 리스트");
        System.out.println("Entry: before/after 참조 추가");
        System.out.println("순서: 연결 리스트로 유지\n");
        
        System.out.println("=== 메모리 ===");
        System.out.println("HashMap보다 많음");
        System.out.println("이유: before/after 참조\n");
        
        System.out.println("=== 특징 ===");
        System.out.println("해시: 빠른 검색");
        System.out.println("연결: 순서 유지");
    }
}
```

### 7.2 시간 복잡도

```java
public class LinkedHashMapComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("put:    O(1)");
        System.out.println("  - 해시 + 연결 추가\n");
        
        System.out.println("get:    O(1)");
        System.out.println("  - 해시 탐색\n");
        
        System.out.println("remove: O(1)");
        System.out.println("  - 해시 + 연결 제거\n");
        
        System.out.println("순회:   O(n)");
        System.out.println("  - 연결 리스트 순회\n");
        
        System.out.println("HashMap보다 약간 느림");
        System.out.println("하지만 여전히 O(1)");
    }
}
```

### 7.3 성능 비교

```java
public class PerformanceComparison {
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
        
        
        System.out.println("HashMap:       " + time1 + "ms");
        System.out.println("LinkedHashMap: " + time2 + "ms");
        System.out.println("\nLinkedHashMap이 약간 느림");
    }
}
```

---

## 8. 실전 활용

### 8.1 순서가 중요한 설정

```java
public class ConfigManager {
    private Map<String, String> config = new LinkedHashMap<>();
    
    public void load() {
        config.put("host", "localhost");
        config.put("port", "8080");
        config.put("username", "admin");
        config.put("password", "1234");
    }
    
    public void display() {
        System.out.println("=== Configuration ===");
        config.forEach((key, value) -> {
            System.out.println(key + " = " + value);
        });
    }
    
    public static void main(String[] args) {
        ConfigManager manager = new ConfigManager();
        manager.load();
        manager.display();
        // 추가한 순서대로 출력
    }
}
```

### 8.2 방문 기록

```java
public class VisitHistory {
    private Map<String, Long> history = new LinkedHashMap<>();
    
    public void visit(String page) {
        history.put(page, System.currentTimeMillis());
    }
    
    public void showHistory() {
        System.out.println("=== 방문 기록 ===");
        history.forEach((page, time) -> {
            System.out.println(page + " - " + new Date(time));
        });
    }
    
    public static void main(String[] args) throws InterruptedException {
        VisitHistory history = new VisitHistory();
        
        history.visit("Home");
        Thread.sleep(100);
        history.visit("About");
        Thread.sleep(100);
        history.visit("Products");
        
        history.showHistory();
        // 방문 순서대로
    }
}
```

### 8.3 최근 검색어

```java
public class RecentSearches {
    private Map<String, Integer> searches;
    private static final int MAX_SIZE = 5;
    
    public RecentSearches() {
        this.searches = new LinkedHashMap<String, Integer>(
            MAX_SIZE, 0.75f, false
        ) {
            @Override
            protected boolean removeEldestEntry(Map.Entry<String, Integer> eldest) {
                return size() > MAX_SIZE;
            }
        };
    }
    
    public void search(String query) {
        // 이미 있으면 제거 (순서 갱신)
        searches.remove(query);
        searches.put(query, searches.getOrDefault(query, 0) + 1);
    }
    
    public void display() {
        System.out.println("=== 최근 검색어 ===");
        List<String> list = new ArrayList<>(searches.keySet());
        Collections.reverse(list);  // 최신순
        
        for (String query : list) {
            System.out.println(query + " (" + searches.get(query) + "회)");
        }
    }
    
    public static void main(String[] args) {
        RecentSearches recent = new RecentSearches();
        
        recent.search("Java");
        recent.search("Python");
        recent.search("JavaScript");
        recent.search("Java");  // 중복
        recent.search("C++");
        recent.search("Kotlin");
        recent.search("Go");  // 5개 초과
        
        recent.display();
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 단어 첫 등장 순서와 빈도

```java
// 단어가 첫 등장한 순서와 빈도수
public class Problem1 {
    public static Map<String, Integer> wordFrequency(String text) {
        // 순서 유지 + 빈도 카운트
        return null;
    }
    
    public static void main(String[] args) {
        String text = "apple banana apple cherry banana apple";
        Map<String, Integer> freq = wordFrequency(text);
        
        System.out.println("단어 빈도 (첫 등장 순서):");
        freq.forEach((word, count) -> {
            System.out.println(word + ": " + count);
        });
        // apple: 3, banana: 2, cherry: 1
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static Map<String, Integer> wordFrequency(String text) {
        Map<String, Integer> freq = new LinkedHashMap<>();
        
        for (String word : text.split("\\s+")) {
            freq.put(word, freq.getOrDefault(word, 0) + 1);
        }
        
        return freq;
    }
}
```
</details>

---

### 문제 2: JSON 순서 유지

```java
// JSON 객체처럼 순서가 중요한 데이터
public class Problem2 {
    static class JSONObject {
        private Map<String, Object> data;
        
        public JSONObject() {
            // 어떤 Map?
        }
        
        public void put(String key, Object value) {
            data.put(key, value);
        }
        
        public String toJSON() {
            // 순서대로 JSON 문자열 생성
            return null;
        }
    }
    
    public static void main(String[] args) {
        JSONObject obj = new JSONObject();
        obj.put("name", "Alice");
        obj.put("age", 25);
        obj.put("city", "Seoul");
        
        System.out.println(obj.toJSON());
        // {"name":"Alice","age":25,"city":"Seoul"}
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    static class JSONObject {
        private Map<String, Object> data;
        
        public JSONObject() {
            this.data = new LinkedHashMap<>();
        }
        
        public void put(String key, Object value) {
            data.put(key, value);
        }
        
        public String toJSON() {
            StringBuilder sb = new StringBuilder("{");
            
            int count = 0;
            for (Map.Entry<String, Object> entry : data.entrySet()) {
                if (count > 0) sb.append(",");
                
                sb.append("\"").append(entry.getKey()).append("\":");
                
                Object value = entry.getValue();
                if (value instanceof String) {
                    sb.append("\"").append(value).append("\"");
                } else {
                    sb.append(value);
                }
                
                count++;
            }
            
            sb.append("}");
            return sb.toString();
        }
    }
}
```
</details>

---

### 문제 3: 간단한 LRU 캐시

```java
// LinkedHashMap으로 LRU 캐시 구현
public class Problem3 {
    static class SimpleLRUCache extends LinkedHashMap<String, String> {
        private int capacity;
        
        public SimpleLRUCache(int capacity) {
            // 액세스 순서 모드로 생성
        }
        
        @Override
        protected boolean removeEldestEntry(Map.Entry<String, String> eldest) {
            // capacity 초과 시 제거
            return false;
        }
    }
    
    public static void main(String[] args) {
        SimpleLRUCache cache = new SimpleLRUCache(3);
        
        cache.put("A", "Value A");
        cache.put("B", "Value B");
        cache.put("C", "Value C");
        System.out.println("초기: " + cache);
        
        cache.get("A");  // A 사용
        System.out.println("A 사용: " + cache);
        
        cache.put("D", "Value D");  // B 제거됨
        System.out.println("D 추가: " + cache);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    static class SimpleLRUCache extends LinkedHashMap<String, String> {
        private int capacity;
        
        public SimpleLRUCache(int capacity) {
            super(capacity, 0.75f, true);  // 액세스 순서
            this.capacity = capacity;
        }
        
        @Override
        protected boolean removeEldestEntry(Map.Entry<String, String> eldest) {
            boolean shouldRemove = size() > capacity;
            if (shouldRemove) {
                System.out.println("제거: " + eldest.getKey());
            }
            return shouldRemove;
        }
    }
    
    public static void main(String[] args) {
        SimpleLRUCache cache = new SimpleLRUCache(3);
        
        cache.put("A", "Value A");
        cache.put("B", "Value B");
        cache.put("C", "Value C");
        System.out.println("초기: " + cache.keySet());
        // [A, B, C]
        
        cache.get("A");  // A 사용
        System.out.println("A 사용: " + cache.keySet());
        // [B, C, A]
        
        cache.put("D", "Value D");  // B 제거됨
        System.out.println("D 추가: " + cache.keySet());
        // [C, A, D]
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// Key-Value + 순서 유지
Map<String, Integer> map = new LinkedHashMap<>();
map.put("C", 3);
map.put("A", 1);
map.put("B", 2);
System.out.println(map);  // {C=3, A=1, B=2}
```

### 주요 메서드
```java
// 추가/삭제 (HashMap과 동일)
map.put("A", 1);        // O(1)
map.remove("A");        // O(1)

// 조회
map.get("A");           // O(1)
map.containsKey("A");   // O(1)

// 순회 (순서 보장!)
for (Map.Entry<K, V> e : map.entrySet()) {
    System.out.println(e);
}
```

### 시간 복잡도
```
put:    O(1)
get:    O(1)
remove: O(1)
순회:   O(n)
```

### 액세스 순서 모드
```java
// LRU 캐시 구현
Map<K, V> cache = new LinkedHashMap<>(
    capacity, 0.75f, true  // 액세스 순서
);
```

### HashMap vs LinkedHashMap
```
HashMap:
- 더 빠름
- 순서 없음
- 메모리 적음

LinkedHashMap:
- 약간 느림
- 순서 유지 ✅
- 메모리 많음
```

### 사용 시기
```
✅ 순서 유지 + Key-Value
✅ 삽입 순서가 중요
✅ LRU 캐시 구현
✅ 예측 가능한 순회
❌ 순서 불필요 (HashMap)
❌ 정렬 필요 (TreeMap)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 09. HashMap](./Collections-09-HashMap.md) | [다음: 11. TreeMap →](./Collections-11-TreeMap.md)**

</div>
