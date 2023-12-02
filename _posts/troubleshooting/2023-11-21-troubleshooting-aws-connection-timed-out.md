---
title: "[AWS] RDS Connection timed out 연결 오류 해결"
date: 2023-11-21 23:00:00 +09:00
categories: [IT, AWS]
tags: [TIL, AWS, TroubleShooting]
image: /assets/img/posts/trouble-shooting.png
---

## 에러

RDS를 연결하는 도중 생긴 에러이다. 몇시간 동안 에러를 해결을 못해서 삭제하고 새로 생성만 반복한지 모르겠다. 인바운드 규칙도 잘못되었나 했는데 한 블로그를 보고 해결이 되었다.

### 1. 퍼블릭 액세스 여부

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/c5c4df10-aedb-4ef5-ba21-ef2bf57812d0)

처음에는 퍼블릭 액세스 여부를 "아니요"로 해서 생긴 에러인 줄 알았으나 다시 예로 생성해도 똑같았다. 이 부분은 잘 확인해야 한다.



### 2. 인바운드 규칙도 확인해야한다.
![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/3d5cbab4-6c47-4da0-80ea-faa299b32cd0)

0.0.0.0/0을 하여 들어가는 것은 모든 사용자가 해당 RDS에 접근할 수 있다는 것이므로 주의해야한다.

<br/>

이렇게 해도 해결이 안되자 구글링으로 RDS의 서브넷이 private이어서 발생하는 것이라고 찾게되었다.AWS에서 UI를 자꾸 변경하는 것 같았다. 처음 본 블로그에서 잘 안되어서 좀 더 찾게 되었다.


## 해결

### 1. VPC 라우팅 테이블로 이동
![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/531389a9-b73d-4297-be36-28d7b7db7e41)
AWS에서 VPC ➝ 라우팅 테이블 메뉴로 이동한다.
이후 RDS가 사용하는 라우팅 테이블을 클릭하고, 작업 ➝ 라우팅 편집을 클릭한다.

### 2. 라우팅 편집
![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/ca1fc8d7-4053-44b0-8748-f95ff9574bca)

0.0.0.0/0을 입력 후 대상 부분을 인터넷 게이트웨이로 지정하면, igw-0000 이 나타난다. 변경 사항 저장 후 연결하면 RDS가 연결이 잘 된다!



## 참고

+ [RDS Connection timed out 연결 오류 해결-2](https://green-bin.tistory.com/34)
