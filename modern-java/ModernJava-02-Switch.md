# Modern Java 02. Switch Expression 완전 정복

> 표현식으로 진화한 Switch - 간결하고 안전한 분기 처리  
> Java 12+ (Preview), Java 14+ (Standard)

---

## 📑 목차

1. [Switch Expression 소개](#1-switch-expression-소개)
2. [기본 문법](#2-기본-문법)
3. [화살표 구문](#3-화살표-구문)
4. [yield 키워드](#4-yield-키워드)
5. [패턴 매칭](#5-패턴-매칭)
6. [Sealed Class와 함께](#6-sealed-class와-함께)
7. [실전 활용](#7-실전-활용)
8. [Best Practices](#8-best-practices)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. Switch Expression 소개

### 1.1 기존 Switch vs Switch Expression

```java
public class SwitchComparison {
    enum Day {
        MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
    }
    
    // ❌ 기존 Switch Statement (Java 13 이전)
    public static String getTypeBefore(Day day) {
        String result;
        switch (day) {
            case MONDAY:
            case TUESDAY:
            case WEDNESDAY:
            case THURSDAY:
            case FRIDAY:
                result = "평일";
                break;
            case SATURDAY:
            case SUNDAY:
                result = "주말";
                break;
            default:
                throw new IllegalArgumentException();
        }
        return result;
    }
    
    // ✅ Switch Expression (Java 14+)
    public static String getTypeAfter(Day day) {
        return switch (day) {
            case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> "평일";
            case SATURDAY, SUNDAY -> "주말";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Switch Expression 비교 ===\n");
        
        System.out.println("기존 Switch 문제점:");
        System.out.println("❌ break 누락 위험");
        System.out.println("❌ 값 반환 복잡");
        System.out.println("❌ 변수 초기화 필요");
        System.out.println("❌ fall-through 버그\n");
        
        System.out.println("Switch Expression 장점:");
        System.out.println("✅ 표현식 (값 반환)");
        System.out.println("✅ break 불필요");
        System.out.println("✅ 간결한 문법");
        System.out.println("✅ 완전성 체크 (exhaustive)");
        
        
        Day day = Day.MONDAY;
        System.out.println("\n" + day + ": " + getTypeAfter(day));
    }
}
```

### 1.2 Expression vs Statement

```java
public class ExpressionVsStatement {
    public static void main(String[] args) {
        System.out.println("=== Expression vs Statement ===\n");
        
        int value = 2;
        
        // Statement (문장) - 값을 반환하지 않음
        switch (value) {
            case 1:
                System.out.println("One");
                break;
            case 2:
                System.out.println("Two");
                break;
        }
        
        
        // Expression (표현식) - 값을 반환
        String result = switch (value) {
            case 1 -> "One";
            case 2 -> "Two";
            case 3 -> "Three";
            default -> "Other";
        };
        
        System.out.println("\n결과: " + result);
        
        
        // 직접 사용 가능
        System.out.println(
            "값: " + switch (value) {
                case 1 -> "One";
                case 2 -> "Two";
                default -> "Other";
            }
        );
    }
}
```

---

## 2. 기본 문법

### 2.1 단일 값 반환

```java
public class BasicSyntax {
    enum Season {
        SPRING, SUMMER, FALL, WINTER
    }
    
    public static String getDescription(Season season) {
        return switch (season) {
            case SPRING -> "봄: 따뜻함";
            case SUMMER -> "여름: 더움";
            case FALL -> "가을: 시원함";
            case WINTER -> "겨울: 추움";
        };
    }
    
    public static int getDays(int month) {
        return switch (month) {
            case 1, 3, 5, 7, 8, 10, 12 -> 31;
            case 4, 6, 9, 11 -> 30;
            case 2 -> 28;
            default -> throw new IllegalArgumentException("Invalid month");
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 기본 문법 ===\n");
        
        // 계절
        System.out.println(getDescription(Season.SPRING));
        System.out.println(getDescription(Season.SUMMER));
        
        // 월별 일수
        System.out.println("\n1월: " + getDays(1) + "일");
        System.out.println("2월: " + getDays(2) + "일");
        System.out.println("4월: " + getDays(4) + "일");
    }
}
```

### 2.2 여러 케이스 그룹핑

```java
public class CaseGrouping {
    enum Grade {
        A, B, C, D, F
    }
    
    public static String getLevel(Grade grade) {
        return switch (grade) {
            case A, B -> "우수";
            case C -> "보통";
            case D, F -> "노력 필요";
        };
    }
    
    public static String getTrafficAction(String light) {
        return switch (light) {
            case "RED", "YELLOW" -> "정지";
            case "GREEN" -> "진행";
            default -> "신호 오류";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 케이스 그룹핑 ===\n");
        
        System.out.println("A: " + getLevel(Grade.A));
        System.out.println("C: " + getLevel(Grade.C));
        System.out.println("F: " + getLevel(Grade.F));
        
        
        System.out.println("\n=== 신호등 ===");
        System.out.println("RED: " + getTrafficAction("RED"));
        System.out.println("GREEN: " + getTrafficAction("GREEN"));
    }
}
```

---

## 3. 화살표 구문

### 3.1 화살표 vs 콜론

```java
public class ArrowVsColon {
    enum Priority {
        LOW, MEDIUM, HIGH, CRITICAL
    }
    
    // 화살표 구문 (권장)
    public static String handlePriorityArrow(Priority priority) {
        return switch (priority) {
            case LOW -> "나중에 처리";
            case MEDIUM -> "적절히 처리";
            case HIGH -> "빨리 처리";
            case CRITICAL -> "즉시 처리";
        };
    }
    
    // 콜론 구문 (기존 방식)
    public static String handlePriorityColon(Priority priority) {
        return switch (priority) {
            case LOW:
                yield "나중에 처리";
            case MEDIUM:
                yield "적절히 처리";
            case HIGH:
                yield "빨리 처리";
            case CRITICAL:
                yield "즉시 처리";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 화살표 vs 콜론 ===\n");
        
        System.out.println("화살표 구문:");
        System.out.println("- break 불필요");
        System.out.println("- fall-through 없음");
        System.out.println("- 간결함\n");
        
        System.out.println("콜론 구문:");
        System.out.println("- yield 필요");
        System.out.println("- 여러 문장 가능");
        System.out.println("- 기존 코드와 호환\n");
        
        
        Priority p = Priority.HIGH;
        System.out.println("화살표: " + handlePriorityArrow(p));
        System.out.println("콜론: " + handlePriorityColon(p));
    }
}
```

### 3.2 단일 표현식

```java
public class SingleExpression {
    public static int calculate(String op, int a, int b) {
        return switch (op) {
            case "+" -> a + b;
            case "-" -> a - b;
            case "*" -> a * b;
            case "/" -> b != 0 ? a / b : 0;
            default -> throw new IllegalArgumentException("Unknown op: " + op);
        };
    }
    
    public static String format(Object obj) {
        return switch (obj) {
            case Integer i -> "정수: " + i;
            case Double d -> String.format("실수: %.2f", d);
            case String s -> "문자열: " + s;
            case null -> "null";
            default -> "기타: " + obj.getClass().getName();
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 단일 표현식 ===\n");
        
        System.out.println("10 + 5 = " + calculate("+", 10, 5));
        System.out.println("10 - 5 = " + calculate("-", 10, 5));
        System.out.println("10 * 5 = " + calculate("*", 10, 5));
        
        
        System.out.println("\n=== 포맷팅 ===");
        System.out.println(format(42));
        System.out.println(format(3.14));
        System.out.println(format("Hello"));
    }
}
```

---

## 4. yield 키워드

### 4.1 yield 기본

```java
public class YieldBasic {
    public static String processValue(int value) {
        return switch (value) {
            case 1, 2, 3 -> "작음";
            case 4, 5, 6 -> {
                String prefix = "중간";
                String suffix = " 범위";
                yield prefix + suffix;
            }
            case 7, 8, 9 -> {
                System.out.println("큰 값 처리 중...");
                yield "큼";
            }
            default -> "범위 밖";
        };
    }
    
    public static String getGrade(int score) {
        return switch (score / 10) {
            case 10, 9 -> "A";
            case 8 -> "B";
            case 7 -> "C";
            case 6 -> "D";
            default -> {
                if (score < 0 || score > 100) {
                    yield "오류";
                }
                yield "F";
            }
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== yield 키워드 ===\n");
        
        System.out.println("2: " + processValue(2));
        System.out.println("5: " + processValue(5));
        System.out.println("8: " + processValue(8));
        
        
        System.out.println("\n=== 성적 ===");
        System.out.println("95점: " + getGrade(95));
        System.out.println("85점: " + getGrade(85));
        System.out.println("55점: " + getGrade(55));
    }
}
```

### 4.2 복잡한 로직

```java
public class ComplexYield {
    record User(String name, int age, String role) {}
    
    public static String authorize(User user) {
        return switch (user.role()) {
            case "ADMIN" -> {
                System.out.println("관리자 권한 확인");
                yield "모든 권한";
            }
            case "USER" -> {
                if (user.age() >= 18) {
                    yield "일반 권한";
                } else {
                    yield "제한된 권한";
                }
            }
            case "GUEST" -> {
                System.out.println("게스트 접근");
                yield "읽기 전용";
            }
            default -> {
                System.err.println("알 수 없는 역할: " + user.role());
                yield "권한 없음";
            }
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 복잡한 yield ===\n");
        
        User admin = new User("Alice", 30, "ADMIN");
        User user = new User("Bob", 25, "USER");
        User minor = new User("Charlie", 15, "USER");
        User guest = new User("David", 20, "GUEST");
        
        System.out.println(admin.name() + ": " + authorize(admin));
        System.out.println(user.name() + ": " + authorize(user));
        System.out.println(minor.name() + ": " + authorize(minor));
        System.out.println(guest.name() + ": " + authorize(guest));
    }
}
```

---

## 5. 패턴 매칭

### 5.1 Type Pattern (Java 17+)

```java
public class TypePattern {
    public static String describe(Object obj) {
        return switch (obj) {
            case Integer i -> "정수 " + i;
            case Long l -> "Long " + l;
            case Double d -> String.format("실수 %.2f", d);
            case String s -> "문자열 길이 " + s.length();
            case int[] arr -> "int 배열 크기 " + arr.length;
            case null -> "null 값";
            default -> "기타 타입";
        };
    }
    
    public static int getLength(Object obj) {
        return switch (obj) {
            case String s -> s.length();
            case int[] arr -> arr.length;
            case java.util.List<?> list -> list.size();
            default -> 0;
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Type Pattern ===\n");
        
        System.out.println(describe(42));
        System.out.println(describe(3.14));
        System.out.println(describe("Hello"));
        System.out.println(describe(new int[]{1, 2, 3}));
        System.out.println(describe(null));
        
        
        System.out.println("\n=== 길이 ===");
        System.out.println("Hello: " + getLength("Hello"));
        System.out.println("배열: " + getLength(new int[]{1, 2, 3, 4}));
    }
}
```

### 5.2 Guarded Pattern (Java 17+)

```java
public class GuardedPattern {
    public static String classify(Object obj) {
        return switch (obj) {
            case String s when s.length() < 5 -> "짧은 문자열";
            case String s when s.length() >= 5 -> "긴 문자열";
            case Integer i when i > 0 -> "양수";
            case Integer i when i < 0 -> "음수";
            case Integer i -> "0";
            case null -> "null";
            default -> "기타";
        };
    }
    
    public static String checkAge(Integer age) {
        return switch (age) {
            case null -> "나이 미입력";
            case Integer a when a < 0 -> "유효하지 않은 나이";
            case Integer a when a < 18 -> "미성년자";
            case Integer a when a < 65 -> "성인";
            case Integer a -> "노년";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Guarded Pattern ===\n");
        
        System.out.println(classify("Hi"));
        System.out.println(classify("Hello World"));
        System.out.println(classify(10));
        System.out.println(classify(-5));
        System.out.println(classify(0));
        
        
        System.out.println("\n=== 나이 체크 ===");
        System.out.println(checkAge(15));
        System.out.println(checkAge(25));
        System.out.println(checkAge(70));
        System.out.println(checkAge(null));
    }
}
```

---

## 6. Sealed Class와 함께

### 6.1 Sealed Class + Switch

```java
sealed interface Shape permits Circle, Rectangle, Triangle {}

record Circle(double radius) implements Shape {
    public double area() {
        return Math.PI * radius * radius;
    }
}

record Rectangle(double width, double height) implements Shape {
    public double area() {
        return width * height;
    }
}

record Triangle(double base, double height) implements Shape {
    public double area() {
        return base * height / 2;
    }
}

public class SealedClassSwitch {
    // default 불필요 (완전성 보장)
    public static double calculateArea(Shape shape) {
        return switch (shape) {
            case Circle c -> c.area();
            case Rectangle r -> r.area();
            case Triangle t -> t.area();
        };
    }
    
    public static String describe(Shape shape) {
        return switch (shape) {
            case Circle c -> "원 (반지름: " + c.radius() + ")";
            case Rectangle r -> "사각형 (" + r.width() + "×" + r.height() + ")";
            case Triangle t -> "삼각형 (밑변: " + t.base() + ", 높이: " + t.height() + ")";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Sealed Class + Switch ===\n");
        
        Shape circle = new Circle(5);
        Shape rect = new Rectangle(4, 6);
        Shape triangle = new Triangle(3, 4);
        
        System.out.println(describe(circle) + " 면적: " + calculateArea(circle));
        System.out.println(describe(rect) + " 면적: " + calculateArea(rect));
        System.out.println(describe(triangle) + " 면적: " + calculateArea(triangle));
        
        
        System.out.println("\n=== 장점 ===");
        System.out.println("✅ default 불필요");
        System.out.println("✅ 완전성 체크");
        System.out.println("✅ 새 타입 추가 시 컴파일 에러");
    }
}
```

### 6.2 실전 활용

```java
sealed interface Result<T> permits Success, Failure {}

record Success<T>(T value) implements Result<T> {}
record Failure<T>(String error) implements Result<T> {}

public class ResultPattern {
    public static Result<Integer> divide(int a, int b) {
        if (b == 0) {
            return new Failure<>("Division by zero");
        }
        return new Success<>(a / b);
    }
    
    public static String handleResult(Result<Integer> result) {
        return switch (result) {
            case Success<Integer> s -> "결과: " + s.value();
            case Failure<Integer> f -> "오류: " + f.error();
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== Result Pattern ===\n");
        
        Result<Integer> result1 = divide(10, 2);
        Result<Integer> result2 = divide(10, 0);
        
        System.out.println(handleResult(result1));
        System.out.println(handleResult(result2));
    }
}
```

---

## 7. 실전 활용

### 7.1 HTTP 상태 처리

```java
public class HttpStatusHandling {
    enum HttpStatus {
        OK(200),
        CREATED(201),
        BAD_REQUEST(400),
        UNAUTHORIZED(401),
        NOT_FOUND(404),
        SERVER_ERROR(500);
        
        final int code;
        HttpStatus(int code) { this.code = code; }
    }
    
    public static String handleStatus(HttpStatus status) {
        return switch (status) {
            case OK -> "요청 성공";
            case CREATED -> "리소스 생성 완료";
            case BAD_REQUEST -> {
                System.err.println("잘못된 요청 감지");
                yield "요청을 확인해주세요";
            }
            case UNAUTHORIZED -> "인증이 필요합니다";
            case NOT_FOUND -> "리소스를 찾을 수 없습니다";
            case SERVER_ERROR -> {
                System.err.println("서버 오류 발생");
                yield "잠시 후 다시 시도해주세요";
            }
        };
    }
    
    public static String getStatusCategory(int code) {
        return switch (code / 100) {
            case 2 -> "성공";
            case 3 -> "리다이렉트";
            case 4 -> "클라이언트 오류";
            case 5 -> "서버 오류";
            default -> "알 수 없음";
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== HTTP 상태 처리 ===\n");
        
        System.out.println("200: " + handleStatus(HttpStatus.OK));
        System.out.println("400: " + handleStatus(HttpStatus.BAD_REQUEST));
        System.out.println("404: " + handleStatus(HttpStatus.NOT_FOUND));
        
        System.out.println("\n=== 카테고리 ===");
        System.out.println("201: " + getStatusCategory(201));
        System.out.println("404: " + getStatusCategory(404));
    }
}
```

### 7.2 커맨드 패턴

```java
public class CommandPattern {
    sealed interface Command permits MoveUp, MoveDown, MoveLeft, MoveRight, Stop {}
    
    record MoveUp(int steps) implements Command {}
    record MoveDown(int steps) implements Command {}
    record MoveLeft(int steps) implements Command {}
    record MoveRight(int steps) implements Command {}
    record Stop() implements Command {}
    
    static class Position {
        int x, y;
        
        Position(int x, int y) {
            this.x = x;
            this.y = y;
        }
        
        void execute(Command command) {
            switch (command) {
                case MoveUp(int steps) -> y += steps;
                case MoveDown(int steps) -> y -= steps;
                case MoveLeft(int steps) -> x -= steps;
                case MoveRight(int steps) -> x += steps;
                case Stop() -> System.out.println("정지");
            }
        }
        
        @Override
        public String toString() {
            return "(" + x + ", " + y + ")";
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 커맨드 패턴 ===\n");
        
        Position pos = new Position(0, 0);
        System.out.println("초기: " + pos);
        
        pos.execute(new MoveUp(5));
        System.out.println("위로 5: " + pos);
        
        pos.execute(new MoveRight(3));
        System.out.println("오른쪽 3: " + pos);
        
        pos.execute(new MoveDown(2));
        System.out.println("아래로 2: " + pos);
        
        pos.execute(new Stop());
    }
}
```

---

## 8. Best Practices

### 8.1 완전성 (Exhaustiveness)

```java
public class Exhaustiveness {
    enum Status {
        PENDING, APPROVED, REJECTED
    }
    
    // ✅ 좋은 예 - 모든 케이스 처리
    public static String handle(Status status) {
        return switch (status) {
            case PENDING -> "대기 중";
            case APPROVED -> "승인됨";
            case REJECTED -> "거부됨";
        };
    }
    
    // ❌ 나쁜 예 - default로 회피
    public static String handleBad(Status status) {
        return switch (status) {
            case PENDING -> "대기 중";
            default -> "기타";  // APPROVED, REJECTED 명시 안 함
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== 완전성 체크 ===\n");
        
        System.out.println("✅ 모든 케이스 명시");
        System.out.println("- 새 상수 추가 시 컴파일 에러");
        System.out.println("- 버그 조기 발견\n");
        
        System.out.println("❌ default 남용");
        System.out.println("- 새 상수 추가 시 무시됨");
        System.out.println("- 버그 위험");
    }
}
```

### 8.2 null 처리

```java
public class NullHandling {
    public static String process(String value) {
        return switch (value) {
            case null -> "null 값";
            case "ADMIN" -> "관리자";
            case "USER" -> "사용자";
            default -> "게스트";
        };
    }
    
    public static int getLength(String str) {
        return switch (str) {
            case null -> 0;
            case String s -> s.length();
        };
    }
    
    public static void main(String[] args) {
        System.out.println("=== null 처리 ===\n");
        
        System.out.println(process(null));
        System.out.println(process("ADMIN"));
        System.out.println(process("unknown"));
        
        System.out.println("\n길이:");
        System.out.println("null: " + getLength(null));
        System.out.println("Hello: " + getLength("Hello"));
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 계산기

```java
// Switch Expression으로 계산기 구현
public class Problem1 {
    public static double calculate(String op, double a, double b) {
        // 구현: +, -, *, /, %
        return 0;
    }
    
    public static void main(String[] args) {
        System.out.println("10 + 5 = " + calculate("+", 10, 5));   // 15
        System.out.println("10 - 5 = " + calculate("-", 10, 5));   // 5
        System.out.println("10 * 5 = " + calculate("*", 10, 5));   // 50
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static double calculate(String op, double a, double b) {
    return switch (op) {
        case "+" -> a + b;
        case "-" -> a - b;
        case "*" -> a * b;
        case "/" -> b != 0 ? a / b : Double.NaN;
        case "%" -> a % b;
        default -> throw new IllegalArgumentException("Unknown op: " + op);
    };
}
```
</details>

---

### 문제 2: 타입 판별

```java
// Type Pattern으로 타입 판별
public class Problem2 {
    public static String getTypeName(Object obj) {
        // 구현: Integer, String, Double, 배열, null, 기타
        return "";
    }
    
    public static void main(String[] args) {
        System.out.println(getTypeName(42));           // "Integer"
        System.out.println(getTypeName("Hello"));      // "String"
        System.out.println(getTypeName(3.14));         // "Double"
        System.out.println(getTypeName(new int[]{}));  // "Array"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static String getTypeName(Object obj) {
    return switch (obj) {
        case null -> "Null";
        case Integer i -> "Integer";
        case String s -> "String";
        case Double d -> "Double";
        case int[] arr -> "Array";
        default -> "Other";
    };
}
```
</details>

---

### 문제 3: 요일 판별

```java
// 요일 그룹 판별
enum Day {
    MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY, SATURDAY, SUNDAY
}

public class Problem3 {
    public static String getDayType(Day day) {
        // 구현: 평일/주말
        return "";
    }
    
    public static void main(String[] args) {
        System.out.println(getDayType(Day.MONDAY));    // "평일"
        System.out.println(getDayType(Day.SATURDAY));  // "주말"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public static String getDayType(Day day) {
    return switch (day) {
        case MONDAY, TUESDAY, WEDNESDAY, THURSDAY, FRIDAY -> "평일";
        case SATURDAY, SUNDAY -> "주말";
    };
}
```
</details>

---

## 📌 핵심 정리

### 기본 문법
```java
String result = switch (value) {
    case 1 -> "One";
    case 2, 3 -> "Two or Three";
    default -> "Other";
};
```

### yield 사용
```java
String result = switch (value) {
    case 1 -> {
        System.out.println("Processing...");
        yield "One";
    }
    default -> "Other";
};
```

### Type Pattern
```java
String result = switch (obj) {
    case Integer i -> "정수: " + i;
    case String s -> "문자열: " + s;
    case null -> "null";
    default -> "기타";
};
```

### Guarded Pattern
```java
String result = switch (obj) {
    case String s when s.length() > 5 -> "긴 문자열";
    case String s -> "짧은 문자열";
    default -> "기타";
};
```

### Sealed Class
```java
// default 불필요
double area = switch (shape) {
    case Circle c -> c.area();
    case Rectangle r -> r.area();
};
```

### Best Practices
```
✅ 모든 케이스 명시 (완전성)
✅ 화살표 구문 사용
✅ null 케이스 처리
✅ Sealed Class와 조합
❌ default 남용
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. Record](./ModernJava-01-Record.md) | [다음: 03. Sealed Class →](./ModernJava-03-Sealed.md)**

</div>
