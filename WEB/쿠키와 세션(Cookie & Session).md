# 쿠키와 세션(Cookie & Session)

## 쿠키와 세션을 사용하는 이유

쿠키와 세션을 사용하는 이유를 알아보기 전에 HTTP 프로토콜의 특징을 알아보겠습니다.

### HTTP 프로토콜의 특징(connectionless, stateless)

### 비연결지향(Connectionless)

- 비연결지향은 클라이언트가 요청(req)을 한 후 응답(resp)을 받으면 그 연결을 끊어 버리는 특징입니다.

- HTTP는 먼저 클라이언트가 request를 서버에 보내면, 서버는 클라이언트에게 요청에 맞는 response를 보내고 접속을 끊는 특성이 있습니다.
- 헤더에 keep-alive라는 값을 줘서 커넥션을 재활용하는데 HTTP1.1에서는 디폴트 값입니다.
- HTTP가 TCP(연결지향)위에서 구현되었기 떄문에 네트워크 관점에서 keep-alive 옵션으로 connectionless의 연결비용을 줄이는 것을 장점으로 비연결지향이라 합니다.

### 무상태(Stateless)

- 무상태는 연결을 끊는 순간 클라이언트와 서버의 통신이 끝나며 상태 정보는 유지하지 않는 특징입니다.

HTTP의 이 두가지 특성을 보완하기 위해 쿠키와 세션을 사용합니다.

Connectionless라는 특성 덕분에 계속해서 통신 연결을 유지하지 않기 때문에 리소스 낭비가 줄어드는 것은 매우 큰 장점이지만
틍신할 때마다 새로운 Connection을 열기 때문에 클라이언트는 내가 누구인지 인증(로그인) 해야하는 단점이 있습니다.

쿠키와 세션을 이용해서 단점을 보완할 수 있습니다.

</br >

## 쿠키(Cookie)

### 쿠키란?

- 쿠키는 **클라이언트(브라우저) 로컬에 저장**되는 키와 값이 들어있는 데이터 파일입니다.
- 사용자 인증이 유효한 시간을 명시할 수 있으며, 유효 시간이 정해지면 브라우저가 종료되어도 인증이 유지된다는 특징이 있습니다.
- Response Header에 Set-Cookie 속성을 사용하여 클라이언트에 쿠키를 만들 수 있습니다.
- 쿠키는 사용자가 따로 요청하지 않아도 브라우저가 Request시에 Request Header를 넣어서 자동으로 서버에 전송합니다.

</br >

### 쿠키 동작 방식

1. 클라이언트가 페이지를 요청합니다.
2. 서버에서 쿠키를 생성합니다.
3. 서버는 HTTP Header에 쿠키를 포함 시켜 응답합니다.
4. 브라우저가 종료되어도 쿠키 만료 기간이 있다면 클라이언트에서 보관하고 있습니다.
5. 클라이언트는 쿠키를 저장하고, 쿠키를 담에 HTTP 재요청을 합니다.

서버에서 쿠키를 읽어 이전 상태 정보를 변경할 필요가 있을 때 쿠키를 업데이트하여 변경된 쿠키를 HTTP Header에 포함시켜 응답합니다.

</br >

### 쿠키 사용 예

- 방문 사이트에서 로그인 시, "아이디와 비밀번호를 저장하시겠습니까?"
- 쇼핑몰의 장바구니 기능
- 자동로그인, 팝업 체크, 쇼핑몰 장바구니 등

</br >

## 세션(Session)

### 세션이란?

- 세션은 쿠키를 기반으로 한다. 하지만 **사용자 정보 파일을 브라우저가 아닌 서버 측에서 관리합니다.**
- 서버에서는 클라이언트를 구분하기 위해 세션 ID를 부여하며 웹 브라우저가 서버에 접속해서 브라우저를 종료할 때까지 인증상태를 유지합니다.
- 접속 시간에 제한을 두어 일정 시간 응답이 없다면 정보가 유지되지 않게 설정이 가능합니다.
- **사용자에 대한 정보를 서버에 두기 때문에 쿠키보다 보안에 좋지만, 사용자가 많아질수록 서버 메모리를 많이 차지하게 되는 단점이 있습니다.**
  - 즉, 동접자 수가 많은 웹 사이트인 경우 서버에 과부하를 주게 되므로 성능 저하의 요인이 됩니다.

</br >

### 세션 동작 방식

1. 클라이언트는 세션ID가 없는 상태에서 HTTP를 요청합니다.
2. 서버는 세션 ID를 생성한 후, 세션 쿠키를 담아 응답합니다.
3. 클라이언트는 세션 쿠키를 저장한후, 저장된 세션 쿠키를 담아 HTTP를 재요청합니다.
4. 서버는 클라이언트 인증을 한후 응답합니다.

</br >

### 세션의 특징

- 각 클라이언트에게 고유 세션ID를 부여합니다.
- 보안 면에서 쿠키보다 우수합니다.
- **사용자가 많아질수록 서버 메모리를 많이 차지합니다.(성능 저하)**
  - 인증 정보를 서버에 저장하기 때문에

</br >

### 쿠키와 세션 차이점

- 세션은 정보를 서버에 저장하고, 쿠키는 정보를 클라이언트에 저장합니다.
- 쿠키는 정보를 클라이언트에 저장하기 때문에 보안에 취약합니다.

