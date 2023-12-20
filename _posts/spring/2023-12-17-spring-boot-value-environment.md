---
title: "@Value와 Environment"
date: 2023-12-17 18:00:00 +09:00
categories: [IT, Spring, TIL]
tags: [SpringBoot, "@Value", Environment]
image: /assets/img/posts/TIL.png
---

application.yml에 데이터를 가져오는 방법을 @Value만 사용했었는데, Environment도 있다는 것을 알게되어 정리해본다.

## @Value와 Environment

|      | @Value | Environment |
|------|--------|-------------|
| 장점 |간단하게 필요한 값 주입 가능| 복잡한 설정 다룸<br/>여러 속성 그룹화 및 특정 프로퍼티 소스 접근 가능<br/>빈으로 바인딩하여 사용 가능 |
| 단점 |여러 값 또는 비슷한 설정이 많을 때 코드 복잡성 증가| 코드가 조금 더 길어질 수 있음<br/> @Value보다 설정이 더 번거로울 수 있음|


<br/>

```yml
spring:
  kafka:
    bootstrap-servers: localhost:9092
    producer:
      retries: 0
      batch-size: 4096
      linger-ms: 1
      buffer-memory: 40960
```

위의 `application.yml` 파일에서 `@Value`와 `Environment`는 어떻게 값을 받아보는지 살펴보자.

### @Value의 사용 예시

```java
@Configuration
@RequiredArgsConstructor
@Slf4j(topic = "KafkaProducerConfiguration 실행")
public class KafkaProducerConfiguration {

    @Value("${spring.kafka.bootstrap-servers}")
    private String bootstrapServers;

    @Value("${spring.kafka.producer.retries}")
    private int retries;

    @Value("${spring.kafka.producer.batch-size}")
    private int batchSize;

    public Map<String, Object> producerConfig() {
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, bootstrapServers);
        props.put(ProducerConfig.RETRIES_CONFIG, retries);
        props.put(ProducerConfig.BATCH_SIZE_CONFIG, batchSize);
        return props;
    }
}
```

위와 같이 `@Value`에 `application.yml`파일에서 가져올 경로를 작성하면 작성된 값을 반환받을 것이다.

### Environment

```java
@Configuration
@RequiredArgsConstructor
@Slf4j(topic = "KafkaProducerConfiguration 실행")
public class KafkaProducerConfiguration {

    private final Environment env;

    public Map<String, Object> producerConfig() {
        Map<String, Object> props = new HashMap<>();

        // server host 지정
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG,
                env.getProperty("spring.kafka.bootstrap-servers"));

        // retries 횟수
        props.put(ProducerConfig.RETRIES_CONFIG,
                env.getProperty("spring.kafka.producer.retries"));

        // batch size 설정
        props.put(ProducerConfig.BATCH_SIZE_CONFIG,
                env.getProperty("spring.kafka.producer.batch-size"));

        // linger.ms
        props.put(ProducerConfig.LINGER_MS_CONFIG,
                env.getProperty("spring.kafka.producer.linger-ms"));

        // buffer memory size 설정
        props.put(ProducerConfig.BUFFER_MEMORY_CONFIG,
                env.getProperty("spring.kafka.producer.buffer-memory"));

        // key serializer 지정
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG,
                StringSerializer.class);

        // value serializer 지정
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG,
                StringSerializer.class);

        return props;
    }
```

`env.getProperty()`를 통해 `application.yml` 값을 가져올 수 있다.

## 결론

+ 단순한 설정 값들을 사용할 때는 @Value를 통해 간단하게 주입받는 것이 편리하다.
+ 여러 속성을 그룹화하거나 빈으로 바인딩하려면 Environment와 @ConfigurationProperties를 함께 사용하는 것이 좋다.
+ 프로젝트의 규모와 복잡성에 따라 선택하면 된다. 작은 프로젝트에서는 @Value를 사용해도 충분하겠지만, 대규모 프로젝트에서는 Environment와 @ConfigurationProperties를 사용하여 유지보수성을 높일 수 있다.