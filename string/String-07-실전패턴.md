# String 07. 실전 패턴

> 알고리즘과 실무에서 자주 쓰이는 String 패턴 모음  
> Java API Reference

---

## 📑 목차

1. [알고리즘 필수 패턴](#1-알고리즘-필수-패턴)
2. [데이터 검증 패턴](#2-데이터-검증-패턴)
3. [파싱 및 포맷팅](#3-파싱-및-포맷팅)
4. [텍스트 처리 패턴](#4-텍스트-처리-패턴)
5. [성능 최적화 패턴](#5-성능-최적화-패턴)

---

## 1. 알고리즘 필수 패턴

### 1.1 팰린드롬 확인

```java
public class PalindromePatterns {
    // 패턴 1: 기본 양쪽 포인터
    public static boolean isPalindrome1(String str) {
        int left = 0;
        int right = str.length() - 1;
        
        while (left < right) {
            if (str.charAt(left) != str.charAt(right)) {
                return false;
            }
            left++;
            right--;
        }
        
        return true;
    }
    
    // 패턴 2: StringBuilder.reverse() 활용
    public static boolean isPalindrome2(String str) {
        String reversed = new StringBuilder(str).reverse().toString();
        return str.equals(reversed);
    }
    
    // 패턴 3: 알파벳만 체크 (공백/특수문자 무시)
    public static boolean isPalindromeIgnoreNonAlpha(String str) {
        int left = 0;
        int right = str.length() - 1;
        
        while (left < right) {
            // 알파벳이 아니면 스킵
            while (left < right && !Character.isLetterOrDigit(str.charAt(left))) {
                left++;
            }
            while (left < right && !Character.isLetterOrDigit(str.charAt(right))) {
                right--;
            }
            
            // 대소문자 무시 비교
            if (Character.toLowerCase(str.charAt(left)) != 
                Character.toLowerCase(str.charAt(right))) {
                return false;
            }
            
            left++;
            right--;
        }
        
        return true;
    }
    
    public static void main(String[] args) {
        System.out.println(isPalindrome1("racecar"));  // true
        System.out.println(isPalindrome1("hello"));    // false
        
        System.out.println(isPalindromeIgnoreNonAlpha("A man, a plan, a canal: Panama"));
        // true (공백/구두점 무시)
    }
}
```

### 1.2 애너그램 확인

```java
public class AnagramPatterns {
    // 패턴 1: 정렬 후 비교
    public static boolean isAnagram1(String s1, String s2) {
        if (s1.length() != s2.length()) {
            return false;
        }
        
        char[] arr1 = s1.toCharArray();
        char[] arr2 = s2.toCharArray();
        
        Arrays.sort(arr1);
        Arrays.sort(arr2);
        
        return Arrays.equals(arr1, arr2);
    }
    
    // 패턴 2: 빈도수 카운트
    public static boolean isAnagram2(String s1, String s2) {
        if (s1.length() != s2.length()) {
            return false;
        }
        
        int[] count = new int[26];  // 소문자만 가정
        
        for (int i = 0; i < s1.length(); i++) {
            count[s1.charAt(i) - 'a']++;
            count[s2.charAt(i) - 'a']--;
        }
        
        for (int c : count) {
            if (c != 0) return false;
        }
        
        return true;
    }
    
    // 패턴 3: HashMap 사용 (유니코드 대응)
    public static boolean isAnagram3(String s1, String s2) {
        if (s1.length() != s2.length()) {
            return false;
        }
        
        Map<Character, Integer> map = new HashMap<>();
        
        for (char ch : s1.toCharArray()) {
            map.put(ch, map.getOrDefault(ch, 0) + 1);
        }
        
        for (char ch : s2.toCharArray()) {
            if (!map.containsKey(ch)) return false;
            map.put(ch, map.get(ch) - 1);
            if (map.get(ch) < 0) return false;
        }
        
        return true;
    }
    
    public static void main(String[] args) {
        System.out.println(isAnagram1("listen", "silent"));  // true
        System.out.println(isAnagram1("hello", "world"));    // false
    }
}
```

### 1.3 부분 문자열 찾기 (슬라이딩 윈도우)

```java
public class SubstringPatterns {
    // 패턴 1: 고정 길이 부분 문자열의 최대값
    public static String longestSubstring(String str, int k) {
        if (str.length() < k) return "";
        
        String max = str.substring(0, k);
        
        for (int i = 1; i <= str.length() - k; i++) {
            String current = str.substring(i, i + k);
            if (current.compareTo(max) > 0) {
                max = current;
            }
        }
        
        return max;
    }
    
    // 패턴 2: 중복 없는 가장 긴 부분 문자열
    public static int lengthOfLongestSubstring(String str) {
        Set<Character> set = new HashSet<>();
        int maxLen = 0;
        int left = 0;
        
        for (int right = 0; right < str.length(); right++) {
            while (set.contains(str.charAt(right))) {
                set.remove(str.charAt(left));
                left++;
            }
            set.add(str.charAt(right));
            maxLen = Math.max(maxLen, right - left + 1);
        }
        
        return maxLen;
    }
    
    public static void main(String[] args) {
        System.out.println(lengthOfLongestSubstring("abcabcbb"));  // 3 (abc)
        System.out.println(lengthOfLongestSubstring("bbbbb"));     // 1 (b)
    }
}
```

### 1.4 문자열 순열 생성

```java
public class PermutationPatterns {
    // 패턴 1: 재귀적 순열
    public static List<String> permute(String str) {
        List<String> result = new ArrayList<>();
        permuteHelper("", str, result);
        return result;
    }
    
    private static void permuteHelper(String prefix, String remaining, 
                                      List<String> result) {
        if (remaining.isEmpty()) {
            result.add(prefix);
            return;
        }
        
        for (int i = 0; i < remaining.length(); i++) {
            char ch = remaining.charAt(i);
            String newPrefix = prefix + ch;
            String newRemaining = remaining.substring(0, i) + 
                                 remaining.substring(i + 1);
            permuteHelper(newPrefix, newRemaining, result);
        }
    }
    
    // 패턴 2: 중복 제거 순열
    public static List<String> permuteUnique(String str) {
        List<String> result = new ArrayList<>();
        char[] chars = str.toCharArray();
        Arrays.sort(chars);  // 중복 처리를 위해 정렬
        boolean[] used = new boolean[chars.length];
        permuteUniqueHelper(chars, used, new StringBuilder(), result);
        return result;
    }
    
    private static void permuteUniqueHelper(char[] chars, boolean[] used, 
                                           StringBuilder current, 
                                           List<String> result) {
        if (current.length() == chars.length) {
            result.add(current.toString());
            return;
        }
        
        for (int i = 0; i < chars.length; i++) {
            if (used[i]) continue;
            // 중복 문자 스킵
            if (i > 0 && chars[i] == chars[i-1] && !used[i-1]) continue;
            
            used[i] = true;
            current.append(chars[i]);
            permuteUniqueHelper(chars, used, current, result);
            current.deleteCharAt(current.length() - 1);
            used[i] = false;
        }
    }
    
    public static void main(String[] args) {
        System.out.println(permute("abc"));
        // [abc, acb, bac, bca, cab, cba]
        
        System.out.println(permuteUnique("aab"));
        // [aab, aba, baa] (중복 제거)
    }
}
```

---

## 2. 데이터 검증 패턴

### 2.1 이메일 검증

```java
public class EmailValidation {
    // 패턴 1: 정규식 (간단)
    public static boolean isValidEmail1(String email) {
        String regex = "^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$";
        return email.matches(regex);
    }
    
    // 패턴 2: 정규식 (상세)
    public static boolean isValidEmail2(String email) {
        String regex = "^[a-zA-Z0-9_+&*-]+(?:\\.[a-zA-Z0-9_+&*-]+)*" +
                      "@(?:[a-zA-Z0-9-]+\\.)+[a-zA-Z]{2,7}$";
        return email.matches(regex);
    }
    
    // 패턴 3: 수동 검증 (정규식 없이)
    public static boolean isValidEmail3(String email) {
        if (email == null || email.isEmpty()) return false;
        
        int atIndex = email.indexOf('@');
        if (atIndex <= 0 || atIndex != email.lastIndexOf('@')) {
            return false;  // @ 없거나 여러 개
        }
        
        int dotIndex = email.lastIndexOf('.');
        if (dotIndex <= atIndex + 1 || dotIndex == email.length() - 1) {
            return false;  // @ 뒤에 . 없거나 마지막이 .
        }
        
        // 유효한 문자만
        for (char ch : email.toCharArray()) {
            if (!Character.isLetterOrDigit(ch) && 
                ch != '@' && ch != '.' && ch != '-' && ch != '_') {
                return false;
            }
        }
        
        return true;
    }
    
    public static void main(String[] args) {
        System.out.println(isValidEmail1("user@example.com"));    // true
        System.out.println(isValidEmail1("invalid@"));            // false
        System.out.println(isValidEmail1("@example.com"));        // false
    }
}
```

### 2.2 비밀번호 검증

```java
public class PasswordValidation {
    // 최소 8자, 대문자, 소문자, 숫자, 특수문자 포함
    public static boolean isStrongPassword(String password) {
        if (password == null || password.length() < 8) {
            return false;
        }
        
        boolean hasUpper = false;
        boolean hasLower = false;
        boolean hasDigit = false;
        boolean hasSpecial = false;
        
        for (char ch : password.toCharArray()) {
            if (Character.isUpperCase(ch)) hasUpper = true;
            else if (Character.isLowerCase(ch)) hasLower = true;
            else if (Character.isDigit(ch)) hasDigit = true;
            else hasSpecial = true;
        }
        
        return hasUpper && hasLower && hasDigit && hasSpecial;
    }
    
    // 정규식 버전
    public static boolean isStrongPasswordRegex(String password) {
        if (password == null || password.length() < 8) {
            return false;
        }
        
        // 모든 조건을 lookahead로 확인
        String regex = "^(?=.*[a-z])(?=.*[A-Z])(?=.*\\d)(?=.*[@$!%*?&])" +
                      "[A-Za-z\\d@$!%*?&]{8,}$";
        return password.matches(regex);
    }
    
    public static void main(String[] args) {
        System.out.println(isStrongPassword("Pass123!"));   // true
        System.out.println(isStrongPassword("password"));   // false
        System.out.println(isStrongPassword("Pass1234"));   // false
    }
}
```

### 2.3 전화번호 검증 및 포맷팅

```java
public class PhoneValidation {
    // 한국 전화번호 검증
    public static boolean isValidKoreanPhone(String phone) {
        // 010-1234-5678 또는 01012345678
        String cleaned = phone.replaceAll("[^0-9]", "");
        
        if (cleaned.length() != 11) return false;
        if (!cleaned.startsWith("010")) return false;
        
        return true;
    }
    
    // 포맷팅
    public static String formatPhone(String phone) {
        String cleaned = phone.replaceAll("[^0-9]", "");
        
        if (cleaned.length() != 11) return phone;
        
        return cleaned.substring(0, 3) + "-" + 
               cleaned.substring(3, 7) + "-" + 
               cleaned.substring(7);
    }
    
    // 다양한 형식 정규화
    public static String normalizePhone(String phone) {
        // 숫자만 추출
        String digits = phone.replaceAll("[^0-9]", "");
        
        // 국제번호 제거 (+82 → 0)
        if (digits.startsWith("82")) {
            digits = "0" + digits.substring(2);
        }
        
        return digits;
    }
    
    public static void main(String[] args) {
        System.out.println(formatPhone("01012345678"));
        // "010-1234-5678"
        
        System.out.println(normalizePhone("+82-10-1234-5678"));
        // "01012345678"
    }
}
```

---

## 3. 파싱 및 포맷팅

### 3.1 날짜 파싱

```java
public class DateParsing {
    // 다양한 날짜 형식 파싱
    public static Map<String, Integer> parseDate(String dateStr) {
        Map<String, Integer> result = new HashMap<>();
        
        // YYYY-MM-DD 형식
        if (dateStr.matches("\\d{4}-\\d{2}-\\d{2}")) {
            String[] parts = dateStr.split("-");
            result.put("year", Integer.parseInt(parts[0]));
            result.put("month", Integer.parseInt(parts[1]));
            result.put("day", Integer.parseInt(parts[2]));
        }
        // DD/MM/YYYY 형식
        else if (dateStr.matches("\\d{2}/\\d{2}/\\d{4}")) {
            String[] parts = dateStr.split("/");
            result.put("day", Integer.parseInt(parts[0]));
            result.put("month", Integer.parseInt(parts[1]));
            result.put("year", Integer.parseInt(parts[2]));
        }
        
        return result;
    }
    
    // 날짜 포맷팅
    public static String formatDate(int year, int month, int day, String format) {
        String formatted = format
            .replace("YYYY", String.format("%04d", year))
            .replace("MM", String.format("%02d", month))
            .replace("DD", String.format("%02d", day));
        
        return formatted;
    }
    
    public static void main(String[] args) {
        System.out.println(parseDate("2024-01-15"));
        // {year=2024, month=1, day=15}
        
        System.out.println(formatDate(2024, 1, 15, "YYYY년 MM월 DD일"));
        // "2024년 01월 15일"
    }
}
```

### 3.2 숫자 포맷팅

```java
public class NumberFormatting {
    // 천 단위 콤마
    public static String addCommas(int number) {
        return String.format("%,d", number);
    }
    
    // 수동 구현
    public static String addCommasManual(String number) {
        StringBuilder sb = new StringBuilder();
        int count = 0;
        
        for (int i = number.length() - 1; i >= 0; i--) {
            if (count == 3) {
                sb.insert(0, ',');
                count = 0;
            }
            sb.insert(0, number.charAt(i));
            count++;
        }
        
        return sb.toString();
    }
    
    // 파일 크기 포맷팅
    public static String formatFileSize(long bytes) {
        if (bytes < 1024) return bytes + " B";
        if (bytes < 1024 * 1024) return String.format("%.2f KB", bytes / 1024.0);
        if (bytes < 1024 * 1024 * 1024) 
            return String.format("%.2f MB", bytes / (1024.0 * 1024));
        return String.format("%.2f GB", bytes / (1024.0 * 1024 * 1024));
    }
    
    public static void main(String[] args) {
        System.out.println(addCommas(1234567));        // "1,234,567"
        System.out.println(formatFileSize(1536));      // "1.50 KB"
        System.out.println(formatFileSize(5242880));   // "5.00 MB"
    }
}
```

### 3.3 URL 파싱

```java
public class UrlParsing {
    public static class UrlComponents {
        String protocol;
        String host;
        int port;
        String path;
        Map<String, String> queryParams;
    }
    
    public static UrlComponents parseUrl(String url) {
        UrlComponents uc = new UrlComponents();
        uc.queryParams = new HashMap<>();
        
        // 프로토콜
        int protocolEnd = url.indexOf("://");
        uc.protocol = url.substring(0, protocolEnd);
        
        // 호스트 시작
        int hostStart = protocolEnd + 3;
        int pathStart = url.indexOf('/', hostStart);
        if (pathStart == -1) pathStart = url.length();
        
        // 호스트:포트
        String hostPort = url.substring(hostStart, pathStart);
        int colonIdx = hostPort.indexOf(':');
        if (colonIdx != -1) {
            uc.host = hostPort.substring(0, colonIdx);
            uc.port = Integer.parseInt(hostPort.substring(colonIdx + 1));
        } else {
            uc.host = hostPort;
            uc.port = uc.protocol.equals("https") ? 443 : 80;
        }
        
        // 경로와 쿼리
        if (pathStart < url.length()) {
            int queryStart = url.indexOf('?', pathStart);
            if (queryStart != -1) {
                uc.path = url.substring(pathStart, queryStart);
                
                // 쿼리 파라미터
                String query = url.substring(queryStart + 1);
                for (String param : query.split("&")) {
                    String[] kv = param.split("=", 2);
                    if (kv.length == 2) {
                        uc.queryParams.put(kv[0], kv[1]);
                    }
                }
            } else {
                uc.path = url.substring(pathStart);
            }
        } else {
            uc.path = "/";
        }
        
        return uc;
    }
    
    public static void main(String[] args) {
        String url = "https://example.com:8080/path?key=value&foo=bar";
        UrlComponents uc = parseUrl(url);
        
        System.out.println("Protocol: " + uc.protocol);  // https
        System.out.println("Host: " + uc.host);          // example.com
        System.out.println("Port: " + uc.port);          // 8080
        System.out.println("Path: " + uc.path);          // /path
        System.out.println("Params: " + uc.queryParams); // {key=value, foo=bar}
    }
}
```

---

## 4. 텍스트 처리 패턴

### 4.1 단어 빈도 분석

```java
public class WordFrequency {
    public static Map<String, Integer> analyzeFrequency(String text) {
        // 소문자 변환 및 특수문자 제거
        text = text.toLowerCase().replaceAll("[^a-z\\s]", "");
        
        Map<String, Integer> freq = new HashMap<>();
        String[] words = text.split("\\s+");
        
        for (String word : words) {
            if (!word.isEmpty()) {
                freq.put(word, freq.getOrDefault(word, 0) + 1);
            }
        }
        
        return freq;
    }
    
    // 가장 빈도가 높은 N개 단어
    public static List<Map.Entry<String, Integer>> topN(
            Map<String, Integer> freq, int n) {
        
        return freq.entrySet().stream()
            .sorted((e1, e2) -> e2.getValue().compareTo(e1.getValue()))
            .limit(n)
            .collect(Collectors.toList());
    }
    
    public static void main(String[] args) {
        String text = "Java is great. Java is powerful. Programming is fun.";
        Map<String, Integer> freq = analyzeFrequency(text);
        
        System.out.println(topN(freq, 3));
        // [java=2, is=3, great=1]
    }
}
```

### 4.2 텍스트 줄바꿈 (Word Wrap)

```java
public class TextWrap {
    public static String wrap(String text, int lineWidth) {
        StringBuilder result = new StringBuilder();
        String[] words = text.split(" ");
        
        int currentLineLength = 0;
        
        for (String word : words) {
            if (currentLineLength + word.length() > lineWidth) {
                if (currentLineLength > 0) {
                    result.append("\n");
                }
                result.append(word);
                currentLineLength = word.length();
            } else {
                if (currentLineLength > 0) {
                    result.append(" ");
                    currentLineLength++;
                }
                result.append(word);
                currentLineLength += word.length();
            }
        }
        
        return result.toString();
    }
    
    public static void main(String[] args) {
        String text = "The quick brown fox jumps over the lazy dog";
        System.out.println(wrap(text, 20));
        /*
        The quick brown fox
        jumps over the lazy
        dog
        */
    }
}
```

### 4.3 마크다운 간단 파싱

```java
public class SimpleMarkdown {
    public static String toHtml(String markdown) {
        StringBuilder html = new StringBuilder();
        String[] lines = markdown.split("\n");
        
        for (String line : lines) {
            // 헤더
            if (line.startsWith("# ")) {
                html.append("<h1>").append(line.substring(2)).append("</h1>\n");
            } else if (line.startsWith("## ")) {
                html.append("<h2>").append(line.substring(3)).append("</h2>\n");
            }
            // 볼드
            else {
                line = line.replaceAll("\\*\\*(.+?)\\*\\*", "<strong>$1</strong>");
                line = line.replaceAll("\\*(.+?)\\*", "<em>$1</em>");
                html.append("<p>").append(line).append("</p>\n");
            }
        }
        
        return html.toString();
    }
    
    public static void main(String[] args) {
        String md = "# Title\n## Subtitle\nThis is **bold** and *italic* text.";
        System.out.println(toHtml(md));
    }
}
```

---

## 5. 성능 최적화 패턴

### 5.1 String Pool 활용

```java
public class StringPoolOptimization {
    // ❌ 비효율: new String() 사용
    public static void inefficient() {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < 1000; i++) {
            list.add(new String("constant"));  // 1000개 객체!
        }
    }
    
    // ✅ 효율: 리터럴 사용
    public static void efficient() {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < 1000; i++) {
            list.add("constant");  // 객체 1개!
        }
    }
    
    // 외부 입력 최적화
    public static Set<String> processLogs(String[] logs) {
        Set<String> unique = new HashSet<>();
        for (String log : logs) {
            unique.add(log.intern());  // Pool 재사용
        }
        return unique;
    }
}
```

### 5.2 StringBuilder 재사용

```java
public class StringBuilderReuse {
    private StringBuilder reusable = new StringBuilder(1000);
    
    // 매번 새로 생성 (비효율)
    public String format1(String name, int value) {
        StringBuilder sb = new StringBuilder();
        sb.append(name).append(": ").append(value);
        return sb.toString();
    }
    
    // 재사용 (효율적)
    public String format2(String name, int value) {
        reusable.setLength(0);  // 초기화
        reusable.append(name).append(": ").append(value);
        return reusable.toString();
    }
    
    // 벤치마크
    public static void benchmark() {
        StringBuilderReuse obj = new StringBuilderReuse();
        
        long start1 = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            obj.format1("test", i);
        }
        long end1 = System.nanoTime();
        
        long start2 = System.nanoTime();
        for (int i = 0; i < 100000; i++) {
            obj.format2("test", i);
        }
        long end2 = System.nanoTime();
        
        System.out.println("New each time: " + (end1-start1)/1_000_000 + "ms");
        System.out.println("Reuse: " + (end2-start2)/1_000_000 + "ms");
    }
}
```

### 5.3 String 연결 최적화

```java
public class ConcatenationOptimization {
    // ❌ 최악: 반복문에서 +
    public static String bad(List<String> items) {
        String result = "";
        for (String item : items) {
            result += item + ",";  // 매번 새 객체!
        }
        return result;
    }
    
    // ✅ 좋음: StringBuilder
    public static String good(List<String> items) {
        StringBuilder sb = new StringBuilder();
        for (String item : items) {
            sb.append(item).append(",");
        }
        return sb.toString();
    }
    
    // ✅ 최고: String.join (Java 8+)
    public static String best(List<String> items) {
        return String.join(",", items);
    }
    
    // 한 두번 연결은 + 괜찮음
    public static String acceptable(String a, String b, String c) {
        return a + b + c;  // 컴파일러가 최적화
    }
}
```

### 5.4 정규식 컴파일 캐싱

```java
public class RegexOptimization {
    // ❌ 비효율: 매번 컴파일
    public static boolean validateBad(String email) {
        return email.matches("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$");
        // matches()는 매번 Pattern 컴파일!
    }
    
    // ✅ 효율: Pattern 재사용
    private static final Pattern EMAIL_PATTERN = 
        Pattern.compile("^[\\w.-]+@[\\w.-]+\\.[a-zA-Z]{2,}$");
    
    public static boolean validateGood(String email) {
        return EMAIL_PATTERN.matcher(email).matches();
    }
    
    // 벤치마크
    public static void benchmark() {
        String[] emails = new String[10000];
        Arrays.fill(emails, "test@example.com");
        
        long start1 = System.nanoTime();
        for (String email : emails) {
            validateBad(email);
        }
        long end1 = System.nanoTime();
        
        long start2 = System.nanoTime();
        for (String email : emails) {
            validateGood(email);
        }
        long end2 = System.nanoTime();
        
        System.out.println("Without cache: " + (end1-start1)/1_000_000 + "ms");
        System.out.println("With cache: " + (end2-start2)/1_000_000 + "ms");
        // 약 10배 이상 차이!
    }
}
```

---

## 📌 핵심 패턴 정리

### 알고리즘
```java
// 팰린드롬: 양쪽 포인터
int left = 0, right = str.length()-1;
while (left < right) {
    if (str.charAt(left++) != str.charAt(right--)) return false;
}

// 애너그램: 빈도수 배열
int[] count = new int[26];
for (char ch : s1.toCharArray()) count[ch-'a']++;
for (char ch : s2.toCharArray()) count[ch-'a']--;
```

### 검증
```java
// 정규식 패턴 캐싱
private static final Pattern EMAIL = Pattern.compile("...");

// 수동 검증으로 성능 향상
boolean valid = checkManually(str);  // 정규식보다 빠름
```

### 파싱
```java
// URL: split 대신 indexOf
int idx = url.indexOf("://");
String protocol = url.substring(0, idx);

// 날짜: String.format 활용
String.format("%04d-%02d-%02d", year, month, day);
```

### 성능
```java
// ✅ StringBuilder 초기 용량
StringBuilder sb = new StringBuilder(expectedSize);

// ✅ String Pool 활용
str.intern()  // 중복 문자열 많을 때

// ✅ String.join
String.join(",", list)  // + 연결보다 빠름
```

---

## 🎉 String 시리즈 완료!

<div align="center">

### 📚 전체 시리즈

| Chapter | 주제 |
|:-------:|------|
| [01. 기본 개념](./String-01-기본개념.md) | Immutable & String Pool |
| [02. 생성과 비교](./String-02-생성과비교.md) | 생성 방법과 비교 메서드 |
| [03. 검색과 인덱싱](./String-03-검색과인덱싱.md) | 문자 접근과 위치 찾기 |
| [04. 변환과 치환](./String-04-변환과치환.md) | 문자열 변환하기 |
| [05. 분리와 결합](./String-05-분리와결합.md) | 나누고 합치기 |
| [06. StringBuilder & StringBuffer](./String-06-StringBuilder-StringBuffer.md) | 가변 문자열 처리 |
| [07. 실전 패턴](./String-07-실전패턴.md) ⭐ | 알고리즘과 실무 패턴 |

</div>

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[← 이전: 06. StringBuilder & StringBuffer](./String-06-StringBuilder-StringBuffer.md)**

</div>
