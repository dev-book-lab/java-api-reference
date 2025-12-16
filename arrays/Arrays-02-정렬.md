# Arrays 02. 정렬 (sort, parallelSort)

> 배열 정렬의 모든 것 - sort()부터 Comparator까지  
> Java API Reference

---

## 📑 목차

1. [sort() 기본](#1-sort-기본)
2. [Comparator를 이용한 커스텀 정렬](#2-comparator를-이용한-커스텀-정렬)
3. [parallelSort() - 병렬 정렬](#3-parallelsort---병렬-정렬)
4. [정렬 알고리즘과 성능](#4-정렬-알고리즘과-성능)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. sort() 기본

### 1.1 기본 타입 정렬

```java
import java.util.Arrays;

public class SortBasic {
    public static void main(String[] args) {
        // int 배열 정렬 (오름차순)
        int[] arr = {5, 2, 8, 1, 9, 3};
        Arrays.sort(arr);
        System.out.println(Arrays.toString(arr));
        // [1, 2, 3, 5, 8, 9]
        
        
        // 부분 정렬 (start ~ end-1)
        int[] arr2 = {5, 2, 8, 1, 9, 3};
        Arrays.sort(arr2, 1, 4);  // 인덱스 1~3만 정렬
        System.out.println(Arrays.toString(arr2));
        // [5, 1, 2, 8, 9, 3]
        
        
        // 다양한 기본 타입
        double[] doubles = {3.14, 1.41, 2.71};
        Arrays.sort(doubles);
        System.out.println(Arrays.toString(doubles));
        // [1.41, 2.71, 3.14]
        
        char[] chars = {'d', 'a', 'c', 'b'};
        Arrays.sort(chars);
        System.out.println(Arrays.toString(chars));
        // [a, b, c, d]
    }
}
```

### 1.2 String 배열 정렬

```java
public class StringSort {
    public static void main(String[] args) {
        // 사전순 정렬
        String[] words = {"banana", "apple", "cherry", "date"};
        Arrays.sort(words);
        System.out.println(Arrays.toString(words));
        // [apple, banana, cherry, date]
        
        
        // 대소문자 구분 (대문자가 먼저)
        String[] mixed = {"Zebra", "apple", "Banana"};
        Arrays.sort(mixed);
        System.out.println(Arrays.toString(mixed));
        // [Banana, Zebra, apple]
        
        
        // 대소문자 무시 정렬
        Arrays.sort(mixed, String.CASE_INSENSITIVE_ORDER);
        System.out.println(Arrays.toString(mixed));
        // [apple, Banana, Zebra]
    }
}
```

### 1.3 내림차순 정렬

```java
import java.util.Collections;

public class DescendingSort {
    public static void main(String[] args) {
        // 기본 타입 배열 - Integer[]로 변환 필요
        Integer[] arr = {5, 2, 8, 1, 9, 3};
        Arrays.sort(arr, Collections.reverseOrder());
        System.out.println(Arrays.toString(arr));
        // [9, 8, 5, 3, 2, 1]
        
        
        // String 배열 - 직접 가능
        String[] words = {"banana", "apple", "cherry"};
        Arrays.sort(words, Collections.reverseOrder());
        System.out.println(Arrays.toString(words));
        // [cherry, banana, apple]
        
        
        // int[] 내림차순 (방법 1: 수동)
        int[] intArr = {5, 2, 8, 1, 9, 3};
        Arrays.sort(intArr);
        reverse(intArr);
        System.out.println(Arrays.toString(intArr));
        // [9, 8, 5, 3, 2, 1]
        
        
        // int[] 내림차순 (방법 2: Stream)
        int[] intArr2 = {5, 2, 8, 1, 9, 3};
        intArr2 = Arrays.stream(intArr2)
                        .boxed()
                        .sorted(Collections.reverseOrder())
                        .mapToInt(Integer::intValue)
                        .toArray();
        System.out.println(Arrays.toString(intArr2));
        // [9, 8, 5, 3, 2, 1]
    }
    
    private static void reverse(int[] arr) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            int temp = arr[left];
            arr[left] = arr[right];
            arr[right] = temp;
            left++;
            right--;
        }
    }
}
```

---

## 2. Comparator를 이용한 커스텀 정렬

### 2.1 Comparator 기본

```java
import java.util.Comparator;

public class ComparatorBasic {
    public static void main(String[] args) {
        String[] words = {"apple", "pie", "banana", "cat"};
        
        // 길이순 정렬
        Arrays.sort(words, new Comparator<String>() {
            @Override
            public int compare(String s1, String s2) {
                return s1.length() - s2.length();
            }
        });
        System.out.println(Arrays.toString(words));
        // [pie, cat, apple, banana]
        
        
        // 람다식
        Arrays.sort(words, (s1, s2) -> s1.length() - s2.length());
        
        // 메서드 참조
        Arrays.sort(words, Comparator.comparingInt(String::length));
    }
}
```

**Comparator 반환값:**
- `음수`: o1 < o2 (o1이 먼저)
- `0`: o1 == o2 (순서 유지)
- `양수`: o1 > o2 (o2가 먼저)

### 2.2 다중 조건 정렬

```java
public class MultipleConditions {
    static class Student {
        String name;
        int score;
        int age;
        
        Student(String name, int score, int age) {
            this.name = name;
            this.score = score;
            this.age = age;
        }
        
        @override
        public String toString() {
            return name + "(" + score + "," + age + ")";
        }
    }
    
    public static void main(String[] args) {
        Student[] students = {
            new Student("Alice", 90, 20),
            new Student("Bob", 85, 22),
            new Student("Charlie", 90, 19),
            new Student("David", 85, 21)
        };
        
        // 점수 내림차순, 같으면 나이 오름차순
        Arrays.sort(students, new Comparator<Student>() {
            @Override
            public int compare(Student s1, Student s2) {
                if (s1.score != s2.score) {
                    return s2.score - s1.score;  // 내림차순
                }
                return s1.age - s2.age;  // 오름차순
            }
        });
        
        System.out.println(Arrays.toString(students));
        // [Charlie(90,19), Alice(90,20), David(85,21), Bob(85,22)]
        
        
        // 람다식
        Arrays.sort(students, (s1, s2) -> {
            if (s1.score != s2.score) return s2.score - s1.score;
            return s1.age - s2.age;
        });
        
        
        // Comparator 체이닝 (Java 8+)
        Arrays.sort(students, 
            Comparator.comparingInt((Student s) -> s.score).reversed()
                      .thenComparingInt(s -> s.age));
    }
}
```

### 2.3 실전 정렬 패턴

#### 절대값 기준 정렬
```java
public class AbsoluteSort {
    public static void main(String[] args) {
        Integer[] arr = {-5, 2, -8, 1, 9, -3};
        
        // 절대값 오름차순
        Arrays.sort(arr, (a, b) -> Math.abs(a) - Math.abs(b));
        System.out.println(Arrays.toString(arr));
        // [1, 2, -3, -5, -8, 9]
        
        
        // 절대값 같으면 실제 값 오름차순
        Arrays.sort(arr, (a, b) -> {
            int absA = Math.abs(a);
            int absB = Math.abs(b);
            if (absA != absB) return absA - absB;
            return a - b;
        });
    }
}
```

#### 좌표 정렬
```java
public class CoordinateSort {
    static class Point {
        int x, y;
        
        Point(int x, int y) {
            this.x = x;
            this.y = y;
        }
        
        @Override
        public String toString() {
            return "(" + x + "," + y + ")";
        }
    }
    
    public static void main(String[] args) {
        Point[] points = {
            new Point(3, 2),
            new Point(1, 4),
            new Point(3, 1),
            new Point(1, 3)
        };
        
        // x좌표 오름차순, 같으면 y좌표 오름차순
        Arrays.sort(points, (p1, p2) -> {
            if (p1.x != p2.x) return p1.x - p2.x;
            return p1.y - p2.y;
        });
        
        System.out.println(Arrays.toString(points));
        // [(1,3), (1,4), (3,1), (3,2)]
        
        
        // 원점에서 가까운 순
        Arrays.sort(points, (p1, p2) -> {
            int dist1 = p1.x * p1.x + p1.y * p1.y;
            int dist2 = p2.x * p2.x + p2.y * p2.y;
            return dist1 - dist2;
        });
    }
}
```

#### 2D 배열 정렬
```java
public class TwoDArraySort {
    public static void main(String[] args) {
        int[][] intervals = {{3, 5}, {1, 3}, {2, 4}, {1, 2}};
        
        // 첫 번째 원소 기준 오름차순
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
        System.out.println(Arrays.deepToString(intervals));
        // [[1, 3], [1, 2], [2, 4], [3, 5]]
        
        
        // 첫 번째 오름차순, 같으면 두 번째 내림차순
        Arrays.sort(intervals, (a, b) -> {
            if (a[0] != b[0]) return a[0] - b[0];
            return b[1] - a[1];
        });
        System.out.println(Arrays.deepToString(intervals));
        // [[1, 3], [1, 2], [2, 4], [3, 5]]
    }
}
```

---

## 3. parallelSort() - 병렬 정렬

### 3.1 기본 사용법

```java
public class ParallelSortBasic {
    public static void main(String[] args) {
        int[] arr = new int[1000000];
        for (int i = 0; i < arr.length; i++) {
            arr[i] = (int)(Math.random() * 1000000);
        }
        
        // 기본 정렬
        int[] arr1 = arr.clone();
        long start1 = System.currentTimeMillis();
        Arrays.sort(arr1);
        long end1 = System.currentTimeMillis();
        System.out.println("sort(): " + (end1 - start1) + "ms");
        
        
        // 병렬 정렬
        int[] arr2 = arr.clone();
        long start2 = System.currentTimeMillis();
        Arrays.parallelSort(arr2);
        long end2 = System.currentTimeMillis();
        System.out.println("parallelSort(): " + (end2 - start2) + "ms");
        
        
        // 결과는 동일
        System.out.println("Same result: " + Arrays.equals(arr1, arr2));
    }
}
```

**결과 예시 (100만 개 정렬):**
```
sort():         ~60ms
parallelSort(): ~25ms (약 2.4배 빠름)
```

### 3.2 언제 사용할까?

```java
public class ParallelSortBenchmark {
    public static void benchmark(int size) {
        int[] arr = new int[size];
        for (int i = 0; i < size; i++) {
            arr[i] = (int)(Math.random() * size);
        }
        
        // sort()
        int[] arr1 = arr.clone();
        long start1 = System.nanoTime();
        Arrays.sort(arr1);
        long time1 = System.nanoTime() - start1;
        
        // parallelSort()
        int[] arr2 = arr.clone();
        long start2 = System.nanoTime();
        Arrays.parallelSort(arr2);
        long time2 = System.nanoTime() - start2;
        
        System.out.println("Size: " + size);
        System.out.println("sort():         " + time1/1_000_000 + "ms");
        System.out.println("parallelSort(): " + time2/1_000_000 + "ms");
        System.out.println("Speedup: " + (double)time1/time2 + "x\n");
    }
    
    public static void main(String[] args) {
        benchmark(1000);       // 작은 크기
        benchmark(10000);      // 중간 크기
        benchmark(100000);     // 큰 크기
        benchmark(1000000);    // 매우 큰 크기
    }
}
```

**가이드라인:**
- **작은 배열 (< 5000)**: `sort()` 사용 (오버헤드 때문)
- **큰 배열 (> 10000)**: `parallelSort()` 고려
- **멀티코어 환경**: `parallelSort()` 효과적
- **싱글코어**: `sort()`와 성능 차이 없음

---

## 4. 정렬 알고리즘과 성능

### 4.1 Arrays.sort() 내부 알고리즘

```java
// 기본 타입 배열 (int, double 등)
// → Dual-Pivot Quicksort
// - 평균: O(n log n)
// - 최악: O(n²) (하지만 매우 드묾)
// - 안정 정렬 아님

int[] primitives = {5, 2, 8, 1, 9};
Arrays.sort(primitives);  // Quicksort 변형


// 객체 배열 (String, Integer 등)
// → TimSort (병합 정렬 + 삽입 정렬)
// - 평균/최악: O(n log n)
// - 안정 정렬 (순서 유지)

String[] objects = {"b", "a", "c"};
Arrays.sort(objects);  // TimSort
```

**안정 정렬 (Stable Sort):**
```java
public class StableSortDemo {
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
        Person[] people = {
            new Person("Alice", 25),
            new Person("Bob", 30),
            new Person("Charlie", 25),
            new Person("David", 30)
        };
        
        // 나이로만 정렬 (안정 정렬)
        Arrays.sort(people, Comparator.comparingInt(p -> p.age));
        System.out.println(Arrays.toString(people));
        // [Alice(25), Charlie(25), Bob(30), David(30)]
        // 같은 나이끼리는 원래 순서 유지!
    }
}
```

### 4.2 성능 비교

```java
public class SortPerformance {
    public static void main(String[] args) {
        int n = 100000;
        
        // 1. 랜덤 데이터
        int[] random = generateRandom(n);
        long time1 = measureSort(random.clone());
        System.out.println("Random: " + time1 + "ms");
        
        
        // 2. 정렬된 데이터
        int[] sorted = generateRandom(n);
        Arrays.sort(sorted);
        long time2 = measureSort(sorted.clone());
        System.out.println("Sorted: " + time2 + "ms");
        
        
        // 3. 역순 데이터
        int[] reversed = sorted.clone();
        reverse(reversed);
        long time3 = measureSort(reversed.clone());
        System.out.println("Reversed: " + time3 + "ms");
        
        
        // 4. 거의 정렬된 데이터
        int[] nearlySorted = sorted.clone();
        for (int i = 0; i < n / 100; i++) {
            int idx1 = (int)(Math.random() * n);
            int idx2 = (int)(Math.random() * n);
            swap(nearlySorted, idx1, idx2);
        }
        long time4 = measureSort(nearlySorted);
        System.out.println("Nearly Sorted: " + time4 + "ms");
    }
    
    private static int[] generateRandom(int n) {
        int[] arr = new int[n];
        for (int i = 0; i < n; i++) {
            arr[i] = (int)(Math.random() * n);
        }
        return arr;
    }
    
    private static long measureSort(int[] arr) {
        long start = System.currentTimeMillis();
        Arrays.sort(arr);
        return System.currentTimeMillis() - start;
    }
    
    private static void reverse(int[] arr) {
        int left = 0, right = arr.length - 1;
        while (left < right) {
            swap(arr, left++, right--);
        }
    }
    
    private static void swap(int[] arr, int i, int j) {
        int temp = arr[i];
        arr[i] = arr[j];
        arr[j] = temp;
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: K번째 수

```java
// 배열에서 K번째로 큰 수 찾기
public class Problem1 {
    public static int findKthLargest(int[] nums, int k) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        int[] nums = {3, 2, 1, 5, 6, 4};
        System.out.println(findKthLargest(nums, 2));  // 5
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    // 방법 1: 정렬 (간단하지만 O(n log n))
    public static int findKthLargest(int[] nums, int k) {
        Arrays.sort(nums);
        return nums[nums.length - k];
    }
    
    // 방법 2: 부분 정렬 (조금 더 효율적)
    public static int findKthLargest2(int[] nums, int k) {
        Integer[] arr = Arrays.stream(nums).boxed().toArray(Integer[]::new);
        Arrays.sort(arr, Collections.reverseOrder());
        return arr[k - 1];
    }
    
    // 방법 3: PriorityQueue (O(n log k))
    public static int findKthLargest3(int[] nums, int k) {
        PriorityQueue<Integer> pq = new PriorityQueue<>();
        
        for (int num : nums) {
            pq.offer(num);
            if (pq.size() > k) {
                pq.poll();
            }
        }
        
        return pq.peek();
    }
}
```
</details>

---

### 문제 2: 회의실 배정

```java
// 회의 시작/종료 시간이 주어질 때, 최대 회의 개수
// 회의는 겹치면 안 됨
public class Problem2 {
    public static int maxMeetings(int[][] meetings) {
        // meetings[i] = {시작시간, 종료시간}
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        int[][] meetings = {
            {1, 3},
            {2, 4},
            {3, 5},
            {4, 6}
        };
        System.out.println(maxMeetings(meetings));  // 2
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int maxMeetings(int[][] meetings) {
        // 종료 시간 기준 오름차순 정렬
        Arrays.sort(meetings, (a, b) -> a[1] - b[1]);
        
        int count = 0;
        int lastEnd = 0;
        
        for (int[] meeting : meetings) {
            if (meeting[0] >= lastEnd) {
                count++;
                lastEnd = meeting[1];
            }
        }
        
        return count;
    }
}
```
</details>

---

### 문제 3: 구간 병합

```java
// 겹치는 구간들을 병합
// 예: [[1,3],[2,6],[8,10],[15,18]] → [[1,6],[8,10],[15,18]]
public class Problem3 {
    public static int[][] merge(int[][] intervals) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[][] intervals = {{1,3}, {2,6}, {8,10}, {15,18}};
        int[][] result = merge(intervals);
        System.out.println(Arrays.deepToString(result));
        // [[1, 6], [8, 10], [15, 18]]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int[][] merge(int[][] intervals) {
        if (intervals.length <= 1) return intervals;
        
        // 시작 시간 기준 정렬
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
        
        List<int[]> merged = new ArrayList<>();
        int[] current = intervals[0];
        
        for (int i = 1; i < intervals.length; i++) {
            if (intervals[i][0] <= current[1]) {
                // 겹침: 병합
                current[1] = Math.max(current[1], intervals[i][1]);
            } else {
                // 안 겹침: 새 구간 시작
                merged.add(current);
                current = intervals[i];
            }
        }
        
        merged.add(current);  // 마지막 구간
        
        return merged.toArray(new int[merged.size()][]);
    }
}
```
</details>

---

## 📌 핵심 정리

### 기본 정렬
```java
Arrays.sort(arr)                       // 오름차순
Arrays.sort(arr, Collections.reverseOrder())  // 내림차순 (객체만)

// int[] 내림차순
Arrays.sort(arr);
reverse(arr);  // 수동 반전
```

### Comparator
```java
// 람다식
Arrays.sort(arr, (a, b) -> a.length - b.length);

// 메서드 참조
Arrays.sort(arr, Comparator.comparingInt(String::length));

// 다중 조건
Arrays.sort(arr, 
    Comparator.comparingInt(A::score).reversed()
              .thenComparingInt(A::age));
```

### 병렬 정렬
```java
Arrays.parallelSort(arr)   // 큰 배열 (>10000)에 효과적
```

### 성능
- **기본 타입**: Dual-Pivot Quicksort (O(n log n))
- **객체**: TimSort - 안정 정렬 (O(n log n))
- **병렬**: Fork/Join 프레임워크 활용

### 주의사항
```java
// ❌
int[] arr = {5, 2, 8};
Arrays.sort(arr, Collections.reverseOrder());  // 컴파일 에러!

// ✅
Integer[] arr = {5, 2, 8};
Arrays.sort(arr, Collections.reverseOrder());
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../../Downloads/README.md)**

</div>

<div align="center">

**[← 이전: 01. 배열 기본](Arrays-01-배열기본.md) | [다음: 03. Arrays 검색 →](Arrays-03-검색.md)**

</div>
