---
title: "Window Kafka 설치 및 실행"
date: 2023-12-16 18:01:00 +09:00
categories: [IT, Kafka]
tags: [til, kafka, zookeeper]
image: /assets/img/posts/kafka.png
---

## Kafka 설치

### 1. 다운로드

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/734041b9-949c-4bbc-870e-fdd5a72a0eb6)

Scala 2.12  - kafka_2.12-3.6.1.tgz (asc, sha512)를 클릭하여 설치를 진행한다.    
Kafka는 스칼라로 개발 되었으므로, 시스템에 JVM(jdk 8이상)이 설치되어 있어야한다.

### 2. 압축 해제

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/03dbc6af-b32e-4970-9382-e0980e899474)

<br/>

## 2. Zookeeper 실행

Kafka를 실행하기 위해서는 먼저 Apache Kafka가 의존하는 ZooKeeper를 실행해야한다.    
ZooKeeper는 Apache Kafka 클러스터의 상태를 관리하고, 분산 시스템에서 필요한 동기화와 조정을 제공하는 역할을 한다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/7eb9b48f-6119-49f1-80e2-33f9ee421cfd)

<br/>

`cd kafka\kafka_2.12-3.6.1`    

압축을 풀은 폴더로 이동 후 `.\bin\windows\zookeeper-server-start.bat .\config\zookeeper.properties` 실행한다.

<br/>

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/a1dd215b-2031-4162-a25e-f8a57c055a20)
실행 후 사진이다.


## 3. Kafka 실행
Zookeeper 서버가 실행된 상태에서 CMD창을 추가로 열어 Kafka 서버 실행한다.

 `cd kafka\kafka_2.12-3.6.1`      
`.\bin\windows\kafka-server-start.bat .\config\server.properties`    

압축을 풀은 폴더에서 위 명령어로 실행한다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/cf3dfa8b-dc3e-4205-9f6f-d65cc2679181)
실행 후 사진이다.

## cmd에서 Kafka topic 생성 및 실행

### 1. topic 생성
Zookeeper와 Kafka를 모두 실행 시킨 뒤에
`cd bin\windows`    
`.\kafka-topics.bat --create --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic test`

### 2. producer 실행

`cd bin\windows`
`.\kafka-console-producer.bat --bootstrap-server localhost:9092 --topic testv.`


### 3. consumer 실행

`cd bin\windows`
`.\kafka-console-consumer.bat --bootstrap-server localhost:9092 --topic testv.`

### 4. 연결 완료 및 테스트

`cd bin\windows`

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/0236b4ab-0631-4385-851a-9d65d2d70504)

producer에서 입력한 데이터가 consumer에 나타나게 된다.






