---
title: "[AWS] EC2 생성"
date: 2023-11-21 23:00:00 +09:00
categories: [IT, AWS]
tags: [TIL, AWS]
image: /assets/img/posts/TroubleShooting.png
---

## EC2 인스턴스 생성

### 1. 지역 변경
EC2에서 지역을 서울로 변경합니다.

![EC2 지역 변경.png](:/2e0c38ba459342a1bc0f9811f4cb61e8)


### 2. 인스턴스 시작
![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/72899fbf-3f1a-459a-ad67-fb350f11fef9)

#### 1. 인스턴스 이름을 입력
영어 및 한글 모두 지원하므로 이름 입력하는 것에 고민하지않아도 된다.

#### 2 . Ubuntu 선택
프리티어는 22.04, 20.04를 지원한다. 이 둘 중 편한것을 고르면 된다.

### 3. 키 페어 생성

![keypair](https://github.com/honge7694/honge7694.github.io/assets/76715487/9307e575-09db-4285-8fc6-907291b6ecc4)

#### 1. 키 페어 생성
이름은 적절하게 지어준다.    
RSA와 .pem을 선택하여 키 페어를 생성한다.

#### 2. 키 페어 생성 완료 (다운로드)
키페어 생성을 완료하면 다운로드가 자동으로 된다.    
이 파일을 잘 보관해두어야한다.    
삭제가되면 재발급을 받을 수 있다.

### 4. 인스턴스 생성 및 연결

![instance-start](https://github.com/honge7694/honge7694.github.io/assets/76715487/91dbec75-bdf5-4160-8cfb-0ca5f6802a2d)

인스턴스 시작을 누른 후 다시 EC2로 이동한다.    
그 후에 생성한 인스턴스에 접속하여 연결을 클릭한다.

![instance-connect](https://github.com/honge7694/honge7694.github.io/assets/76715487/5e4c8bdd-f955-4ded-8c1d-d97a1cda4f03)


### 5. 접속 확인

![aws-join](https://github.com/honge7694/honge7694.github.io/assets/76715487/bb01d9e1-78f8-4d7d-b67a-30f0b5eede1c)


### 6. 윈도우 터미널로 접속 확인

![instance-connect-page](https://github.com/honge7694/honge7694.github.io/assets/76715487/a40267ff-2cb5-48ae-822e-77da03e04771)

예 :
`ssh -i "ec2-key.pem" ubuntu@ec2-15-165-159-198.ap-northeast-2.compute.amazonaws.com` 를 키페어를 생성하여 다운 받은 디렉토리에 가서 실행하면 된다.
![ec2-terminal](https://github.com/honge7694/honge7694.github.io/assets/76715487/7248c701-de8e-4e62-a336-48a4d8eaa418)