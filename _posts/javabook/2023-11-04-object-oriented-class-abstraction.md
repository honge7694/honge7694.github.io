---
title: "[자바 객체 지향의 원리와 이해] 3장 : 추상화"
date: 2023-11-04 18:00:00 +09:00
categories: [IT, Java]
tags: [ java, 자바 객체 지향의 원리와 이해]
image: /assets/img/posts/java-study-book.png
---

## 추상화(Abstraction)
추상화란 **구체적인 것을 분해해서 관찰자가 관심 있는 특성만 가지고 재조합**하는 것이다. 이는 곧 **모델링**이라고도 말하는데 모델을 추상화를 통해 실제 사물을 단순하게 묘사하는 것이다.

<br/>

개발자인 우리가 "사람 클래스"를 만든다면  시력, 키, 몸무게, 혈액형 등의 속성과 먹다, 자다, 일하다, 울다의 메서드 등의 공통된 특성을 찾게된다. 하지만 "사람"의 모든 특성을 나열할 필요는 없다. 개발자가 필요한 "사람"은 **Context(애플리케이션 경계)에 따라 달라 질 수 있다.**
바로 예를 들어보자.    

**병원 애플리케이션**
+ 시력, 키, 몸무게, 혈액형, 나이 등의 속성
+ 먹다(), 자다(), 운동하다() 등의 메서드
 

**은행 애플리케이션**
+  나이, 직업, 연봉 등의 속성
+  출금하다(), 이체하다(), 대출하다() 등의 메서드

<br/>

위처럼 사용하려는 애플리케이션의 목적에 따라 필요한 특성이 달라진다. 우리는 애플리케이경계를 알기 위해서 "내가 만들고자 하는 애플리케이션은 어디에서 사용될 것인가?"라는 물음을 던져 명확하게 설계하도록 해야한다.

####  **추상화 = 모델링 = 자바의 class 키워드**

<br/>

### 추상화 실습
클래스와 객체 관계를 자바에서는 다음과 같이 표현한다.    
`클래스 객체_참조_변수 = new 클래스();`    
위 코드의 내용을 좀 더 상세히 살펴보면 다음 표와 같다.
![추상화 메모리 표1](https://github.com/honge7694/honge7694.github.io/assets/76715487/df60bd8d-9555-4d46-8e4f-1b96292dbb21)

위의 표를 예시로 들면서, 추상화 실습을 진행해보자. 

![추상화 메모리 표2](https://github.com/honge7694/honge7694.github.io/assets/76715487/9fffb845-deb0-42ab-9029-7a43191de2b4)

쥐 캐릭터 객체들을 관찰하여 클래스를 설계(추상화)한다면 필요한 것이 무엇일까? 바로, 성명, 나이, 종교 등의 속성들과 달리다, 먹다 등 행위들일 것이다. 이것들을 코드로 나타내면 아래와 같다.

```java
public class Mouse {
	public String name;
	public int age;
	public int countOfTail;
	
	public void sing() {
		System.out.println(name + "찍찍");
	}
}
```

<br/>

### T메모리
그렇다면, 위의 코드를 실행시키면 메모리에서는 어떻게 될까?
```java
public class MouseDriver{
	public static void main(String[] args){
		Mouse mickey = new Mouse();
		mickey.name = "미키";
		mickey.age = 85;
		mickey.sing();
        
		Mouse jerry = new Mouse();
        
		jerry.name = "제리";
		jerry.age = 73;    
		jerry.sing()
   }
}
```

1. main 메서드가 실행될 때, `mickey`와 `jerry`라는 두 개의 `Mouse` 클래스의 인스턴스가 힙 메모리에 생성된다.
2. `mickey` 객체와 `jerry`객체가 생성되고 힙 메모리에 저장된다. 
3. `main` 메서드가 종료되면 `mickey`와 `jerry` 변수는 스택 메모리에서 삭제되지만 힙 메모리에 생성된 객체들은 삭제되지 않는다.
4. 가비지 컬렉터는 일정한 주기로 실행되며, 사용되지 않는 객체를 확인하고 힙 메모리에서 정리한다. 객체가 삭제되면 해당 객체가 차지하던 메모리 공간은 다른 객체에 할당될 수 있다.

<br/>

### 클래스 멤버 vs 객체 멤버 = static 멤버 vs 인스턴스 멤버


