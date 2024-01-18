---
title: "Overriding & Overloading"
date: 2023-12-07 18:00:00 +09:00
categories: [IT, CS]
tags: [til, overriding, overloading, cs]
image: /assets/img/posts/cs.png
---


## Overriding와 Overloading

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/2fcb0493-d77f-486f-b69c-d878cb78f179)

### Overriding

`Overriding`이란 부모 클래스에서 상속한 메서드를 자식 클래스에서 필요에 맞게 다시 정의하는 것이다. 즉, 같은 이름의 메서드를 자식 클래스에서 덮어쓰는 것이다.    
이때 메서드의 이름, 매개변수 형식, 반환 유형이 부모 클래스와 일치해야 한다.    
`@Override` 어노테이션을 사용하여 명시적으로 재정의되었음을 나타낸다.

```java
class Animal {
	void makeSound() {
		System.out.println("Some generic sound");
	}
}

class Dog extends Animal {
	@Override
	void makeSoundI() {
		System.out.println("멍멍!");
	}
}
```

### 적용 사례
interceptor를 사용하여 jwt 인가를 구현하면서 사용한 경험이 있습니다. interceptor를 사용하여 특정 경로 또는 요청에 대한 인가 로직을 커스터마이징하기 위해 사용한 경험이 있습니다. 

#### 예상 꼬리 질문

+ `intercepter`와 `filter`의 차이
	+  `Interceptor`는 Spring의 컨텍스트에서 동작하며, Spring 빈을 주입받을 수 있습니다. 따라서 `Interceptor` 내에서 Spring Bean에 의존하는 작업을 수행하기 편리합니다. 반면에 `Filter`는 Servlet의 일부로서 Spring 빈에 직접적으로 접근하기 어렵습니다.


<br/>

### Overloaing

`Overloading`은 같은 클래스 내에서 메서드 이름은 같지만 매개변수의 종류나 개수가 다른 여러 메서드를 정의하는 것이다. 이로써 같은 이름의 메서드를 다양한 매개변수로 호출할 수 있다.    반환 유형은 오버로딩에 영향을 미치지 않는다.

```java
class Calulator {
	int add(int a, int b) {
		return a + b;
	}
	
	double add(double a, double b) {
		return a + b;
	}
}
```
 
간단하게 설명하면, Override는 상속에서 부모 클래스의 메서드를 자식 클래스에서 다시 정의하는 것이고, Overloading은 같은 클래스에서 같은 이름의 메서드를 다양한 매개변수로 다르게 정의하는 것이다.


> 나의 답     
Overriding이란 부모 클래스에서 상속한 메서드를 자식 클래스에서 필요에 맞게 다시 정의하는 것이다. 즉, 같은 이름의 메서드를 자식 클래스에서 덮어쓰는 것이다.    
이때 메서드의 이름, 매개변수 형식, 반환 유형이 부모 클래스와 일치해야 한다.     
Overloading은 같은 클래스 내에서 메서드 이름은 같지만 매개변수의 종류나 개수가 다른 여러 메서드를 정의하는 것이다. 이로써 같은 이름의 메서드를 다양한 매개변수로 호출할 수 있다. 
{ .prompt-warning }


> 모범 답안    
Override와 Overload는 객체 지향 프로그래밍에서 다형성을 구현하는 두 가지 개념입니다. Override는 상속관계에 있는 클래스에 적용되는 개념으로, 하위 클래스가 상위 클래스의 메서드를 재정의하는 것을 의미합니다. 메서드 이름과 매개변수는 유지하되, 메서드 내부의 로직을 상속받은 하위클래스에 알맞게 다시 정의하는게 Override입니다. Overload는 같은 클래스 내에서 적용되는 개념으로, 두 메서드의 이름이 같더라도 입력받는 매개변수의 유형과 갯수가 다르다면 서로 다른 메서드로 취급되는 것을 의미합니다. 이 두 개념은 모두 코드의 가독성과 유연성을 높히는데 큰 도움을 줍니다. 
{ .prompot-info }


<br/>

***

<br/>