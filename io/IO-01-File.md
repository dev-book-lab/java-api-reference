# IO 01. File 기본

> 파일 시스템 다루기 - java.io.File과 NIO Path  
> Java API Reference

---

## 📑 목차

1. [File 클래스 기본](#1-file-클래스-기본)
2. [파일 정보 조회](#2-파일-정보-조회)
3. [파일/디렉토리 생성과 삭제](#3-파일디렉토리-생성과-삭제)
4. [디렉토리 탐색](#4-디렉토리-탐색)
5. [NIO Path와 Files](#5-nio-path와-files)
6. [실전 연습 문제](#6-실전-연습-문제)

---

## 1. File 클래스 기본

### 1.1 File 객체 생성

```java
import java.io.File;

public class FileBasic {
    public static void main(String[] args) {
        // 파일 경로로 생성
        File file1 = new File("test.txt");
        File file2 = new File("C:/temp/data.txt");  // Windows
        File file3 = new File("/usr/local/data.txt");  // Unix/Linux
        
        System.out.println("file1: " + file1);
        System.out.println("file2: " + file2);
        
        
        // 부모 디렉토리 + 파일명
        File parent = new File("C:/temp");
        File file4 = new File(parent, "data.txt");
        
        System.out.println("file4: " + file4);
        
        
        // 부모 경로 문자열 + 파일명
        File file5 = new File("C:/temp", "data.txt");
        
        
        // 구분자
        System.out.println("\n=== 구분자 ===");
        System.out.println("separator: " + File.separator);  // \ or /
        System.out.println("pathSeparator: " + File.pathSeparator);  // ; or :
        
        
        // 플랫폼 독립적 경로
        File file6 = new File("data" + File.separator + "test.txt");
        System.out.println("Platform independent: " + file6);
    }
}
```

### 1.2 경로 표현

```java
public class FilePaths {
    public static void main(String[] args) {
        File file = new File("test.txt");
        
        // 경로 정보
        System.out.println("getName: " + file.getName());  // test.txt
        System.out.println("getPath: " + file.getPath());  // test.txt
        
        
        // 절대 경로
        System.out.println("getAbsolutePath: " + file.getAbsolutePath());
        // C:\Users\...\test.txt
        
        File absoluteFile = file.getAbsoluteFile();
        System.out.println("getAbsoluteFile: " + absoluteFile);
        
        
        // 정규 경로 (canonical path)
        try {
            System.out.println("getCanonicalPath: " + file.getCanonicalPath());
            // 상대 경로 해석, 심볼릭 링크 해석
        } catch (Exception e) {
            e.printStackTrace();
        }
        
        
        // 부모 디렉토리
        File file2 = new File("data/test.txt");
        System.out.println("\ngetParent: " + file2.getParent());  // data
        
        File parentFile = file2.getParentFile();
        System.out.println("getParentFile: " + parentFile);
    }
}
```

---

## 2. 파일 정보 조회

### 2.1 존재 여부와 타입

```java
public class FileInfo {
    public static void main(String[] args) {
        File file = new File("test.txt");
        File dir = new File("data");
        
        // 존재 여부
        System.out.println("exists: " + file.exists());
        
        
        // 파일인지 디렉토리인지
        System.out.println("isFile: " + file.isFile());
        System.out.println("isDirectory: " + file.isDirectory());
        
        
        // 숨김 파일
        System.out.println("isHidden: " + file.isHidden());
        
        
        // 절대 경로 여부
        System.out.println("isAbsolute: " + file.isAbsolute());
        
        
        // 실전 예제
        File target = new File("data");
        
        if (target.exists()) {
            if (target.isFile()) {
                System.out.println(target.getName() + "는 파일입니다.");
            } else if (target.isDirectory()) {
                System.out.println(target.getName() + "는 디렉토리입니다.");
            }
        } else {
            System.out.println(target.getName() + "가 존재하지 않습니다.");
        }
    }
}
```

### 2.2 권한 확인

```java
public class FilePermissions {
    public static void main(String[] args) {
        File file = new File("test.txt");
        
        // 읽기 권한
        System.out.println("canRead: " + file.canRead());
        
        
        // 쓰기 권한
        System.out.println("canWrite: " + file.canWrite());
        
        
        // 실행 권한
        System.out.println("canExecute: " + file.canExecute());
        
        
        // 권한 설정
        boolean readSuccess = file.setReadable(true);
        boolean writeSuccess = file.setWritable(true);
        boolean executeSuccess = file.setExecutable(true);
        
        System.out.println("\nSetReadable: " + readSuccess);
        System.out.println("SetWritable: " + writeSuccess);
        System.out.println("SetExecutable: " + executeSuccess);
        
        
        // 읽기 전용 설정
        file.setReadOnly();
        System.out.println("\nAfter setReadOnly:");
        System.out.println("canWrite: " + file.canWrite());  // false
    }
}
```

### 2.3 파일 크기와 시간

```java
import java.util.Date;

public class FileSizeAndTime {
    public static void main(String[] args) {
        File file = new File("test.txt");
        
        if (file.exists()) {
            // 파일 크기 (바이트)
            long size = file.length();
            System.out.println("Size: " + size + " bytes");
            
            // 읽기 쉬운 형태
            System.out.println("Size: " + formatSize(size));
            
            
            // 마지막 수정 시간 (밀리초)
            long lastModified = file.lastModified();
            System.out.println("Last modified: " + lastModified);
            
            // Date로 변환
            Date date = new Date(lastModified);
            System.out.println("Date: " + date);
            
            
            // 수정 시간 변경
            long newTime = System.currentTimeMillis();
            boolean success = file.setLastModified(newTime);
            System.out.println("setLastModified: " + success);
        }
    }
    
    // 파일 크기를 읽기 쉬운 형태로
    public static String formatSize(long size) {
        if (size < 1024) {
            return size + " B";
        } else if (size < 1024 * 1024) {
            return String.format("%.2f KB", size / 1024.0);
        } else if (size < 1024 * 1024 * 1024) {
            return String.format("%.2f MB", size / (1024.0 * 1024));
        } else {
            return String.format("%.2f GB", size / (1024.0 * 1024 * 1024));
        }
    }
}
```

---

## 3. 파일/디렉토리 생성과 삭제

### 3.1 파일 생성

```java
import java.io.IOException;

public class FileCreation {
    public static void main(String[] args) {
        // 새 파일 생성
        File file = new File("newfile.txt");
        
        try {
            boolean created = file.createNewFile();
            
            if (created) {
                System.out.println("파일 생성 성공!");
            } else {
                System.out.println("파일이 이미 존재합니다.");
            }
        } catch (IOException e) {
            System.out.println("파일 생성 실패: " + e.getMessage());
        }
        
        
        // 임시 파일 생성
        try {
            File tempFile = File.createTempFile("temp", ".txt");
            System.out.println("\nTemp file: " + tempFile.getAbsolutePath());
            
            // 프로그램 종료 시 삭제
            tempFile.deleteOnExit();
            
        } catch (IOException e) {
            e.printStackTrace();
        }
        
        
        // 특정 디렉토리에 임시 파일
        try {
            File tempDir = new File("temp");
            tempDir.mkdir();
            
            File tempFile2 = File.createTempFile("data", ".tmp", tempDir);
            System.out.println("Temp file 2: " + tempFile2.getName());
            
            tempFile2.deleteOnExit();
            
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

### 3.2 디렉토리 생성

```java
public class DirectoryCreation {
    public static void main(String[] args) {
        // 단일 디렉토리 생성
        File dir1 = new File("mydir");
        
        if (dir1.mkdir()) {
            System.out.println("디렉토리 생성 성공!");
        } else {
            System.out.println("디렉토리 생성 실패 (이미 존재하거나 부모 디렉토리 없음)");
        }
        
        
        // 중첩 디렉토리 생성
        File dir2 = new File("parent/child/grandchild");
        
        if (dir2.mkdirs()) {
            System.out.println("중첩 디렉토리 생성 성공!");
        } else {
            System.out.println("디렉토리 생성 실패");
        }
        
        
        // 실전 예제: 안전한 디렉토리 생성
        File targetDir = new File("data/backup");
        
        if (!targetDir.exists()) {
            if (targetDir.mkdirs()) {
                System.out.println("디렉토리 생성: " + targetDir.getAbsolutePath());
            } else {
                System.out.println("디렉토리 생성 실패!");
            }
        } else {
            System.out.println("디렉토리가 이미 존재합니다.");
        }
    }
}
```

### 3.3 삭제와 이름 변경

```java
public class FileOperations {
    public static void main(String[] args) {
        // 파일 삭제
        File file = new File("test.txt");
        
        if (file.exists()) {
            if (file.delete()) {
                System.out.println("파일 삭제 성공!");
            } else {
                System.out.println("파일 삭제 실패!");
            }
        }
        
        
        // 디렉토리 삭제 (비어있어야 함!)
        File dir = new File("mydir");
        
        if (dir.exists() && dir.isDirectory()) {
            if (dir.delete()) {
                System.out.println("디렉토리 삭제 성공!");
            } else {
                System.out.println("디렉토리 삭제 실패! (비어있지 않을 수 있음)");
            }
        }
        
        
        // 이름 변경 / 이동
        File oldFile = new File("old.txt");
        File newFile = new File("new.txt");
        
        if (oldFile.exists()) {
            if (oldFile.renameTo(newFile)) {
                System.out.println("이름 변경 성공!");
            } else {
                System.out.println("이름 변경 실패!");
            }
        }
        
        
        // 다른 디렉토리로 이동
        File source = new File("data.txt");
        File dest = new File("backup/data.txt");
        
        if (source.renameTo(dest)) {
            System.out.println("파일 이동 성공!");
        }
    }
}
```

---

## 4. 디렉토리 탐색

### 4.1 파일 목록

```java
public class DirectoryListing {
    public static void main(String[] args) {
        File dir = new File(".");
        
        // 파일명 배열
        String[] files = dir.list();
        
        if (files != null) {
            System.out.println("=== 파일 목록 ===");
            for (String file : files) {
                System.out.println(file);
            }
        }
        
        
        // File 객체 배열
        File[] fileObjects = dir.listFiles();
        
        if (fileObjects != null) {
            System.out.println("\n=== 파일 상세 정보 ===");
            for (File file : fileObjects) {
                String type = file.isDirectory() ? "[DIR]" : "[FILE]";
                System.out.println(type + " " + file.getName());
            }
        }
    }
}
```

### 4.2 필터링

```java
import java.io.FilenameFilter;
import java.io.FileFilter;

public class FileFiltering {
    public static void main(String[] args) {
        File dir = new File(".");
        
        // FilenameFilter: .txt 파일만
        FilenameFilter txtFilter = new FilenameFilter() {
            @Override
            public boolean accept(File dir, String name) {
                return name.endsWith(".txt");
            }
        };
        
        String[] txtFiles = dir.list(txtFilter);
        System.out.println("=== .txt 파일 ===");
        for (String file : txtFiles) {
            System.out.println(file);
        }
        
        
        // Lambda 사용
        String[] javaFiles = dir.list((d, name) -> name.endsWith(".java"));
        System.out.println("\n=== .java 파일 ===");
        for (String file : javaFiles) {
            System.out.println(file);
        }
        
        
        // FileFilter: 디렉토리만
        FileFilter dirFilter = new FileFilter() {
            @Override
            public boolean accept(File file) {
                return file.isDirectory();
            }
        };
        
        File[] dirs = dir.listFiles(dirFilter);
        System.out.println("\n=== 디렉토리 ===");
        for (File d : dirs) {
            System.out.println(d.getName());
        }
        
        
        // Lambda 사용
        File[] largeFiles = dir.listFiles(f -> f.isFile() && f.length() > 1024);
        System.out.println("\n=== 1KB 이상 파일 ===");
        for (File f : largeFiles) {
            System.out.println(f.getName() + " (" + f.length() + " bytes)");
        }
    }
}
```

### 4.3 재귀적 탐색

```java
public class RecursiveTraversal {
    public static void main(String[] args) {
        File dir = new File(".");
        
        System.out.println("=== 전체 파일 트리 ===");
        printTree(dir, 0);
    }
    
    // 재귀적으로 디렉토리 출력
    public static void printTree(File file, int depth) {
        // 들여쓰기
        String indent = "  ".repeat(depth);
        
        if (file.isDirectory()) {
            System.out.println(indent + "[DIR] " + file.getName());
            
            File[] files = file.listFiles();
            if (files != null) {
                for (File f : files) {
                    printTree(f, depth + 1);
                }
            }
        } else {
            System.out.println(indent + file.getName());
        }
    }
    
    // 파일 검색
    public static void searchFiles(File dir, String extension) {
        File[] files = dir.listFiles();
        
        if (files != null) {
            for (File file : files) {
                if (file.isDirectory()) {
                    searchFiles(file, extension);  // 재귀
                } else if (file.getName().endsWith(extension)) {
                    System.out.println(file.getAbsolutePath());
                }
            }
        }
    }
}
```

---

## 5. NIO Path와 Files

### 5.1 Path 기본

```java
import java.nio.file.*;

public class PathBasic {
    public static void main(String[] args) {
        // Path 생성
        Path path1 = Paths.get("test.txt");
        Path path2 = Paths.get("data", "files", "test.txt");
        Path path3 = Paths.get("C:/temp/data.txt");
        
        System.out.println("path1: " + path1);
        System.out.println("path2: " + path2);
        
        
        // File ↔ Path 변환
        File file = new File("test.txt");
        Path pathFromFile = file.toPath();
        
        Path path = Paths.get("data.txt");
        File fileFromPath = path.toFile();
        
        
        // Path 정보
        System.out.println("\n=== Path 정보 ===");
        System.out.println("getFileName: " + path2.getFileName());
        System.out.println("getParent: " + path2.getParent());
        System.out.println("getRoot: " + path2.getRoot());
        System.out.println("getNameCount: " + path2.getNameCount());
        
        
        // 경로 구성 요소
        for (int i = 0; i < path2.getNameCount(); i++) {
            System.out.println("getName(" + i + "): " + path2.getName(i));
        }
        
        
        // 절대 경로
        Path absolute = path1.toAbsolutePath();
        System.out.println("\nAbsolute: " + absolute);
        
        
        // 정규 경로
        try {
            Path real = path1.toRealPath();
            System.out.println("Real path: " + real);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 5.2 Files 유틸리티

```java
import java.nio.file.*;

public class FilesUtility {
    public static void main(String[] args) {
        Path path = Paths.get("test.txt");
        
        try {
            // 존재 여부
            boolean exists = Files.exists(path);
            System.out.println("Exists: " + exists);
            
            
            // 파일/디렉토리 확인
            boolean isRegularFile = Files.isRegularFile(path);
            boolean isDirectory = Files.isDirectory(path);
            boolean isSymbolicLink = Files.isSymbolicLink(path);
            
            System.out.println("Is file: " + isRegularFile);
            System.out.println("Is directory: " + isDirectory);
            
            
            // 권한 확인
            boolean readable = Files.isReadable(path);
            boolean writable = Files.isWritable(path);
            boolean executable = Files.isExecutable(path);
            
            
            // 파일 크기
            if (exists) {
                long size = Files.size(path);
                System.out.println("Size: " + size + " bytes");
            }
            
            
            // 파일 생성
            Path newFile = Paths.get("newfile.txt");
            Files.createFile(newFile);
            System.out.println("File created!");
            
            
            // 디렉토리 생성
            Path newDir = Paths.get("newdir");
            Files.createDirectory(newDir);
            
            Path nestedDir = Paths.get("parent/child/grandchild");
            Files.createDirectories(nestedDir);
            
            
            // 삭제
            Files.delete(newFile);
            Files.deleteIfExists(newFile);  // 없어도 예외 안 남
            
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

### 5.3 파일 복사와 이동

```java
public class FilesCopyMove {
    public static void main(String[] args) {
        try {
            Path source = Paths.get("source.txt");
            Path target = Paths.get("target.txt");
            
            // 파일 복사
            Files.copy(source, target);
            System.out.println("복사 완료!");
            
            
            // 덮어쓰기 옵션
            Files.copy(source, target, StandardCopyOption.REPLACE_EXISTING);
            
            
            // 파일 이동
            Path dest = Paths.get("backup/source.txt");
            Files.move(source, dest);
            System.out.println("이동 완료!");
            
            
            // 원자적 이동 (가능한 경우)
            Files.move(source, dest, StandardCopyOption.ATOMIC_MOVE);
            
            
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}
```

---

## 6. 실전 연습 문제

### 문제 1: 디렉토리 크기 계산

```java
// 디렉토리의 전체 크기 계산 (재귀)
public class Problem1 {
    public static long getDirectorySize(File directory) {
        // 여기에 코드 작성
        return 0;
    }
    
    public static void main(String[] args) {
        File dir = new File(".");
        long size = getDirectorySize(dir);
        System.out.println("Total size: " + size + " bytes");
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem1 {
    public static long getDirectorySize(File directory) {
        if (!directory.exists() || !directory.isDirectory()) {
            return 0;
        }
        
        long size = 0;
        File[] files = directory.listFiles();
        
        if (files != null) {
            for (File file : files) {
                if (file.isDirectory()) {
                    size += getDirectorySize(file);  // 재귀
                } else {
                    size += file.length();
                }
            }
        }
        
        return size;
    }
    
    // 읽기 쉬운 형태로
    public static String formatSize(long size) {
        if (size < 1024) return size + " B";
        if (size < 1024 * 1024) return String.format("%.2f KB", size / 1024.0);
        if (size < 1024 * 1024 * 1024) return String.format("%.2f MB", size / (1024.0 * 1024));
        return String.format("%.2f GB", size / (1024.0 * 1024 * 1024));
    }
}
```
</details>

---

### 문제 2: 확장자별 파일 개수

```java
// 디렉토리에서 확장자별 파일 개수 세기
public class Problem2 {
    public static Map<String, Integer> countByExtension(File directory) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        File dir = new File(".");
        Map<String, Integer> count = countByExtension(dir);
        
        count.forEach((ext, cnt) -> 
            System.out.println(ext + ": " + cnt));
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem2 {
    public static Map<String, Integer> countByExtension(File directory) {
        Map<String, Integer> map = new HashMap<>();
        
        if (!directory.exists() || !directory.isDirectory()) {
            return map;
        }
        
        File[] files = directory.listFiles();
        if (files != null) {
            for (File file : files) {
                if (file.isFile()) {
                    String name = file.getName();
                    int dotIndex = name.lastIndexOf('.');
                    
                    String ext = (dotIndex > 0) ? name.substring(dotIndex) : "(no extension)";
                    
                    map.put(ext, map.getOrDefault(ext, 0) + 1);
                }
            }
        }
        
        return map;
    }
    
    // 재귀 버전
    public static void countByExtensionRecursive(File directory, Map<String, Integer> map) {
        File[] files = directory.listFiles();
        
        if (files != null) {
            for (File file : files) {
                if (file.isDirectory()) {
                    countByExtensionRecursive(file, map);
                } else {
                    String name = file.getName();
                    int dotIndex = name.lastIndexOf('.');
                    String ext = (dotIndex > 0) ? name.substring(dotIndex) : "(no extension)";
                    map.put(ext, map.getOrDefault(ext, 0) + 1);
                }
            }
        }
    }
}
```
</details>

---

### 문제 3: 오래된 파일 찾기

```java
// N일 이전에 수정된 파일 찾기
public class Problem3 {
    public static List<File> findOldFiles(File directory, int days) {
        // 여기에 코드 작성
        return null;
    }
    
    public static void main(String[] args) {
        File dir = new File(".");
        List<File> oldFiles = findOldFiles(dir, 30);  // 30일 이전
        
        System.out.println("=== 30일 이전 파일 ===");
        for (File file : oldFiles) {
            System.out.println(file.getName());
        }
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
public class Problem3 {
    public static List<File> findOldFiles(File directory, int days) {
        List<File> result = new ArrayList<>();
        
        if (!directory.exists() || !directory.isDirectory()) {
            return result;
        }
        
        long threshold = System.currentTimeMillis() - (days * 24L * 60 * 60 * 1000);
        
        File[] files = directory.listFiles();
        if (files != null) {
            for (File file : files) {
                if (file.isFile() && file.lastModified() < threshold) {
                    result.add(file);
                }
            }
        }
        
        return result;
    }
    
    // 재귀 버전
    public static void findOldFilesRecursive(File directory, int days, List<File> result) {
        long threshold = System.currentTimeMillis() - (days * 24L * 60 * 60 * 1000);
        
        File[] files = directory.listFiles();
        if (files != null) {
            for (File file : files) {
                if (file.isDirectory()) {
                    findOldFilesRecursive(file, days, result);
                } else if (file.lastModified() < threshold) {
                    result.add(file);
                }
            }
        }
    }
}
```
</details>

---

## 📌 핵심 정리

### File 기본
```java
File file = new File("test.txt");
File dir = new File("data");
File path = new File(parent, "child.txt");

// 구분자
File.separator          // \ or /
File.pathSeparator      // ; or :
```

### 파일 정보
```java
file.exists()           // 존재 여부
file.isFile()           // 파일인지
file.isDirectory()      // 디렉토리인지
file.length()           // 크기
file.lastModified()     // 수정 시간
file.canRead()          // 읽기 권한
file.canWrite()         // 쓰기 권한
```

### 생성과 삭제
```java
file.createNewFile()    // 파일 생성
dir.mkdir()             // 디렉토리 생성
dir.mkdirs()            // 중첩 디렉토리 생성
file.delete()           // 삭제
file.renameTo(newFile)  // 이름 변경/이동
```

### 목록 조회
```java
dir.list()              // 파일명 배열
dir.listFiles()         // File 배열
dir.listFiles(filter)   // 필터링
```

### NIO Path & Files
```java
Path path = Paths.get("test.txt");
Files.exists(path)
Files.isRegularFile(path)
Files.size(path)
Files.copy(source, target)
Files.move(source, target)
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. 텍스트 파일 입출력 →](./IO-02-Text.md)**

</div>
