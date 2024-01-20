---
title: "Bulk Insert?"
date: 2024-01-20 18:01:00 +09:00
categories: [IT, Spring]
tags: [til, project, bulk insert, spring boot]
image: /assets/img/posts/spring.png
---


## 한 번에 데이터를 넣는 bulk insert

대기열에서 n개의 데이터를 가져와 DB에 저장할 때 save()보다는 saveAll()이 속도가 더 빠르다고하여, saveAll()로 바꿔 진행해보았다.   
saveAll()로 데이터를 한번에 저장했을 때, save()와 같이 insert가 3번이 보였다. 
내가 생각한 것은 insert가 한개에 여러개의 데이터를 넣는 것이였는데, 이것을 한번 알아보았다.

> bulk insert란?    
> 대량의 데이터를 한 번에 데이터베이스에 삽입하는 기능을 말합니다. 이는 대량의 데이터를 일괄 처리하여 데이터베이스에 효율적으로 삽입할 수 있는 방법을 의미한다.    
> 주로 saveAll()을 사용하는 방법과 Jdbc Template를 사용하는 방법이 있다.
{: .prompt-info }


### save()

```bash
Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id) 
    values
        (?, ?, ?, ?)

Hibernate: 
	select
	...

Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id) 
    values
        (?, ?, ?, ?)

Hibernate: 
	select
	...

Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id) 
    values
        (?, ?, ?, ?)
```

save()로 진행했을 때, 데이터를 3개 요청하였다. 예상대로 3번의 insert가 나타난다.

<br/>

### saveAll()

```bash
Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id) 
    values
        (?, ?, ?, ?)

Hibernate: 
	select
	...

Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id) 
    values
        (?, ?, ?, ?)

Hibernate: 
	select
	...

Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id) 
    values
        (?, ?, ?, ?)
```

마찬가지로 3번의 insert가 일어난다. 이렇게 이뤄지는 것때문에 무엇이 문제인가 살펴보게 되었다.

## saveAll()의 문제점

일반적으로 JPA를 사용할 때 엔티티의 기본키 생성 전략을 **`GenerationType.IDENTITY`**로 가져간다.    

<br/>

@Id(기본키로) 지정된 필드가 비어있는데 어떻게 영속화가 가능할까??    

<br/>

Hibernates에서는 IDENTITY 전략을 사용할 경우 save 할 때 데이터베이스에 일단 INSERT한 뒤 생성된 기본키를 가져온다.    
그러다보니 Bulk Insert 할 때 N번 째 데이터의 기본키(식별자)를 채번 하기 위해 N-1 데이터까지 INSERT가 되어있는 상태에서 N번 째 데이터를 **INSERT하고 생성된 기본키를 가져오게 되어서 실질적으로 N번의 삽입 쿼리가 발생**한다.    

<br/>

위와 같은 채번 원리때문에 Hibernates에서는 **기본적으로 IDENTITY 기본키 생성 전략을 가져갈 때 Batch Insert를 허용하지 않는다**는 것을 알 수 있다.

<br/>

## 방법 1. 기본키 생성 전략 변경

```sql
CREATE TABLE tickets (
	id LONG PRIMARY KEY,
	...
);

CREATE TABLE sequence (
	table_name VARCHAR(255) NOT NULL PRIMARY KEY,
	next_val BIGINT
);
```

GenerationType.SEQUENCE 또는 GenerationType.TABLE 방식을 사용하는 것이다.

로컬에서는 MySQL을 사용하고 있어, SEQUENCE 채빈 방식을 지원하지 않기 때문에 TABLE 채번 방식을 활용한다.

### 1. Entity 클래스 코드를 변경

```java
@Entity
@Getter
@Setter
@AllArgsConstructor
@Builder
@NoArgsConstructor
@Table(name = "tickets")
public class Ticket {
	@Id
	@TableGenerator(
			name = "ticket_sequence_generator",
			table = "sequence",
			pkColumnName = "table_name",
			pkColumnValue = "tickets",
			allocationSize = 10000
	)
	@GeneratedValue(strategy = GenerationType.TABLE, generator = "ticket_sequence_generator")
	Long ticketId;

	@Column(name="posX")
	Long posX;
	@Column(name="posY")
	Long posY;

	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "user_id")
	private User user;

	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "ticketInfo_id")
	private TicketInfo ticketInfo;
}
```


### 1. 결과

```bash
Hibernate: 
	select
	...

Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id, ticket_id) 
    values
        (?, ?, ?, ?, ?)
Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id, ticket_id) 
    values
        (?, ?, ?, ?, ?)
Hibernate: 
    /* insert for
        com.example.yetiproject.entity.Ticket */insert 
    into
        tickets (posx, posy, ticket_info_id, user_id, ticket_id) 
    values
        (?, ?, ?, ?, ?)
```

select가 나오고 insert가 연속으로 나오게 바뀌었다. 하지만 여전히 bulk insert라고 할 수 있을지는 의문이다.

### 2. application.yml 파일에 url 추가

1.결과에서 Hibernate는 단지 PreparedStatemenet.addBatch()를 호출하기만 할 뿐 실제로는 쿼리가 합쳐지는지 아닌지는 모르는 상태이기 때문이다. 실제로 발생한 쿼리를 모와서 Batch Insert를 시키는 주체는 Hibernate가 아니라 MySQL 드라이버에서 진행하는 것을 알 수 있다.

```yml
spring:
  datasource:
    driver-class-name: com.mysql.cj.jdbc.Driver
    url: jdbc:mysql://localhost:3306/<DB>?rewriteBatchedStatements=true&profileSQL=true&logger=Slf4JLogger&maxQuerySizeToLog=999999
    username: <username>
    password: <password>
```

이를 위해 `url: jdbc:mysql://localhost:3306/yeti?rewriteBatchedStatements=true&profileSQL=true&logger=Slf4JLogger&maxQuerySizeToLog=999999`    
? 뒤에 명령어를 써준다면 insert가 한번에 되는지 알 수 있다.

### 2. 결과

```bash
[QUERY] /* insert for com.example.yetiproject.entity.Ticket */insert into tickets (posx,posy,ticket_info_id,user_id,ticket_id) values (30,52,1,3,70002),(61,57,1,3,70003),(72,8,1,3,70004) 
```

3개의 결과가 들어가는 것을 확인할 수 있다.

> saveAll()은 yml을 바꿔주면 어떻게 될까?    
> 위에서 말한 saveAll()의 문제점에서 말했듯이 insert하는 과정에서 INSERT하고 생성된 기본키를 가져오게 되어서 실질적으로 N번의 삽입 쿼리가 발생하므로, 3번의 INSERT가 발생한다.
{: .prompt-info }


## 방법 2. Jdbc BatchUpdate

별도의 테이블을 만들지 않고 기본키 생성 전략도 변경하지 않는 방법이 있다.    
바로 JDBC의 batchUpdate를 사용하는 방법이다.

### 1. Entity 원상복구

```java
@Entity
@Getter
@Setter
@AllArgsConstructor
@Builder
@NoArgsConstructor
@Table(name = "tickets")
public class Ticket {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	Long ticketId;

	@Column(name="posX")
	Long posX;
	@Column(name="posY")
	Long posY;

	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "user_id")
	private User user;

	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "ticketInfo_id")
	private TicketInfo ticketInfo;
}
```

### 2. TicketJdbcBatchRepository 생성

```java
@Repository
@RequiredArgsConstructor
public class TicketJdbcBatchRepository {
    private final JdbcTemplate jdbcTemplate;

    public List<Long> batchUpdate(List<Ticket> tickets) {
        String sql = "INSERT INTO tickets (ticket_info_id, posX, posY) VALUES (?, ?, ?)";
        return Arrays.stream(
                jdbcTemplate.batchUpdate(sql, new BatchPreparedStatementSetter() {
                    @Override
                    public void setValues(PreparedStatement ps, int i) throws SQLException {
                        Ticket ticket = tickets.get(i);
                        ps.setLong(1, ticket.getTicketInfo().getTicketInfoId());
                        ps.setLong(2, ticket.getPosX());
                        ps.setLong(3, ticket.getPosY());
                    }

                    @Override
                    public int getBatchSize() {
                        return tickets.size();
                    }
                })
        ).boxed().map(Integer::longValue).toList();
    }
}
```


### 결과

```bash
[QUERY] INSERT INTO tickets (ticket_info_id, posX, posY) VALUES (1, 8, 91),(1, 92, 90),(1, 80, 7)
```

3개의 데이터를 넣었을 때, 한번의 INSERT가 일어나는 것을 확인할 수 있었다.

