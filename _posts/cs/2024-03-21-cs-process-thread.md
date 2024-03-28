---
title: "프로세스와 스레드"
date: 2024-03-21 18:00:00 +09:00
categories: [IT, CS]
tags: [til, java, cs, thread, process]
image: /assets/img/posts/CS.png
---

## 프로세스란?

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/da68d2cf-db14-4f01-94a2-1d0084e82a7e)

단순히 실행 중인 프로그램이라고 할 수 있다. 즉, 사용자가 작성한 프로그램이 OS에 의해 메모리 공간을 할당받아 실행 중인 것을 말한다.      
예를들어 카카오톡, 브라우저, JAVA프로그램 모두 프로세스로 실행되며 브라우저를 2개 띄우면 브라우저 프로세스도 2개가 띄워진 것이다.

<br/>

### 프로세스의 구조

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/265621a9-4f06-4b6e-a860-2387d9bc6204)

OS가 프로그램 실행을 위한 프로세스를 할당해 줄 때 프로세스 안에 프로그램 Code와 Data 그리고 메모리 영역(Stack, Heap)을 함께 할당해준다.

+ Code는 Java main 메소드와 같은 코드를 말한다.
+ Data는 프로그램이 실행 중 저장할 수 있는 저장 공간을 의미한다.
	+ 전역변수, 정적변수(static), 배열 등 초기화 된 데이터를 저장하는 공간
+ 메모리 영역
	+ Stack : 지역변수, 매개변수, 리턴변수를 저장하는 공간
	+ Heap : 프로그램이 동적으로 필요한 변수를 저장하는 공간(new(), mallock())

<br/>

## 스레드

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/ae732f62-224d-4e36-9fb0-ef99858190ff)

프로세스 내에서 실제로 작업을 수행하는 주제를 의미한다. 모든 프로세스에는 한 개 이상의 스레드가 존재하여 작업을 수행한다.     
또한, 두개 이상의 스레드를 가지는 프로세스를 멀티스레드 프로세스라고 한다.     
프로세스 안에는 여러 스레드들이 있고, 스레드들은 실행을 위한 프로세스 내 주소 공간이나 메모리 공간(Heap)을 공유받으며, 각각 명령처리를 위한 자신만의 메모리공간(Stack)도 할당 받는다.

> 크롬 브라우저의 탭은 프로세스? 스레드?    
> 크롬 브라우저를 실행시키는 것은 하나의 프로세스가 동작하는 것이고, 각각의 탭은 스레드라고 할 수 있다.        
> 하나의 탭에서 문제가 발생해도 다른 탭이나 브라우저 자체에 영향을 미치지 않도록 한다.
{: .prompt-info }


<br/>

### Java 스레드

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/edcdac66-401d-4a6a-affc-ab317823d639)

Java 프로그램을 실행하면 JVM 프로세스 위에서 실행되고, Java Main 스레드부터 실행되며 JVM에 의해 실행된다.

## Java Thread 구현

### 1. Thread Class 상속
`run()` 메서드에 작성된 코드가 스레드가 수행할 작업이다.

```java
public class TestThread extends Thread {
    @Override
    public void run() {
//        super.run();
        // 실제 스레드에서 수행할 작업
        for (int i = 0; i < 100; i++) {
            System.out.print("*");
        }
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        TestThread thread = new TestThread();
        thread.start();
    }
}

// 결과
// ***...*
```

### 2. Runalbe interface
 class 상속 보다 더 많이 사용하게 된다. 이유는 다중 상속이 가능하기 때문이다.

```java
public class TestRunnable implements  Runnable{

    @Override
    public void run() {
        // 수행할 작업
        for (int i = 0; i < 100; i++) {
            System.out.print("$");
        }
    }
}
```

```java
public class Main {
    public static void main(String[] args) {
        Runnable run = new TestRunnable();
        Thread thread = new Thread(run);
		
        thread.start();
    }
}
```

### 3. lambda

```java
public class Main {
    public static void main(String[] args) {
        Runnable task = () -> {
            int sum = 0;
            for (int i = 0; i < 50; i++) {
                sum += i;
                System.out.print(sum);
            }
            System.out.println(Thread.currentThread().getName() + " 최종 합 : " + sum);
        };

        Thread thread1 = new Thread(task);
        thread1.setName("thread1");
        Thread thread2 = new Thread(task);
        thread2.setName("thread2");

        thread1.start();
        thread2.start();
    }
}

// 결과
0
1
3
6
0
1
3
6
10
15
21
thread1 최종 합 : 1225
thread2 최종 합 : 1225
```

+ `.setName("")`으로 thread의 이름을 선언할 수 있다.
+ `Thread.currentThread().getName()` 은 현재 실행 중인 쓰레드의 이름을 반환


> 단일스레드와 멀티스레드    
> 위와 같이 실행했을 때 print의 결과가 완료되기 전에 다시 0부터 시작하는 것을 볼 수 있다.     > 여러개의 작업을 실행하는 것인데, 순서가 정해져 있지 않아 출력의 형태가 계속해서 변화하는 것을 확인할 수 있고, 이 두 스레드의 실행 순서 또는 시간은 OS의 스케줄러가 처리하기 때문에 알 수 없다.
{: .prompt-info }