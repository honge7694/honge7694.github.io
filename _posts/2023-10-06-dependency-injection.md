---
title: 의존성 주입을 알아보자
date: 2023-10-06 23:12:30 +09:00
categories: [DevOps, TIL, Spring]
tags: [TIL, Spring, DI]
image: /assets/img/posts/Dependency Injection.png
---

오늘은 항해99 강의를 듣다가 Spring 강의를 봤다.    
스프링을 급하게 배우는 것보다 미리 조금씩 해보는게 낫다 생각하였다.    
항해99 강의는 내일 듣거나 일요일에 몰아 들어야겠다..   
    
Spring 기본 기능 구현 강의를 보면서 개념을 파악하고있는데, 잘 이해가 가지 않는 것들이 많은데 오늘은 무슨 말인지 몰랐지만 자주 나오는 **의존성 주입(Dependency Injection, DI)**에 대해서 정리를 해봐야겠다.


## DI란?
> 소프트웨어 디자인 패턴 중 하나로, 객체 간의 의존성을 외부에서 주입하는 방식이다.     
> 이것은 객체 지향 프로그래밍에서 다른 객체를 사용하는 객체에 필요한 의존성을 제공하는 메커니즘이다.   
> DI는 소프트웨어를 더 모듈화하고 유연하게 만들어 관리하기 쉽게 도와준다.
    
위의 정의만 봐서는 무슨 소리를 하는건지 이해가 가지않아서 예시를 들어가며 이해해봤다.
    
예를 들어 커피 주문을 생각해 보자.
1. 커피 주문 객체는 커피를 만드는 커피 메이커 객체에 의존한다.
2. 이때 의존성 주입이란 커피주문 객체가 커피 메이커 객체를 직접생성하거나 관리하지 않고, 다른 누군가(바리스타)가 필요한 커피 메이커 객체를 주입해주는 것을 의미한다.
    
글로만 보는 것보다 아래와 같이 코드를 보는게 이해가 더 쉽게되었다. 

```java
// 커피 메이커 클래스
public class CoffeeMaker {
    public void makeCoffee() {
        System.out.println("커피를 만듭니다.");
    }
}

// 커피 주문 클래스
public class CoffeeOrder {
    private CoffeeMaker coffeeMaker; // 커피 메이커에 대한 의존성

    // 생성자를 통한 의존성 주입
    public CoffeeOrder(CoffeeMaker coffeeMaker) {
        this.coffeeMaker = coffeeMaker;
    }

    public void serveCoffee() {
        System.out.println("커피 주문을 처리합니다.");
        coffeeMaker.makeCoffee(); // 커피 메이커 사용
    }
}


public class Main {
    public static void main(String[] args) {
        // 커피 메이커 생성
        CoffeeMaker coffeeMaker = new CoffeeMaker();

        // 커피 주문 생성 및 의존성 주입
        CoffeeOrder coffeeOrder = new CoffeeOrder(coffeeMaker);

        // 커피 주문 처리
        coffeeOrder.serveCoffee();
    }
}
```

위의 코드에서 CoffeeOrder는 커피 주문을 처리할 때 CoffeeMaker를 사용하고 있으며, CoffeeMaker 객체는 외부에서 주입되었다.      
    
"**CoffeeOrder 클래스는 CoffeeMaker에 의존**한다." 또는 "**CoffeOrder 클래스가 CoffeeMaker의 의존성을 주입**받았다." 라고 표현한다.     
    
즉, CoffeeOrder 클래스는 어떤 종류의 CoffeeMaker 객체를 주입받더라도 동일한 방식으로 동작하며, 특정 CoffeeMaker를 생성하거나 관리하는 부분은 신경쓰지 않는다는 것이다. 이것이 **의존성 주입의 핵심 아이디어**라고 한다.    
    
예를 들어, 코드에서 CoffeeOrder를 사용할 때 다음과 같이 어떤 종류의 CoffeeMaker 객체를 주입하더라도 CoffeeOrder 클래스는 동일한 방식으로 동작한다.

```java
CoffeeMaker coffeeMaker1 = new CoffeeMaker();
CoffeeOrder coffeeOrder1 = new CoffeeOrder(coffeeMaker1);
coffeeOrder1.serveCoffee(); // coffeeMaker1을 사용하여 커피를 만든다.

CoffeeMaker coffeeMaker2 = new CoffeeMaker();
CoffeeOrder coffeeOrder2 = new CoffeeOrder(coffeeMaker2);
coffeeOrder2.serveCoffee(); // coffeeMaker2를 사용하여 커피를 만든다.

```

CoffeeOrder 클래스는 CoffeeMaker의 구체적인 인스턴스를 생성하는 부분에 대해 신경 쓰지 않으므로, 다양한 CoffeeMaker 구현체를 주입하여 동일한 CoffeeOrder 클래스를 재사용할 수 있다.



### 의존성 주입의 핵심 아이디어
+ 의존성 분리    
DI를 사용하면 클래스 또는 컴포넌트 간의 의존성을 분리할 수 있다.      
이것은 클래스가 직접 다른 클래스를 생성하거나 인스턴스화하지 않도록 하며, 대신 필요한 의존성을 외부에서 주입받는다.
+ 유연성    
의존성이 외부에서 주입되므로 런타임 시에 다른 구현체를 주입하여 동일한 인터페이스를 사용할 수 있다.     이것은 코드를 변경하지 않고도 의존성을 교체하거나 확장할 수 있게 해준다.
+ 테스트 용이성    
DI를 사용하면 의존성을 가짜 객체(Mock 또는 Stub)로 교체하여 테스트하기 쉬워진다.    의존성 주입을 통해 테스트할 코드와 실제 의존성을 분리하여 테스트가 더 쉬워진다.
+ 코드 재사용성    
의존성 주입을 통해 다양한 컴포넌트에서 동일한 의존성을 사용할 수 있으므로 코드 재사용성이 향상된다.


### 의존성 주입의 구현 방식
+ 생성자 주입(Constructor Injection)    
	객체 생성 시 의존성을 주입하는 방식이다.     
    생성자를 통해 필요한 의존성을 전달받는다.
	
	```java
	public class UserService {
		private final UserRepository userRepository;

		// 생성자를 통한 의존성 주입
		public UserService(UserRepository userRepository) {
			this.userRepository = userRepository;
		}

		public void saveUser(User user) {
			userRepository.save(user);
		}
	}
	```


+ Setter 주입(Setter Injection)     
	Setter 메서드를 통해 의존성을 주입하는 방식이다.     
    Setter 메서드를 호출하여 의존성을 설정한다.
	
	```java
		public class ProductService {
			private ProductRepository productRepository;

			// Setter 메서드를 통한 의존성 주입
			public void setProductRepository(ProductRepository productRepository) {
				this.productRepository = productRepository;
			}

			public void addProduct(Product product) {
				productRepository.add(product);
			}
		}
	```


+ 메서드 주입(Method Injection)    
	메서드를 호출할 때 의존성을 주입하는 방식이다.     
    메서드의 매개변수로 의존성을 전달한다.
	```java
	public class NotificationService {
		public void sendNotification(User user, String message) {
			// 메서드 주입
			EmailSender emailSender = new EmailSender();
			emailSender.sendEmail(user.getEmail(), message);
		}
	}

	```
    
    
## 참고
[의존성 주입 3분만에 이해하기](https://www.youtube.com/watch?v=1vdeIL2iCcM)
[DI 쉽게 이해하기 - 추천](https://www.youtube.com/watch?v=WjsDN_aFfyw)
[IoC 이해하기](https://www.youtube.com/watch?v=QrIp5zc6Bo4)