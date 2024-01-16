---
title: "EC2 & 탄력적 IP"
date: 2023-11-23 23:01:00 +09:00
categories: [IT, AWS, CI/CD]
tags: [til, AWS, CI/CD, Github Actions]
image: /assets/img/posts/aws.png
---

## 탄력적 IP 설정
EC2 인스턴스는 재가동 시 퍼블릭 IP가 변경된다. 따라서 지속적인 개발환경에서 사용하려면 고정적인 IP주소가 필요하다. 현재 IP주소를 고정해보자.

### 1. 탄력적 IP 주소 이동

![instance](https://github.com/honge7694/honge7694.github.io/assets/76715487/6fb8cb47-7c62-49cf-b944-a47dcc62fea9)


### 2. 탄력적 IP 주소 연결 및 생성
왼쪽 사이드바를 보면 네트워크 및 보안 그룹에 탄력적 IP가 있다. 이것을 클릭하여 들어가여 **탄력적 IP 주소 할당**을 누른 후 생성을 해준다.

![ip](https://github.com/honge7694/honge7694.github.io/assets/76715487/0db57fca-2461-472e-b85f-fe322483dfda)

할당 된 주소를 눌러 탄력적 IP 주소 연결을 해주자.

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/ef9c105c-7b01-4ef6-bed2-f6e0ed60c45c)

위와 같이 클릭하여 전에 [EC2 생성](https://honge7694.github.io/posts/aws-ec2-start/)한 인스턴스를 선택하고, 프라이빗 IP주소에도 선택해 연결해준다.

### 3. 탄력적 IP 연결 확인

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/e44c3e49-5108-458b-8591-c1f76ed1afb0)

생성된 인스턴스에 가보면 처음에 없던 탄력적 IP주소가 연결된 것을 확인할 수 있다.

