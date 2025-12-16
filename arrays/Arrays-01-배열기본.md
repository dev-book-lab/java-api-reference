# Arrays 01. 배열 기본과 Arrays 클래스

> 배열의 모든 것 - 선언부터 Arrays 클래스 활용까지  
> Java API Reference

---

## 📑 목차

1. [배열 기본](#1-배열-기본)
2. [Arrays 클래스 개요](#2-arrays-클래스-개요)
3. [toString() - 배열 출력](#3-tostring---배열-출력)
4. [fill() - 배열 채우기](#4-fill---배열-채우기)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. 배열 기본

### 1.1 배열이란?

**배열 (Array)**: 같은 타입의 데이터를 연속된 메모리 공간에 저장하는 자료구조

```java
public class ArrayBasics {
    public static void main(String[] args) {
        // 배열 선언과 생성
        int[] numbers = new int[5];
        
        // 인덱스로 접근 (0부터 시작)
        numbers[0] = 10;
        numbers[1] = 20;
        numbers[2] = 30;
        
        // 길이 확인
        System.out.println("Length: " + numbers.length);  // 5
        
        // 읽기
        System.out.println(numbers[0]);  // 10
    }
}
```

**메모리 구조:**
```
numbers 배열 (int[5]):
인덱스:  0    1    2    3    4
값:     10   20   30   0    0
주소:  0x100 0x104 0x108 0x10C 0x110
```

### 1.2 배열 선언 방법

```java
public class ArrayDeclaration {
    public static void main(String[] args) {
        // 방법 1: 크기 지정
        int[] arr1 = new int[5];  // 0으로 초기화
        
        // 방법 2: 초기값 지정
        int[] arr2 = {1, 2, 3, 4, 5};
        
        // 방법 3: new와 함께 초기값
        int[] arr3 = new int[]{1, 2, 3};
        
        // 방법 4: 타입 뒤에 [] (비추천)
        int arr4[] = new int[5];  // C 스타일
        
        
        // 참조 타입 배열
        String[] strs = new String[3];  // null로 초기화
        String[] names = {"Alice", "Bob", "Charlie"};
        
        
        // 다양한 타입
        double[] doubles = {3.14, 2.71, 1.41};
        boolean[] flags = new boolean[4];  // false로 초기화
        char[] chars = {'a', 'b', 'c'};
    }
}
```

**기본값:**
| 타입 | 기본값 |
|------|--------|
| `int, byte, short, long` | `0` |
| `float, double` | `0.0` |
| `boolean` | `false` |
| `char` | `'\u0000'` |
| `참조 타입` | `null` |

### 1.3 배열 순회

```java
public class ArrayIteration {
    public static void main(String[] args) {
        int[] arr = {10, 20, 30, 40, 50};
        
        // 방법 1: 일반 for문
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i] + " ");
        }
        System.out.println();
        
        
        // 방법 2: 향상된 for문 (for-each)
        for (int num : arr) {
            System.out.print(num + " ");
        }
        System.out.println();
        
        
        // 방법 3: Stream API (Java 8+)
        Arrays.stream(arr).forEach(num -> System.out.print(num + " "));
        System.out.println();
        
        
        // 역순 순회
        for (int i = arr.length - 1; i >= 0; i--) {
            System.out.print(arr[i] + " ");
        }
        System.out.println();
    }
}
```

### 1.4 배열의 특징

```java
public class ArrayCharacteristics {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3};
        
        // 1. 길이 고정
        // arr.length = 5;  // 컴파일 에러! (final)
        // 배열 크기는 생성 후 변경 불가
        
        
        // 2. 인덱스 범위 체크
        try {
            int value = arr[10];  // ArrayIndexOutOfBoundsException!
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("인덱스 초과!");
        }
        
        
        // 3. 참조 타입
        int[] arr2 = arr;  // 같은 배열을 가리킴
        arr2[0] = 100;
        System.out.println(arr[0]);  // 100 (같은 배열)
        
        
        // 4. null 가능
        int[] arr3 = null;
        // System.out.println(arr3.length);  // NullPointerException!
    }
}
```

---

## 2. Arrays 클래스 개요

### 2.1 Arrays 클래스란?

`java.util.Arrays`: 배열을 다루는 유틸리티 메서드를 제공하는 클래스

```java
import java.util.Arrays;

public class ArraysClassIntro {
    public static void main(String[] args) {
        int[] arr = {5, 2, 8, 1, 9};
        
        // 정렬
        Arrays.sort(arr);
        
        // 출력
        System.out.println(Arrays.toString(arr));
        // [1, 2, 5, 8, 9]
        
        // 검색
        int index = Arrays.binarySearch(arr, 5);
        System.out.println("Index of 5: " + index);  // 2
        
        // 복사
        int[] copy = Arrays.copyOf(arr, arr.length);
        
        // 비교
        boolean same = Arrays.equals(arr, copy);
        System.out.println("Same: " + same);  // true
    }
}
```

**주요 메서드 분류:**

| 카테고리 | 메서드 | 기능 |
|---------|--------|------|
| **출력** | `toString()` | 배열을 문자열로 |
| **채우기** | `fill()` | 특정 값으로 채우기 |
| **정렬** | `sort()` | 정렬 |
| **검색** | `binarySearch()` | 이진 탐색 |
| **복사** | `copyOf()`, `copyOfRange()` | 배열 복사 |
| **비교** | `equals()`, `deepEquals()` | 배열 비교 |
| **변환** | `asList()`, `stream()` | List/Stream 변환 |

---

## 3. toString() - 배열 출력

### 3.1 기본 사용법

```java
import java.util.Arrays;

public class ToStringBasic {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        
        // ❌ 직접 출력 (주소값 출력됨)
        System.out.println(arr);
        // [I@15db9742 (타입@해시코드)
        
        
        // ✅ Arrays.toString() 사용
        System.out.println(Arrays.toString(arr));
        // [1, 2, 3, 4, 5]
        
        
        // 다양한 타입
        String[] strs = {"apple", "banana", "cherry"};
        System.out.println(Arrays.toString(strs));
        // [apple, banana, cherry]
        
        double[] doubles = {3.14, 2.71, 1.41};
        System.out.println(Arrays.toString(doubles));
        // [3.14, 2.71, 1.41]
        
        boolean[] flags = {true, false, true};
        System.out.println(Arrays.toString(flags));
        // [true, false, true]
    }
}
```

### 3.2 다차원 배열 출력

```java
public class DeepToString {
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        // ❌ toString() - 1차원만 가능
        System.out.println(Arrays.toString(matrix));
        // [[I@15db9742, [I@6d06d69c, [I@7852e922]
        
        
        // ✅ deepToString() - 다차원 배열
        System.out.println(Arrays.deepToString(matrix));
        // [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
        
        
        // 3차원 배열도 가능
        int[][][] cube = {
            {{1, 2}, {3, 4}},
            {{5, 6}, {7, 8}}
        };
        System.out.println(Arrays.deepToString(cube));
        // [[[1, 2], [3, 4]], [[5, 6], [7, 8]]]
    }
}
```

### 3.3 커스텀 출력

```java
public class CustomPrint {
    // 공백으로 구분
    public static void printSpaceSeparated(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            System.out.print(arr[i]);
            if (i < arr.length - 1) {
                System.out.print(" ");
            }
        }
        System.out.println();
    }
    
    // 줄바꿈으로 구분
    public static void printEachLine(int[] arr) {
        for (int num : arr) {
            System.out.println(num);
        }
    }
    
    // 인덱스와 함께
    public static void printWithIndex(int[] arr) {
        for (int i = 0; i < arr.length; i++) {
            System.out.println("[" + i + "] = " + arr[i]);
        }
    }
    
    public static void main(String[] args) {
        int[] arr = {10, 20, 30};
        
        printSpaceSeparated(arr);
        // 10 20 30
        
        printWithIndex(arr);
        // [0] = 10
        // [1] = 20
        // [2] = 30
    }
}
```

---

## 4. fill() - 배열 채우기

### 4.1 기본 사용법

```java
import java.util.Arrays;

public class FillBasic {
    public static void main(String[] args) {
        // 전체 채우기
        int[] arr = new int[5];
        Arrays.fill(arr, 10);
        System.out.println(Arrays.toString(arr));
        // [10, 10, 10, 10, 10]
        
        
        // 부분 채우기 (start ~ end-1)
        int[] arr2 = new int[10];
        Arrays.fill(arr2, 2, 7, 99);  // 인덱스 2~6
        System.out.println(Arrays.toString(arr2));
        // [0, 0, 99, 99, 99, 99, 99, 0, 0, 0]
        
        
        // 다양한 타입
        String[] strs = new String[3];
        Arrays.fill(strs, "empty");
        System.out.println(Arrays.toString(strs));
        // [empty, empty, empty]
        
        boolean[] flags = new boolean[4];
        Arrays.fill(flags, true);
        System.out.println(Arrays.toString(flags));
        // [true, true, true, true]
    }
}
```

### 4.2 실전 활용

#### 초기화
```java
public class InitializationPatterns {
    public static void main(String[] args) {
        // -1로 초기화 (미방문 표시 등)
        int[] visited = new int[100];
        Arrays.fill(visited, -1);
        
        
        // 무한대로 초기화 (최단거리 등)
        int[] dist = new int[100];
        Arrays.fill(dist, Integer.MAX_VALUE);
        
        
        // 2차원 배열 행별 초기화
        int[][] grid = new int[5][5];
        for (int[] row : grid) {
            Arrays.fill(row, 1);
        }
        System.out.println(Arrays.deepToString(grid));
        // [[1, 1, 1, 1, 1], [1, 1, 1, 1, 1], ...]
    }
}
```

#### DP 테이블 초기화
```java
public class DpInitialization {
    public static void main(String[] args) {
        int n = 100;
        
        // DP 테이블 (-1로 초기화)
        int[] dp = new int[n + 1];
        Arrays.fill(dp, -1);
        dp[0] = 0;  // 베이스 케이스
        
        
        // 2차원 DP
        int[][] dp2 = new int[n + 1][n + 1];
        for (int[] row : dp2) {
            Arrays.fill(row, -1);
        }
    }
}
```

#### 그래프 인접 행렬
```java
public class GraphInitialization {
    public static void main(String[] args) {
        int n = 5;  // 정점 개수
        
        // 인접 행렬 (무한대로 초기화)
        int[][] graph = new int[n][n];
        for (int[] row : graph) {
            Arrays.fill(row, Integer.MAX_VALUE);
        }
        
        // 자기 자신으로 가는 비용은 0
        for (int i = 0; i < n; i++) {
            graph[i][i] = 0;
        }
        
        System.out.println(Arrays.deepToString(graph));
    }
}
```

### 4.3 주의사항

```java
public class FillCaution {
    public static void main(String[] args) {
        // 주의 1: 참조 타입 배열
        StringBuilder[] arr = new StringBuilder[3];
        StringBuilder sb = new StringBuilder("test");
        Arrays.fill(arr, sb);  // 같은 객체 참조!
        
        arr[0].append("1");
        System.out.println(arr[1]);  // "test1" (같은 객체!)
        
        
        // 올바른 방법: 각각 새 객체 생성
        for (int i = 0; i < arr.length; i++) {
            arr[i] = new StringBuilder("test");
        }
        
        
        // 주의 2: 범위 초과
        int[] arr2 = new int[5];
        try {
            Arrays.fill(arr2, 0, 10, 1);  // 범위 초과!
        } catch (ArrayIndexOutOfBoundsException e) {
            System.out.println("범위 초과!");
        }
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 배열 회전

```java
// 배열을 왼쪽으로 k번 회전
// 예: [1,2,3,4,5], k=2 → [3,4,5,1,2]
public class Problem1 {
    public static void rotate(int[] arr, int k) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        rotate(arr, 2);
        System.out.println(Arrays.toString(arr));
        // [3, 4, 5, 1, 2]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void rotate(int[] arr, int k) {
        int n = arr.length;
        k = k % n;  // k가 n보다 클 경우 처리
        
        // 방법 1: 임시 배열 사용
        int[] temp = new int[n];
        for (int i = 0; i < n; i++) {
            temp[i] = arr[(i + k) % n];
        }
        System.arraycopy(temp, 0, arr, 0, n);
    }
    
    // 방법 2: 반전 활용 (공간 O(1))
    public static void rotate2(int[] arr, int k) {
        int n = arr.length;
        k = k % n;
        
        // 1. 전체 반전
        reverse(arr, 0, n - 1);
        // 2. 앞 부분 반전
        reverse(arr, 0, n - k - 1);
        // 3. 뒷 부분 반전
        reverse(arr, n - k, n - 1);
    }
    
    private static void reverse(int[] arr, int start, int end) {
        while (start < end) {
            int temp = arr[start];
            arr[start] = arr[end];
            arr[end] = temp;
            start++;
            end--;
        }
    }
}
```
</details>

---

### 문제 2: 중복 제거

```java
// 정렬된 배열에서 중복 제거 후 새 배열 반환
// 예: [1,1,2,2,3,4,4] → [1,2,3,4]
public class Problem2 {
    public static int[] removeDuplicates(int[] arr) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 1, 2, 2, 3, 4, 4};
        int[] result = removeDuplicates(arr);
        System.out.println(Arrays.toString(result));
        // [1, 2, 3, 4]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int[] removeDuplicates(int[] arr) {
        if (arr.length == 0) return new int[0];
        
        // 고유한 원소 개수 세기
        int count = 1;
        for (int i = 1; i < arr.length; i++) {
            if (arr[i] != arr[i - 1]) {
                count++;
            }
        }
        
        // 새 배열 생성
        int[] result = new int[count];
        result[0] = arr[0];
        int idx = 1;
        
        for (int i = 1; i < arr.length; i++) {
            if (arr[i] != arr[i - 1]) {
                result[idx++] = arr[i];
            }
        }
        
        return result;
    }
    
    // Stream 버전
    public static int[] removeDuplicates2(int[] arr) {
        return Arrays.stream(arr).distinct().toArray();
    }
}
```
</details>

---

### 문제 3: 2차원 배열 시계방향 90도 회전

```java
// n x n 배열을 시계방향 90도 회전
public class Problem3 {
    public static int[][] rotate90(int[][] matrix) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        int[][] rotated = rotate90(matrix);
        System.out.println(Arrays.deepToString(rotated));
        // [[7, 4, 1], [8, 5, 2], [9, 6, 3]]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int[][] rotate90(int[][] matrix) {
        int n = matrix.length;
        int[][] result = new int[n][n];
        
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                result[j][n - 1 - i] = matrix[i][j];
            }
        }
        
        return result;
    }
    
    // In-place 회전 (공간 O(1))
    public static void rotate90InPlace(int[][] matrix) {
        int n = matrix.length;
        
        // 1. 전치 (transpose)
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[j][i];
                matrix[j][i] = temp;
            }
        }
        
        // 2. 각 행 반전
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n / 2; j++) {
                int temp = matrix[i][j];
                matrix[i][j] = matrix[i][n - 1 - j];
                matrix[i][n - 1 - j] = temp;
            }
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 배열 기본
```java
// 선언 및 생성
int[] arr = new int[5];           // 0으로 초기화
int[] arr = {1, 2, 3, 4, 5};      // 초기값 지정

// 길이
arr.length                         // 필드 (괄호 없음!)

// 순회
for (int i = 0; i < arr.length; i++)  // 인덱스 필요 시
for (int num : arr)                    // 값만 필요 시
```

### Arrays 클래스
```java
// 출력
Arrays.toString(arr)               // 1차원
Arrays.deepToString(arr2d)         // 다차원

// 채우기
Arrays.fill(arr, 10)               // 전체
Arrays.fill(arr, 2, 7, 99)         // 부분 (2~6)
```

### 주의사항
```java
// ❌
System.out.println(arr)            // 주소값 출력
arr.length()                       // 메서드 아님!
Arrays.fill(objArr, obj)           // 참조 복사 주의

// ✅
Arrays.toString(arr)               // 문자열로 변환
arr.length                         // 필드
for (int i = 0; i < n; i++) {     // 각각 생성
    objArr[i] = new Object();
}
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../../Downloads/README.md)**

</div>

<div align="center">

**[다음: 02. Arrays 정렬 →](Arrays-02-정렬.md)**

</div>
