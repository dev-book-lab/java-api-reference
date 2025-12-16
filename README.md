# Java API Reference

> 📚 Java 표준 라이브러리 완전 정복 - 실전 예제와 함께하는 API 레퍼런스

백준/알고리즘 문제 풀이와 실무 개발을 위한 Java API 심화 학습 자료입니다.  
각 API의 동작 원리부터 실전 활용 패턴까지 상세한 예제 코드와 함께 정리했습니다.

---

## 📖 목차

### 🔹 [IO & 입출력](./io/)
Java 입출력의 모든 것 - BufferedReader부터 File 처리까지

- [Java IO 완벽 가이드](./io/Java-IO-완벽-가이드.md) - 백준 문제 풀이용 입출력 완전 정복

### 🔹 [String & 문자열](./string/)
문자열 처리의 핵심 - String, StringBuilder, 그리고 실전 패턴

- [01. 기본 개념 (Immutable & Intern Pool)](./string/String-01-기본개념.md)
- [02. 생성과 비교](./string/String-02-생성과비교.md)
- [03. 검색과 인덱싱](./string/String-03-검색과인덱싱.md)
- [04. 변환과 치환](./string/04-변환과치환.md) *(예정)*
- [05. 분리와 결합](./string/05-분리와결합.md) *(예정)*
- [06. StringBuilder & StringBuffer](./string/06-StringBuilder-StringBuffer.md) *(예정)*
- [07. 실전 패턴](./string/07-실전패턴.md) *(예정)*

### 🔹 [Collections Framework](./collections/)
자료구조의 완성 - List, Set, Map, Queue의 모든 것

- 01. Collections 개요 *(예정)*
- 02. List - ArrayList *(예정)*
- 03. List - LinkedList *(예정)*
- 04. List 비교와 선택 *(예정)*
- 05. Set - HashSet *(예정)*
- 06. Set - LinkedHashSet *(예정)*
- 07. Set - TreeSet *(예정)*
- 08. Set 비교와 선택 *(예정)*
- 09. Map - HashMap *(예정)*
- 10. Map - LinkedHashMap *(예정)*
- 11. Map - TreeMap *(예정)*
- 12. Map 비교와 선택 *(예정)*
- 13. Queue & Deque 기본 *(예정)*
- 14. PriorityQueue *(예정)*
- 15. Stack (+ Deque로 구현) *(예정)*
- 16. Collections 유틸 클래스 *(예정)*

### 🔹 [Arrays](./arrays/)
배열 처리의 달인 - Arrays 클래스 완전 활용

- 01. 배열 기본과 Arrays 클래스 *(예정)*
- 02. Arrays 정렬 (sort, parallelSort) *(예정)*
- 03. Arrays 검색 (binarySearch) *(예정)*
- 04. Arrays 비교와 복사 *(예정)*
- 05. Arrays 변환 (stream, asList) *(예정)*
- 06. 다차원 배열 다루기 *(예정)*

### 🔹 [Math & Number](./math/)
수학 연산의 모든 것 - Math, Wrapper, BigInteger

- 01. Math 클래스 - 기본 연산 *(예정)*
- 02. Math 클래스 - 삼각함수와 지수로그 *(예정)*
- 03. Math 클래스 - 난수와 특수 함수 *(예정)*
- 04. Wrapper 클래스 (Integer, Double 등) *(예정)*
- 05. BigInteger & BigDecimal *(예정)*
- 06. Number 형변환과 파싱 *(예정)*

### 🔹 [Date & Time](./datetime/)
날짜와 시간의 마스터 - Java 8 Time API

- 01. Java 8 Time API 개요 *(예정)*
- 02. LocalDate, LocalTime, LocalDateTime *(예정)*
- 03. ZonedDateTime & Instant *(예정)*
- 04. Period & Duration *(예정)*
- 05. DateTimeFormatter *(예정)*
- 06. 레거시 (Date, Calendar) vs 신규 API *(예정)*

### 🔹 [기타 유틸리티](./utils/)
실무 필수 도구들

- Comparator & Comparable *(예정)*
- Optional *(예정)*
- Stream API 기초 *(예정)*
- 정규표현식 (Pattern, Matcher) *(예정)*

---

## 🎯 학습 방법

### 1단계: 기초 다지기
- **IO & 입출력** - 백준 문제 풀이의 시작
- **String 01~03** - 문자열 기본기

### 2단계: 핵심 자료구조
- **Collections Framework** - ArrayList, HashMap 중심
- **Arrays** - 배열 처리 마스터

### 3단계: 심화 학습
- **String 04~07** - 고급 문자열 처리
- **Collections 심화** - TreeSet, PriorityQueue 등
- **Math & Number** - 수학 연산 완성

### 4단계: 실전 응용
- **Date & Time** - 날짜 처리
- **Utils** - Comparator, Optional, Stream

---

## 📝 문서 구성

각 문서는 다음과 같이 구성됩니다:

1. **기본 개념** - 이론과 원리
2. **메서드 레퍼런스** - 사용법과 예제
3. **실전 활용** - 실무/알고리즘 패턴
4. **연습 문제** - 실력 체크
5. **핵심 정리** - 빠른 복습

---

## 💡 특징

### ✅ 실행 가능한 코드
모든 예제는 복사-붙여넣기로 바로 실행 가능합니다.

### ✅ 실전 중심
백준, 프로그래머스 등 알고리즘 문제와 실무에서 자주 쓰는 패턴 위주로 구성했습니다.

### ✅ 내부 동작 이해
단순 암기가 아닌, 왜 그렇게 동작하는지 원리부터 설명합니다.

### ✅ 성능 비교
같은 기능을 하는 여러 방법의 성능을 비교하고 언제 무엇을 써야 하는지 안내합니다.

### ✅ 함정 회피
초보자가 자주 하는 실수와 주의사항을 명확히 표시했습니다.

---

## 🔥 Quick Start

### 백준 문제 풀이를 시작한다면?
1. [Java IO 완벽 가이드](./io/Java-IO-완벽-가이드.md) - 입출력 마스터
2. [String 01-03](./string/) - 문자열 기본
3. [Collections - ArrayList, HashMap](./collections/) - 자료구조 핵심

### API를 깊이있게 공부한다면?
1. [String 전체](./string/) - 문자열 완전 정복
2. [Collections Framework](./collections/) - 자료구조 전문가
3. [Math & Arrays](./math/) - 수학/배열 고수

---

**Happy Coding! 🚀**
