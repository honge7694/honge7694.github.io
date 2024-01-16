---
title: 항해99 일지(25) - SpringBoot Cookie, Session, JWT
date: 2023-11-10 14:00:00 +09:00
categories: [IT, Spring, TIL]
tags: [Spring Boot, session, cookie, jwt]
image: /assets/img/posts/spring-boot-start.png
---

## 인증과 인가

- **인증(Authentication)**
    - 인증은 해당 유저가 실제 유저인지 인증하는 개념이다.
    - 스마트폰에 지문인식, 이용하는 사이트에 로그인 등과 같이, 실제 그 유저가 맞는지를 확인하는 절차이다.
- **인가(Authorization)**
    - 인가는 해당 유저가 특정 리소스에 접근이 가능한지 허가를 확인하는 개념이다. 예를들어 관리자 페이지-관리자 권한 같은 것들을 들 수 있다.

<br />

## 쿠키와 세션
쿠키, 세션 방식은 서버가 ‘특정 유저가 로그인 되었다’는 상태를 저장하는 방식이다.    
인증과 관련된 아주 약간의 정보만 서버가 가지고 있게 되고 유저의 이전 상태의 전부는 아니더라도 인증과 관련된 최소한의 정보는 저장해서 로그인을 유지시킨다는 개념이다.

|            | 쿠키                                        | 세션                                   |
|------------|---------------------------------------------|----------------------------------------|
| 설명       | 클라이언트에 저장될 목적으로 생성한 작은 정보를 담은 파일 | 서버에서 일정 시간 동안 클라이언트 상태를 유지하기 위해 사용 |
| 저장 위치  | 클라이언트 (웹 브라우저)                      | 웹 서버                                |
| 사용 예    | 사이트 팝업의 "오늘 다시보지 않기" 정보 저장       | 로그인 정보 저장                       |
| 만료 시점  | 쿠키 저장 시 만료일시 설정 가능<br/> (브라우저 종료 시도 유지 가능) | 다음 조건 중 하나가 만족될 경우 만료됨: <br/> 1. 브라우저 종료 시까지 <br/> 2. 클라이언트 로그아웃 시까지 <br/> 3. 서버에 설정한 유지 기간까지 해당 클라이언트의 재요청이 없는 경우 |
| 용량 제한  | 브라우저 별로 다름 (크롬 기준):<br/> - 하나의 도메인 당 180개<br/> - 하나의 쿠키 당 4KB(=4096byte) | 개수 제한 없음<br/> (단, 세션 저장소 크기 이상 저장 불가능) |
| 보안       | 취약<br/> (클라이언트에서 쿠키 정보를 쉽게 변경, 삭제 및 가로채기 당할 수 있음) | 비교적 안전 <br/>(서버에 저장되기 때문에 상대적으로 안전) |


<br/>

### 쿠키, 세션 순서
1. 사용자 로그인
	+ 사용자가 로그인 페이지로 이동하거나 로그인 요청을 보낸다.

2. 인증 처리
	+ 사용자가 입력한 아이디와 비밀번호를 이용하여 서버에서 인증을 수행한다.
	+ 사용자 정보를 데이터베이스에서 조회하여 유효성을 검사한다.
	+ 인증에 성공하면 사용자에 대한 정보를 세션에 저장하고, 세션 ID를 생성하여 클라이언트에게 전달한다.

3. 세션 생성 및 쿠키 전송
	+ 서버는 세션 ID를 이용하여 사용자 정보를 세션에 저장한다.
	+ 서버는 클라이언트에게 응답을 할 때, 세션 ID를 쿠키로 생성하여 클라이언트에게 전송한다.

4. 클라이언트 쿠키 저장
	+ 클라이언트는 받은 쿠키를 저장하고, 이후의 요청에서는 저장된 쿠키를 함께 서버로 전송한다.
	+ 서버는 클라이언트를 식별하고, 세션에 저장된 사용자 정보를 유지할 수 있다.

5. 인증 확인 및 서비스 제공
	+ 클라이언트의 요청이 올 때마다 서버는 세션 ID를 이용하여 세션에서 사용자 정보를 확인한다.
	+ 인증된 사용자에 대해 요청에 대한 서비스를 제공하고, 인증되지 않은 사용자에 대해서는 로그인 페이지로 리다이렉트하거나 에러 응답을 반환한다.

### 쿠키 코드

#### 생성
```java
public static void addCookie(String cookieValue, HttpServletResponse res) {
    try {
        cookieValue = URLEncoder.encode(cookieValue, "utf-8").replaceAll("\\+", "%20"); // Cookie Value 에는 공백이 불가능해서 encoding 진행

        Cookie cookie = new Cookie(AUTHORIZATION_HEADER, cookieValue); // Name-Value
        cookie.setPath("/");
        cookie.setMaxAge(30 * 60);

        // Response 객체에 Cookie 추가
        res.addCookie(cookie);
    } catch (UnsupportedEncodingException e) {
        throw new RuntimeException(e.getMessage());
    }
}
```

+ `new Cookie(AUTHORIZATION_HEADER, cookieValue)`;
	+ Name과 Value를 생성자로 받는 Cookie 객체 생성
+ `setPath("/")`, `setMaxAge(30 - 60)`
	+ 쿠키가 사용될 경로와 쿠키 만료기한을 저장
+ `HttpServletResponse` 객체에 생성한 Cooke 객체를 추가하여 브라우저 반환
	+ 브라우저의 Cookie 저장
+ Cookie 생성은 범용적으로 사용될 수 있기 떄문에 `static` 메서드로 선언

#### 읽기

```java
@GetMapping("/get-cookie")
public String getCookie(@CookieValue(AUTHORIZATION_HEADER) String value) {
    System.out.println("value = " + value);

    return "getCookie : " + value;
}
```

+ `@CookieValue("Cookie의 Name")`
	+ Cookie의 Name 정보를 전달해주면 해당 정보를 토대로 Cookie의 `Value`를 가져온다.

#### 전체 코드

```java
@RestController
@RequestMapping("/api")
public class AuthController {

    public static final String AUTHORIZATION_HEADER = "Authorization";

    @GetMapping("/create-cookie")
    public String createCookie(HttpServletResponse res) {
        addCookie("Robbie Auth", res); // 공백이 있으면 에러가 난다. addCookie 메서드에서 에러 처리 가능.

        return "createCookie";
    }

    // 쿠키 GET
    @GetMapping("/get-cookie")
    public String getCookie(@CookieValue(AUTHORIZATION_HEADER) String value) {
        System.out.println("value = " + value);

        return "getCookie : " + value;
    }

    // 쿠키 생성
    public static void addCookie(String cookieValue, HttpServletResponse res) {
        try {
            cookieValue = URLEncoder.encode(cookieValue, "utf-8").replaceAll("\\+", "%20"); // Cookie Value 에는 공백이 불가능해서 encoding 진행

            Cookie cookie = new Cookie(AUTHORIZATION_HEADER, cookieValue); // Name-Value 형식
            cookie.setPath("/");
            cookie.setMaxAge(30 * 60); // 만료 기한

            // Response 객체에 Cookie 추가
            res.addCookie(cookie);
        } catch (UnsupportedEncodingException e) {
            throw new RuntimeException(e.getMessage());
        }
    }
}
```


<br/>

### 세션 코드

#### 생성
```java
@GetMapping("/create-session")
public String createSession(HttpServletRequest req) {
    // 세션이 존재할 경우 세션 반환, 없을 경우 새로운 세션을 생성한 후 반환
    HttpSession session = req.getSession(true);

    // 세션에 저장될 정보 Name - Value 를 추가합니다.
    session.setAttribute(AUTHORIZATION_HEADER, "Robbie Auth");

    return "createSession";
}
```

+ `HttpServletRequest`를 사용하여 세션을 생성 및 반환 한다.
+ `req.getSession(true)`
	+ 세션이 존재할 경우 세션을 반환하고 없을 경우 새로운 세션을 생성한다.
+ `req.getSession(false)`
	+ 세션이 존재할 경우 세션을 반환하고 없을 경우 `null`을 반환한다.
+ 세션에 저장할 정보를 Name-Value 형식으로 추가한다.
+ 반환된 세션은 브라우저 Cookie 저장소에 `JSESSIONID`라는 Name으로 Value에 저장된다.

#### 읽기

```java
@GetMapping("/get-session")
public String getSession(HttpServletRequest req) {
    // 세션이 존재할 경우 세션 반환, 없을 경우 null 반환
    HttpSession session = req.getSession(false);

    String value = (String) session.getAttribute(AUTHORIZATION_HEADER); // 가져온 세션에 저장된 Value 를 Name 을 사용하여 가져옵니다.
    System.out.println("value = " + value);

    return "getSession : " + value;
}
```

+ `session.getAttribute("세션에 저장된 정보 Name")`
	+ Name을 사용하여 세션에 저장된 Value를 가져온다.

<br/>

## JWT (Json Web Token)

JWT 기반 인증은 쿠키/세션 방식과 유사하게 JWT 토큰(Access Token)을 HTTP 헤더에 실어 서버가 클라이언트를 식별한다.

| 장점                                   | 단점                                              |
|----------------------------------------|---------------------------------------------------|
| 동시 접속자가 많을 때 서버 측 부하를 낮출 수 있음 | 구현의 복잡도가 증가함                              |
| Client와 Server가 다른 도메인을 사용할 때 유용 <br/> (예: 카카오 OAuth2 로그인 시 JWT Token 사용) | JWT에 담는 내용이 커질수록 네트워크 비용이 증가함 |
|                                        | 생성된 JWT 일부만 만료시킬 방법이 없음              |
|                                        | Secret key 유출 시 JWT를 조작할 수 있음            |

<br/>

### JWT 순서 
1. 사용자 로그인
	+ 사용자가 아이디와 비밀번호로 로그인을 시도합니다.

2. 인증 처리
	+ 입력된 아이디와 비밀번호를 기반으로 사용자를 인증한다.
	+ 인증이 성공하면, 서버는 해당 사용자에 대한 정보를 기반으로 JWT를 생성한다.

3. JWT 생성
	+ 서버는 사용자 정보와 함께 JWT를 생성한다.
	+ JWT는 Header, Payload, Signature 세 부분으로 구성되어 있다.
		+ Header: 토큰의 타입과 사용된 해시 알고리즘 정보를 포함한다.
		+ Payload: 사용자 정보가 포함되며, 여기에는 사용자 아이디, 권한 등이 들어간다.
		+ Signature: Header와 Payload를 암호화한 값으로, 서버가 생성한 비밀 키로 검증된다.

4. JWT 응답
	+ 서버는 생성된 JWT를 클라이언트에게 응답으로 전송한다.
	+ 일반적으로는 HTTP 헤더나 응답 본문에 JWT를 포함시킨다.

5. 클라이언트 저장
	+ 클라이언트는 받은 JWT를 안전한 곳에 저장한다.
	+ 일반적으로는 브라우저의 쿠키나 로컬 스토리지에 저장된다.

6. 인증된 요청
	+ 사용자가 인증이 필요한 리소스에 접근할 때마다, 클라이언트는 저장한 JWT를 요청 헤더에 포함시켜 서버에 전송한다.

7. JWT 검증
	+ 서버는 받은 JWT를 검증한다.
	 + 서명을 통해 JWT의 무결성을 확인하고, 클레임의 정보를 확인하여 사용자를 인증한다.

8. 인증 완료
	+ 검증이 성공하면, 서버는 클라이언트의 요청을 수행하고 요청한 리소스를 반환한다.
	+ 인증이 실패하면, 클라이언트에게 오류 응답을 반환하거나 리다이렉트한다.


### JWT 코드

#### JWT 설정

```java
// build.gradle
dependencies {
    // JWT 추가
    compileOnly group: 'io.jsonwebtoken', name: 'jjwt-api', version: '0.11.5'
    runtimeOnly group: 'io.jsonwebtoken', name: 'jjwt-impl', version: '0.11.5'
    runtimeOnly group: 'io.jsonwebtoken', name: 'jjwt-jackson', version: '0.11.5'
	...
}
```

```java
// application.properties
jwt.secret.key=7Iqk7YyM66W07YOA7L2U65Sp7YG065+9U3ByaW5n6r~~~~
```
`base64`로 생성

#### JWT 데이터

```java
 // JWT 데이터

// Header KEY 값
public static final String AUTHORIZATION_HEADER = "Authorization";
// 사용자 권한 값의 KEY
public static final String AUTHORIZATION_KEY = "auth";
// Token 식별자
public static final String BEARER_PREFIX = "Bearer ";
// 토큰 만료시간
private final long TOKEN_TIME = 60 * 60 * 1000L; // 60분

@Value("${jwt.secret.key}") // Base64 Encode 한 SecretKey
private String secretKey;
private Key key;
private final SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256;


// 로그 설정 (로깅)
// 로깅이란?
// 어플리케이션 동작하는 동안 프로젝트 상태 또는 동작을 시간순으로 기록
public static final Logger logger = LoggerFactory.getLogger("JWT 관련 로그");



@PostConstruct
public void init() {
	byte[] bytes = Base64.getDecoder().decode(secretKey);
	key = Keys.hmacShaKeyFor(bytes);
}
```

+ `@Value("${키}")`
	+ `application.properties`에 있는 Value를 가져온다.
+ `@PostConstruct`
	+ 딱 한번만 받아오는 값을 사용할 때마다 요청을 새로 호출하는 실수를 방지해준다.

#### 사용자 권한

```java
package com.sparta.springauth.entity;

public enum UserRoleEnum {
    USER(Authority.USER),  // 사용자 권한
    ADMIN(Authority.ADMIN);  // 관리자 권한

    private final String authority;

    UserRoleEnum(String authority) {
        this.authority = authority;
    }

    public String getAuthority() {
        return this.authority;
    }

    public static class Authority {
        public static final String USER = "ROLE_USER";
        public static final String ADMIN = "ROLE_ADMIN";
    }
}
```
+ 다른 패키지에 사용자 권한 Enum을 만들어준다. 
+ USER와 ADMIN의 권한을 설정해준다.

#### 토큰 생성

```java
// 토큰 생성
public String createToken(String username, UserRoleEnum role) {
    Date date = new Date();

    return BEARER_PREFIX +
            Jwts.builder()
                    .setSubject(username) // 사용자 식별자값(ID)
                    .claim(AUTHORIZATION_KEY, role) // 사용자 권한
                    .setExpiration(new Date(date.getTime() + TOKEN_TIME)) // 만료 시간
                    .setIssuedAt(date) // 발급일
                    .signWith(key, signatureAlgorithm) // 암호화 알고리즘
                    .compact();
}
```

#### 토큰 저장

```java
// 생성된 JWT를 Cookie에 저장
public void addJwtToCookie(String token, HttpServletResponse res) {
	try {
		token = URLEncoder.encode(token, "utf-8").replaceAll("\\+", "%20"); // Cookie Value 에는 공백이 불가능해서 encoding 진행

		Cookie cookie = new Cookie(AUTHORIZATION_HEADER, token); // Name-Value
		cookie.setPath("/");

		// Response 객체에 Cookie 추가
		res.addCookie(cookie);
	} catch (UnsupportedEncodingException e) {
		logger.error(e.getMessage());
	}
}
```

#### 토큰 Substring

```java
public String substringToken(String tokenValue) {
	if (StringUtils.hasText(tokenValue) && tokenValue.startsWith(BEARER_PREFIX)) {
		return tokenValue.substring(7); // Bearer  공백까지 7글자
	}
	logger.error("Not Found Token");
	throw new NullPointerException("Not Found Token");
}
```

- `StringUtils.hasText`를 사용하여 공백, null을 확인하고` startsWith`을 사용하여 토큰의 시작값이 Bearer이 맞는지 확인한다.
- 맞다면 순수 JWT를 반환하기 위해 substring을 사용하여 Bearer을 잘라낸다.

#### 토큰 검증 및 가져오기

```java
// JWT 검증
public boolean validateToken(String token) {
	try {
		Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token);
		return true;
	} catch (SecurityException | MalformedJwtException | SignatureException e) {
		logger.error("Invalid JWT signature, 유효하지 않는 JWT 서명 입니다.");
	} catch (ExpiredJwtException e) {
		logger.error("Expired JWT token, 만료된 JWT token 입니다.");
	} catch (UnsupportedJwtException e) {
		logger.error("Unsupported JWT token, 지원되지 않는 JWT 토큰 입니다.");
	} catch (IllegalArgumentException e) {
		logger.error("JWT claims is empty, 잘못된 JWT 토큰 입니다.");
	}
	return false;
}

// JWT에서 사용자 정보 가져오기
public Claims getUserInfoFromToken(String token) {
	return Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token).getBody();
    }
```
- `Jwts.parserBuilder()` 를 사용하여 JWT를 파싱할 수 있다.
- JWT가 위변조되지 않았는지 secretKey(key)값을 넣어 확인한다.
-- JWT의 구조 중 **Payload** 부분에는 토큰에 담긴 정보가 들어있다.
- 여기에 담긴 정보의 한 ‘조각’ 을 클레임(**claim**) 이라고 부르고, 이는 key-value 의 한 쌍으로 이뤄져있습니다. 토큰에는 여러개의 클레임 들을 넣을 수 있다.
- `Jwts.parserBuilder()` 와 secretKey를 사용하여 JWT의 Claims를 가져와 담겨 있는 사용자의 정보를 사용한다.

### 전체 코드

#### 컨트롤러

```java
package com.sparta.springauth.auth;

import com.sparta.springauth.entity.UserRoleEnum;
import com.sparta.springauth.jwt.JwtUtil;
import io.jsonwebtoken.Claims;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import jakarta.servlet.http.HttpSession;
import org.springframework.web.bind.annotation.CookieValue;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;

@RestController
@RequestMapping("/api")
public class AuthController {

    public static final String AUTHORIZATION_HEADER = "Authorization";

    private final JwtUtil jwtUtil;

    public AuthController(JwtUtil jwtUtil) {
        this.jwtUtil = jwtUtil;
    }

    // JWT 생성
    @GetMapping("/create-jwt")
    public String createJwt(HttpServletResponse res) {
        // Jwt 생성
        String token = jwtUtil.createToken("Robbie", UserRoleEnum.USER);

        // Jwt 쿠키 저장
        jwtUtil.addJwtToCookie(token, res);

        return "createJwt : " + token;
    }

    @GetMapping("/get-jwt")
    public String getJwt(@CookieValue(JwtUtil.AUTHORIZATION_HEADER) String tokenValue) {
        // JWT 토큰 substring
        String token = jwtUtil.substringToken(tokenValue);

        // 토큰 검증
        if(!jwtUtil.validateToken(token)){
            throw new IllegalArgumentException("Token Error");
        }

        // 토큰에서 사용자 정보 가져오기
        Claims info = jwtUtil.getUserInfoFromToken(token);
        // 사용자 username
        String username = info.getSubject();
        System.out.println("username = " + username);
        // 사용자 권한
        String authority = (String) info.get(JwtUtil.AUTHORIZATION_KEY);
        System.out.println("authority = " + authority);

        return "getJwt : " + username + ", " + authority;
    }
}
```

#### jwtUtil

```java
package com.sparta.springauth.jwt;

// util이란? 특정한 매개변수, 파라미터 어떠한 작업을 수행하는 메서드가 존재하는 클래스
// 다른 객체에 의존하지않고 하나의 모듈로서 동작하는 클래스
import com.sparta.springauth.entity.UserRoleEnum;
import io.jsonwebtoken.*;
import io.jsonwebtoken.security.Keys;
import jakarta.annotation.PostConstruct;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletResponse;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import org.springframework.util.StringUtils;

import java.io.UnsupportedEncodingException;
import java.net.URLEncoder;
import java.security.Key;
import java.util.Base64;
import java.util.Date;

@Component
public class JwtUtil {
    // JWT 데이터

    // Header KEY 값
    public static final String AUTHORIZATION_HEADER = "Authorization";
    // 사용자 권한 값의 KEY
    public static final String AUTHORIZATION_KEY = "auth";
    // Token 식별자
    public static final String BEARER_PREFIX = "Bearer ";
    // 토큰 만료시간
    private final long TOKEN_TIME = 60 * 60 * 1000L; // 60분

    @Value("${jwt.secret.key}") // Base64 Encode 한 SecretKey
    private String secretKey;
    private Key key;
    private final SignatureAlgorithm signatureAlgorithm = SignatureAlgorithm.HS256;


    // 로그 설정 (로깅)
    // 로깅이란?
    // 어플리케이션 동작하는 동안 프로젝트 상태 또는 동작을 시간순으로 기록
    public static final Logger logger = LoggerFactory.getLogger("JWT 관련 로그");

    @PostConstruct
    public void init() {
        byte[] bytes = Base64.getDecoder().decode(secretKey);
        key = Keys.hmacShaKeyFor(bytes);
    }

    // JWT 생성
    public String createToken(String username, UserRoleEnum role) {
        Date date = new Date();

        return BEARER_PREFIX +
                Jwts.builder()
                        .setSubject(username) // 사용자 식별자값(ID)
                        .claim(AUTHORIZATION_KEY, role) // 사용자 권한
                        .setExpiration(new Date(date.getTime() + TOKEN_TIME)) // 만료 시간
                        .setIssuedAt(date) // 발급일
                        .signWith(key, signatureAlgorithm) // 암호화 알고리즘
                        .compact();
    }

    // 생성된 JWT를 Cookie에 저장
    public void addJwtToCookie(String token, HttpServletResponse res) {
        try {
            token = URLEncoder.encode(token, "utf-8").replaceAll("\\+", "%20"); // Cookie Value 에는 공백이 불가능해서 encoding 진행

            Cookie cookie = new Cookie(AUTHORIZATION_HEADER, token); // Name-Value
            cookie.setPath("/");

            // Response 객체에 Cookie 추가
            res.addCookie(cookie);
        } catch (UnsupportedEncodingException e) {
            logger.error(e.getMessage());
        }
    }

    // Cookie에 들어있던 JWT 토큰을 Substring
    public String substringToken(String tokenValue) {
        if (StringUtils.hasText(tokenValue) && tokenValue.startsWith(BEARER_PREFIX)) {
            return tokenValue.substring(7); // Bearer  공백까지 7글자
        }
        logger.error("Not Found Token");
        throw new NullPointerException("Not Found Token");
    }

    // JWT 검증
    public boolean validateToken(String token) {
        try {
            Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token);
            return true;
        } catch (SecurityException | MalformedJwtException | SignatureException e) {
            logger.error("Invalid JWT signature, 유효하지 않는 JWT 서명 입니다.");
        } catch (ExpiredJwtException e) {
            logger.error("Expired JWT token, 만료된 JWT token 입니다.");
        } catch (UnsupportedJwtException e) {
            logger.error("Unsupported JWT token, 지원되지 않는 JWT 토큰 입니다.");
        } catch (IllegalArgumentException e) {
            logger.error("JWT claims is empty, 잘못된 JWT 토큰 입니다.");
        }
        return false;
    }

    // JWT에서 사용자 정보 가져오기
    public Claims getUserInfoFromToken(String token) {
        return Jwts.parserBuilder().setSigningKey(key).build().parseClaimsJws(token).getBody();
    }

}
```

#### 권한 부여

```java
package com.sparta.springauth.entity;

public enum UserRoleEnum {
    USER(Authority.USER),  // 사용자 권한
    ADMIN(Authority.ADMIN);  // 관리자 권한

    private final String authority;

    UserRoleEnum(String authority) {
        this.authority = authority;
    }

    public String getAuthority() {
        return this.authority;
    }

    public static class Authority {
        public static final String USER = "ROLE_USER";
        public static final String ADMIN = "ROLE_ADMIN";
    }
}
```