# Collections 15. Stack 구현 (Deque 사용)

> Deque로 Stack 완벽 구현하기  
> Java API Reference

---

## 📑 목차

1. [Stack의 문제점](#1-stack의-문제점)
2. [Deque로 Stack 구현](#2-deque로-stack-구현)
3. [Stack 연산](#3-stack-연산)
4. [응용 문제](#4-응용-문제)
5. [실전 활용](#5-실전-활용)
6. [실전 연습 문제](#6-실전-연습-문제)

---

## 1. Stack의 문제점

### 1.1 레거시 Stack 클래스

```java
import java.util.*;

public class StackProblem {
    public static void main(String[] args) {
        System.out.println("=== Stack 클래스 문제점 ===\n");
        
        System.out.println("1. 레거시 (Java 1.0)");
        System.out.println("   - Vector 상속");
        System.out.println("   - 오래된 설계\n");
        
        System.out.println("2. 동기화 오버헤드");
        System.out.println("   - 모든 메서드 synchronized");
        System.out.println("   - 단일 스레드에서도 느림\n");
        
        System.out.println("3. Vector 상속 문제");
        System.out.println("   - 중간 삽입/삭제 가능");
        System.out.println("   - Stack 의미 위반\n");
        
        System.out.println("4. 비일관적 API");
        System.out.println("   - push/pop (Stack)");
        System.out.println("   - add/remove (Vector)\n");
        
        System.out.println("=== 해결책 ===");
        System.out.println("Deque 인터페이스 사용!");
    }
}
```

### 1.2 성능 비교

```java
public class PerformanceComparison {
    public static void main(String[] args) {
        int n = 1000000;
        
        // Stack 클래스
        Stack<Integer> stack = new Stack<>();
        long start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            stack.push(i);
        }
        long time1 = System.currentTimeMillis() - start;
        
        
        // Deque
        Deque<Integer> deque = new ArrayDeque<>();
        start = System.currentTimeMillis();
        for (int i = 0; i < n; i++) {
            deque.push(i);
        }
        long time2 = System.currentTimeMillis() - start;
        
        
        System.out.println("=== " + n + "개 push ===");
        System.out.println("Stack:  " + time1 + "ms");
        System.out.println("Deque:  " + time2 + "ms");
        System.out.println("\nDeque가 훨씬 빠름!");
    }
}
```

---

## 2. Deque로 Stack 구현

### 2.1 기본 Stack 연산

```java
public class DequeAsStack {
    public static void main(String[] args) {
        // ArrayDeque로 Stack
        Deque<String> stack = new ArrayDeque<>();
        
        System.out.println("=== Stack 연산 ===\n");
        
        // push (추가)
        stack.push("A");
        stack.push("B");
        stack.push("C");
        System.out.println("push 후: " + stack);
        // [C, B, A]
        
        
        // peek (조회)
        String top = stack.peek();
        System.out.println("peek: " + top);  // C
        System.out.println("stack: " + stack);  // 변화 없음
        
        
        // pop (제거)
        String popped = stack.pop();
        System.out.println("pop: " + popped);  // C
        System.out.println("stack: " + stack);
        // [B, A]
        
        
        // 크기 확인
        System.out.println("size: " + stack.size());
        System.out.println("isEmpty: " + stack.isEmpty());
    }
}
```

### 2.2 메서드 매핑

```java
public class MethodMapping {
    public static void main(String[] args) {
        System.out.println("=== Stack 메서드 매핑 ===\n");
        
        System.out.println("Stack 메서드  →  Deque 메서드");
        System.out.println("--------------------------------");
        System.out.println("push(e)       →  push(e)");
        System.out.println("              →  offerFirst(e)");
        System.out.println("              →  addFirst(e)\n");
        
        System.out.println("pop()         →  pop()");
        System.out.println("              →  pollFirst()");
        System.out.println("              →  removeFirst()\n");
        
        System.out.println("peek()        →  peek()");
        System.out.println("              →  peekFirst()");
        System.out.println("              →  getFirst()\n");
        
        System.out.println("isEmpty()     →  isEmpty()");
        System.out.println("size()        →  size()");
    }
}
```

---

## 3. Stack 연산

### 3.1 기본 연산 예제

```java
public class StackOperations {
    public static void main(String[] args) {
        Deque<Integer> stack = new ArrayDeque<>();
        
        // 여러 요소 push
        for (int i = 1; i <= 5; i++) {
            stack.push(i);
            System.out.println("push " + i + ": " + stack);
        }
        
        System.out.println("\n=== pop 순서 ===");
        while (!stack.isEmpty()) {
            System.out.println("pop: " + stack.pop());
        }
        // 5, 4, 3, 2, 1 (LIFO)
        
        
        // 빈 스택
        System.out.println("\n빈 스택 peek: " + stack.peek());  // null
        
        try {
            stack.pop();  // 예외
        } catch (NoSuchElementException e) {
            System.out.println("빈 스택 pop: 예외!");
        }
    }
}
```

### 3.2 조건부 연산

```java
public class ConditionalOperations {
    public static void main(String[] args) {
        Deque<Integer> stack = new ArrayDeque<>();
        
        // 안전한 pop
        Integer value = stack.isEmpty() ? null : stack.pop();
        System.out.println("안전한 pop: " + value);
        
        
        // 조건부 push
        stack.push(1);
        stack.push(2);
        stack.push(3);
        
        if (stack.peek() != null && stack.peek() > 2) {
            stack.pop();
            System.out.println("3 제거됨");
        }
        
        System.out.println("Stack: " + stack);
    }
}
```

---

## 4. 응용 문제

### 4.1 괄호 검증

```java
public class BracketValidation {
    public static boolean isValid(String s) {
        Deque<Character> stack = new ArrayDeque<>();
        
        for (char c : s.toCharArray()) {
            if (c == '(' || c == '[' || c == '{') {
                stack.push(c);
            } else {
                if (stack.isEmpty()) return false;
                
                char top = stack.pop();
                if (c == ')' && top != '(') return false;
                if (c == ']' && top != '[') return false;
                if (c == '}' && top != '{') return false;
            }
        }
        
        return stack.isEmpty();
    }
    
    public static void main(String[] args) {
        String[] tests = {"()", "()[]{}", "(]", "([)]", "{[]}"};
        
        for (String test : tests) {
            System.out.println(test + ": " + isValid(test));
        }
    }
}
```

### 4.2 후위 표기식 계산

```java
public class PostfixEvaluation {
    public static int evaluate(String expression) {
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (String token : expression.split(" ")) {
            if (isOperator(token)) {
                int b = stack.pop();
                int a = stack.pop();
                int result = calculate(a, b, token);
                stack.push(result);
            } else {
                stack.push(Integer.parseInt(token));
            }
        }
        
        return stack.pop();
    }
    
    private static boolean isOperator(String s) {
        return s.equals("+") || s.equals("-") || 
               s.equals("*") || s.equals("/");
    }
    
    private static int calculate(int a, int b, String op) {
        switch (op) {
            case "+": return a + b;
            case "-": return a - b;
            case "*": return a * b;
            case "/": return a / b;
            default: throw new IllegalArgumentException();
        }
    }
    
    public static void main(String[] args) {
        String expr = "3 4 + 2 * 7 /";
        System.out.println("표기식: " + expr);
        System.out.println("결과: " + evaluate(expr));
        // (3 + 4) * 2 / 7 = 2
    }
}
```

### 4.3 중위 → 후위 변환

```java
public class InfixToPostfix {
    public static String convert(String infix) {
        StringBuilder result = new StringBuilder();
        Deque<Character> stack = new ArrayDeque<>();
        
        for (char c : infix.toCharArray()) {
            if (Character.isLetterOrDigit(c)) {
                result.append(c);
            } else if (c == '(') {
                stack.push(c);
            } else if (c == ')') {
                while (!stack.isEmpty() && stack.peek() != '(') {
                    result.append(stack.pop());
                }
                stack.pop();  // '(' 제거
            } else {  // 연산자
                while (!stack.isEmpty() && 
                       precedence(c) <= precedence(stack.peek())) {
                    result.append(stack.pop());
                }
                stack.push(c);
            }
        }
        
        while (!stack.isEmpty()) {
            result.append(stack.pop());
        }
        
        return result.toString();
    }
    
    private static int precedence(char op) {
        switch (op) {
            case '+':
            case '-': return 1;
            case '*':
            case '/': return 2;
            default: return 0;
        }
    }
    
    public static void main(String[] args) {
        String infix = "A+B*C";
        String postfix = convert(infix);
        
        System.out.println("중위: " + infix);
        System.out.println("후위: " + postfix);
        // ABC*+
    }
}
```

---

## 5. 실전 활용

### 5.1 함수 호출 스택

```java
public class CallStack {
    private Deque<String> callStack = new ArrayDeque<>();
    
    public void enter(String function) {
        callStack.push(function);
        printStack("Enter");
    }
    
    public void exit() {
        if (!callStack.isEmpty()) {
            String func = callStack.pop();
            printStack("Exit " + func);
        }
    }
    
    private void printStack(String action) {
        System.out.println(action + ": " + callStack);
    }
    
    public static void main(String[] args) {
        CallStack cs = new CallStack();
        
        cs.enter("main()");
        cs.enter("foo()");
        cs.enter("bar()");
        cs.exit();
        cs.enter("baz()");
        cs.exit();
        cs.exit();
        cs.exit();
    }
}
```

### 5.2 되돌리기 (Undo) 기능

```java
public class UndoManager {
    private Deque<String> undoStack = new ArrayDeque<>();
    private Deque<String> redoStack = new ArrayDeque<>();
    private String current = "";
    
    public void write(String text) {
        undoStack.push(current);
        current = text;
        redoStack.clear();
        System.out.println("Write: " + current);
    }
    
    public void undo() {
        if (undoStack.isEmpty()) {
            System.out.println("Undo 불가");
            return;
        }
        
        redoStack.push(current);
        current = undoStack.pop();
        System.out.println("Undo: " + current);
    }
    
    public void redo() {
        if (redoStack.isEmpty()) {
            System.out.println("Redo 불가");
            return;
        }
        
        undoStack.push(current);
        current = redoStack.pop();
        System.out.println("Redo: " + current);
    }
    
    public static void main(String[] args) {
        UndoManager um = new UndoManager();
        
        um.write("Hello");
        um.write("Hello World");
        um.write("Hello World!");
        
        um.undo();
        um.undo();
        um.redo();
    }
}
```

### 5.3 최소값 스택

```java
public class MinStack {
    private Deque<Integer> stack;
    private Deque<Integer> minStack;
    
    public MinStack() {
        this.stack = new ArrayDeque<>();
        this.minStack = new ArrayDeque<>();
    }
    
    public void push(int val) {
        stack.push(val);
        
        if (minStack.isEmpty() || val <= minStack.peek()) {
            minStack.push(val);
        }
    }
    
    public void pop() {
        int val = stack.pop();
        
        if (val == minStack.peek()) {
            minStack.pop();
        }
    }
    
    public int top() {
        return stack.peek();
    }
    
    public int getMin() {
        return minStack.peek();
    }
    
    public static void main(String[] args) {
        MinStack ms = new MinStack();
        
        ms.push(5);
        ms.push(2);
        ms.push(7);
        ms.push(1);
        
        System.out.println("최소값: " + ms.getMin());  // 1
        
        ms.pop();
        System.out.println("최소값: " + ms.getMin());  // 2
        
        ms.pop();
        System.out.println("최소값: " + ms.getMin());  // 2
    }
}
```

---

## 6. 실전 연습 문제

### 문제 1: 유효한 괄호 문자열

```java
// * 가 (, ), 빈 문자열 중 하나가 될 수 있을 때
public class Problem1 {
    public static boolean checkValidString(String s) {
        // 코드 작성
        return false;
    }
    
    public static void main(String[] args) {
        System.out.println(checkValidString("()"));       // true
        System.out.println(checkValidString("(*)"));      // true
        System.out.println(checkValidString("(*))"));     // true
        System.out.println(checkValidString("(())"));     // true
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static boolean checkValidString(String s) {
        Deque<Integer> leftStack = new ArrayDeque<>();
        Deque<Integer> starStack = new ArrayDeque<>();
        
        for (int i = 0; i < s.length(); i++) {
            char c = s.charAt(i);
            
            if (c == '(') {
                leftStack.push(i);
            } else if (c == '*') {
                starStack.push(i);
            } else {  // ')'
                if (!leftStack.isEmpty()) {
                    leftStack.pop();
                } else if (!starStack.isEmpty()) {
                    starStack.pop();
                } else {
                    return false;
                }
            }
        }
        
        // 남은 '('를 '*'로 매칭
        while (!leftStack.isEmpty() && !starStack.isEmpty()) {
            if (leftStack.pop() > starStack.pop()) {
                return false;
            }
        }
        
        return leftStack.isEmpty();
    }
}
```
</details>

---

### 문제 2: 일일 온도

```java
// 각 날짜별로 며칠 후에 더 따뜻한 날이 오는지
public class Problem2 {
    public static int[] dailyTemperatures(int[] temps) {
        // 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        int[] temps = {73, 74, 75, 71, 69, 72, 76, 73};
        int[] result = dailyTemperatures(temps);
        
        System.out.println("온도: " + Arrays.toString(temps));
        System.out.println("대기: " + Arrays.toString(result));
        // [1, 1, 4, 2, 1, 1, 0, 0]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int[] dailyTemperatures(int[] temps) {
        int n = temps.length;
        int[] result = new int[n];
        Deque<Integer> stack = new ArrayDeque<>();
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && temps[stack.peek()] < temps[i]) {
                int idx = stack.pop();
                result[idx] = i - idx;
            }
            stack.push(i);
        }
        
        return result;
    }
}
```
</details>

---

### 문제 3: 히스토그램 최대 직사각형

```java
// 히스토그램에서 가장 큰 직사각형 넓이
public class Problem3 {
    public static int largestRectangle(int[] heights) {
        // 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        int[] heights = {2, 1, 5, 6, 2, 3};
        int area = largestRectangle(heights);
        
        System.out.println("높이: " + Arrays.toString(heights));
        System.out.println("최대 넓이: " + area);
        // 10 (5*2)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static int largestRectangle(int[] heights) {
        Deque<Integer> stack = new ArrayDeque<>();
        int maxArea = 0;
        int n = heights.length;
        
        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && heights[stack.peek()] > heights[i]) {
                int height = heights[stack.pop()];
                int width = stack.isEmpty() ? i : i - stack.peek() - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.push(i);
        }
        
        while (!stack.isEmpty()) {
            int height = heights[stack.pop()];
            int width = stack.isEmpty() ? n : n - stack.peek() - 1;
            maxArea = Math.max(maxArea, height * width);
        }
        
        return maxArea;
    }
}
```
</details>

---

## 📌 핵심 정리

### Stack 클래스 대신 Deque
```java
// ❌ 사용하지 말 것
Stack<String> stack = new Stack<>();

// ✅ 권장
Deque<String> stack = new ArrayDeque<>();
```

### 기본 연산
```java
Deque<String> stack = new ArrayDeque<>();

// 추가
stack.push("A");

// 조회
String top = stack.peek();

// 제거
String popped = stack.pop();

// 크기/비어있는지
int size = stack.size();
boolean empty = stack.isEmpty();
```

### 메서드 매핑
```
push(e)  = offerFirst(e) = addFirst(e)
pop()    = pollFirst()   = removeFirst()
peek()   = peekFirst()   = getFirst()
```

### 사용 시기
```
✅ 괄호 검증
✅ 후위 표기식
✅ 함수 호출 스택
✅ Undo/Redo
✅ DFS
❌ Queue 필요 (FIFO)
❌ 양방향 접근
```

### 장점
```
1. 빠름 (동기화X)
2. 메모리 효율적
3. 일관된 API
4. 현대적 설계
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 14. PriorityQueue](./Collections-14-PriorityQueue.md) | [다음: 16. Collections 유틸 →](./Collections-16-CollectionsUtil.md)**

</div>
