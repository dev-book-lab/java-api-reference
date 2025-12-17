# Collections 11. TreeMap 완전 정복

> 정렬된 맵 - TreeMap 마스터하기  
> Java API Reference

---

## 📑 목차

1. [TreeMap 기본](#1-treemap-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [정렬과 탐색](#4-정렬과-탐색)
5. [범위 연산](#5-범위-연산)
6. [순회 방법](#6-순회-방법)
7. [내부 구조와 성능](#7-내부-구조와-성능)
8. [실전 활용](#8-실전-활용)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. TreeMap 기본

### 1.1 특징

```java
import java.util.*;

public class TreeMapBasic {
    public static void main(String[] args) {
        System.out.println("=== TreeMap 특징 ===\n");
        
        System.out.println("1. Key-Value 쌍");
        System.out.println("   - Key 중복 불가\n");
        
        System.out.println("2. 자동 정렬");
        System.out.println("   - Key 기준 오름차순");
        System.out.println("   - Comparable/Comparator\n");
        
        System.out.println("3. 성능");
        System.out.println("   - put: O(log n)");
        System.out.println("   - get: O(log n)");
        System.out.println("   - remove: O(log n)\n");
        
        System.out.println("4. null 불가");
        System.out.println("   - Key에 null 불가");
        System.out.println("   - Value는 null 가능");
    }
}
```

### 1.2 자동 정렬 예제

```java
public class AutoSort {
    public static void main(String[] args) {
        // TreeMap (자동 정렬)
        Map<Integer, String> treeMap = new TreeMap<>();
        treeMap.put(5, "Five");
        treeMap.put(2, "Two");
        treeMap.put(8, "Eight");
        treeMap.put(1, "One");
        
        System.out.println("TreeMap: " + treeMap);
        // {1=One, 2=Two, 5=Five, 8=Eight}
        
        
        // HashMap (순서 없음)
        Map<Integer, String> hashMap = new HashMap<>();
        hashMap.put(5, "Five");
        hashMap.put(2, "Two");
        hashMap.put(8, "Eight");
        hashMap.put(1, "One");
        
        System.out.println("HashMap: " + hashMap);
        // 무작위
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class TreeMapCreation {
    public static void main(String[] args) {
        // 기본 생성 (자연 순서)
        TreeMap<Integer, String> map1 = new TreeMap<>();
        System.out.println("기본: " + map1);
        
        
        // 다른 Map으로
        TreeMap<Integer, String> map2 = new TreeMap<>();
        map2.put(3, "Three");
        map2.put(1, "One");
        map2.put(2, "Two");
        
        TreeMap<Integer, String> map3 = new TreeMap<>(map2);
        System.out.println("복사: " + map3);
        // {1=One, 2=Two, 3=Three}
        
        
        // Comparator로 (역순)
        TreeMap<Integer, String> map4 = new TreeMap<>(
            Collections.reverseOrder()
        );
        map4.put(1, "One");
        map4.put(2, "Two");
        map4.put(3, "Three");
        System.out.println("역순: " + map4);
        // {3=Three, 2=Two, 1=One}
        
        
        // SortedMap 타입 (권장)
        SortedMap<Integer, String> map5 = new TreeMap<>();
        System.out.println("SortedMap: " + map5);
    }
}
```

### 2.2 커스텀 정렬

```java
public class CustomSort {
    static class Person {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        // 나이순 정렬
        TreeMap<Person, String> map1 = new TreeMap<>(
            Comparator.comparingInt(p -> p.age)
        );
        
        map1.put(new Person("Alice", 25), "Engineer");
        map1.put(new Person("Bob", 20), "Student");
        map1.put(new Person("Charlie", 30), "Manager");
        
        System.out.println("나이순:");
        map1.forEach((person, job) -> {
            System.out.println(person + ": " + job);
        });
        
        
        // 이름순 정렬
        TreeMap<Person, String> map2 = new TreeMap<>(
            Comparator.comparing(p -> p.name)
        );
        
        map2.putAll(map1);
        
        System.out.println("\n이름순:");
        map2.forEach((person, job) -> {
            System.out.println(person + ": " + job);
        });
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (put)

```java
public class TreeMapPut {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        
        // 추가 (자동 정렬)
        map.put(5, "Five");
        map.put(2, "Two");
        map.put(8, "Eight");
        
        System.out.println("추가 후: " + map);
        // {2=Two, 5=Five, 8=Eight}
        
        
        // 중복 Key (덮어쓰기)
        map.put(5, "FIVE");
        System.out.println("수정 후: " + map);
        // {2=Two, 5=FIVE, 8=Eight}
        
        
        // putIfAbsent
        map.putIfAbsent(1, "One");
        map.putIfAbsent(5, "Five");  // 무시
        System.out.println("putIfAbsent: " + map);
        
        
        // null Key (에러!)
        try {
            map.put(null, "Null");
        } catch (NullPointerException e) {
            System.out.println("\nnull Key 불가!");
        }
    }
}
```

### 3.2 삭제 (remove)

```java
public class TreeMapRemove {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        map.put(1, "One");
        map.put(2, "Two");
        map.put(3, "Three");
        map.put(4, "Four");
        map.put(5, "Five");
        
        System.out.println("원본: " + map);
        
        
        // Key로 삭제
        map.remove(3);
        System.out.println("3 삭제: " + map);
        
        
        // 첫 Entry 삭제
        Map.Entry<Integer, String> first = map.pollFirstEntry();
        System.out.println("pollFirst: " + first);
        System.out.println("After: " + map);
        
        
        // 마지막 Entry 삭제
        Map.Entry<Integer, String> last = map.pollLastEntry();
        System.out.println("pollLast: " + last);
        System.out.println("After: " + map);
    }
}
```

---

## 4. 정렬과 탐색

### 4.1 첫/마지막 Entry

```java
public class TreeMapFirstLast {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        map.put(5, "Five");
        map.put(2, "Two");
        map.put(8, "Eight");
        map.put(1, "One");
        
        System.out.println("Map: " + map);
        // {1=One, 2=Two, 5=Five, 8=Eight}
        
        
        // 첫/마지막 Key
        Integer firstKey = map.firstKey();
        Integer lastKey = map.lastKey();
        
        System.out.println("First key: " + firstKey);  // 1
        System.out.println("Last key: " + lastKey);    // 8
        
        
        // 첫/마지막 Entry
        Map.Entry<Integer, String> firstEntry = map.firstEntry();
        Map.Entry<Integer, String> lastEntry = map.lastEntry();
        
        System.out.println("First entry: " + firstEntry);
        System.out.println("Last entry: " + lastEntry);
    }
}
```

### 4.2 근접 Key 찾기

```java
public class TreeMapNearest {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        map.put(1, "One");
        map.put(3, "Three");
        map.put(5, "Five");
        map.put(7, "Seven");
        map.put(9, "Nine");
        
        System.out.println("Map: " + map);
        
        
        // lowerKey: 작은 Key 중 가장 큰 것
        System.out.println("\nlowerKey(5): " + map.lowerKey(5));  // 3
        System.out.println("lowerKey(6): " + map.lowerKey(6));    // 5
        
        
        // floorKey: 작거나 같은 Key 중 가장 큰 것
        System.out.println("\nfloorKey(5): " + map.floorKey(5));  // 5
        System.out.println("floorKey(6): " + map.floorKey(6));    // 5
        
        
        // ceilingKey: 크거나 같은 Key 중 가장 작은 것
        System.out.println("\nceilingKey(5): " + map.ceilingKey(5));  // 5
        System.out.println("ceilingKey(6): " + map.ceilingKey(6));    // 7
        
        
        // higherKey: 큰 Key 중 가장 작은 것
        System.out.println("\nhigherKey(5): " + map.higherKey(5));  // 7
        System.out.println("higherKey(6): " + map.higherKey(6));    // 7
    }
}
```

---

## 5. 범위 연산

### 5.1 부분 맵 (subMap)

```java
public class TreeMapSubMap {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        for (int i = 1; i <= 10; i++) {
            map.put(i, "Value" + i);
        }
        
        System.out.println("전체: " + map);
        
        
        // subMap (from 포함, to 미포함)
        SortedMap<Integer, String> sub1 = map.subMap(3, 8);
        System.out.println("subMap(3, 8): " + sub1);
        // {3=Value3, 4=Value4, ..., 7=Value7}
        
        
        // subMap (포함 여부 지정)
        NavigableMap<Integer, String> sub2 = map.subMap(3, true, 8, true);
        System.out.println("subMap(3-8 포함): " + sub2);
        // {3=Value3, 4=Value4, ..., 8=Value8}
        
        
        // headMap (처음부터 to 미포함)
        SortedMap<Integer, String> head = map.headMap(5);
        System.out.println("headMap(5): " + head);
        // {1=Value1, 2=Value2, 3=Value3, 4=Value4}
        
        
        // tailMap (from 포함부터 끝까지)
        SortedMap<Integer, String> tail = map.tailMap(7);
        System.out.println("tailMap(7): " + tail);
        // {7=Value7, 8=Value8, 9=Value9, 10=Value10}
    }
}
```

### 5.2 범위 내 Entry 처리

```java
public class TreeMapRangeOps {
    public static void main(String[] args) {
        TreeMap<Integer, Integer> scores = new TreeMap<>();
        scores.put(85, 1);
        scores.put(92, 2);
        scores.put(78, 3);
        scores.put(95, 4);
        scores.put(88, 5);
        
        System.out.println("전체: " + scores);
        
        
        // 80~90점 사이
        NavigableMap<Integer, Integer> range = scores.subMap(80, true, 90, true);
        System.out.println("80~90점: " + range);
        
        
        // 90점 이상
        NavigableMap<Integer, Integer> high = scores.tailMap(90, true);
        System.out.println("90점 이상: " + high);
        
        
        // 합계
        int sum = range.values().stream()
                      .mapToInt(Integer::intValue)
                      .sum();
        System.out.println("80~90점 합계: " + sum);
    }
}
```

---

## 6. 순회 방법

### 6.1 오름차순 순회

```java
public class TreeMapAscending {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        map.put(5, "Five");
        map.put(2, "Two");
        map.put(8, "Eight");
        map.put(1, "One");
        
        // EntrySet (오름차순)
        System.out.println("=== 오름차순 ===");
        for (Map.Entry<Integer, String> entry : map.entrySet()) {
            System.out.println(entry.getKey() + ": " + entry.getValue());
        }
        // 1, 2, 5, 8
        
        
        // KeySet
        System.out.println("\n=== KeySet ===");
        for (Integer key : map.keySet()) {
            System.out.println(key);
        }
    }
}
```

### 6.2 내림차순 순회

```java
public class TreeMapDescending {
    public static void main(String[] args) {
        TreeMap<Integer, String> map = new TreeMap<>();
        map.put(5, "Five");
        map.put(2, "Two");
        map.put(8, "Eight");
        map.put(1, "One");
        
        // descendingMap (내림차순 뷰)
        System.out.println("=== 내림차순 Map ===");
        NavigableMap<Integer, String> desc = map.descendingMap();
        System.out.println(desc);
        // {8=Eight, 5=Five, 2=Two, 1=One}
        
        
        // descendingKeySet
        System.out.println("\n=== descendingKeySet ===");
        for (Integer key : map.descendingKeySet()) {
            System.out.println(key + ": " + map.get(key));
        }
    }
}
```

---

## 7. 내부 구조와 성능

### 7.1 레드-블랙 트리

```java
public class TreeMapStructure {
    public static void main(String[] args) {
        System.out.println("=== TreeMap 구조 ===\n");
        
        System.out.println("내부: 레드-블랙 트리");
        System.out.println("특징: 자가 균형 이진 탐색 트리");
        System.out.println("높이: O(log n) 유지\n");
        
        System.out.println("=== 정렬 보장 ===");
        System.out.println("중위 순회 = 정렬 순서");
        System.out.println("Comparable 또는 Comparator\n");
        
        System.out.println("=== Entry 저장 ===");
        System.out.println("Key: 정렬 기준");
        System.out.println("Value: 함께 저장");
    }
}
```

### 7.2 시간 복잡도

```java
public class TreeMapComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("put:         O(log n)");
        System.out.println("  - 트리 탐색 + 삽입\n");
        
        System.out.println("get:         O(log n)");
        System.out.println("  - 이진 탐색\n");
        
        System.out.println("remove:      O(log n)");
        System.out.println("  - 탐색 + 삭제 + 균형\n");
        
        System.out.println("firstKey:    O(log n)");
        System.out.println("lastKey:     O(log n)\n");
        
        System.out.println("HashMap보다 느림");
        System.out.println("하지만 정렬 보장!");
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
        
        
        // TreeMap
        Map<Integer, String> treeMap = new TreeMap<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            treeMap.put(i, "Value" + i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        System.out.println("HashMap:  " + time1 + "ms");
        System.out.println("TreeMap:  " + time2 + "ms");
        System.out.println("\nTreeMap이 느림");
        System.out.println("하지만 정렬됨!");
    }
}
```

---

## 8. 실전 활용

### 8.1 순위표

```java
public class Leaderboard {
    private TreeMap<Integer, String> scores;
    
    public Leaderboard() {
        this.scores = new TreeMap<>(Collections.reverseOrder());
    }
    
    public void addScore(String player, int score) {
        scores.put(score, player);
    }
    
    public void showTop5() {
        System.out.println("=== Top 5 ===");
        int rank = 1;
        
        for (Map.Entry<Integer, String> entry : scores.entrySet()) {
            System.out.println(rank + ". " + entry.getValue() + 
                             " - " + entry.getKey() + "점");
            if (++rank > 5) break;
        }
    }
    
    public static void main(String[] args) {
        Leaderboard board = new Leaderboard();
        
        board.addScore("Alice", 95);
        board.addScore("Bob", 87);
        board.addScore("Charlie", 92);
        board.addScore("David", 88);
        board.addScore("Eve", 91);
        board.addScore("Frank", 85);
        
        board.showTop5();
    }
}
```

### 8.2 범위 쿼리

```java
public class RangeQuery {
    private TreeMap<Integer, String> data;
    
    public RangeQuery() {
        this.data = new TreeMap<>();
    }
    
    public void add(int key, String value) {
        data.put(key, value);
    }
    
    public List<String> getRange(int from, int to) {
        return new ArrayList<>(
            data.subMap(from, true, to, true).values()
        );
    }
    
    public String getNearest(int target) {
        // 가장 가까운 Key
        Integer floor = data.floorKey(target);
        Integer ceiling = data.ceilingKey(target);
        
        if (floor == null) return data.get(ceiling);
        if (ceiling == null) return data.get(floor);
        
        int key = (target - floor) <= (ceiling - target) ? floor : ceiling;
        return data.get(key);
    }
    
    public static void main(String[] args) {
        RangeQuery query = new RangeQuery();
        
        query.add(10, "Ten");
        query.add(20, "Twenty");
        query.add(30, "Thirty");
        query.add(40, "Forty");
        query.add(50, "Fifty");
        
        System.out.println("20~40: " + query.getRange(20, 40));
        System.out.println("35에 가까운 값: " + query.getNearest(35));
    }
}
```

### 8.3 이벤트 타임라인

```java
public class EventTimeline {
    private TreeMap<Long, String> events;
    
    public EventTimeline() {
        this.events = new TreeMap<>();
    }
    
    public void addEvent(long timestamp, String event) {
        events.put(timestamp, event);
    }
    
    public void showTimeline() {
        System.out.println("=== 타임라인 ===");
        events.forEach((time, event) -> {
            Date date = new Date(time);
            System.out.println(date + ": " + event);
        });
    }
    
    public List<String> getEventsBetween(long start, long end) {
        return new ArrayList<>(
            events.subMap(start, true, end, true).values()
        );
    }
    
    public static void main(String[] args) throws InterruptedException {
        EventTimeline timeline = new EventTimeline();
        
        long now = System.currentTimeMillis();
        
        timeline.addEvent(now, "Event 1");
        Thread.sleep(100);
        timeline.addEvent(now + 100, "Event 2");
        Thread.sleep(100);
        timeline.addEvent(now + 200, "Event 3");
        
        timeline.showTimeline();
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 빈도수 정렬

```java
// 단어 빈도를 빈도수 기준 정렬
public class Problem1 {
    public static Map<String, Integer> sortByFrequency(Map<String, Integer> freq) {
        // 빈도수 기준 정렬 (높은 순)
        return null;
    }
    
    public static void main(String[] args) {
        Map<String, Integer> freq = new HashMap<>();
        freq.put("apple", 3);
        freq.put("banana", 5);
        freq.put("cherry", 2);
        
        Map<String, Integer> sorted = sortByFrequency(freq);
        System.out.println("빈도순: " + sorted);
        // {banana=5, apple=3, cherry=2}
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static Map<String, Integer> sortByFrequency(Map<String, Integer> freq) {
        // Value 기준 역순 정렬
        TreeMap<Integer, String> sorted = new TreeMap<>(
            Collections.reverseOrder()
        );
        
        // Key-Value 뒤집어서 저장 (Value가 Key가 됨)
        for (Map.Entry<String, Integer> entry : freq.entrySet()) {
            sorted.put(entry.getValue(), entry.getKey());
        }
        
        // 다시 원래 형태로
        Map<String, Integer> result = new LinkedHashMap<>();
        for (Map.Entry<Integer, String> entry : sorted.entrySet()) {
            result.put(entry.getValue(), entry.getKey());
        }
        
        return result;
    }
    
    // Stream 버전
    public static Map<String, Integer> sortByFrequency2(Map<String, Integer> freq) {
        return freq.entrySet().stream()
            .sorted(Map.Entry.<String, Integer>comparingByValue().reversed())
            .collect(Collectors.toMap(
                Map.Entry::getKey,
                Map.Entry::getValue,
                (e1, e2) -> e1,
                LinkedHashMap::new
            ));
    }
}
```
</details>

---

### 문제 2: 범위 내 값 합계

```java
// 범위 내 값들의 합계
public class Problem2 {
    public static int rangeSum(TreeMap<Integer, Integer> map, int from, int to) {
        // 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        TreeMap<Integer, Integer> map = new TreeMap<>();
        map.put(1, 100);
        map.put(3, 300);
        map.put(5, 500);
        map.put(7, 700);
        map.put(9, 900);
        
        int sum = rangeSum(map, 3, 7);
        System.out.println("3~7 합계: " + sum);
        // 300 + 500 + 700 = 1500
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int rangeSum(TreeMap<Integer, Integer> map, int from, int to) {
        NavigableMap<Integer, Integer> range = map.subMap(from, true, to, true);
        
        return range.values().stream()
                   .mapToInt(Integer::intValue)
                   .sum();
    }
    
    // for 루프 버전
    public static int rangeSum2(TreeMap<Integer, Integer> map, int from, int to) {
        int sum = 0;
        for (Integer value : map.subMap(from, true, to, true).values()) {
            sum += value;
        }
        return sum;
    }
}
```
</details>

---

### 문제 3: K개의 가장 큰 Entry

```java
// 가장 큰 K개의 Entry
public class Problem3 {
    public static Map<Integer, String> topK(TreeMap<Integer, String> map, int k) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        TreeMap<Integer, String> scores = new TreeMap<>();
        scores.put(85, "Alice");
        scores.put(92, "Bob");
        scores.put(78, "Charlie");
        scores.put(95, "David");
        scores.put(88, "Eve");
        
        Map<Integer, String> top3 = topK(scores, 3);
        System.out.println("Top 3: " + top3);
        // {95=David, 92=Bob, 88=Eve}
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static Map<Integer, String> topK(TreeMap<Integer, String> map, int k) {
        Map<Integer, String> result = new LinkedHashMap<>();
        
        int count = 0;
        for (Map.Entry<Integer, String> entry : map.descendingMap().entrySet()) {
            result.put(entry.getKey(), entry.getValue());
            if (++count >= k) break;
        }
        
        return result;
    }
    
    // NavigableMap 버전
    public static Map<Integer, String> topK2(TreeMap<Integer, String> map, int k) {
        // 가장 큰 K번째 Key 찾기
        int count = 0;
        Integer kthKey = null;
        
        for (Integer key : map.descendingKeySet()) {
            if (++count == k) {
                kthKey = key;
                break;
            }
        }
        
        if (kthKey == null) return map;
        
        return new LinkedHashMap<>(map.tailMap(kthKey, true));
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// Key-Value + 자동 정렬
TreeMap<Integer, String> map = new TreeMap<>();
map.put(5, "Five");
map.put(2, "Two");
map.put(8, "Eight");
System.out.println(map);  // {2=Two, 5=Five, 8=Eight}
```

### 주요 메서드
```java
// 추가/삭제
map.put(5, "Five");     // O(log n)
map.remove(5);          // O(log n)

// 첫/마지막
map.firstKey();         // 최소 Key
map.lastKey();          // 최대 Key
map.pollFirstEntry();   // 최소 + 제거
map.pollLastEntry();    // 최대 + 제거

// 근접 Key
map.lowerKey(5);        // < 5
map.floorKey(5);        // <= 5
map.ceilingKey(5);      // >= 5
map.higherKey(5);       // > 5

// 범위
map.subMap(3, 8);       // [3, 8)
map.headMap(5);         // [처음, 5)
map.tailMap(7);         // [7, 끝]
```

### 시간 복잡도
```
put:      O(log n)
get:      O(log n)
remove:   O(log n)
firstKey: O(log n)
```

### 사용 시기
```
✅ Key 기준 자동 정렬
✅ 범위 쿼리
✅ 최소/최대 빠른 접근
✅ 순위 시스템
❌ 빠른 검색만 (HashMap)
❌ 순서 유지만 (LinkedHashMap)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 10. LinkedHashMap](./Collections-10-LinkedHashMap.md) | [다음: 12. Map 비교 →](./Collections-12-MapComparison.md)**

</div>
