# 쿠키, 세션, 토큰의 등장 이유

1. HTTP 통신은 서버가 클라이언트 요청에 응답을 완료하면 연결을 끊어버린다(Connectionless).
2. 또한 연결을 끊은 뒤에는 클라이언트의 상태를 저장하지 않는다(Stateless).  
   이러한 HTTP의 특징 때문에 로그인 등을 했을때 상태를 기억하기 위해 사용한다.

## Conection Oriented vs Connectionless

1. Connection Oriented: 상대와 고정된 connection을 확립한 뒤 통신하는 프로토콜

```
ex)
전화, TCP
전송되는 데이터 패킷은 순서대로 전달된다.
데이터가 모두 전송되고 나면 접속은 끊어진다.
```

2. 커넥션을 확립하지 않고 통신하는 프로토콜

```
ex)
편지, UDP
데이터 그램은 순서에 상관 없이 일단 전송한다.
```

## Stateful vs Stateless

1. stateful: server side에 client와 server의 동작, 상태 정보를 '저장'하는 형태의 통신.  
   이러한 세션 상태에 기반하여 server의 응답이 달라진다.

```
ex)
TCP는 3way handshaking을 통해 SYN_SEND 상태, SYN_RECIEVED, ESTABLISHED 상태를 갖는다.
세션의 상태를 server side에 저장하고 이러한 세션의 상태에 따라 서버의 응답이 달라지게 하는 것이
stateful이다.
```

2. stateless: 서버가 클라이언트의 상태를 보존하지 않는 것이다.  
   서버는 단순히 요청이 오면 응답을 보내는 역할만을 수행한다.

```
ex)
UDP는 연결 세션을 인증하는 절차를 거치지 않고 일단 데이터를 보낸다.
통신에 필요한 모든 상태 정보들을 client가 가지고 있다가 서버와 통신할때 데이터를 실어 보내는 것이
stateless이다.
```

# 쿠키

브라우저의 쿠키 저장소에 저장되는 정보. 사용자가 갖고 있는 정보라고 볼 수 있다.  
쿠키는 당사자 뿐 아니라 제3자도 조회가 가능하기 때문에 개인 정보를 담은 내용이나 보안상 민감한 정보를 저장하기 적합하지 않다.  
크게 중요하지 않은 웹페이지 내 설정이나 기록을 저장하는 역할을 주로 맡는다.

- 클라이언트가 서버에 데이터를 요청하면 서버는 쿠키를 생성해서 set-cookie에 담아 보낸다.

- 클라이언트가 재요청을 할 때 마다 저장된 쿠키의 요청 헤더에 cookie에 담아 보낸다.

- 서버는 쿠키에 담긴 정보를 바탕으로 해당 요청을 클라이언트를 식별할 수 있다.

# 세션

서버에 사용자의 식별자인 session id를 만들어 서버측에 저장된 정보로 사용자를 판별하는 것.  
세션 이전의 방식은 HTTP 요청의 stateless라는 특징 때문에 매번 아이디와 비밀번호를 인증해야 한다.  
그렇게 되면 보안에 문제가 생길 수 있는데 이러한 불편함을 해결하기 위해 서버는 세션이라는 증서를 만들고 사용자의 매 요청마다 전달된 세션과 서버에 저장된 세션을 비교해 같은 사용자인지를 판별한다.

- 세션 방식은 쿠키 값을 받았을 때 바로 회원을 판별할 수 있어 서버 자원에의 접근이 용이하다.

- 세션은 서버에 저장되기 때문에 메모리 부족과 데이터 부하를 일으킬 수 있다.

- 세션 하이재킹 공격을 당할 수 있다 -> HTTPS를 사용해 정보를 암호화 하거나 세션에 유효 시간을 부여할 수 있다.

# Session - Cookie 방식

session id를 쿠키를 통해 사용자에게 보낸다.
사용자는 쿠키에 저장된 session id를 이용하여 서버에 요청을 보낸다.  
서버는 쿠키에 저장된 session id를 받아 세션 정보에 접근 가능 여부를 판별한다.

# 토큰(JWT)

사용자가 로그인 요청 시 서버에서 데이터를 기반으로 인증이 가능한 토큰을 만들어 클라이언트에 전달하는 방식이다.  클라이언트는 요청 시 토큰을 서버로 보내고 서버는 토큰의 유효성을 검사하여 사용자의 인증 여부를 판별한다.

- 메모리를 많이 차지하는 세션 방식의 대안으로 쓰인다.

- 사용자에 대한 인증 방식의 확장이 가능한다 (Oauth)

- 사용자 정보를 메모리로 관리하지 않기 때문에 scale-out(서버를 늘림)을 자유롭게 할 수 있다.

- 토큰을 발급하면 토큰의 만료까지 탈취된 토큰의 유효성이 지속된다(클라이언트에서 관리에 주의).

- 토큰 자체에 정보를 담고 있기 때문에 보안상 문제가 될 수도 있다.

## 토큰의 생성 방식

토큰의 생성에는 비대칭 암호화 방식을 사용한다.

### 비대칭 암호

타인에게 노출되어서는 안되는 개인키(Private Key)와 공개적으로 개방되어 있는 공개키(Public Key)가 쌍을 이룬 형태이다.

## 토큰의 구조

jwt는 .을 기준으로 Header, Payload, Signature로 나뉜다.  
전자서명은 비대칭 암호화 알고리즘을 사용하기 때문에 암호화를 위한 키와 복호화를 위한 키가 다르다.  
암호화(서명)에는 개인키를, 복호화(검증)에는 공개키를 사용한다.

### Header

jwt의 검증(verify)에 필요한 토큰 유형과 서명 알고리즘을 정의한다.

```
Header
{
    "alg" : "HS256"
    "typ" : "JWT"
}
```

alg: 서명 암호화 알고리즘 .
typ: 토큰 유형.

### Payload

사용자의 데이터나 권한이 key - value 형태로 담겨있다.  
(key-value 형태로 이루어진 한 쌍의 정보 = Claim)

```
Payload
{
    "sub": "123"
    "name": "MyName"
    "iat": "15623423"
}
```

1. Registered claim: 미리 정의된 클레임

```
iss(issuer): 발행자
exp(expiration time): 만료 시간
sub(subject): 제목
iat(issued At): 발행 시간
jti: JWT ID
```

2. Public claim: 사용자가 정의할 수 있는 클레임 공개용 정보 전달을 위해 사용된다.

3. Private claim: 당사자들간 정보를 공유하기 위해 만들어진 사용자 지정 클레임. 해당 유저를 특정할 수 있는 정보들을 담는다.

### Signature

헤더와 문자열을 서명한 값.  
서명은 헤더의 alg에 정의된 알고리즘으로 (헤더 + 페이로드) + 서버의 secret key를 이용해 생성하고, Base64 URL-safe로 인코딩 한다.

```
Signature = Base64Url(Header) + . + Base64Url(Payload) + server's secret key
```

## 토큰의 보안 전략

### 짧은 만료 기한을 설정

토큰의 유효기간을 짧게 설정하여 보안성을 높일 수 있지만 사용자에게 다시 로그인을 하게 만드는 단점이 있다 (보통 사용하지 않음).

### Sliding Session (Refresh Token 사용)

사용자가 로그인 정보를 다시 입력하지 않고 현재 가지고 있는 토큰을 새로운 토큰으로 발급하는 방식.  
refresh token은 access token과 마찬가지로 JWT를 사용할 수 있고 액세스 토큰에 비해 만료 시간이 길다.

1. 처음 사용자가 로그인 할 때 액세스 토큰과 리프레시 토큰을 발급한다.

2. 클라이언트는 엑세스 토큰 만료로 에러가 발생한 경우 리프레시 토큰을 이용하여 새로운 액세스 토큰을 발급 해달라고 요청한다.

3. 리프레시 토큰 만료로 다시 리프레시 토큰을 발급받으려 하는 경우에도 가장 최긍네 발급한 리프레시 토큰으로 새로운 토큰을 발급 받는다.

4. 리프레시 토큰은 보통 서버의 DB에 저장해주고 요청에 포함된 리프레시 토큰과 비교한다.

---

Reference:  
[쿠키, 세션, 토큰의 차이점](https://velog.io/@jung5318/%EC%BF%A0%ED%82%A4-%EC%84%B8%EC%85%98-%ED%86%A0%ED%81%B0%EC%9D%98-%EC%B0%A8%EC%9D%B4%EC%A0%90)  
[Cookie, Session, Token의 차이점](https://tofusand-dev.tistory.com/89)  
[Stateful/Stateless](<https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-Stateful-Stateless-%EC%A0%95%EB%A6%AC#Stateless_(%EB%AC%B4%EC%83%81%ED%83%9C)>)  
[JWT 토큰 인증이란?](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-JWTjson-web-token-%EB%9E%80-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC#JWT_%EA%B5%AC%EC%A1%B0)  
NestJS로 배우는 백엔드 프로그래밍