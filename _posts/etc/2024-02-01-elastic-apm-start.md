---
title: "Elastic APM 시작하기"
date: 2024-02-01 15:30:00 +09:00
categories: [IT, Elastic]
tags: [elastic, setting]
image: /assets/img/posts/apm.png
---

docker 환경에서 Elastic 실행을 정리해본다. Elastic APM을 사용해보기 위함이다.

## swap 메모리 설정

### 1. ec2 접속

```bash
ssh -i {key-pem 파일} {서버 OS}@{퍼블릭 ip 주소}
```

### 2. swap 파일 메모리 할당

```bash
sudo dd if=/dev/zero of=/swapfile bs=128M count=16
```

### 3. swapfile에 접근할 수 있는 권한을 설정

```bash
# swapfile 권한 세팅 (READ, WRITE)
sudo chmod 600 /swapfile
```

### 4. mkswap 명령어로 swapfile을 추가할 swap 공간 생성

```bash
# swap 공간 생성 (Make swap)
sudo mkswap /swapfile
```

### 5. swapon 명령어를 통해 swapfile을 swap memory에 추가

```bash
# swap 공간에 swapfile 추가하여 즉시 사용할 수 있도록
sudo swapon /swapfile
```

### 6. /etc/fstab에 세팅

```bash
# /etc/fstab vi 에디터 열기
sudo vi /etc/fstab

# 파일의 맨 끝 다음줄에 아래 명령어 작성
/swapfile swap swap defaults 0 0
```

### 7. free로 확인

```bash
free

               total        used        free      shared  buff/cache   available
Mem:         8120824      546844     2553080         984     5020900     7254752
Swap:        2097148           0     2097148
```

## [Elasticsearch 와 Kibana 설치](https://www.elastic.co/guide/en/elasticsearch/reference/current/docker.html)

### 1. docker network 생성

```bash
docker network create elastic
```

### 2. docker elasticsearch image 생성

```bash
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.12.0
```

### 3. elasticsearch 실행

```bash
docker run -d --name es01 --net elastic -p 9200:9200 -it -m 1GB docker.elastic.co/elasticsearch/elasticsearch:8.12.0
```

### 3-1. token 및 비밀번호 확인

```
✅ Elasticsearch security features have been automatically configured!
✅ Authentication is enabled and cluster connections are encrypted.

ℹ️  Password for the elastic user (reset with `bin/elasticsearch-reset-password -u elastic`):
  *0F09yWevfjAB9h4cI+D

ℹ️  HTTP CA certificate SHA-256 fingerprint:
  cb01b3ddd843de067282565596446da4eedad22c896269144f0fd0e628138609

ℹ️  Configure Kibana to use this cluster:
• Run Kibana and click the configuration link in the terminal when Kibana starts.
• Copy the following enrollment token and paste it into Kibana in your browser (valid for the next 30 minutes):
  eyJ2ZXIiOiI4LjEyLjAiLCJhZHIiOlsiMTcyLjE4LjAuMjo5MjAwIl0sImZnciI6ImNiMDFiM2RkZDg0M2RlMDY3MjgyNTY1NTk2NDQ2ZGE0ZWVkYWQyMmM4OTYyNjkxNDRmMGZkMGU2MjgxMzg2MDkiLCJrZXkiOiJxel9fWTQwQmZMOXpERmdiZUR2NzplYWNVUDRnX1RnMmh3T3ZZcGN5S1h3In0=

ℹ️ Configure other nodes to join this cluster:
• Copy the following enrollment token and start new Elasticsearch nodes with `bin/elasticsearch --enrollment-token <token>` (valid for the next 30 minutes):
  eyJ2ZXIiOiI4LjEyLjAiLCJhZHIiOlsiMTcyLjE4LjAuMjo5MjAwIl0sImZnciI6ImNiMDFiM2RkZDg0M2RlMDY3MjgyNTY1NTk2NDQ2ZGE0ZWVkYWQyMmM4OTYyNjkxNDRmMGZkMGU2MjgxMzg2MDkiLCJrZXkiOiJxVF9fWTQwQmZMOXpERmdiZUR2VTpOYk1YN0NpYlFFU2QxeEZvaU45dk13In0=

  If you're running in Docker, copy the enrollment token and run:
  `docker run -e "ENROLLMENT_TOKEN=<token>" docker.elastic.co/elasticsearch/elasticsearch:8.12.0`
```

- Password for the elastic user    
Kibana에 로그인할 때 사용할 수 있는 elastic 사용자의 비밀번호. 이 비밀번호를 사용하여 Kibana에 로그인하거나 API 요청을 보낼 때 인증에 사용할 수 있다.

- HTTP CA certificate SHA-256 fingerprint    
 Elasticsearch와 통신할 때 필요한 인증서의 지문이다. 이 인증서를 사용하여 Elasticsearch 클러스터와 안전하게 통신할 수 있다.
 
 - Configure Kibana to use this cluster
 : Kibana를 Elasticsearch 클러스터에 등록할 때 사용할 수 있는 토큰이다. 이 토큰을 사용하여 Kibana를 Elasticsearch 클러스터에 연결하고 보안 기능을 활성화할 수 있다.


### 4. docker kibana image 생성

```bash
docker pull docker.elastic.co/kibana/kibana:8.12.0
```

### 5. kibana 실행

```bash
docker run -d --name kib01 --net elastic -p 5601:5601 docker.elastic.co/kibana/kibana:8.12.0
```

### 5-1. kibana code확인

```
docker logs -f kib01
Go to http://0.0.0.0:5601/?code=174965 to get started. # code 확인
```

### 6. web 접속

1. ip:5601 접속 후 3-1의 Configure Kibana to use this cluster를 입력
2. 5-1의 code 입력
3. ID(elastic), Password(3-1) 입력

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/30c3b10e-907d-4e14-8183-c092b7e6f1fe)

4. 로그인 후 화면

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/e6601b8e-46fe-48f5-ab88-cf1206854094)

## APM 설치
나는 Ubuntu를 이용중이므로 Linux DEB를 선택
![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/3f2a1b35-684a-48d6-b7a5-11b207b76980)

### 1. APM Server 설치

```
curl -L -O https://artifacts.elastic.co/downloads/apm-server/apm-server-8.12.0-amd64.deb
sudo dpkg -i apm-server-8.12.0-amd64.deb
```


### 2. Configuration 변경

apm-server.yml 변경
```
sudo find / -name apm-server.yml
/etc/apm-server/apm-server.yml
```
위치를 위의 명령어로 찾을 수 있다.

```
apm-server:
	host: ":8200"
output.elasticsearch:
    hosts: ["http://ip:9200/"]
    username: "elastic"
    password: "0F09yWevfjAB9h4cI+D"
```

### 3. apm 실행
이 과정에서 에러가 자꾸 났다` sudo journalctl -u apm-server.service`를 활용하여 에러 내용을 파악하자.

### 웹 접속시 false..
```
// 20240201214411
// http://13.124.187.0:8200/

{
  "build_date": "2024-01-09T16:06:45Z",
  "build_sha": "3e0d0cc3ab30586076aca0eda6beeb71f4c1906d",
  "publish_ready": false,
  "version": "8.12.0"
}
```

> **이 방법들은 실패!!**
{: .prompt-danger }


## Docker-compose 사용

### docker-compose.yml 작성

```yml
version: '3'

services:
  apm-server:
      image: docker.elastic.co/apm/apm-server:7.14.0
      ports:
        - 8200:8200
      environment:
        - 'output.elasticsearch.hosts=http://elasticsearch:9200'
        - 'setup.kibana.host="kibana:5601"'
        - 'apm-server.kibana.host="kibana:5601"'
        - 'setup.template.enabled=true'
        - 'logging.to_files=false'
      depends_on:
        elasticsearch:
          condition: service_healthy
        kibana:
          condition: service_healthy
      healthcheck:
        interval: 10s
        retries: 12
        test: curl --write-out 'HTTP %{http_code}' --fail --silent --output /dev/null http://13.124.187.0:8200/

  kibana:
    image: docker.elastic.co/kibana/kibana:7.14.0
    environment:
      ELASTICSEARCH_URL: http://elasticsearch:9200
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    ports:
    - 5601:5601
    healthcheck:
      interval: 10s
      retries: 20
      test: curl --write-out 'HTTP %{http_code}' --fail --silent --output /dev/null http://13.124.187.0:5601/api/status

    depends_on:
      elasticsearch:
        condition: service_healthy
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.14.0
    environment:
    - "cluster.routing.allocation.disk.threshold_enabled=false"
    - "discovery.type=single-node"
    - xpack.security.enabled=false
    - ES_JAVA_OPTS=-Xms512m -Xmx512m
    ports:
    - 9200:9200
    healthcheck:
      test: ['CMD', 'curl', '-f', 'http://13.124.187.0:9200']
      interval: 20s
      retries: 3
```


### docker-compose 실행

```yaml
docker-compose up -d
```

### http://{ip}:8200/ 연결확인

```
// 20240202014426
// http://{ip}:8200/

{
  "build_date": "2021-07-29T19:55:24Z",
  "build_sha": "af26b5a744b6af9bcccb04fade6435241ab91f43",
  "version": "7.14.0"
}
```

<br/>


## SpringBoot와 연결


### 1. build.gradle 설치

```
 implementation 'co.elastic.apm:elastic-apm-agent:1.36.0'
```

### 2. [jar 파일 다운로드](https://mvnrepository.com/artifact/co.elastic.apm/elastic-apm-agent/1.36.0)

프로젝트 최상단 위치에 `elastic-apm-agent-1.36.0.jar` 파일을 둔다.

### 3. 프로젝트 구성 편집

실행 ⭢ 구성 편집 ⭢ 옵션 수정 ⭢ VM 옵션추가

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/a6888d61-d947-4443-b6a8-bedce7f61b7e)

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/4010475b-5306-47de-be4a-6f515fbe6079)

```
-javaagent:D:/sparta/Yeti/elastic-apm-agent-1.36.0.jar
-Delastic.apm.service_name=yeti-project
-Delastic.apm.server_url=http://{ip}:8200
-Delastic.apm.application_packages=com.example.yetiproject
```

javaagent는 절대경로로 작성해야한다.


### 4. 프로젝트 실행 후 APM site Launch APM
{ip:5601}으로 접속하여 apm에서 launch APM

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/3b419911-e70d-48ef-aa68-15caeed06286)

```
2024-02-02 13:59:37,927 [main] INFO  co.elastic.apm.agent.util.JmxUtils - Found JVM-specific OperatingSystemMXBean interface: com.sun.management.OperatingSystemMXBean
2024-02-02 13:59:37,957 [main] INFO  co.elastic.apm.agent.util.JmxUtils - Found JVM-specific ThreadMXBean interface: com.sun.management.ThreadMXBean
2024-02-02 13:59:37,978 [main] INFO  co.elastic.apm.agent.configuration.StartupInfo - Starting Elastic APM 1.36.0 as yeti-project on Java 17.0.9 Runtime version: 17.0.9+11-LTS-201 VM version: 17.0.9+11-LTS-201 (Oracle Corporation) Windows 10 10.0
2024-02-02 13:59:37,979 [main] INFO  co.elastic.apm.agent.configuration.StartupInfo - service_name: 'yeti-project' (source: Java System Properties)
2024-02-02 13:59:37,979 [main] INFO  co.elastic.apm.agent.configuration.StartupInfo - server_url: 'http://13.124.187.0:8200' (source: Java System Properties)
2024-02-02 13:59:37,979 [main] INFO  co.elastic.apm.agent.configuration.StartupInfo - application_packages: 'com.example.yetiproject' (source: Java System Properties)
2024-02-02 13:59:39,339 [elastic-apm-server-healthcheck] INFO  co.elastic.apm.agent.report.ApmServerHealthChecker - Elastic APM server is available: {  "build_date": "2021-07-29T19:55:24Z",  "build_sha": "af26b5a744b6af9bcccb04fade6435241ab91f43",  "version": "7.14.0"}
2024-02-02 13:59:40,033 [main] INFO  co.elastic.apm.agent.impl.ElasticApmTracer - Tracer switched to RUNNING state
```

위와 같이 뜨면 잘 되는 것이다.




## 완료

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/74d2aced-da1a-48f7-b44e-130c930492f6)


## 메모리 에러

`sudo sysctl -w vm.max_map_count=262144`      
실행



## 참고

+ [끝이 없는 배움의 끝](https://oopsys.tistory.com/284)
+ [tmdtjq32.log](https://velog.io/@tmdtjq32/%EC%84%B1%EB%8A%A5-%ED%85%8C%EC%8A%A4%ED%8A%B8%EB%A5%BC-%EC%9C%84%ED%95%9C-elastic-APM-%EC%A0%81%EC%9A%A9-2)