---
title: 항해99 일지(31) - Final Project Start
date: 2023-12-04 18:00:00 +09:00
categories: [IT, WIL]
tags: [ wil]
image: /assets/img/posts/til.png
published: false
---

## Final Project 시작

이번엔 팀원이 4명이다. 박정민님, 이현범님, 이진서님 그리고 나이다. 이번 프로젝트는 대용량 트래픽을 다뤄보려하는데 잘 되면 좋겠다. 우리는 다양한 기술을 많이 사용할 예정인데, 어떻게 변할지는 잘 모르겠다. CI/CD를 이용할 것이고, Ecs, Elastic Search, WebFlux 등등 사용할 예정인데 열심히 공부해서 완성할 수 있으면 좋겠다.

<br/>

사실 기술을 뭘 사용할지 아직 제대로 정하지 않았지만, 앞으로 사용해야할 기술들을 정리해 보았다. 그리고 성공한 CI/CD를 이번에는 잘 정리하여서 팀장님에게 공유해줬더니, 팀장님이 보고 CI/CD 구현을 쉽고 빠르게 할 수 있었다고 감사하다하여 뿌듯했다. 

<br/>


## 기술 리스트

### 인프라

- CI/CD : Github Action 
    - 선택지 : Jenkins / GithubAction
    - CD : Jenkins 작업 속도가 더 빠르다. / ArgoCD
    - 참고 사이트 : [https://velog.io/@kimseungki94/Jenkins-vs-Github-Action-어떤걸-쓰는게-좋을까](https://velog.io/@kimseungki94/Jenkins-vs-Github-Action-%EC%96%B4%EB%96%A4%EA%B1%B8-%EC%93%B0%EB%8A%94%EA%B2%8C-%EC%A2%8B%EC%9D%84%EA%B9%8C)
    - 블로그 참고한 것들 정리
- Docker container image
- Ecs
- 로드밸런싱
    - 선택지 : docker compose / Nginx(오픈소스) / ELB
    - ELB 참고 사이트:
        - [https://rachel0115.tistory.com/entry/AWS-로드-밸런서-구축하기-ELB](https://rachel0115.tistory.com/entry/AWS-%EB%A1%9C%EB%93%9C-%EB%B0%B8%EB%9F%B0%EC%84%9C-%EA%B5%AC%EC%B6%95%ED%95%98%EA%B8%B0-ELB)
        - AWS의 모든것 강의 한번 듣기

### 모니터링

- 선택지 : Grafana / Prometheus / kibana / Elastic Apm

- Grafana
    - 장점
        - 시스템 관점(cpu, 메모리, 디스크)의 메트릭 지표를 시각화 하는데 특화
        - 다양한 데이터베이스를 선택 가능
        - 맞춤형 경고 알람기능 무료로 사용 가능
        - 데이터 주석 또는 스냅샷과 같은 다양한 추가 기능 제공
    - 단점
        - 주요 초점이 메트릭 분석에 있기 때문에 로그 분석에 대한 지원이 좋지않다.
        - 데이터 수집 및 저장은 별도로 설정해야한다

- Prometheus
    - 그라파나를 통한 시각화 지원
    - 많은 시스템을 모니터링 할 수 있는 다양한 플러그인 보유
    - 쿠버네티스의 메인 모니터링 시스템으로 많이 채택
    - 주기적으로 exproter(모니터링 대상 시스템)으로 부터 pulling 방식으로 메트릭을 읽어서 수집
- kibana
    - 장점
        - 추카 코딩이나 인프라가 필요하지 않다.
        - 데이터를 시각화하는 맞춤형 방법을 생성하는 기능을 제공
        - 내장형 이상 감지 시스템 보유
        - 잘 설계된 사용자 인터페이스 보유
        - 데이터 공유 및 내보내기 기능 제공
    - 단점
        - 로그 분석 및 기타 로그 종속 데이터 분석에 있어 메트릭 분석에 대한 지원이 좋지않음
        - ElasticStack의 제한 사항에 영향을 받음

- Elastic APM(Application Perfomance Monitoring)
    - 애플리케이션의 성능 모니터링 및 디버깅을 목적으로 사용
    - 느린 트랜잭션을 식별하여 병목이 일어나는 구간 파악 가능
    - Request의 급증 및 급감 파악 가능

### 데이터 베이스 (KEEP)

- 선택지 : MySQL / PostgreSql / MongoDB / Cassandra

- MySQL
    - 장점
        - PostgreSQL보다 빠르다.
        - 조회 성능이 잘 나온다.
        - 안정성과 신뢰성이 높고, 다양한 커뮤니티가 존재하여 레퍼런스를 찾기 쉽다.
    - 단점
        - 지원하는 쿼리가 단순하다.
        - 동시성 제어의 성능이 PostgreSQL에 비해 부족하다.
- PostgreSql
    - 장점
        - 쓰기 조회에 성능이 잘나옴
        - 다중버전 동시성 제어 제공
    - 단점
        - 메모리 서능이 떨어진다.
            - 읽기가 많은 간단한 작업의 경우 RDBMS보다 성능이 떨어진다.
        - Update 쿼리에 약하다
            - Update 반복적으로 수행해야하는 응용서비스에는 제약이 있다

- MongoDB - 빅데이터 처리 특화
    - 장점
        - Schema-less 구조
            - 다양한 형태의 데이터 저장 가능
        - Read/Write 성능 뛰어남
        - Scale out 구조
            - 많은 데이터 저장이 가능
            - 장비 확장이 간단
        - JSON 구조
        - 사용방법 간단, 개발이 편리
        - 분산 목적 시 RDBMS에 비해 낮은 비용과 빠른 성능을 제공
    - 단점
        - 데이터 업데이트 중 장애 발생 시 데이터 손실 가능
        - 많은 인덱스 사용 시 충분한 메모리 확보 필요
        - 제공되는 MapReduce 작업이 Hadoop에 비해 성능이 떨어짐
        - 복잡한 JOIN 사용 시 성능 제약이 따름
- ~~Cassandra~~
https://goyunji.tistory.com/95
    - 장점
        - 분산화와 집중화
            - 여러 머신에서 동작하지만 사용자에게는 하나로 보인다.
        - 탄력적인 확장성
            - 약간의 성능저하를 동반하지만 더 많은 요청을 처리할 수 있는 시스템의 아키텍처 특징
            - 클러스터의 중단없이 규모 확대 축소 가능
        - 고가용성과 결합 허용
            - 시스템을 중단하지않고 장애가 발생한 노드 교체 가능
            - 다중 데이터 센터에 데이터를 복제해 로컬 성능 개선 가능
    - 단점
        - 높은 진입장벽
            - 컬럼형 데이터베이스로 로우형 데이터베이스인 관계형 데이터베이스와 다른 개념
        - 복잡한 조건의 검색 불가
            - 로우 키와 칼럼 두가지에 대한 인덱스만 가능
            - 대량이지만 검색 조건은 단순한 서비스에 적합
        - 자동화 처리 어려움
            - 데이터에 대한 **동시 갱신 요청(동시성제어?)이 발생할 가능성이 높거나** 자동화한 트랜잭션이 필요한 서비스에서는 다른 데이터베이스를 고려해야한다.

### 데이터 파이프라인

- 선택지 : redis / kafka
- Kafka
    - 장점
        - 분산형 아키텍처를 채택하고 있어 대용량 데이터 처리 안정적
        - 대용량 데이터 실시간 처리 가능
    - 단점
        - 구성 복잡,  일정 수준의 기술적 이해와 노하우가 필요
        - 설정 잘못할 시 시스템 동작에 오류 유발 가능성 있음
- Redis (**Re**mote **Di**ctionary **S**erver)
    - 장점
        - 높은 성능, 간단한 데이터 모델, 다양한 데이터 구조 지원, 지속성 옵션, 레플리케이션 및 분산 환경 지원
    - 단점
        - 메모리 사용, 단일 스레드 모델, 복잡한 쿼리 처리, 데이터 복제의 지연

### 클러스터링(Ecs 지원)

- 선택지 : Docker swarm / kubernetes
- Docker swarm
    - 장점
        - 단순성: 초기 설정이 간단하며, 작은 규모의 클러스터에 적합
        - 통합: 도커 엔진과 통합되어 있어 도커 명령어와 연계하여 사용 가능
- kubernetes
    - 장점
        - 애플리케이션 배포의 단순화
        - 애플리케이션 개발의 단순화
        - 컴퓨팅 리소스 활용
        - 지속적인 상태 확인과 셀프 힐링
        - 오토스케일링

### 소켓통신

- 웹소켓

### 로깅관리

- 선택지 : logback / log4j / log4j2

- log4j
    - 콘솔로  출력하는 stdout 외에도 파일 출력을 제공한다.
    - 2015년에 개발이 중단됨 기존 시스템이 아니라면 사용할 이유가 없다.
- logback - log4j는 보안성 이슈로인해 logback을 선택
    - log4j2 전에 개발된 로깅프로그램
    - log4j에서 향상된 성능과 필터링 옵션을 제공한다.
    - slf4j도 지원한다. 그리고 자동 리로드도 가능하다.
- log4j2
    - logback과 동일하게 자동 리로드 기능과 필터링 기능을 제공한다.
    - log4j 및 logback와 차이점은 Apache에 따르면 멀티 쓰레드 환경에서 비동기 로거(Async Logger)의 경우 처리량이 18배 더 높고 대기 시간이 훨씬 짧다.
    - 람다 표현식과 사용자 정의 로그 레벨도 지원한다.

### 검색 성능 개선

- Elasticsearch QueryDSL/ MongDB Index
- 참고 사이트
    - https://kkambi.tistory.com/193
- Elasticsearch QueryDSL
    - 장점
        - 검색 성능이 빠르다.
        - 확장성이 뛰어나다.
        - RESTful API를 제공한다.
        - 다양한 플러그인을 지원한다.
        - 다양한 검색 기능을 제공한다.
    - 단점
        - 인덱싱에 대한 고민이 필요하다.
        - 운영이 복잡하다.
        - 복잡한 쿼리 작성이 필요하다.
        - 메모리 사용량이 높다.
        - 문서의 크기가 큰 경우 처리 속도가 느려질 수 있다.
    
- MongDB Index
    - 장점
        - 검색 속도 향상 : 인덱스를 사용하면 데이터를 빠르게 검색할 수 있다. 특히 대용량의 데이터베이스에서는 이 속도 향상이 두드러집니다.
        - 정렬 및 그룹화 성능 향상 : 인덱스를 통해 데이터를 정렬하거나 그룹화하는 작업에서 성능이 향상된다.
        - 유니크 제약 조건 지원 : 유니크한 값을 갖는 필드에 인덱스를 생성하여 중복을 방지할 수 있다.
        - 쿼리 최적화 : 쿼리 성능을 최적화하기 위해 인덱스를 사용할 수 있다.
    - 단점
        - 저장 공간 소비 : 인덱스는 추가적인 저장 공간을 차지하므로 데이터베이스의 크기가 증가한다.
        - 쓰기 연산 속도 저하 : 인덱스가 많으면 쓰기 연산(데이터 추가, 갱신, 삭제)의 속도가 저하될 수 있다. 인덱스를 업데이트하는데 시간이 걸리기 대문
        - 적절하지 않은 인덱스 선택으로 인한 성능 감소 : 적절하지 않게 선택한 인덱스는 오히려 성능을 감소시킬 수 있다.

### 부하 테스트(KEEP)

- Jmeter, Ngrinder
- Jmeter
    - Apache에서 만들었으며 순수하게 자바로 만들어진 웹 어플리케이션 성능 테스트 오픈 소스
    - GUI를 제공 Logic controller라고 불리는 기능은 GUI에서 테스트를 설정할 수 있는 뛰어난 유연성을 제공
    - 다양한 프로토콜/서버를 테스트할 수 있으며 CLI또한 지원한다.
    - 다양한 외부 플로그인을 사용하여 기능 확장이 가능하다.
    - 여러 튜토리얼을 보면 GUI설정을 통해 테스트할 수 있으며 .jmx 파일을 통해 CLI로도 테스트 할 수 있다.
- nGrinder
    - 네이버에서 성능 측정 목적으로 개발된 오픈소스 프로젝트
    - Jython, Groovy 두 가지 중 하나를 선택하여 스크립트를 작성한다.
    - 또한 Junit 기반으로 되어 있어 IDE에서 먼저 확인해보고 디버깅할 수 있다.
    - 스크립트를 수정할 수 있어 세밀한 성능 테스트가 가능하다.
    - 개발자가 교착 상태와 속도 저하를 찾을 수 있도록 구축되었다.






