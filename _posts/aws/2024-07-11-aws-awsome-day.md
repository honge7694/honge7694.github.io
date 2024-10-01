---
title: "[AWS] AWSOME DAY 온라인 컨퍼런스 정리"
date: 2024-07-11 18:00:00 +09:00
categories: [IT, AWS]
tags: [til, aws]
image: /assets/img/posts/aws.png
---


# AWS 클라우드 소개

![image](https://github.com/user-attachments/assets/ff935596-9d3c-47dd-9ab2-64198a9608a9)
출처 : AWS pdf

## 온프레미스와 클라우드의 차이
온프레미스는 회사가 서버, 스토리지, 데이터베이스 등을 직접 구축하고 소유하며 운영관리하는 것이고, 클라우드는 인터넷을 통해서 IT 리소스나 애플리케이션을 필요시 사용한 만큼만 지불하고 사용하는 서비스이다.

## 클라우드 컴퓨팅의  장점

1. 선행 비용을 가변 비용으로 대체
	+ 구축하고자 하는 서비스에 필요한 만큼만 리소스를 사용할 수 있다.
	+ 빠르게 변화하는 비즈니스 환경에 맞추어 사용할 수 있다.
	
2. 규모의 경제로 얻게 되는 이점
	+ 여러 고객들이 리소스를 함께 구매하여 사용하여 단위 인프라에 대한 비용이 낮아진다.
	
3. 용량 추정 불필요
	+ 필요한 만큼 사용할 수 있기 때문에 계획을 세우는 데 많은 노력이나 시간이 필요없다.
	+ 애자일한 개발 환경으로 다양한 시도를 비교적 짧은 시간 안에 신기술 도입이나 아키텍처 변경 등 많은 시도를 할 수 있다. (AWS에서는 이러한 개념을 fast fail, 자유로운 실수로 정의)
4. 속도 및 민첩성 향상
	+ 직접 구현해야 했던 복잡한 기능들을 서비스를 통해 이용 가능하다.
5. 데이터 센터 운영 및 유지 관리에 비용 투자 불필요
	+ IT 인프라와 건물 등에 지출되는 비용을 비즈니스 가치 창출에 집중한다.
6. 몇 분 만에 전 세계에 배포

## 온프레미스와 AWS의 차이

![image](https://github.com/user-attachments/assets/1c0ffb00-b8ac-4111-a098-44d6a2f6de57)
출처 : AWS pdf

## 일반적인 클라우드 용어
+ 클라우드 컴퓨팅
	+ IT 리소스를 온디맨드 방식으로 사용할 수 있도록 제공하는 것
+ 클라우드 서비스 공급자
	+ 클라우드 컴퓨팅을 유료로 제공하는 기업 또는 조직
+ 퍼블릭 클라우드
	+ 클라우드 서비스 제공자가 제공하는 컴퓨팅 서비스
+ 프라이빗 클라우드
	+ 기업 또는 조직에 속한 프로비저닝 된 클라우드 인프라로 일반적인 사용자는 이용 불가하다
+ 하이브리드 클라우드
	+ 프라이빗 클라우드 + 퍼블릭 클라우드를 함께 사용하는 것
+ 서비스형 인프라(IaaS)
	+ 벤더가 인터넷을 통해 가상 클라우드 컴퓨팅 리소스를 제공하는 클라우드 컴퓨팅 모델
+ 서비스형 플랫폼(PaaS)
	+ 애플리케이션 및 서비스를 빌드하는데 필요한 모든 하드웨어 및 소프트웨어 환경을 클라우드를 통해 제공하는 클라우드 컴퓨팅 서비스 모델
+ 서비스형 소프트웨어(SaaS)
	+ 애플리케이션을 구매하고 설치하여 실행할 필요가 없도록 벤더가 인터넷을 통해 제공하는 것
+ 서비스형 백엔드 (BaaS)
	+ 개발자가 직접 코드를 개발하지않고, 클라우드와 연동시켜 제공하는 API를 호출하여 개발시간을 단축 가능
+ 자동 크기 조정
	+ 컴퓨팅 리소스를 자동으로 추적하고 조정하는 AWS의 서비스
+ 탄력성
	+ 필요할 때 리소스를 추가적으로 확보하고, 필요하지 않을 때는 리소스를 제거하는 능력을 의미
+ 서버리스, 서버리스 컴퓨팅
	+ 컴퓨터를 관리 할 필요가 없도록 컴퓨팅, 스토리지 및 네트워크 리소스를 제공하는 플랫폼


# AWS 클라우드 핵심 서비스 소개

## 1. 컴퓨팅

### AmazonElastic Compute Cloud(Amazon EC2)

+ EC2 이점
	+ 탄력성, 안전성, 제어, 유연성, 보안 등이 있다.

+ AMI(Amazon Machine Images)를 이용하여 사용 가능한 하드웨어 및 소프트웨어를 종량 과금제를 통해 폭넓게 제공한다. (Ubuntu 20.04)
 
+ 크기 조정 가능한 컴퓨팅 용량으로 어떤 목적으로 사용하는지에 따라 EC 인스턴스 유형 선택
![image](https://github.com/user-attachments/assets/2d5a5710-f583-43f8-96b0-06023dca5742)
출처 : AWS pdf

+ 상황에 맞게 요금제를 선택
	+ 온디맨드 인스턴스
		+ 장기약정 없이 컴퓨팅 용량비용을 초나 시간 비용단위로 지불
		+ 중단할 수 없는 불규칙한 단기 워크로드가 있는 애플리케이션에 적합
	+ 예약형 인스턴스
		+ 특정 기간 동안 특정 가격으로 이용하므로 비용 절약 가능
	+ Savings Plans
		+ 정해진 시간동안 일정한 금액을 지출하겠다는 약정에 기반 
		+ 예약형 인스턴스보다 더 유연하게 사용 가능
	+ 스팟 인스턴스
		+ 여분의 EC2 호스트 용량을 사용하는 인스턴스
		+ 온디맨드 요금보다 최대 90%까지 절약 가능

+ 서버리스 컴퓨팅 지원

> **서버리스란?**  
> 서버리스는 개발자가 인프라 관리 작업을 신경 쓸 필요 없이 코드 실행에 집중할 수 있게 해주는 모델이다.    
> AWS Lambda와 같은 서비스를 통해 구현되며, 이벤트 기반으로 작동한다.
사용한 만큼만 비용을 지불하는 구조로, 리소스 사용이 없을 때는 비용이 발생하지 않는다.
{: .prompt-info }

### AWS Lambda

+ 완전 관리형 컴퓨팅 서비스
+ stateless 코드 실행
+ 일정에 따라, 또는 이벤트에 대응하여 코드 실행(ex) Amazon S3 버킷 또는 Amazon Dynamo DB 테이블의 데이터 변경)

> **stateless란?**    
> 서버가 클라이언트의 상태정보를 저장하지않는다.    
> 각 요청을 독립적으로 처리하며, 이전 요청에 대한 정보를 유지하지 않는다.     
> 상태관리가 없어 일반적으로 응답 속도가 빠르며, 지속적인 스토리지가 없어 비용이 낮다.
{: .prompt-info }

#### 썸네일 생성 예시

활성 서버가 실행되지 않는 event driven architecture

![image](https://github.com/user-attachments/assets/2b651731-25df-4d98-a011-cba3d8a32e97)
출처 : AWS pdf

1. S3에 사진을 업로드
2. S3가 람다를 트리거
3. Lambda는 다른 서비스를 사용하여 이미지 인식을 수행한 다음
4. 그 결과를 다시 S3 버킷에 저장


### Amazon Elastic Container Service(Amazon ECS)

+ 컨테이너 실행 오케스트레이션으로 컨테이너에 대한 관리를 지원하는 서비스
+ 컨테이너를 실행하는 여러 노드 유지 관리 및 확장
+ 인프라 구축의 복잡성 해소

## 2. 스토리지

![image](https://github.com/user-attachments/assets/0fabddc8-0e90-430c-a46f-f5edd4030fff)
출처 : AWS pdf

### Amazon S3 

+ 객체 스토리지 서비스이며, 데이터를 키-값 형태로 저장
+ 파일 스토리지와 같은 파일 및 폴더구조가 사용되지 않고 버킷 내 모든 객체는 수평적으로 저장된다.
+ 매우 뛰어난 데이터 내구성을 제공
+ 이벤트 트리거 기능으로 객체 업로드, 삭제 등에 대한 이벤트가 발생하면 사용자에게 알림
+ Amazon S3에 업로드된 각 객체는 고유한 URL에 연결되어 정적 웹 사이트 호스팅에 사용 가능
+ 콘텐츠 저장 및 배포, 백업 및 아카이빙, 빅 데이터 분석, 재해 복구, 정적 웹 사이트 호스팅에 사용된다.


### Amazon Elastic Block Store(Amazon EBS)

+ 블록 스토리지 서비스로, Amazon EC2 인스턴스에 사용하는 영구 블록 스토리지 볼륨을 제공한다.
+ Amazon EBS의 유형
	+ 트랜잭션 워크로드를 위한 SSD 지원 스토리지
	+ 처리량 워크로드에 적합한 HDD 지원 스토리지
+ 특정 시점에 스냅샷을 생성해서 S3에 저장하며 증분식 백업으로 마지막 스냅샷 이후 변경된 디바이스의 블록만 저장한다.


## 3. 데이터베이스

![image](https://github.com/user-attachments/assets/860617c7-9f5f-4f11-8a61-8dbf25ce2727)
출처 : AWS pdf


### Amazon RDS (Amazon Relational Database Service)

+ 대표적인 관계형 데이터베이스 서비스
+ 효율적인 비용으로 확장 가능한 용량을 제공
+ AWS Database Migration Service를 통해 기존 DB를 Amazon RDS로 손쉽게 이동 가능
+ 여러 가용 영역에 DB인스턴스를 여러 대 프로비저닝 할 수 있어 DB 이중화를 쉽게 구축 ㅏ능

> **이중화란?**    
> DB 이중화는 데이터베이스의 안정성, 가용성, 그리고 성능을 향상시키기 위한 기술     
> 주로 마스터(Master)와 슬레이브(Slave) 구조를 사용하며 마스터 DB에서 데이터 변경이 발생하면 슬레이브 DB로 복제    
> 실시간 데이터 백업: 한 서버에 문제가 생겨도 다른 서버의 데이터를 사용 가능    
> 부하 분산: 마스터 DB에서는 주로 데이터 변경 작업을, 슬레이브 DB에서는 조회 작업을 수행하여 서버 부하를 분산할 수 있다.
{: .prompt-info }

### Amazon Aurora

+ AWS에서 자체적으로 개발한 관계형 데이터베이스
+ MySQL 및 Postgre SQL과 호환
+ 상용 DB와 비교하여 데이터베이스 비용을 90% 낮추고, 안정성과 가용성을 높일 수 있다.

#### Amazon DynamoDB

어떤 규모에서든 10밀리초 미만의 지연시간을 요구되는 모든 애플리켜션을 위한 빠르고 유연성이 뛰어난 **비관계형 데이터베이스 서비스**이며 문서모델과 키-값 모델을 지원한다.

![image](https://github.com/user-attachments/assets/4e2cd663-0019-4349-aeee-e3836b679b3a)

+ Document Store
	+ 데이터가 JSON 문서로 계층적으로 저장된다. 
+ Key-Value Store
	+ 데이터가 키-값 형태로 저장된다.
+ Wide-Column Store
	+ 데이터가 열 내부에서 키-값 형태로 저장된다.
+ Graph Store   
	+ 데이터가 그래프 구조에 노드와 관계 형태로 저장된다.


## 4. 네트워킹



워크로드란? 
VPC로 IP 주소 범위 선택, 서브넷 생성, 라우팅 테이블 및 인터넷 게이트웨이 구성 등 가상 네트워킹 환경을 제어할 수 있으며 계층적으로 네트워크를 방어할 수 있다.
### Amazon Virtual Private Cloud(Amazon VPC)

+ VPC는 논리적으로 격리된 개인 네트워크 공간
+ VPC로 IP 주소 범위 선택, 서브넷 생성, 라우팅 테이블 및 인터넷 게이트웨이 구성 등 가상 네트워킹 환경을 사용자가 제어할 수 있다.
+ Amazon EC2를 VPC에 배치하기 위해서는 **서브넷**이 필요하다.
	+ 서브넷은 VPC, IP 주소 범위내의 세그먼트 또는 파티션으로 볼 수 있고, 서브넷을 생성하여 워크로드를 네트워크에서 격리하는 기능을 제공한다. 
	+ 서브넷은 기본적으로 외부에서 접근 불가능한 프라이빗 서브넷이다.
	+ 프라이빗 서브넷을 퍼블릭 서브넷으로 바꿔보기
		1. 인터넷 게이트웨이 서비스를 생성하여 VPC 연결
		2. 해당 서브넷의 라우팅 테이블을 이용하여 트래픽을 인터넷 게이트 웨이로 향하게 지정
		3. 퍼블릭 서브넷에 있는 서비스가 외부에서 접근할 수 있는 퍼블릭 IP 주소를 가져야한다.

#### 인프라 보안

![image](https://github.com/user-attachments/assets/5d739b68-ef8a-4fce-af1a-78375f60bbb1)

VPC의 보안그룹네트워크 액세스 제어목록 및 라우팅테이블을 포함한 보안계층을 사용해 각 서브넷에서 Amazon EC2 인스턴스 액세스를 제어할 수 있다.

1. 라우팅 테이블
	+ 네트워크 트래픽이 전달되는 위치를 제어 가능
	+ 트래픽이 전달되는 위치를 상세하게 제어할 수 있기 때문에 일종의 첫번째 관문
	+ 라우팅 테이블에 해당 규칙이 없으면 접근 불가능
	+ VPC, 게이트웨이 및 서브넷 연결하여 사용
2. 네트워크 액세스 제어 목록(네트워크 ACL)
	+ 서브넷 범위의 인바운드와 아웃바운드를 제어하기 위한 방화벽 역할을 하는 선택적 보안 계층
	+ 규칙을 이용한 SSH 접속과 같은 유형 프로토콜, 포트범위 소스 대상을 지정하여 서브넷과 주고받는 트래픽을 허용 또는 거부 가능
3. 보안그룹 (Security Group)
	+ 인스턴스에 대한 인바운드 및 아웃바운드 트래픽을 제어하는 가상 방화벽 의미
	+ 서브넷 범위가 아닌 인스턴스를 보호하기 때문에 하나의 서브넷 안에 여러 인스턴스를 각각 다른 보안 그룹으로 지정 가능
	+ 허용하고자 하는 규칙을 지정하여 트래픽 제어

### ELB (Elastic Load Balancing)

+ ELB는 들어오는 애플리케이션 트래픽을  Amazon EC2 인스턴스, 컨테이너, IP 주소, Lambda 함수 등 여러 대상에 자동으로 분산시키는 로드 밸런싱 서비스다.

![image](https://github.com/user-attachments/assets/e389f982-fff9-4f65-91cd-03259ee0c6da)

1. 고가용성을 제공하여 여러 가용 영역에 걸쳐 트래픽을 분산시키는 것이 특징이다.
2. 트래픽 증가에 따라 자동으로 용량을 조절하는 자동 확장 기능을 갖추고 있다.
3. SSL/TLS 암호화 지원과 Amazon VPC 통합을 통해 강화된 보안을 제공한다.
4. 여러 EC2 인스턴스나 컨테이너에 걸쳐 트래픽을 균등하게 분산시키는 기능이 있다.
5. 주기적으로 대상의 상태를 확인하고 비정상 대상으로의 트래픽을 중단하는 상태 확인 기능을 제공한다.
6. Application, Network, Gateway, Classic 등 다양한 유형의 로드 밸런서를 제공한다.
7. CloudWatch와의 통합을 통해 상세한 모니터링과 로깅 기능을 제공한다.
8. 콘텐츠 기반, 경로 기반 등 다양한 라우팅 옵션을 제공하여 유연한 트래픽 관리가 가능하다.
9. 사용자 세션을 특정 인스턴스에 고정할 수 있는 세션 고정 기능을 제공한다.
10. 사용량에 따른 요금 부과와 별도의 인프라 관리가 필요 없어 비용 효율적이다.


### Amazon Route 53

+ Amazon Route 53은 가용성과 확장성이 뛰어난 클라우드 DNS 서비스이다.
+ 웹 애플리케이션을 위한 도메인 이름을 등록할 수 있다. 사용자가 웹 브라우저의 주소창에 도메인 이름을 입력하면 Route 53은 이를 IP 정보로 변경하여 전달하고, 브라우저는 이 IP 주소로 웹사이트에 연결할 수 있다.
+ 다양한 라우팅 옵션을 제공한다. 지역 기반 라우터를 예로 들자면 싱가포르 접근 사용자는 싱가포르 리전으로, 버지니아 북부 접근 사용자는 버지니아 북부 리전으로 접속하도록 트래픽을 라우팅할 수 있다.

## 5. 보안

### AWS IAM(AWS Identity and Access Management)

+ AWS 서비스와 리소스에 대한 액세스를 안전하게 관리하기 위한 서비스이다.
+ 누가 AWS 리소스를 사용할 수 있는가에 대한 **인증** 그리고 무엇을 할 수 있는가에 대한 **인가**를 제어하는 서비스이다.

#### IAM 구성

![image](https://github.com/user-attachments/assets/1590f584-0ca6-4a80-a8ee-21aa59be61f5)

1. 사용자(User):
	+ AWS 리소스와 상호 작용하는 개별 사람 또는 서비스를 나타내며, 고유한 보안 자격 증명을 가지는 엔티티이다.
2. 그룹(Group):
	+ IAM 사용자들의 집합으로, 여러 사용자에게 공통된 권한을 한 번에 할당할 수 있어 관리가 용이한 단위이다.
3. 역할(Role):
	+ 특정 권한을 가진 IAM 자격 증명으로, 사용자나 AWS 서비스가 일시적으로 맡을 수 있는 권한 세트를 정의하는 메커니즘이다.
4. 권한(Permission):
	+ AWS 리소스에 대해 수행할 수 있는 작업을 정의하며, JSON 형식의 정책 문서로 표현되는 액세스 제어 단위이다.
5. 정책(Policy):
	+ 권한을 정의하는 문서로, 사용자, 그룹, 역할에 연결하여 AWS 리소스에 대한 액세스를 제어하는 핵심 요소이다.


#### AWS CloudTrail

+ 사용자 활동 및 API 사용을 추적하여 감사 보안 모니터링 및 운영 문제 해결 지원
+ 요청한 사람, 사용된 서비스, 수행된 작업, 파라미터, AWS에서 반환한 대응 요소를 비롯하여 주요 정보를 기록
+ 로그파일은 Amazon S3 버킷에 저장되어 확인 가능