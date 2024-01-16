---
title: "[AWS & Redis] Error creating bean with name 'redissonLockTicketFacade' defined in URL"
date: 2023-12-16 18:02:00 +09:00
categories: [IT, TroubleShooting, Redis]
tags: [til, troubleshooting, redis]
image: /assets/img/posts/trouble-shooting.png
---

티켓 예매 프로젝트에 Redis를 이용한 동시성 제어를 추가하면서 ec2에 redis가 필요해졌다.    
redis를 읽어오지 못하여 에러가 생겼는데, 이 에러를 해결하기 위해 새벽 4시까지 팀장님과 함께 했지만 해결하지 못했다가, 멘토님의 조언에 따라 해결하게 되었다.

## 에러 발생 상황
main에 push하여 ci/cd가 동작하며, cd를 통한 배포도중 redis를 읽지 못하는 상황이 생겼다.

```java
Error creating bean with name 'redissonLockTicketFacade' defined in URL [jar:nested:/app.jar/!BOOT-INF/classes/!/com/example/yetiproject/facade/RedissonLockTicketFacade.class]: Unsatisfied dependency expressed through constructor parameter 0: Error creating bean with name 'redisson' defined in class path resource [org/redisson/spring/starter/RedissonAutoConfiguration.class]: Failed to instantiate [org.redisson.api.RedissonClient]: Factory method 'redisson' threw exception with message: Unable to connect to Redis server: localhost/127.0.0.1:6379
...
```

## 해결 과정

### 1. green/blue 각각 redis를 포함하여 배포
docker-compose.green.yml, docker-compose.blue.yml에 각각 redis를 생성하는 코드를 넣어서 ci/cd하면 해결될거라 생각했었다.    
하지만 이 방법은 redis가 2개가 올라갈거라 예상이되어 실행하지는 않았다.

### 2. redis를 ec2에서 작동
이 방법을 2번째로 시작했는데, 이 방법으로해도 에러가 해결되지 않았었다. 왜 에러가 해결되지 않았는지는 해결 부분에 있다. 

### 3. green/blue와 함께 redis 배포
다음은 redis-compose.yml을 작성하여 redis가 ci/cd를 하며 함께 올라간 docker-compose.green.yml 과 docker-compose.blue.yml이랑 함께 서버에 올라간 후 depends_on으로 종속된 redis를 이용한다고 착각했다.

<br/>

이 방법을 진행했더니, image가 생성이 되지않으며, 당연히 container도 생성되지 않아 에러를 찾을 수 없었고, 몇시간 동안 삽질을 하게 되었었다.

#### redis-compose.yml

```docker
version: '3'

services:
  redis:
    image: "redis"
    ports:
      - "6379:6379"
```

#### docker-compose.green.yml

```docker
version: '3'
services:
  backend:
    build: .
    ports:
      - "8082:8080"
    container_name: seniors-green
    environment:
      - REDIS_HOST = redis
      - REDIS_PORT = 6379
    depends_on:
      - redis
volumes:
  redis_data:
```

#### docker-compose.blue.yml

```docker
version: '3'
services:
  backend:
    build: .
    ports:
      - "8081:8080"
    container_name: seniors-blue
    environment:
      - REDIS_HOST = redis
      - REDIS_PORT = 6379
    depends_on:
      - redis
volumes:
  redis_data:
```



## 해결
사실 위의 에러 모두 너무 간단한 문제였다. ec2에 깔린 redis의 host를 찾지 못하는 에러였기 때문이었다. 이제부터 해결 방법을 알아보자.   (redis-compose.yml는 사용하지 않는다.)

### 1. redis를 서버에서 작동하도록 하기.
먼저 redis를 ec2에서 작동하게 해두었다. blue, green들과 함께 redis가 ci/cd 할 필요가 없다고 멘토님이 조언해주셨기 때문이다.
```
REPOSITORY     TAG       IMAGE ID       CREATED       SIZE
redis          latest    76506809a39f   11 days ago   138MB
```

### 2. blue, green docker-compose.yml 수정

#### docker-compose.blue.yml

```docker
version: '3'
services:
  backend:
    build: .
    ports:
      - "8081:8080"
    container_name: seniors-blue
    environment:
      - REDIS_HOST = {redis가 설치된 ec2 주소}
      - REDIS_PORT = 6379
volumes:
  redis_data:
```

#### docker-compose.green.yml

```docker
version: '3'
services:
  backend:
    build: .
    ports:
      - "8082:8080"
    container_name: seniors-green
    environment:
      - REDIS_HOST = {redis가 설치된 ec2 주소}
      - REDIS_PORT = 6379
volumes:
  redis_data:
```

여기서는 environment를 주어, REDIS의 HOST를 가져왔다.    
아까 해결과정의 2번에서 안되었던 **이유가 REDIS_HOST를 못 찾았기 때문**이었다.

### 3. application.yml 수정
프로젝트에서 application.yml파일은 따로 작성하지 않고, ci/cd를 github actions사용하여 하기때문에 git secrets and variables를 이용했다.

GihubAction > 프로젝트 Repository > Settings > Security > Secrets and variables > Actions    

Repository secrets > New repository secret > APPLICATION 수정     

```
spring:
  datasource:
    url: jdbc:postgresql://{rds end point}:{port}/postgres
    driver-class-name: org.postgresql.Driver
    username: {postgresql usernmae}
    password: {postgresql password}
  jpa:
    hibernate:
      ddl-auto: update
    show-sql: true
  data:
    redis:
      host: {ec2 ip}
      port: 6379
      timeout: 3000
jwt:
  secret:
    key:
     {jwt key}
feUrl:
  http://localhost:3000
```