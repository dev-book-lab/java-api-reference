# Collections 09. HashMap 완전 정복

> 해시 기반 맵 - HashMap 마스터하기  
> Java API Reference

---

## 📑 목차

1. [HashMap 기본](#1-hashmap-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [조회와 검색](#4-조회와-검색)
5. [순회 방법](#5-순회-방법)
6. [고급 메서드](#6-고급-메서드)
7. [내부 구조와 성능](#7-내부-구조와-성능)
8. [실전 활용](#8-실전-활용)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. HashMap 기본

### 1.1 특징

```java
import java.util.*;

public class HashMapBasic {
    public static void main(String[] args) {
        System.out.println("=== HashMap 특징 ===\n");
        
        System.out.println("1. Key-Value 쌍");
        System.out.println("   - Key는 중복 불가");
        System.out.println("   - Value는 중복 가능\n");
        
        System.out.println("2. 순서 없음");
        System.out.println("   - 삽입 순서 보장 안 함");
        System.out.println("   - 해시값에 따라 저장\n");
        
        System.out.println("3. 성능");
        System.out.println("   - put: O(1)");
        System.out.println("   - get: O(1)");
        System.out.println("   - remove: O(1)\n");
        
        System.out.println("4. null 허용");
        System.out.println("   - Key: null 1개");
        System.out.println("   - Value: null 여러 개");
    }
}
```

### 1.2 기본 사용 예제

```java
public class HashMapExample {
    public static void main(String[] args) {
        // 학생 번호 -> 이름
        Map<Integer, String> students = new HashMap<>();
        
        students.put(101, "Alice");
        students.put(102, "Bob");
        students.put(103, "Charlie");
        
        System.out.println("Students: " + students);
        
        
        // 조회
        String name = students.get(102);
        System.out.println("102번: " + name);
        
        
        // 수정 (같은 Key로 put)
        students.put(102, "Robert");
        System.out.println("수정 후: " + students);
        
        
        // 삭제
        students.remove(103);
        System.out.println("삭제 후: " + students);
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class HashMapCreation {
    public static void main(String[] args) {
        // 기본 생성 (초기 용량 16)
        Map<String, Integer> map1 = new HashMap<>();
        System.out.println("기본: " + map1);
        
        
        // 초기 용량 지정
        Map<String, Integer> map2 = new HashMap<>(100);
        System.out.println("용량 100: " + map2);
        
        
        // 다른 Map으로
        Map<String, Integer> map3 = new HashMap<>(map1);
        System.out.println("복사: " + map3);
        
        
        // Map 인터페이스로 (권장)
        Map<String, Integer> map4 = new HashMap<>();
        System.out.println("Map 타입: " + map4);
    }
}
```

### 2.2 초기화 방법들

```java
public class HashMapInit {
    public static void main(String[] args) {
        // 방법 1: put
        Map<String, Integer> map1 = new HashMap<>();
        map1.put("Apple", 1000);
        map1.put("Banana", 1500);
        System.out.println("put: " + map1);
        
        
        // 방법 2: 이중 중괄호 (비권장)
        Map<String, Integer> map2 = new HashMap<>() {{
            put("Apple", 1000);
            put("Banana", 1500);
        }};
        System.out.println("이중 중괄호: " + map2);
        
        
        // 방법 3: Map.of (Java 9+, 불변)
        Map<String, Integer> immutable = Map.of(
            "Apple", 1000,
            "Banana", 1500,
            "Cherry", 2000
        );
        Map<String, Integer> map3 = new HashMap<>(immutable);
        System.out.println("Map.of: " + map3);
        
        
        // 방법 4: Map.ofEntries (10개 이상)
        Map<String, Integer> map4 = new HashMap<>(
            Map.ofEntries(
                Map.entry("A", 1),
                Map.entry("B", 2),
                Map.entry("C", 3)
            )
        );
        System.out.println("Map.ofEntries: " + map4);
        
        
        // 방법 5: Stream
        Map<String, Integer> map5 = Stream.of(
            new String[][] {
                {"Apple", "1000"},
                {"Banana", "1500"}
            }
        ).collect(Collectors.toMap(
            data -> data[0],
            data -> Integer.parseInt(data[1])
        ));
        System.out.println("Stream: " + map5);
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (put)

```java
public class HashMapPut {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        
        // 추가 (반환값 = 이전 값)
        Integer prev1 = map.put("Apple", 1000);
        System.out.println("Apple 추가: " + prev1);  // null
        System.out.println("Map: " + map);
        
        
        // 수정 (같은 Key)
        Integer prev2 = map.put("Apple", 1200);
        System.out.println("Apple 수정: " + prev2);  // 1000
        System.out.println("Map: " + map);
        
        
        // putIfAbsent (없을 때만 추가)
        map.putIfAbsent("Banana", 1500);
        map.putIfAbsent("Apple", 9999);  // 무시됨
        System.out.println("putIfAbsent: " + map);
        
        
        // putAll (여러 개 추가)
        Map<String, Integer> other = new HashMap<>();
        other.put("Cherry", 2000);
        other.put("Durian", 2500);
        
        map.putAll(other);
        System.out.println("putAll: " + map);
    }
}
```

### 3.2 삭제 (remove)

```java
public class HashMapRemove {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        
        System.out.println("원본: " + map);
        
        
        // Key로 삭제 (반환값 = 삭제된 값)
        Integer removed = map.remove("Banana");
        System.out.println("삭제된 값: " + removed);
        System.out.println("Map: " + map);
        
        
        // Key-Value 모두 일치해야 삭제
        boolean removed2 = map.remove("Apple", 1000);
        System.out.println("Apple 삭제: " + removed2);
        
        boolean removed3 = map.remove("Cherry", 9999);
        System.out.println("Cherry 삭제 실패: " + removed3);
        
        System.out.println("Map: " + map);
        
        
        // 전체 삭제
        map.clear();
        System.out.println("clear: " + map);
    }
}
```

---

## 4. 조회와 검색

### 4.1 조회 (get)

```java
public class HashMapGet {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        
        // 조회
        Integer price1 = map.get("Apple");
        System.out.println("Apple: " + price1);
        
        
        // 없는 Key (null 반환)
        Integer price2 = map.get("Durian");
        System.out.println("Durian: " + price2);  // null
        
        
        // getOrDefault (기본값 제공)
        Integer price3 = map.getOrDefault("Durian", 0);
        System.out.println("Durian (기본값): " + price3);  // 0
        
        
        // null Key/Value
        map.put(null, 9999);
        map.put("Unknown", null);
        
        System.out.println("null Key: " + map.get(null));
        System.out.println("null Value: " + map.get("Unknown"));
    }
}
```

### 4.2 검색 (containsKey, containsValue)

```java
public class HashMapContains {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        
        // Key 존재 확인
        boolean hasApple = map.containsKey("Apple");
        boolean hasDurian = map.containsKey("Durian");
        
        System.out.println("Apple 있음? " + hasApple);   // true
        System.out.println("Durian 있음? " + hasDurian); // false
        
        
        // Value 존재 확인 (느림 - O(n))
        boolean has1500 = map.containsValue(1500);
        boolean has9999 = map.containsValue(9999);
        
        System.out.println("1500 있음? " + has1500);  // true
        System.out.println("9999 있음? " + has9999);  // false
        
        
        // 크기와 비어있는지
        System.out.println("size: " + map.size());
        System.out.println("isEmpty: " + map.isEmpty());
    }
}
```

---

## 5. 순회 방법

### 5.1 KeySet 순회

```java
public class HashMapKeySet {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        
        // KeySet으로 순회
        System.out.println("=== KeySet ===");
        for (String key : map.keySet()) {
            Integer value = map.get(key);
            System.out.println(key + ": " + value);
        }
        
        
        // Key만 필요할 때
        System.out.println("\n=== Key만 ===");
        for (String key : map.keySet()) {
            System.out.println(key);
        }
    }
}
```

### 5.2 EntrySet 순회 (권장)

```java
public class HashMapEntrySet {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        
        // EntrySet (가장 효율적)
        System.out.println("=== EntrySet ===");
        for (Map.Entry<String, Integer> entry : map.entrySet()) {
            String key = entry.getKey();
            Integer value = entry.getValue();
            System.out.println(key + ": " + value);
            
            // 수정도 가능
            entry.setValue(value + 100);
        }
        
        System.out.println("\n수정 후: " + map);
    }
}
```

### 5.3 Values 순회

```java
public class HashMapValues {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        
        // Values만 순회
        System.out.println("=== Values ===");
        for (Integer value : map.values()) {
            System.out.println(value);
        }
        
        
        // 합계 계산
        int sum = 0;
        for (Integer value : map.values()) {
            sum += value;
        }
        System.out.println("합계: " + sum);
    }
}
```

### 5.4 forEach와 Stream

```java
public class HashMapForEach {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        map.put("Cherry", 2000);
        
        // forEach (Java 8+)
        System.out.println("=== forEach ===");
        map.forEach((key, value) -> {
            System.out.println(key + ": " + value);
        });
        
        
        // Stream
        System.out.println("\n=== Stream (필터) ===");
        map.entrySet().stream()
           .filter(e -> e.getValue() >= 1500)
           .forEach(e -> System.out.println(e.getKey() + ": " + e.getValue()));
    }
}
```

---

## 6. 고급 메서드

### 6.1 compute 계열

```java
public class HashMapCompute {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        
        // compute (Key로 값 계산)
        map.compute("Apple", (key, value) -> value + 100);
        System.out.println("compute: " + map);
        
        
        // computeIfPresent (있을 때만)
        map.computeIfPresent("Apple", (key, value) -> value * 2);
        System.out.println("computeIfPresent: " + map);
        
        
        // computeIfAbsent (없을 때만)
        map.computeIfAbsent("Banana", key -> 1500);
        map.computeIfAbsent("Apple", key -> 9999);  // 무시
        System.out.println("computeIfAbsent: " + map);
    }
}
```

### 6.2 merge

```java
public class HashMapMerge {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        
        // merge (있으면 병합, 없으면 추가)
        map.merge("Apple", 500, (oldVal, newVal) -> oldVal + newVal);
        System.out.println("Apple merge: " + map);  // 1500
        
        map.merge("Banana", 1500, (oldVal, newVal) -> oldVal + newVal);
        System.out.println("Banana merge: " + map);  // 1500 (새로 추가)
        
        
        // 단어 빈도 카운트 예제
        String text = "apple banana apple cherry banana apple";
        Map<String, Integer> frequency = new HashMap<>();
        
        for (String word : text.split(" ")) {
            frequency.merge(word, 1, Integer::sum);
        }
        
        System.out.println("\n빈도: " + frequency);
    }
}
```

### 6.3 replace

```java
public class HashMapReplace {
    public static void main(String[] args) {
        Map<String, Integer> map = new HashMap<>();
        map.put("Apple", 1000);
        map.put("Banana", 1500);
        
        // replace (Key가 있을 때만)
        map.replace("Apple", 1200);
        map.replace("Cherry", 2000);  // 무시됨
        System.out.println("replace: " + map);
        
        
        // replace (이전 값 확인)
        boolean replaced1 = map.replace("Apple", 1200, 1300);
        boolean replaced2 = map.replace("Apple", 9999, 1400);
        
        System.out.println("replaced1: " + replaced1);  // true
        System.out.println("replaced2: " + replaced2);  // false
        System.out.println("Map: " + map);
        
        
        // replaceAll (모든 값 변경)
        map.replaceAll((key, value) -> value + 100);
        System.out.println("replaceAll: " + map);
    }
}
```

---

## 7. 내부 구조와 성능

### 7.1 해시 테이블 구조

```java
public class HashMapStructure {
    public static void main(String[] args) {
        System.out.println("=== HashMap 내부 구조 ===\n");
        
        System.out.println("구조: 배열 + 연결 리스트/트리");
        System.out.println("버킷: 배열의 각 슬롯");
        System.out.println("충돌: 같은 버킷에 여러 Entry\n");
        
        System.out.println("=== 충돌 처리 ===");
        System.out.println("1. hashCode() 계산");
        System.out.println("2. 버킷 인덱스 결정");
        System.out.println("3. 충돌 시 연결 리스트");
        System.out.println("4. 많은 충돌 시 트리 변환 (8개 이상)\n");
        
        System.out.println("=== Load Factor ===");
        System.out.println("기본: 0.75");
        System.out.println("75% 차면 2배 확장");
    }
}
```

### 7.2 hashCode와 equals

```java
public class HashMapEquality {
    static class Product {
        String name;
        int price;
        
        Product(String name, int price) {
            this.name = name;
            this.price = price;
        }
        
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Product product = (Product) o;
            return name.equals(product.name);
        }
        
        @Override
        public int hashCode() {
            return Objects.hash(name);
        }
        
        @Override
        public String toString() {
            return name + "(" + price + ")";
        }
    }
    
    public static void main(String[] args) {
        Map<Product, Integer> stock = new HashMap<>();
        
        Product p1 = new Product("Apple", 1000);
        Product p2 = new Product("Apple", 1200);  // 같은 이름
        
        stock.put(p1, 100);
        stock.put(p2, 50);  // 덮어쓰기 (같은 Key)
        
        System.out.println("Stock: " + stock);
        System.out.println("Size: " + stock.size());  // 1
    }
}
```

### 7.3 시간 복잡도

```java
public class HashMapComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("put:           O(1) 평균");
        System.out.println("  - hashCode 계산");
        System.out.println("  - 버킷 찾기\n");
        
        System.out.println("get:           O(1) 평균");
        System.out.println("  - hashCode 계산");
        System.out.println("  - 버킷 탐색\n");
        
        System.out.println("remove:        O(1) 평균");
        System.out.println("  - get + 삭제\n");
        
        System.out.println("containsKey:   O(1) 평균");
        System.out.println("containsValue: O(n)");
        System.out.println("  - 전체 순회 필요\n");
        
        System.out.println("최악: O(n) - 모든 Entry 충돌");
    }
}
```

---

## 8. 실전 활용

### 8.1 빈도 카운트

```java
public class FrequencyCount {
    public static void main(String[] args) {
        String text = "apple banana apple cherry banana apple";
        String[] words = text.split(" ");
        
        Map<String, Integer> frequency = new HashMap<>();
        
        for (String word : words) {
            frequency.put(word, frequency.getOrDefault(word, 0) + 1);
        }
        
        System.out.println("빈도: " + frequency);
        
        
        // 가장 많이 나온 단어
        String mostFrequent = "";
        int maxCount = 0;
        
        for (Map.Entry<String, Integer> entry : frequency.entrySet()) {
            if (entry.getValue() > maxCount) {
                mostFrequent = entry.getKey();
                maxCount = entry.getValue();
            }
        }
        
        System.out.println("가장 많음: " + mostFrequent + " (" + maxCount + "번)");
    }
}
```

### 8.2 그룹화

```java
public class Grouping {
    static class Student {
        String name;
        String major;
        
        Student(String name, String major) {
            this.name = name;
            this.major = major;
        }
        
        @Override
        public String toString() {
            return name;
        }
    }
    
    public static void main(String[] args) {
        List<Student> students = Arrays.asList(
            new Student("Alice", "CS"),
            new Student("Bob", "Math"),
            new Student("Charlie", "CS"),
            new Student("David", "Math"),
            new Student("Eve", "CS")
        );
        
        // 전공별 그룹화
        Map<String, List<Student>> byMajor = new HashMap<>();
        
        for (Student student : students) {
            byMajor.computeIfAbsent(student.major, k -> new ArrayList<>())
                   .add(student);
        }
        
        System.out.println("전공별 학생:");
        byMajor.forEach((major, studentList) -> {
            System.out.println(major + ": " + studentList);
        });
    }
}
```

### 8.3 캐시 구현

```java
public class SimpleCache {
    private Map<String, String> cache = new HashMap<>();
    
    public String get(String key) {
        if (cache.containsKey(key)) {
            System.out.println("Cache hit: " + key);
            return cache.get(key);
        }
        
        System.out.println("Cache miss: " + key);
        String value = fetchFromDB(key);
        cache.put(key, value);
        return value;
    }
    
    private String fetchFromDB(String key) {
        // 실제로는 DB 조회
        return "Data for " + key;
    }
    
    public static void main(String[] args) {
        SimpleCache cache = new SimpleCache();
        
        cache.get("user1");  // miss
        cache.get("user2");  // miss
        cache.get("user1");  // hit!
        cache.get("user2");  // hit!
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 두 배열의 교집합

```java
// 두 배열에서 공통 원소 찾기
public class Problem1 {
    public static int[] intersection(int[] nums1, int[] nums2) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] nums1 = {1, 2, 2, 3, 4};
        int[] nums2 = {2, 2, 3, 5};
        
        int[] result = intersection(nums1, nums2);
        System.out.println("교집합: " + Arrays.toString(result));
        // [2, 3]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static int[] intersection(int[] nums1, int[] nums2) {
        Map<Integer, Integer> map = new HashMap<>();
        
        // nums1 빈도
        for (int num : nums1) {
            map.put(num, map.getOrDefault(num, 0) + 1);
        }
        
        // 교집합
        List<Integer> result = new ArrayList<>();
        for (int num : nums2) {
            if (map.containsKey(num) && map.get(num) > 0) {
                result.add(num);
                map.put(num, map.get(num) - 1);
            }
        }
        
        return result.stream().mapToInt(i -> i).toArray();
    }
    
    // Set 버전 (중복 제거)
    public static int[] intersectionUnique(int[] nums1, int[] nums2) {
        Set<Integer> set1 = new HashSet<>();
        for (int num : nums1) set1.add(num);
        
        Set<Integer> result = new HashSet<>();
        for (int num : nums2) {
            if (set1.contains(num)) {
                result.add(num);
            }
        }
        
        return result.stream().mapToInt(i -> i).toArray();
    }
}
```
</details>

---

### 문제 2: 아나그램 그룹화

```java
// 아나그램끼리 그룹화
public class Problem2 {
    public static Map<String, List<String>> groupAnagrams(String[] words) {
        // 아나그램: 같은 문자로 구성 (eat, tea, ate)
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        String[] words = {"eat", "tea", "tan", "ate", "nat", "bat"};
        Map<String, List<String>> groups = groupAnagrams(words);
        
        System.out.println("그룹: " + groups);
        // {aet=[eat, tea, ate], ant=[tan, nat], abt=[bat]}
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static Map<String, List<String>> groupAnagrams(String[] words) {
        Map<String, List<String>> groups = new HashMap<>();
        
        for (String word : words) {
            // 정렬해서 Key로 사용
            char[] chars = word.toCharArray();
            Arrays.sort(chars);
            String key = new String(chars);
            
            groups.computeIfAbsent(key, k -> new ArrayList<>())
                  .add(word);
        }
        
        return groups;
    }
    
    // 출력용
    public static void main(String[] args) {
        String[] words = {"eat", "tea", "tan", "ate", "nat", "bat"};
        Map<String, List<String>> groups = groupAnagrams(words);
        
        System.out.println("=== 아나그램 그룹 ===");
        groups.forEach((key, group) -> {
            System.out.println(group);
        });
    }
}
```
</details>

---

### 문제 3: LRU 캐시

```java
// LRU (Least Recently Used) 캐시 구현
public class Problem3 {
    static class LRUCache {
        private int capacity;
        // HashMap + LinkedList 조합
        
        public LRUCache(int capacity) {
            // 코드 작성
        }
        
        public int get(int key) {
            // 코드 작성
            return -1;
        }
        
        public void put(int key, int value) {
            // 코드 작성
        }
    }
    
    public static void main(String[] args) {
        LRUCache cache = new LRUCache(2);
        
        cache.put(1, 1);
        cache.put(2, 2);
        System.out.println(cache.get(1));  // 1
        cache.put(3, 3);  // 2 제거됨
        System.out.println(cache.get(2));  // -1 (없음)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    static class LRUCache {
        private int capacity;
        private Map<Integer, Integer> map;
        private LinkedList<Integer> order;
        
        public LRUCache(int capacity) {
            this.capacity = capacity;
            this.map = new HashMap<>();
            this.order = new LinkedList<>();
        }
        
        public int get(int key) {
            if (!map.containsKey(key)) {
                return -1;
            }
            
            // 최근 사용으로 이동
            order.remove((Integer) key);
            order.addFirst(key);
            
            return map.get(key);
        }
        
        public void put(int key, int value) {
            if (map.containsKey(key)) {
                // 업데이트
                order.remove((Integer) key);
            } else if (map.size() >= capacity) {
                // 용량 초과 시 가장 오래된 것 제거
                int oldest = order.removeLast();
                map.remove(oldest);
            }
            
            map.put(key, value);
            order.addFirst(key);
        }
        
        public void print() {
            System.out.println("Cache: " + map);
            System.out.println("Order: " + order);
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// Key-Value 쌍, 순서 없음
Map<String, Integer> map = new HashMap<>();
map.put("Apple", 1000);
map.put("Apple", 1200);  // 덮어쓰기
System.out.println(map.get("Apple"));  // 1200
```

### 주요 메서드
```java
// 추가/삭제
map.put("A", 1);        // O(1)
map.remove("A");        // O(1)
map.clear();

// 조회
map.get("A");           // O(1)
map.getOrDefault("A", 0);
map.containsKey("A");   // O(1)
map.containsValue(1);   // O(n)

// 순회
for (String key : map.keySet()) { }
for (Map.Entry<K, V> e : map.entrySet()) { }
map.forEach((k, v) -> { });

// 고급
map.putIfAbsent("A", 1);
map.computeIfAbsent("A", k -> 1);
map.merge("A", 1, Integer::sum);
```

### 시간 복잡도
```
put:          O(1) 평균
get:          O(1) 평균
remove:       O(1) 평균
containsKey:  O(1) 평균
containsValue: O(n)
```

### 사용 시기
```
✅ Key-Value 저장
✅ 빠른 검색 (Key)
✅ 빈도 카운트
✅ 캐시 구현
❌ 순서 유지 (LinkedHashMap)
❌ 정렬 (TreeMap)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 08. Set 비교](./Collections-08-SetComparison.md) | [다음: 10. LinkedHashMap →](./Collections-10-LinkedHashMap.md)**

</div>
