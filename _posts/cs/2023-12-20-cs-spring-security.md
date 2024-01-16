---
title: "Spring Security의 구조와 JWT 발급 과정"
date: 2023-12-20 18:00:00 +09:00
categories: [IT, CS]
tags: [TIL, CS, Spring Security]
image: /assets/img/posts/cs.png
---

## Spring Security와 JWT

### Spring Security 구조

Spring Security는 인증(Authentication)과 권한(Authorization)을 관리하는 스프링 기반의 보안 프레임워크입니다. 주로 필터 기반의 아키텍처로 구성되어 있으며, 다양한 보안 기능과 확장 가능한 인터페이스를 제공합니다.

<br/>

필터 체인은 각 필터가 특정한 보안 기능을 담당하며, 순차적으로 실행되어 요청의 인증 및 권한 검사를 수행합니다. AuthenticationManager는 AuthenticationProvider를 통해 실제 인증을, UserDetailsService는 UserDetails를 제공하여 사용자 정보를 관리합니다. 

<br/>

SecurityContextHolder는 현재 스레드의 보안 컨텍스트를 제공하여 여러 부가적인 보안 작업을 수행하며, AccessDecisionManager는 권한 부여 결정을 내리는 역할을 합니다



### JWT 발급과정

![image](https://github.com/honge7694/honge7694.github.io/assets/76715487/2d58b2fc-a006-4603-8fea-d19126859aa8)

1. 사용자가 로그인 정보와 함께 인증 요청(Http Request)
2. AuthenticationFilter가 요청을 가로채고, 가로챈 정보를 통해 UsernamePasswordAuthentication Token의 인증 객체 생성
3. AuthenticationManager의 구현체인 Providermanager에게 생성한 UsernamePasswordToken객체를 전달
4. AuthenticationManager는 등록된 AuthenticationProvider을 조회하여 인증을 요구
5. DB에서 사용자 인증정보를 가져오는 UserDetailsService에 사용자 정보를 반환
6. 넘겨받은 사용자 정보를 통해 DB에서 찾은 사용자 정보인 UserDetails 객체를 생성
7. AuthenticationProvider는 UserDetails를 넘겨받고 사용자 정보를 비교
8. 인증이 완료되면 권한 등의 사용자 정보를 담은 Authentication 객체를 반환
9. 다시 최초의 AuthenticationFIlter에 Authenticatio 객체가 반환
10. Authentication 객체를 SecurityContext에 저장

<br/>

최종적으로 SecurityContextHolder는 세션 영역에 있는 SecurityContext에 Authentication 객체를 저장한다.
사용자 정보를 저장한다는 것은 Spring Security가 전통적인 세션-쿠키 기반의 인증 방식을 사용한다는 것을 의미한다.

## 참고

+ [T 블로그](https://dev-coco.tistory.com/174)