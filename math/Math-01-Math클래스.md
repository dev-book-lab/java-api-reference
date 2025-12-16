# Math 01. Math 클래스

> 수학 연산의 모든 것 - java.lang.Math 완전 정복  
> Java API Reference

---

## 📑 목차

1. [기본 연산](#1-기본-연산)
2. [최대값과 최소값](#2-최대값과-최소값)
3. [절댓값과 부호](#3-절댓값과-부호)
4. [거듭제곱과 제곱근](#4-거듭제곱과-제곱근)
5. [반올림과 버림](#5-반올림과-버림)
6. [삼각함수와 로그](#6-삼각함수와-로그)
7. [난수 생성](#7-난수-생성)
8. [실전 연습 문제](#8-실전-연습-문제)

---

## 1. 기본 연산

### 1.1 Math 클래스 소개

```java
public class MathIntro {
    public static void main(String[] args) {
        // Math 클래스는 static 메서드만 제공
        // 인스턴스 생성 불가 (생성자가 private)
        // Math math = new Math();  // 컴파일 에러!
        
        // 모든 메서드를 클래스명으로 직접 호출
        double result = Math.abs(-10);
        System.out.println("abs(-10) = " + result);
        
        
        // 상수
        System.out.println("PI = " + Math.PI);      // 3.141592653589793
        System.out.println("E = " + Math.E);        // 2.718281828459045
    }
}
```

### 1.2 상수

```java
public class MathConstants {
    public static void main(String[] args) {
        // 원주율 (π)
        double pi = Math.PI;
        System.out.println("π = " + pi);
        // 3.141592653589793
        
        
        // 자연로그의 밑 (e)
        double e = Math.E;
        System.out.println("e = " + e);
        // 2.718281828459045
        
        
        // 활용 예시
        double radius = 5.0;
        double area = Math.PI * radius * radius;
        double circumference = 2 * Math.PI * radius;
        
        System.out.println("반지름 " + radius + "인 원의 넓이: " + area);
        System.out.println("반지름 " + radius + "인 원의 둘레: " + circumference);
    }
}
```

---

## 2. 최대값과 최소값

### 2.1 max / min

```java
public class MaxMin {
    public static void main(String[] args) {
        // int
        int maxInt = Math.max(10, 20);
        int minInt = Math.min(10, 20);
        
        System.out.println("max(10, 20) = " + maxInt);  // 20
        System.out.println("min(10, 20) = " + minInt);  // 10
        
        
        // long
        long maxLong = Math.max(100L, 200L);
        long minLong = Math.min(100L, 200L);
        
        
        // float
        float maxFloat = Math.max(3.14f, 2.71f);
        float minFloat = Math.min(3.14f, 2.71f);
        
        
        // double
        double maxDouble = Math.max(3.14, 2.71);
        double minDouble = Math.min(3.14, 2.71);
        
        System.out.println("max(3.14, 2.71) = " + maxDouble);  // 3.14
        System.out.println("min(3.14, 2.71) = " + minDouble);  // 2.71
        
        
        // 여러 값 중 최대/최소 (체이닝)
        int max = Math.max(Math.max(10, 20), 30);
        int min = Math.min(Math.min(10, 20), 30);
        
        System.out.println("max(10, 20, 30) = " + max);  // 30
        System.out.println("min(10, 20, 30) = " + min);  // 10
    }
}
```

### 2.2 배열에서 최대/최소

```java
public class ArrayMaxMin {
    public static void main(String[] args) {
        int[] numbers = {5, 2, 8, 1, 9, 3};
        
        // 방법 1: 반복문
        int max = numbers[0];
        int min = numbers[0];
        
        for (int num : numbers) {
            max = Math.max(max, num);
            min = Math.min(min, num);
        }
        
        System.out.println("Max: " + max);  // 9
        System.out.println("Min: " + min);  // 1
        
        
        // 방법 2: Stream (Java 8+)
        int streamMax = Arrays.stream(numbers).max().orElse(0);
        int streamMin = Arrays.stream(numbers).min().orElse(0);
        
        System.out.println("Stream Max: " + streamMax);  // 9
        System.out.println("Stream Min: " + streamMin);  // 1
    }
}
```

---

## 3. 절댓값과 부호

### 3.1 abs - 절댓값

```java
public class AbsoluteValue {
    public static void main(String[] args) {
        // int
        int absInt = Math.abs(-10);
        System.out.println("abs(-10) = " + absInt);  // 10
        
        
        // long
        long absLong = Math.abs(-100L);
        System.out.println("abs(-100L) = " + absLong);  // 100
        
        
        // float
        float absFloat = Math.abs(-3.14f);
        System.out.println("abs(-3.14f) = " + absFloat);  // 3.14
        
        
        // double
        double absDouble = Math.abs(-2.71);
        System.out.println("abs(-2.71) = " + absDouble);  // 2.71
        
        
        // 활용: 두 수의 차이
        int a = 10, b = 25;
        int difference = Math.abs(a - b);
        System.out.println("차이: " + difference);  // 15
        
        
        // 주의: Integer.MIN_VALUE
        int minValue = Integer.MIN_VALUE;
        int absMin = Math.abs(minValue);
        System.out.println("abs(MIN_VALUE) = " + absMin);
        // -2147483648 (오버플로우!)
        
        // 안전한 처리
        long safeMath = Math.abs((long) minValue);
        System.out.println("Safe abs = " + safeMath);  // 2147483648
    }
}
```

### 3.2 signum - 부호 판별

```java
public class SignumFunction {
    public static void main(String[] args) {
        // signum: 부호 반환 (-1.0, 0.0, 1.0)
        
        double positive = Math.signum(10.5);
        double zero = Math.signum(0.0);
        double negative = Math.signum(-5.3);
        
        System.out.println("signum(10.5) = " + positive);   // 1.0
        System.out.println("signum(0.0) = " + zero);        // 0.0
        System.out.println("signum(-5.3) = " + negative);   // -1.0
        
        
        // float 버전
        float signumFloat = Math.signum(-3.14f);
        System.out.println("signum(-3.14f) = " + signumFloat);  // -1.0
        
        
        // 활용: 방향 결정
        double velocity = -50.0;
        double direction = Math.signum(velocity);
        
        if (direction > 0) {
            System.out.println("앞으로 이동");
        } else if (direction < 0) {
            System.out.println("뒤로 이동");
        } else {
            System.out.println("정지");
        }
    }
}
```

---

## 4. 거듭제곱과 제곱근

### 4.1 pow - 거듭제곱

```java
public class PowerFunction {
    public static void main(String[] args) {
        // Math.pow(a, b): a^b
        
        double square = Math.pow(5, 2);
        System.out.println("5^2 = " + square);  // 25.0
        
        double cube = Math.pow(3, 3);
        System.out.println("3^3 = " + cube);    // 27.0
        
        double power = Math.pow(2, 10);
        System.out.println("2^10 = " + power);  // 1024.0
        
        
        // 음수 지수 (분수)
        double reciprocal = Math.pow(2, -1);
        System.out.println("2^-1 = " + reciprocal);  // 0.5
        
        double quarter = Math.pow(2, -2);
        System.out.println("2^-2 = " + quarter);     // 0.25
        
        
        // 실수 지수
        double root = Math.pow(8, 1.0/3.0);
        System.out.println("8^(1/3) = " + root);  // 2.0 (세제곱근)
        
        
        // 0과 1의 거듭제곱
        System.out.println("0^5 = " + Math.pow(0, 5));    // 0.0
        System.out.println("1^100 = " + Math.pow(1, 100)); // 1.0
        
        
        // 특수 케이스
        System.out.println("NaN^2 = " + Math.pow(Double.NaN, 2));  // NaN
        System.out.println("∞^2 = " + Math.pow(Double.POSITIVE_INFINITY, 2)); // Infinity
    }
}
```

### 4.2 sqrt - 제곱근

```java
public class SquareRoot {
    public static void main(String[] args) {
        // Math.sqrt(x): √x
        
        double sqrt4 = Math.sqrt(4);
        System.out.println("√4 = " + sqrt4);    // 2.0
        
        double sqrt9 = Math.sqrt(9);
        System.out.println("√9 = " + sqrt9);    // 3.0
        
        double sqrt2 = Math.sqrt(2);
        System.out.println("√2 = " + sqrt2);    // 1.4142135623730951
        
        
        // 활용: 피타고라스 정리
        double a = 3.0, b = 4.0;
        double c = Math.sqrt(a*a + b*b);
        System.out.println("빗변 길이: " + c);  // 5.0
        
        
        // 활용: 두 점 사이 거리
        double x1 = 0, y1 = 0;
        double x2 = 3, y2 = 4;
        double distance = Math.sqrt(Math.pow(x2-x1, 2) + Math.pow(y2-y1, 2));
        System.out.println("거리: " + distance);  // 5.0
        
        
        // 음수의 제곱근
        double sqrtNegative = Math.sqrt(-1);
        System.out.println("√-1 = " + sqrtNegative);  // NaN
    }
}
```

### 4.3 cbrt - 세제곱근

```java
public class CubeRoot {
    public static void main(String[] args) {
        // Math.cbrt(x): ∛x
        
        double cbrt8 = Math.cbrt(8);
        System.out.println("∛8 = " + cbrt8);    // 2.0
        
        double cbrt27 = Math.cbrt(27);
        System.out.println("∛27 = " + cbrt27);  // 3.0
        
        
        // 음수도 가능 (sqrt와 다름)
        double cbrtNegative = Math.cbrt(-8);
        System.out.println("∛-8 = " + cbrtNegative);  // -2.0
        
        
        // pow vs cbrt
        double cbrtByPow = Math.pow(8, 1.0/3.0);
        double cbrtByFunc = Math.cbrt(8);
        
        System.out.println("pow 방식: " + cbrtByPow);   // 2.0
        System.out.println("cbrt 함수: " + cbrtByFunc); // 2.0
        // cbrt가 더 정확하고 빠름
    }
}
```

### 4.4 hypot - 빗변 계산

```java
public class Hypotenuse {
    public static void main(String[] args) {
        // Math.hypot(x, y): √(x² + y²)
        // 오버플로우 없이 빗변 계산
        
        double a = 3.0, b = 4.0;
        
        // 방법 1: 직접 계산
        double c1 = Math.sqrt(a*a + b*b);
        
        // 방법 2: hypot 사용 (권장)
        double c2 = Math.hypot(a, b);
        
        System.out.println("직접 계산: " + c1);  // 5.0
        System.out.println("hypot: " + c2);     // 5.0
        
        
        // 활용: 두 점 사이 거리
        double x1 = 1, y1 = 2;
        double x2 = 4, y2 = 6;
        double distance = Math.hypot(x2-x1, y2-y1);
        
        System.out.println("거리: " + distance);  // 5.0
        
        
        // 큰 수에서도 안전
        double large1 = 1e200;
        double large2 = 1e200;
        
        // double overflow = Math.sqrt(large1*large1 + large2*large2); // Infinity
        double safe = Math.hypot(large1, large2);
        System.out.println("Safe calculation: " + safe);  // 1.414...e200
    }
}
```

---

## 5. 반올림과 버림

### 5.1 round - 반올림

```java
public class RoundFunction {
    public static void main(String[] args) {
        // Math.round(float): long 반환
        long round1 = Math.round(3.4f);
        long round2 = Math.round(3.5f);
        long round3 = Math.round(3.6f);
        
        System.out.println("round(3.4) = " + round1);  // 3
        System.out.println("round(3.5) = " + round2);  // 4 (올림)
        System.out.println("round(3.6) = " + round3);  // 4
        
        
        // Math.round(double): long 반환
        long round4 = Math.round(3.14159);
        System.out.println("round(3.14159) = " + round4);  // 3
        
        
        // 음수
        long round5 = Math.round(-3.4);
        long round6 = Math.round(-3.5);
        long round7 = Math.round(-3.6);
        
        System.out.println("round(-3.4) = " + round5);  // -3
        System.out.println("round(-3.5) = " + round6);  // -3 (0 방향)
        System.out.println("round(-3.6) = " + round7);  // -4
        
        
        // 소수점 둘째 자리 반올림
        double value = 3.14159;
        double rounded = Math.round(value * 100.0) / 100.0;
        System.out.println("둘째 자리: " + rounded);  // 3.14
    }
}
```

### 5.2 floor - 내림

```java
public class FloorFunction {
    public static void main(String[] args) {
        // Math.floor(x): x보다 작거나 같은 최대 정수
        
        double floor1 = Math.floor(3.4);
        double floor2 = Math.floor(3.9);
        double floor3 = Math.floor(3.0);
        
        System.out.println("floor(3.4) = " + floor1);  // 3.0
        System.out.println("floor(3.9) = " + floor2);  // 3.0
        System.out.println("floor(3.0) = " + floor3);  // 3.0
        
        
        // 음수
        double floor4 = Math.floor(-3.4);
        double floor5 = Math.floor(-3.9);
        
        System.out.println("floor(-3.4) = " + floor4);  // -4.0 (더 작은 정수)
        System.out.println("floor(-3.9) = " + floor5);  // -4.0
        
        
        // 활용: 나이 계산
        double age = 25.8;
        int flooredAge = (int) Math.floor(age);
        System.out.println("만 나이: " + flooredAge);  // 25
        
        
        // 활용: 페이지 계산
        int totalItems = 107;
        int itemsPerPage = 10;
        int pages = (int) Math.ceil((double) totalItems / itemsPerPage);
        System.out.println("페이지 수: " + pages);  // 11
    }
}
```

### 5.3 ceil - 올림

```java
public class CeilFunction {
    public static void main(String[] args) {
        // Math.ceil(x): x보다 크거나 같은 최소 정수
        
        double ceil1 = Math.ceil(3.1);
        double ceil2 = Math.ceil(3.9);
        double ceil3 = Math.ceil(3.0);
        
        System.out.println("ceil(3.1) = " + ceil1);  // 4.0
        System.out.println("ceil(3.9) = " + ceil2);  // 4.0
        System.out.println("ceil(3.0) = " + ceil3);  // 3.0
        
        
        // 음수
        double ceil4 = Math.ceil(-3.1);
        double ceil5 = Math.ceil(-3.9);
        
        System.out.println("ceil(-3.1) = " + ceil4);  // -3.0 (더 큰 정수)
        System.out.println("ceil(-3.9) = " + ceil5);  // -3.0
        
        
        // 활용: 필요한 상자 개수
        int items = 23;
        int boxCapacity = 5;
        int boxes = (int) Math.ceil((double) items / boxCapacity);
        System.out.println("필요한 상자: " + boxes);  // 5
    }
}
```

### 5.4 rint - 가장 가까운 정수

```java
public class RintFunction {
    public static void main(String[] args) {
        // Math.rint(x): x에 가장 가까운 정수 (double 반환)
        
        double rint1 = Math.rint(3.4);
        double rint2 = Math.rint(3.5);
        double rint3 = Math.rint(3.6);
        
        System.out.println("rint(3.4) = " + rint1);  // 3.0
        System.out.println("rint(3.5) = " + rint2);  // 4.0
        System.out.println("rint(3.6) = " + rint3);  // 4.0
        
        
        // 특이 케이스: 정확히 중간값
        double rint4 = Math.rint(2.5);  // 짝수로 반올림
        double rint5 = Math.rint(3.5);  // 짝수로 반올림
        
        System.out.println("rint(2.5) = " + rint4);  // 2.0 (짝수)
        System.out.println("rint(3.5) = " + rint5);  // 4.0 (짝수)
        
        
        // round vs rint
        long round = Math.round(2.5);
        double rint = Math.rint(2.5);
        
        System.out.println("round(2.5) = " + round);  // 3 (올림)
        System.out.println("rint(2.5) = " + rint);    // 2.0 (짝수)
    }
}
```

---

## 6. 삼각함수와 로그

### 6.1 삼각함수

```java
public class TrigonometricFunctions {
    public static void main(String[] args) {
        // 라디안 단위 사용!
        
        // sin, cos, tan
        double angle = Math.PI / 4;  // 45도
        
        double sin = Math.sin(angle);
        double cos = Math.cos(angle);
        double tan = Math.tan(angle);
        
        System.out.println("sin(45°) = " + sin);  // 0.707...
        System.out.println("cos(45°) = " + cos);  // 0.707...
        System.out.println("tan(45°) = " + tan);  // 1.0
        
        
        // 도(degree)를 라디안으로 변환
        double degrees = 45;
        double radians = Math.toRadians(degrees);
        System.out.println("45도 = " + radians + " 라디안");
        
        
        // 라디안을 도로 변환
        double rad = Math.PI / 2;
        double deg = Math.toDegrees(rad);
        System.out.println(rad + " 라디안 = " + deg + "도");
        
        
        // 역삼각함수
        double asin = Math.asin(0.5);      // arcsin
        double acos = Math.acos(0.5);      // arccos
        double atan = Math.atan(1.0);      // arctan
        
        System.out.println("asin(0.5) = " + Math.toDegrees(asin) + "도");  // 30
        System.out.println("acos(0.5) = " + Math.toDegrees(acos) + "도");  // 60
        System.out.println("atan(1.0) = " + Math.toDegrees(atan) + "도");  // 45
        
        
        // atan2: 좌표로 각도 계산
        double x = 1.0, y = 1.0;
        double angle2 = Math.atan2(y, x);
        System.out.println("atan2(1, 1) = " + Math.toDegrees(angle2) + "도");  // 45
    }
}
```

### 6.2 쌍곡선 함수

```java
public class HyperbolicFunctions {
    public static void main(String[] args) {
        double x = 1.0;
        
        // sinh, cosh, tanh
        double sinh = Math.sinh(x);
        double cosh = Math.cosh(x);
        double tanh = Math.tanh(x);
        
        System.out.println("sinh(1) = " + sinh);  // 1.175...
        System.out.println("cosh(1) = " + cosh);  // 1.543...
        System.out.println("tanh(1) = " + tanh);  // 0.761...
        
        
        // 항등식 확인: cosh²(x) - sinh²(x) = 1
        double identity = cosh*cosh - sinh*sinh;
        System.out.println("cosh²-sinh² = " + identity);  // 1.0
    }
}
```

### 6.3 로그 함수

```java
public class LogarithmFunctions {
    public static void main(String[] args) {
        // 자연로그 (밑이 e)
        double ln = Math.log(Math.E);
        System.out.println("ln(e) = " + ln);  // 1.0
        
        double ln10 = Math.log(10);
        System.out.println("ln(10) = " + ln10);  // 2.302...
        
        
        // 상용로그 (밑이 10)
        double log10 = Math.log10(100);
        System.out.println("log₁₀(100) = " + log10);  // 2.0
        
        double log1000 = Math.log10(1000);
        System.out.println("log₁₀(1000) = " + log1000);  // 3.0
        
        
        // 이진로그 (밑이 2) - Java 9+
        // double log2 = Math.log(8) / Math.log(2);
        // System.out.println("log₂(8) = " + log2);  // 3.0
        
        
        // log(1 + x) - 작은 x에 대해 정확
        double log1p = Math.log1p(0.0001);
        System.out.println("log(1 + 0.0001) = " + log1p);
        
        
        // e^x
        double exp = Math.exp(1);
        System.out.println("e¹ = " + exp);  // 2.718...
        
        double exp2 = Math.exp(2);
        System.out.println("e² = " + exp2);  // 7.389...
        
        
        // e^x - 1 - 작은 x에 대해 정확
        double expm1 = Math.expm1(0.0001);
        System.out.println("e^0.0001 - 1 = " + expm1);
    }
}
```

---

## 7. 난수 생성

### 7.1 random - 기본 난수

```java
public class RandomBasic {
    public static void main(String[] args) {
        // Math.random(): 0.0 이상 1.0 미만의 double
        
        double random1 = Math.random();
        System.out.println("Random: " + random1);  // 0.0 <= x < 1.0
        
        
        // 0 ~ 9 정수
        int digit = (int) (Math.random() * 10);
        System.out.println("0~9: " + digit);
        
        
        // 1 ~ 10 정수
        int oneToTen = (int) (Math.random() * 10) + 1;
        System.out.println("1~10: " + oneToTen);
        
        
        // min ~ max 정수
        int min = 5, max = 15;
        int range = (int) (Math.random() * (max - min + 1)) + min;
        System.out.println(min + "~" + max + ": " + range);
        
        
        // 여러 개 생성
        System.out.print("주사위 10번: ");
        for (int i = 0; i < 10; i++) {
            int dice = (int) (Math.random() * 6) + 1;
            System.out.print(dice + " ");
        }
        System.out.println();
    }
}
```

### 7.2 난수 활용 예제

```java
public class RandomExamples {
    public static void main(String[] args) {
        // 동전 던지기
        String coin = Math.random() < 0.5 ? "앞면" : "뒷면";
        System.out.println("동전: " + coin);
        
        
        // 확률 적용
        double probability = Math.random();
        if (probability < 0.1) {
            System.out.println("10% 확률 이벤트 발생!");
        }
        
        
        // 배열에서 랜덤 선택
        String[] fruits = {"사과", "바나나", "포도", "딸기"};
        int randomIndex = (int) (Math.random() * fruits.length);
        System.out.println("랜덤 과일: " + fruits[randomIndex]);
        
        
        // 랜덤 색상
        int r = (int) (Math.random() * 256);
        int g = (int) (Math.random() * 256);
        int b = (int) (Math.random() * 256);
        System.out.println("RGB: (" + r + ", " + g + ", " + b + ")");
    }
}
```

---

## 8. 실전 연습 문제

### 문제 1: 원의 넓이와 둘레

```java
// 반지름으로 원의 넓이와 둘레 계산
public class Problem1 {
    public static double[] calculateCircle(double radius) {
        // 여기에 코드 작성
        // [넓이, 둘레] 반환
        return null;
    }
    
    public static void main(String[] args) {
        double radius = 5.0;
        double[] result = calculateCircle(radius);
        
        System.out.println("넓이: " + result[0]);
        System.out.println("둘레: " + result[1]);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static double[] calculateCircle(double radius) {
        double area = Math.PI * radius * radius;
        double circumference = 2 * Math.PI * radius;
        
        return new double[] {area, circumference};
    }
    
    // 더 자세한 버전
    public static class Circle {
        double radius;
        
        Circle(double radius) {
            this.radius = radius;
        }
        
        double getArea() {
            return Math.PI * Math.pow(radius, 2);
        }
        
        double getCircumference() {
            return 2 * Math.PI * radius;
        }
        
        double getDiameter() {
            return 2 * radius;
        }
    }
}
```
</details>

---

### 문제 2: 거리 계산

```java
// 두 점 사이의 유클리드 거리 계산
public class Problem2 {
    public static double distance(double x1, double y1, double x2, double y2) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        double dist = distance(0, 0, 3, 4);
        System.out.println("거리: " + dist);  // 5.0
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static double distance(double x1, double y1, double x2, double y2) {
        return Math.hypot(x2 - x1, y2 - y1);
    }
    
    // 다른 방법
    public static double distance2(double x1, double y1, double x2, double y2) {
        double dx = x2 - x1;
        double dy = y2 - y1;
        return Math.sqrt(dx*dx + dy*dy);
    }
    
    // 3차원 거리
    public static double distance3D(double x1, double y1, double z1,
                                    double x2, double y2, double z2) {
        double dx = x2 - x1;
        double dy = y2 - y1;
        double dz = z2 - z1;
        return Math.sqrt(dx*dx + dy*dy + dz*dz);
    }
}
```
</details>

---

### 문제 3: 로또 번호 생성

```java
// 1~45 중 중복 없는 6개 번호 생성 (정렬)
public class Problem3 {
    public static int[] generateLotto() {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] lotto = generateLotto();
        System.out.println("로또 번호: " + Arrays.toString(lotto));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int[] generateLotto() {
        Set<Integer> numbers = new HashSet<>();
        
        while (numbers.size() < 6) {
            int num = (int) (Math.random() * 45) + 1;
            numbers.add(num);
        }
        
        int[] lotto = numbers.stream()
                            .mapToInt(Integer::intValue)
                            .sorted()
                            .toArray();
        
        return lotto;
    }
    
    // 배열 사용 버전
    public static int[] generateLotto2() {
        int[] lotto = new int[6];
        
        for (int i = 0; i < 6; i++) {
            int num = (int) (Math.random() * 45) + 1;
            
            // 중복 체크
            boolean duplicate = false;
            for (int j = 0; j < i; j++) {
                if (lotto[j] == num) {
                    duplicate = true;
                    break;
                }
            }
            
            if (duplicate) {
                i--;  // 다시 생성
            } else {
                lotto[i] = num;
            }
        }
        
        Arrays.sort(lotto);
        return lotto;
    }
}
```
</details>

---

## 📌 핵심 정리

### 상수
```java
Math.PI     // 3.141592653589793
Math.E      // 2.718281828459045
```

### 최대/최소
```java
Math.max(a, b)    // 최대값
Math.min(a, b)    // 최소값
```

### 절댓값/부호
```java
Math.abs(x)       // 절댓값
Math.signum(x)    // 부호 (-1, 0, 1)
```

### 거듭제곱/제곱근
```java
Math.pow(a, b)    // a^b
Math.sqrt(x)      // √x
Math.cbrt(x)      // ∛x
Math.hypot(x, y)  // √(x²+y²)
```

### 반올림
```java
Math.round(x)     // 반올림 (long)
Math.floor(x)     // 내림 (double)
Math.ceil(x)      // 올림 (double)
Math.rint(x)      // 가장 가까운 정수 (double)
```

### 삼각함수 (라디안)
```java
Math.sin(x), Math.cos(x), Math.tan(x)
Math.asin(x), Math.acos(x), Math.atan(x)
Math.toRadians(degrees)    // 도 → 라디안
Math.toDegrees(radians)    // 라디안 → 도
```

### 로그/지수
```java
Math.log(x)       // 자연로그 ln(x)
Math.log10(x)     // 상용로그 log₁₀(x)
Math.exp(x)       // e^x
```

### 난수
```java
Math.random()     // 0.0 <= x < 1.0
(int)(Math.random() * n)         // 0 ~ n-1
(int)(Math.random() * n) + 1     // 1 ~ n
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Wrapper 클래스 →](./Math-02-Wrapper.md)**

</div>
