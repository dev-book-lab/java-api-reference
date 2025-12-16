# String 05. 분리와 결합

> 문자열을 나누고 합치는 모든 방법  
> Java API Reference

---

## 📑 목차

1. [split() - 문자열 분리](#1-split---문자열-분리)
2. [join() - 문자열 결합](#2-join---문자열-결합)
3. [StringJoiner](#3-stringjoiner)
4. [실전 응용 패턴](#4-실전-응용-패턴)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. split() - 문자열 분리

### 1.1 기본 사용법

```java
public class SplitBasic {
    public static void main(String[] args) {
        String str = "apple,banana,cherry";
        
        // 쉼표로 분리
        String[] fruits = str.split(",");
        System.out.println(Arrays.toString(fruits));
        // [apple, banana, cherry]
        
        
        // 공백으로 분리
        String sentence = "Hello World Java";
        String[] words = sentence.split(" ");
        System.out.println(Arrays.toString(words));
        // [Hello, World, Java]
        
        
        // 여러 문자로 분리 (정규식)
        String text = "one,two;three:four";
        String[] parts = text.split("[,;:]");
        System.out.println(Arrays.toString(parts));
        // [one, two, three, four]
    }
}
```

### 1.2 limit 파라미터

```java
public class SplitLimit {
    public static void main(String[] args) {
        String str = "a:b:c:d:e";
        
        // limit 없음: 전체 분리
        String[] parts1 = str.split(":");
        System.out.println(Arrays.toString(parts1));
        // [a, b, c, d, e]
        
        
        // limit > 0: 최대 n개로 분리
        String[] parts2 = str.split(":", 3);
        System.out.println(Arrays.toString(parts2));
        // [a, b, c:d:e] (3개까지만, 나머지는 마지막에)
        
        
        // limit = 0: 끝의 빈 문자열 제거 (기본값)
        String str2 = "a:b:c::";
        String[] parts3 = str2.split(":", 0);
        System.out.println(Arrays.toString(parts3));
        // [a, b, c] (끝의 빈 문자열 제거)
        
        
        // limit < 0: 끝의 빈 문자열 포함
        String[] parts4 = str2.split(":", -1);
        System.out.println(Arrays.toString(parts4));
        // [a, b, c, , ] (빈 문자열 포함)
    }
}
```

### 1.3 정규표현식 활용

```java
public class SplitRegex {
    public static void main(String[] args) {
        // 공백 문자로 분리 (스페이스, 탭, 줄바꿈 등)
        String text = "Hello\tWorld\nJava";
        String[] parts1 = text.split("\\s+");
        System.out.println(Arrays.toString(parts1));
        // [Hello, World, Java]
        
        
        // 숫자로 분리
        String str = "apple123banana456cherry";
        String[] parts2 = str.split("\\d+");
        System.out.println(Arrays.toString(parts2));
        // [apple, banana, cherry]
        
        
        // 대문자로 분리 (카멜케이스 분해)
        String camel = "helloWorldJavaProgramming";
        String[] parts3 = camel.split("(?=[A-Z])");
        System.out.println(Arrays.toString(parts3));
        // [hello, World, Java, Programming]
        
        
        // 특수문자로 분리
        String text2 = "one@two#three$four";
        String[] parts4 = text2.split("[^a-zA-Z]+");
        System.out.println(Arrays.toString(parts4));
        // [one, two, three, four]
    }
}
```

### 1.4 주의사항

#### 빈 문자열 처리
```java
public class SplitEmpty {
    public static void main(String[] args) {
        String str = "a,,b,,c";
        
        String[] parts = str.split(",");
        System.out.println(Arrays.toString(parts));
        // [a, , b, , c] (빈 문자열 포함!)
        
        System.out.println(parts.length);  // 5
        
        
        // 빈 문자열 제거하려면
        String[] filtered = Arrays.stream(parts)
            .filter(s -> !s.isEmpty())
            .toArray(String[]::new);
        System.out.println(Arrays.toString(filtered));
        // [a, b, c]
    }
}
```

#### 특수 문자 이스케이프
```java
public class SplitEscape {
    public static void main(String[] args) {
        String str = "192.168.0.1";
        
        // ❌ 틀린 방법: .은 정규식에서 "모든 문자"
        String[] wrong = str.split(".");
        System.out.println(Arrays.toString(wrong));
        // [] (전부 구분자로 인식!)
        
        // ✅ 올바른 방법: 이스케이프
        String[] correct = str.split("\\.");
        System.out.println(Arrays.toString(correct));
        // [192, 168, 0, 1]
        
        
        // 특수 문자들: . * + ? | ( ) [ ] { } ^ $ \
        // 모두 이스케이프 필요!
    }
}
```

### 1.5 실전 활용

#### CSV 파싱
```java
public class CsvParsing {
    public static void main(String[] args) {
        String csv = "John,25,USA,Engineer";
        
        String[] data = csv.split(",");
        
        String name = data[0];
        int age = Integer.parseInt(data[1]);
        String country = data[2];
        String job = data[3];
        
        System.out.println("Name: " + name);
        System.out.println("Age: " + age);
        
        
        // 복잡한 CSV (쉼표가 데이터에 포함)
        String complexCsv = "\"Kim, John\",25,USA";
        // 이런 경우는 전문 CSV 라이브러리 사용 추천!
    }
}
```

#### URL 파라미터 파싱
```java
public class UrlParams {
    public static Map<String, String> parseParams(String query) {
        Map<String, String> params = new HashMap<>();
        
        if (query == null || query.isEmpty()) {
            return params;
        }
        
        String[] pairs = query.split("&");
        for (String pair : pairs) {
            String[] keyValue = pair.split("=", 2);
            if (keyValue.length == 2) {
                params.put(keyValue[0], keyValue[1]);
            }
        }
        
        return params;
    }
    
    public static void main(String[] args) {
        String query = "name=John&age=25&city=Seoul";
        Map<String, String> params = parseParams(query);
        
        System.out.println(params);
        // {name=John, age=25, city=Seoul}
    }
}
```

#### 경로 분리
```java
public class PathSplit {
    public static void main(String[] args) {
        // Unix 경로
        String unixPath = "/home/user/documents/file.txt";
        String[] unixParts = unixPath.split("/");
        System.out.println(Arrays.toString(unixParts));
        // [, home, user, documents, file.txt]
        
        
        // Windows 경로 (이스케이프 필요!)
        String winPath = "C:\\Users\\John\\Documents\\file.txt";
        String[] winParts = winPath.split("\\\\");
        System.out.println(Arrays.toString(winParts));
        // [C:, Users, John, Documents, file.txt]
        
        
        // 파일명과 확장자 분리
        String filename = "document.backup.txt";
        String[] nameParts = filename.split("\\.");
        System.out.println(Arrays.toString(nameParts));
        // [document, backup, txt]
        
        // 확장자만 가져오기
        String ext = nameParts[nameParts.length - 1];
        System.out.println("Extension: " + ext);  // txt
    }
}
```

---

## 2. join() - 문자열 결합

### 2.1 기본 사용법

```java
public class JoinBasic {
    public static void main(String[] args) {
        // 배열 결합
        String[] fruits = {"apple", "banana", "cherry"};
        String joined = String.join(", ", fruits);
        System.out.println(joined);
        // "apple, banana, cherry"
        
        
        // List 결합
        List<String> words = Arrays.asList("Hello", "World", "Java");
        String sentence = String.join(" ", words);
        System.out.println(sentence);
        // "Hello World Java"
        
        
        // 구분자 없이
        String noDelim = String.join("", "a", "b", "c");
        System.out.println(noDelim);  // "abc"
        
        
        // 가변 인자
        String result = String.join("-", "2024", "01", "15");
        System.out.println(result);  // "2024-01-15"
    }
}
```

### 2.2 split()과 join() 조합

```java
public class SplitJoinCombo {
    public static void main(String[] args) {
        // 공백을 하이픈으로 변경
        String text = "Hello World Java";
        String result = String.join("-", text.split(" "));
        System.out.println(result);
        // "Hello-World-Java"
        
        
        // CSV를 TSV로 변환
        String csv = "a,b,c,d";
        String tsv = String.join("\t", csv.split(","));
        System.out.println(tsv);
        // "a	b	c	d"
        
        
        // 역순 정렬
        String[] words = "one two three".split(" ");
        Collections.reverse(Arrays.asList(words));
        String reversed = String.join(" ", words);
        System.out.println(reversed);
        // "three two one"
    }
}
```

### 2.3 실전 활용

#### SQL IN 절 생성
```java
public class SqlIn {
    public static String createInClause(List<Integer> ids) {
        List<String> strIds = ids.stream()
            .map(String::valueOf)
            .collect(Collectors.toList());
        
        return "(" + String.join(", ", strIds) + ")";
    }
    
    public static void main(String[] args) {
        List<Integer> ids = Arrays.asList(1, 2, 3, 4, 5);
        String inClause = createInClause(ids);
        
        String sql = "SELECT * FROM users WHERE id IN " + inClause;
        System.out.println(sql);
        // SELECT * FROM users WHERE id IN (1, 2, 3, 4, 5)
    }
}
```

#### 경로 결합
```java
public class PathJoin {
    public static String joinPath(String... parts) {
        return String.join("/", parts);
    }
    
    public static void main(String[] args) {
        String path = joinPath("home", "user", "documents", "file.txt");
        System.out.println(path);
        // "home/user/documents/file.txt"
        
        
        // 또는
        String[] pathParts = {"var", "log", "app.log"};
        String fullPath = "/" + String.join("/", pathParts);
        System.out.println(fullPath);
        // "/var/log/app.log"
    }
}
```

#### 태그 생성
```java
public class TagGenerator {
    public static String createHashtags(String[] tags) {
        List<String> hashtagged = Arrays.stream(tags)
            .map(tag -> "#" + tag)
            .collect(Collectors.toList());
        
        return String.join(" ", hashtagged);
    }
    
    public static void main(String[] args) {
        String[] tags = {"java", "programming", "tutorial"};
        System.out.println(createHashtags(tags));
        // "#java #programming #tutorial"
    }
}
```

---

## 3. StringJoiner

### 3.1 기본 사용법

```java
import java.util.StringJoiner;

public class StringJoinerBasic {
    public static void main(String[] args) {
        // 기본 생성자
        StringJoiner sj = new StringJoiner(", ");
        
        sj.add("apple");
        sj.add("banana");
        sj.add("cherry");
        
        System.out.println(sj.toString());
        // "apple, banana, cherry"
        
        
        // 체이닝
        StringJoiner sj2 = new StringJoiner(" | ");
        String result = sj2.add("A").add("B").add("C").toString();
        System.out.println(result);
        // "A | B | C"
    }
}
```

### 3.2 prefix와 suffix

```java
public class StringJoinerPrefixSuffix {
    public static void main(String[] args) {
        // prefix, suffix 지정
        StringJoiner sj = new StringJoiner(", ", "[", "]");
        
        sj.add("1");
        sj.add("2");
        sj.add("3");
        
        System.out.println(sj.toString());
        // "[1, 2, 3]"
        
        
        // 괄호로 감싸기
        StringJoiner sql = new StringJoiner(", ", "(", ")");
        sql.add("'John'").add("25").add("'USA'");
        System.out.println(sql.toString());
        // "('John', 25, 'USA')"
    }
}
```

### 3.3 setEmptyValue()

```java
public class StringJoinerEmpty {
    public static void main(String[] args) {
        // 빈 경우 기본값
        StringJoiner sj1 = new StringJoiner(", ");
        System.out.println(sj1.toString());  // "" (빈 문자열)
        
        
        // 빈 경우 대체 값 설정
        StringJoiner sj2 = new StringJoiner(", ");
        sj2.setEmptyValue("(empty)");
        System.out.println(sj2.toString());  // "(empty)"
        
        
        // prefix/suffix가 있을 때
        StringJoiner sj3 = new StringJoiner(", ", "[", "]");
        System.out.println(sj3.toString());  // "[]"
        
        sj3.setEmptyValue("empty");
        System.out.println(sj3.toString());  // "empty"
    }
}
```

### 3.4 merge()

```java
public class StringJoinerMerge {
    public static void main(String[] args) {
        StringJoiner sj1 = new StringJoiner(", ");
        sj1.add("A").add("B");
        
        StringJoiner sj2 = new StringJoiner(", ");
        sj2.add("C").add("D");
        
        // 병합
        sj1.merge(sj2);
        System.out.println(sj1.toString());
        // "A, B, C, D"
        
        
        // prefix/suffix가 있을 때
        StringJoiner sj3 = new StringJoiner(", ", "[", "]");
        sj3.add("1").add("2");
        
        StringJoiner sj4 = new StringJoiner(", ", "[", "]");
        sj4.add("3").add("4");
        
        sj3.merge(sj4);
        System.out.println(sj3.toString());
        // "[1, 2, 3, 4]" (prefix/suffix는 한 번만)
    }
}
```

### 3.5 실전 활용

#### SQL INSERT 문 생성
```java
public class SqlInsert {
    public static String createInsert(String table, Map<String, String> data) {
        StringJoiner columns = new StringJoiner(", ", "(", ")");
        StringJoiner values = new StringJoiner(", ", "(", ")");
        
        for (Map.Entry<String, String> entry : data.entrySet()) {
            columns.add(entry.getKey());
            values.add("'" + entry.getValue() + "'");
        }
        
        return "INSERT INTO " + table + " " + columns + " VALUES " + values;
    }
    
    public static void main(String[] args) {
        Map<String, String> data = new LinkedHashMap<>();
        data.put("name", "John");
        data.put("age", "25");
        data.put("city", "Seoul");
        
        System.out.println(createInsert("users", data));
        // INSERT INTO users (name, age, city) VALUES ('John', '25', 'Seoul')
    }
}
```

#### JSON 배열 생성
```java
public class JsonArray {
    public static String createJsonArray(List<String> items) {
        StringJoiner sj = new StringJoiner(", ", "[", "]");
        
        for (String item : items) {
            sj.add("\"" + item + "\"");
        }
        
        return sj.toString();
    }
    
    public static void main(String[] args) {
        List<String> fruits = Arrays.asList("apple", "banana", "cherry");
        System.out.println(createJsonArray(fruits));
        // ["apple", "banana", "cherry"]
    }
}
```

---

## 4. 실전 응용 패턴

### 4.1 CSV 파싱 및 변환

```java
public class CsvTransform {
    public static void main(String[] args) {
        // CSV → Map
        String csv = "name:John,age:25,city:Seoul";
        
        Map<String, String> data = new HashMap<>();
        String[] pairs = csv.split(",");
        for (String pair : pairs) {
            String[] kv = pair.split(":", 2);
            if (kv.length == 2) {
                data.put(kv[0], kv[1]);
            }
        }
        
        System.out.println(data);
        // {name=John, age=25, city=Seoul}
        
        
        // Map → CSV
        StringJoiner sj = new StringJoiner(",");
        for (Map.Entry<String, String> entry : data.entrySet()) {
            sj.add(entry.getKey() + ":" + entry.getValue());
        }
        
        System.out.println(sj.toString());
        // name:John,age:25,city:Seoul
    }
}
```

### 4.2 경로 정규화

```java
public class PathNormalize {
    public static String normalizePath(String path) {
        // 슬래시로 통일
        path = path.replace("\\", "/");
        
        // 연속 슬래시를 하나로
        path = path.replaceAll("/+", "/");
        
        // 경로 분리 및 .. 처리
        String[] parts = path.split("/");
        List<String> normalized = new ArrayList<>();
        
        for (String part : parts) {
            if (part.equals("..") && !normalized.isEmpty()) {
                normalized.remove(normalized.size() - 1);
            } else if (!part.equals(".") && !part.isEmpty()) {
                normalized.add(part);
            }
        }
        
        String result = String.join("/", normalized);
        return path.startsWith("/") ? "/" + result : result;
    }
    
    public static void main(String[] args) {
        System.out.println(normalizePath("/home/user/../john/./docs"));
        // "/home/john/docs"
        
        System.out.println(normalizePath("a//b///c"));
        // "a/b/c"
    }
}
```

### 4.3 단어 빈도 계산

```java
public class WordFrequency {
    public static Map<String, Integer> countWords(String text) {
        // 소문자 변환 및 특수문자 제거
        text = text.toLowerCase().replaceAll("[^a-z\\s]", "");
        
        String[] words = text.split("\\s+");
        Map<String, Integer> freq = new HashMap<>();
        
        for (String word : words) {
            if (!word.isEmpty()) {
                freq.put(word, freq.getOrDefault(word, 0) + 1);
            }
        }
        
        return freq;
    }
    
    public static void main(String[] args) {
        String text = "Hello world! Hello Java. Java is great.";
        Map<String, Integer> freq = countWords(text);
        
        System.out.println(freq);
        // {hello=2, world=1, java=2, is=1, great=1}
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 이메일 도메인 추출

```java
// 이메일 주소에서 고유 도메인 목록 추출
public class Problem1 {
    public static Set<String> extractDomains(String[] emails) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        String[] emails = {
            "user1@gmail.com",
            "user2@naver.com",
            "user3@gmail.com",
            "user4@daum.net"
        };
        
        System.out.println(extractDomains(emails));
        // [gmail.com, naver.com, daum.net]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static Set<String> extractDomains(String[] emails) {
        Set<String> domains = new HashSet<>();
        
        for (String email : emails) {
            String[] parts = email.split("@");
            if (parts.length == 2) {
                domains.add(parts[1]);
            }
        }
        
        return domains;
    }
    
    // Stream 버전
    public static Set<String> extractDomains2(String[] emails) {
        return Arrays.stream(emails)
            .map(email -> email.split("@"))
            .filter(parts -> parts.length == 2)
            .map(parts -> parts[1])
            .collect(Collectors.toSet());
    }
}
```
</details>

---

### 문제 2: 로그 파싱

```java
// 로그에서 에러 레벨 메시지만 추출
// 형식: "[레벨] 메시지"
public class Problem2 {
    public static List<String> extractErrors(String log) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        String log = "[INFO] Application started\n" +
                     "[ERROR] Connection failed\n" +
                     "[DEBUG] Processing data\n" +
                     "[ERROR] File not found";
        
        System.out.println(extractErrors(log));
        // [Connection failed, File not found]
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static List<String> extractErrors(String log) {
        List<String> errors = new ArrayList<>();
        String[] lines = log.split("\n");
        
        for (String line : lines) {
            if (line.startsWith("[ERROR]")) {
                String message = line.split("]", 2)[1].trim();
                errors.add(message);
            }
        }
        
        return errors;
    }
    
    // 정규식 버전
    public static List<String> extractErrors2(String log) {
        return Arrays.stream(log.split("\n"))
            .filter(line -> line.startsWith("[ERROR]"))
            .map(line -> line.replaceFirst("\\[ERROR\\]\\s*", ""))
            .collect(Collectors.toList());
    }
}
```
</details>

---

### 문제 3: 브레드크럼 생성

```java
// URL 경로를 브레드크럼 네비게이션으로 변환
// 예: "/home/user/documents" → "Home > User > Documents"
public class Problem3 {
    public static String createBreadcrumb(String path) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        System.out.println(createBreadcrumb("/home/user/documents"));
        // "Home > User > Documents"
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static String createBreadcrumb(String path) {
        // 앞뒤 슬래시 제거
        path = path.replaceAll("^/|/$", "");
        
        if (path.isEmpty()) {
            return "Home";
        }
        
        // 경로 분리
        String[] parts = path.split("/");
        
        // 첫 글자 대문자로
        for (int i = 0; i < parts.length; i++) {
            parts[i] = parts[i].substring(0, 1).toUpperCase() 
                     + parts[i].substring(1);
        }
        
        return String.join(" > ", parts);
    }
}
```
</details>

---

### 문제 4: 테이블 포맷팅

```java
// 2차원 배열을 정렬된 테이블로 출력
public class Problem4 {
    public static String formatTable(String[][] data) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        String[][] data = {
            {"Name", "Age", "City"},
            {"John", "25", "Seoul"},
            {"Alice", "30", "Busan"}
        };
        
        System.out.println(formatTable(data));
        /* 출력:
        Name  | Age | City
        ------|-----|------
        John  | 25  | Seoul
        Alice | 30  | Busan
        */
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem4 {
    public static String formatTable(String[][] data) {
        if (data.length == 0) return "";
        
        // 각 열의 최대 길이 계산
        int cols = data[0].length;
        int[] maxLengths = new int[cols];
        
        for (String[] row : data) {
            for (int i = 0; i < cols; i++) {
                maxLengths[i] = Math.max(maxLengths[i], row[i].length());
            }
        }
        
        StringBuilder result = new StringBuilder();
        
        // 헤더
        result.append(formatRow(data[0], maxLengths)).append("\n");
        
        // 구분선
        StringJoiner separator = new StringJoiner("|");
        for (int len : maxLengths) {
            separator.add("-".repeat(len + 2));
        }
        result.append(separator.toString()).append("\n");
        
        // 데이터 행
        for (int i = 1; i < data.length; i++) {
            result.append(formatRow(data[i], maxLengths)).append("\n");
        }
        
        return result.toString();
    }
    
    private static String formatRow(String[] row, int[] maxLengths) {
        StringJoiner sj = new StringJoiner(" | ");
        for (int i = 0; i < row.length; i++) {
            String padded = String.format("%-" + maxLengths[i] + "s", row[i]);
            sj.add(padded);
        }
        return sj.toString();
    }
}
```
</details>

---

## 📌 핵심 정리

### split()
```java
String[] parts = str.split(",");           // 기본 분리
String[] parts = str.split(",", 3);        // 최대 3개로 분리
String[] parts = str.split("\\.");         // 특수문자는 이스케이프!
String[] parts = str.split("\\s+");        // 정규식 활용

// 주의: 빈 문자열도 포함됨!
```

### join()
```java
String.join(", ", array)                   // 배열 결합
String.join(" ", list)                     // List 결합
String.join("-", "a", "b", "c")            // 가변 인자
```

### StringJoiner
```java
StringJoiner sj = new StringJoiner(", ");              // 기본
StringJoiner sj = new StringJoiner(", ", "[", "]");   // prefix/suffix
sj.add("A").add("B");                                  // 체이닝
sj.setEmptyValue("empty");                             // 빈 값 처리
```

### 패턴
```java
// CSV → 배열
String[] parts = csv.split(",");

// 배열 → CSV
String csv = String.join(",", parts);

// 변환
String result = String.join("-", text.split(" "));
```

### 주의사항
```java
// ❌ 특수문자 이스케이프 안 함
str.split(".")  // 모든 문자로 분리됨!

// ✅ 이스케이프
str.split("\\.")

// ❌ 빈 문자열 무시
"a,,b".split(",")  // [a, , b] (빈 문자열 포함!)

// ✅ 필터링
Arrays.stream(parts).filter(s -> !s.isEmpty())
```

---

**다음 문서:** [String 06. StringBuilder & StringBuffer](String-06-StringBuilder-StringBuffer.md)
