---
title: "Redis의 저장 방식"
date: 2024-01-27 18:00:00 +09:00
categories: [IT, Redis]
tags: [til, redis, aof, rdb]
image: /assets/img/posts/redis.png
---


프로젝트를 진행하는 도중, redis의 서버가 갑자기 꺼진다면 대기열은 어떻게 보관해야할까? 라는 질문에서 redis에 AOF와 RDB 저장방식이 있다는 것을 확인하였다. 기존의 RDB방식과 AOF의 차이가 무엇인지 작성해본다.

## Redis의 저장 방식

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/773cc872-5499-4a43-8976-d029eec8af3e)

Redis는 In-Memory DB임에도 불구하고, 메모리 데이터를 disk에 저장할 수 있는 특징이 있다.
서버가 꺼진 후 restart 되더라도, disk에 저장해놓은 데이터를 다시 읽어서 메모리에 로딩하기 때문에 데이터가 유실되지 않는다.    

<br/>

Redis 에서는 데이터를 저장하는 방법이 RDB방식과 AOF방식 두 가지가 있다.
+ RDB 방식은 특정한 간격마다 메모리에 있는 레디스 데이터 전체를 디스크에 쓰는 것으로 백업에 용이하다.
+ AOF 방식은 명령이 실행될 때 마다 데이터를 파일에 기록하여 데이터의 손실이 거의없다.

<br/>

## RDB(Redis DataBase)

```bash
127.0.0.1:6379> config get save
1) "save"
2) "3600 1 300 100 60 10000"
```

Redis는 기본적으로 RDB방식을 사용하여 데이터를 영속화한다.  RDB 방식은 주기적인 스냅샷을 생성하기 때문에 일정 시간 간격으로 디스크에 저장된 스냅샷 파일이 생성된다. 이 파일은 일반적으로 .rdb 확장자를 가지며, Redis 서버 재시작 시에 이 파일을 로드하여 데이터를 복구한다.

<br/>

redis-cli를 접속하여 `config get save`를 입력하면 볼 수 있는 결과이다. RDB의 스냅샷 조건기본설정이다.
+ 3600초 안에 1개 이상의 데이터가 변경되면 저장.
+ 300초 안에 100개 이상의 데이터가 변경되면 저장
+ 60초 안에 10000개 이상의 데이터가 변경되면 저장

RDB를 사용하지 않을 것이라면 `config set save ""` 명령을 통해 조건을 삭제해주면 된다.

<br/>

docker에서 실행시킨 redis를 접속하여  백업 된 파일을 확인할 수 있다.    
`docker exec -it c28fe90a936c /bin/bash`  를 입력하여 접속하게 되면 /data에 .rdb파일이 있는 것을 확인할 수 있다.

```bash
root@c28fe90a936c:/data# ls
dump.rdb
```

<br/>

### RDB 사용 주의할 점

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/dc4b03b9-2344-4e5e-b89a-076bb59c15c9)

RDB(Snapshot) 방식의 문제점은 매우 명확한데, Redis에 장애가 발생했을 때 백업 시점을 제외한 중간 시점에서 발생한 데이터는 유실될 수 있다는 것이다. 이것 때문에 AOF방식을 생각하게 되었다. 

<br/>

## AOF(Append-Only File)

AOF 방식은 모든 쓰기 작업을 로그 파일에 기록한다. AOF 방식에서 생성된 로그 파일은 일반적으로 .aof 확장자를 가진다. AOF는 쓰기 명령에 대해 추가하며 기록되기 때문에 파일 사이즈가 커지기 때문에 서버 시작시 로딩속도가 느려진다. Redis 서버가 재시작되면 AOF 파일에 기록된 모든 쓰기 작업을 다시 실행하여 데이터를 복구한다.

<br/>

```bash
docker run -d --name my-redis -v /data:/data redis redis-server --appendonly yes
```
로 실행시켜 주면 AOF로 실행시킨다.


마찬가지로 redis-clie에 접속하여 확인할 수 있다.
```bash
127.0.0.1:6379> CONFIG GET appendonly
1) "appendonly"
2) "yes"
```

2)에 yes가 되어있다면 AOF가 적용된 것이다.

### AOF 기본 설정

#### appendonly yes 또는 no 
yes는 AOF기능을 사용하는 것이며, 레디스 서버 시작 값이 yes 일때만 AOF 파일을 읽어들인다. no 이면 AOF 파일이 있어도 읽어들이지 않는다.

#### appendfilename "appendonly.aof" 
AOF 파일명을 지정하고, Path는 지정할 수 없다. Path는 working directory에 따른다.

#### appendfsync 
AOF에 기록하는 시점을 정한다.
+ always    
명령 실행 시 마다 AOF에 기록한다. 데이터 유실의 염려는 없으나, 성능이 매우 떨어진다.
+ everysec     
매 초 마다 AOF에 기록한다. 1초 사이 데이터 유실될 수 있으나, 성능에 거의 영향을 미치지 않으면서 데이터를 보존할 수 있어, 일반적으로 권장한다. default 값이다.
+ no     
AOF에 기록하는 시점을 OS가 정한다. 기본적으로 리눅스는 30초 마다 flush하지만 커널의 정확한 조정에 달려있다.

### AOF rewrite
Redis의 AOF(Append-Only File) 파일을 다시 작성하여 파일 크기를 최적화하는 프로세스를 말한다.  이를 통해 AOF 파일의 크기를 줄이고 디스크 공간을 절약할 수 있다.

```bash
docker run -d --name my-redis -v /data:/data redis redis-server --appendonly yes --appendfilename appendonly.aof --aof-rewrite-incremental yes
```

 Redis 컨테이너를 시작하고, AOF Rewrite를 실행하는 옵션을 함께 지정한다. 이렇게 하면 Redis 서버가 실행되는 동안 AOF Rewrite를 실행하여 AOF 파일을 최적화할 수 있다.
     
여기서 /data는 호스트 시스템의 경로로, Redis 데이터가 저장될 디렉토리를 나타낸다. 필요에 따라 해당 경로를 수정하여 Redis 데이터를 저장할 경로를 지정할 수 있다.

<br/>

### AOF 사용 주의할 점

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/91a2607f-f116-4f37-b1dc-78b6167c210a)


쓰기 작업의 기록을 저장해 replay하는 형식으로 복구 할 수 있기 때문에 안정적이고 이상적일 수 있지만 그렇지않다. 100번의 Increment 작업을 통해 0의 데이터를 100으로 만들었다고 가정해봤을 때, 최종 저장되어 있는 데이터는 100이지만, AOF 방식에서 기록된 파일을 읽어와 복구하려면 100번의 Increment 쓰기 작업을 실행해야한다. RDB 방식에서는 단순하게 100이란 값을 단순히 읽어오기만 하면 되는 것에 비해 너무 낭비가 심하다. 뿐만 아니라 RDB 방식에 비해 백업 데이터가 크기도 하고, 서버 자원 또한 많이 잡아먹는다.



<br/>

## RDB vs AOF 선택

그럼 어느 경우에 AOF 와 RDB 중 어떤 방법을 사용해 파일을 읽어야 할까?
우선 redis 캐시로만 사용한다면 백업기능은 필요 없다. 그래도 백업은 필요하지만 어느 정도의 데이터 손실이 발생해도 괜찮은 경우, RDB를 단독 사용하는 것을 고려한다. 이전에 보았던 save 옵션을 적절하게 변경하면 된다.

```bash
SAVE 900 1 # 900초 동안 1개 이상 키가 변경되었을 때 RDB 파일 재작성
```

하지만 모든 데이터가 보장되어야할 경우 AOF 사용하면 된다.


## 프로젝트에서는??

프로젝트에서는 RDB와 AOF가 적절하게 있는 Kafka를 적용시킨다면 좀 더 성능을 끌어올릴 수 있고, 에러시에도 데이터가 유지되므로 Kafka를 적용시켜보기로 한다.



## 참고

+ [Inpa](https://inpa.tistory.com/entry/REDIS-%F0%9F%93%9A-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EC%98%81%EA%B5%AC-%EC%A0%80%EC%9E%A5%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95-%EB%8D%B0%EC%9D%B4%ED%84%B0%EC%9D%98-%EC%98%81%EC%86%8D%EC%84%B1)
+ [꾸준함은 최고를](https://velog.io/@pjh612/Redis%EC%9D%98-%EB%B0%B1%EC%97%85RDB-AOF-%EC%95%8C%EC%95%84%EB%B3%B4%EA%B8%B0)