---
title: "[Cookie] Spring - React 쿠키가 저장되지 않는 문제 해결"
date: 2023-11-25 23:01:00 +09:00
categories: [IT, TroubleShooting]
tags: [ til, troubleshooting]
image: /assets/img/posts/trouble-shooting.png
---


## 문제

저번에 CORS에러를 해결했더니 또 바로 새로운 문제가 등장했다.    
쿠키를 유저 인증 및 인가에 사용해야 브라우저에 저장해야 했는데, 쿠키가 저장되지 않던 문제이다.    

<br/>

엄청나게 간단한 코드로 해결할 수 있었다.


## 쿠키 저장 해결

브라우저의 개발자 도구 애플리케이션에 쿠키가 저장되지 않고있다. 이를 해결한 코드는 다음과 같다.

```javascript
const handleSubmit = async (e) => {
    e.preventDefault();

    try {
      const response = await axios.post(
        'http://localhost:8080/signin',
        {
          email: email,
          password: password,
        },
        {
          withCredentials: true, // 추가해줘야 애플리케이션 쿠키에 저장된다.
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

`withCredentials: true`는 XMLHttpRequest 객체의 withCredentials 속성을 설정하는 옵션이며, 이 설정은 브라우저 간의 쿠키 전송을 허용하도록 하는데 사용된다.

<br/>

일반적으로 서로 다른 도메인 간의 요청에서는 withCredentials를 사용하는 것이 보안상의 이유로 피해야 하지만, 특별한 상황에서 필요한 경우에 사용된다. 이 설정은 서버 측에서도 쿠키를 전송할 것인지 받을 것인지에 대한 설정이 필요하다.

#### 마치며
코드를 한줄 추가해서 에러를 해결했는데, 트러블 슈팅을 작성하며 에러를 재현했더니 한줄을 작성하지 않아도 잘 되었다. 이 부분에서 에러가 왜 났는지 알 수 없지만, 혹시 다음번에도 같은 에러를 마주한다면 이 코드를 작성해서 해결하기 위해 기록해 두자.


