# Arrays 06. 다차원 배열

> 2D, 3D 배열 완전 정복 - 생성부터 순회, 응용까지  
> Java API Reference

---

## 📑 목차

1. [2D 배열 기본](#1-2d-배열-기본)
2. [2D 배열 순회와 연산](#2-2d-배열-순회와-연산)
3. [가변 배열 (Jagged Array)](#3-가변-배열-jagged-array)
4. [3D 배열 및 고차원](#4-3d-배열-및-고차원)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. 2D 배열 기본

### 1.1 선언과 생성

```java
public class TwoDArrayBasic {
    public static void main(String[] args) {
        // 방법 1: 크기 지정
        int[][] arr1 = new int[3][4];  // 3행 4열
        
        // 방법 2: 초기값 지정
        int[][] arr2 = {
            {1, 2, 3, 4},
            {5, 6, 7, 8},
            {9, 10, 11, 12}
        };
        
        // 방법 3: new와 함께
        int[][] arr3 = new int[][] {
            {1, 2},
            {3, 4},
            {5, 6}
        };
        
        
        // 길이 확인
        System.out.println("행 개수: " + arr2.length);        // 3
        System.out.println("열 개수: " + arr2[0].length);     // 4
        
        
        // 접근
        System.out.println(arr2[1][2]);  // 7 (2행 3열)
    }
}
```

**메모리 구조:**
```
arr[3][4]:
arr → [행0] → [1][2][3][4]
      [행1] → [5][6][7][8]
      [행2] → [9][10][11][12]
```

### 1.2 출력

```java
import java.util.Arrays;

public class TwoDArrayPrint {
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        // ❌ toString() - 주소값
        System.out.println(matrix);
        // [[I@15db9742
        
        // ❌ Arrays.toString() - 1차원만
        System.out.println(Arrays.toString(matrix));
        // [[I@15db9742, [I@6d06d69c, [I@7852e922]
        
        
        // ✅ Arrays.deepToString() - 다차원
        System.out.println(Arrays.deepToString(matrix));
        // [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
        
        
        // 행별 출력
        for (int[] row : matrix) {
            System.out.println(Arrays.toString(row));
        }
        // [1, 2, 3]
        // [4, 5, 6]
        // [7, 8, 9]
    }
}
```

### 1.3 초기화

```java
public class TwoDArrayInit {
    public static void main(String[] args) {
        int n = 5, m = 4;
        
        // 모두 0으로 (기본값)
        int[][] arr1 = new int[n][m];
        
        
        // 특정 값으로 채우기
        int[][] arr2 = new int[n][m];
        for (int[] row : arr2) {
            Arrays.fill(row, -1);
        }
        System.out.println(Arrays.deepToString(arr2));
        // [[-1, -1, -1, -1], ...]
        
        
        // 1차원 인덱스로 채우기
        int[][] arr3 = new int[n][m];
        int value = 1;
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < m; j++) {
                arr3[i][j] = value++;
            }
        }
        System.out.println(Arrays.deepToString(arr3));
        // [[1,2,3,4], [5,6,7,8], ...]
    }
}
```

---

## 2. 2D 배열 순회와 연산

### 2.1 순회 방법

```java
public class TwoDArrayTraversal {
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        // 방법 1: 일반 for문
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                System.out.print(matrix[i][j] + " ");
            }
            System.out.println();
        }
        
        
        // 방법 2: 향상된 for문
        for (int[] row : matrix) {
            for (int num : row) {
                System.out.print(num + " ");
            }
            System.out.println();
        }
        
        
        // 방법 3: Stream
        Arrays.stream(matrix)
              .flatMapToInt(Arrays::stream)
              .forEach(n -> System.out.print(n + " "));
        System.out.println();
    }
}
```

### 2.2 행/열 연산

```java
public class RowColumnOps {
    // 행 합계
    public static int[] rowSums(int[][] matrix) {
        int[] sums = new int[matrix.length];
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < matrix[i].length; j++) {
                sums[i] += matrix[i][j];
            }
        }
        return sums;
    }
    
    // 열 합계
    public static int[] colSums(int[][] matrix) {
        int cols = matrix[0].length;
        int[] sums = new int[cols];
        
        for (int i = 0; i < matrix.length; i++) {
            for (int j = 0; j < cols; j++) {
                sums[j] += matrix[i][j];
            }
        }
        return sums;
    }
    
    // 전체 합계
    public static int totalSum(int[][] matrix) {
        return Arrays.stream(matrix)
                    .flatMapToInt(Arrays::stream)
                    .sum();
    }
    
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        System.out.println("Row sums: " + Arrays.toString(rowSums(matrix)));
        // [6, 15, 24]
        
        System.out.println("Col sums: " + Arrays.toString(colSums(matrix)));
        // [12, 15, 18]
        
        System.out.println("Total: " + totalSum(matrix));
        // 45
    }
}
```

### 2.3 전치 (Transpose)

```java
public class MatrixTranspose {
    public static int[][] transpose(int[][] matrix) {
        int rows = matrix.length;
        int cols = matrix[0].length;
        
        int[][] result = new int[cols][rows];
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                result[j][i] = matrix[i][j];
            }
        }
        
        return result;
    }
    
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6}
        };
        
        System.out.println("Original: " + Arrays.deepToString(matrix));
        // [[1, 2, 3], [4, 5, 6]]
        
        int[][] transposed = transpose(matrix);
        System.out.println("Transposed: " + Arrays.deepToString(transposed));
        // [[1, 4], [2, 5], [3, 6]]
    }
}
```

### 2.4 회전

```java
public class MatrixRotation {
    // 시계방향 90도 회전
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
    
    // In-place 회전 (정사각형만)
    public static void rotate90InPlace(int[][] matrix) {
        int n = matrix.length;
        
        // 1. 전치
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

### 2.5 대각선

```java
public class DiagonalOps {
    // 주 대각선 합
    public static int mainDiagonalSum(int[][] matrix) {
        int sum = 0;
        for (int i = 0; i < matrix.length; i++) {
            sum += matrix[i][i];
        }
        return sum;
    }
    
    // 부 대각선 합
    public static int antiDiagonalSum(int[][] matrix) {
        int sum = 0;
        int n = matrix.length;
        for (int i = 0; i < n; i++) {
            sum += matrix[i][n - 1 - i];
        }
        return sum;
    }
    
    // 대각선 순회
    public static void printDiagonals(int[][] matrix) {
        int n = matrix.length;
        
        // 주 대각선
        System.out.print("Main: ");
        for (int i = 0; i < n; i++) {
            System.out.print(matrix[i][i] + " ");
        }
        System.out.println();
        
        // 부 대각선
        System.out.print("Anti: ");
        for (int i = 0; i < n; i++) {
            System.out.print(matrix[i][n - 1 - i] + " ");
        }
        System.out.println();
    }
    
    public static void main(String[] args) {
        int[][] matrix = {
            {1, 2, 3},
            {4, 5, 6},
            {7, 8, 9}
        };
        
        System.out.println("Main diagonal sum: " + mainDiagonalSum(matrix));
        // 15 (1+5+9)
        
        System.out.println("Anti diagonal sum: " + antiDiagonalSum(matrix));
        // 15 (3+5+7)
        
        printDiagonals(matrix);
        // Main: 1 5 9
        // Anti: 3 5 7
    }
}
```

---

## 3. 가변 배열 (Jagged Array)

### 3.1 생성

```java
public class JaggedArray {
    public static void main(String[] args) {
        // 행만 먼저 할당
        int[][] jagged = new int[3][];
        
        // 각 행의 크기를 다르게
        jagged[0] = new int[2];      // 2개
        jagged[1] = new int[4];      // 4개
        jagged[2] = new int[3];      // 3개
        
        
        // 초기값으로 생성
        int[][] jagged2 = {
            {1, 2},
            {3, 4, 5, 6},
            {7, 8, 9}
        };
        
        System.out.println(Arrays.deepToString(jagged2));
        // [[1, 2], [3, 4, 5, 6], [7, 8, 9]]
    }
}
```

**메모리 구조:**
```
jagged → [행0] → [1][2]
         [행1] → [3][4][5][6]
         [행2] → [7][8][9]
```

### 3.2 순회

```java
public class JaggedArrayTraversal {
    public static void main(String[] args) {
        int[][] jagged = {
            {1, 2},
            {3, 4, 5},
            {6, 7, 8, 9}
        };
        
        // 각 행의 길이가 다름!
        for (int i = 0; i < jagged.length; i++) {
            for (int j = 0; j < jagged[i].length; j++) {
                System.out.print(jagged[i][j] + " ");
            }
            System.out.println();
        }
        
        
        // 향상된 for문 (더 안전)
        for (int[] row : jagged) {
            for (int num : row) {
                System.out.print(num + " ");
            }
            System.out.println();
        }
    }
}
```

### 3.3 활용 예시

```java
public class JaggedArrayUseCases {
    public static void main(String[] args) {
        // 파스칼의 삼각형
        int[][] pascal = new int[5][];
        for (int i = 0; i < 5; i++) {
            pascal[i] = new int[i + 1];
            pascal[i][0] = 1;
            pascal[i][i] = 1;
            
            for (int j = 1; j < i; j++) {
                pascal[i][j] = pascal[i-1][j-1] + pascal[i-1][j];
            }
        }
        
        System.out.println(Arrays.deepToString(pascal));
        // [[1], [1, 1], [1, 2, 1], [1, 3, 3, 1], [1, 4, 6, 4, 1]]
        
        
        // 그래프 인접 리스트
        int[][] graph = new int[5][];
        graph[0] = new int[]{1, 2};      // 0번 정점의 이웃
        graph[1] = new int[]{0, 2, 3};   // 1번 정점의 이웃
        graph[2] = new int[]{0, 1, 4};
        graph[3] = new int[]{1};
        graph[4] = new int[]{2};
    }
}
```

---

## 4. 3D 배열 및 고차원

### 4.1 3D 배열

```java
public class ThreeDArray {
    public static void main(String[] args) {
        // 3x4x5 배열 (3개 층, 4행, 5열)
        int[][][] cube = new int[3][4][5];
        
        // 초기값 지정
        int[][][] cube2 = {
            {
                {1, 2, 3},
                {4, 5, 6}
            },
            {
                {7, 8, 9},
                {10, 11, 12}
            }
        };
        
        System.out.println(Arrays.deepToString(cube2));
        // [[[1, 2, 3], [4, 5, 6]], [[7, 8, 9], [10, 11, 12]]]
        
        
        // 접근
        System.out.println(cube2[1][0][2]);  // 9
        // 층1, 행0, 열2
    }
}
```

### 4.2 3D 순회

```java
public class ThreeDTraversal {
    public static void main(String[] args) {
        int[][][] cube = {
            {{1, 2}, {3, 4}},
            {{5, 6}, {7, 8}}
        };
        
        // 3중 for문
        for (int i = 0; i < cube.length; i++) {
            System.out.println("Layer " + i + ":");
            for (int j = 0; j < cube[i].length; j++) {
                for (int k = 0; k < cube[i][j].length; k++) {
                    System.out.print(cube[i][j][k] + " ");
                }
                System.out.println();
            }
        }
        
        
        // 향상된 for문
        for (int[][] layer : cube) {
            for (int[] row : layer) {
                for (int num : row) {
                    System.out.print(num + " ");
                }
                System.out.println();
            }
            System.out.println("---");
        }
    }
}
```

### 4.3 4D 이상

```java
public class HigherDimensions {
    public static void main(String[] args) {
        // 4D 배열 (시간, 층, 행, 열)
        int[][][][] tensor4d = new int[2][3][4][5];
        
        // 크기 확인
        System.out.println("Dim 0: " + tensor4d.length);        // 2
        System.out.println("Dim 1: " + tensor4d[0].length);     // 3
        System.out.println("Dim 2: " + tensor4d[0][0].length);  // 4
        System.out.println("Dim 3: " + tensor4d[0][0][0].length); // 5
        
        
        // 실용적으로는 잘 안 쓰임
        // 대신 객체 배열이나 List 사용 추천
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 나선형 순회

```java
// n x n 배열을 나선형으로 채우기
// 1 2 3
// 8 9 4
// 7 6 5
public class Problem1 {
    public static int[][] spiralMatrix(int n) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[][] result = spiralMatrix(3);
        System.out.println(Arrays.deepToString(result));
        // [[1, 2, 3], [8, 9, 4], [7, 6, 5]]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static int[][] spiralMatrix(int n) {
        int[][] matrix = new int[n][n];
        
        int top = 0, bottom = n - 1;
        int left = 0, right = n - 1;
        int num = 1;
        
        while (top <= bottom && left <= right) {
            // 오른쪽으로
            for (int i = left; i <= right; i++) {
                matrix[top][i] = num++;
            }
            top++;
            
            // 아래로
            for (int i = top; i <= bottom; i++) {
                matrix[i][right] = num++;
            }
            right--;
            
            // 왼쪽으로
            if (top <= bottom) {
                for (int i = right; i >= left; i--) {
                    matrix[bottom][i] = num++;
                }
                bottom--;
            }
            
            // 위로
            if (left <= right) {
                for (int i = bottom; i >= top; i--) {
                    matrix[i][left] = num++;
                }
                left++;
            }
        }
        
        return matrix;
    }
}
```
</details>

---

### 문제 2: 행렬 곱셈

```java
// A(m x n) * B(n x p) = C(m x p)
public class Problem2 {
    public static int[][] matrixMultiply(int[][] A, int[][] B) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[][] A = {{1, 2}, {3, 4}};
        int[][] B = {{5, 6}, {7, 8}};
        
        int[][] C = matrixMultiply(A, B);
        System.out.println(Arrays.deepToString(C));
        // [[19, 22], [43, 50]]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int[][] matrixMultiply(int[][] A, int[][] B) {
        int m = A.length;
        int n = A[0].length;
        int p = B[0].length;
        
        // B의 행 개수가 A의 열 개수와 같아야 함
        if (n != B.length) {
            throw new IllegalArgumentException("Invalid matrix dimensions");
        }
        
        int[][] C = new int[m][p];
        
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < p; j++) {
                for (int k = 0; k < n; k++) {
                    C[i][j] += A[i][k] * B[k][j];
                }
            }
        }
        
        return C;
    }
}
```
</details>

---

### 문제 3: 섬의 개수

```java
// 1은 땅, 0은 물. 연결된 땅의 개수 (섬) 세기
// 상하좌우로만 연결
public class Problem3 {
    public static int numIslands(int[][] grid) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        int[][] grid = {
            {1, 1, 0, 0, 0},
            {1, 1, 0, 0, 0},
            {0, 0, 1, 0, 0},
            {0, 0, 0, 1, 1}
        };
        
        System.out.println(numIslands(grid));  // 3
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int numIslands(int[][] grid) {
        if (grid == null || grid.length == 0) return 0;
        
        int count = 0;
        int rows = grid.length;
        int cols = grid[0].length;
        
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                if (grid[i][j] == 1) {
                    count++;
                    dfs(grid, i, j);
                }
            }
        }
        
        return count;
    }
    
    private static void dfs(int[][] grid, int i, int j) {
        int rows = grid.length;
        int cols = grid[0].length;
        
        if (i < 0 || i >= rows || j < 0 || j >= cols || grid[i][j] == 0) {
            return;
        }
        
        grid[i][j] = 0;  // 방문 표시
        
        // 상하좌우 탐색
        dfs(grid, i - 1, j);
        dfs(grid, i + 1, j);
        dfs(grid, i, j - 1);
        dfs(grid, i, j + 1);
    }
}
```
</details>

---

## 📌 핵심 정리

### 2D 배열
```java
// 생성
int[][] arr = new int[3][4];           // 3행 4열
int[][] arr = {{1,2}, {3,4}, {5,6}};

// 출력
Arrays.deepToString(arr)               // 다차원 배열

// 길이
arr.length                             // 행 개수
arr[0].length                          // 열 개수

// 순회
for (int i = 0; i < arr.length; i++) {
    for (int j = 0; j < arr[i].length; j++) {
        // arr[i][j]
    }
}
```

### 가변 배열
```java
int[][] jagged = new int[3][];
jagged[0] = new int[2];
jagged[1] = new int[4];
jagged[2] = new int[3];

// 각 행의 길이가 다름!
for (int[] row : jagged) {
    for (int num : row) {
        // ...
    }
}
```

### 3D 배열
```java
int[][][] cube = new int[3][4][5];     // 층, 행, 열
cube[1][2][3]                          // 층1, 행2, 열3

Arrays.deepToString(cube)              // 출력
```

### 주의사항
```java
// ❌ toString() - 주소값
System.out.println(arr);

// ❌ Arrays.toString() - 1차원만
System.out.println(Arrays.toString(arr));

// ✅ Arrays.deepToString() - 다차원
System.out.println(Arrays.deepToString(arr));

// ❌ 가변 배열에서 고정 길이 가정
for (int j = 0; j < arr[0].length; j++)  // 위험!

// ✅ 각 행의 길이 확인
for (int j = 0; j < arr[i].length; j++)
```

---

## 🎉 Arrays 시리즈 완료!

<div align="center">

### 📚 전체 시리즈

|               Chapter               | 주제 |
|:-----------------------------------:|------|
|  [01. 배열 기본](./Arrays-01-배열기본.md)   | 배열 기초와 Arrays 클래스 |
|     [02. 정렬](./Arrays-02-정렬.md)     | sort, parallelSort, Comparator |
|     [03. 검색](./Arrays-03-검색.md)     | binarySearch, Lower/Upper Bound |
| [04. 비교와 복사](./Arrays-04-비교와복사.md)  | equals, copyOf, 깊은 복사 |
|     [05. 변환](./Arrays-05-변환.md)     | asList, stream, List/배열 변환 |
| [06. 다차원 배열](./Arrays-06-다차원배열.md)  | 2D/3D 배열, 가변 배열 |

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 05. Arrays 변환](./Arrays-05-변환.md)**

</div>
