# IO 02. 텍스트 파일 입출력

> 텍스트 읽기와 쓰기 - Reader/Writer 완전 정복  
> Java API Reference

---

## 📑 목차

1. [문자 스트림 기본](#1-문자-스트림-기본)
2. [파일 읽기](#2-파일-읽기)
3. [파일 쓰기](#3-파일-쓰기)
4. [버퍼링과 성능](#4-버퍼링과-성능)
5. [인코딩 처리](#5-인코딩-처리)
6. [실전 연습 문제](#6-실전-연습-문제)

---

## 1. 문자 스트림 기본

### 1.1 Reader/Writer 계층

```java
/*
스트림 계층 구조:

Reader (추상)
├── InputStreamReader
│   └── FileReader
├── BufferedReader
├── StringReader
└── CharArrayReader

Writer (추상)
├── OutputStreamWriter
│   └── FileWriter
├── BufferedWriter
├── StringWriter
└── PrintWriter
*/

public class StreamHierarchy {
    public static void main(String[] args) {
        System.out.println("=== 문자 스트림 ===");
        System.out.println("Reader/Writer: 문자 단위 처리");
        System.out.println("InputStream/OutputStream: 바이트 단위 처리");
        
        System.out.println("\n=== 주요 클래스 ===");
        System.out.println("FileReader/FileWriter: 파일");
        System.out.println("BufferedReader/BufferedWriter: 버퍼링");
        System.out.println("PrintWriter: 포맷 출력");
    }
}
```

### 1.2 try-with-resources

```java
import java.io.*;

public class TryWithResources {
    public static void main(String[] args) {
        // ❌ 전통적 방식 (복잡)
        FileReader fr = null;
        try {
            fr = new FileReader("test.txt");
            // 읽기...
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                if (fr != null) fr.close();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        
        
        // ✅ try-with-resources (권장)
        try (FileReader reader = new FileReader("test.txt")) {
            // 읽기...
            // 자동으로 close() 호출!
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 여러 리소스
        try (
            FileReader reader = new FileReader("input.txt");
            FileWriter writer = new FileWriter("output.txt")
        ) {
            // 사용...
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 2. 파일 읽기

### 2.1 FileReader 기본

```java
public class FileReaderBasic {
    public static void main(String[] args) {
        // 한 글자씩 읽기
        try (FileReader reader = new FileReader("test.txt")) {
            int ch;
            
            while ((ch = reader.read()) != -1) {
                System.out.print((char) ch);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 배열로 읽기
        try (FileReader reader = new FileReader("test.txt")) {
            char[] buffer = new char[1024];
            int len;
            
            while ((len = reader.read(buffer)) != -1) {
                System.out.print(new String(buffer, 0, len));
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 2.2 BufferedReader (권장)

```java
public class BufferedReaderDemo {
    public static void main(String[] args) {
        // 한 줄씩 읽기
        try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
            
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 줄 번호와 함께
        try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
            
            String line;
            int lineNumber = 1;
            
            while ((line = br.readLine()) != null) {
                System.out.println(lineNumber++ + ": " + line);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // Stream API (Java 8+)
        try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
            
            br.lines()
              .forEach(System.out::println);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 2.3 전체 읽기

```java
import java.nio.file.*;

public class ReadAllLines {
    public static void main(String[] args) {
        // Files.readAllLines (간단)
        try {
            List<String> lines = Files.readAllLines(Paths.get("test.txt"));
            
            for (String line : lines) {
                System.out.println(line);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // Files.readString (Java 11+)
        try {
            String content = Files.readString(Paths.get("test.txt"));
            System.out.println(content);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 커스텀 메서드
        String text = readFile("test.txt");
        System.out.println(text);
    }
    
    public static String readFile(String filename) {
        StringBuilder sb = new StringBuilder();
        
        try (BufferedReader br = new BufferedReader(new FileReader(filename))) {
            String line;
            
            while ((line = br.readLine()) != null) {
                sb.append(line).append("\n");
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        return sb.toString();
    }
}
```

---

## 3. 파일 쓰기

### 3.1 FileWriter 기본

```java
public class FileWriterBasic {
    public static void main(String[] args) {
        // 문자열 쓰기
        try (FileWriter writer = new FileWriter("output.txt")) {
            
            writer.write("Hello, World!\n");
            writer.write("Java IO\n");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 배열 쓰기
        try (FileWriter writer = new FileWriter("output.txt")) {
            
            char[] chars = "Hello".toCharArray();
            writer.write(chars);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 추가 모드 (append)
        try (FileWriter writer = new FileWriter("output.txt", true)) {
            
            writer.write("추가 내용\n");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 3.2 BufferedWriter (권장)

```java
public class BufferedWriterDemo {
    public static void main(String[] args) {
        // 기본 사용
        try (BufferedWriter bw = new BufferedWriter(new FileWriter("output.txt"))) {
            
            bw.write("첫 번째 줄");
            bw.newLine();  // 플랫폼 독립적 줄바꿈
            
            bw.write("두 번째 줄");
            bw.newLine();
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 여러 줄 쓰기
        List<String> lines = Arrays.asList(
            "Line 1",
            "Line 2",
            "Line 3"
        );
        
        try (BufferedWriter bw = new BufferedWriter(new FileWriter("output.txt"))) {
            
            for (String line : lines) {
                bw.write(line);
                bw.newLine();
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 3.3 PrintWriter (포맷 출력)

```java
public class PrintWriterDemo {
    public static void main(String[] args) {
        // PrintWriter 사용
        try (PrintWriter pw = new PrintWriter("output.txt")) {
            
            pw.println("Hello, World!");
            pw.println(123);
            pw.println(3.14);
            pw.println(true);
            
            // printf
            pw.printf("Name: %s, Age: %d\n", "John", 25);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 자동 flush
        try (PrintWriter pw = new PrintWriter(new FileWriter("output.txt"), true)) {
            
            pw.println("자동 flush!");
            // true: autoFlush on
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // BufferedWriter 위에
        try (PrintWriter pw = new PrintWriter(
                new BufferedWriter(new FileWriter("output.txt")))) {
            
            pw.println("버퍼링 + 포맷 출력");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 3.4 Files.write (간단)

```java
public class FilesWrite {
    public static void main(String[] args) {
        // 한 줄 쓰기
        try {
            Files.writeString(Paths.get("output.txt"), "Hello, World!");
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 여러 줄 쓰기
        List<String> lines = Arrays.asList(
            "Line 1",
            "Line 2",
            "Line 3"
        );
        
        try {
            Files.write(Paths.get("output.txt"), lines);
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 추가 모드
        try {
            Files.write(
                Paths.get("output.txt"),
                lines,
                StandardOpenOption.APPEND
            );
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 4. 버퍼링과 성능

### 4.1 버퍼링의 중요성

```java
public class BufferingPerformance {
    public static void main(String[] args) throws IOException {
        String filename = "large.txt";
        int lines = 100000;
        
        // 테스트 파일 생성
        createLargeFile(filename, lines);
        
        
        // 1. BufferedReader 없이 (느림)
        long start = System.currentTimeMillis();
        
        try (FileReader fr = new FileReader(filename)) {
            int ch;
            while ((ch = fr.read()) != -1) {
                // 처리...
            }
        }
        
        long time1 = System.currentTimeMillis() - start;
        System.out.println("FileReader: " + time1 + "ms");
        
        
        // 2. BufferedReader 사용 (빠름)
        start = System.currentTimeMillis();
        
        try (BufferedReader br = new BufferedReader(new FileReader(filename))) {
            String line;
            while ((line = br.readLine()) != null) {
                // 처리...
            }
        }
        
        long time2 = System.currentTimeMillis() - start;
        System.out.println("BufferedReader: " + time2 + "ms");
        
        System.out.println("\n속도 차이: " + (time1 / (double) time2) + "배");
    }
    
    static void createLargeFile(String filename, int lines) throws IOException {
        try (PrintWriter pw = new PrintWriter(filename)) {
            for (int i = 0; i < lines; i++) {
                pw.println("Line " + i);
            }
        }
    }
}
```

### 4.2 버퍼 크기 조정

```java
public class BufferSize {
    public static void main(String[] args) {
        // 기본 버퍼 크기 (8192)
        try (BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
            // 8KB 버퍼
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 큰 버퍼
        try (BufferedReader br = new BufferedReader(
                new FileReader("test.txt"), 
                65536)) {  // 64KB 버퍼
            
            // 큰 파일 처리 시 유용
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 작은 버퍼
        try (BufferedReader br = new BufferedReader(
                new FileReader("test.txt"), 
                1024)) {  // 1KB 버퍼
            
            // 메모리 제약이 있을 때
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 5. 인코딩 처리

### 5.1 인코딩 지정

```java
import java.nio.charset.*;

public class EncodingDemo {
    public static void main(String[] args) {
        // UTF-8 읽기
        try (BufferedReader br = new BufferedReader(
                new InputStreamReader(
                    new FileInputStream("test.txt"),
                    StandardCharsets.UTF_8))) {
            
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // EUC-KR 읽기
        try (BufferedReader br = new BufferedReader(
                new InputStreamReader(
                    new FileInputStream("test.txt"),
                    Charset.forName("EUC-KR")))) {
            
            String line;
            while ((line = br.readLine()) != null) {
                System.out.println(line);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // UTF-8 쓰기
        try (BufferedWriter bw = new BufferedWriter(
                new OutputStreamWriter(
                    new FileOutputStream("output.txt"),
                    StandardCharsets.UTF_8))) {
            
            bw.write("한글 테스트");
            bw.newLine();
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 5.2 Files API로 인코딩

```java
public class FilesEncoding {
    public static void main(String[] args) {
        // UTF-8 읽기 (기본)
        try {
            String content = Files.readString(
                Paths.get("test.txt"),
                StandardCharsets.UTF_8
            );
            
            System.out.println(content);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // EUC-KR 읽기
        try {
            List<String> lines = Files.readAllLines(
                Paths.get("test.txt"),
                Charset.forName("EUC-KR")
            );
            
            lines.forEach(System.out::println);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // UTF-8 쓰기
        try {
            Files.writeString(
                Paths.get("output.txt"),
                "한글 테스트",
                StandardCharsets.UTF_8
            );
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 5.3 인코딩 변환

```java
public class EncodingConversion {
    public static void main(String[] args) {
        // EUC-KR → UTF-8 변환
        try (
            BufferedReader br = new BufferedReader(
                new InputStreamReader(
                    new FileInputStream("input_euckr.txt"),
                    Charset.forName("EUC-KR")));
            
            BufferedWriter bw = new BufferedWriter(
                new OutputStreamWriter(
                    new FileOutputStream("output_utf8.txt"),
                    StandardCharsets.UTF_8))
        ) {
            String line;
            while ((line = br.readLine()) != null) {
                bw.write(line);
                bw.newLine();
            }
            
            System.out.println("변환 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 6. 실전 연습 문제

### 문제 1: 줄 번호 추가

```java
// 파일의 각 줄에 줄 번호 추가
public class Problem1 {
    public static void addLineNumbers(String input, String output) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        addLineNumbers("input.txt", "output.txt");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static void addLineNumbers(String input, String output) {
        try (
            BufferedReader br = new BufferedReader(new FileReader(input));
            PrintWriter pw = new PrintWriter(output)
        ) {
            String line;
            int lineNumber = 1;
            
            while ((line = br.readLine()) != null) {
                pw.printf("%3d: %s\n", lineNumber++, line);
            }
            
            System.out.println("완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
</details>

---

### 문제 2: 단어 개수 세기

```java
// 파일의 총 단어 개수
public class Problem2 {
    public static int countWords(String filename) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        int count = countWords("test.txt");
        System.out.println("Words: " + count);
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static int countWords(String filename) {
        int count = 0;
        
        try (BufferedReader br = new BufferedReader(new FileReader(filename))) {
            String line;
            
            while ((line = br.readLine()) != null) {
                String[] words = line.trim().split("\\s+");
                if (!line.trim().isEmpty()) {
                    count += words.length;
                }
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        return count;
    }
    
    // 상세 정보
    public static void analyzeFile(String filename) {
        int lines = 0;
        int words = 0;
        int chars = 0;
        
        try (BufferedReader br = new BufferedReader(new FileReader(filename))) {
            String line;
            
            while ((line = br.readLine()) != null) {
                lines++;
                chars += line.length();
                
                String[] wordsArray = line.trim().split("\\s+");
                if (!line.trim().isEmpty()) {
                    words += wordsArray.length;
                }
            }
            
            System.out.println("Lines: " + lines);
            System.out.println("Words: " + words);
            System.out.println("Chars: " + chars);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
</details>

---

### 문제 3: 파일 병합

```java
// 여러 파일을 하나로 병합
public class Problem3 {
    public static void mergeFiles(String[] inputs, String output) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        String[] files = {"file1.txt", "file2.txt", "file3.txt"};
        mergeFiles(files, "merged.txt");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static void mergeFiles(String[] inputs, String output) {
        try (PrintWriter pw = new PrintWriter(output)) {
            
            for (String input : inputs) {
                try (BufferedReader br = new BufferedReader(new FileReader(input))) {
                    
                    String line;
                    while ((line = br.readLine()) != null) {
                        pw.println(line);
                    }
                    
                    // 파일 구분선
                    pw.println("--- End of " + input + " ---");
                    pw.println();
                    
                } catch (IOException e) {
                    System.out.println("Error reading " + input);
                }
            }
            
            System.out.println("병합 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    // 구분선 없이
    public static void mergeFilesSimple(String[] inputs, String output) {
        try (BufferedWriter bw = new BufferedWriter(new FileWriter(output))) {
            
            for (String input : inputs) {
                try (BufferedReader br = new BufferedReader(new FileReader(input))) {
                    
                    String line;
                    while ((line = br.readLine()) != null) {
                        bw.write(line);
                        bw.newLine();
                    }
                    
                } catch (IOException e) {
                    System.out.println("Error reading " + input);
                }
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### 파일 읽기
```java
// BufferedReader (권장)
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    String line;
    while ((line = br.readLine()) != null) {
        System.out.println(line);
    }
}

// Files (간단)
List<String> lines = Files.readAllLines(Paths.get("file.txt"));
String content = Files.readString(Paths.get("file.txt"));
```

### 파일 쓰기
```java
// BufferedWriter
try (BufferedWriter bw = new BufferedWriter(new FileWriter("file.txt"))) {
    bw.write("Hello");
    bw.newLine();
}

// PrintWriter (포맷)
try (PrintWriter pw = new PrintWriter("file.txt")) {
    pw.println("Hello");
    pw.printf("Value: %d\n", 123);
}

// Files (간단)
Files.writeString(Paths.get("file.txt"), "Hello");
Files.write(Paths.get("file.txt"), lines);
```

### 인코딩
```java
// UTF-8 읽기
BufferedReader br = new BufferedReader(
    new InputStreamReader(
        new FileInputStream("file.txt"),
        StandardCharsets.UTF_8));

// UTF-8 쓰기
BufferedWriter bw = new BufferedWriter(
    new OutputStreamWriter(
        new FileOutputStream("file.txt"),
        StandardCharsets.UTF_8));

// Files API
Files.readString(path, StandardCharsets.UTF_8);
Files.writeString(path, text, StandardCharsets.UTF_8);
```

### try-with-resources
```java
// 자동 close (권장)
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
    // 사용...
} // 자동으로 close()
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 01. File 기본](./IO-01-File.md) | [다음: 03. 바이트 스트림 →](./IO-03-Binary.md)**

</div>
