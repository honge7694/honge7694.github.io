---
title: "[AWS] CodeDeploy 생성"
date: 2023-11-23 23:03:00 +09:00
categories: [IT, AWS]
tags: [TIL, AWS]
image: /assets/img/posts/aws.png
---


## EC2 추가 설정

CodeDeploy와 연계하기 위해 저번에 생성한 [EC2](https://honge7694.github.io/posts/aws-ec2-start/)에 추가 설정을 해줘야 한다.

### 1. 태그 생성

#### 1. 태그 관리 이동

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/a29f408d-1eb1-4445-8bf8-b91c3579792f)

#### 2. 태그 생성

![12dacfd66da5c2e3d18f79e0d1cdf29d.png](:/480fdd56457a4bdb9d4aea23bb882bb7)

키의 이름과 값을 넣어준다.

<br/>

그 다음 EC2 인스턴스에서 S3 버킷에 올려놓은 데이터에 접근할 수 있도록 권한을 준다.   
IAM 페이지로 이동한다.

### 2. IAM 생성

#### 1. AWS의 IAM 서비스로 이동

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/de7d40b7-81d9-4935-b5e4-51e5ba4933c8)

이동 후 역할 생성을 클릭한다.


#### 2. IAM 역할 생성

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/2e2a0412-ef83-4b47-8602-feb05c880d37)

#### 3. 권한 추가

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/baf4845b-91bf-4c98-bae8-4fa279ecaa5d)

#### 4. IAM 생성 완료

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/ca8f178b-07aa-4460-bf97-b92e8ac229ad)

역할 이름과 권한 추가를확인한 후 완료를 한다


#### 5. EC2 이동 후 IAM 역할 수정 이동

![47ecbee72ae15f6964287e5804d22082.png](:/276d28c2f73f47dd8ced373de1bd7f5a)


#### 6. IAM 역할 수정 후 저장

![421149a3a897ccbc0d99e213d45b93ac.png](:/84a7837fae2543c69ac1f5b423a98845)

저장을 눌러준다.




### S3 생성
빌드한 프로젝트를 압축해서 이 버킷에 저장을 해준다. 이후 ec2 에서 버킷 내부에있는 파일을 가져와 사용할 수 있다.

#### 1. S3 서비스로 이동

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/ebd5d2f7-945d-4738-b6f2-a0b02d6cdccb)

AWS의 서비스 S3로 이동하여 버킷 만들기를 눌러준다.

#### 2. S3 생성

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/7e5e84af-3482-4670-bfe9-e8e10dc7b34f)

사각형을 입력한 곳만 변경하고 모두 default이다.    
bucket 이름은 다른 사람들이 사용하고 있을 수 있으므로 고유한 이름을 지어줘야한다.





## CodeDeploy 생성 준비

### 1. EC2 인스턴스 접속 후 CodeDeply Agent 설치

```bash
$ sudo apt update
$ sudo apt install ruby-full
$ sudo apt install wget
$ cd /home/ubuntu
$ wget https://aws-codedeploy-ap-northeast-2.s3.ap-northeast-2.amazonaws.com/latest/install
$ chmod +x ./install
$ sudo ./install auto > /tmp/logfile
$ sudo service codedeploy-agent status
```

경로 확인을 꼭 해야한다.

![489f468f3bbb40cdb5f5c1ab7e5f903a.png](:/709bac63d276431d847c073f2b1581b0)

위와 같이 나온다면 성공이다.

### 2. IAM 역할 생성

#### 1. IAM - 역할 - 역할 만들기 이동
![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/54847288-7ee5-4fd9-9967-625e65d4f2ed)

#### 2. 역할 생성 완료

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/3e691228-522e-4746-a065-caa0db9333cb)


## CodeDeploy 생성

### 1. CodeDeploy 서비스 이동 후 애플리케이션 생성

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/2f93a065-f502-416e-8d69-fe59f3d0e48a)

### 2. 애플리케이션 생성

![b65b80efb035af001beaa48c93d5c56c.png](:/d116ec354fd34bee8759bc35edb6cf97)


### 3. 배포 그룹

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/19c8779b-3d31-4b88-89be-dcb3e1a63f45)

여기서 중요한 것은 **키**이다.   
EC2 인스턴스에서 **태그**를 생성한 것을 넣어주면 된다. 이미 만들었기 때문에 선택만 하면 된다.

<br/>

이제 CI/CD를 하기 위한 설정이 완료되었다.