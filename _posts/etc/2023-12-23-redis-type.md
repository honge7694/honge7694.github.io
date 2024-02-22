---
title: "Redis 데이터 타입"
date: 2023-12-23 18:00:00 +09:00
categories: [IT, Redis]
tags: [til, redis, redis data type]
image: /assets/img/posts/redis.png
---

## Redis란?




## Redis의 다양한 데이터 타입

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/aed402af-5ace-4b96-93fc-5e6bbc9274cb)


## Redis - Strings
- 일반적인 문자열
- 값은 최대 512 MB이며, String으로 될 수 있는 binary data도, JPEDG 이미지도 저장 가능하다.
- **단순 증감 연산**에 좋음
- stirng-string 매핑을 이용하여 연결되는 자료 매핑을 할 수 있다. HTML 매핑도 가능하다.

### Strings 명령어 리스트

#### SET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/1953b79f-459a-415a-b76f-342a6471ef05)

#### GET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/97402008-dff2-4548-89e4-0b745da86446)

#### INCR, DECR

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/341dafa3-97d3-4b6f-b9a0-6779435ade0a)

#### 사용 방법

```bash
# 한개 조회
127.0.0.1:6379> set <key> <value>
127.0.0.1:6379> get <key> <value>

# 여러개 조회
127.0.0.1:6379> mset <key> <value> <key> <value> ...
127.0.0.1:6379> mget <key> <key> <key> ...
```


## Lists
- array 형식의 데이터 구조이며 데이터를 순서대로 저장한다.
- 추가 / 삭제 / 조회하는 것은 O(1)의 속도를 가졌지만, 중간의 특정 index 값을 조회할 때는 O(N)의 속도를 가지는 단점이 있다.
- 즉, 중간에 추가/삭제가 느리다. 따라서 head-tail에서 추가/삭제 한다. (push/pop 연산)
- 메세지 queue로 사용하기 적절하다.

### Lists 명령어 리스트

#### SET (PUSH)

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/4088138f-956a-45b9-9c98-a2acecb693fd)

#### GET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/df1ac88b-6d32-40f5-b776-9097151e26ea)

#### POP

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/55fa89be-9116-4232-ace1-fa29c6cd742c)

#### REM

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/0654e237-ba68-4c70-aafd-0f48c88b36c1)

#### 사용 방법

```bash
# 왼쪽에 삽입
127.0.0.1:6379> lpush <key> <value>

# 오른쪽에 삽입
127.0.0.1:6379> rpush <key> <value>

# 삭제
127.0.0.1:6379> lpop <key>
127.0.0.1:6379> rpop <key>
```



## Sets

- **중복된 데이터를 담지 않기 위해 사용**하는 자료구조
- 유니크한 key 값을 갖는다.
- 정렬되지 않은 집합
- 중복된 데이터를 여러번 저장하면 최종 한번만 저장된다.
- Set간의 연산을 지원, 교집합, 합집합, 차이를 매우 빠른 시간내에 추출 할 수 있다.
- **단, 모든 데이터를 전부 다 갖고 올 수 있는 명령이 있으므로 주의해서 사용해야 한다.**


### Sets 명령어 리스트


#### SET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/c8af5144-e3c9-40f4-ada0-6bdf1794a6ca)

#### GET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/c17ebc4a-25f7-400e-86cd-11c38d727be9)

#### POP

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/bf2fcc60-da9d-4323-8146-1046d7441b52)

#### REM

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/236a2ebc-b02d-4e11-b256-875cdbc8bea8)

#### 집합연산

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/58a54b84-8101-46d5-97d1-fae3e76f1a96)

```bash
127.0.0.1:6379> sadd <key> <item>

# 존재 여부를 체크, 있으면 1 없으면 0 반환
127.0.0.1:6379> sismember <key> <item>

# 삭제
127.0.0.1:6379> srem <key> <value>

# key의 모든 item 조회
127.0.0.1:6379> smembers <key>
```


## Sorted Sets
- set에 score라는 필드가 추가된 데이터형 (score는 가중치라 이해하면 된다.)
- 일반적으로 set은 정렬이 되어있지않고 insert한 순서대로 들어간다.     
  그러나 `Sorted Set`은 Set의 특성을 그대로 가지며 추가적으로 저장된 member들의 **순서도 관리**한다.
- 데이터가 저장될때부터 score 순으로 정렬되며 저장된다.
- sorted set에서 데이터는 **오름차순으로 내부 정렬** 된다.
- value는 중복 불가능, score는 중복 가능하다.
- 만약 score 값이 같으면 사전 순으로 정렬되어 저장된다.

### Sorted Sets 명령어 리스트

#### SET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/e6bcb876-e5e9-422d-ad0a-c9f91fd905a5)

#### GET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/658c2f5d-d397-4e81-9a33-65bb2c2bdd51)

#### POP

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/267574c9-5db9-4cd9-bcd5-d36a66132356)

#### REM

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/fa0b52b1-b9fc-474b-9f1c-ea3a36a8241f)

#### INCR

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/b73557f6-f40b-45a1-96b8-266fad1ce7fd)

#### 집합연산

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/0dda5009-c96e-4022-b9a3-df3de792529e)

```bash
# ZADD : key에 score-member에 추가
127.0.0.1:6379> zadd people 1 kim
(integer) 1

# ZADD : 복수개의 key를 추가
127.0.0.1:6379> zadd people 2 hong 3 nam
(integer) 2

# ZRANGE : key에 해당하는 범위 출력
127.0.0.1:6379> zrange people 0 -1
1) "kim"
2) "hong"
3) "nam"

# ZADD : 중복 된 member를 add시 score update
127.0.0.1:6379> zadd people 10 kim
(integer) 0

# ZRANGE : key에 해당하는 범위 출력
127.0.0.1:6379> zrange people 0 -1
1) "hong"
2) "nam"
3) "kim"

# ZSCORE : member에 해당하는 score 값 리턴
127.0.0.1:6379> zscore people kim
"10"

# ZRANK : member에 해당하는 rank(순위) 값 리턴
127.0.0.1:6379> zrank people kim
(integer) 2
```


## Hash
- field-value로 구성 되어있는 전형적인 hash의 형태 (파이썬의 딕셔너리)
- key 하위에 subkey를 이용해 추가적인 Hash Table을 제공하는 자료구조
- 메모리가 허용하는 한, 제한없이 field들을 넣을 수가 있다.

### Hash 명령어 리스트

#### SET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/4c7152c2-cfaa-410d-8896-85b4b75c9a97)

#### GET

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/46a4015a-7b3c-41df-843a-9475f60e29b0)

#### REM

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/8ee95621-997b-45ce-bc44-42fd84bf4894)

#### INCR

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/fd483edf-d7aa-4258-a99a-5fefa12484b8)

```bash
# 한개 값 삽입 및 삭제
127.0.0.1:6379> hset <key> <subkey> <value>
127.0.0.1:6379> hget <key> <subkey>

# 여러 값 삽입 및 삭제
127.0.0.1:6379> hmset <key> <subkey> <value> <subkey> <value> ...
127.0.0.1:6379> hnget <key> <subkey> <subkey> <subkey> ... 

# 모든 subkey와 value 가져오기, Collection에 너무 많은 key가 있으면 장애의 원인이 됨
127.0.0.1:6379> hgetall <key>

# 모든 value값만 가져오기
127.0.0.1:6379> hvlas <key>
```


