---
title: "N+1 문제의 발생 이유와 해결 방법"
date: 2023-12-21 18:00:00 +09:00
categories: [IT, CS]
tags: [TIL, CS, N+1 문제]
image: /assets/img/posts/cs.png
---


# N + 1
연관 관계가 설정된 엔티티를 조회할 경우에 조회된 데이터 갯수(n) 만큼 연관관계의 조회 쿼리가 추가로 발생하여 데이터를 읽어오는 현상이다.

## 문제 발생 이유
N+1 문제는 ORM(Object-Relational Mapping)을 사용하는 경우 발생할 수 있는 성능 이슈 중 하나입니다. 이 문제는 데이터베이스에서 엔티티를 조회할 때, 연관된 엔티티를 함께 로딩하는 대신에, 각각의 연관된 엔티티를 별도로 추가적인 쿼리로 로딩하는 상황에서 발생합니다.

<br/>

예를 들어, 부모 엔티티를 조회할 때 해당 부모 엔티티와 연관된 자식 엔티티들이 N+1번의 추가 쿼리로 따로 로딩되는 상황이 발생합니다.

## 해결 방법

### 1. Fetch Join
N+1 자체가 발생하는 이유는 한쪽 테이블만 조회하고 연결된 다른 테이블은 따로 조회하기 때문입니다. 미리 두 테이블을 JOIN 하여 한 번에 모든 데이터를 가져올 수 있다면  N+1 문제가 발생하지 않을 것입니다.
@Queyry 어노테이션과 JPQL을 사용하여 DB에서 데이터를 가져올 때 처음부터 연관된 데이터까지 같이 가져오게 하는 방법입니다.
```
public interface TestRepository extends JpaRepository<Test, Long> {
	@Query("select t from test t join fetch t.users")
	List<Test> findAllFetchJoin();
}
```

#### 단점
Fetch Join도 언뜻보면 유용해보이지만 단점은 있다. 우선은 우리가 연관관계 설정해놓은 FetchType을 사용할 수 없다는 것이다. Fetch Join을 사용하게 되면 데이터 호출 시점에 모든 연관 관계의 데이터를 가져오기 때문에 FetchType을 Lazy로 해놓는것이 무의미하다.
또한, 페이징 쿼리를 사용할 수 없다. 하나의 쿼리문으로 가져오다 보니 페이징 단위로 데이터를 가져오는것이 불가능하다.


### 2. Batch Size
하이버네이트가 제공하는 org.hibernate.annotations.BatchSize 어노테이션을 이용하면 연관된 엔티티를 조회할 때 지정된 size 만큼 SQL의 IN절을 사용해서 조회합니다. 
정확히는 N+1 문제를 안 일어나게 하는 방법은 아니고 N+1 문제가 발생하더라도 `select * from user where team_id = ?` 이 아닌 `select * from user where team_id in (?, ?, ? )` 방식으로 N+1 문제가 발생하게 하는 방법입니다.
이렇게 하면 100번 일어날 N+1 문제를 1번만 더 조회하는 방식으로 성능을 최적화할 수 있습니다.

#### application.yml
```yaml
spring:
  jpa:
    properties:
      hibernate:
        default_batch_fetch_size: 1000
```

#### Entity
```java
@Entity
@Getter
@Setter
@NoArgsConstructor
public class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;

    @BatchSize(size=5) // 사용
    @ManyToOne(fetch = FetchType.EAGER)
		@JoinColumn(name = "team_id")
    private Team team;
}
```


## 실무에서의 해결 방법
우선 연관관계에 대한 설정이 필요하다면 FetchType을 성능 최적화를 하기 어려운 즉시 로딩(EAGER)을 사용하는 것이 아니라 지연 로딩(LAZY) 모드로 사용을 하고 성능 최적화가 필요한 부분에서는 Fetch 조인을 사용한다. 또한 기본적으로 Batch Size의 값을 1000이하로 설정한다. (대부분 DB에서 IN절의 최대 개수 값 : 1000)

그 외에는 꼭 연관관계 설정이 필요 없다면 N + 1 문제로 DB가 죽어버리는 불상사를 막기 위해 연관관계를 끊어버리고 사용하는 것도 하나의 방법이다.


## 결론

+ N+1은 JPA를 사용하면서 연관관계를 맺는 엔티티를 사용한다면 한번 쯤은 부딪힐 수 있는 문제이다.

+ Fetch Join이나 EntityGraph를 사용한다면 Join문을 이용하여 하나의 쿼리로 해결할 수 있지만 중복 데이터 관리가 필요하고 FetchType을 어떻게 사용할지에 따라 달라질 수 있다.

+ SUBSELECT는 두번의 쿼리로 실행되지만 FethType을 EAGER로 설정해두어야 한다는 단점이 있다.

+ BatchSize는 연관관계의 데이터 사이즈를 정확하게 알 수 있다면 최적화할 수 있는 size를 구할 수 있겠지만 사실상 연관 관계 데이터의 최적화 데이터 사이즈를 알기는 쉽지 않다.

+ JPA 만으로는 실제 비즈니스 로직을 모두 구현하기 부족할 수 있다. JPA는 만능이 아니다. 간단한 구현은 JPA를 사용하여 프로젝트의 퍼포먼스를 향상 시킬수 있겠지만 다양한 비즈니스 로직을 복잡한 쿼리를 통해서 구현하다보면 다양한 난관에 부딪힐 수 있다. 그리고 불필요한 쿼리도 항상 조심해야 한다. 그러므로 QueryBuilder를 함께 사용하는 것을 추천한다. 그러면 생각보다 다양한 이슈를 큰 고민없이 바로 해결할 수 있다.



## 참고

+ [블로그 1](https://incheol-jung.gitbook.io/docs/q-and-a/spring/n+1)
+ [블로그 2](https://programmer93.tistory.com/83)