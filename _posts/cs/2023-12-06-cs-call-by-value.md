---
title: "Call by Value, Call by Reference"
date: 2023-12-06 18:00:00 +09:00
categories: [IT, CS]
tags: [til, cs, call by reference]
image: /assets/img/posts/cs.png
math: true
---


$$
자바에서는 Call by Refernce 개념이 없다.
왜냐하면 C와 달리 자바에서는 포인터를 철저하게 숨겨 개발자가 직접 메모리 주소에 접근하지 못하게 조치했기 때문이다. 자바에서의 파라미터는 Call by Value로서만 동작되며, 원시값이 복사 되느냐 주소값이 복사되느냐 차이가 있을 뿐이다. 그리고 매개변수에 복사된 값에 따라, 원시값이면 바로 연산을하고 주소값이면 해당 메모리 주소를 참조해 값을 가져와 연산한다.
$$



***



## Call by Value, Call by Reference
함수의 매개변수에서 **값을 복사** 또는 **주소값을 참조** 하는 것에 따라 반환 결과가 달라진다. 자바의 데이터형을 알아보면 크게 두가지로 나뉘게 된다.

+ 기본형
	+ Boolean Type(boolean), Numeric Type(short, int, long, float, double, char)
+ 참조형
	+ Class Type, Interface Type, Array Type, Enum Type, 기본형을 제외한 모든 것들

```java
public class Main {
    public static void main(String[] args) {

        int var = 1;
        int[] arr = { 1, 2, 3 };

        // 변수 자체를 보냄 call by value
        add_value(var);
        System.out.println(var); // 1 : 값 변화가 없음

        // 배열 자체를 보냄 (call by reference)
        add_reference(arr);
        System.out.println(Arrays.toString(arr)); // 101, 102, 103 : 값이 변화함
    }

    static void add_value(int var_arg) {
        var_arg += 100;
    }

    static void add_reference(int[] arr_arg) {
        Arrays.setAll(arr_arg, i -> arr_arg[i] + 100);
    }
}
```



### 1. Call by Value
Call by Value는 메서드를 호출할 때 값을 넘겨주기 때문에 Pass by Value라고도 부른다.    
메서드를 호출하는 호출자(Caller)의 변수와 호출 당하는 수신자(Calle)의 파라미터는 복사된 **서로 다른 변수**이다.      
값만을 전달하기 때문에 수신자의 파라미터를 수정해도 호출자의 변수에는 아무런 영향이 없다. 그래서 위의 `var` 변수가 변화가 없는 것이다.

### 2. Call by Reference
Call by Reference는 참조(주소)를 직접 전달하여 Pass By Reference라고도 부른다.    
참조를 직접 넘기기 때문에 호출자의 변수와 수신자의 파라미터는 **완전히 동일한 변수**이다.    메서드 내에서 파라미터를 수정하면 그대로 원본 변수에도 반영된다. 그래서 위의 `arr` 변수에도 변경된 값으로 변화하게 된다.

  
> 참조값(주소값) 복사는 배열 뿐만 아니라 클래스, 리스트, 맵 등 원시(primitive)타입이 아닌 모든 타입에 대해서 적용된다.
{: prompt-tip }


## Call by Value 과정

### 1. main 스택 프레임에 두 변수가 담기게 된다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/8cf527f1-a07a-42c9-a304-38c408eb5290)

Java에서 변수를 선언하면 Stack 영역에 할당된다.

<br/>

원시타입(Primitive Type)은 Stack 영역에 변수와 함께 저장되며, main 스택 프레임에 저장된다.

<br/>

참조타입(Reference Type) 객체의 실제 데이터는 Heap 영역에 저장되고 이를 참조할 주소값을 Stack 영역에 저장하게 된다.

> Info    
**[ 스택 프레임 (stack frame) ]**    
하나의 메서드에 필요한 메모리 덩어리를 묶어서 스택 프레임이라고 한다.     
하나의 메서드당 하나의 스택 프레임이 필요하며, 메서드를 호출하기 직전 스택프레임을 자바 Stack에 생성한 후 메서드를 호출하게 된다.    
스택 프레임에 쌓이는 데이터는 메서드의 매개변수, 지역변수, 리턴값 등이 있다.     
만일 메서드 호출범위가 종료되면 스택에서 제거된다.    
{: prompt-info }


### 2. add_value() 메서드 - call by value

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/12111c06-e4be-4f36-82ea-dc79bb935ec8)

1. `add_value()` 메서드가 호출되면서, add_value 스택 프레임이 생성되고 그 안에 지역 변수(매개변수) var_arg가 값을 1을 받은채 생성되게 된다.
2. 그리고 자체 메서드 로직으로 100을 더해 값은 101이 된다.

<br/>

**add_value 스택 프레임 안에 있는 변수 `var_arg`가 바뀐 것이지, main 스택 프레임 안에 있는 변수 `var`가 바뀐 것이 아니다**.    
매개 변수 `var_args`는 그저 변수 `var`로 부터 원시값을 복사하여 받은 것 뿐이다.(call by value)     즉, 메인에 정의 되어 있는 `var`변수와 `add_value`메서드에 정의되어 있는 `var_arg`변수는 서로 다른 것이다.

### 3. add_reference() 메서드 - call by reference

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/44c84657-4e11-438b-857d-f00a4575f025)

1. `add_reference()` 메서드가 호추로디면서, add_reference 스택 프레임이 생성되고 그 안에 지역변수(매개변수) `arr_arg`가 생성된다.
2. 이때도 전의 `add_value()`메서드 호출 때 처럼 변수의 값이 복사되어 파라미터에 넘겨지는데, 스택 프레임에 생성되는 `arr`변수가 들고 있는 값은 **주소값**이다.

<br/>

결과적으로 두 변수 `arr`, `arr_arg`는 같은 주소값을 들고 있게 되는 것이고, 이 주소가 가리키는 메모리는 같기 때문에 두변수는 하나의 데이터를 동시에 참조하고 있다고 말 할 수 있다.





## 참고
+ [Inpa Dev](https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EC%9E%90%EB%B0%94%EB%8A%94-Call-by-reference-%EA%B0%9C%EB%85%90%EC%9D%B4-%EC%97%86%EB%8B%A4-%E2%9D%93#%EC%9E%90%EB%B0%94%EC%9D%98_call_by_value_/_call_by_reference)
+ [T블로그](https://bcp0109.tistory.com/360)