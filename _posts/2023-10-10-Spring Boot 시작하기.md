---
title: Spring Boot 시작하기
date: 2023-10-10 21:30:00 +09:00
categories: [DevOps, Spring, TIL]
tags: [Spring Boot, 환경설정, 프로젝트 시작, start.spring.io, spring-boot-devtools, spring-boot배포]
image: /assets/img/posts/Spring Boot 시작하기.png
---

Spring Framework랑 다르게 정말 간단하고 빠르게 설정을 해줄 수 있다. 
개발자 친화적이라는 소리를 들었는데 이렇게까지 편리성이 극대화 된다는 것이 신기했다.

## Spring initializer 
### 프로젝트 생성
#### 1. start.spring.io 이동
[스프링 부트 세팅](https://start.spring.io/)

#### 2. 아래와 같이 세팅
> Java 17 버전부터 Spring Boot 버전 3.x를 사용하고, Java 11은 버전 2.x를 이용해야 에러가 나타나지 않는다.

![start spring io 사용하기](https://github.com/honge7694/honge7694.github.io/assets/76715487/f8f4f77e-c73d-4e58-b5de-0ae63cea9ba4)

작성 후에 GENERATE를 클릭하여 다운로드한다.

#### 3. 압축 해제

#### 4. IntelliJ에서 실행
![IntelliJ에서 불러오기](https://github.com/honge7694/honge7694.github.io/assets/76715487/d238a416-c748-46f9-8f04-6c481dab157c)
압축 해제한 파일을 불러오면 된다.

#### 5. 빌드 완료 후 실행
> http://localhost:8080/을 들어가서 확인해보자.

![실행 결과](https://github.com/honge7694/honge7694.github.io/assets/76715487/b856d4f2-60d0-43ee-8bf4-992247a48129)
![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/d9c90f0b-014c-4353-85e5-1b787d3c42e4)

`Index.html`이 없어서 Error Page 뜨는 것이 정상적으로 실행된 것이다.

## spring-boot-devtools
> 서버를 재시작하지 않고 프론트 변경 사항을 바로 확인할 수 있도록 하고자 하는 라이브러리이다.    

### 프로젝트에 적용
### 1. build.gradle에 의존성 추가

```
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-thymeleaf'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	developmentOnly 'org.springframework.boot:spring-boot-devtools' // 추가
}
```

### 2. application.properties에 사용 여부 추가

```
spring.devtools.livereload.enabled=true
spring.devtools.restart.enabled=true
```

### 3. 환경 설정
#### 1. 빌드, 실행, 배포 설정
    ![빌드, 실행, 배포](https://github.com/honge7694/honge7694.github.io/assets/76715487/6c04e47d-3d3a-4d84-be00-148b77be31b5)

#### 2. 고급 설정
    ![고급 설정 - 컴파일러](https://github.com/honge7694/honge7694.github.io/assets/76715487/a1fcd359-90d7-4e31-bb83-b20295d9e658)

### 4. Chrome LiveReload 설치
![LiveReload](https://github.com/honge7694/honge7694.github.io/assets/76715487/d074f797-375e-46b6-bd3f-a2c10a099b26)

[LiveReload++ 설치](https://chrome.google.com/webstore/detail/livereload%20%20/ciehpookapcdlakedibajeccomagbfab)


## 빌드 및 배포
> 스프링에서 제공하는 빌드를 이용하면 매우 간단하다.

### 빌드

```cmd
프로젝트 경로 > gradlew.bat 
프로젝트 경로 > gradlew build 
```

윈도우의 경우 위의 두 명령어 두개다 해줘야한다. mac일 경우 gradlew build 하나만 해주면 된다.

![프로젝트 빌드](https://github.com/honge7694/honge7694.github.io/assets/76715487/fa339682-266b-4ca0-bcd6-588113348fb9)

### 배포 
> build 폴더 SNAPSHOT.jar 확인

```
> cd build
> cd libs

> java -jar 프로젝트 제목-버전-SNAPSHOT.jar
```

위 파일을 배포해주면 완료된다고 한다! 따로 설정하지않아도 되어 엄청나게 간단하다.