# Collections 08. Set 비교와 선택

> HashSet vs LinkedHashSet vs TreeSet - 완벽 비교 가이드  
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
        System.out.println("=== Set 구현체 비교 ===\n");
        
        System.out.println("HashSet:");
        System.out.println("  순서: 없음");
        System.out.println("  정렬: 없음");
        System.out.println("  성능: O(1)");
        System.out.println("  null: 허용 (1개)\n");
        
        System.out.println("LinkedHashSet:");
        System.out.println("  순서: 삽입 순서");
        System.out.println("  정렬: 없음");
        System.out.println("  성능: O(1)");
        System.out.println("  null: 허용 (1개)\n");
        
        System.out.println("TreeSet:");
        System.out.println("  순서: 정렬 순서");
        System.out.println("  정렬: 자동");
        System.out.println("  성능: O(log n)");
        System.out.println("  null: 불가");
    }
}
```

### 1.2 순서 차이

```java
public class OrderComparison {
    public static void main(String[] args) {
        List<Integer> data = Arrays.asList(5, 2, 8, 1, 9, 3);
        
        // HashSet (무작위)
        Set<Integer> hashSet = new HashSet<>(data);
        System.out.println("HashSet:       " + hashSet);
        // 순서 예측 불가
        
        
        // LinkedHashSet (삽입 순서)
        Set<Integer> linkedHashSet = new LinkedHashSet<>(data);
        System.out.println("LinkedHashSet: " + linkedHashSet);
        // [5, 2, 8, 1, 9, 3]
        
        
        // TreeSet (정렬)
        Set<Integer> treeSet = new TreeSet<>(data);
        System.out.println("TreeSet:       " + treeSet);
        // [1, 2, 3, 5, 8, 9]
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
        
        System.out.println("연산        HashSet    LinkedHashSet    TreeSet");
        System.out.println("-----------------------------------------------");
        System.out.println("add         O(1)       O(1)             O(log n)");
        System.out.println("contains    O(1)       O(1)             O(log n)");
        System.out.println("remove      O(1)       O(1)             O(log n)");
        System.out.println("순회        O(n)       O(n)             O(n)");
        System.out.println("first/last  X          X                O(log n)");
        System.out.println("range       X          X                O(log n)");
        
        System.out.println("\n=== 결론 ===");
        System.out.println("HashSet: 가장 빠름");
        System.out.println("LinkedHashSet: 약간 느림");
        System.out.println("TreeSet: 느림 (하지만 정렬!)");
    }
}
```

### 2.2 추가 성능

```java
public class AddPerformance {
    public static void main(String[] args) {
        int n = 100000;
        
        // HashSet
        Set<Integer> hashSet = new HashSet<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            hashSet.add(i);
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // LinkedHashSet
        Set<Integer> linkedHashSet = new LinkedHashSet<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            linkedHashSet.add(i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        // TreeSet
        Set<Integer> treeSet = new TreeSet<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            treeSet.add(i);
        }
        long time3 = System.currentTimeMillis() - start;
        
        
        System.out.println("=== " + n + "개 추가 ===");
        System.out.println("HashSet:       " + time1 + "ms");
        System.out.println("LinkedHashSet: " + time2 + "ms");
        System.out.println("TreeSet:       " + time3 + "ms");
    }
}
```

### 2.3 검색 성능

```java
public class SearchPerformance {
    public static void main(String[] args) {
        int n = 100000;
        
        // 데이터 준비
        Set<Integer> hashSet = new HashSet<>();
        Set<Integer> linkedHashSet = new LinkedHashSet<>();
        Set<Integer> treeSet = new TreeSet<>();
        
        for (int i = 0; i < n; i++) {
            hashSet.add(i);
            linkedHashSet.add(i);
            treeSet.add(i);
        }
        
        
        // HashSet 검색
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            hashSet.contains(i);
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // LinkedHashSet 검색
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            linkedHashSet.contains(i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        // TreeSet 검색
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            treeSet.contains(i);
        }
        long time3 = System.currentTimeMillis() - start;
        
        
        System.out.println("=== " + n + "번 검색 ===");
        System.out.println("HashSet:       " + time1 + "ms");
        System.out.println("LinkedHashSet: " + time2 + "ms");
        System.out.println("TreeSet:       " + time3 + "ms");
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
        
        System.out.println("HashSet:");
        System.out.println("  구조: 해시 테이블");
        System.out.println("  오버헤드: 중간\n");
        
        System.out.println("LinkedHashSet:");
        System.out.println("  구조: 해시 + 연결 리스트");
        System.out.println("  오버헤드: 큼 (prev/next 참조)\n");
        
        System.out.println("TreeSet:");
        System.out.println("  구조: 레드-블랙 트리");
        System.out.println("  오버헤드: 중간-큼 (parent/left/right)\n");
        
        System.out.println("=== 순서 ===");
        System.out.println("HashSet < TreeSet < LinkedHashSet");
    }
}
```

---

## 4. 선택 가이드

### 4.1 선택 플로우

```java
public class SelectionGuide {
    public static void main(String[] args) {
        System.out.println("=== Set 선택 가이드 ===\n");
        
        System.out.println("Q1. 정렬이 필요한가?");
        System.out.println("  YES → TreeSet");
        System.out.println("    - 자동 정렬");
        System.out.println("    - 범위 연산");
        System.out.println("    - 최소/최대 접근\n");
        
        System.out.println("  NO → Q2로\n");
        
        System.out.println("Q2. 순서 유지가 필요한가?");
        System.out.println("  YES → LinkedHashSet");
        System.out.println("    - 삽입 순서");
        System.out.println("    - 예측 가능한 순회\n");
        
        System.out.println("  NO → HashSet");
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
        
        System.out.println("기능             HashSet  LinkedHashSet  TreeSet");
        System.out.println("------------------------------------------------");
        System.out.println("중복 제거        O        O              O");
        System.out.println("null 허용        O        O              X");
        System.out.println("순서 유지        X        O              O (정렬)");
        System.out.println("성능 (일반)      최고     중간           낮음");
        System.out.println("메모리           중간     많음           중간");
        System.out.println("first/last       X        X              O");
        System.out.println("범위 연산        X        X              O");
        System.out.println("근접 값 찾기     X        X              O");
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
            testAdd(size);
            testContains(size);
            testIterate(size);
        }
    }
    
    static void testAdd(int n) {
        long t1 = measureAdd(new HashSet<>(), n);
        long t2 = measureAdd(new LinkedHashSet<>(), n);
        long t3 = measureAdd(new TreeSet<>(), n);
        
        System.out.println("Add - HS: " + t1 + "ms, LHS: " + t2 + "ms, TS: " + t3 + "ms");
    }
    
    static void testContains(int n) {
        Set<Integer> hs = new HashSet<>();
        Set<Integer> lhs = new LinkedHashSet<>();
        Set<Integer> ts = new TreeSet<>();
        
        for (int i = 0; i < n; i++) {
            hs.add(i);
            lhs.add(i);
            ts.add(i);
        }
        
        long t1 = measureContains(hs, n);
        long t2 = measureContains(lhs, n);
        long t3 = measureContains(ts, n);
        
        System.out.println("Contains - HS: " + t1 + "ms, LHS: " + t2 + "ms, TS: " + t3 + "ms");
    }
    
    static void testIterate(int n) {
        Set<Integer> hs = new HashSet<>();
        Set<Integer> lhs = new LinkedHashSet<>();
        Set<Integer> ts = new TreeSet<>();
        
        for (int i = 0; i < n; i++) {
            hs.add(i);
            lhs.add(i);
            ts.add(i);
        }
        
        long t1 = measureIterate(hs);
        long t2 = measureIterate(lhs);
        long t3 = measureIterate(ts);
        
        System.out.println("Iterate - HS: " + t1 + "ms, LHS: " + t2 + "ms, TS: " + t3 + "ms");
    }
    
    static long measureAdd(Set<Integer> set, int n) {
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) set.add(i);
        return System.currentTimeMillis() - start;
    }
    
    static long measureContains(Set<Integer> set, int n) {
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) set.contains(i);
        return System.currentTimeMillis() - start;
    }
    
    static long measureIterate(Set<Integer> set) {
        long start = System.currentTimeMillis();
        for (int num : set) { }
        return System.currentTimeMillis() - start;
    }
}
```

---

## 6. 사용 사례

### 6.1 HashSet 사례

```java
public class HashSetUseCases {
    public static void main(String[] args) {
        System.out.println("=== HashSet 사용 사례 ===\n");
        
        // 1. 중복 확인
        Set<String> usernames = new HashSet<>();
        usernames.add("alice");
        usernames.add("bob");
        
        boolean exists = usernames.contains("alice");
        System.out.println("1. 사용자명 중복: " + exists + "\n");
        
        
        // 2. 고유 방문자
        Set<String> visitors = new HashSet<>();
        visitors.add("192.168.1.1");
        visitors.add("192.168.1.2");
        visitors.add("192.168.1.1");  // 중복
        
        System.out.println("2. 고유 방문자: " + visitors.size() + "\n");
        
        
        // 3. 태그 시스템
        Set<String> tags = new HashSet<>();
        tags.add("java");
        tags.add("programming");
        tags.add("tutorial");
        
        System.out.println("3. 태그: " + tags);
    }
}
```

### 6.2 LinkedHashSet 사례

```java
public class LinkedHashSetUseCases {
    public static void main(String[] args) {
        System.out.println("=== LinkedHashSet 사용 사례 ===\n");
        
        // 1. 방문 기록 (순서 유지)
        Set<String> history = new LinkedHashSet<>();
        history.add("page1");
        history.add("page2");
        history.add("page1");  // 이미 있으니 무시
        history.add("page3");
        
        System.out.println("1. 방문 순서: " + history + "\n");
        
        
        // 2. 추천 시스템 (순서 중요)
        Set<String> recommendations = new LinkedHashSet<>();
        recommendations.add("Product A");
        recommendations.add("Product B");
        recommendations.add("Product C");
        
        System.out.println("2. 추천 순서: " + recommendations + "\n");
        
        
        // 3. 재생 목록
        Set<String> playlist = new LinkedHashSet<>();
        playlist.add("Song 1");
        playlist.add("Song 2");
        playlist.add("Song 3");
        
        System.out.println("3. 재생 목록: " + playlist);
    }
}
```

### 6.3 TreeSet 사례

```java
public class TreeSetUseCases {
    public static void main(String[] args) {
        System.out.println("=== TreeSet 사용 사례 ===\n");
        
        // 1. 리더보드 (자동 정렬)
        TreeSet<Integer> leaderboard = new TreeSet<>(
            Collections.reverseOrder()
        );
        leaderboard.add(95);
        leaderboard.add(87);
        leaderboard.add(92);
        
        System.out.println("1. 상위 점수: " + leaderboard + "\n");
        
        
        // 2. 단어 사전 (알파벳 순)
        TreeSet<String> dictionary = new TreeSet<>();
        dictionary.add("apple");
        dictionary.add("banana");
        dictionary.add("cherry");
        
        System.out.println("2. 사전: " + dictionary + "\n");
        
        
        // 3. 우선순위 큐
        TreeSet<Integer> priorities = new TreeSet<>();
        priorities.add(5);  // 낮은 우선순위
        priorities.add(1);  // 높은 우선순위
        priorities.add(3);
        
        System.out.println("3. 다음 작업: " + priorities.first());
    }
}
```

---

## 7. 실전 연습 문제

### 문제 1: 적절한 Set 선택

```java
// 각 상황에 맞는 Set 선택
public class Problem1 {
    public static void main(String[] args) {
        System.out.println("=== Set 선택 문제 ===\n");
        
        System.out.println("1. 사용자 ID 중복 확인 (순서 불필요)");
        System.out.println("2. 최근 검색어 5개 (순서 유지)");
        System.out.println("3. 성적 상위 10명 (정렬)");
        System.out.println("4. 고유 IP 추출 (빠른 속도)");
        System.out.println("5. 카테고리 목록 (알파벳 순)");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void main(String[] args) {
        // 1. HashSet - 빠른 중복 확인
        Set<String> userIds = new HashSet<>();
        
        // 2. LinkedHashSet - 순서 유지
        Set<String> recentSearches = new LinkedHashSet<>();
        
        // 3. TreeSet - 자동 정렬
        TreeSet<Integer> topScores = new TreeSet<>(
            Collections.reverseOrder()
        );
        
        // 4. HashSet - 가장 빠름
        Set<String> uniqueIPs = new HashSet<>();
        
        // 5. TreeSet - 알파벳 정렬
        Set<String> categories = new TreeSet<>();
        
        System.out.println("1. HashSet");
        System.out.println("2. LinkedHashSet");
        System.out.println("3. TreeSet");
        System.out.println("4. HashSet");
        System.out.println("5. TreeSet");
    }
}
```
</details>

---

### 문제 2: 성능 차이 체험

```java
// 세 Set의 성능 차이 직접 측정
public class Problem2 {
    public static void measurePerformance(int n) {
        // HashSet, LinkedHashSet, TreeSet
        // 1. 추가 성능
        // 2. 검색 성능
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
        Set<Integer> hs = new HashSet<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) hs.add(i);
        long t1 = System.currentTimeMillis() - start;
        
        Set<Integer> lhs = new LinkedHashSet<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) lhs.add(i);
        long t2 = System.currentTimeMillis() - start;
        
        Set<Integer> ts = new TreeSet<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ts.add(i);
        long t3 = System.currentTimeMillis() - start;
        
        System.out.println("추가:");
        System.out.println("  HashSet:       " + t1 + "ms");
        System.out.println("  LinkedHashSet: " + t2 + "ms");
        System.out.println("  TreeSet:       " + t3 + "ms");
        System.out.println("  Winner: HashSet\n");
        
        
        // 2. 검색
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) hs.contains(i);
        t1 = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) lhs.contains(i);
        t2 = System.currentTimeMillis() - start;
        
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) ts.contains(i);
        t3 = System.currentTimeMillis() - start;
        
        System.out.println("검색:");
        System.out.println("  HashSet:       " + t1 + "ms");
        System.out.println("  LinkedHashSet: " + t2 + "ms");
        System.out.println("  TreeSet:       " + t3 + "ms");
        System.out.println("  Winner: HashSet");
    }
}
```
</details>

---

### 문제 3: 최적 Set 구현

```java
// 상황별 최적 Set 구현
public class Problem3 {
    // 고유 단어 추출 (빠른 속도)
    public static Set<String> extractUniqueWords(String text) {
        // 어떤 Set?
        return null;
    }
    
    // 최근 5개 검색어 (순서 유지)
    public static class RecentSearches {
        private Set<String> searches;
        
        public RecentSearches() {
            // 어떤 Set?
        }
        
        public void add(String query) {
            searches.add(query);
            // 5개 유지 로직
        }
    }
    
    // 실시간 랭킹 Top 10 (정렬)
    public static class TopRanking {
        private TreeSet<Integer> scores;
        
        public TopRanking() {
            // 어떤 정렬?
        }
        
        public void addScore(int score) {
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
    // HashSet - 빠른 중복 제거
    public static Set<String> extractUniqueWords(String text) {
        Set<String> words = new HashSet<>();
        for (String word : text.split("\\s+")) {
            words.add(word.toLowerCase());
        }
        return words;
    }
    
    // LinkedHashSet - 순서 유지
    public static class RecentSearches {
        private LinkedHashSet<String> searches;
        private static final int MAX_SIZE = 5;
        
        public RecentSearches() {
            this.searches = new LinkedHashSet<>();
        }
        
        public void add(String query) {
            // 이미 있으면 제거 (순서 갱신)
            searches.remove(query);
            searches.add(query);
            
            // 5개 초과 시 가장 오래된 것 제거
            if (searches.size() > MAX_SIZE) {
                Iterator<String> it = searches.iterator();
                it.next();
                it.remove();
            }
        }
        
        public List<String> getRecent() {
            return new ArrayList<>(searches);
        }
    }
    
    // TreeSet - 자동 정렬
    public static class TopRanking {
        private TreeSet<Integer> scores;
        private static final int MAX_SIZE = 10;
        
        public TopRanking() {
            this.scores = new TreeSet<>(Collections.reverseOrder());
        }
        
        public void addScore(int score) {
            scores.add(score);
            
            // 10개 초과 시 최하위 제거
            if (scores.size() > MAX_SIZE) {
                scores.pollLast();
            }
        }
        
        public List<Integer> getTop10() {
            return new ArrayList<>(scores);
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 비교표
```
특성          HashSet    LinkedHashSet    TreeSet
-----------------------------------------------
순서          없음       삽입 순서        정렬 순서
성능          O(1)       O(1)             O(log n)
메모리        중간       많음             중간
null          O          O                X
first/last    X          X                O
범위 연산     X          X                O
```

### 선택 기준
```java
// 99% 경우: HashSet
Set<String> set = new HashSet<>();

// 순서 유지 필요: LinkedHashSet
Set<String> ordered = new LinkedHashSet<>();

// 정렬 필요: TreeSet
Set<Integer> sorted = new TreeSet<>();
```

### 일반 권장사항
```
대부분: HashSet ✅
- 가장 빠름
- 메모리 효율적
- 순서 불필요

순서 중요: LinkedHashSet
- 삽입 순서 유지
- 예측 가능한 순회

정렬 필요: TreeSet
- 자동 정렬
- 범위 연산
- 최소/최대
```

---

## 🎉 Set 시리즈 완료!

<div align="center">

### 📚 Set 시리즈

|                        Chapter                         | 주제 |
|:------------------------------------------------------:|------|
|       [05. HashSet](./Collections-05-HashSet.md)       | 해시 기반 집합 |
| [06. LinkedHashSet](./Collections-06-LinkedHashSet.md) | 순서 유지 집합 |
|       [07. TreeSet](./Collections-07-TreeSet.md)       | 정렬된 집합 |
|    [08. Set 비교](./Collections-08-SetComparison.md)     | 성능, 선택 가이드 |

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 07. TreeSet](./Collections-07-TreeSet.md)**

</div>
