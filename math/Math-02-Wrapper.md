# Math 02. Wrapper 클래스

> 기본 타입의 객체 래퍼 - Integer, Double, Long 완전 정복  
> Java API Reference

---

## 📑 목차

1. [Wrapper 클래스 개요](#1-wrapper-클래스-개요)
2. [Integer 클래스](#2-integer-클래스)
3. [Double / Float 클래스](#3-double--float-클래스)
4. [Long / Short / Byte 클래스](#4-long--short--byte-클래스)
5. [Boolean / Character 클래스](#5-boolean--character-클래스)
6. [Boxing과 Unboxing](#6-boxing과-unboxing)
7. [실전 연습 문제](#7-실전-연습-문제)

---

## 1. Wrapper 클래스 개요

### 1.1 기본 타입 vs Wrapper 클래스

```java
public class WrapperIntro {
    public static void main(String[] args) {
        // 기본 타입 (Primitive Type)
        int primitiveInt = 10;
        double primitiveDouble = 3.14;
        boolean primitiveBoolean = true;
        
        // Wrapper 클래스 (Reference Type)
        Integer wrapperInt = Integer.valueOf(10);
        Double wrapperDouble = Double.valueOf(3.14);
        Boolean wrapperBoolean = Boolean.valueOf(true);
        
        System.out.println("Primitive int: " + primitiveInt);
        System.out.println("Wrapper Integer: " + wrapperInt);
        
        
        // 기본 타입과 Wrapper 매핑
        System.out.println("\n=== 매핑 관계 ===");
        System.out.println("byte    → Byte");
        System.out.println("short   → Short");
        System.out.println("int     → Integer");
        System.out.println("long    → Long");
        System.out.println("float   → Float");
        System.out.println("double  → Double");
        System.out.println("char    → Character");
        System.out.println("boolean → Boolean");
    }
}
```

### 1.2 왜 Wrapper 클래스가 필요한가?

```java
import java.util.*;

public class WhyWrapper {
    public static void main(String[] args) {
        // 1. 컬렉션에는 객체만 저장 가능
        // List<int> list = new ArrayList<>();  // 컴파일 에러!
        List<Integer> list = new ArrayList<>();  // OK
        list.add(10);
        list.add(20);
        
        
        // 2. null 값 표현 가능
        int primitive = 0;  // 0인지 초기화 안 된 건지 구분 불가
        Integer wrapper = null;  // null로 "값 없음" 표현 가능
        
        
        // 3. 유틸리티 메서드 제공
        String str = "123";
        int parsed = Integer.parseInt(str);  // 문자열 → int
        
        
        // 4. 제네릭에 사용
        // public class Box<T> { ... }
        // Box<int> box = new Box<>();  // 에러!
        Box<Integer> box = new Box<>();  // OK
        
        
        // 5. 상수 제공
        System.out.println("Integer.MAX_VALUE: " + Integer.MAX_VALUE);
        System.out.println("Integer.MIN_VALUE: " + Integer.MIN_VALUE);
        System.out.println("Double.MAX_VALUE: " + Double.MAX_VALUE);
    }
    
    static class Box<T> {
        T value;
    }
}
```

---

## 2. Integer 클래스

### 2.1 생성과 변환

```java
public class IntegerCreation {
    public static void main(String[] args) {
        // 생성 방법
        Integer i1 = Integer.valueOf(10);        // 권장 (캐싱)
        Integer i2 = Integer.valueOf("123");     // 문자열에서
        // Integer i3 = new Integer(10);         // Deprecated (Java 9+)
        
        
        // 기본 타입으로 변환
        int primitive = i1.intValue();
        long longValue = i1.longValue();
        double doubleValue = i1.doubleValue();
        
        System.out.println("int: " + primitive);
        System.out.println("long: " + longValue);
        System.out.println("double: " + doubleValue);
        
        
        // 문자열 변환
        String str1 = i1.toString();
        String str2 = Integer.toString(123);
        
        System.out.println("String: " + str1);
    }
}
```

### 2.2 파싱

```java
public class IntegerParsing {
    public static void main(String[] args) {
        // 문자열 → int
        int num1 = Integer.parseInt("123");
        System.out.println("parseInt: " + num1);  // 123
        
        
        // 문자열 → Integer
        Integer num2 = Integer.valueOf("456");
        System.out.println("valueOf: " + num2);   // 456
        
        
        // 진법 지정
        int binary = Integer.parseInt("1010", 2);      // 2진수
        int octal = Integer.parseInt("17", 8);         // 8진수
        int hex = Integer.parseInt("FF", 16);          // 16진수
        
        System.out.println("Binary 1010: " + binary);  // 10
        System.out.println("Octal 17: " + octal);      // 15
        System.out.println("Hex FF: " + hex);          // 255
        
        
        // 예외 처리
        try {
            int invalid = Integer.parseInt("abc");
        } catch (NumberFormatException e) {
            System.out.println("파싱 실패: " + e.getMessage());
        }
    }
}
```

### 2.3 상수와 비교

```java
public class IntegerConstants {
    public static void main(String[] args) {
        // 상수
        System.out.println("MAX_VALUE: " + Integer.MAX_VALUE);  // 2147483647
        System.out.println("MIN_VALUE: " + Integer.MIN_VALUE);  // -2147483648
        System.out.println("SIZE: " + Integer.SIZE);            // 32 (bits)
        System.out.println("BYTES: " + Integer.BYTES);          // 4
        
        
        // 비교
        Integer a = 100;
        Integer b = 100;
        Integer c = 200;
        
        // compareTo: 음수, 0, 양수
        System.out.println(a.compareTo(b));  // 0 (같음)
        System.out.println(a.compareTo(c));  // -1 (a < c)
        System.out.println(c.compareTo(a));  // 1 (c > a)
        
        
        // compare (static)
        int result = Integer.compare(10, 20);
        System.out.println("compare(10, 20): " + result);  // -1
    }
}
```

### 2.4 비트 연산

```java
public class IntegerBitOperations {
    public static void main(String[] args) {
        int num = 10;  // 0000 1010
        
        // 2진수 문자열
        String binary = Integer.toBinaryString(num);
        System.out.println("Binary: " + binary);  // 1010
        
        
        // 8진수, 16진수
        String octal = Integer.toOctalString(num);
        String hex = Integer.toHexString(num);
        
        System.out.println("Octal: " + octal);    // 12
        System.out.println("Hex: " + hex);        // a
        
        
        // 비트 수 세기
        int bitCount = Integer.bitCount(num);
        System.out.println("Bit count: " + bitCount);  // 2 (1의 개수)
        
        
        // 최상위 비트 위치
        int highestOneBit = Integer.highestOneBit(num);
        System.out.println("Highest one bit: " + highestOneBit);  // 8 (1000)
        
        
        // 최하위 비트 위치
        int lowestOneBit = Integer.lowestOneBit(num);
        System.out.println("Lowest one bit: " + lowestOneBit);    // 2 (0010)
        
        
        // 선행 0 개수
        int leadingZeros = Integer.numberOfLeadingZeros(num);
        System.out.println("Leading zeros: " + leadingZeros);  // 28
        
        
        // 후행 0 개수
        int trailingZeros = Integer.numberOfTrailingZeros(num);
        System.out.println("Trailing zeros: " + trailingZeros);  // 1
        
        
        // 비트 반전
        int reversed = Integer.reverse(num);
        System.out.println("Reversed: " + Integer.toBinaryString(reversed));
    }
}
```

### 2.5 캐싱 (-128 ~ 127)

```java
public class IntegerCaching {
    public static void main(String[] args) {
        // -128 ~ 127 범위는 캐싱됨
        Integer a1 = Integer.valueOf(100);
        Integer a2 = Integer.valueOf(100);
        
        System.out.println(a1 == a2);  // true (같은 객체)
        
        
        // 범위 밖은 새 객체
        Integer b1 = Integer.valueOf(200);
        Integer b2 = Integer.valueOf(200);
        
        System.out.println(b1 == b2);  // false (다른 객체)
        
        
        // equals 사용 권장
        System.out.println(b1.equals(b2));  // true (값 비교)
        
        
        // new는 항상 새 객체 (Deprecated)
        // Integer c1 = new Integer(100);
        // Integer c2 = new Integer(100);
        // System.out.println(c1 == c2);  // false
    }
}
```

---

## 3. Double / Float 클래스

### 3.1 생성과 변환

```java
public class DoubleBasic {
    public static void main(String[] args) {
        // 생성
        Double d1 = Double.valueOf(3.14);
        Double d2 = Double.valueOf("2.71");
        
        
        // 기본 타입으로 변환
        double primitive = d1.doubleValue();
        float floatValue = d1.floatValue();
        int intValue = d1.intValue();  // 소수점 버림
        
        System.out.println("double: " + primitive);
        System.out.println("float: " + floatValue);
        System.out.println("int: " + intValue);    // 3
        
        
        // 문자열 변환
        String str = d1.toString();
        System.out.println("String: " + str);
    }
}
```

### 3.2 파싱과 상수

```java
public class DoubleParsing {
    public static void main(String[] args) {
        // 파싱
        double d1 = Double.parseDouble("3.14");
        Double d2 = Double.valueOf("2.71");
        
        System.out.println("parseDouble: " + d1);
        System.out.println("valueOf: " + d2);
        
        
        // 상수
        System.out.println("MAX_VALUE: " + Double.MAX_VALUE);
        // 1.7976931348623157E308
        
        System.out.println("MIN_VALUE: " + Double.MIN_VALUE);
        // 4.9E-324 (양수 최소값!)
        
        System.out.println("MIN_NORMAL: " + Double.MIN_NORMAL);
        // 2.2250738585072014E-308
        
        System.out.println("SIZE: " + Double.SIZE);      // 64 bits
        System.out.println("BYTES: " + Double.BYTES);    // 8
        
        
        // 특수 값
        System.out.println("POSITIVE_INFINITY: " + Double.POSITIVE_INFINITY);
        System.out.println("NEGATIVE_INFINITY: " + Double.NEGATIVE_INFINITY);
        System.out.println("NaN: " + Double.NaN);
    }
}
```

### 3.3 특수 값 처리

```java
public class DoubleSpecialValues {
    public static void main(String[] args) {
        // NaN (Not a Number)
        double nan = Double.NaN;
        double result1 = 0.0 / 0.0;
        double result2 = Math.sqrt(-1);
        
        System.out.println("NaN: " + nan);
        System.out.println("0/0: " + result1);
        System.out.println("√-1: " + result2);
        
        
        // NaN 확인
        System.out.println("isNaN: " + Double.isNaN(nan));          // true
        System.out.println("isNaN: " + Double.isNaN(3.14));         // false
        
        // ❌ NaN == NaN은 false!
        System.out.println("NaN == NaN: " + (Double.NaN == Double.NaN));  // false
        
        
        // Infinity
        double posInf = Double.POSITIVE_INFINITY;
        double negInf = Double.NEGATIVE_INFINITY;
        double result3 = 1.0 / 0.0;
        double result4 = -1.0 / 0.0;
        
        System.out.println("\nPositive Infinity: " + posInf);
        System.out.println("1/0: " + result3);
        System.out.println("-1/0: " + result4);
        
        
        // Infinity 확인
        System.out.println("isInfinite: " + Double.isInfinite(posInf));  // true
        System.out.println("isInfinite: " + Double.isInfinite(3.14));    // false
        
        
        // 유한 값 확인
        System.out.println("isFinite(3.14): " + Double.isFinite(3.14));  // true
        System.out.println("isFinite(NaN): " + Double.isFinite(Double.NaN));  // false
        System.out.println("isFinite(∞): " + Double.isFinite(posInf));   // false
    }
}
```

### 3.4 비교

```java
public class DoubleComparison {
    public static void main(String[] args) {
        Double d1 = 3.14;
        Double d2 = 2.71;
        
        // compareTo
        System.out.println(d1.compareTo(d2));  // 1 (d1 > d2)
        
        
        // compare
        int result = Double.compare(3.14, 2.71);
        System.out.println("compare: " + result);  // 1
        
        
        // 부동소수점 비교 주의!
        double a = 0.1 + 0.2;
        double b = 0.3;
        
        System.out.println("a: " + a);  // 0.30000000000000004
        System.out.println("b: " + b);  // 0.3
        System.out.println("a == b: " + (a == b));  // false!
        
        
        // 올바른 비교
        double epsilon = 0.0001;
        boolean equal = Math.abs(a - b) < epsilon;
        System.out.println("Almost equal: " + equal);  // true
    }
}
```

---

## 4. Long / Short / Byte 클래스

### 4.1 Long 클래스

```java
public class LongClass {
    public static void main(String[] args) {
        // 생성
        Long l1 = Long.valueOf(1000000000000L);
        Long l2 = Long.valueOf("123456789");
        
        
        // 상수
        System.out.println("MAX_VALUE: " + Long.MAX_VALUE);
        // 9223372036854775807
        
        System.out.println("MIN_VALUE: " + Long.MIN_VALUE);
        // -9223372036854775808
        
        System.out.println("SIZE: " + Long.SIZE);      // 64
        System.out.println("BYTES: " + Long.BYTES);    // 8
        
        
        // 파싱
        long parsed = Long.parseLong("123456789");
        long hex = Long.parseLong("FFFF", 16);
        
        System.out.println("Parsed: " + parsed);
        System.out.println("Hex: " + hex);  // 65535
        
        
        // 변환
        int intValue = l1.intValue();  // 손실 가능!
        double doubleValue = l1.doubleValue();
        
        
        // 비트 연산 (Integer와 유사)
        String binary = Long.toBinaryString(255);
        System.out.println("Binary: " + binary);  // 11111111
        
        int bitCount = Long.bitCount(255);
        System.out.println("Bit count: " + bitCount);  // 8
    }
}
```

### 4.2 Short 클래스

```java
public class ShortClass {
    public static void main(String[] args) {
        // 생성
        Short s1 = Short.valueOf((short) 100);
        Short s2 = Short.valueOf("200");
        
        
        // 상수
        System.out.println("MAX_VALUE: " + Short.MAX_VALUE);  // 32767
        System.out.println("MIN_VALUE: " + Short.MIN_VALUE);  // -32768
        System.out.println("SIZE: " + Short.SIZE);            // 16
        System.out.println("BYTES: " + Short.BYTES);          // 2
        
        
        // 파싱
        short parsed = Short.parseShort("123");
        System.out.println("Parsed: " + parsed);
        
        
        // 변환
        int intValue = s1.intValue();
        String str = s1.toString();
    }
}
```

### 4.3 Byte 클래스

```java
public class ByteClass {
    public static void main(String[] args) {
        // 생성
        Byte b1 = Byte.valueOf((byte) 10);
        Byte b2 = Byte.valueOf("20");
        
        
        // 상수
        System.out.println("MAX_VALUE: " + Byte.MAX_VALUE);  // 127
        System.out.println("MIN_VALUE: " + Byte.MIN_VALUE);  // -128
        System.out.println("SIZE: " + Byte.SIZE);            // 8
        System.out.println("BYTES: " + Byte.BYTES);          // 1
        
        
        // 파싱
        byte parsed = Byte.parseByte("50");
        System.out.println("Parsed: " + parsed);
        
        
        // unsigned 변환 (Java 8+)
        byte signed = -1;
        int unsigned = Byte.toUnsignedInt(signed);
        System.out.println("Signed: " + signed);       // -1
        System.out.println("Unsigned: " + unsigned);   // 255
    }
}
```

---

## 5. Boolean / Character 클래스

### 5.1 Boolean 클래스

```java
public class BooleanClass {
    public static void main(String[] args) {
        // 생성
        Boolean b1 = Boolean.valueOf(true);
        Boolean b2 = Boolean.valueOf("true");
        Boolean b3 = Boolean.valueOf("false");
        Boolean b4 = Boolean.valueOf("yes");  // false! (true만 인식)
        
        System.out.println("b2: " + b2);  // true
        System.out.println("b3: " + b3);  // false
        System.out.println("b4: " + b4);  // false
        
        
        // 상수
        System.out.println("TRUE: " + Boolean.TRUE);
        System.out.println("FALSE: " + Boolean.FALSE);
        
        
        // 파싱
        boolean parsed = Boolean.parseBoolean("true");
        System.out.println("Parsed: " + parsed);
        
        
        // 비교
        int result = Boolean.compare(true, false);
        System.out.println("compare(true, false): " + result);  // 1
        
        
        // 논리 연산
        boolean and = Boolean.logicalAnd(true, false);
        boolean or = Boolean.logicalOr(true, false);
        boolean xor = Boolean.logicalXor(true, false);
        
        System.out.println("AND: " + and);  // false
        System.out.println("OR: " + or);    // true
        System.out.println("XOR: " + xor);  // true
    }
}
```

### 5.2 Character 클래스

```java
public class CharacterClass {
    public static void main(String[] args) {
        // 생성
        Character c1 = Character.valueOf('A');
        
        
        // 상수
        System.out.println("MAX_VALUE: " + (int) Character.MAX_VALUE);  // 65535
        System.out.println("MIN_VALUE: " + (int) Character.MIN_VALUE);  // 0
        System.out.println("SIZE: " + Character.SIZE);                  // 16
        System.out.println("BYTES: " + Character.BYTES);                // 2
        
        
        // 문자 타입 확인
        char ch = 'A';
        
        System.out.println("isLetter: " + Character.isLetter(ch));      // true
        System.out.println("isDigit: " + Character.isDigit(ch));        // false
        System.out.println("isUpperCase: " + Character.isUpperCase(ch)); // true
        System.out.println("isLowerCase: " + Character.isLowerCase(ch)); // false
        System.out.println("isWhitespace: " + Character.isWhitespace(' ')); // true
        
        
        // 변환
        char upper = Character.toUpperCase('a');
        char lower = Character.toLowerCase('A');
        
        System.out.println("Upper: " + upper);  // A
        System.out.println("Lower: " + lower);  // a
        
        
        // 숫자 → 문자
        char digit = Character.forDigit(15, 16);  // 16진수 F
        System.out.println("Digit: " + digit);    // f
        
        
        // 문자 → 숫자
        int value = Character.digit('F', 16);
        System.out.println("Value: " + value);    // 15
    }
}
```

---

## 6. Boxing과 Unboxing

### 6.1 자동 Boxing/Unboxing

```java
public class AutoBoxing {
    public static void main(String[] args) {
        // Auto-boxing (기본 타입 → Wrapper)
        Integer i = 10;  // Integer.valueOf(10)과 동일
        Double d = 3.14;  // Double.valueOf(3.14)와 동일
        
        
        // Auto-unboxing (Wrapper → 기본 타입)
        int primitive = i;  // i.intValue()와 동일
        double doublePrimitive = d;  // d.doubleValue()와 동일
        
        
        // 연산에서 자동 unboxing
        Integer a = 10;
        Integer b = 20;
        int sum = a + b;  // 자동 unboxing 후 연산
        
        System.out.println("Sum: " + sum);  // 30
        
        
        // 컬렉션에서 auto-boxing
        List<Integer> list = new ArrayList<>();
        list.add(10);  // auto-boxing
        
        int value = list.get(0);  // auto-unboxing
        System.out.println("Value: " + value);
    }
}
```

### 6.2 주의사항

```java
public class BoxingCautions {
    public static void main(String[] args) {
        // 1. NullPointerException
        Integer wrapper = null;
        try {
            int primitive = wrapper;  // NullPointerException!
        } catch (NullPointerException e) {
            System.out.println("NPE 발생!");
        }
        
        
        // 2. 성능 이슈
        long start = System.nanoTime();
        
        Integer sum = 0;
        for (int i = 0; i < 1000000; i++) {
            sum += i;  // 매번 boxing/unboxing!
        }
        
        long end = System.nanoTime();
        System.out.println("Boxing time: " + (end - start) / 1000000.0 + "ms");
        
        
        start = System.nanoTime();
        
        int sum2 = 0;
        for (int i = 0; i < 1000000; i++) {
            sum2 += i;  // 기본 타입만 사용
        }
        
        end = System.nanoTime();
        System.out.println("Primitive time: " + (end - start) / 1000000.0 + "ms");
        // 기본 타입이 훨씬 빠름!
        
        
        // 3. == 비교 주의
        Integer i1 = 1000;
        Integer i2 = 1000;
        
        System.out.println(i1 == i2);       // false (다른 객체)
        System.out.println(i1.equals(i2));  // true (값 비교)
        
        
        // 4. 캐싱 범위 주의
        Integer i3 = 100;
        Integer i4 = 100;
        
        System.out.println(i3 == i4);  // true (캐싱된 객체)
    }
}
```

---

## 7. 실전 연습 문제

### 문제 1: 진법 변환

```java
// 10진수를 2, 8, 16진수로 변환
public class Problem1 {
    public static void convertBase(int decimal) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        convertBase(255);
        // Binary: 11111111
        // Octal: 377
        // Hex: ff
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void convertBase(int decimal) {
        String binary = Integer.toBinaryString(decimal);
        String octal = Integer.toOctalString(decimal);
        String hex = Integer.toHexString(decimal);
        
        System.out.println("Binary: " + binary);
        System.out.println("Octal: " + octal);
        System.out.println("Hex: " + hex);
    }
    
    // 역변환
    public static int fromBinary(String binary) {
        return Integer.parseInt(binary, 2);
    }
    
    public static int fromHex(String hex) {
        return Integer.parseInt(hex, 16);
    }
}
```
</details>

---

### 문제 2: 안전한 파싱

```java
// 문자열을 Integer로 안전하게 변환 (실패 시 기본값)
public class Problem2 {
    public static Integer safeParse(String str, Integer defaultValue) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        System.out.println(safeParse("123", 0));    // 123
        System.out.println(safeParse("abc", 0));    // 0
        System.out.println(safeParse(null, -1));    // -1
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static Integer safeParse(String str, Integer defaultValue) {
        if (str == null) {
            return defaultValue;
        }
        
        try {
            return Integer.valueOf(str);
        } catch (NumberFormatException e) {
            return defaultValue;
        }
    }
    
    // Optional 사용 버전 (Java 8+)
    public static Integer safeParseOptional(String str, Integer defaultValue) {
        return Optional.ofNullable(str)
                      .flatMap(s -> {
                          try {
                              return Optional.of(Integer.valueOf(s));
                          } catch (NumberFormatException e) {
                              return Optional.empty();
                          }
                      })
                      .orElse(defaultValue);
    }
}
```
</details>

---

### 문제 3: 비트 플래그

```java
// 비트 플래그로 권한 관리
public class Problem3 {
    static final int READ = 1;      // 0001
    static final int WRITE = 2;     // 0010
    static final int EXECUTE = 4;   // 0100
    static final int DELETE = 8;    // 1000
    
    public static boolean hasPermission(int flags, int permission) {
        // 여기에 코드 작성
        return false;
    }
    
    public static void main(String[] args) {
        int userFlags = READ | WRITE;  // 읽기, 쓰기 권한
        
        System.out.println(hasPermission(userFlags, READ));     // true
        System.out.println(hasPermission(userFlags, WRITE));    // true
        System.out.println(hasPermission(userFlags, EXECUTE));  // false
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    static final int READ = 1;
    static final int WRITE = 2;
    static final int EXECUTE = 4;
    static final int DELETE = 8;
    
    public static boolean hasPermission(int flags, int permission) {
        return (flags & permission) == permission;
    }
    
    public static int addPermission(int flags, int permission) {
        return flags | permission;
    }
    
    public static int removePermission(int flags, int permission) {
        return flags & ~permission;
    }
    
    public static void printPermissions(int flags) {
        System.out.println("READ: " + hasPermission(flags, READ));
        System.out.println("WRITE: " + hasPermission(flags, WRITE));
        System.out.println("EXECUTE: " + hasPermission(flags, EXECUTE));
        System.out.println("DELETE: " + hasPermission(flags, DELETE));
    }
}
```
</details>

---

## 📌 핵심 정리

### Wrapper 클래스
```java
byte    → Byte
short   → Short
int     → Integer
long    → Long
float   → Float
double  → Double
char    → Character
boolean → Boolean
```

### 생성 (valueOf 권장)
```java
Integer i = Integer.valueOf(10);    // 권장 (캐싱)
// Integer i = new Integer(10);     // Deprecated
```

### 파싱
```java
int i = Integer.parseInt("123");
double d = Double.parseDouble("3.14");
boolean b = Boolean.parseBoolean("true");
```

### 상수
```java
Integer.MAX_VALUE / Integer.MIN_VALUE
Double.MAX_VALUE / Double.MIN_VALUE
Double.POSITIVE_INFINITY / Double.NaN
```

### Auto Boxing/Unboxing
```java
Integer i = 10;     // auto-boxing
int x = i;          // auto-unboxing
int sum = i + 20;   // auto-unboxing 후 연산
```

### 주의사항
```java
// 1. null 체크
Integer i = null;
// int x = i;  // NullPointerException!

// 2. == 비교 주의
Integer a = 1000, b = 1000;
a == b          // false (다른 객체)
a.equals(b)     // true (값 비교)

// 3. 캐싱 (-128 ~ 127)
Integer c = 100, d = 100;
c == d          // true (같은 캐싱 객체)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Math 클래스](./Math-01-Math클래스.md) | [다음: 03. BigInteger & BigDecimal →](./Math-03-Big.md)**

</div>
