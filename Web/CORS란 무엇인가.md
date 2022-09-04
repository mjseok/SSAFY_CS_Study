# CORS

Cross Origin Resource Sharing
개발자가 가져오는 리소스들이 안전한지 검사하는 관문

## CORS가 발생하는 이유

HTTP 요청에 대해서 어떤 요청을 하느냐에 따라 각기 다른 특징을 갖고 있어서

### HTML

- 기본적으로 Cross-Origin 정책을 따름
- link 태그의 href에서 다른 origin의 css 등의 리소스에 접근하는 것이 가능
- img 태그의 src에서 다른 리소스에 접근하는 것이 가능

### XMLHttpRequest, Fetch API 등 script 태그 내

- 기본적으로 Smae-Origin 정책을 따름
- 자바스크립트는 서로 다른 도메인에 대한 요청을 보안상 제한함(브라우저 기본 설정은 하나의 서버 연결만 허용)
- Same- Origin-Policy
