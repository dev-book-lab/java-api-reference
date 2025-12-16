# IO 03. 바이트 스트림

> 바이너리 파일 처리 - InputStream/OutputStream 완전 정복  
> Java API Reference

---

## 📑 목차

1. [바이트 스트림 기본](#1-바이트-스트림-기본)
2. [파일 입출력](#2-파일-입출력)
3. [데이터 스트림](#3-데이터-스트림)
4. [객체 직렬화](#4-객체-직렬화)
5. [실전 연습 문제](#5-실전-연습-문제)

---

## 1. 바이트 스트림 기본

### 1.1 InputStream/OutputStream 계층

```java
/*
바이트 스트림 계층:

InputStream (추상)
├── FileInputStream
├── BufferedInputStream
├── DataInputStream
├── ObjectInputStream
└── ByteArrayInputStream

OutputStream (추상)
├── FileOutputStream
├── BufferedOutputStream
├── DataOutputStream
├── ObjectOutputStream
└── ByteArrayOutputStream
*/

public class ByteStreamHierarchy {
    public static void main(String[] args) {
        System.out.println("=== 바이트 vs 문자 스트림 ===");
        System.out.println("바이트 스트림: 바이너리 데이터 (이미지, 영상, 압축 등)");
        System.out.println("문자 스트림: 텍스트 데이터");
        
        System.out.println("\n=== 주요 클래스 ===");
        System.out.println("FileInputStream/FileOutputStream: 파일");
        System.out.println("BufferedInputStream/BufferedOutputStream: 버퍼링");
        System.out.println("DataInputStream/DataOutputStream: 기본 타입");
        System.out.println("ObjectInputStream/ObjectOutputStream: 객체");
    }
}
```

### 1.2 바이트 읽기/쓰기

```java
import java.io.*;

public class ByteReadWrite {
    public static void main(String[] args) {
        // 바이트 읽기
        try (FileInputStream fis = new FileInputStream("data.bin")) {
            
            int byteData;
            while ((byteData = fis.read()) != -1) {
                System.out.print(byteData + " ");
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 바이트 쓰기
        try (FileOutputStream fos = new FileOutputStream("data.bin")) {
            
            fos.write(65);  // 'A'
            fos.write(66);  // 'B'
            fos.write(67);  // 'C'
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 배열로 읽기
        try (FileInputStream fis = new FileInputStream("data.bin")) {
            
            byte[] buffer = new byte[1024];
            int len;
            
            while ((len = fis.read(buffer)) != -1) {
                // buffer[0] ~ buffer[len-1] 처리
                System.out.println("Read " + len + " bytes");
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 배열로 쓰기
        try (FileOutputStream fos = new FileOutputStream("data.bin")) {
            
            byte[] data = {65, 66, 67, 68, 69};
            fos.write(data);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 2. 파일 입출력

### 2.1 파일 복사

```java
public class FileCopy {
    public static void main(String[] args) {
        // 기본 복사
        try (
            FileInputStream fis = new FileInputStream("source.jpg");
            FileOutputStream fos = new FileOutputStream("dest.jpg")
        ) {
            int byteData;
            
            while ((byteData = fis.read()) != -1) {
                fos.write(byteData);
            }
            
            System.out.println("복사 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 버퍼 사용 (빠름)
        try (
            FileInputStream fis = new FileInputStream("source.jpg");
            FileOutputStream fos = new FileOutputStream("dest.jpg")
        ) {
            byte[] buffer = new byte[8192];  // 8KB 버퍼
            int len;
            
            while ((len = fis.read(buffer)) != -1) {
                fos.write(buffer, 0, len);
            }
            
            System.out.println("복사 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 2.2 BufferedStream (권장)

```java
public class BufferedStreamDemo {
    public static void main(String[] args) {
        // BufferedInputStream/OutputStream 사용
        try (
            BufferedInputStream bis = new BufferedInputStream(
                new FileInputStream("source.jpg"));
            BufferedOutputStream bos = new BufferedOutputStream(
                new FileOutputStream("dest.jpg"))
        ) {
            byte[] buffer = new byte[8192];
            int len;
            
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
            }
            
            System.out.println("복사 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 성능 비교
        long start, time1, time2;
        
        // 버퍼 없이
        start = System.currentTimeMillis();
        copyWithoutBuffer("large.dat", "copy1.dat");
        time1 = System.currentTimeMillis() - start;
        
        // 버퍼 사용
        start = System.currentTimeMillis();
        copyWithBuffer("large.dat", "copy2.dat");
        time2 = System.currentTimeMillis() - start;
        
        System.out.println("Without buffer: " + time1 + "ms");
        System.out.println("With buffer: " + time2 + "ms");
        System.out.println("Speed up: " + (time1 / (double) time2) + "x");
    }
    
    static void copyWithoutBuffer(String src, String dest) {
        try (
            FileInputStream fis = new FileInputStream(src);
            FileOutputStream fos = new FileOutputStream(dest)
        ) {
            int b;
            while ((b = fis.read()) != -1) {
                fos.write(b);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    static void copyWithBuffer(String src, String dest) {
        try (
            BufferedInputStream bis = new BufferedInputStream(
                new FileInputStream(src));
            BufferedOutputStream bos = new BufferedOutputStream(
                new FileOutputStream(dest))
        ) {
            byte[] buffer = new byte[8192];
            int len;
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
            }
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 2.3 Files.copy (간단)

```java
import java.nio.file.*;

public class FilesCopyDemo {
    public static void main(String[] args) {
        try {
            // 파일 복사
            Path source = Paths.get("source.jpg");
            Path dest = Paths.get("dest.jpg");
            
            Files.copy(source, dest, StandardCopyOption.REPLACE_EXISTING);
            
            System.out.println("복사 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // InputStream에서 파일로
        try (InputStream is = new FileInputStream("data.dat")) {
            
            Files.copy(is, Paths.get("output.dat"), 
                      StandardCopyOption.REPLACE_EXISTING);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 파일에서 OutputStream으로
        try (OutputStream os = new FileOutputStream("output.dat")) {
            
            Files.copy(Paths.get("data.dat"), os);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

---

## 3. 데이터 스트림

### 3.1 DataInputStream/DataOutputStream

```java
public class DataStreamDemo {
    public static void main(String[] args) {
        // 데이터 쓰기
        try (DataOutputStream dos = new DataOutputStream(
                new FileOutputStream("data.dat"))) {
            
            dos.writeInt(100);
            dos.writeDouble(3.14);
            dos.writeBoolean(true);
            dos.writeUTF("Hello");  // 문자열
            
            System.out.println("쓰기 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 데이터 읽기 (쓴 순서대로!)
        try (DataInputStream dis = new DataInputStream(
                new FileInputStream("data.dat"))) {
            
            int intValue = dis.readInt();
            double doubleValue = dis.readDouble();
            boolean boolValue = dis.readBoolean();
            String strValue = dis.readUTF();
            
            System.out.println("Int: " + intValue);
            System.out.println("Double: " + doubleValue);
            System.out.println("Boolean: " + boolValue);
            System.out.println("String: " + strValue);
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 3.2 구조화된 데이터 저장

```java
public class StructuredData {
    public static void main(String[] args) {
        // 학생 데이터 쓰기
        try (DataOutputStream dos = new DataOutputStream(
                new BufferedOutputStream(
                    new FileOutputStream("students.dat")))) {
            
            // 학생 3명
            writeStudent(dos, "Alice", 20, 3.8);
            writeStudent(dos, "Bob", 22, 3.5);
            writeStudent(dos, "Charlie", 21, 3.9);
            
            System.out.println("저장 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 학생 데이터 읽기
        try (DataInputStream dis = new DataInputStream(
                new BufferedInputStream(
                    new FileInputStream("students.dat")))) {
            
            while (dis.available() > 0) {
                String name = dis.readUTF();
                int age = dis.readInt();
                double gpa = dis.readDouble();
                
                System.out.println(name + ", " + age + ", " + gpa);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    static void writeStudent(DataOutputStream dos, 
                            String name, int age, double gpa) 
                            throws IOException {
        dos.writeUTF(name);
        dos.writeInt(age);
        dos.writeDouble(gpa);
    }
}
```

---

## 4. 객체 직렬화

### 4.1 Serializable 인터페이스

```java
import java.io.*;

// Serializable 구현
class Student implements Serializable {
    private static final long serialVersionUID = 1L;
    
    String name;
    int age;
    double gpa;
    
    // transient: 직렬화 제외
    transient String password;
    
    public Student(String name, int age, double gpa) {
        this.name = name;
        this.age = age;
        this.gpa = gpa;
    }
    
    @Override
    public String toString() {
        return "Student{name='" + name + "', age=" + age + ", gpa=" + gpa + "}";
    }
}

public class SerializableDemo {
    public static void main(String[] args) {
        Student student = new Student("Alice", 20, 3.8);
        
        // 객체 저장 (직렬화)
        try (ObjectOutputStream oos = new ObjectOutputStream(
                new FileOutputStream("student.ser"))) {
            
            oos.writeObject(student);
            System.out.println("직렬화 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 객체 읽기 (역직렬화)
        try (ObjectInputStream ois = new ObjectInputStream(
                new FileInputStream("student.ser"))) {
            
            Student loaded = (Student) ois.readObject();
            System.out.println("역직렬화: " + loaded);
            
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

### 4.2 여러 객체 저장

```java
public class MultipleObjects {
    public static void main(String[] args) {
        // 여러 객체 저장
        List<Student> students = Arrays.asList(
            new Student("Alice", 20, 3.8),
            new Student("Bob", 22, 3.5),
            new Student("Charlie", 21, 3.9)
        );
        
        try (ObjectOutputStream oos = new ObjectOutputStream(
                new FileOutputStream("students.ser"))) {
            
            oos.writeObject(students);
            System.out.println("저장 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 여러 객체 읽기
        try (ObjectInputStream ois = new ObjectInputStream(
                new FileInputStream("students.ser"))) {
            
            @SuppressWarnings("unchecked")
            List<Student> loaded = (List<Student>) ois.readObject();
            
            System.out.println("=== 로드된 학생들 ===");
            loaded.forEach(System.out::println);
            
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        }
    }
}
```

### 4.3 커스텀 직렬화

```java
class CustomStudent implements Serializable {
    private static final long serialVersionUID = 1L;
    
    String name;
    int age;
    transient double gpa;  // 직렬화 제외
    
    public CustomStudent(String name, int age, double gpa) {
        this.name = name;
        this.age = age;
        this.gpa = gpa;
    }
    
    // 커스텀 직렬화
    private void writeObject(ObjectOutputStream oos) throws IOException {
        oos.defaultWriteObject();  // 기본 필드 (name, age)
        
        // gpa는 암호화해서 저장
        String encrypted = encrypt(String.valueOf(gpa));
        oos.writeObject(encrypted);
    }
    
    // 커스텀 역직렬화
    private void readObject(ObjectInputStream ois) 
            throws IOException, ClassNotFoundException {
        ois.defaultReadObject();  // 기본 필드
        
        // gpa 복호화
        String encrypted = (String) ois.readObject();
        this.gpa = Double.parseDouble(decrypt(encrypted));
    }
    
    private String encrypt(String data) {
        // 간단한 예시 (실제로는 암호화 알고리즘 사용)
        return "encrypted_" + data;
    }
    
    private String decrypt(String encrypted) {
        return encrypted.replace("encrypted_", "");
    }
    
    @Override
    public String toString() {
        return "CustomStudent{name='" + name + "', age=" + age + ", gpa=" + gpa + "}";
    }
}
```

### 4.4 직렬화 주의사항

```java
public class SerializationCautions {
    public static void main(String[] args) {
        System.out.println("=== 직렬화 주의사항 ===");
        
        System.out.println("\n1. serialVersionUID");
        System.out.println("   - 클래스 버전 관리");
        System.out.println("   - 명시하지 않으면 자동 생성 (변경 시 문제)");
        
        System.out.println("\n2. transient");
        System.out.println("   - 직렬화에서 제외할 필드");
        System.out.println("   - 비밀번호, 계산 가능한 값 등");
        
        System.out.println("\n3. static 필드");
        System.out.println("   - 직렬화되지 않음 (클래스에 속함)");
        
        System.out.println("\n4. 부모 클래스");
        System.out.println("   - 부모도 Serializable이어야 함");
        System.out.println("   - 아니면 기본 생성자 필요");
        
        System.out.println("\n5. 성능");
        System.out.println("   - JSON, XML보다 빠름");
        System.out.println("   - 하지만 Java 전용 (호환성 낮음)");
    }
}
```

---

## 5. 실전 연습 문제

### 문제 1: 이미지 크기 변경 후 저장

```java
// 이미지 파일을 읽어서 품질을 낮춰 저장 (간단 버전)
public class Problem1 {
    public static void compressImage(String input, String output) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        compressImage("image.jpg", "compressed.jpg");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
import javax.imageio.ImageIO;
import java.awt.image.BufferedImage;

public class Problem1 {
    public static void compressImage(String input, String output) {
        try {
            // 이미지 읽기
            BufferedImage image = ImageIO.read(new File(input));
            
            // 크기 조정 (50%)
            int newWidth = image.getWidth() / 2;
            int newHeight = image.getHeight() / 2;
            
            BufferedImage resized = new BufferedImage(
                newWidth, newHeight, image.getType());
            
            Graphics2D g = resized.createGraphics();
            g.drawImage(image, 0, 0, newWidth, newHeight, null);
            g.dispose();
            
            // 저장
            ImageIO.write(resized, "jpg", new File(output));
            
            System.out.println("압축 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    // 간단 복사 버전 (실제 압축은 아님)
    public static void copyImage(String input, String output) {
        try (
            BufferedInputStream bis = new BufferedInputStream(
                new FileInputStream(input));
            BufferedOutputStream bos = new BufferedOutputStream(
                new FileOutputStream(output))
        ) {
            byte[] buffer = new byte[8192];
            int len;
            
            while ((len = bis.read(buffer)) != -1) {
                bos.write(buffer, 0, len);
            }
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```
</details>

---

### 문제 2: 설정 파일 저장/로드

```java
// Properties 객체를 직렬화로 저장
class Config implements Serializable {
    String dbUrl;
    String username;
    int maxConnections;
}

public class Problem2 {
    public static void saveConfig(Config config, String filename) {
        // 여기에 코드 작성
    }
    
    public static Config loadConfig(String filename) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        Config config = new Config();
        config.dbUrl = "jdbc:mysql://localhost/mydb";
        config.username = "admin";
        config.maxConnections = 10;
        
        saveConfig(config, "config.ser");
        Config loaded = loadConfig("config.ser");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
class Config implements Serializable {
    private static final long serialVersionUID = 1L;
    
    String dbUrl;
    String username;
    int maxConnections;
    
    @Override
    public String toString() {
        return "Config{dbUrl='" + dbUrl + "', username='" + username + 
               "', maxConnections=" + maxConnections + "}";
    }
}

public class Problem2 {
    public static void saveConfig(Config config, String filename) {
        try (ObjectOutputStream oos = new ObjectOutputStream(
                new FileOutputStream(filename))) {
            
            oos.writeObject(config);
            System.out.println("설정 저장 완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    public static Config loadConfig(String filename) {
        try (ObjectInputStream ois = new ObjectInputStream(
                new FileInputStream(filename))) {
            
            Config config = (Config) ois.readObject();
            System.out.println("설정 로드 완료!");
            return config;
            
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
            return null;
        }
    }
}
```
</details>

---

### 문제 3: 파일 암호화

```java
// XOR 암호화로 파일 암호화/복호화
public class Problem3 {
    public static void encryptFile(String input, String output, byte key) {
        // 여기에 코드 작성
    }
    
    public static void main(String[] args) {
        byte key = 0x5A;
        
        encryptFile("plain.txt", "encrypted.bin", key);
        encryptFile("encrypted.bin", "decrypted.txt", key);  // 복호화
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static void encryptFile(String input, String output, byte key) {
        try (
            BufferedInputStream bis = new BufferedInputStream(
                new FileInputStream(input));
            BufferedOutputStream bos = new BufferedOutputStream(
                new FileOutputStream(output))
        ) {
            int data;
            
            while ((data = bis.read()) != -1) {
                // XOR 암호화
                int encrypted = data ^ key;
                bos.write(encrypted);
            }
            
            System.out.println("완료!");
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    
    // 배열 버전 (더 빠름)
    public static void encryptFileBuffer(String input, String output, byte key) {
        try (
            BufferedInputStream bis = new BufferedInputStream(
                new FileInputStream(input));
            BufferedOutputStream bos = new BufferedOutputStream(
                new FileOutputStream(output))
        ) {
            byte[] buffer = new byte[8192];
            int len;
            
            while ((len = bis.read(buffer)) != -1) {
                // 버퍼 암호화
                for (int i = 0; i < len; i++) {
                    buffer[i] ^= key;
                }
                
                bos.write(buffer, 0, len);
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

### 바이트 스트림 기본
```java
// 읽기
FileInputStream fis = new FileInputStream("data.bin");
int b = fis.read();  // 1바이트
byte[] buffer = new byte[1024];
int len = fis.read(buffer);  // 배열로

// 쓰기
FileOutputStream fos = new FileOutputStream("data.bin");
fos.write(65);  // 1바이트
fos.write(buffer);  // 배열로
```

### 버퍼링 (권장)
```java
BufferedInputStream bis = new BufferedInputStream(
    new FileInputStream("data.bin"));

BufferedOutputStream bos = new BufferedOutputStream(
    new FileOutputStream("data.bin"));
```

### 데이터 타입
```java
DataOutputStream dos = new DataOutputStream(...);
dos.writeInt(100);
dos.writeDouble(3.14);
dos.writeUTF("Hello");

DataInputStream dis = new DataInputStream(...);
int i = dis.readInt();
double d = dis.readDouble();
String s = dis.readUTF();
```

### 객체 직렬화
```java
// 직렬화
ObjectOutputStream oos = new ObjectOutputStream(...);
oos.writeObject(object);

// 역직렬화
ObjectInputStream ois = new ObjectInputStream(...);
MyClass obj = (MyClass) ois.readObject();

// Serializable 필수
class MyClass implements Serializable {
    private static final long serialVersionUID = 1L;
    transient String password;  // 제외
}
```

### 파일 복사
```java
// 간단
Files.copy(source, dest, StandardCopyOption.REPLACE_EXISTING);

// 수동 (버퍼 사용)
try (
    BufferedInputStream bis = ...;
    BufferedOutputStream bos = ...
) {
    byte[] buffer = new byte[8192];
    int len;
    while ((len = bis.read(buffer)) != -1) {
        bos.write(buffer, 0, len);
    }
}
```

---

## 🎉 IO 시리즈 완료!

<div align="center">

### 📚 전체 시리즈

|              Chapter              | 주제 |
|:---------------------------------:|------|
|  [01. File 기본](./IO-01-File.md)   | File 클래스, 경로, 디렉토리 |
|   [02. 텍스트 파일](./IO-02-Text.md)   | Reader/Writer, 인코딩 |
| [03. 바이트 스트림](./IO-03-Binary.md)  | Stream, 직렬화, 복사 |

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 02. 텍스트 파일 입출력](./IO-02-Text.md)**

</div>
