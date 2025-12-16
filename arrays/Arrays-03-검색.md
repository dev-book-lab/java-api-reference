# Arrays 03. 검색 (binarySearch)

> 이진 탐색으로 빠르게 찾기 - binarySearch() 완전 정복  
> Java API Reference

---

## 📑 목차

1. [binarySearch() 기본](#1-binarysearch-기본)
2. [반환값 이해하기](#2-반환값-이해하기)
3. [Comparator를 이용한 검색](#3-comparator를-이용한-검색)
4. [이진 탐색 활용 패턴](#4-이진-탐색-활용-패턴)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. binarySearch() 기본

### 1.1 기본 사용법

```java
import java.util.Arrays;

public class BinarySearchBasic {
    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 7, 9, 11, 13};  // ⚠️ 정렬 필수!
        
        // 존재하는 값 찾기
        int idx1 = Arrays.binarySearch(arr, 7);
        System.out.println("Index of 7: " + idx1);  // 3
        
        int idx2 = Arrays.binarySearch(arr, 1);
        System.out.println("Index of 1: " + idx2);  // 0
        
        int idx3 = Arrays.binarySearch(arr, 13);
        System.out.println("Index of 13: " + idx3);  // 6
        
        
        // 존재하지 않는 값
        int idx4 = Arrays.binarySearch(arr, 8);
        System.out.println("Index of 8: " + idx4);  // 음수 (-5)
    }
}
```

**중요: 배열이 정렬되어 있어야 함!**
```java
int[] unsorted = {5, 2, 8, 1, 9};
int idx = Arrays.binarySearch(unsorted, 5);
System.out.println(idx);  // 예측 불가능한 결과!

// 올바른 방법
Arrays.sort(unsorted);
idx = Arrays.binarySearch(unsorted, 5);
System.out.println(idx);  // 2
```

### 1.2 부분 검색

```java
public class BinarySearchRange {
    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 7, 9, 11, 13, 15, 17};
        
        // 범위 지정 검색 (fromIndex ~ toIndex-1)
        int idx1 = Arrays.binarySearch(arr, 2, 7, 9);
        System.out.println("Index: " + idx1);  // 4
        
        // 범위 밖의 값
        int idx2 = Arrays.binarySearch(arr, 2, 7, 13);
        System.out.println("Index: " + idx2);  // 음수 (범위에 없음)
    }
}
```

### 1.3 다양한 타입

```java
public class BinarySearchTypes {
    public static void main(String[] args) {
        // double 배열
        double[] doubles = {1.1, 2.2, 3.3, 4.4, 5.5};
        int idx1 = Arrays.binarySearch(doubles, 3.3);
        System.out.println("Index: " + idx1);  // 2
        
        
        // char 배열
        char[] chars = {'a', 'c', 'e', 'g', 'i'};
        int idx2 = Arrays.binarySearch(chars, 'e');
        System.out.println("Index: " + idx2);  // 2
        
        
        // String 배열 (사전순)
        String[] words = {"apple", "banana", "cherry", "date"};
        int idx3 = Arrays.binarySearch(words, "cherry");
        System.out.println("Index: " + idx3);  // 2
        
        int idx4 = Arrays.binarySearch(words, "avocado");
        System.out.println("Index: " + idx4);  // -2 (banana 앞에 삽입)
    }
}
```

---

## 2. 반환값 이해하기

### 2.1 반환값 규칙

**값을 찾았을 때:**
- 해당 인덱스 반환 (0 이상)

**못 찾았을 때:**
- `-(삽입 위치) - 1` 반환 (음수)

```java
public class BinarySearchReturn {
    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 7, 9};
        
        // 존재하는 값들
        System.out.println(Arrays.binarySearch(arr, 1));  // 0
        System.out.println(Arrays.binarySearch(arr, 5));  // 2
        System.out.println(Arrays.binarySearch(arr, 9));  // 4
        
        
        // 존재하지 않는 값들
        System.out.println(Arrays.binarySearch(arr, 0));  // -1 (맨 앞 삽입)
        System.out.println(Arrays.binarySearch(arr, 2));  // -2 (인덱스 1에 삽입)
        System.out.println(Arrays.binarySearch(arr, 4));  // -3 (인덱스 2에 삽입)
        System.out.println(Arrays.binarySearch(arr, 6));  // -4 (인덱스 3에 삽입)
        System.out.println(Arrays.binarySearch(arr, 10)); // -6 (맨 뒤 삽입)
    }
}
```

**삽입 위치 계산:**
```
반환값이 음수일 때 삽입 위치 = -(반환값) - 1

예: 반환값 = -3
삽입 위치 = -(-3) - 1 = 3 - 1 = 2
```

### 2.2 삽입 위치 활용

```java
public class InsertionPoint {
    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 7, 9};
        
        int key = 6;
        int result = Arrays.binarySearch(arr, key);
        
        if (result >= 0) {
            System.out.println("Found at index: " + result);
        } else {
            int insertionPoint = -(result) - 1;
            System.out.println("Not found. Insert at: " + insertionPoint);
            
            // 삽입 위치 확인
            System.out.println("Before: " + Arrays.toString(arr));
            int[] newArr = insertAt(arr, insertionPoint, key);
            System.out.println("After: " + Arrays.toString(newArr));
        }
    }
    
    private static int[] insertAt(int[] arr, int index, int value) {
        int[] newArr = new int[arr.length + 1];
        System.arraycopy(arr, 0, newArr, 0, index);
        newArr[index] = value;
        System.arraycopy(arr, index, newArr, index + 1, arr.length - index);
        return newArr;
    }
}
```

**출력:**
```
Not found. Insert at: 3
Before: [1, 3, 5, 7, 9]
After: [1, 3, 5, 6, 7, 9]
```

### 2.3 중복 값 처리

```java
public class DuplicateHandling {
    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 5, 5, 7, 9};
        
        int result = Arrays.binarySearch(arr, 5);
        System.out.println("Index: " + result);
        // 2, 3, 4 중 하나 (정확히 어디인지 보장 안 됨!)
        
        
        // 첫 번째 위치 찾기
        int first = findFirst(arr, 5);
        System.out.println("First index: " + first);  // 2
        
        // 마지막 위치 찾기
        int last = findLast(arr, 5);
        System.out.println("Last index: " + last);  // 4
    }
    
    private static int findFirst(int[] arr, int target) {
        int idx = Arrays.binarySearch(arr, target);
        if (idx < 0) return -1;
        
        // 왼쪽으로 이동
        while (idx > 0 && arr[idx - 1] == target) {
            idx--;
        }
        return idx;
    }
    
    private static int findLast(int[] arr, int target) {
        int idx = Arrays.binarySearch(arr, target);
        if (idx < 0) return -1;
        
        // 오른쪽으로 이동
        while (idx < arr.length - 1 && arr[idx + 1] == target) {
            idx++;
        }
        return idx;
    }
}
```

---

## 3. Comparator를 이용한 검색

### 3.1 커스텀 정렬된 배열 검색

```java
import java.util.Comparator;

public class ComparatorSearch {
    public static void main(String[] args) {
        String[] words = {"pie", "cat", "apple", "banana"};
        
        // 길이순 정렬
        Arrays.sort(words, Comparator.comparingInt(String::length));
        System.out.println(Arrays.toString(words));
        // [pie, cat, apple, banana]
        
        
        // 같은 Comparator로 검색
        int idx = Arrays.binarySearch(words, "dog", 
                                      Comparator.comparingInt(String::length));
        System.out.println("Index: " + idx);  // 1 또는 -2 (길이 3)
        
        
        // ⚠️ Comparator 없이 검색 - 잘못된 결과!
        int wrong = Arrays.binarySearch(words, "dog");
        System.out.println("Wrong: " + wrong);  // 예측 불가
    }
}
```

### 3.2 객체 배열 검색

```java
public class ObjectSearch {
    static class Student implements Comparable<Student> {
        String name;
        int score;
        
        Student(String name, int score) {
            this.name = name;
            this.score = score;
        }
        
        @Override
        public int compareTo(Student other) {
            return Integer.compare(this.score, other.score);
        }
        
        @Override
        public String toString() {
            return name + "(" + score + ")";
        }
    }
    
    public static void main(String[] args) {
        Student[] students = {
            new Student("Alice", 85),
            new Student("Bob", 90),
            new Student("Charlie", 95),
            new Student("David", 80)
        };
        
        // 점수 기준 정렬
        Arrays.sort(students);
        System.out.println(Arrays.toString(students));
        // [David(80), Alice(85), Bob(90), Charlie(95)]
        
        
        // 점수로 검색 (더미 객체 생성)
        Student target = new Student("", 90);
        int idx = Arrays.binarySearch(students, target);
        System.out.println("Index: " + idx);  // 2
        
        if (idx >= 0) {
            System.out.println("Found: " + students[idx]);
            // Found: Bob(90)
        }
    }
}
```

### 3.3 2D 배열 검색

```java
public class TwoDArraySearch {
    public static void main(String[] args) {
        int[][] intervals = {
            {1, 3},
            {2, 5},
            {4, 7},
            {6, 9}
        };
        
        // 첫 번째 원소 기준 정렬
        Arrays.sort(intervals, (a, b) -> a[0] - b[0]);
        
        // 검색 (첫 번째 원소가 4인 구간)
        int idx = Arrays.binarySearch(intervals, new int[]{4, 0}, 
                                      (a, b) -> a[0] - b[0]);
        
        System.out.println("Index: " + idx);  // 2
        if (idx >= 0) {
            System.out.println("Found: " + Arrays.toString(intervals[idx]));
            // Found: [4, 7]
        }
    }
}
```

---

## 4. 이진 탐색 활용 패턴

### 4.1 Lower Bound / Upper Bound

```java
public class BoundSearch {
    // Lower Bound: target 이상인 첫 번째 인덱스
    public static int lowerBound(int[] arr, int target) {
        int result = Arrays.binarySearch(arr, target);
        
        if (result >= 0) {
            // 찾았으면 첫 번째 위치로
            while (result > 0 && arr[result - 1] == target) {
                result--;
            }
            return result;
        } else {
            // 못 찾았으면 삽입 위치
            return -(result) - 1;
        }
    }
    
    // Upper Bound: target 초과인 첫 번째 인덱스
    public static int upperBound(int[] arr, int target) {
        int result = Arrays.binarySearch(arr, target);
        
        if (result >= 0) {
            // 찾았으면 마지막 다음 위치로
            while (result < arr.length && arr[result] == target) {
                result++;
            }
            return result;
        } else {
            // 못 찾았으면 삽입 위치
            return -(result) - 1;
        }
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 5, 5, 7, 9};
        
        System.out.println("Lower bound of 5: " + lowerBound(arr, 5));  // 2
        System.out.println("Upper bound of 5: " + upperBound(arr, 5));  // 5
        
        System.out.println("Lower bound of 6: " + lowerBound(arr, 6));  // 5
        System.out.println("Upper bound of 6: " + upperBound(arr, 6));  // 5
        
        // 범위 개수
        int count = upperBound(arr, 5) - lowerBound(arr, 5);
        System.out.println("Count of 5: " + count);  // 3
    }
}
```

### 4.2 범위 검색

```java
public class RangeSearch {
    public static int countInRange(int[] arr, int left, int right) {
        int leftIdx = lowerBound(arr, left);
        int rightIdx = upperBound(arr, right);
        return rightIdx - leftIdx;
    }
    
    private static int lowerBound(int[] arr, int target) {
        int result = Arrays.binarySearch(arr, target);
        if (result >= 0) {
            while (result > 0 && arr[result - 1] >= target) result--;
            return result;
        }
        return -(result) - 1;
    }
    
    private static int upperBound(int[] arr, int target) {
        int result = Arrays.binarySearch(arr, target);
        if (result >= 0) {
            while (result < arr.length && arr[result] <= target) result++;
            return result;
        }
        return -(result) - 1;
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 3, 5, 7, 9, 11, 13, 15};
        
        // 5 이상 11 이하 개수
        int count = countInRange(arr, 5, 11);
        System.out.println("Count [5, 11]: " + count);  // 4 (5,7,9,11)
    }
}
```

### 4.3 최장 증가 부분 수열 (LIS)

```java
public class LIS {
    public static int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        int len = 0;
        
        for (int num : nums) {
            int idx = Arrays.binarySearch(dp, 0, len, num);
            if (idx < 0) {
                idx = -(idx) - 1;  // 삽입 위치
            }
            dp[idx] = num;
            if (idx == len) {
                len++;
            }
        }
        
        return len;
    }
    
    public static void main(String[] args) {
        int[] nums = {10, 9, 2, 5, 3, 7, 101, 18};
        System.out.println("LIS length: " + lengthOfLIS(nums));
        // 4 ([2, 3, 7, 18] or [2, 3, 7, 101])
    }
}
```

### 4.4 Parametric Search

```java
public class ParametricSearch {
    // 나무 자르기: H 높이로 잘랐을 때 M 이상 얻을 수 있는 최대 H
    public static int cutTrees(int[] trees, int target) {
        Arrays.sort(trees);
        
        int left = 0;
        int right = trees[trees.length - 1];
        int answer = 0;
        
        while (left <= right) {
            int mid = (left + right) / 2;
            long sum = 0;
            
            for (int tree : trees) {
                if (tree > mid) {
                    sum += tree - mid;
                }
            }
            
            if (sum >= target) {
                answer = mid;
                left = mid + 1;  // 더 높이 자를 수 있는지 확인
            } else {
                right = mid - 1;
            }
        }
        
        return answer;
    }
    
    public static void main(String[] args) {
        int[] trees = {20, 15, 10, 17};
        int target = 7;
        System.out.println("Max height: " + cutTrees(trees, target));
        // 15 (15로 자르면 5+0+0+2=7)
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 숫자 카드

```java
// N개의 카드를 가지고 있고, M개의 숫자가 주어질 때
// 각 숫자가 카드에 있는지 확인
public class Problem1 {
    public static int[] hasCards(int[] cards, int[] queries) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] cards = {6, 3, 2, 10, -10, 10, 7};
        int[] queries = {10, 9, -5, 2, 3, 4, 5, -10};
        
        int[] result = hasCards(cards, queries);
        System.out.println(Arrays.toString(result));
        // [1, 0, 0, 1, 1, 0, 0, 1] (1=있음, 0=없음)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static int[] hasCards(int[] cards, int[] queries) {
        Arrays.sort(cards);  // 정렬 필수
        
        int[] result = new int[queries.length];
        
        for (int i = 0; i < queries.length; i++) {
            int idx = Arrays.binarySearch(cards, queries[i]);
            result[i] = idx >= 0 ? 1 : 0;
        }
        
        return result;
    }
    
    // 중복 개수까지 세기
    public static int[] countCards(int[] cards, int[] queries) {
        Arrays.sort(cards);
        
        int[] result = new int[queries.length];
        
        for (int i = 0; i < queries.length; i++) {
            int lower = lowerBound(cards, queries[i]);
            int upper = upperBound(cards, queries[i]);
            result[i] = upper - lower;
        }
        
        return result;
    }
    
    private static int lowerBound(int[] arr, int target) {
        int result = Arrays.binarySearch(arr, target);
        if (result >= 0) {
            while (result > 0 && arr[result - 1] == target) result--;
            return result;
        }
        return -(result) - 1;
    }
    
    private static int upperBound(int[] arr, int target) {
        int result = Arrays.binarySearch(arr, target);
        if (result >= 0) {
            while (result < arr.length && arr[result] == target) result++;
            return result;
        }
        return -(result) - 1;
    }
}
```
</details>

---

### 문제 2: 예산

```java
// 각 지방의 예산 요청과 총 예산이 주어질 때, 
// 모든 요청이 배정될 수 있는 최대 상한액
public class Problem2 {
    public static int maxBudget(int[] requests, int total) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        int[] requests = {120, 110, 140, 150};
        int total = 485;
        System.out.println(maxBudget(requests, total));
        // 127 (120+110+127+127=484)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int maxBudget(int[] requests, int total) {
        Arrays.sort(requests);
        
        int left = 0;
        int right = requests[requests.length - 1];
        int answer = 0;
        
        while (left <= right) {
            int mid = (left + right) / 2;
            long sum = 0;
            
            for (int request : requests) {
                sum += Math.min(request, mid);
            }
            
            if (sum <= total) {
                answer = mid;
                left = mid + 1;
            } else {
                right = mid - 1;
            }
        }
        
        return answer;
    }
}
```
</details>

---

### 문제 3: 공유기 설치

```java
// N개 집에 C개 공유기를 설치하는데,
// 가장 인접한 두 공유기 사이 거리를 최대화
public class Problem3 {
    public static int installRouters(int[] houses, int c) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        int[] houses = {1, 2, 8, 4, 9};
        int c = 3;
        System.out.println(installRouters(houses, c));
        // 3 (1, 4, 8 또는 1, 4, 9)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int installRouters(int[] houses, int c) {
        Arrays.sort(houses);
        
        int left = 1;
        int right = houses[houses.length - 1] - houses[0];
        int answer = 0;
        
        while (left <= right) {
            int mid = (left + right) / 2;
            
            if (canInstall(houses, c, mid)) {
                answer = mid;
                left = mid + 1;  // 거리를 더 늘려볼 수 있는지
            } else {
                right = mid - 1;
            }
        }
        
        return answer;
    }
    
    private static boolean canInstall(int[] houses, int c, int minDist) {
        int count = 1;  // 첫 집에 설치
        int lastPos = houses[0];
        
        for (int i = 1; i < houses.length; i++) {
            if (houses[i] - lastPos >= minDist) {
                count++;
                lastPos = houses[i];
                if (count >= c) return true;
            }
        }
        
        return false;
    }
}
```
</details>

---

## 📌 핵심 정리

### 기본 사용
```java
Arrays.binarySearch(arr, key)              // 전체 검색
Arrays.binarySearch(arr, from, to, key)    // 부분 검색

// ⚠️ 정렬 필수!
Arrays.sort(arr);
int idx = Arrays.binarySearch(arr, key);
```

### 반환값
```java
result >= 0  // 찾음: 인덱스
result < 0   // 못 찾음: -(삽입위치) - 1

// 삽입 위치
int insertPos = -(result) - 1;
```

### Comparator
```java
// 정렬과 검색에 같은 Comparator 사용
Arrays.sort(arr, comparator);
Arrays.binarySearch(arr, key, comparator);
```

### 시간 복잡도
- **이진 탐색**: O(log n)
- **정렬 필요**: O(n log n)
- **전체**: O(n log n)

### 주의사항
```java
// ❌ 정렬 안 함
int[] arr = {5, 2, 8};
Arrays.binarySearch(arr, 5);  // 예측 불가!

// ✅ 정렬 후 검색
Arrays.sort(arr);
Arrays.binarySearch(arr, 5);
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../../Downloads/README.md)**

</div>

<div align="center">

**[← 이전: 02. Arrays 정렬](Arrays-02-정렬.md) | [다음: 04. Arrays 비교와 복사 →](./Arrays-04-비교와복사.md)**

</div>
