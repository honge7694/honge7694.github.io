---
title: "EC2 생성"
date: 2023-11-23 23:00:00 +09:00
categories: [IT, AWS, CI/CD]
tags: [til, AWS, CI/CD, Github Actions]
image: /assets/img/posts/aws.png
---

## EC2 인스턴스 생성

### 1. 지역 변경
EC2에서 지역을 서울로 변경합니다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/226fbeb7-3c83-4aa8-b7c1-9de3f0bff968)


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

### 4. 보안 그룹 생성

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/a84a4a45-786c-4011-a45f-fcb5c13ab395)

처음 보안그룹을 생성할 때 launch-wizard-1이 생성된다. 이미 만든 인스턴스가 있기때문에 launch-wizard-2가 생성되었다.    

<br/>

보안 그룹은 보안과 관련된 그룹을 만들어, 어느 곳에서 접속을 허락하는지 설정 할 수 있다. 하나의 보안그룹이 여러 ec2 인스턴스에 설정될 수 있다.

### 5. 인스턴스 생성 및 연결

![instance-start](https://github.com/honge7694/honge7694.github.io/assets/76715487/91dbec75-bdf5-4160-8cfb-0ca5f6802a2d)

인스턴스 시작을 누른 후 다시 EC2로 이동한다.    
그 후에 생성한 인스턴스에 접속하여 연결을 클릭한다.

![instance-connect](https://github.com/honge7694/honge7694.github.io/assets/76715487/5e4c8bdd-f955-4ded-8c1d-d97a1cda4f03)


### 6. 접속 확인

![aws-join](https://github.com/honge7694/honge7694.github.io/assets/76715487/bb01d9e1-78f8-4d7d-b67a-30f0b5eede1c)


### 7. 윈도우 터미널로 접속 확인

![instance-connect-page](https://github.com/honge7694/honge7694.github.io/assets/76715487/a40267ff-2cb5-48ae-822e-77da03e04771)

예 :
`ssh -i "ec2-key.pem" ubuntu@ec2-15-165-159-198.ap-northeast-2.compute.amazonaws.com` 를 키페어를 생성하여 다운 받은 디렉토리에 가서 실행하면 된다.
![ec2-terminal](https://github.com/honge7694/honge7694.github.io/assets/76715487/7248c701-de8e-4e62-a336-48a4d8eaa418)

## EC2 보안 그룹

### 1. 보안 그룹 접속
사이드바에 있는 보안그룹에 들어가여 전에 만들었던 인스턴스의 보안그룹(launch-wizard-1)에 들어간다.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/811c329b-8c2e-4458-8e67-946962efa270)

여기서 **인바운드 규칙 편집**을 눌러 인바운드 규칙을 다음과 같이 한다.


### 2. 인바운드 규칙 편집

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/c3265fd8-49b6-4619-9ea1-6ae29921828c)

<br/>

> 인바운드 : 외부  ➝ EC2 인스턴스 내부 허용    
> 아웃바운드 : EC2 인스턴스 내부  ➝ 외부 허용    


