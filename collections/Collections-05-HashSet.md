# Collections 05. HashSet 완전 정복

> 해시 기반 집합 - HashSet 마스터하기  
> Java API Reference

---

## 📑 목차

1. [HashSet 기본](#1-hashset-기본)
2. [생성과 초기화](#2-생성과-초기화)
3. [추가와 삭제](#3-추가와-삭제)
4. [검색과 확인](#4-검색과-확인)
5. [집합 연산](#5-집합-연산)
6. [순회 방법](#6-순회-방법)
7. [내부 구조와 성능](#7-내부-구조와-성능)
8. [실전 활용](#8-실전-활용)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. HashSet 기본

### 1.1 특징

```java
import java.util.*;

public class HashSetBasic {
    public static void main(String[] args) {
        System.out.println("=== HashSet 특징 ===\n");
        
        System.out.println("1. 중복 불가");
        System.out.println("   - 같은 값 저장 안 됨");
        System.out.println("   - equals()와 hashCode() 사용\n");
        
        System.out.println("2. 순서 없음");
        System.out.println("   - 삽입 순서 보장 안 함");
        System.out.println("   - 해시값에 따라 저장\n");
        
        System.out.println("3. 성능");
        System.out.println("   - add: O(1)");
        System.out.println("   - contains: O(1)");
        System.out.println("   - remove: O(1)\n");
        
        System.out.println("4. null 허용");
        System.out.println("   - null 1개만 저장 가능");
    }
}
```

### 1.2 중복 제거 예제

```java
public class DuplicateRemoval {
    public static void main(String[] args) {
        // List에 중복 데이터
        List<String> list = Arrays.asList(
            "Apple", "Banana", "Apple", "Cherry", "Banana"
        );
        
        System.out.println("List: " + list);
        
        
        // HashSet으로 중복 제거
        Set<String> set = new HashSet<>(list);
        
        System.out.println("Set: " + set);
        // 순서 무작위, 중복 제거됨
        
        
        // 다시 List로
        List<String> unique = new ArrayList<>(set);
        System.out.println("Unique list: " + unique);
    }
}
```

---

## 2. 생성과 초기화

### 2.1 기본 생성

```java
public class HashSetCreation {
    public static void main(String[] args) {
        // 기본 생성 (초기 용량 16)
        Set<String> set1 = new HashSet<>();
        System.out.println("기본: " + set1);
        
        
        // 초기 용량 지정
        Set<String> set2 = new HashSet<>(100);
        System.out.println("용량 100: " + set2);
        
        
        // 다른 컬렉션으로
        Set<String> set3 = new HashSet<>(
            Arrays.asList("A", "B", "C")
        );
        System.out.println("복사: " + set3);
        
        
        // Set 인터페이스로 (권장)
        Set<String> set4 = new HashSet<>();
        System.out.println("Set 타입: " + set4);
    }
}
```

### 2.2 초기화 방법들

```java
public class HashSetInit {
    public static void main(String[] args) {
        // 방법 1: add
        Set<String> set1 = new HashSet<>();
        set1.add("Apple");
        set1.add("Banana");
        System.out.println("add: " + set1);
        
        
        // 방법 2: Arrays.asList
        Set<String> set2 = new HashSet<>(
            Arrays.asList("A", "B", "C")
        );
        System.out.println("Arrays.asList: " + set2);
        
        
        // 방법 3: Set.of (Java 9+, 불변)
        Set<String> immutable = Set.of("X", "Y", "Z");
        Set<String> set3 = new HashSet<>(immutable);
        System.out.println("Set.of: " + set3);
        
        
        // 방법 4: addAll
        Set<String> set4 = new HashSet<>();
        set4.addAll(Arrays.asList("1", "2", "3"));
        System.out.println("addAll: " + set4);
        
        
        // 방법 5: Stream
        Set<Integer> set5 = Stream.of(1, 2, 3, 4, 5)
                                 .collect(Collectors.toSet());
        System.out.println("Stream: " + set5);
    }
}
```

---

## 3. 추가와 삭제

### 3.1 추가 (add)

```java
public class HashSetAdd {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();
        
        // 추가 (성공 시 true)
        boolean added1 = set.add("Apple");
        System.out.println("Apple 추가: " + added1);  // true
        System.out.println("Set: " + set);
        
        
        // 중복 추가 (실패 시 false)
        boolean added2 = set.add("Apple");
        System.out.println("Apple 재추가: " + added2);  // false
        System.out.println("Set: " + set);  // 변화 없음
        
        
        // 여러 개 추가
        set.addAll(Arrays.asList("Banana", "Cherry"));
        System.out.println("여러 개: " + set);
        
        
        // null 추가
        set.add(null);
        System.out.println("null 추가: " + set);
    }
}
```

### 3.2 삭제 (remove)

```java
public class HashSetRemove {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        System.out.println("원본: " + set);
        
        
        // 요소 삭제 (성공 시 true)
        boolean removed1 = set.remove("C");
        System.out.println("C 삭제: " + removed1);  // true
        System.out.println("Set: " + set);
        
        
        // 없는 요소 삭제 (실패 시 false)
        boolean removed2 = set.remove("Z");
        System.out.println("Z 삭제: " + removed2);  // false
        
        
        // 여러 개 삭제
        set.removeAll(Arrays.asList("A", "B"));
        System.out.println("여러 개 삭제: " + set);
        
        
        // 전체 삭제
        set.clear();
        System.out.println("전체 삭제: " + set);
    }
}
```

### 3.3 조건부 삭제

```java
public class HashSetConditionalRemove {
    public static void main(String[] args) {
        Set<Integer> set = new HashSet<>(
            Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)
        );
        
        System.out.println("원본: " + set);
        
        
        // removeIf (Java 8+)
        set.removeIf(n -> n % 2 == 0);  // 짝수 삭제
        System.out.println("짝수 삭제: " + set);
        
        
        // retainAll (교집합만 유지)
        Set<Integer> set2 = new HashSet<>(
            Arrays.asList(1, 2, 3, 10, 11, 12)
        );
        
        Set<Integer> set3 = new HashSet<>(set2);
        set3.retainAll(Arrays.asList(1, 3, 5, 7));
        System.out.println("retainAll: " + set3);  // [1, 3]
    }
}
```

---

## 4. 검색과 확인

### 4.1 포함 확인 (contains)

```java
public class HashSetContains {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>(
            Arrays.asList("Apple", "Banana", "Cherry")
        );
        
        // 단일 요소 확인
        boolean has1 = set.contains("Apple");
        boolean has2 = set.contains("Durian");
        
        System.out.println("Apple 있음? " + has1);   // true
        System.out.println("Durian 있음? " + has2);  // false
        
        
        // 여러 요소 확인
        boolean hasAll = set.containsAll(
            Arrays.asList("Apple", "Banana")
        );
        System.out.println("Apple, Banana 모두? " + hasAll);  // true
        
        
        // null 확인
        set.add(null);
        boolean hasNull = set.contains(null);
        System.out.println("null 있음? " + hasNull);  // true
    }
}
```

### 4.2 크기와 비어있는지 확인

```java
public class HashSetSize {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>();
        
        // 비어있는지
        System.out.println("isEmpty: " + set.isEmpty());  // true
        System.out.println("size: " + set.size());        // 0
        
        
        // 추가
        set.addAll(Arrays.asList("A", "B", "C"));
        
        System.out.println("isEmpty: " + set.isEmpty());  // false
        System.out.println("size: " + set.size());        // 3
        
        
        // 중복 추가 시 크기 변화 없음
        set.add("A");
        System.out.println("size: " + set.size());  // 3 (변화 없음)
    }
}
```

---

## 5. 집합 연산

### 5.1 합집합 (Union)

```java
public class SetUnion {
    public static void main(String[] args) {
        Set<Integer> set1 = new HashSet<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        Set<Integer> set2 = new HashSet<>(
            Arrays.asList(4, 5, 6, 7, 8)
        );
        
        // 합집합
        Set<Integer> union = new HashSet<>(set1);
        union.addAll(set2);
        
        System.out.println("Set1: " + set1);
        System.out.println("Set2: " + set2);
        System.out.println("Union: " + union);
        // [1, 2, 3, 4, 5, 6, 7, 8]
    }
}
```

### 5.2 교집합 (Intersection)

```java
public class SetIntersection {
    public static void main(String[] args) {
        Set<Integer> set1 = new HashSet<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        Set<Integer> set2 = new HashSet<>(
            Arrays.asList(4, 5, 6, 7, 8)
        );
        
        // 교집합
        Set<Integer> intersection = new HashSet<>(set1);
        intersection.retainAll(set2);
        
        System.out.println("Set1: " + set1);
        System.out.println("Set2: " + set2);
        System.out.println("Intersection: " + intersection);
        // [4, 5]
    }
}
```

### 5.3 차집합 (Difference)

```java
public class SetDifference {
    public static void main(String[] args) {
        Set<Integer> set1 = new HashSet<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        Set<Integer> set2 = new HashSet<>(
            Arrays.asList(4, 5, 6, 7, 8)
        );
        
        // 차집합 (set1 - set2)
        Set<Integer> difference = new HashSet<>(set1);
        difference.removeAll(set2);
        
        System.out.println("Set1: " + set1);
        System.out.println("Set2: " + set2);
        System.out.println("Difference: " + difference);
        // [1, 2, 3]
    }
}
```

### 5.4 대칭 차집합 (Symmetric Difference)

```java
public class SetSymmetricDifference {
    public static void main(String[] args) {
        Set<Integer> set1 = new HashSet<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        Set<Integer> set2 = new HashSet<>(
            Arrays.asList(4, 5, 6, 7, 8)
        );
        
        // 대칭 차집합 ((set1 - set2) ∪ (set2 - set1))
        Set<Integer> symDiff = new HashSet<>(set1);
        symDiff.addAll(set2);  // 합집합
        
        Set<Integer> intersection = new HashSet<>(set1);
        intersection.retainAll(set2);  // 교집합
        
        symDiff.removeAll(intersection);  // 합집합 - 교집합
        
        System.out.println("Set1: " + set1);
        System.out.println("Set2: " + set2);
        System.out.println("Symmetric Diff: " + symDiff);
        // [1, 2, 3, 6, 7, 8]
    }
}
```

---

## 6. 순회 방법

### 6.1 for 루프

```java
public class HashSetIteration {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // 향상된 for (권장)
        System.out.println("=== 향상된 for ===");
        for (String item : set) {
            System.out.print(item + " ");
        }
        System.out.println();
        
        
        // 인덱스 없음!
        // for (int i = 0; i < set.size(); i++) { }  // 불가능
    }
}
```

### 6.2 Iterator

```java
public class HashSetIterator {
    public static void main(String[] args) {
        Set<String> set = new HashSet<>(
            Arrays.asList("A", "B", "C", "D", "E")
        );
        
        // Iterator
        System.out.println("=== Iterator ===");
        Iterator<String> it = set.iterator();
        
        while (it.hasNext()) {
            String item = it.next();
            System.out.print(item + " ");
            
            // 조건부 삭제
            if (item.equals("C")) {
                it.remove();
            }
        }
        System.out.println();
        System.out.println("After remove: " + set);
    }
}
```

### 6.3 forEach와 Stream

```java
public class HashSetForEach {
    public static void main(String[] args) {
        Set<Integer> set = new HashSet<>(
            Arrays.asList(1, 2, 3, 4, 5)
        );
        
        // forEach
        System.out.println("=== forEach ===");
        set.forEach(n -> System.out.print(n + " "));
        System.out.println();
        
        
        // 메서드 참조
        System.out.println("\n=== 메서드 참조 ===");
        set.forEach(System.out::println);
        
        
        // Stream
        System.out.println("\n=== Stream ===");
        set.stream()
           .filter(n -> n % 2 == 0)
           .forEach(System.out::println);
    }
}
```

---

## 7. 내부 구조와 성능

### 7.1 해시 테이블 구조

```java
public class HashSetStructure {
    public static void main(String[] args) {
        System.out.println("=== HashSet 내부 구조 ===\n");
        
        System.out.println("내부: HashMap 사용");
        System.out.println("저장: Key에만 저장 (Value는 dummy)");
        System.out.println("구조: 배열 + 연결 리스트/트리\n");
        
        System.out.println("=== 해시 충돌 처리 ===");
        System.out.println("1. 해시값 계산: hashCode()");
        System.out.println("2. 버킷 결정: hash % capacity");
        System.out.println("3. 충돌 시: 연결 리스트");
        System.out.println("4. 많은 충돌: 트리 변환 (Java 8+)\n");
        
        System.out.println("=== Load Factor ===");
        System.out.println("기본: 0.75 (75% 차면 확장)");
        System.out.println("확장: 2배 크기로 rehash");
    }
}
```

### 7.2 hashCode와 equals

```java
public class HashSetEquality {
    static class Person {
        String name;
        int age;
        
        Person(String name, int age) {
            this.name = name;
            this.age = age;
        }
        
        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;
            Person person = (Person) o;
            return age == person.age && name.equals(person.name);
        }
        
        @Override
        public int hashCode() {
            return Objects.hash(name, age);
        }
        
        @Override
        public String toString() {
            return name + "(" + age + ")";
        }
    }
    
    public static void main(String[] args) {
        Set<Person> set = new HashSet<>();
        
        Person p1 = new Person("Alice", 25);
        Person p2 = new Person("Alice", 25);  // 같은 내용
        
        set.add(p1);
        set.add(p2);  // 중복으로 판단 (hashCode + equals)
        
        System.out.println("Set: " + set);  // 1개만 저장
        System.out.println("Size: " + set.size());  // 1
        
        
        // hashCode와 equals 없으면?
        System.out.println("\nhashCode: " + p1.hashCode());
        System.out.println("hashCode: " + p2.hashCode());
        System.out.println("equals: " + p1.equals(p2));
    }
}
```

### 7.3 시간 복잡도

```java
public class HashSetComplexity {
    public static void main(String[] args) {
        System.out.println("=== 시간 복잡도 ===\n");
        
        System.out.println("add:       O(1) 평균");
        System.out.println("  - hashCode 계산");
        System.out.println("  - 버킷 찾기");
        System.out.println("  - 충돌 시 O(log n)\n");
        
        System.out.println("contains:  O(1) 평균");
        System.out.println("  - 해시 탐색\n");
        
        System.out.println("remove:    O(1) 평균");
        System.out.println("  - 해시 탐색 + 삭제\n");
        
        System.out.println("size:      O(1)");
        System.out.println("  - 내부 카운터\n");
        
        System.out.println("최악: O(n) - 모든 요소 충돌");
    }
}
```

---

## 8. 실전 활용

### 8.1 중복 확인

```java
public class DuplicateCheck {
    public static void main(String[] args) {
        List<Integer> numbers = Arrays.asList(
            1, 2, 3, 4, 2, 5, 3, 6
        );
        
        // 중복 찾기
        Set<Integer> seen = new HashSet<>();
        Set<Integer> duplicates = new HashSet<>();
        
        for (int num : numbers) {
            if (!seen.add(num)) {  // add 실패 = 중복
                duplicates.add(num);
            }
        }
        
        System.out.println("원본: " + numbers);
        System.out.println("중복: " + duplicates);  // [2, 3]
    }
}
```

### 8.2 고유 문자 세기

```java
public class UniqueChars {
    public static void main(String[] args) {
        String text = "hello world";
        
        // 고유 문자
        Set<Character> unique = new HashSet<>();
        
        for (char c : text.toCharArray()) {
            if (c != ' ') {
                unique.add(c);
            }
        }
        
        System.out.println("Text: " + text);
        System.out.println("Unique: " + unique);
        System.out.println("Count: " + unique.size());
    }
}
```

### 8.3 두 배열의 공통 요소

```java
public class CommonElements {
    public static void main(String[] args) {
        Integer[] arr1 = {1, 2, 3, 4, 5};
        Integer[] arr2 = {4, 5, 6, 7, 8};
        
        Set<Integer> set1 = new HashSet<>(Arrays.asList(arr1));
        Set<Integer> set2 = new HashSet<>(Arrays.asList(arr2));
        
        // 교집합
        set1.retainAll(set2);
        
        System.out.println("공통 요소: " + set1);  // [4, 5]
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 두 배열의 합집합/교집합/차집합

```java
// 집합 연산 구현
public class Problem1 {
    public static Set<Integer> union(int[] arr1, int[] arr2) {
        // 코드 작성
        return null;
    }
    
    public static Set<Integer> intersection(int[] arr1, int[] arr2) {
        // 코드 작성
        return null;
    }
    
    public static Set<Integer> difference(int[] arr1, int[] arr2) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};
        int[] arr2 = {4, 5, 6, 7, 8};
        
        System.out.println("Union: " + union(arr1, arr2));
        System.out.println("Intersection: " + intersection(arr1, arr2));
        System.out.println("Difference: " + difference(arr1, arr2));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static Set<Integer> union(int[] arr1, int[] arr2) {
        Set<Integer> set = new HashSet<>();
        for (int n : arr1) set.add(n);
        for (int n : arr2) set.add(n);
        return set;
    }
    
    public static Set<Integer> intersection(int[] arr1, int[] arr2) {
        Set<Integer> set1 = new HashSet<>();
        for (int n : arr1) set1.add(n);
        
        Set<Integer> result = new HashSet<>();
        for (int n : arr2) {
            if (set1.contains(n)) {
                result.add(n);
            }
        }
        return result;
    }
    
    public static Set<Integer> difference(int[] arr1, int[] arr2) {
        Set<Integer> set1 = new HashSet<>();
        for (int n : arr1) set1.add(n);
        
        Set<Integer> set2 = new HashSet<>();
        for (int n : arr2) set2.add(n);
        
        set1.removeAll(set2);
        return set1;
    }
}
```
</details>

---

### 문제 2: 중복 단어 찾기

```java
// 문장에서 중복 단어 찾기
public class Problem2 {
    public static Set<String> findDuplicates(String text) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        String text = "the quick brown fox jumps over the lazy dog the fox";
        Set<String> dups = findDuplicates(text);
        
        System.out.println("Duplicates: " + dups);
        // [the, fox]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static Set<String> findDuplicates(String text) {
        String[] words = text.split("\\s+");
        
        Set<String> seen = new HashSet<>();
        Set<String> duplicates = new HashSet<>();
        
        for (String word : words) {
            if (!seen.add(word)) {
                duplicates.add(word);
            }
        }
        
        return duplicates;
    }
    
    // Map 사용 버전
    public static Set<String> findDuplicates2(String text) {
        String[] words = text.split("\\s+");
        Map<String, Integer> count = new HashMap<>();
        
        for (String word : words) {
            count.put(word, count.getOrDefault(word, 0) + 1);
        }
        
        Set<String> duplicates = new HashSet<>();
        for (Map.Entry<String, Integer> entry : count.entrySet()) {
            if (entry.getValue() > 1) {
                duplicates.add(entry.getKey());
            }
        }
        
        return duplicates;
    }
}
```
</details>

---

### 문제 3: 유니크한 이메일

```java
// 유효한 고유 이메일 주소 세기
public class Problem3 {
    public static int countUniqueEmails(String[] emails) {
        // 규칙:
        // 1. local@domain 형식
        // 2. local의 '.' 무시
        // 3. local의 '+' 이후 무시
        
        // 예: "test.email+spam@gmail.com" 
        //  -> "testemail@gmail.com"
        
        return 0;
    }
    
    public static void main(String[] args) {
        String[] emails = {
            "test.email@gmail.com",
            "testemail@gmail.com",
            "test.email+spam@gmail.com"
        };
        
        System.out.println("Unique: " + countUniqueEmails(emails));
        // 1 (모두 같음)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int countUniqueEmails(String[] emails) {
        Set<String> unique = new HashSet<>();
        
        for (String email : emails) {
            String normalized = normalize(email);
            unique.add(normalized);
        }
        
        return unique.size();
    }
    
    private static String normalize(String email) {
        String[] parts = email.split("@");
        String local = parts[0];
        String domain = parts[1];
        
        // '+' 이후 제거
        int plusIndex = local.indexOf('+');
        if (plusIndex != -1) {
            local = local.substring(0, plusIndex);
        }
        
        // '.' 제거
        local = local.replace(".", "");
        
        return local + "@" + domain;
    }
}
```
</details>

---

## 📌 핵심 정리

### 특징
```java
// 중복 불가, 순서 없음
Set<String> set = new HashSet<>();
set.add("A");
set.add("A");  // 무시됨
System.out.println(set);  // [A]
```

### 주요 메서드
```java
// 추가/삭제
set.add("A");       // O(1)
set.remove("A");    // O(1)
set.clear();

// 검색
set.contains("A");  // O(1)
set.isEmpty();
set.size();

// 집합 연산
set.addAll(other);     // 합집합
set.retainAll(other);  // 교집합
set.removeAll(other);  // 차집합
```

### 시간 복잡도
```
add:      O(1) 평균
contains: O(1) 평균
remove:   O(1) 평균
size:     O(1)
```

### 사용 시기
```
✅ 중복 제거
✅ 빠른 검색 (contains)
✅ 집합 연산
✅ 고유 값 관리
❌ 순서 유지 필요
❌ 인덱스 접근
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 04. List 비교](./Collections-04-ListComparison.md) | [다음: 06. LinkedHashSet →](./Collections-06-LinkedHashSet.md)**

</div>
