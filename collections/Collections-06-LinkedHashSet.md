# Collections 06. LinkedHashSet 완전 정복

> 순서 유지 집합 - LinkedHashSet 마스터하기  
> Java API Reference

---

## 📑 목차

1. [LinkedHashSet 기본](#1-linkedhashset-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [순서 유지](#4-순서-유지)
5. [집합 연산](#5-집합-연산)
6. [순회 방법](#6-순회-방법)
7. [내부 구조와 성능](#7-내부-구조와-성능)
8. [실전 활용](#8-실전-활용)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. LinkedHashSet 기본

### 1.1 특징

```java
import java.util.*;

public class LinkedHashSetBasic {
    public static void main(String[] args) {
        System.out.println("=== LinkedHashSet 특징 ===\n");
        
        System.out.println("1. 중복 불가");
        System.out.println("   - HashSet과 동일\n");
        
        System.out.println("2. 삽입 순서 유지");
        System.out.println("   - 추가한 순서대로 저장");
        System.out.println("   - 이중 연결 리스트 사용\n");
        
        System.out.println("3. 성능");
        System.out.println("   - add: O(1)");
        System.out.println("   - contains: O(1)");
        System.out.println("   - HashSet보다 약간 느림\n");
        
        System.out.println("4. null 허용");
        System.out.println("   - null 1개만 저장 가능");
    }
}
```

### 1.2 HashSet vs LinkedHashSet

```java
public class HashSetVsLinkedHashSet {
    public static void main(String[] args) {
        // HashSet (순서 없음)
        Set<String> hashSet = new HashSet<>();
        hashSet.add("C");
        hashSet.add("A");
        hashSet.add("B");
        
        System.out.println("HashSet: " + hashSet);
        // 순서 무작위: [A, B, C] 또는 다른 순서
        
        
        // LinkedHashSet (순서 유지)
        Set<String> linkedHashSet = new LinkedHashSet<>();
        linkedHashSet.add("C");
        linkedHashSet.add("A");
        linkedHashSet.add("B");
        
        System.out.println("LinkedHashSet: " + linkedHashSet);
        // 삽입 순서: [C, A, B]
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("HashSet: 빠름, 순서X");
        System.out.println("LinkedHashSet: 약간 느림, 순서O");
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class LinkedHashSetCreation {
    public static void main(String[] args) {
        // 기본 생성
        Set<String> set1 = new LinkedHashSet<>();
        System.out.println("기본: " + set1);
        
        
        // 초기 용량 지정
        Set<String> set2 = new LinkedHashSet<>(100);
        System.out.println("용량 100: " + set2);
        
        
        // 다른 컬렉션으로
        Set<String> set3 = new LinkedHashSet<>(
            Arrays.asList("A", "B", "C")
        );
        System.out.println("복사: " + set3);
        
        
        // Set 인터페이스로 (권장)
        Set<String> set4 = new LinkedHashSet<>();
        System.out.println("Set 타입: " + set4);
    }
}
```

### 2.2 초기화 방법들

```java
public class LinkedHashSetInit {
    public static void main(String[] args) {
        // 방법 1: add (순서대로)
        Set<String> set1 = new LinkedHashSet<>();
        set1.add("Apple");
        set1.add("Banana");
        set1.add("Cherry");
        System.out.println("add: " + set1);
        // [Apple, Banana, Cherry]
        
        
        // 방법 2: Arrays.asList
        Set<String> set2 = new LinkedHashSet<>(
            Arrays.asList("C", "A", "B")
        );
        System.out.println("Arrays.asList: " + set2);
        // [C, A, B] (순서 유지)
        
        
        // 방법 3: addAll
        Set<String> set3 = new LinkedHashSet<>();
        set3.addAll(Arrays.asList("3", "1", "2"));
        System.out.println("addAll: " + set3);
        // [3, 1, 2]
        
        
        // 방법 4: Stream
        Set<Integer> set4 = Stream.of(5, 3, 1, 4, 2)
                                 .collect(Collectors.toCollection(
                                     LinkedHashSet::new));
        System.out.println("Stream: " + set4);
        // [5, 3, 1, 4, 2]
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (add)

```java
public class LinkedHashSetAdd {
    public static void main(String[] args) {
        Set<String> set = new LinkedHashSet<>();
        
        // 순서대로 추가
        set.add("First");
        set.add("Second");
        set.add("Third");
        
        System.out.println("추가 후: " + set);
        // [First, Second, Third]
        
        
        // 중복 추가 (무시되고 순서 변화 없음)
        set.add("First");
        System.out.println("중복 후: " + set);
        // [First, Second, Third] (변화 없음)
        
        
        // 중간에 추가 (끝에 추가됨)
        set.add("Fourth");
        System.out.println("Fourth 추가: " + set);
        // [First, Second, Third, Fourth]
        
        
        // null 추가
        set.add(null);
        System.out.println("null 추가: " + set);
    }
}
```

### 3.2 삭제 (remove)

```java
public class LinkedHashSetRemove {
    public static void main(String[] args) {
        Set<String> set = new LinkedHashSet<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        System.out.println("원본: " + set);
        // [A, B, C, D, E]
        
        
        // 중간 요소 삭제
        set.remove("C");
        System.out.println("C 삭제: " + set);
        // [A, B, D, E]
        
        
        // 첫 요소 삭제
        set.remove("A");
        System.out.println("A 삭제: " + set);
        // [B, D, E]
        
        
        // 여러 개 삭제
        set.removeAll(Arrays.asList("B", "D"));
        System.out.println("여러 개 삭제: " + set);
        // [E]
    }
}
```

---

## 4. 순서 유지

### 4.1 삽입 순서 확인

```java
public class InsertionOrder {
    public static void main(String[] args) {
        Set<Integer> set = new LinkedHashSet<>();
        
        // 랜덤하게 추가
        set.add(5);
        set.add(2);
        set.add(8);
        set.add(1);
        set.add(9);
        
        System.out.println("삽입 순서: " + set);
        // [5, 2, 8, 1, 9]
        
        
        // 중복 추가 (순서 변화 없음)
        set.add(2);
        set.add(8);
        System.out.println("중복 후: " + set);
        // [5, 2, 8, 1, 9] (변화 없음)
        
        
        // 삭제 후 재추가 (끝에 추가)
        set.remove(2);
        System.out.println("2 삭제: " + set);
        // [5, 8, 1, 9]
        
        set.add(2);
        System.out.println("2 재추가: " + set);
        // [5, 8, 1, 9, 2] (끝에 추가)
    }
}
```

### 4.2 순회 순서

```java
public class IterationOrder {
    public static void main(String[] args) {
        Set<String> set = new LinkedHashSet<>();
        set.add("Apple");
        set.add("Banana");
        set.add("Cherry");
        set.add("Durian");
        
        // 순회 = 삽입 순서
        System.out.println("=== 순회 ===");
        for (String fruit : set) {
            System.out.println(fruit);
        }
        // Apple, Banana, Cherry, Durian
        
        
        // Iterator도 순서 유지
        System.out.println("\n=== Iterator ===");
        Iterator<String> it = set.iterator();
        while (it.hasNext()) {
            System.out.println(it.next());
        }
    }
}
```

---

## 5. 집합 연산

### 5.1 합집합 (순서 유지)

```java
public class LinkedHashSetUnion {
    public static void main(String[] args) {
        Set<Integer> set1 = new LinkedHashSet<>(
            Arrays.asList(1, 2, 3)
        );
        
        Set<Integer> set2 = new LinkedHashSet<>(
            Arrays.asList(3, 4, 5)
        );
        
        // 합집합 (set1 순서 + set2의 새 요소)
        Set<Integer> union = new LinkedHashSet<>(set1);
        union.addAll(set2);
        
        System.out.println("Set1: " + set1);
        System.out.println("Set2: " + set2);
        System.out.println("Union: " + union);
        // [1, 2, 3, 4, 5]
    }
}
```

### 5.2 교집합 (순서 유지)

```java
public class LinkedHashSetIntersection {
    public static void main(String[] args) {
        Set<Integer> set1 = new LinkedHashSet<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        Set<Integer> set2 = new LinkedHashSet<>(
            Arrays.asList(4, 5, 6, 7, 8)
        );
        
        // 교집합 (set1의 순서 유지)
        Set<Integer> intersection = new LinkedHashSet<>(set1);
        intersection.retainAll(set2);
        
        System.out.println("Set1: " + set1);
        System.out.println("Set2: " + set2);
        System.out.println("Intersection: " + intersection);
        // [4, 5]
    }
}
```

---

## 6. 순회 방법

### 6.1 for 루프

```java
public class LinkedHashSetIteration {
    public static void main(String[] args) {
        Set<String> set = new LinkedHashSet<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 향상된 for (순서 보장)
        System.out.println("=== 향상된 for ===");
        for (String item : set) {
            System.out.print(item + " ");
        }
        System.out.println();
        // A B C D E
    }
}
```

### 6.2 Iterator

```java
public class LinkedHashSetIterator {
    public static void main(String[] args) {
        Set<String> set = new LinkedHashSet<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // Iterator (순서 보장)
        System.out.println("=== Iterator ===");
        Iterator<String> it = set.iterator();
        
        while (it.hasNext()) {
            System.out.print(it.next() + " ");
        }
        System.out.println();
        // A B C D E
    }
}
```

### 6.3 forEach와 Stream

```java
public class LinkedHashSetForEach {
    public static void main(String[] args) {
        Set<Integer> set = new LinkedHashSet<>(
            Arrays.asList(5, 3, 8, 1, 9)
        );
        
        // forEach (순서 보장)
        System.out.println("=== forEach ===");
        set.forEach(n -> System.out.print(n + " "));
        System.out.println();
        // 5 3 8 1 9
        
        
        // Stream (순서 보장)
        System.out.println("\n=== Stream ===");
        set.stream()
           .forEach(n -> System.out.print(n + " "));
        System.out.println();
        // 5 3 8 1 9
    }
}
```

---

## 7. 내부 구조와 성능

### 7.1 내부 구조

```java
public class LinkedHashSetStructure {
    public static void main(String[] args) {
        System.out.println("=== LinkedHashSet 구조 ===\n");
        
        System.out.println("내부: LinkedHashMap 사용");
        System.out.println("구조: HashTable + 이중 연결 리스트");
        System.out.println("노드: Entry (prev, next 포함)\n");
        
        System.out.println("=== 메모리 ===");
        System.out.println("HashSet보다 많음");
        System.out.println("이유: prev/next 참조 추가\n");
        
        System.out.println("=== 특징 ===");
        System.out.println("해시: 빠른 검색");
        System.out.println("연결: 순서 유지");
    }
}
```

### 7.2 시간 복잡도

```java
public class LinkedHashSetComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("add:       O(1)");
        System.out.println("  - 해시 + 연결 리스트\n");
        
        System.out.println("contains:  O(1)");
        System.out.println("  - 해시 탐색\n");
        
        System.out.println("remove:    O(1)");
        System.out.println("  - 해시 + 연결 제거\n");
        
        System.out.println("순회:      O(n)");
        System.out.println("  - 연결 리스트 순회\n");
        
        System.out.println("HashSet보다 약간 느림");
        System.out.println("하지만 여전히 O(1)");
    }
}
```

### 7.3 성능 비교

```java
public class PerformanceComparison {
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
        
        
        System.out.println("HashSet: " + time1 + "ms");
        System.out.println("LinkedHashSet: " + time2 + "ms");
        System.out.println("\nLinkedHashSet이 약간 느림");
        System.out.println("하지만 큰 차이 없음");
    }
}
```

---

## 8. 실전 활용

### 8.1 중복 제거 + 순서 유지

```java
public class RemoveDuplicatesKeepOrder {
    public static void main(String[] args) {
        List<String> list = Arrays.asList(
            "Apple", "Banana", "Apple", "Cherry", "Banana"
        );
        
        System.out.println("원본: " + list);
        
        
        // LinkedHashSet으로 중복 제거
        Set<String> set = new LinkedHashSet<>(list);
        
        System.out.println("중복 제거: " + set);
        // [Apple, Banana, Cherry] (순서 유지)
        
        
        // 다시 List로
        List<String> result = new ArrayList<>(set);
        System.out.println("List: " + result);
    }
}
```

### 8.2 방문 순서 추적

```java
public class VisitTracker {
    private Set<String> visited = new LinkedHashSet<>();
    
    public void visit(String page) {
        visited.add(page);
    }
    
    public void showHistory() {
        System.out.println("방문 순서: " + visited);
    }
    
    public static void main(String[] args) {
        VisitTracker tracker = new VisitTracker();
        
        tracker.visit("Home");
        tracker.visit("About");
        tracker.visit("Products");
        tracker.visit("Home");  // 중복
        tracker.visit("Contact");
        
        tracker.showHistory();
        // [Home, About, Products, Contact]
    }
}
```

### 8.3 순서가 중요한 유니크 데이터

```java
public class OrderedUniqueData {
    public static void main(String[] args) {
        // 설문조사 응답 (순서 중요, 중복 제거)
        Set<String> responses = new LinkedHashSet<>();
        
        responses.add("Option A");
        responses.add("Option B");
        responses.add("Option A");  // 중복
        responses.add("Option C");
        responses.add("Option B");  // 중복
        
        System.out.println("응답 순서: " + responses);
        // [Option A, Option B, Option C]
        
        
        // 첫 응답부터 처리
        for (String response : responses) {
            System.out.println("처리: " + response);
        }
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 로그에서 고유 IP 추출 (순서 유지)

```java
// 로그에서 고유 IP를 추출하되 첫 등장 순서 유지
public class Problem1 {
    public static List<String> extractUniqueIPs(List<String> logs) {
        // 로그 형식: "192.168.1.1 - GET /home"
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        List<String> logs = Arrays.asList(
            "192.168.1.1 - GET /home",
            "192.168.1.2 - GET /about",
            "192.168.1.1 - POST /login",
            "192.168.1.3 - GET /products",
            "192.168.1.2 - GET /contact"
        );
        
        List<String> ips = extractUniqueIPs(logs);
        System.out.println("고유 IP (순서): " + ips);
        // [192.168.1.1, 192.168.1.2, 192.168.1.3]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static List<String> extractUniqueIPs(List<String> logs) {
        Set<String> uniqueIPs = new LinkedHashSet<>();
        
        for (String log : logs) {
            String ip = log.split(" ")[0];
            uniqueIPs.add(ip);
        }
        
        return new ArrayList<>(uniqueIPs);
    }
}
```
</details>

---

### 문제 2: 단어 첫 등장 순서

```java
// 문장에서 단어가 첫 등장한 순서대로 출력
public class Problem2 {
    public static List<String> firstAppearanceOrder(String text) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        String text = "the quick brown fox jumps over the lazy dog the fox";
        List<String> words = firstAppearanceOrder(text);
        
        System.out.println("첫 등장 순서: " + words);
        // [the, quick, brown, fox, jumps, over, lazy, dog]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static List<String> firstAppearanceOrder(String text) {
        String[] words = text.split("\\s+");
        Set<String> uniqueWords = new LinkedHashSet<>(
            Arrays.asList(words)
        );
        
        return new ArrayList<>(uniqueWords);
    }
    
    // 소문자 변환 버전
    public static List<String> firstAppearanceOrderLower(String text) {
        String[] words = text.toLowerCase().split("\\s+");
        Set<String> uniqueWords = new LinkedHashSet<>(
            Arrays.asList(words)
        );
        
        return new ArrayList<>(uniqueWords);
    }
}
```
</details>

---

### 문제 3: 재생 목록 관리

```java
// 재생 목록: 중복 곡 자동 제거, 추가 순서 유지
public class Problem3 {
    static class Playlist {
        private Set<String> songs;
        
        public Playlist() {
            // 코드 작성
        }
        
        public void addSong(String song) {
            // 코드 작성
        }
        
        public void removeSong(String song) {
            // 코드 작성
        }
        
        public void play() {
            // 순서대로 재생
        }
    }
    
    public static void main(String[] args) {
        Playlist playlist = new Playlist();
        
        playlist.addSong("Song A");
        playlist.addSong("Song B");
        playlist.addSong("Song A");  // 중복, 무시
        playlist.addSong("Song C");
        
        playlist.play();
        // Song A, Song B, Song C
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    static class Playlist {
        private Set<String> songs;
        
        public Playlist() {
            this.songs = new LinkedHashSet<>();
        }
        
        public void addSong(String song) {
            if (songs.add(song)) {
                System.out.println("추가: " + song);
            } else {
                System.out.println("이미 있음: " + song);
            }
        }
        
        public void removeSong(String song) {
            if (songs.remove(song)) {
                System.out.println("삭제: " + song);
            }
        }
        
        public void play() {
            System.out.println("\n=== 재생 순서 ===");
            int index = 1;
            for (String song : songs) {
                System.out.println(index++ + ". " + song);
            }
        }
        
        public int size() {
            return songs.size();
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// 중복 불가 + 순서 유지
Set<String> set = new LinkedHashSet<>();
set.add("C");
set.add("A");
set.add("B");
System.out.println(set);  // [C, A, B]
```

### 주요 메서드
```java
// 추가/삭제 (HashSet과 동일)
set.add("A");       // O(1)
set.remove("A");    // O(1)
set.clear();

// 검색
set.contains("A");  // O(1)
set.isEmpty();
set.size();

// 순회 (순서 보장!)
for (String s : set) {
    System.out.println(s);
}
```

### 시간 복잡도
```
add:      O(1)
contains: O(1)
remove:   O(1)
순회:     O(n)
```

### HashSet vs LinkedHashSet
```
HashSet:
- 더 빠름
- 순서 없음
- 메모리 적음

LinkedHashSet:
- 약간 느림
- 순서 유지 ✅
- 메모리 많음
```

### 사용 시기
```
✅ 중복 제거 + 순서 유지
✅ 삽입 순서가 중요
✅ 예측 가능한 순회
❌ 순서 불필요 (HashSet)
❌ 정렬 필요 (TreeSet)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 05. HashSet](./Collections-05-HashSet.md) | [다음: 07. TreeSet →](./Collections-07-TreeSet.md)**

</div>
