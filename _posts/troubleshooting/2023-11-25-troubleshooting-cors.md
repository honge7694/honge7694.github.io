---
title: "[CORS] 항해99 일지(28) - Spring - React CORS 문제 해결"
date: 2023-11-25 23:00:00 +09:00
categories: [IT, 항해99, TroubleShooting]
tags: [TIL, 항해99, TroubleShooting]
image: /assets/img/posts/trouble-shooting.png
---


#### 들어가며

프로젝트를 진행하며 React에서 로그인을 하여 Spring에게 jwt로 만들어진 쿠키를 가져오는 로직을 작성하였지만 CORS 에러가 발생하였다.

<br/>

CORS를 해결했던 방법과 브라우저에 쿠키를 저장하는 트러블 슈팅을 작성해보자.


## CORS 해결

```javascript
const handleSubmit = async (e) => {
    e.preventDefault();

    try {
      const response = await axios.post(
        'http://localhost:8080/signin',
        {
          email: email,
          password: password,
        }
      );

      console.log(response.data);
      history('/');
    } catch (error) {
      setError(error.response.data.message);
      console.log(error);
    }
};
```

리액트 코드에서 위와 같이 로그인을 하여 쿠키를 얻어오는 도중 CORS에러를 만났다.    
> Access to XMLHttpRequest at 'http://localhost:8080/signin' from origin 'http://localhost:3000' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource

<br/>

해결법은 생각보다 간단했다.    
Spring의 WebMvcConfigurer를 상속받아서 CORS를 해결할 수 있었다.    


```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Value("${feUrl}")
    public String url;
	
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**")

            .allowedOrigins(url)
            .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
            .allowedHeaders("Authorization", "Content-Type", "X-Requested-With")
            .allowCredentials(true);
    }
}
```

이렇게 작성해주면 되며, `application.yml`에서 feUrl을 작성해서 `@Value` 어노테이션을 이용해 프론트엔드의 주소를 가져와서 사용한다.    
`allowMethods`는 허용할 HTTP 메서드를 지정하는 것이며,    
`allowedHeaders`는 요청 헤더 중에서 허용할 것들을 지정하는 것이다. Authorization으로 온  헤더를 가져와 사용한다는 뜻이다.    
`allowCrendentials`는 인증 정보를 포함할 수 있도록 설정한다. 쿠키를 주고받을 때 필요하다.

<br/>

#### 마치며

생각보다 간단했다. CORS 해결을 하나의 클래스만 작성해서 끝낼 수 있다니... 하지만 이 외에도 다른 에러가 나는게 문제였다. 항상 FE - BE 연결이 좀 복잡하다.



