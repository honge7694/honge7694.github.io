---
title: "SpringBootTest vs Mock Test"
date: 2025-03-29 18:00:00 +09:00
categories: [IT, Spring]
tags: [til, spring boot, test]
image: /assets/img/posts/spring.png
---



- 통합 테스트(Integration Test)
	`@SpringBootTest` + `@Transational`
- 단위 테스트 (Unit Test)
	`@Mock`
    
각각 어떤 차이가 있는지 정리해보려고 한다.

## 통합 테스트 @SpringBootTest + @Transactional

`@SpringBootTest`는 스프링 어플리케이션의 전체 컨텍스트를 로딩하여 테스트를 수행한다.
이는 실제 어플리케이션 실행환경과 유사한 환경을 제공하여 전체적인 통합 테스트를 가능하게 한다.

> 주목적    
> - 어플리케이션 전체 동작을 검증
> - 실제 데이터 베이스 연결을 통한 Repository, Service 레이어의 통합 테스트 수행

### 장점

- 실제 애플리케이션과 매우 유사한 환경에서 동작 검증
- 데이터베이스 연동 및 트랜잭션이 정확하게 작동하는지 확인 가능
- Repository, Service, Controller 등 전체 로직을 함께 검증 가능

### 단점

- 전체 컨텍스트를 로딩하므로 수행 속도가 느림
- 간단한 로직이나 Repository 단위 테스트에는 부적합할 수 있음

### 테스트에서  `@Transactional`이란?
테스트 메서드를 실행할 때 트랜잭션을 자동으로 시작하고, 테스트 종료 후 롤백한다.    
왜 `@Transaction` 이 rollback이 되는가?    
[`@Rollback`](https://docs.spring.io/spring-framework/reference/testing/annotations/integration-spring/annotation-rollback.html) 에 접속하여 보면 다음과 같은 설명이 있다.

> @Rollback    
@Rollback indicates whether the transaction for a transactional test method should be rolled back after the test method has completed. If true, the transaction is rolled back. Otherwise, the transaction is committed (see also @Commit). Rollback for integration tests in the Spring TestContext Framework defaults to true even if @Rollback is not explicitly declared.    
> When declared as a class-level annotation, @Rollback defines the default rollback semantics for all test methods within the test class hierarchy. When declared as a method-level annotation, @Rollback defines rollback semantics for the specific test method, potentially overriding class-level @Rollback or @Commit semantics.    
> The following example causes a test method’s result to not be rolled back (that is, the result is committed to the database):
{: .prompt-info }

```java
protected final boolean isRollback(TestContext testContext) throws Exception {
    boolean rollback = this.isDefaultRollback(testContext);  // 1️⃣ 기본 롤백 여부 확인

    Method testMethod = testContext.getTestMethod();  // 2️⃣ 현재 실행 중인 테스트 메서드 가져오기
    Rollback rollbackAnnotation = (Rollback) AnnotatedElementUtils.findMergedAnnotation(testMethod, Rollback.class);

    if (rollbackAnnotation != null) {  // 3️⃣ @Rollback 어노테이션이 존재하면 값 가져오기
        boolean rollbackOverride = rollbackAnnotation.value();
        
        if (logger.isTraceEnabled()) {
            logger.trace("Method-level @Rollback(%s) overrides default rollback [%s] for test context %s"
                .formatted(rollbackOverride, rollback, testContext));
        } else if (logger.isDebugEnabled()) {
            logger.debug("Method-level @Rollback(%s) overrides default rollback [%s] for test method [%s]"
                .formatted(rollbackOverride, rollback, testMethod));
        }

        rollback = rollbackOverride;  // 4️⃣ 어노테이션 값이 있으면 기존 rollback 값을 덮어씀
    } else if (logger.isTraceEnabled()) {
        logger.trace("No method-level @Rollback override: using default rollback [%s] for test context %s"
            .formatted(rollback, testContext));
    } else if (logger.isDebugEnabled()) {
        logger.debug("No method-level @Rollback override: using default rollback [%s] for test method [%s]"
            .formatted(rollback, testMethod));
    }

    return rollback;  // 5️⃣ 최종 결정된 rollback 값 반환
}

```

코드를 CHAT GPT에 물어보았고, 자동으로 rollback 되는 이유는? 다음 과같은 순서로 된다고 한다.
1. TransactionalTestExecutionListener가 spring.factories에서 로드되어,
2. beforeTestMethod()에서 트랜잭션을 시작하고,
3. afterTestMethod()에서 자동 롤백하도록 동작하기 때문이다.

### 왜 Rollback이 필요한가?

테스트 데이터를 Commit을 한다면?
- 각 테스트 메서드가 실행될 때마다 더미 데이터가 쌓인다.
- 이전 테스트가 남긴 데이터가 다음 데이터에 영향을 준다.
-  **테스트가 독립적이지 않게 되어 결과 불안정**
Rollback의 장점
- **독립성 유지** : 각 테스트가 DB의 동일한 상태에서 시작된다.
- **테스트 신뢰성 향상**: 항상 동일한 결과를 보장하여 신뢰성이 향상된다.

### 예시 코드

```java
@SpringBootTest
@Transactional
class JpaApplicationTests {

    @Autowired
    private MemberRepository memberRepository;  // Mock 대신 실제 Repository 사용

    @Test
    void contextLoads() {
        // Given
        Member member = new Member();
        member.setUsername("member A");

        // When
        Long saveId = memberRepository.save(member);
        Member findMember = memberRepository.find(saveId);

        // Then
        Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());
        Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
    }
}

```
    

<br/>

---

## 단위 테스트 `@Mock`


- `@Mock`은 Mockito와 같은 Mocking 프레임워크에서 제공하는 기능으로, 특정 클래스, 인터페이스의 동작을 가짜로 흉내내어 사용하는 것을 의미한다.
- Repository나 외부 의존성을 가진 객체의 동작을 원하는 대로 설정하여 특정 메서드의 반환값을 조작할 수 있다.

> **Mocking의 주요 개념**    
> - Mock 객체	 : 가짜 객체로, 실제 객체 대신 로직이나 반환값을 프로그래머가 지정할 수 있다.    
> - Stubbing	 : 메서드의 동작이나 리턴 값을 임의로 지정하는 행위이다.    
> - Verify 			: Mock 객체가 실제로 호출되었는지, 호출 횟수와 순서를 검증한다.

### 장점

- 테스트가 빠르다
- 외부 의존성을 제거하므로 독립적인 테스트가 가능하다.

### 단점

- 실제 로직을 테스트하는 것이 아니라 Mock 객체를 통해 구현한 동작만 검증할 수 있다.
- DB 연동이나 실제 환경과의 차이가 있을 수 있다.


### 예시 코드

```java
@ExtendWith(MockitoExtension.class)
class JpaApplicationTests {

    @Mock
    private MemberRepository memberRepository;

    @Test
    @Transactional
    void contextLoads() {
        // Given
        Member member = new Member();
        member.setId(1L);
        member.setUsername("member A");

        when(memberRepository.save(any(Member.class))).thenReturn(member.getId());
        when(memberRepository.find(anyLong())).thenReturn(member);

        // When
        Long saveId = memberRepository.save(member);
        Member findMember = memberRepository.find(saveId);

        // Then
        Assertions.assertThat(findMember.getId()).isEqualTo(member.getId());
        Assertions.assertThat(findMember.getUsername()).isEqualTo(member.getUsername());
    }

}
```


## 언제 Mock을 쓰고, 언제 SpringBoot를 쓸까?

### Mock을 사용해야 하는 경우

- Repository나 Service를 독립적으로 테스트하고 싶을 때
- DB와의 상호작용 없이 빠르게 테스트하고 싶을 때
- 특정 메서드의 동작을 원하는 대로 시뮬레이션해야 할 때 (예: when(...).thenReturn(...))

### SpringBootTest를 사용해야 하는 경우

- 실제 JPA, EntityManager, 트랜잭션 동작을 확인하고 싶을 때
- 실제 Spring 환경에서 Integration Test를 할 때
- H2 같은 테스트용 DB에서 실제 데이터를 저장하고 조회해야 할 때