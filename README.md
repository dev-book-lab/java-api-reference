<div align="center">

# 📖 Java API Reference

**실전 예제와 함께하는 Java 표준 라이브러리 완전 정복**

<br/>

> *"암기가 아닌 이해, 요약이 아닌 통찰"*

실무 개발부터 코딩 테스트까지,  
**왜 그렇게 동작하는지** 원리부터 파헤치는 Java API 심화 학습 자료

<br/>

[![GitHub](https://img.shields.io/badge/GitHub-dev--book--lab-181717?style=flat-square&logo=github)](https://github.com/dev-book-lab)

</div>

---

## 🎯 이 프로젝트에 대하여

단순한 메서드 나열이 아닌, **실전에서 바로 써먹을 수 있는** Java API 레퍼런스입니다.

### ✨ 특징

| 🎯 **원리 중심** | 💻 **실행 가능** | 🔥 **실전 패턴** | 📊 **성능 비교** |
|:---:|:---:|:---:|:---:|
| 왜 그렇게 동작하는지<br/>원리부터 설명 | 모든 코드는<br/>복사-붙여넣기 즉시 실행 | 실무에서<br/>자주 쓰는 패턴 | 같은 기능의<br/>여러 방법 비교 |

- ✅ **200+ 실행 가능한 예제 코드** - 이론만이 아닌 실습 중심
- ✅ **내부 동작 원리 설명** - 단순 암기가 아닌 깊이 있는 이해
- ✅ **25+ 실전 연습 문제** - 학습 내용 즉시 확인
- ✅ **성능 벤치마크 포함** - 언제 무엇을 써야 하는지 명확히
- ✅ **함정 회피 가이드** - 초보자가 자주 하는 실수 미리 차단

---

## 📚 목차

> 💡 **각 챕터를 클릭하면 상세한 학습 문서로 이동합니다**

### 🔹 String & 문자열

문자열 처리의 핵심 - String, StringBuilder, 그리고 실전 패턴

| Chapter | 주제 | 핵심 키워드 |
|:-------:|------|------------|
| **[01. 기본 개념](./string/String-01-기본개념.md)** | Immutable & String Pool | 불변성, intern(), 메모리 구조 |
| **[02. 생성과 비교](./string/String-02-생성과비교.md)** | 생성 방법과 비교 메서드 | equals, compareTo, valueOf |
| **[03. 검색과 인덱싱](./string/String-03-검색과인덱싱.md)** | 문자 접근과 위치 찾기 | charAt, indexOf, substring |
| **[04. 변환과 치환](./string/String-04-변환과치환.md)** | 문자열 변환하기 | toUpperCase, replace, trim |
| **[05. 분리와 결합](./string/String-05-분리와결합.md)** | 나누고 합치기 | split, join, StringJoiner |
| **[06. StringBuilder & StringBuffer](./string/String-06-StringBuilder-StringBuffer.md)** | 가변 문자열 처리 | 성능 최적화, 동기화 |
| **[07. 실전 패턴](./string/String-07-실전패턴.md)** | 알고리즘과 실무 패턴 | 팰린드롬, 검증, 파싱, 최적화 |

<br/>

### 🔹 Collections Framework

자료구조의 완성 - List, Set, Map, Queue의 모든 것

<table>
<tr>
<td width="50%">

**📋 List**
- 01. **[Collections 개요](./collections/Collections-01-Overview.md)**
- 02. **[ArrayList](./collections/Collections-02-ArrayList.md)**
- 03. **[LinkedList](./collections/Collections-03-LinkedList.md)**
- 04. **[List 비교와 선택](./collections/Collections-04-ListComparison.md)**

**🎯 Set**
- 05. **[HashSet](./collections/Collections-05-HashSet.md)**
- 06. **[LinkedHashSet](./collections/Collections-06-LinkedHashSet.md)**
- 07. **[TreeSet](./collections/Collections-07-TreeSet.md)**
- 08. **[Set 비교와 선택](./collections/Collections-08-SetComparison.md)**

</td>
<td width="50%">

**🗺️ Map**
- 09. **[HashMap](./collections/Collections-09-HashMap.md)**
- 10. **[LinkedHashMap](./collections/Collections-10-LinkedHashMap.md)**
- 11. **[TreeMap](./collections/Collections-11-TreeMap.md)**
- 12. **[Map 비교와 선택](./collections/Collections-12-MapComparison.md)**

**📤 Queue & Stack**
- 13. **[Queue & Deque](./collections/Collections-13-Queue-Deque.md)**
- 14. **[PriorityQueue](./collections/Collections-14-PriorityQueue.md)**
- 15. **[Stack](./collections/Collections-15-Stack.md)**
- 16. **[Collections 유틸](./collections/Collections-16-Utils.md)**

</td>
</tr>
</table>

<br/>

### 🔹 Arrays

배열 처리의 달인 - Arrays 클래스 완전 활용

| Chapter | 주제 | 핵심 키워드 |
|:-------:|------|------------|
| **01. [배열 기본](./arrays/Arrays-01-배열기본.md)** | 배열과 Arrays 클래스 | 선언, 초기화, 기본 연산 |
| **02. [정렬](./arrays/Arrays-02-정렬.md)** | sort, parallelSort | Comparator, 성능 비교 |
| **03. [검색](./arrays/Arrays-03-검색.md)** | binarySearch | 이진 탐색 활용 |
| **04. [비교와 복사](./arrays/Arrays-04-비교와복사.md)** | equals, copyOf | 깊은 복사, 얕은 복사 |
| **05. [변환](./arrays/Arrays-05-변환.md)** | stream, asList | 배열 ↔ List 변환 |
| **06. [다차원 배열](./arrays/Arrays-06-다차원배열.md)** | 2D, 3D 배열 | deepEquals, deepToString |

<br/>

### 🔹 Math & Number

수학 연산의 모든 것 - Math, Wrapper, BigInteger

<table>
<tr>
<td width="50%">

**🔢 Math 클래스**
- 01. **[기본 연산](./math/01-기본연산.md)**
- 02. **[삼각함수와 지수로그](./math/02-삼각함수.md)**
- 03. **[난수와 특수 함수](./math/03-난수.md)**

</td>
<td width="50%">

**🎁 Number 클래스**
- 04. **[Wrapper 클래스](./math/04-Wrapper.md)**
- 05. **[BigInteger & BigDecimal](./math/05-BigInteger.md)**
- 06. **[형변환과 파싱](./math/06-형변환.md)**

</td>
</tr>
</table>

<br/>

### 🔹 Date & Time

날짜와 시간의 마스터 - Java 8 Time API

| Chapter | 주제 | 핵심 키워드 |
|:-------:|------|------------|
| **01. [Time API 개요](./datetime/DateTime-01-개요.md)** | Java 8 Time API | LocalDate, ZonedDateTime |
| **02. [Local 클래스](./datetime/DateTime-02-Local.md)** | LocalDate, LocalTime, LocalDateTime | 날짜/시간 기본 |
| **03. [Zoned & Instant](./datetime/DateTime-03-Zoned.md)** | ZonedDateTime, Instant | 타임존, UTC |
| **04. [Period & Duration](./datetime/DateTime-04-Period.md)** | 기간 계산 | 날짜 차이, 시간 차이 |
| **05. [포맷팅](./datetime/DateTime-05-Formatter.md)** | DateTimeFormatter | 날짜 포맷 변환 |
| **06. [레거시 vs 신규](./datetime/DateTime-06-Legacy.md)** | Date, Calendar 비교 | 마이그레이션 가이드 |

<br/>

### 🔹 IO & 입출력

Java 입출력의 모든 것 - 스트림부터 파일 처리까지

| Chapter | 주제 | 핵심 키워드 |
|:-------:|------|------------|
| **01. [InputStream & OutputStream](./io/01-InputStream.md)** *(예정)* | 바이트 스트림 | read, write, close |
| **02. [Reader & Writer](./io/02-Reader.md)** *(예정)* | 문자 스트림 | 인코딩, 문자 처리 |
| **03. [BufferedReader & Writer](./io/03-Buffered.md)** *(예정)* | 버퍼링 | 성능 최적화, readLine |
| **04. [File & Path](./io/04-File.md)** *(예정)* | 파일 처리 | NIO.2, Files 클래스 |

<br/>

### 🔹 기타 유틸리티

실무 필수 도구들

- [Comparator & Comparable](./utils/Comparator.md) *(예정)*
- [Optional](./utils/Optional.md) *(예정)*
- [Stream API](./utils/Stream.md) *(예정)*
- [정규표현식](./utils/Regex.md) *(예정)*

---

## 🎓 학습 방법

```
📖 Read → 💻 Practice → 🤔 Think → 📝 Review → 🔁 Repeat
```

### 1️⃣ 기초부터 차근차근
```
String 01~03 → Collections (ArrayList, HashMap) → Arrays 기본
```

### 2️⃣ 핵심 자료구조 마스터
```
String 전체 → Collections 전체 → Math & Arrays
```

### 3️⃣ 심화 및 최적화
```
전체 심화 → 성능 최적화 → 실전 패턴
```

---

## 💻 시작하기

### 📋 필요 사항
- **Java 8** 이상 (일부 Java 11+ 기능 포함)
- **IntelliJ IDEA** 또는 Eclipse, VS Code

### 1️⃣ Repository 클론
```bash
git clone https://github.com/dev-book-lab/java-api-reference.git
cd java-api-reference
```

### 2️⃣ 학습 방법
1. 관심 있는 챕터의 문서 읽기
2. 예제 코드를 직접 실행하며 이해
3. 연습 문제로 실력 확인
4. 실전 패턴을 프로젝트에 적용

---

## 📖 문서 구성

각 문서는 다음과 같은 구조로 구성됩니다:

| 섹션 | 설명 |
|------|------|
| 📌 **기본 개념** | 이론과 원리 설명 |
| 💻 **메서드 레퍼런스** | 사용법과 예제 코드 |
| 🔥 **실전 활용** | 실무 패턴과 활용법 |
| 🎯 **연습 문제** | 학습 내용 확인 |
| ⚡ **성능 비교** | 여러 방법의 성능 측정 |
| 📌 **핵심 정리** | 빠른 복습용 요약 |

---

## 📊 진행 상황

<div align="center">

| Category | Status | Progress |
|:--------:|:------:|:--------:|
| **String** | ✅ 완료 | ![100%](https://progress-bar.dev/100) |
| **Collections** | 🚧 예정 | ![0%](https://progress-bar.dev/0) |
| **Arrays** | 🚧 예정 | ![0%](https://progress-bar.dev/0) |
| **Math** | 🚧 예정 | ![0%](https://progress-bar.dev/0) |
| **Date & Time** | 🚧 예정 | ![0%](https://progress-bar.dev/0) |
| **IO** | 🚧 예정 | ![0%](https://progress-bar.dev/0) |
| **Utils** | 🚧 예정 | ![0%](https://progress-bar.dev/0) |

</div>

---

## 🤝 기여하기

더 좋은 예제나 설명이 있다면 언제든 환영합니다!

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingExample`)
3. Commit your changes (`git commit -m 'Add amazing example'`)
4. Push to the branch (`git push origin feature/AmazingExample`)
5. Open a Pull Request

---

## 🙏 Reference

- [Oracle Java Documentation](https://docs.oracle.com/en/java/)
- [OpenJDK Source Code](https://github.com/openjdk/jdk)

---

## ✨ Dev Book Lab

<div align="center">

**AI와 함께 개발 서적을 분석하고 정리하는 연구소**

[📂 More Projects](https://github.com/dev-book-lab)

</div>

---

<div align="center">

**⭐️ 도움이 되셨다면 Star를 눌러주세요!**

Made with ❤️ by Dev Book Lab

<br/>

*"암기가 아닌 이해, 요약이 아닌 통찰"*

</div>
