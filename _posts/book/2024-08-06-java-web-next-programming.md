---
title: "[자바 웹 프로그래밍 Next Step] 2장 - 문자열 계산기 구현을 통한 테스트와 리펙토링"
date: 2024-08-06 18:00:00 +09:00
categories: [IT, TIL, Spring]
tags: [til, spring, 자바 웹 프로그래밍 Next Step]
image: /assets/img/posts/til.png
---

# 2장. 문자열 계산기 구현을 통한 테스트와 리팩토링

#### 목표
테스트와 리팩토링을 학습하는 것과 더불어 자바 개발 환경을 익히고, 책의 실습 진행 방식을 겸험한다.

## JUnit을 활용한 테스트

[JUnit](http://junit.org)은 단위 테스트 프레임워크 중 하나로, main() 메소드의 한계를 해결해줄 수 있는 도구이다.    

> **main() 메소드의 한계란?**  
> 1. 프로덕션 코드와 테스트 코드가 같은 클래스에 위치하는 문제    
> 2. 하나의 main() 메소드에서 프로덕션 코드의 여러 메소드를 동시에 실행
> 3. 콘솔에 출력되는 값을 통해 수동으로 확인
{: .prompt-info }

### main() 메소드의 한계

+ 1. 프로덕션 코드와 테스트 코드가 같은 클래스에 위치하는 문제
```java
public class Calculator {
	...
	public static void main(String[] args) {
		...
	}
}
```

Calculator 클래스와 CalculatorTest로 분리

+ 2. 하나의 main() 메소드에서 프로덕션 코드의 여러 메소드를 동시에 실행
+ 3. 콘솔에 출력되는 값을 통해 수동으로 확인

```
public class Calculator {
	int add (int i, int j)	{
		return i + j;
	}
	
	int subtract(int i, int j) {
		return i - j;
	}
	public static void main(String[] args) {
		Calculator cal = new CalCulator();
		// 2. 
		System.out.println(cal.add(3, 4)); // 3.
		System.out.println(cal.subtract(5, 4)); // 3.
	}
}
```



### JUnit 활용

위의 코드를 src/main에서 진행하지말고, src/test에서 진행하게 되면 결과값을 프로그램을 통해 확인이 가능하다.
  

```java
class CalculatorTest {
		@DisplayName("계산기 - 덧셈")
    @Test
    void add() {
        private Calculator cal = new Calculator();
        assertEquals(9, cal.add(6, 3));
    }

    @DisplayName("계산기 - 뺄셈")
    @Test
    void subtract() {
        private Calculator cal = new Calculator();
        assertEquals(3, cal.subtract(6, 3));
    }
}
```

위처럼 진행하면 중복되는 코드가 생긴다.     
`private Calculator cal = new Calculator();`    
이것을 해결하기 위해 class의 인스턴스 변수로 선언하는 방법이 있지만, Test 코드에서는 `@BeforeEach`를 사용하는 것을 추천한다.

```
class CalculatorTest {
    private Calculator cal;

    @BeforeEach
    public void setUp() {
        cal = new Calculator();
    }

		@DisplayName("계산기 - 덧셈")
    @Test
    void add() {
        assertEquals(9, cal.add(6, 3));
    }

    @DisplayName("계산기 - 뺄셈")
    @Test
    void subtract() {
        private Calculator cal = new Calculator();
        assertEquals(3, cal.subtract(6, 3));
    }
}
```

왜냐하면 JUnit에는 `@BeforeEach` 말고도 다양한 어노테이션이 있는데, `@RunWith`, `@Rule`은 `@Before`에서 초기화된 객체에만 접근이 가능하다는 제약사항이 있기때문이다.

| 어노테이션            | 설명                                                                                  |
|-------------------|-------------------------------------------------------------------------------------|
| `@Test`           | 테스트 메서드를 표시하여 테스트 프레임워크에 의해 실행되도록 한다.                                 |
| `@ParameterizedTest` | 매개변수를 사용하여 여러 번 실행할 수 있는 테스트 메서드를 표시한다.                                      |
| `@RepeatedTest`   | 테스트 메서드를 지정된 횟수만큼 반복 실행한다.                                                        |
| `@TestFactory`    | 동적으로 테스트 케이스를 생성하여 반환하는 메서드를 표시한다.                                           |
| `@TestTemplate`   | 여러 다른 시나리오에서 테스트를 실행할 때 사용하며, 여러 번 호출될 수 있는 메서드를 표시한다.                     |
| `@DisplayName`    | 테스트 클래스 또는 테스트 메서드의 이름을 지정하여, 테스트 보고서에 표시될 이름을 설정한다.                       |
| `@BeforeEach`     | 각 테스트 메서드가 실행되기 전에 실행되는 메서드를 표시하여, 테스트 환경을 설정한다.                         |
| `@AfterEach`      | 각 테스트 메서드가 실행된 후에 실행되는 메서드를 표시하여, 테스트 환경을 정리한다.                          |
| `@BeforeAll`      | 테스트 클래스 내의 모든 테스트 메서드가 실행되기 전에 한 번 실행되는 메서드를 표시하며, 정적 메서드여야 한다.                |
| `@AfterAll`       | 테스트 클래스 내의 모든 테스트 메서드가 실행된 후에 한 번 실행되는 메서드를 표시하며, 정적 메서드여야 한다.                 |
| `@Nested`         | 테스트 클래스 내에 중첩된 테스트 클래스를 정의할 때 사용하며, 논리적으로 그룹화된 테스트를 만든다.                      |
| `@Tag`            | 테스트에 태그를 추가하여 테스트를 그룹화하거나 필터링할 때 사용한다.                                           |
| `@Disabled`       | 특정 테스트 메서드나 클래스 전체를 비활성화하여 실행되지 않도록 한다.                                  |
| `@Timeout`        | 테스트 메서드의 최대 실행 시간을 지정하며, 지정된 시간 내에 테스트가 완료되지 않으면 실패로 처리한다.                  |
| `@ExtendWith`     | JUnit 5 확장 모델을 사용하여 테스트 클래스에 확장을 등록할 때 사용한다.                                    |
| `@TempDir`        | 테스트 중 임시 디렉토리를 생성하고 이를 주입할 때 사용한다.                                                  |

## 실습 - 문자열 계산기 요구사항


```
/**
 * 1. 쉼표 또는 콜론 구분자로 가지는 문자열로 분리
 * 2. // \n 사이에 있는 커스텀 구분자
 * 3. 음수를 전달하는 경우 RuntimeException
 * 4. 빈문자열은 0을 리턴
 */

public class Calculator {
    int add(String txt) {
        Matcher m = Pattern.compile("//(.)\\n(.*)").matcher(txt);
        if (txt.isEmpty()) {
            return 0;
        } else if(m.find()) {
            String matchChar = m.group(1);
            String[] splitTxt = m.group(2).split(matchChar);
            return sumResult(splitTxt);
        } else {
            String[] splitTxt = txt.split("[,:]");
            return sumResult(splitTxt);
        }
    }

    private int sumResult(String txt[]) {
        int sum = 0;
        for (String ch : txt) {
            int num = Integer.parseInt(ch);
            if (num < 0) {
                throw new RuntimeException();
            }
            sum += num;
        }
        return sum;
    }

    public static void main(String[] args) {
    }
}
```

실습 문제를 풀며 나는 위와 같이 풀었는데, 책에서는 리팩토링을 거쳐 굉장히 코드를 깔끔하게 정리해놨다.

### 리팩토링

1. 메소드가 한가지 책임만 가지도록 구현한다.
2. 인덴트(indent, 들여쓰기) 깊이를 1단계로 유지한다.
	+ 인덴트는 while문과 if문을 사용할 경우 인덴트 깊이가 1씩 증가한다.
3. else를 사용하지 마라. 프로그래밍을 구현할 때 else를 사용하지 않고 프로그래밍을 하는 것이 가능하다.

모든코드를 위 세가지의 원칙을 지키면서 구현하기는 어렵지만, 최대한 지키려고 노력하면 깔끔한 코드 작성이 가능하다.




