# CORS

Cross Origin Resource Sharing(교차 출처 리소스 공유)
개발자가 가져오는 리소스들이 안전한지 검사하는 관문

- 특정 도메인 간의 교차, Ajax요청은 동일-출처 보안 정책에 의해 금지된다.

## CORS가 발생하는 이유

HTTP 요청에 대해서 어떤 요청을 하느냐에 따라 각기 다른 특징을 갖고 있어서

### HTML

- 기본적으로 Cross-Origin 정책을 따름
- link 태그의 href에서 다른 origin의 css 등의 리소스에 접근하는 것이 가능
- img 태그의 src에서 다른 리소스에 접근하는 것이 가능

### XMLHttpRequest, Fetch API 등 script 태그 내

- 기본적으로 Smae-Origin 정책을 따름  
  Same-Origin정책 ? 자바스크립트는 서로 다른 도메인에 대한 요청을 보안상 제한함(브라우저 기본 설정은 하나의 서버 연결만 허용)

## 출처(Origin)

EX) `https://www.google.com/search?q=asdas&oq=asdas&aqs=chrome..69i57j69i59j69i60l4j69i65l2.1703j1j7&sourceid=chrome&ie=UTF-8`
라는 URL이 있으면, 이 URL은 Protocoal,Host,Port,Path,Query String, Fragment등으로 이뤄져있다.
여기서 출처란, Protocol,Host,Port를 모두 합친 것을 말한다.
즉, `https://google.com`이 부분이 출처다.

## Same Origin vs Cross Origin

웹에는 크게 SOP(Same Origin Policy) 와 CORS(Cross Origin Resource Sharing)이 있다.

### SOP

같은 출처에서만 리소스를 공유할 수 있다.
즉, 프로토콜이나 호스트, 포트가 다른 곳에서는 공유를 할 수 없다. Cross Origin이 된다.  
출처를 비교하는 로직은 서버에 구현된 스펙이 아니라 브라우저에 구현된 스펙이다.  
즉, 서버가 정상적으로 응답을 하더라도 브라우저 상에서 CORS정책에 위반되면 해당 응답은 무시된다.  
(Internet Explorer는 Origin 비교시 포트를 무시하기 때문에 보안이 취약하다.)

### 개발 중에 만날 수 있는 오류(예시)

프론트 개발을 React와 같은 라이브러리를 사용해서 개발할 때, 백엔드 서버와 별도의 프론트 서버가 존재하게 된다.  
프론트 개발 도중, 로컬의 백엔드 서버에 연동하거나 개발 서버에 연결해서 API연동을 할 때,
프론트 서버 : http://localhost:3000 백엔드 서버: http://localhost:8080 로 띄웠다면, 서버에서 응답이 넘어올 때 브라우저에서 CORS오류가 발생하게 된다.

### CORS

- HTTP header를 추가적으로 사용해서 다른 출처에 있는 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 것

### CORS 등장 배경

초창기 웹에서 다른 웹사이트로 request를 보낼 때 쿠키와 같이 전송되기 때문에 다른 웹 사이트에서도 쿠기 값을 확인할 수 있었다.  
또한, 출처가 다른 것끼리 요청을 주고받기 위해서 JSONP라는 방식을 사용했는데, 이는 html스크립트 상에서는 다른 도메인에 대한 요청을 허용하기 때문에 `<script>`를 이용해서 다른 도메인에서 데이터를 가져오는 방법입니다. 이는 우회적인 방법이고 이를 막으려고 하니 많은 웹사이트에서 쓰고 있던 방법이라서, 다른 공식적인 방법이 필요해서 만들게 되었다.

## CORS 동작

[개발자]

- 서버에서 Access-Control-Allow-Origin 세팅  
   `res.setHeader('Access-Control-Allow-origin', 'https://inpa.tistory.com');`  
  예시

```
Access-Control-Allow-Methods: POST, GET, OPTIONS
Access-Control-Allow-Headers:  Content-Type
Access-Control-Max-Age: 86400
```

- POST, GET, OPTIONS의 method가 가능
- Content-Type의 Header만 받을 수 있다.
- 86400초동안 다시 preflight를 날릴 필요가 없음

[CORS 동작원리]

### [preflight요청]

![image](https://user-images.githubusercontent.com/57527380/188455853-f44d2e23-3fb4-4991-b67c-bf85cb6b38fa.png)

1. browser -> server로 `preflight request`를 날린다.

- preflight request(예비요청): 본 요청을 보내기 전에 브라우저가 안전한 요청인지 확인하는 것(개발자가 허용한 출처인지 확인) `OPTIONS`라는 메서드로 보내짐

2. server -> browser 응답으로 header에 `Access-Control-Allow-Origin`를 담아서 보낸다.

- Access-Control-Allow-Origin에는 어떤 응답을 허용하고 금지하는지에 대한 정보가 담겨있다.

3. browser -> server 서버의 응답과 비교해서 안전한지 확인하고 본요청을 본낸다.
4. server -> browser 원하는 응답을 보내준다.

### [단순 요청]

![image](https://user-images.githubusercontent.com/57527380/188455922-231ea71e-f2f4-4c2d-896c-23e0825bd448.png)

1. preflight를 보내지 않고 바로 서버에 직행으로 본 요청을 보낸다.
2. 서버가 `Access-Control-Allow-Origin`를 보낸다.
3. 브라우저가 CORS 위반 여부 검사

#### 단순 요청이 일어나는 경우

1. 요청의 메소드는 `GET`, `HEAD`, `POST` 중 하나
2. 수동으로 설정할 수 있는 header를 [`CORS-safelisted request-header`로 정의한 헤더](https://fetch.spec.whatwg.org/#cors-safelisted-request-header)만 사용
   (예:Accept, Accept-Language, Content-Language, Content-Type, DPR, Downlink, Save-Data, Viewport-Width, Width)

- 여기서 Content-Type이여도 application/x-www-form-urlencoded, multipart/form-data, text/plain 값만 허용

### [인증정보 포함 요청]

credentials에 옵션을 설정해줄 때 사용하는 방법  
credentials 옵션에는

1. omit :절대로 cookie들을 전송하거나 받지 않는다
2. same-origin은 동일 출처라면 user credential(cookies, basic http auth등등)을 전송한다.
3. include는 cross-origin호출이여도 언제나 user credential을 전송한다.  
예시
fetchAPI

```js
fetch("주소", {
  credentials: "include",
});
```

Axios통신

```js
axios.post(주소, 데이터, { withCredentials: true });
```

## 참고

https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-CORS-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-%F0%9F%91%8F#CORS_(Cross_Origin_Resource_Sharing)  
https://ko.wikipedia.org/wiki/%EA%B5%90%EC%B0%A8_%EC%B6%9C%EC%B2%98_%EB%A6%AC%EC%86%8C%EC%8A%A4_%EA%B3%B5%EC%9C%A0  
https://ingg.dev/cors/
