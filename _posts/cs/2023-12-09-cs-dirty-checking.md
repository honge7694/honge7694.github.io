---
title: "JPA의 더티 체킹이란?"
date: 2023-12-09 18:00:00 +09:00
categories: [IT, CS]
tags: [til, jpa dirty checking, cs]
image: /assets/img/posts/cs.png
---

## 더티 체킹이란??
JPA는 조회, 저장, 삭제에 대한 기능을 제공하지만, 수정에 대한 기능이 따로 존재하지않는다.
그래서 Dirty Checking 이란 것을 제공한다.    

JPA에서는 트랜잭션이 끝나는 시점에 변화가 있는 모든 엔티티 객체를 데이터베이스에 자동으로 반영해준다. 변화의 기준은 최초 조회 상태이다.    

이런 JPA 에서는 수정에 대한 기능이 따로 존재하지 않기 때문에, Dirty Checking 을 제공해주는데, Dirty Checking은 Transaction 안에서 엔티티의 변경이 일어나면, 변경 내용을 자동으로 데이터베이스에 반영하는 JPA 특징이다.    

<br/>

### 더티체킹의 순서

1. 엔티티 로딩
	JPA는 특정 엔티티를 데이터베이스에서 로드할 때 해당 엔티티의 현재 상태를 추적한다.
2. 트랜잭션 내에서 변경
	트랜잭션 내에서 엔티티의 상태가 변경되면 JPA는 이를 감지하고 해당 엔티티를 '더티'상태로 표시한다.
3. 커밋 시점에서 변경 감지
	트랜잭션을 커밋할 때, JPA는 엔티티 매니저가 관리하는 모든 엔티티를 검사하여 '더티'한 상태의 엔티티를 찾는다.
4. 변경된 엔티티 자동 갱신
	변경된 엔티티가 감지되면, JPA는 해당 엔티티의 변경 사항을 데이터베이스에 자동으로 반영한다. 이 프로세스는 SQL UPDATE 쿼리를 생성하여 변경된 필드 값을 데이터베이스에 반영한다.
	
<br/>

## 코드 예시

#### entity

```java
@Entity
@Getter
@Setter
@Builder
@AllArgsConstructor
@NoArgsConstructor
public class Sports {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long sportId;
    private String sportName;
    private String matchDate;

    @ManyToOne(fetch = FetchType.LAZY, cascade = {CascadeType.PERSIST, CascadeType.MERGE})
    @JoinColumn(name = "stadium_id")
    private Stadium stadium;

    public Sports(SportsRequestDto sportsRequestDto, Stadium stadium) {
        this.sportName = sportsRequestDto.getSportName();
        this.matchDate = sportsRequestDto.getMatchDate();
        this.stadium = stadium;
    }

    public void update(SportsRequestDto sportsRequestDto) {
        this.sportName = sportsRequestDto.getSportName();
        this.matchDate = sportsRequestDto.getMatchDate();
    }
}
```

#### service

```java
@Transactional
public SportsResponseDto updateSport(Long sportId, SportsRequestDto sportsRequestDto) {
        Sports sports = sportsRepository.findById(sportId)
                .orElseThrow(() -> new SportsNotFoundException("존재하지 않는 경기입니다"));
        Long stadiumId = sportsRequestDto.getStadiumId();
        if (stadiumId != null) {
            Stadium newStadium = stadiumRepository.findById(stadiumId)
                    .orElseThrow(() -> new StadiumNotFoundException("New Stadium not found"));

            sports.setStadium(newStadium);
        }
        sports.update(sportsRequestDto);
        return new SportsResponseDto(sports);
    }
```

Postman으로 API를 실행했을 때, save()를 작성하지않아도 값이 변하는 것을 볼 수 있다. 나는 sportName만 변경을 시도했는데, 모든 값이 변경되는 것을 아래 hibernate를 보고 알 수 있다.

```bash
Hibernate: 
    /* update
        for com.example.yetiproject.entity.Sports */update sports 
    set
        match_date=?,
        sport_name=?,
        stadium_id=? 
    where
        sport_id=?
```

더티 체킹을 통해  자동으로 생성된 update 쿼리는 기본적으로 모든 필드를 업데이트한다. 

<br/>

## DynamicUpdate
Dirty Checking으로 생성되는 Update 쿼리는 기본적으로 모든 필드를 업데이트한다.    
JPA에서 변경된 필드만 Update하지 않고 모든 필드를 변경하는 쿼리를 생성하는 이유는 다음과 같다.

+ 모든 필드를 Update 쿼리로 만들면 수정 쿼리가 항상 동일하게 생성되므로 미리 쿼리를 생성하여 재사용이 가능하기 때문이다.
+ 또한 데이터베이스는 동일한 쿼리를 전달받으면 항상 파싱된 쿼리를 재사용할 수 있다.

하지만 필드가 많은 경우에는 전체 필드 Update 쿼리가 부담이 될 수 있다. 이러한 경우에 `@DynamicUpdate`를 추가하여 변경필드만 반영될 수 있도록 할 수 있다.

#### Entity

```java
@Entity
@Getter
@Setter
@Builder
@DynamicUpdate // 추가
@AllArgsConstructor
@NoArgsConstructor
public class Sports {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long sportId;
    private String sportName;
    private String matchDate;

    @ManyToOne(fetch = FetchType.LAZY, cascade = {CascadeType.PERSIST, CascadeType.MERGE})
    @JoinColumn(name = "stadium_id")
    private Stadium stadium;

    public Sports(SportsRequestDto sportsRequestDto, Stadium stadium) {
        this.sportName = sportsRequestDto.getSportName();
        this.matchDate = sportsRequestDto.getMatchDate();
        this.stadium = stadium;
    }

    public void update(SportsRequestDto sportsRequestDto) {
        this.sportName = sportsRequestDto.getSportName();
        this.matchDate = sportsRequestDto.getMatchDate();
    }
}
```
	
	
#### 결과

```
Hibernate: 
    /* update
        for com.example.yetiproject.entity.Sports */update sports 
    set
        sport_name=? 
    where
        sport_id=?
```

<br/>

***

<br/>

#### **핵심 키워드 : 변경 감지, 커밋, 스냅샷, Transaction**


<br/>

> **나의 답변**    
더티 체킹은 객체 지향 프로그래밍에서 데이터베이스와의 상호 작용에서 변경된 데이터를 자동으로 감지하는 메커니즘입니다. JPA에서 더티 체킹은 영속성 컨텍스트가 관리하는 엔티티 객체의 상태를 주기적으로 확인하여 변경 사항을 감지하고, 이를 데이터베이스에 자동으로 반영하는 기능을 나타냅니다.
{: .prompt-warning }

<br/>

> **모범 답안**    
> Entity가 최초로 영속화된 상태와 영속화가 종료되는 시점의 상태를 비교하여 변경이 있을 경우 JPA가 자동으로 업데이트 쿼리를 실행하여 DB에 반영해 주는 기능입니다.
{: .prompt-info }





