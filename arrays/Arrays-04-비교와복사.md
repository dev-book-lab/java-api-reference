# Arrays 04. 비교와 복사

> 배열 비교와 복사의 모든 것 - equals부터 깊은 복사까지  
> Java API Reference

---

## 📑 목차

1. [equals() - 배열 비교](#1-equals---배열-비교)
2. [copyOf() / copyOfRange() - 배열 복사](#2-copyof--copyofrange---배열-복사)
3. [얕은 복사 vs 깊은 복사](#3-얕은-복사-vs-깊은-복사)
4. [System.arraycopy()](#4-systemarraycopy)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. equals() - 배열 비교

### 1.1 기본 비교

```java
import java.util.Arrays;

public class ArrayEquals {
    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};
        int[] arr2 = {1, 2, 3, 4, 5};
        int[] arr3 = {1, 2, 3, 4, 6};
        
        // ❌ == 연산자 (참조 비교)
        System.out.println(arr1 == arr2);  // false (다른 객체)
        
        
        // ✅ Arrays.equals() (값 비교)
        System.out.println(Arrays.equals(arr1, arr2));  // true
        System.out.println(Arrays.equals(arr1, arr3));  // false
        
        
        // 자기 자신과 비교
        System.out.println(Arrays.equals(arr1, arr1));  // true
        
        
        // null 처리
        int[] arr4 = null;
        System.out.println(Arrays.equals(arr1, arr4));  // false
        System.out.println(Arrays.equals(arr4, arr4));  // true
    }
}
```

**비교 조건:**
- 길이가 같아야 함
- 각 인덱스의 값이 같아야 함
- null 안전 (NullPointerException 안 남)

### 1.2 다양한 타입 비교

```java
public class EqualsTypes {
    public static void main(String[] args) {
        // double 배열 (부동소수점 주의)
        double[] d1 = {1.0, 2.0, 3.0};
        double[] d2 = {1.0, 2.0, 3.0};
        System.out.println(Arrays.equals(d1, d2));  // true
        
        
        // String 배열
        String[] s1 = {"apple", "banana", "cherry"};
        String[] s2 = {"apple", "banana", "cherry"};
        System.out.println(Arrays.equals(s1, s2));  // true
        
        
        // boolean 배열
        boolean[] b1 = {true, false, true};
        boolean[] b2 = {true, false, true};
        System.out.println(Arrays.equals(b1, b2));  // true
        
        
        // char 배열
        char[] c1 = {'a', 'b', 'c'};
        char[] c2 = {'a', 'b', 'c'};
        System.out.println(Arrays.equals(c1, c2));  // true
    }
}
```

### 1.3 부분 비교

```java
public class EqualsRange {
    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};
        int[] arr2 = {0, 2, 3, 4, 0};
        
        // 범위 비교 (Java 9+)
        boolean result = Arrays.equals(arr1, 1, 4, arr2, 1, 4);
        System.out.println("Range equals: " + result);  // true
        // arr1[1~3] == arr2[1~3] → {2,3,4} == {2,3,4}
        
        
        // 다른 범위
        boolean result2 = Arrays.equals(arr1, 0, 3, arr2, 0, 3);
        System.out.println("Range equals: " + result2);  // false
        // arr1[0~2] == arr2[0~2] → {1,2,3} != {0,2,3}
    }
}
```

### 1.4 deepEquals() - 다차원 배열

```java
public class DeepEquals {
    public static void main(String[] args) {
        int[][] matrix1 = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        int[][] matrix2 = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        // ❌ equals() - 1차원만 비교
        System.out.println(Arrays.equals(matrix1, matrix2));  // false!
        // 배열 참조만 비교함
        
        
        // ✅ deepEquals() - 재귀적 비교
        System.out.println(Arrays.deepEquals(matrix1, matrix2));  // true
        
        
        // 3차원 배열
        int[][][] cube1 = {{{1, 2}, {3, 4}}, {{5, 6}, {7, 8}}};
        int[][][] cube2 = {{{1, 2}, {3, 4}}, {{5, 6}, {7, 8}}};
        System.out.println(Arrays.deepEquals(cube1, cube2));  // true
    }
}
```

### 1.5 compare() / mismatch() - Java 9+

```java
public class CompareArrays {
    public static void main(String[] args) {
        int[] arr1 = {1, 2, 3, 4, 5};
        int[] arr2 = {1, 2, 3, 4, 6};
        int[] arr3 = {1, 2, 3};
        
        // compare(): 사전순 비교
        // 음수: arr1 < arr2
        // 0: arr1 == arr2
        // 양수: arr1 > arr2
        System.out.println(Arrays.compare(arr1, arr2));  // -1 (5 < 6)
        System.out.println(Arrays.compare(arr1, arr1));  // 0
        System.out.println(Arrays.compare(arr2, arr1));  // 1 (6 > 5)
        System.out.println(Arrays.compare(arr1, arr3));  // 1 (더 긺)
        
        
        // mismatch(): 첫 불일치 인덱스
        System.out.println(Arrays.mismatch(arr1, arr2));  // 4
        System.out.println(Arrays.mismatch(arr1, arr1));  // -1 (일치)
        System.out.println(Arrays.mismatch(arr1, arr3));  // 3 (길이 차이)
    }
}
```

---

## 2. copyOf() / copyOfRange() - 배열 복사

### 2.1 copyOf() - 전체/부분 복사

```java
public class CopyOfBasic {
    public static void main(String[] args) {
        int[] original = {1, 2, 3, 4, 5};
        
        // 정확한 길이로 복사
        int[] copy1 = Arrays.copyOf(original, original.length);
        System.out.println(Arrays.toString(copy1));
        // [1, 2, 3, 4, 5]
        
        
        // 더 짧게 복사 (앞에서부터)
        int[] copy2 = Arrays.copyOf(original, 3);
        System.out.println(Arrays.toString(copy2));
        // [1, 2, 3]
        
        
        // 더 길게 복사 (0으로 채움)
        int[] copy3 = Arrays.copyOf(original, 8);
        System.out.println(Arrays.toString(copy3));
        // [1, 2, 3, 4, 5, 0, 0, 0]
        
        
        // 독립적인 배열
        copy1[0] = 100;
        System.out.println("Original: " + Arrays.toString(original));
        System.out.println("Copy: " + Arrays.toString(copy1));
        // Original: [1, 2, 3, 4, 5]
        // Copy: [100, 2, 3, 4, 5]
    }
}
```

### 2.2 copyOfRange() - 범위 복사

```java
public class CopyOfRange {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        
        // 범위 복사 (from ~ to-1)
        int[] range1 = Arrays.copyOfRange(arr, 2, 7);
        System.out.println(Arrays.toString(range1));
        // [3, 4, 5, 6, 7]
        
        
        // 처음부터
        int[] range2 = Arrays.copyOfRange(arr, 0, 5);
        System.out.println(Arrays.toString(range2));
        // [1, 2, 3, 4, 5]
        
        
        // 끝까지
        int[] range3 = Arrays.copyOfRange(arr, 5, arr.length);
        System.out.println(Arrays.toString(range3));
        // [6, 7, 8, 9, 10]
        
        
        // 범위 초과 (0으로 채움)
        int[] range4 = Arrays.copyOfRange(arr, 8, 15);
        System.out.println(Arrays.toString(range4));
        // [9, 10, 0, 0, 0, 0, 0]
    }
}
```

### 2.3 다양한 타입 복사

```java
public class CopyTypes {
    public static void main(String[] args) {
        // String 배열
        String[] words = {"apple", "banana", "cherry"};
        String[] wordsCopy = Arrays.copyOf(words, words.length);
        System.out.println(Arrays.toString(wordsCopy));
        // [apple, banana, cherry]
        
        
        // double 배열
        double[] doubles = {3.14, 2.71, 1.41};
        double[] doublesCopy = Arrays.copyOf(doubles, 5);
        System.out.println(Arrays.toString(doublesCopy));
        // [3.14, 2.71, 1.41, 0.0, 0.0]
        
        
        // boolean 배열
        boolean[] flags = {true, false, true};
        boolean[] flagsCopy = Arrays.copyOf(flags, 2);
        System.out.println(Arrays.toString(flagsCopy));
        // [true, false]
    }
}
```

---

## 3. 얕은 복사 vs 깊은 복사

### 3.1 얕은 복사 (Shallow Copy)

```java
public class ShallowCopy {
    static class Person {
        String name;
        
        Person(String name) {
            this.name = name;
        }
        
        @Override
        public String toString() {
            return name;
        }
    }
    
    public static void main(String[] args) {
        Person[] original = {
            new Person("Alice"),
            new Person("Bob"),
            new Person("Charlie")
        };
        
        // 얕은 복사
        Person[] copy = Arrays.copyOf(original, original.length);
        
        // 배열은 다름
        System.out.println(original == copy);  // false
        
        // 하지만 원소는 같은 객체!
        System.out.println(original[0] == copy[0]);  // true
        
        
        // 원본 수정 시 복사본도 영향 받음
        original[0].name = "ALICE";
        System.out.println("Original: " + Arrays.toString(original));
        System.out.println("Copy: " + Arrays.toString(copy));
        // Original: [ALICE, Bob, Charlie]
        // Copy: [ALICE, Bob, Charlie] (같이 변경!)
    }
}
```

**메모리 구조:**
```
original → [Person1, Person2, Person3]
                ↓       ↓       ↓
copy     → [Person1, Person2, Person3]
            (같은 객체들을 가리킴)
```

### 3.2 깊은 복사 (Deep Copy)

```java
public class DeepCopy {
    static class Person implements Cloneable {
        String name;
        
        Person(String name) {
            this.name = name;
        }
        
        @Override
        protected Person clone() {
            return new Person(this.name);
        }
        
        @Override
        public String toString() {
            return name;
        }
    }
    
    public static void main(String[] args) {
        Person[] original = {
            new Person("Alice"),
            new Person("Bob"),
            new Person("Charlie")
        };
        
        // 깊은 복사 (수동)
        Person[] copy = new Person[original.length];
        for (int i = 0; i < original.length; i++) {
            copy[i] = original[i].clone();
        }
        
        // 배열도 다르고 원소도 다름
        System.out.println(original == copy);  // false
        System.out.println(original[0] == copy[0]);  // false
        
        
        // 원본 수정 시 복사본 영향 없음
        original[0].name = "ALICE";
        System.out.println("Original: " + Arrays.toString(original));
        System.out.println("Copy: " + Arrays.toString(copy));
        // Original: [ALICE, Bob, Charlie]
        // Copy: [Alice, Bob, Charlie] (영향 없음!)
    }
}
```

**메모리 구조:**
```
original → [Person1, Person2, Person3]

copy     → [Person1', Person2', Person3']
            (새로운 객체들)
```

### 3.3 2차원 배열 깊은 복사

```java
public class DeepCopy2D {
    public static void main(String[] args) {
        int[][] original = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        // ❌ 얕은 복사 (1차원만 복사)
        int[][] shallowCopy = Arrays.copyOf(original, original.length);
        shallowCopy[0][0] = 100;
        System.out.println("Original: " + Arrays.deepToString(original));
        System.out.println("Shallow: " + Arrays.deepToString(shallowCopy));
        // Original: [[100, 2, 3], [4, 5, 6], [7, 8, 9]] (영향 받음!)
        
        
        // ✅ 깊은 복사 (각 행을 개별 복사)
        int[][] deepCopy = new int[original.length][];
        for (int i = 0; i < original.length; i++) {
            deepCopy[i] = Arrays.copyOf(original[i], original[i].length);
        }
        
        original[0][0] = 1;  // 원상복구
        deepCopy[0][0] = 999;
        System.out.println("Original: " + Arrays.deepToString(original));
        System.out.println("Deep: " + Arrays.deepToString(deepCopy));
        // Original: [[1, 2, 3], [4, 5, 6], [7, 8, 9]] (영향 없음!)
        // Deep: [[999, 2, 3], [4, 5, 6], [7, 8, 9]]
    }
}
```

### 3.4 실전 복사 유틸리티

```java
public class CopyUtils {
    // 2D int 배열 깊은 복사
    public static int[][] deepCopy2D(int[][] original) {
        if (original == null) return null;
        
        int[][] copy = new int[original.length][];
        for (int i = 0; i < original.length; i++) {
            copy[i] = Arrays.copyOf(original[i], original[i].length);
        }
        return copy;
    }
    
    // 객체 배열 깊은 복사 (clone 이용)
    public static <T extends Cloneable> T[] deepCopy(T[] original) {
        if (original == null) return null;
        
        T[] copy = Arrays.copyOf(original, original.length);
        for (int i = 0; i < original.length; i++) {
            try {
                copy[i] = (T) original[i].getClass()
                                        .getMethod("clone")
                                        .invoke(original[i]);
            } catch (Exception e) {
                throw new RuntimeException(e);
            }
        }
        return copy;
    }
    
    public static void main(String[] args) {
        int[][] matrix = {{1, 2}, {3, 4}};
        int[][] copied = deepCopy2D(matrix);
        
        copied[0][0] = 999;
        System.out.println("Original: " + Arrays.deepToString(matrix));
        System.out.println("Copy: " + Arrays.deepToString(copied));
        // Original: [[1, 2], [3, 4]]
        // Copy: [[999, 2], [3, 4]]
    }
}
```

---

## 4. System.arraycopy()

### 4.1 기본 사용법

```java
public class ArrayCopyBasic {
    public static void main(String[] args) {
        int[] src = {1, 2, 3, 4, 5};
        int[] dest = new int[5];
        
        // System.arraycopy(src, srcPos, dest, destPos, length)
        System.arraycopy(src, 0, dest, 0, src.length);
        System.out.println(Arrays.toString(dest));
        // [1, 2, 3, 4, 5]
        
        
        // 부분 복사
        int[] dest2 = new int[10];
        System.arraycopy(src, 1, dest2, 3, 3);
        System.out.println(Arrays.toString(dest2));
        // [0, 0, 0, 2, 3, 4, 0, 0, 0, 0]
        // src[1~3] → dest2[3~5]
    }
}
```

### 4.2 Arrays.copyOf vs System.arraycopy

```java
public class CopyComparison {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        
        // Arrays.copyOf: 새 배열 생성
        int[] copy1 = Arrays.copyOf(arr, arr.length);
        
        // System.arraycopy: 기존 배열에 복사
        int[] copy2 = new int[arr.length];
        System.arraycopy(arr, 0, copy2, 0, arr.length);
        
        // 결과는 동일
        System.out.println(Arrays.equals(copy1, copy2));  // true
    }
}
```

**차이점:**

| 특징 | Arrays.copyOf() | System.arraycopy() |
|------|-----------------|-------------------|
| **사용성** | 간편 (한 줄) | 복잡 (파라미터 많음) |
| **배열 생성** | 자동 생성 | 수동 생성 필요 |
| **유연성** | 낮음 | 높음 (부분 복사) |
| **성능** | 동일 (내부적으로 arraycopy 사용) | 동일 |

### 4.3 배열 확장

```java
public class ArrayExpansion {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3};
        
        // 배열 확장 (Arrays.copyOf)
        arr = Arrays.copyOf(arr, arr.length * 2);
        System.out.println(Arrays.toString(arr));
        // [1, 2, 3, 0, 0, 0]
        
        
        // 배열 확장 (System.arraycopy)
        int[] old = {1, 2, 3};
        int[] newArr = new int[old.length * 2];
        System.arraycopy(old, 0, newArr, 0, old.length);
        System.out.println(Arrays.toString(newArr));
        // [1, 2, 3, 0, 0, 0]
    }
}
```

### 4.4 배열 삽입/삭제

```java
public class ArrayInsertDelete {
    // 특정 위치에 삽입
    public static int[] insert(int[] arr, int index, int value) {
        int[] newArr = new int[arr.length + 1];
        
        // index 전까지 복사
        System.arraycopy(arr, 0, newArr, 0, index);
        
        // 값 삽입
        newArr[index] = value;
        
        // index 이후 복사
        System.arraycopy(arr, index, newArr, index + 1, arr.length - index);
        
        return newArr;
    }
    
    // 특정 위치 삭제
    public static int[] delete(int[] arr, int index) {
        int[] newArr = new int[arr.length - 1];
        
        // index 전까지 복사
        System.arraycopy(arr, 0, newArr, 0, index);
        
        // index 이후 복사 (index 건너뜀)
        System.arraycopy(arr, index + 1, newArr, index, arr.length - index - 1);
        
        return newArr;
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        
        // 인덱스 2에 99 삽입
        arr = insert(arr, 2, 99);
        System.out.println(Arrays.toString(arr));
        // [1, 2, 99, 3, 4, 5]
        
        // 인덱스 2 삭제
        arr = delete(arr, 2);
        System.out.println(Arrays.toString(arr));
        // [1, 2, 3, 4, 5]
    }
}
```

### 4.5 자기 자신으로 복사

```java
public class SelfCopy {
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
        
        // 왼쪽으로 이동 (회전)
        System.arraycopy(arr, 2, arr, 0, 8);
        System.out.println(Arrays.toString(arr));
        // [3, 4, 5, 6, 7, 8, 9, 10, 9, 10]
        
        
        // 오른쪽으로 이동
        int[] arr2 = {1, 2, 3, 4, 5};
        System.arraycopy(arr2, 0, arr2, 2, 3);
        System.out.println(Arrays.toString(arr2));
        // [1, 2, 1, 2, 3]
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 배열 회전 복사

```java
// 배열을 k만큼 회전한 새 배열 반환
// 원본은 변경하지 않음
public class Problem1 {
    public static int[] rotateArray(int[] arr, int k) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] arr = {1, 2, 3, 4, 5};
        int[] rotated = rotateArray(arr, 2);
        
        System.out.println("Original: " + Arrays.toString(arr));
        System.out.println("Rotated: " + Arrays.toString(rotated));
        // Original: [1, 2, 3, 4, 5]
        // Rotated: [3, 4, 5, 1, 2]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static int[] rotateArray(int[] arr, int k) {
        int n = arr.length;
        k = k % n;  // k가 n보다 클 경우
        
        int[] result = new int[n];
        
        // 방법 1: System.arraycopy 사용
        System.arraycopy(arr, k, result, 0, n - k);
        System.arraycopy(arr, 0, result, n - k, k);
        
        return result;
    }
    
    // 방법 2: Arrays.copyOfRange 사용
    public static int[] rotateArray2(int[] arr, int k) {
        int n = arr.length;
        k = k % n;
        
        int[] left = Arrays.copyOfRange(arr, k, n);
        int[] right = Arrays.copyOfRange(arr, 0, k);
        
        int[] result = new int[n];
        System.arraycopy(left, 0, result, 0, left.length);
        System.arraycopy(right, 0, result, left.length, right.length);
        
        return result;
    }
}
```
</details>

---

### 문제 2: 2D 배열 비교

```java
// 두 2D 배열이 같은지 확인 (순서 무관)
// 각 행이 같은 내용이면 같다고 판단
public class Problem2 {
    public static boolean isSame(int[][] arr1, int[][] arr2) {
        // 여기에 코드 작성
        return false;
    }
    
    public static void main(String[] args) {
        int[][] arr1 = {{1, 2, 3}, {4, 5, 6}, {7, 8, 9}};
        int[][] arr2 = {{4, 5, 6}, {1, 2, 3}, {7, 8, 9}};
        
        System.out.println(isSame(arr1, arr2));  // true
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static boolean isSame(int[][] arr1, int[][] arr2) {
        if (arr1.length != arr2.length) return false;
        
        // 각 배열의 행들을 정렬
        int[][] sorted1 = new int[arr1.length][];
        int[][] sorted2 = new int[arr2.length][];
        
        for (int i = 0; i < arr1.length; i++) {
            sorted1[i] = Arrays.copyOf(arr1[i], arr1[i].length);
            sorted2[i] = Arrays.copyOf(arr2[i], arr2[i].length);
        }
        
        // 각 행을 정렬된 문자열로 변환하여 Set에 저장
        Set<String> set1 = new HashSet<>();
        Set<String> set2 = new HashSet<>();
        
        for (int[] row : sorted1) {
            Arrays.sort(row);
            set1.add(Arrays.toString(row));
        }
        
        for (int[] row : sorted2) {
            Arrays.sort(row);
            set2.add(Arrays.toString(row));
        }
        
        return set1.equals(set2);
    }
}
```
</details>

---

### 문제 3: 배열 병합

```java
// 두 정렬된 배열을 하나의 정렬된 배열로 병합
public class Problem3 {
    public static int[] merge(int[] arr1, int[] arr2) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] arr1 = {1, 3, 5, 7};
        int[] arr2 = {2, 4, 6, 8};
        
        int[] merged = merge(arr1, arr2);
        System.out.println(Arrays.toString(merged));
        // [1, 2, 3, 4, 5, 6, 7, 8]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int[] merge(int[] arr1, int[] arr2) {
        int[] result = new int[arr1.length + arr2.length];
        int i = 0, j = 0, k = 0;
        
        // 두 배열을 비교하며 병합
        while (i < arr1.length && j < arr2.length) {
            if (arr1[i] <= arr2[j]) {
                result[k++] = arr1[i++];
            } else {
                result[k++] = arr2[j++];
            }
        }
        
        // 남은 원소 복사
        while (i < arr1.length) {
            result[k++] = arr1[i++];
        }
        
        while (j < arr2.length) {
            result[k++] = arr2[j++];
        }
        
        return result;
    }
    
    // System.arraycopy 사용 버전
    public static int[] merge2(int[] arr1, int[] arr2) {
        int[] result = new int[arr1.length + arr2.length];
        int i = 0, j = 0, k = 0;
        
        while (i < arr1.length && j < arr2.length) {
            if (arr1[i] <= arr2[j]) {
                result[k++] = arr1[i++];
            } else {
                result[k++] = arr2[j++];
            }
        }
        
        // 남은 원소를 한 번에 복사
        if (i < arr1.length) {
            System.arraycopy(arr1, i, result, k, arr1.length - i);
        }
        if (j < arr2.length) {
            System.arraycopy(arr2, j, result, k, arr2.length - j);
        }
        
        return result;
    }
}
```
</details>

---

## 📌 핵심 정리

### 배열 비교
```java
Arrays.equals(arr1, arr2)          // 1차원 비교
Arrays.deepEquals(arr1, arr2)      // 다차원 비교
Arrays.compare(arr1, arr2)         // 사전순 비교 (Java 9+)
Arrays.mismatch(arr1, arr2)        // 첫 불일치 인덱스 (Java 9+)
```

### 배열 복사
```java
// 간단한 복사
Arrays.copyOf(arr, length)         // 전체 또는 일부
Arrays.copyOfRange(arr, from, to)  // 범위 복사

// 유연한 복사
System.arraycopy(src, srcPos, dest, destPos, length)
```

### 얕은 복사 vs 깊은 복사
```java
// 얕은 복사 (배열만 복사, 원소는 공유)
arr2 = Arrays.copyOf(arr1, arr1.length);

// 깊은 복사 (원소까지 복사)
for (int i = 0; i < arr.length; i++) {
    arr2[i] = arr1[i].clone();
}
```

### 선택 가이드
- **단순 복사**: `Arrays.copyOf()`
- **부분 복사**: `Arrays.copyOfRange()`
- **복잡한 복사**: `System.arraycopy()`
- **2D 깊은 복사**: 각 행마다 `Arrays.copyOf()`

### 주의사항
```java
// ❌ 참조만 복사
arr2 = arr1;

// ❌ 얕은 복사 (객체 배열)
Person[] copy = Arrays.copyOf(original, original.length);
// 객체는 공유됨!

// ✅ 깊은 복사
for (int i = 0; i < original.length; i++) {
    copy[i] = original[i].clone();
}
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 03. Arrays 검색](./Arrays-03-검색.md) | [다음: 05. Arrays 변환 →](./Arrays-05-변환.md)**

</div>
