---
title: "[Redis] Java 8 LocalDateTime Error"
date: 2024-01-29 18:00:00 +09:00
categories: [IT, Spring]
tags: [til, Spring, trouble shooting, cache, redis]
image: /assets/img/posts/TroubleShooting.png
---


## 에러 발생 상황

Redis를 이용하여 캐싱을 하는도중 마주친 에러이다.     

```
org.springframework.data.redis.serializer.SerializationException: Could not write JSON: Java 8 date/time type `java.time.LocalDateTime` not supported by default: add Module "com.fasterxml.jackson.datatype:jackson-datatype-jsr310" to enable handling (through reference chain: com.example.yetiproject.entity.TicketInfo["openDate"])] with root cause
```


## 해결
Redis에 저장되는 데이터가 직렬화(serialization)되어야 한다. 이러한 직렬화를 위해 Spring Data Redis는 기본적으로 Jackson 라이브러리를 사용한다. 그러나 기본적으로 Jackson은 Java 8의 날짜/시간 유형을 처리할 수 없기 때문에 해당 오류가 발생한것이다.

### 1. gradle 추가

```
implementation 'com.fasterxml.jackson.datatype:jackson-datatype-jsr310'
implementation 'com.fasterxml.jackson.core:jackson-databind'
```

### 2. entity 변경

```java
@Getter
@Setter
@Entity
@Builder
@AllArgsConstructor
@NoArgsConstructor
@DynamicUpdate
@Table(name = "ticket_info")
public class TicketInfo {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long ticketInfoId;

    @Column(nullable = false)
    private Long ticketPrice;

    @Column(nullable = false)
    private Long stock;

    @Column(nullable = false)
    @JsonSerialize(using = LocalDateTimeSerializer.class) // 추가
    @JsonDeserialize(using = LocalDateTimeDeserializer.class) // 추가
    private LocalDateTime openDate;

    @Column(nullable = false)
    @JsonSerialize(using = LocalDateTimeSerializer.class) // 추가
    @JsonDeserialize(using = LocalDateTimeDeserializer.class) // 추가
    private LocalDateTime closeDate;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "sports_id")
    private Sports sports;
}
```

