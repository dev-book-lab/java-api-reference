# Math 03. BigInteger & BigDecimal

> 임의 정밀도 연산 - 큰 수와 정확한 실수 계산  
> Java API Reference

---

## 📑 목차

1. [BigInteger - 큰 정수](#1-biginteger---큰-정수)
2. [BigDecimal - 정확한 실수](#2-bigdecimal---정확한-실수)
3. [실수 연산의 정확성](#3-실수-연산의-정확성)
4. [성능과 최적화](#4-성능과-최적화)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. BigInteger - 큰 정수

### 1.1 생성

```java
import java.math.BigInteger;

public class BigIntegerCreation {
    public static void main(String[] args) {
        // 문자열로 생성
        BigInteger bi1 = new BigInteger("123456789012345678901234567890");
        System.out.println("From string: " + bi1);
        
        
        // long으로 생성
        BigInteger bi2 = BigInteger.valueOf(1000000);
        System.out.println("From long: " + bi2);
        
        
        // 진법 지정
        BigInteger binary = new BigInteger("1010", 2);
        BigInteger hex = new BigInteger("FF", 16);
        
        System.out.println("Binary 1010: " + binary);  // 10
        System.out.println("Hex FF: " + hex);          // 255
        
        
        // 상수
        System.out.println("ZERO: " + BigInteger.ZERO);
        System.out.println("ONE: " + BigInteger.ONE);
        System.out.println("TWO: " + BigInteger.TWO);
        System.out.println("TEN: " + BigInteger.TEN);
        
        
        // 큰 수 예시
        BigInteger factorial100 = factorial(100);
        System.out.println("100!: " + factorial100);
    }
    
    // 팩토리얼 계산
    public static BigInteger factorial(int n) {
        BigInteger result = BigInteger.ONE;
        for (int i = 2; i <= n; i++) {
            result = result.multiply(BigInteger.valueOf(i));
        }
        return result;
    }
}
```

### 1.2 사칙연산

```java
public class BigIntegerArithmetic {
    public static void main(String[] args) {
        BigInteger a = new BigInteger("100");
        BigInteger b = new BigInteger("30");
        
        // 덧셈
        BigInteger sum = a.add(b);
        System.out.println("100 + 30 = " + sum);  // 130
        
        
        // 뺄셈
        BigInteger diff = a.subtract(b);
        System.out.println("100 - 30 = " + diff);  // 70
        
        
        // 곱셈
        BigInteger product = a.multiply(b);
        System.out.println("100 * 30 = " + product);  // 3000
        
        
        // 나눗셈
        BigInteger quotient = a.divide(b);
        System.out.println("100 / 30 = " + quotient);  // 3
        
        
        // 나머지
        BigInteger remainder = a.remainder(b);
        System.out.println("100 % 30 = " + remainder);  // 10
        
        
        // 나눗셈과 나머지 동시에
        BigInteger[] divRem = a.divideAndRemainder(b);
        System.out.println("Quotient: " + divRem[0]);   // 3
        System.out.println("Remainder: " + divRem[1]);  // 10
        
        
        // 거듭제곱
        BigInteger power = a.pow(3);
        System.out.println("100³ = " + power);  // 1000000
        
        
        // 절댓값
        BigInteger negative = new BigInteger("-100");
        BigInteger abs = negative.abs();
        System.out.println("abs(-100) = " + abs);  // 100
        
        
        // 부호 반전
        BigInteger negated = a.negate();
        System.out.println("negate(100) = " + negated);  // -100
    }
}
```

### 1.3 비교

```java
public class BigIntegerComparison {
    public static void main(String[] args) {
        BigInteger a = new BigInteger("100");
        BigInteger b = new BigInteger("200");
        BigInteger c = new BigInteger("100");
        
        // compareTo: 음수, 0, 양수
        System.out.println(a.compareTo(b));  // -1 (a < b)
        System.out.println(a.compareTo(c));  // 0 (a == c)
        System.out.println(b.compareTo(a));  // 1 (b > a)
        
        
        // equals
        System.out.println(a.equals(c));  // true
        System.out.println(a.equals(b));  // false
        
        
        // min, max
        BigInteger min = a.min(b);
        BigInteger max = a.max(b);
        
        System.out.println("min(100, 200) = " + min);  // 100
        System.out.println("max(100, 200) = " + max);  // 200
    }
}
```

### 1.4 비트 연산

```java
public class BigIntegerBitwise {
    public static void main(String[] args) {
        BigInteger a = new BigInteger("10");  // 1010
        BigInteger b = new BigInteger("6");   // 0110
        
        // AND
        BigInteger and = a.and(b);
        System.out.println("10 & 6 = " + and);  // 2 (0010)
        
        
        // OR
        BigInteger or = a.or(b);
        System.out.println("10 | 6 = " + or);   // 14 (1110)
        
        
        // XOR
        BigInteger xor = a.xor(b);
        System.out.println("10 ^ 6 = " + xor);  // 12 (1100)
        
        
        // NOT
        BigInteger not = a.not();
        System.out.println("~10 = " + not);     // -11
        
        
        // 쉬프트
        BigInteger leftShift = a.shiftLeft(2);   // 곱하기 4
        BigInteger rightShift = a.shiftRight(1); // 나누기 2
        
        System.out.println("10 << 2 = " + leftShift);   // 40
        System.out.println("10 >> 1 = " + rightShift);  // 5
        
        
        // 비트 조작
        BigInteger setBit = a.setBit(0);      // 0번 비트를 1로
        BigInteger clearBit = a.clearBit(1);  // 1번 비트를 0으로
        BigInteger flipBit = a.flipBit(0);    // 0번 비트 반전
        
        System.out.println("setBit(10, 0) = " + setBit);     // 11
        System.out.println("clearBit(10, 1) = " + clearBit); // 8
        System.out.println("flipBit(10, 0) = " + flipBit);   // 11
        
        
        // 비트 카운트
        int bitCount = a.bitCount();
        int bitLength = a.bitLength();
        
        System.out.println("bitCount(10) = " + bitCount);   // 2 (1의 개수)
        System.out.println("bitLength(10) = " + bitLength); // 4 (필요한 비트 수)
    }
}
```

### 1.5 소수와 최대공약수

```java
public class BigIntegerMath {
    public static void main(String[] args) {
        // 소수 판별
        BigInteger num = new BigInteger("97");
        boolean isPrime = num.isProbablePrime(10);  // 확률적 소수 판별
        System.out.println("97 is prime: " + isPrime);  // true
        
        
        // 다음 소수 찾기
        BigInteger nextPrime = num.nextProbablePrime();
        System.out.println("Next prime after 97: " + nextPrime);  // 101
        
        
        // 최대공약수 (GCD)
        BigInteger a = new BigInteger("48");
        BigInteger b = new BigInteger("18");
        BigInteger gcd = a.gcd(b);
        
        System.out.println("gcd(48, 18) = " + gcd);  // 6
        
        
        // 모듈러 연산
        BigInteger base = new BigInteger("5");
        BigInteger exponent = new BigInteger("10");
        BigInteger modulus = new BigInteger("13");
        
        BigInteger result = base.modPow(exponent, modulus);
        System.out.println("5^10 mod 13 = " + result);  // 4
        
        
        // 모듈러 역원
        BigInteger inverse = base.modInverse(modulus);
        System.out.println("5^-1 mod 13 = " + inverse);  // 8
        // 5 * 8 = 40 ≡ 1 (mod 13)
    }
}
```

### 1.6 변환

```java
public class BigIntegerConversion {
    public static void main(String[] args) {
        BigInteger big = new BigInteger("123456789");
        
        // 기본 타입으로 변환
        int intValue = big.intValue();         // 오버플로우 가능!
        long longValue = big.longValue();
        float floatValue = big.floatValue();
        double doubleValue = big.doubleValue();
        
        System.out.println("int: " + intValue);
        System.out.println("long: " + longValue);
        
        
        // 정확한 변환 (예외 발생 가능)
        try {
            int exact = big.intValueExact();   // 범위 초과 시 ArithmeticException
        } catch (ArithmeticException e) {
            System.out.println("int 범위 초과!");
        }
        
        
        // 문자열 변환
        String decimal = big.toString();       // 10진수
        String binary = big.toString(2);       // 2진수
        String hex = big.toString(16);         // 16진수
        
        System.out.println("Decimal: " + decimal);
        System.out.println("Binary: " + binary);
        System.out.println("Hex: " + hex);
        
        
        // 바이트 배열
        byte[] bytes = big.toByteArray();
        BigInteger fromBytes = new BigInteger(bytes);
        
        System.out.println("From bytes: " + fromBytes);
    }
}
```

---

## 2. BigDecimal - 정확한 실수

### 2.1 생성

```java
import java.math.BigDecimal;

public class BigDecimalCreation {
    public static void main(String[] args) {
        // ❌ double로 생성 (부정확할 수 있음)
        BigDecimal bd1 = new BigDecimal(0.1);
        System.out.println("From double: " + bd1);
        // 0.1000000000000000055511151231257827021181583404541015625
        
        
        // ✅ 문자열로 생성 (정확)
        BigDecimal bd2 = new BigDecimal("0.1");
        System.out.println("From string: " + bd2);  // 0.1
        
        
        // valueOf 사용 (권장)
        BigDecimal bd3 = BigDecimal.valueOf(0.1);
        System.out.println("valueOf: " + bd3);  // 0.1
        
        
        // 정수로 생성
        BigDecimal bd4 = BigDecimal.valueOf(100);
        System.out.println("From int: " + bd4);  // 100
        
        
        // 상수
        System.out.println("ZERO: " + BigDecimal.ZERO);
        System.out.println("ONE: " + BigDecimal.ONE);
        System.out.println("TEN: " + BigDecimal.TEN);
    }
}
```

### 2.2 사칙연산

```java
import java.math.RoundingMode;

public class BigDecimalArithmetic {
    public static void main(String[] args) {
        BigDecimal a = new BigDecimal("10.5");
        BigDecimal b = new BigDecimal("3.2");
        
        // 덧셈
        BigDecimal sum = a.add(b);
        System.out.println("10.5 + 3.2 = " + sum);  // 13.7
        
        
        // 뺄셈
        BigDecimal diff = a.subtract(b);
        System.out.println("10.5 - 3.2 = " + diff);  // 7.3
        
        
        // 곱셈
        BigDecimal product = a.multiply(b);
        System.out.println("10.5 * 3.2 = " + product);  // 33.60
        
        
        // 나눗셈 (반올림 모드 필요!)
        BigDecimal quotient = a.divide(b, 2, RoundingMode.HALF_UP);
        System.out.println("10.5 / 3.2 = " + quotient);  // 3.28
        
        
        // 나눗셈 (무한소수 가능성 있으면 예외)
        try {
            BigDecimal bd1 = new BigDecimal("1");
            BigDecimal bd2 = new BigDecimal("3");
            BigDecimal result = bd1.divide(bd2);  // ArithmeticException!
        } catch (ArithmeticException e) {
            System.out.println("무한소수: 반올림 모드 필요!");
        }
        
        
        // 거듭제곱
        BigDecimal power = a.pow(2);
        System.out.println("10.5² = " + power);  // 110.25
        
        
        // 절댓값
        BigDecimal negative = new BigDecimal("-10.5");
        BigDecimal abs = negative.abs();
        System.out.println("abs(-10.5) = " + abs);  // 10.5
    }
}
```

### 2.3 반올림 모드

```java
public class RoundingModes {
    public static void main(String[] args) {
        BigDecimal value = new BigDecimal("10.5");
        BigDecimal divisor = new BigDecimal("3");
        
        System.out.println("10.5 / 3 =");
        
        // HALF_UP: 반올림 (5 이상 올림)
        System.out.println("HALF_UP: " + 
            value.divide(divisor, 2, RoundingMode.HALF_UP));  // 3.50
        
        // HALF_DOWN: 반내림 (5 이하 내림)
        System.out.println("HALF_DOWN: " + 
            value.divide(divisor, 2, RoundingMode.HALF_DOWN));  // 3.50
        
        // HALF_EVEN: 짝수 쪽으로 (은행가 반올림)
        System.out.println("HALF_EVEN: " + 
            value.divide(divisor, 2, RoundingMode.HALF_EVEN));  // 3.50
        
        // UP: 무조건 올림
        System.out.println("UP: " + 
            value.divide(divisor, 2, RoundingMode.UP));  // 3.50
        
        // DOWN: 무조건 내림
        System.out.println("DOWN: " + 
            value.divide(divisor, 2, RoundingMode.DOWN));  // 3.50
        
        // CEILING: 양의 무한대 방향
        System.out.println("CEILING: " + 
            value.divide(divisor, 2, RoundingMode.CEILING));  // 3.50
        
        // FLOOR: 음의 무한대 방향
        System.out.println("FLOOR: " + 
            value.divide(divisor, 2, RoundingMode.FLOOR));  // 3.50
        
        
        // 실제 차이가 나는 예시
        BigDecimal val = new BigDecimal("2.5");
        BigDecimal div = BigDecimal.ONE;
        
        System.out.println("\n2.5 / 1 =");
        System.out.println("HALF_UP: " + 
            val.divide(div, 0, RoundingMode.HALF_UP));    // 3
        System.out.println("HALF_DOWN: " + 
            val.divide(div, 0, RoundingMode.HALF_DOWN));  // 2
        System.out.println("HALF_EVEN: " + 
            val.divide(div, 0, RoundingMode.HALF_EVEN));  // 2 (짝수)
    }
}
```

### 2.4 비교와 정밀도

```java
public class BigDecimalComparison {
    public static void main(String[] args) {
        BigDecimal a = new BigDecimal("10.50");
        BigDecimal b = new BigDecimal("10.5");
        
        // compareTo (값 비교)
        System.out.println(a.compareTo(b));  // 0 (값이 같음)
        
        
        // equals (값과 스케일 모두 비교)
        System.out.println(a.equals(b));  // false! (스케일이 다름)
        
        System.out.println("a scale: " + a.scale());  // 2
        System.out.println("b scale: " + b.scale());  // 1
        
        
        // 값 비교는 compareTo 사용!
        if (a.compareTo(b) == 0) {
            System.out.println("값이 같음");
        }
        
        
        // 정밀도
        System.out.println("a precision: " + a.precision());  // 4 (유효숫자)
        System.out.println("b precision: " + b.precision());  // 3
        
        
        // stripTrailingZeros: 후행 0 제거
        BigDecimal stripped = a.stripTrailingZeros();
        System.out.println("Stripped: " + stripped);  // 10.5
        System.out.println("Scale: " + stripped.scale());  // 1
    }
}
```

### 2.5 스케일 조정

```java
public class BigDecimalScale {
    public static void main(String[] args) {
        BigDecimal value = new BigDecimal("10.123");
        
        // setScale: 소수점 자리 조정
        BigDecimal scaled1 = value.setScale(2, RoundingMode.HALF_UP);
        System.out.println("Scale to 2: " + scaled1);  // 10.12
        
        BigDecimal scaled2 = value.setScale(5, RoundingMode.HALF_UP);
        System.out.println("Scale to 5: " + scaled2);  // 10.12300
        
        
        // movePointRight/Left: 소수점 이동
        BigDecimal moved1 = value.movePointRight(2);  // 곱하기 100
        System.out.println("Move right 2: " + moved1);  // 1012.3
        
        BigDecimal moved2 = value.movePointLeft(1);   // 나누기 10
        System.out.println("Move left 1: " + moved2);   // 1.0123
        
        
        // scaleByPowerOfTen: 10의 거듭제곱 곱하기
        BigDecimal scaled = value.scaleByPowerOfTen(2);
        System.out.println("Scale by 10²: " + scaled);  // 1012.3
    }
}
```

---

## 3. 실수 연산의 정확성

### 3.1 부동소수점 문제

```java
public class FloatingPointIssue {
    public static void main(String[] args) {
        // double의 문제
        double d1 = 0.1;
        double d2 = 0.2;
        double sum = d1 + d2;
        
        System.out.println("0.1 + 0.2 = " + sum);  // 0.30000000000000004
        System.out.println("Is 0.3? " + (sum == 0.3));  // false!
        
        
        // BigDecimal로 해결
        BigDecimal bd1 = new BigDecimal("0.1");
        BigDecimal bd2 = new BigDecimal("0.2");
        BigDecimal bdSum = bd1.add(bd2);
        
        System.out.println("\nBigDecimal:");
        System.out.println("0.1 + 0.2 = " + bdSum);  // 0.3
        System.out.println("Is 0.3? " + (bdSum.compareTo(new BigDecimal("0.3")) == 0));  // true
        
        
        // 금액 계산 예시
        System.out.println("\n=== 금액 계산 ===");
        
        // ❌ double 사용
        double price = 0.1;
        double quantity = 3;
        double total = price * quantity;
        System.out.println("double: " + total);  // 0.30000000000000004
        
        
        // ✅ BigDecimal 사용
        BigDecimal bdPrice = new BigDecimal("0.1");
        BigDecimal bdQuantity = new BigDecimal("3");
        BigDecimal bdTotal = bdPrice.multiply(bdQuantity);
        System.out.println("BigDecimal: " + bdTotal);  // 0.3
    }
}
```

### 3.2 금융 계산 예제

```java
import java.math.MathContext;

public class FinancialCalculation {
    public static void main(String[] args) {
        // 상품 가격 계산
        BigDecimal price = new BigDecimal("1234.56");
        BigDecimal taxRate = new BigDecimal("0.1");  // 10% 세금
        
        // 세금
        BigDecimal tax = price.multiply(taxRate)
                             .setScale(2, RoundingMode.HALF_UP);
        System.out.println("Tax: " + tax);  // 123.46
        
        // 총액
        BigDecimal total = price.add(tax);
        System.out.println("Total: " + total);  // 1358.02
        
        
        // 할인 적용
        BigDecimal discount = new BigDecimal("0.2");  // 20% 할인
        BigDecimal discountAmount = total.multiply(discount)
                                        .setScale(2, RoundingMode.HALF_UP);
        BigDecimal finalPrice = total.subtract(discountAmount);
        
        System.out.println("Discount: " + discountAmount);  // 271.60
        System.out.println("Final: " + finalPrice);         // 1086.42
        
        
        // 월 이자 계산 (연 5%, 12개월)
        BigDecimal principal = new BigDecimal("10000");
        BigDecimal annualRate = new BigDecimal("0.05");
        BigDecimal months = new BigDecimal("12");
        
        BigDecimal monthlyRate = annualRate.divide(months, 10, RoundingMode.HALF_UP);
        BigDecimal interest = principal.multiply(monthlyRate)
                                      .setScale(2, RoundingMode.HALF_UP);
        
        System.out.println("\nMonthly interest: " + interest);  // 41.67
    }
}
```

---

## 4. 성능과 최적화

### 4.1 성능 비교

```java
public class PerformanceComparison {
    public static void main(String[] args) {
        int iterations = 1000000;
        
        // int 연산
        long start = System.nanoTime();
        int sum1 = 0;
        for (int i = 0; i < iterations; i++) {
            sum1 += i;
        }
        long end = System.nanoTime();
        System.out.println("int time: " + (end - start) / 1000000.0 + "ms");
        
        
        // BigInteger 연산
        start = System.nanoTime();
        BigInteger sum2 = BigInteger.ZERO;
        for (int i = 0; i < iterations; i++) {
            sum2 = sum2.add(BigInteger.valueOf(i));
        }
        end = System.nanoTime();
        System.out.println("BigInteger time: " + (end - start) / 1000000.0 + "ms");
        
        
        // BigDecimal 연산
        start = System.nanoTime();
        BigDecimal sum3 = BigDecimal.ZERO;
        for (int i = 0; i < iterations; i++) {
            sum3 = sum3.add(BigDecimal.valueOf(i));
        }
        end = System.nanoTime();
        System.out.println("BigDecimal time: " + (end - start) / 1000000.0 + "ms");
        
        
        System.out.println("\n결론: 기본 타입이 훨씬 빠름!");
        System.out.println("BigInteger/BigDecimal은 정확성이 필요할 때만 사용");
    }
}
```

### 4.2 최적화 팁

```java
public class OptimizationTips {
    public static void main(String[] args) {
        // 1. valueOf 재사용
        BigInteger bi1 = BigInteger.valueOf(100);  // 캐싱됨
        BigInteger bi2 = BigInteger.valueOf(100);  // 같은 객체
        
        System.out.println(bi1 == bi2);  // true (작은 값은 캐싱)
        
        
        // 2. 상수 재사용
        BigInteger result = BigInteger.ZERO;
        for (int i = 0; i < 10; i++) {
            result = result.add(BigInteger.ONE);  // ONE 재사용
        }
        
        
        // 3. 불필요한 변환 피하기
        // ❌ 비효율적
        BigDecimal bad = new BigDecimal(Double.toString(0.1));
        
        // ✅ 효율적
        BigDecimal good = new BigDecimal("0.1");
        
        
        // 4. MathContext 활용
        MathContext mc = new MathContext(10);  // 유효숫자 10자리
        
        BigDecimal bd1 = new BigDecimal("1");
        BigDecimal bd2 = new BigDecimal("3");
        BigDecimal result2 = bd1.divide(bd2, mc);
        
        System.out.println("1/3: " + result2);  // 0.3333333333
        
        
        // 5. 적절한 타입 선택
        // 정수 연산 → BigInteger
        // 실수 연산 (정확성 필요) → BigDecimal
        // 일반 연산 → 기본 타입 (int, long, double)
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 피보나치 수열 (큰 수)

```java
// n번째 피보나치 수 계산 (BigInteger)
public class Problem1 {
    public static BigInteger fibonacci(int n) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        System.out.println("Fib(100): " + fibonacci(100));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static BigInteger fibonacci(int n) {
        if (n <= 1) return BigInteger.valueOf(n);
        
        BigInteger a = BigInteger.ZERO;
        BigInteger b = BigInteger.ONE;
        
        for (int i = 2; i <= n; i++) {
            BigInteger temp = a.add(b);
            a = b;
            b = temp;
        }
        
        return b;
    }
    
    // 재귀 + 메모이제이션
    static Map<Integer, BigInteger> memo = new HashMap<>();
    
    public static BigInteger fibonacci2(int n) {
        if (n <= 1) return BigInteger.valueOf(n);
        
        if (memo.containsKey(n)) {
            return memo.get(n);
        }
        
        BigInteger result = fibonacci2(n - 1).add(fibonacci2(n - 2));
        memo.put(n, result);
        
        return result;
    }
}
```
</details>

---

### 문제 2: 복리 계산

```java
// 원금, 연이율, 기간으로 복리 계산
// 최종 금액 = 원금 * (1 + 이율)^기간
public class Problem2 {
    public static BigDecimal compoundInterest(
        BigDecimal principal,
        BigDecimal rate,
        int years) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        BigDecimal principal = new BigDecimal("1000000");  // 100만원
        BigDecimal rate = new BigDecimal("0.05");          // 연 5%
        int years = 10;
        
        BigDecimal result = compoundInterest(principal, rate, years);
        System.out.println("10년 후: " + result);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static BigDecimal compoundInterest(
        BigDecimal principal,
        BigDecimal rate,
        int years) {
        
        // (1 + rate)
        BigDecimal multiplier = BigDecimal.ONE.add(rate);
        
        // (1 + rate)^years
        BigDecimal result = principal;
        for (int i = 0; i < years; i++) {
            result = result.multiply(multiplier);
        }
        
        return result.setScale(2, RoundingMode.HALF_UP);
    }
    
    // pow 사용 버전 (정수 지수만 가능)
    public static BigDecimal compoundInterest2(
        BigDecimal principal,
        BigDecimal rate,
        int years) {
        
        BigDecimal multiplier = BigDecimal.ONE.add(rate);
        
        // multiplier^years
        BigDecimal power = multiplier.pow(years);
        
        return principal.multiply(power)
                       .setScale(2, RoundingMode.HALF_UP);
    }
}
```
</details>

---

### 문제 3: 정확한 평균 계산

```java
// 여러 실수의 정확한 평균 계산
public class Problem3 {
    public static BigDecimal average(String... numbers) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        BigDecimal avg = average("0.1", "0.2", "0.3");
        System.out.println("Average: " + avg);  // 0.2
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static BigDecimal average(String... numbers) {
        if (numbers.length == 0) {
            return BigDecimal.ZERO;
        }
        
        BigDecimal sum = BigDecimal.ZERO;
        for (String num : numbers) {
            sum = sum.add(new BigDecimal(num));
        }
        
        BigDecimal count = BigDecimal.valueOf(numbers.length);
        return sum.divide(count, 10, RoundingMode.HALF_UP);
    }
    
    // Stream 사용 버전
    public static BigDecimal average2(String... numbers) {
        if (numbers.length == 0) {
            return BigDecimal.ZERO;
        }
        
        BigDecimal sum = Arrays.stream(numbers)
                              .map(BigDecimal::new)
                              .reduce(BigDecimal.ZERO, BigDecimal::add);
        
        BigDecimal count = BigDecimal.valueOf(numbers.length);
        return sum.divide(count, 10, RoundingMode.HALF_UP);
    }
}
```
</details>

---

## 📌 핵심 정리

### BigInteger
```java
// 생성
BigInteger bi = new BigInteger("123456789012345678901234567890");
BigInteger bi2 = BigInteger.valueOf(1000000);

// 연산
bi.add(bi2)         // 덧셈
bi.subtract(bi2)    // 뺄셈
bi.multiply(bi2)    // 곱셈
bi.divide(bi2)      // 나눗셈
bi.pow(3)           // 거듭제곱

// 비교
bi.compareTo(bi2)   // 음수, 0, 양수
bi.equals(bi2)      // 같은지

// 유틸리티
bi.gcd(bi2)         // 최대공약수
bi.isProbablePrime(10)  // 소수 판별
```

### BigDecimal
```java
// 생성 (문자열 권장!)
BigDecimal bd = new BigDecimal("0.1");  // 정확
BigDecimal bd2 = BigDecimal.valueOf(0.1);

// 연산 (반올림 모드 주의!)
bd.add(bd2)
bd.subtract(bd2)
bd.multiply(bd2)
bd.divide(bd2, 2, RoundingMode.HALF_UP)  // 소수점 2자리, 반올림

// 비교 (compareTo 사용!)
bd.compareTo(bd2) == 0  // 값 비교
bd.equals(bd2)          // 값과 스케일 모두 비교

// 스케일
bd.setScale(2, RoundingMode.HALF_UP)  // 소수점 조정
bd.stripTrailingZeros()               // 후행 0 제거
```

### 반올림 모드
```java
RoundingMode.HALF_UP     // 반올림 (일반적)
RoundingMode.HALF_DOWN   // 반내림
RoundingMode.HALF_EVEN   // 은행가 반올림
RoundingMode.UP          // 올림
RoundingMode.DOWN        // 내림
RoundingMode.CEILING     // 양의 무한대 방향
RoundingMode.FLOOR       // 음의 무한대 방향
```

### 사용 가이드
```java
// BigInteger: 매우 큰 정수, 정확한 정수 연산
factorial(100), fibonacci(1000), RSA 암호화

// BigDecimal: 정확한 실수 연산
금융 계산, 회계, 과학 계산

// 기본 타입: 일반적인 경우 (성능 중요)
일반 계산, 반복문, 알고리즘
```

---

## 🎉 Math & Number 시리즈 완료!

<div align="center">

### 📚 전체 시리즈

| Chapter | 주제 |
|:-------:|------|
| [01. Math 클래스](./Math-01-Math클래스.md) | 수학 연산, 삼각함수, 난수 |
| [02. Wrapper 클래스](./Math-02-Wrapper.md) | Integer, Double, Boxing |
| [03. BigInteger & BigDecimal](./Math-03-Big.md) ⭐ | 큰 수, 정확한 실수 |

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. Wrapper 클래스](./Math-02-Wrapper.md)**

</div>
