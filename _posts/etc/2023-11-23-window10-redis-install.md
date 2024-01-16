---
title: Windows 10 환경 Redis 설치
date: 2023-11-23 18:00:00 +09:00
categories: [IT, redis]
tags: [til, redis]
image: /assets/img/posts/redis.png
---


## Redis 설치

### [Git에서 설치](https://github.com/microsoftarchive/redis/releases)

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/cb240d74-0446-4f0e-aa7c-281c01ee43dd)

버전에 맞게 설치하면 된다. 윈도우용인.msi를 설치하였다.     
설치는 Next만 눌러서 설치하면된다. 기본포트는 6379이다.

<br/>

### 설치 확인

#### 1. 포트 확인
설치가 끝나면 설치 확인을 위해 cmd를 시작하여, 다음과 같이 입력한다.

```bash
netstat -an | findstr 6379
  TCP    0.0.0.0:6379           0.0.0.0:0              LISTENING
```
위와 같이 나온다면 잘 설치가 된 것이다.

<br/>

```bash
D:\Program Files\Redis> .\redis-server.exe redis.windows.conf
[36100] 23 Nov 18:10:20.222 # Creating Server TCP listening socket *:6379: bind: No error
```
또는 설치된 파일로 이동하여 이렇게 확인한다.

<br/>

#### 2. 서버 확인

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/845202f5-465d-424f-b685-e5cb4634d29c)    

<br/>

기동중인 상태가 확인이 되었다면 설치 파일로 들어가서 redis-cli.exe를 실행한다.    
이것을 시작하기전에 redis-server.exe를 한번 눌러주자.

<br/>

![image2](https://github.com/honge7694/honge7694.github.io/assets/76715487/cac062dc-fbad-4500-86e8-df4274aba68a)    

<br/>

위와 같이 ping을 보냈을 때 PONG이 온다면 정상적으로 실행 중인것이다.


## 에러??

redis-cli.exe를 클릭했을 때, 남들처럼 redis 대표 아이콘이 나오지않고 그냥 꺼졌다.
하지만 위의 작업들을 진행하고 나니까 잘 실행이 되었다.     
백그라운드에서 서버가 돌고있어서 redis가 꺼지는것이었다.
