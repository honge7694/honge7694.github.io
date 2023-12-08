---
title: "Override & Overload"
date: 2023-12-07 18:00:00 +09:00
categories: [IT, CS]
tags: [TIL, Override, Overload, CS]
image: /assets/img/posts/CS.png
---

## Override와 Overload

### Override

Override란 부모 클래스에서 상속한 메서드를 자식 클래스에서 필요에 맞게 다시 정의하는 것이다. 즉, 같은 이름의 메서드를 자식 클래스에서 덮어쓰는 것이다.    
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

<br/>

### Overload

Overload는 같은 클래스 내에서 메서드 이름은 같지만 매개변수의 종류나 개수가 다른 여러 메서드를 정의하는 것이다. 이로써 같은 이름의 메서드를 다양한 매개변수로 호출할 수 있다.    반환 유형은 오버로딩에 영향을 미치지 않는다.

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

