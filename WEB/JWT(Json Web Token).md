# JWT(Json Web Token)

## JWT(Json Web Token)이란?

JWT는 JSON 객체를 사용하여 자가 수용적인 방식으로 정보를 안정성 있게 전달해 주기 위한 토큰입니다.

</br >

## JWT 구조

![img](https://blog.kakaocdn.net/dn/b0msBg/btq8NdjRqck/gsjfFAsh7SMJDrk6m2N4X0/img.png)

Header: 정보를 암호화할 방식(alg), 토큰의 타입(type)이 들어갑니다. (agl:HS256, type:jwt)

Payload : 서버에 보내질 데이터. 유저 아이디, 유효기간 등이 들어갑니다.

Signature : Base64로 인코딩한 Header와 Payload를 의미합니다. SECRET KEY를 포함하여 암호화되어 있습니다.

### 예제

~~~java
{ // headerJSON
  "alg": "HS256", // algorithm used for signature
  "typ": "JWT" // type of token
}
{ // payloadJSON
  "sub": "12345", // reserved keyword (subject)
  "exp": 300, // reserved keyword (expiration time)
  "name": "jwt payload", // custom keyword
  "canWinOscar": true // custom keyword
}
~~~

위 데이터를 JWT로 전환합니다.

~~~java
header = base64_encode(headerJSON)
payload = base64_encode(payloadJSON);
signature = base64_encode(HMAC_SHA256(header + "." + payload, secret))
jwt = header + "." + payload + "." + signature
~~~

</br >

## 장점

- JWT는 사용자 인증에 필요한 모든 정보를 토큰 자체에 포함하기 때문에 별도의 인증 저장소가 필요 없습니다.
  - 별도의 저장소가 필요 없어 Stateless한 서버를 만드는데 유리합니다.
- 토큰 기반으로 하는 다른 인증 시스템에 접근이 가능합니다.
  - 예를 들어 Facebook 로그인, Google 로그인 등은 모두 토큰을 기반으로 인증을 합니다. 이에 선택적으로 이름이나 이메일 등을 받을 수 있는 권한도 받을 수 있습니다. 

</br >

## 단점

- 이미 발급된 JWT에 대해서는 돌이킬 수 없습니다.
  - 세션/쿠키의 경우 만일 쿠키가 악의적으로 이용된다면, 해당하는 세션을 지워버리면 됩니다. 하지만 **JWT는 한 번 발급되면 유효기간이 완료될 때 까지는 계속 사용이 가능합니다.** 따라서 악의적인 사용자는 유효기간이 지나기 전까지 정보 탈취가 가능합니다.

해결책으로 기존의 Access Token의 유효기간을 짧게 하고 Refresh Token이라는 새로운 토큰을 발급합니다. 그렇게 되면 Access Token을 탈취당해도 상대적으로 피해를 줄일 수 있습니다. 

