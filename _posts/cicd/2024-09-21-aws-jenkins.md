---
title: "Jenkins + Docker + S3 + Spring Boot 배포 과정"
date: 2024-09-21 18:00:00 +09:00
categories: [IT, AWS, CI/CD]
tags: [til, aws, ci/cd, jenkins]
image: /assets/img/posts/aws.png
---

### 서론

Jenkins를 이용한 배포를 위해 CI/CD를 학습하던 중 정리가 필요해 작성하게 되었다. 설계하는 아키텍처에는 Jenkins, EC2, S3, Docker, CodeDeploy 도구들이 사용되며, Git Webhook을 이용하여 main branch에 PUSH가 되면 Jenkins에서 CI가 되고, S3에 압축된 파일을 올리고, CodeDeploy에서 배로를 위한 어떤 파일들이 필요한지 작성해보려 한다.

## 1. Git Push

먼저, Git에 Push 하게 되면, Git Webhook이 이를 감지하여 설정해둔 Payload URL을 통해 Jenkins에서 빌드 프로세스를 시작한다.

## 2. Jenkins 빌드 및 아티팩트 생성

Jenkins는 Git 코드를 pull한 후, Spring Boot 애플리케이션을 빌드한다.
이 때, 나는 JAR로 작성하였기 때문에, 생성된 파일은 `taget/` 또는 `build/libs/` 폴더에 저장된다.

## 3. S3에 아티팩트 업로드

빌드가 완료되면 Jenkins > Configuration > 소스 코드 관리에 설정한 S3 Bucket으로 `deploy.sh`, `appspec.yml`, `JAR`, `Dockerfile`이 업로드 된다.

## 4. CodeDeploy 실행

Jenkins는 S3에 파일을 업로드한 후, AWS CodeDeploy를 통해 EC2 인스턴스에 배포를 시작한다. CodeDeploy도 S3와 함께 Jenkins > Configuration > 소스 코드 관리에서 설정이 가능하다.    CodeDeploy는 S3에 업로드 된 `appspec.yml` 파일을 이용하여 배포를 진행한다.

```yml
version: 0.0
os: linux

files: # S3에서 EC2로 파일을 복사하는 부분을 정의
  - source: /
    destination: /home/ubuntu # S3에서 파일을 EC2로 복사할 위치
    overwrite: yes

permissions: # 복사된 파일들의 권한 설정
  - object: /
    pattern: "**"
    owner: ubuntu
    group: ubuntu

hooks: # 배포 중에 실행될 스크립트들을 정의
  ApplicationStart:
    - location: deploy.sh # 배포 후 실행할 스크립트 경로
      timeout: 60
      runas: ubuntu
```

> **appspec.yml 파일은 무엇일까??**    
> CodeDeploy에서 사용되는 배포 명세 파일로 S3에서 어떤 파일을 EC2로 복사할지, 복사 후 어떤 스크립트를 실행할지 작성해놓는 파일이다.      
> 이 파일에 따라 EC2 인스턴스에서 `deploy.sh`이 실행된다.    
{: .prompt-info }

## 5. EC2 인스턴스에서 배포 작업 수행

`deploy.sh` 스크립트가 EC2 인스턴스에서 실행된다.

```sh
#!/bin/bash

cd /home/ubuntu

DOCKER_APP_NAME=ec2-application
IMAGE_NAME=cms-app-image
CONTAINER_NAME=cms-app-container
PORT=8080
JAR_FILE="cms-0.0.1-SNAPSHOT.jar"

# 이전에 실행 중인 컨테이너 중지 및 삭제
if [ "$(docker ps -q -f name=${CONTAINER_NAME})" ]; then
  echo "실행중인 애플리케이션을 정지 후 삭제합니다."
  docker stop ${CONTAINER_NAME}
  docker rm ${CONTAINER_NAME}
fi

# 이전에 존재하는 이미지 삭제
if [ "$(docker images -q ${IMAGE_NAME})" ]; then
  echo "기존의 이미지를 삭제합니다."
  docker rmi ${IMAGE_NAME}
fi

# JAR 파일을 현재 디렉토리로 복사
cp /home/ubuntu/build/libs/${JAR_FILE} .

# Docker 이미지를 빌드
echo "Building new Docker Image..."
docker build -t ${IMAGE_NAME} --build-arg JAR_FILE=${JAR_FILE} .

# 새 컨테이너를 실행
echo "Starting new container..."
docker run -d --name ${CONTAINER_NAME} -p ${PORT}:8080 ${IMAGE_NAME}

# 배포 상태 확인
if [ "$(docker ps -q -f name=${CONTAINER_NAME})" ]; then
  echo "배포 성공! 어플리케이션 실행합니다. PORT : ${PORT}"
else
  echo "배포 실패"
  exit 1
fi

```

### [ERROR] COPY failed: file not found in build context or excluded by .dockerignore: stat cms-0.0.1-SNAPSHOT.jar: file does not exist

여기서 `cp /home/ubuntu/build/libs/${JAR_FILE} .`를 이용하여 JAR 파일을 현재 디렉토리로 복사하는 것을 안해서 CodeDeploy에서 배포를 실패했다.     
이 코드를 작성하는 이유는 Dockerfile이 실행될 때 실행 할 디렉토리에서 필요한 파일이 존재해야하기 때문이다.


> **deploy.sh 파일은 무엇일까??**    
> 기존에 실행 중인 Docker 컨테이너를 중지 및 삭제, 새로운 컨테이너 실행.    
> Docker 이미지 빌드 : `DockerFile`을 사용하여 새로운 Docker 이미지를 빌드한다.
{: .prompt-info }
