# Concurrency 01. Thread & Runnable 기초

> 멀티스레딩의 시작 - Thread와 Runnable 완전 정복  
> Java Concurrency API

---

## 📑 목차

1. [스레드 기본 개념](#1-스레드-기본-개념)
2. [Thread 클래스](#2-thread-클래스)
3. [Runnable 인터페이스](#3-runnable-인터페이스)
4. [스레드 생명주기](#4-스레드-생명주기)
5. [스레드 제어](#5-스레드-제어)
6. [데몬 스레드](#6-데몬-스레드)
7. [스레드 그룹](#7-스레드-그룹)
8. [실전 예제](#8-실전-예제)
9. [실전 연습 문제](#9-실전-연습-문제)

---

## 1. 스레드 기본 개념

### 1.1 프로세스 vs 스레드

```java
public class ProcessVsThread {
    public static void main(String[] args) {
        System.out.println("=== 프로세스 vs 스레드 ===\n");
        
        System.out.println("📌 프로세스 (Process)");
        System.out.println("- 실행 중인 프로그램");
        System.out.println("- 독립된 메모리 공간");
        System.out.println("- 자원 할당의 기본 단위");
        System.out.println("- 프로세스 간 통신 비용 높음\n");
        
        System.out.println("📌 스레드 (Thread)");
        System.out.println("- 프로세스 내의 실행 단위");
        System.out.println("- 프로세스 메모리 공유");
        System.out.println("- CPU 스케줄링 단위");
        System.out.println("- 스레드 간 통신 비용 낮음\n");
        
        System.out.println("=== Java 프로그램 구조 ===");
        System.out.println("JVM 프로세스");
        System.out.println("└─ main 스레드 (기본)");
        System.out.println("   ├─ 사용자 스레드 1");
        System.out.println("   ├─ 사용자 스레드 2");
        System.out.println("   └─ 데몬 스레드 (GC 등)");
    }
}
```

### 1.2 멀티스레딩의 장단점

```java
public class MultithreadingProsAndCons {
    public static void main(String[] args) {
        System.out.println("=== 멀티스레딩 장점 ===\n");
        
        System.out.println("✅ 응답성 향상");
        System.out.println("- UI 블로킹 방지");
        System.out.println("- 사용자 경험 개선\n");
        
        System.out.println("✅ 자원 공유");
        System.out.println("- 같은 메모리 공간 사용");
        System.out.println("- 효율적인 통신\n");
        
        System.out.println("✅ 경제성");
        System.out.println("- 프로세스 생성보다 저렴");
        System.out.println("- 컨텍스트 스위칭 비용 낮음\n");
        
        System.out.println("✅ 멀티코어 활용");
        System.out.println("- 병렬 처리");
        System.out.println("- 성능 향상\n");
        
        
        System.out.println("=== 멀티스레딩 단점 ===\n");
        
        System.out.println("❌ 복잡성 증가");
        System.out.println("- 동기화 필요");
        System.out.println("- 디버깅 어려움\n");
        
        System.out.println("❌ 동시성 문제");
        System.out.println("- 경쟁 조건 (Race Condition)");
        System.out.println("- 데드락 (Deadlock)\n");
        
        System.out.println("❌ 컨텍스트 스위칭 오버헤드");
        System.out.println("- 스레드가 많으면 성능 저하");
    }
}
```

### 1.3 main 스레드

```java
public class MainThread {
    public static void main(String[] args) {
        // 현재 실행 중인 스레드 정보
        Thread mainThread = Thread.currentThread();
        
        System.out.println("=== main 스레드 정보 ===");
        System.out.println("이름: " + mainThread.getName());
        System.out.println("ID: " + mainThread.getId());
        System.out.println("우선순위: " + mainThread.getPriority());
        System.out.println("상태: " + mainThread.getState());
        System.out.println("데몬 여부: " + mainThread.isDaemon());
        System.out.println("살아있음: " + mainThread.isAlive());
        
        
        System.out.println("\n=== 실행 순서 ===");
        System.out.println("1. JVM 시작");
        System.out.println("2. main 스레드 생성");
        System.out.println("3. main() 메서드 실행");
        System.out.println("4. main() 종료");
        System.out.println("5. 모든 스레드 종료 대기");
        System.out.println("6. JVM 종료");
    }
}
```

---

## 2. Thread 클래스

### 2.1 Thread 상속 방식

```java
public class ThreadExtends {
    // Thread 상속
    static class MyThread extends Thread {
        private String name;
        
        MyThread(String name) {
            this.name = name;
        }
        
        @Override
        public void run() {
            for (int i = 1; i <= 5; i++) {
                System.out.println(name + ": " + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Thread 상속 ===");
        
        MyThread t1 = new MyThread("스레드-1");
        MyThread t2 = new MyThread("스레드-2");
        
        // 스레드 시작
        t1.start();
        t2.start();
        
        System.out.println("main 스레드 종료");
    }
}
```

### 2.2 Thread 생성자

```java
public class ThreadConstructors {
    static class Task extends Thread {
        @Override
        public void run() {
            System.out.println("Task 실행: " + getName());
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Thread 생성자 ===\n");
        
        // 기본 생성자
        Thread t1 = new Task();
        t1.start();
        
        // 이름 지정
        Thread t2 = new Task();
        t2.setName("Custom-Thread");
        t2.start();
        
        // Runnable + 이름
        Thread t3 = new Thread(() -> {
            System.out.println("Lambda 실행: " + Thread.currentThread().getName());
        }, "Lambda-Thread");
        t3.start();
        
        // ThreadGroup + 이름
        ThreadGroup group = new ThreadGroup("MyGroup");
        Thread t4 = new Thread(group, () -> {
            System.out.println("그룹 스레드 실행");
        }, "Group-Thread");
        t4.start();
    }
}
```

### 2.3 start() vs run()

```java
public class StartVsRun {
    static class MyThread extends Thread {
        @Override
        public void run() {
            System.out.println("스레드 실행: " + Thread.currentThread().getName());
        }
    }
    
    public static void main(String[] args) {
        MyThread t1 = new MyThread();
        MyThread t2 = new MyThread();
        
        System.out.println("=== start() 호출 ===");
        t1.start();  // 새 스레드에서 실행
        
        System.out.println("\n=== run() 직접 호출 ===");
        t2.run();    // main 스레드에서 실행
        
        System.out.println("\n현재 스레드: " + Thread.currentThread().getName());
        
        
        System.out.println("\n=== 차이점 ===");
        System.out.println("start():");
        System.out.println("  - 새로운 스레드 생성");
        System.out.println("  - Call Stack 생성");
        System.out.println("  - run() 메서드를 새 스레드에서 실행");
        System.out.println("\nrun():");
        System.out.println("  - 현재 스레드에서 실행");
        System.out.println("  - 일반 메서드 호출과 동일");
    }
}
```

---

## 3. Runnable 인터페이스

### 3.1 Runnable 구현 방식

```java
public class RunnableImplements {
    // Runnable 구현
    static class MyTask implements Runnable {
        private String name;
        
        MyTask(String name) {
            this.name = name;
        }
        
        @Override
        public void run() {
            for (int i = 1; i <= 5; i++) {
                System.out.println(name + ": " + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== Runnable 구현 ===");
        
        // Runnable 객체 생성
        MyTask task1 = new MyTask("작업-1");
        MyTask task2 = new MyTask("작업-2");
        
        // Thread 객체에 전달
        Thread t1 = new Thread(task1);
        Thread t2 = new Thread(task2);
        
        // 스레드 시작
        t1.start();
        t2.start();
        
        System.out.println("main 스레드 종료");
    }
}
```

### 3.2 익명 클래스와 람다

```java
public class RunnableAnonymousAndLambda {
    public static void main(String[] args) {
        System.out.println("=== 익명 클래스 ===");
        
        // 익명 클래스
        Thread t1 = new Thread(new Runnable() {
            @Override
            public void run() {
                System.out.println("익명 클래스 실행");
            }
        });
        t1.start();
        
        
        System.out.println("\n=== 람다 표현식 ===");
        
        // 람다 (Runnable은 함수형 인터페이스)
        Thread t2 = new Thread(() -> {
            System.out.println("람다 실행");
        });
        t2.start();
        
        
        // 간단한 람다
        Thread t3 = new Thread(() -> System.out.println("한 줄 람다"));
        t3.start();
        
        
        // 여러 작업
        Thread t4 = new Thread(() -> {
            for (int i = 1; i <= 3; i++) {
                System.out.println("람다 작업: " + i);
                try {
                    Thread.sleep(100);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        t4.start();
    }
}
```

### 3.3 Thread vs Runnable 비교

```java
public class ThreadVsRunnable {
    public static void main(String[] args) {
        System.out.println("=== Thread vs Runnable ===\n");
        
        System.out.println("📌 Thread 상속");
        System.out.println("장점:");
        System.out.println("  - 간단한 구현");
        System.out.println("  - Thread 메서드 직접 사용");
        System.out.println("단점:");
        System.out.println("  - 다중 상속 불가");
        System.out.println("  - 유연성 떨어짐\n");
        
        System.out.println("📌 Runnable 구현");
        System.out.println("장점:");
        System.out.println("  - 다른 클래스 상속 가능");
        System.out.println("  - 코드 재사용성 높음");
        System.out.println("  - 객체지향적");
        System.out.println("단점:");
        System.out.println("  - Thread 객체 별도 생성 필요\n");
        
        System.out.println("✅ 권장: Runnable 사용");
        System.out.println("이유: 상속보다 구성(Composition)이 유리");
    }
}
```

---

## 4. 스레드 생명주기

### 4.1 스레드 상태

```java
public class ThreadStates {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 스레드 상태 ===\n");
        
        Thread thread = new Thread(() -> {
            try {
                System.out.println("RUNNABLE: 실행 중");
                Thread.sleep(1000);
                System.out.println("RUNNABLE: sleep 종료");
            } catch (InterruptedException e) {
                System.out.println("INTERRUPTED");
            }
        });
        
        // NEW
        System.out.println("1. NEW: " + thread.getState());
        
        // RUNNABLE
        thread.start();
        System.out.println("2. RUNNABLE: " + thread.getState());
        
        // TIMED_WAITING
        Thread.sleep(100);
        System.out.println("3. TIMED_WAITING: " + thread.getState());
        
        // TERMINATED
        thread.join();
        System.out.println("4. TERMINATED: " + thread.getState());
        
        
        System.out.println("\n=== 상태 설명 ===");
        System.out.println("NEW:           생성, start() 미호출");
        System.out.println("RUNNABLE:      실행 가능 또는 실행 중");
        System.out.println("BLOCKED:       동기화 블록 대기");
        System.out.println("WAITING:       무한 대기");
        System.out.println("TIMED_WAITING: 시간 제한 대기");
        System.out.println("TERMINATED:    실행 완료");
    }
}
```

### 4.2 상태 전이

```java
public class ThreadStateTransition {
    public static void main(String[] args) {
        System.out.println("=== 상태 전이 다이어그램 ===\n");
        
        System.out.println("        NEW");
        System.out.println("         |");
        System.out.println("      start()");
        System.out.println("         ↓");
        System.out.println("     RUNNABLE ←→ BLOCKED");
        System.out.println("      ↓    ↓       (synchronized)");
        System.out.println("   sleep() wait()");
        System.out.println("      ↓      ↓");
        System.out.println("TIMED_WAITING  WAITING");
        System.out.println("      ↓      ↓");
        System.out.println("   timeout  notify()");
        System.out.println("      ↓      ↓");
        System.out.println("     RUNNABLE");
        System.out.println("         ↓");
        System.out.println("    run() 종료");
        System.out.println("         ↓");
        System.out.println("    TERMINATED");
    }
}
```

---

## 5. 스레드 제어

### 5.1 sleep() - 일시 정지

```java
public class ThreadSleep {
    public static void main(String[] args) {
        System.out.println("=== sleep() 예제 ===");
        
        Thread thread = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("카운트: " + i);
                try {
                    Thread.sleep(1000);  // 1초 대기
                } catch (InterruptedException e) {
                    System.out.println("sleep 중단됨");
                    return;
                }
            }
        });
        
        thread.start();
        
        
        System.out.println("\n=== sleep() 특징 ===");
        System.out.println("- 지정 시간 동안 대기");
        System.out.println("- TIMED_WAITING 상태");
        System.out.println("- InterruptedException 발생 가능");
        System.out.println("- static 메서드 (현재 스레드 대상)");
    }
}
```

### 5.2 join() - 대기

```java
public class ThreadJoin {
    public static void main(String[] args) {
        System.out.println("=== join() 예제 ===");
        
        Thread worker = new Thread(() -> {
            System.out.println("작업 시작");
            try {
                Thread.sleep(2000);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
            System.out.println("작업 완료");
        });
        
        worker.start();
        
        System.out.println("작업자 스레드 대기 중...");
        
        try {
            worker.join();  // worker 종료까지 대기
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        
        System.out.println("모든 작업 완료");
        
        
        System.out.println("\n=== join() 특징 ===");
        System.out.println("- 다른 스레드 종료 대기");
        System.out.println("- WAITING 상태");
        System.out.println("- join(millis) 시간 제한 가능");
    }
}
```

### 5.3 interrupt() - 중단 요청

```java
public class ThreadInterrupt {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== interrupt() 예제 ===");
        
        Thread worker = new Thread(() -> {
            System.out.println("작업 시작");
            try {
                while (!Thread.currentThread().isInterrupted()) {
                    System.out.println("작업 중...");
                    Thread.sleep(500);
                }
            } catch (InterruptedException e) {
                System.out.println("작업 중단됨");
            }
            System.out.println("작업 종료");
        });
        
        worker.start();
        
        Thread.sleep(2000);
        
        System.out.println("작업 중단 요청");
        worker.interrupt();  // 중단 요청
        
        worker.join();
        
        
        System.out.println("\n=== interrupt() 특징 ===");
        System.out.println("- 스레드에 중단 신호 전달");
        System.out.println("- sleep, wait, join 중이면 예외 발생");
        System.out.println("- interrupted 플래그 설정");
        System.out.println("- 강제 종료 아님 (협력적 중단)");
    }
}
```

### 5.4 yield() - 양보

```java
public class ThreadYield {
    static class YieldTask implements Runnable {
        private String name;
        private boolean useYield;
        
        YieldTask(String name, boolean useYield) {
            this.name = name;
            this.useYield = useYield;
        }
        
        @Override
        public void run() {
            for (int i = 0; i < 5; i++) {
                System.out.println(name + ": " + i);
                if (useYield) {
                    Thread.yield();  // 다른 스레드에 양보
                }
            }
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== yield() 예제 ===\n");
        
        Thread t1 = new Thread(new YieldTask("양보O", true));
        Thread t2 = new Thread(new YieldTask("양보X", false));
        
        t1.start();
        t2.start();
        
        
        System.out.println("\n=== yield() 특징 ===");
        System.out.println("- 다른 스레드에 실행 기회 양보");
        System.out.println("- RUNNABLE 상태 유지");
        System.out.println("- 스케줄러에게 힌트 제공");
        System.out.println("- 보장되지 않음 (힌트일 뿐)");
    }
}
```

---

## 6. 데몬 스레드

### 6.1 데몬 스레드 기본

```java
public class DaemonThreadBasic {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 데몬 스레드 ===");
        
        // 일반 스레드
        Thread userThread = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                System.out.println("사용자 스레드: " + i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        });
        
        // 데몬 스레드
        Thread daemonThread = new Thread(() -> {
            while (true) {
                System.out.println("데몬 스레드 실행 중...");
                try {
                    Thread.sleep(300);
                } catch (InterruptedException e) {
                    break;
                }
            }
        });
        daemonThread.setDaemon(true);  // 데몬 설정
        
        userThread.start();
        daemonThread.start();
        
        Thread.sleep(2000);
        System.out.println("\nmain 종료 → 데몬 스레드도 종료");
        
        
        System.out.println("\n=== 데몬 스레드 특징 ===");
        System.out.println("- 백그라운드 작업");
        System.out.println("- 사용자 스레드 종료 시 자동 종료");
        System.out.println("- start() 전에 setDaemon(true) 호출");
        System.out.println("- 예: GC, 모니터링, 로깅");
    }
}
```

### 6.2 데몬 스레드 활용

```java
public class DaemonThreadExample {
    static class AutoSaveThread extends Thread {
        public void save() {
            System.out.println("자동 저장 완료: " + 
                java.time.LocalTime.now());
        }
        
        @Override
        public void run() {
            while (true) {
                try {
                    Thread.sleep(3000);
                    save();
                } catch (InterruptedException e) {
                    break;
                }
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 자동 저장 데몬 ===");
        
        AutoSaveThread autoSave = new AutoSaveThread();
        autoSave.setDaemon(true);
        autoSave.start();
        
        // 메인 작업
        for (int i = 1; i <= 10; i++) {
            System.out.println("작업 진행 중: " + i);
            Thread.sleep(1000);
        }
        
        System.out.println("프로그램 종료");
        // 데몬 스레드는 자동 종료됨
    }
}
```

---

## 7. 스레드 그룹

### 7.1 ThreadGroup 기본

```java
public class ThreadGroupBasic {
    public static void main(String[] args) {
        System.out.println("=== ThreadGroup ===\n");
        
        // 스레드 그룹 생성
        ThreadGroup group = new ThreadGroup("MyGroup");
        
        // 그룹에 스레드 추가
        Thread t1 = new Thread(group, () -> {
            System.out.println("스레드 1 실행");
        }, "Thread-1");
        
        Thread t2 = new Thread(group, () -> {
            System.out.println("스레드 2 실행");
        }, "Thread-2");
        
        t1.start();
        t2.start();
        
        // 그룹 정보
        System.out.println("\n=== 그룹 정보 ===");
        System.out.println("그룹 이름: " + group.getName());
        System.out.println("활성 스레드 수: " + group.activeCount());
        System.out.println("최대 우선순위: " + group.getMaxPriority());
        
        // 스레드 목록
        Thread[] threads = new Thread[group.activeCount()];
        group.enumerate(threads);
        System.out.println("\n스레드 목록:");
        for (Thread t : threads) {
            if (t != null) {
                System.out.println("  - " + t.getName());
            }
        }
    }
}
```

### 7.2 ThreadGroup 제어

```java
public class ThreadGroupControl {
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== ThreadGroup 제어 ===");
        
        ThreadGroup group = new ThreadGroup("Workers");
        
        // 여러 작업 스레드
        for (int i = 1; i <= 3; i++) {
            final int id = i;
            new Thread(group, () -> {
                while (!Thread.currentThread().isInterrupted()) {
                    System.out.println("작업자 " + id + " 실행 중");
                    try {
                        Thread.sleep(500);
                    } catch (InterruptedException e) {
                        break;
                    }
                }
                System.out.println("작업자 " + id + " 종료");
            }, "Worker-" + i).start();
        }
        
        Thread.sleep(2000);
        
        System.out.println("\n모든 작업자 중단 요청");
        group.interrupt();  // 그룹의 모든 스레드 중단
        
        Thread.sleep(1000);
        System.out.println("활성 스레드: " + group.activeCount());
    }
}
```

---

## 8. 실전 예제

### 8.1 파일 다운로더

```java
public class FileDownloader {
    static class DownloadTask implements Runnable {
        private String fileName;
        private int fileSize;
        
        DownloadTask(String fileName, int fileSize) {
            this.fileName = fileName;
            this.fileSize = fileSize;
        }
        
        @Override
        public void run() {
            System.out.println(fileName + " 다운로드 시작");
            
            for (int i = 0; i <= 100; i += 10) {
                System.out.println(fileName + ": " + i + "%");
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    System.out.println(fileName + " 다운로드 취소");
                    return;
                }
            }
            
            System.out.println(fileName + " 다운로드 완료!");
        }
    }
    
    public static void main(String[] args) {
        System.out.println("=== 멀티스레드 다운로더 ===\n");
        
        Thread d1 = new Thread(new DownloadTask("파일1.zip", 100));
        Thread d2 = new Thread(new DownloadTask("파일2.pdf", 50));
        Thread d3 = new Thread(new DownloadTask("파일3.mp4", 200));
        
        d1.start();
        d2.start();
        d3.start();
        
        System.out.println("다운로드 시작됨");
    }
}
```

### 8.2 카운트다운 타이머

```java
public class CountdownTimer {
    static class Timer implements Runnable {
        private int seconds;
        
        Timer(int seconds) {
            this.seconds = seconds;
        }
        
        @Override
        public void run() {
            try {
                for (int i = seconds; i > 0; i--) {
                    System.out.println("남은 시간: " + i + "초");
                    Thread.sleep(1000);
                }
                System.out.println("시간 종료!");
            } catch (InterruptedException e) {
                System.out.println("타이머 중단됨");
            }
        }
    }
    
    public static void main(String[] args) throws InterruptedException {
        System.out.println("=== 카운트다운 타이머 ===\n");
        
        Thread timer = new Thread(new Timer(10));
        timer.start();
        
        // 5초 후 중단
        Thread.sleep(5000);
        System.out.println("\n타이머 중단 요청");
        timer.interrupt();
    }
}
```

### 8.3 생산자-소비자 (기본)

```java
public class ProducerConsumerBasic {
    static class SharedData {
        private int data = 0;
        private boolean hasData = false;
        
        public synchronized void produce(int value) {
            while (hasData) {
                try {
                    wait();
                } catch (InterruptedException e) {
                    return;
                }
            }
            this.data = value;
            this.hasData = true;
            System.out.println("생산: " + value);
            notify();
        }
        
        public synchronized int consume() {
            while (!hasData) {
                try {
                    wait();
                } catch (InterruptedException e) {
                    return -1;
                }
            }
            int value = this.data;
            this.hasData = false;
            System.out.println("소비: " + value);
            notify();
            return value;
        }
    }
    
    public static void main(String[] args) {
        SharedData shared = new SharedData();
        
        // 생산자
        Thread producer = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                shared.produce(i);
                try {
                    Thread.sleep(500);
                } catch (InterruptedException e) {
                    break;
                }
            }
        });
        
        // 소비자
        Thread consumer = new Thread(() -> {
            for (int i = 1; i <= 5; i++) {
                shared.consume();
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    break;
                }
            }
        });
        
        producer.start();
        consumer.start();
    }
}
```

---

## 9. 실전 연습 문제

### 문제 1: 병렬 카운터

```java
// 여러 스레드가 동시에 카운트하는 프로그램
public class Problem1 {
    static class Counter implements Runnable {
        private String name;
        private int limit;
        
        Counter(String name, int limit) {
            this.name = name;
            this.limit = limit;
        }
        
        @Override
        public void run() {
            // 구현
        }
    }
    
    public static void main(String[] args) {
        // 3개 스레드가 각각 1~5까지 카운트
        Thread t1 = new Thread(new Counter("A", 5));
        Thread t2 = new Thread(new Counter("B", 5));
        Thread t3 = new Thread(new Counter("C", 5));
        
        t1.start();
        t2.start();
        t3.start();
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
@Override
public void run() {
    for (int i = 1; i <= limit; i++) {
        System.out.println(name + ": " + i);
        try {
            Thread.sleep(100);
        } catch (InterruptedException e) {
            return;
        }
    }
}
```
</details>

---

### 문제 2: 순차 실행

```java
// 스레드를 순차적으로 실행 (t1 → t2 → t3)
public class Problem2 {
    public static void main(String[] args) throws InterruptedException {
        Thread t1 = new Thread(() -> System.out.println("작업 1"));
        Thread t2 = new Thread(() -> System.out.println("작업 2"));
        Thread t3 = new Thread(() -> System.out.println("작업 3"));
        
        // 구현 (순차 실행되도록)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
t1.start();
t1.join();

t2.start();
t2.join();

t3.start();
t3.join();
```
</details>

---

### 문제 3: 타임아웃 기능

```java
// 작업이 5초 안에 완료되지 않으면 중단
public class Problem3 {
    public static void main(String[] args) throws InterruptedException {
        Thread worker = new Thread(() -> {
            try {
                System.out.println("긴 작업 시작");
                Thread.sleep(10000);  // 10초 작업
                System.out.println("작업 완료");
            } catch (InterruptedException e) {
                System.out.println("작업 타임아웃");
            }
        });
        
        worker.start();
        
        // 구현 (5초 타임아웃)
    }
}
```

**정답:**
<details>
<summary>정답 보기</summary>

```java
worker.start();

worker.join(5000);  // 5초 대기

if (worker.isAlive()) {
    System.out.println("타임아웃! 작업 중단");
    worker.interrupt();
}
```
</details>

---

## 📌 핵심 정리

### Thread 생성 방법
```java
// 1. Thread 상속
class MyThread extends Thread {
    public void run() { }
}

// 2. Runnable 구현
class MyTask implements Runnable {
    public void run() { }
}

// 3. 람다
new Thread(() -> { }).start();
```

### 주요 메서드
```java
// 시작
thread.start()

// 대기
thread.join()
thread.sleep(millis)

// 중단
thread.interrupt()
thread.isInterrupted()

// 양보
Thread.yield()

// 상태
thread.getState()
thread.isAlive()
```

### 스레드 상태
```
NEW → RUNNABLE ↔ BLOCKED
       ↓
   TIMED_WAITING / WAITING
       ↓
   TERMINATED
```

### 데몬 스레드
```java
thread.setDaemon(true);  // start() 전에 호출
thread.isDaemon()
```

### Best Practices
```
✅ Runnable 사용 (Thread 상속보다)
✅ interrupt()로 협력적 종료
✅ join()으로 완료 대기
✅ 데몬 스레드는 백그라운드 작업만
❌ sleep() 중 무시 금지
❌ run() 직접 호출 금지
❌ start() 중복 호출 금지
```

---

<div align="center">

**[⬆ 목차로 돌아가기](../README.md)**

</div>

<div align="center">

**[다음: 02. Synchronization →](./Concurrency-02-Sync.md)**

</div>
