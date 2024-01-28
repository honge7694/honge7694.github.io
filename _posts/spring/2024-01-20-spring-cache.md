---
title: "Cache를 사용해보자"
date: 2024-01-20 18:01:00 +09:00
categories: [IT, Spring]
tags: [til, project, spring boot, cache]
image: /assets/img/posts/spring.png
---


프로젝트의 티켓예매 로직은 티켓을 예매할 때, ticket_info의 정보를 조회하여 ticket이 저장되는 구조이다. 그러다보니 같은 ticket_info의 예매를 할 때마다 정보를 조회 하는 것이 `hibernate` 로그에 보이게 되었다. 그래서 이것을 캐싱을 한다면 리소스를 줄일 수 있지않을까? 하여 캐싱을 도입해보기로 하였다.

```
Hibernate: 
    select
        ti1_0.ticket_info_id,
        ti1_0.close_date,
        ti1_0.open_date,
        s1_0.sport_id,
        s1_0.match_date,
        s1_0.sport_name,
        s2_0.stadium_id,
        s2_0.stadium_name,
        ti1_0.stock,
        ti1_0.ticket_price 
    from
        ticket_info ti1_0 
    left join
        sports s1_0 
            on s1_0.sport_id=ti1_0.sports_id 
    left join
        stadium s2_0 
            on s2_0.stadium_id=s1_0.stadium_id 
    where
        ti1_0.ticket_info_id=?
```


## Cache란?

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/e5f7fd8f-3106-4f0d-9201-ab92b19a356d)

자주 사용되는 데이터를 메모리에 저장하여 빠른 검색을 가능하게 하는 기능을 제공한다. 해당 캐시에 저장되는 데이터를 `Cachemanager`를 통하여 메모리뿐만 아니라 디스크, 데이터베이스, 클라우드 서비스와 같은 다른 저장소에도 저장이 가능하다.

<br/>

그렇다면 Cache의 사용목적은 무엇일까? API에서 Database로부터 데이터를 조회하는 경우 동일한 데이터를 반복하여 조회해옴으로써 불필요한 일을 반복하는 문제가 발생한다.
이에 따라서 API 캐시를 통해서 DB로부터 반복적으로 데이터를 조회해 오는 일에 대해 최초 데이터를 조회해 온 뒤 이후는 캐시에서 데이터를 조회해 오는 처리를 수행함으로써 API의 성능을 올리며 응답시간을 단축하는 효율성을 가져온다.

## Cache 사용

### 1. Dependency 추가

```gradle
implementation 'org.springframework.boot:spring-boot-starter-cache'
```

`spring-boot-starter-web`같은 스타터 모듈에 자동으로 포함되어 있는 `spring-context`라는 모듈이 있어 `spring-boot-starter-cache`를 추가하지 않아도 캐시 기능을 사용할 수 있다.     
spring-boot-starter-cache 모듈을 추가해야 spring-context-support 모듈을 가져와서 캐시 관련된 여러 기능을 제공하기 때문에 캐시 관련 의존성을 추가해준다고한다.

### 2. Configuration 추가

```java
@EnableCaching
@Configuration
public class CachingConfig {
    @Bean
    public CacheManager cacheManager() {
        ConcurrentMapCacheManager cacheManager = new ConcurrentMapCacheManager();
        cacheManager.setAllowNullValues(false);
        cacheManager.setCacheNames(List.of("ticketInfo"));
        return cacheManager;
    }
}
```

`@EnableCaching`은 `SpringBootApplication`에 추가해도 되지만 Config 클래스를 추가한다면 Config 클래스에서 추가해도 된다.

#### Spring Boot Cache Annotation

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/b3eadf25-31ab-4ab6-9724-28099bf4c536)

+ `@Cachealbe`
	+ 데이터를 캐시에 저장
	+ 메서드를 호출할 때 캐시의 이름(value)와 키(key)를 확인하여 이미 저장된 데이터가 있으면 해당 데이터를 리턴
	+ 데이터가 없다면 메서드 수행 후 결과 값을 저장
+ `@CachePut`
	+ `@Cacheable`은 캐시에 데이터가 이미 존재하면 메서드를 수행하지 않지만, `@CachePut`은 항상 메서드를 수행
	+ 주로 캐시 데이터를 갱신할 때 많이 사용
+ `@CahceEvict`
	+ 캐시에 있는 데이터를 삭제
+ `@CacheConfig`
	+ 메서드가 아닌 클래스에 붙여서 공통된 캐시 기능을 모음
	+ `cacheName`, `cacheManager` 등
+ `@Cahcing`
	+ Cacheable, CachePut, CacheEvict를 여러개 사용할 때 묶어주는 기능

일반적으로 `@Cacheable`을 사용해서 캐싱하고 데이터를 갱신할 때 `@CachePut`, `@CacheEvict` 중 하나를 선택해서 갱신한다.

<br/>

> **이름(value)와 키(key)의 차이점은?**    
>  value는 캐시의 이름을 나타내며, key는 메서드의 매개변수 또는 반환 값을 기반으로 캐시 키를 생성한다. 따라서 value에서 정해진 캐시에서 검색하거나 저장할 때 key에 지정된 값을 캐시 키로 사용한다.


### 3. Repository 수정

```java
@Repository
@CacheConfig(cacheNames = "ticketInfo") // 추가
public interface TicketInfoRepository extends JpaRepository<TicketInfo, Long> {
	@Override //기본 적으로 findById 을 제공하기 때문에 Override 하여 재정의 후 사용
	@EntityGraph(attributePaths = {"sports","sports.stadium"})
	@Cacheable(key = "#ticketInfoId", unless = "#result == null") // 추가
	Optional<TicketInfo> findById(Long ticketInfoId);
}
```

`ticketInfoId` 를 키 값으로 설정하고  `unless = "#result == null"` 조건을 추가하여 DB에 없는 데이터인 경우 캐싱하지 않는다. 만약 조건이 없다면 null 값도 캐싱된다.    
CacheConfig 클래스에서 `cacheManager.setAllowNullValues(false);`를 추가했기 때문에 null 값을 캐싱하면 에러가 발생하니 꼭 조건을 추가해줘야한다.

<br/>

> **key 속성의 값은 매개변수와 같아야할까??**    
메서드의 매개변수나 반환값 등을 사용하여 캐시 키를 생성하는데 사용할 수 있다.
{: .prompt-tip }

<br/>


## 결과

### 캐싱 전

```bash
Hibernate: 
    /* <criteria> */ select
        u1_0.user_id,
        u1_0.address,
        u1_0.email,
        u1_0.password,
        u1_0.phone_number,
        u1_0.username 
    from
        users u1_0 
    where
        u1_0.username=?

Hibernate: 
    select
        ti1_0.ticket_info_id,
        ti1_0.close_date,
        ti1_0.open_date,
        s1_0.sport_id,
        s1_0.match_date,
        s1_0.sport_name,
        s2_0.stadium_id,
        s2_0.stadium_name,
        ti1_0.stock,
        ti1_0.ticket_price 
    from
        ticket_info ti1_0 
    left join
        sports s1_0 
            on s1_0.sport_id=ti1_0.sports_id 
    left join
        stadium s2_0 
            on s2_0.stadium_id=s1_0.stadium_id 
    where
        ti1_0.ticket_info_id=?

Hibernate: 
    /* dynamic native SQL query */ select
        stock 
    from
        ticket_info 
    where
        ticket_info_id = ?
```


### 캐싱 후


```bash
Hibernate: 
    /* <criteria> */ select
        u1_0.user_id,
        u1_0.address,
        u1_0.email,
        u1_0.password,
        u1_0.phone_number,
        u1_0.username 
    from
        users u1_0 
    where
        u1_0.username=?

Hibernate: 
    /* dynamic native SQL query */ select
        stock 
    from
        ticket_info 
    where
        ticket_info_id = ?
```

ticketInfo를 조회하는 과정이 생략된 것을 알 수 있다.

<br/>

***

<br/>

## 참고

+ [뱀귤 블로그](https://bcp0109.tistory.com/385)
+ [Contributor9 블로그](https://adjh54.tistory.com/165)