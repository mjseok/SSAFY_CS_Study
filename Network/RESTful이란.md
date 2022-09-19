# REST
- `Representational State Transfer(대표적인 상태 전략)`
- 웹의 장점을 최대한 활용할 수 있는 아키텍처(표준이 아니라 설계 가이드일 뿐이다.)
    why? 웹의 기존 기술과 HTTP 프로토콜을 그대로 활용하기 때문  
- 웹에 존재하는 모든 자원(이미지, 동영상, DB자원,등)에 고유한 URI를 부여해서 자원을 주고받는 것
EX) 'DB의 학생 정보'라는 자원을 'students'로 표현할 수 있고 이의 상태를 주고 받는 것

## 구성요소
- 자원(resource) : URI
    자원을 구별하는 ID는 `/groups/:group_id`와 같은 HTTP URI이고 자원은 서버에 존재한다.  
    클라이언트는 이 URI를 이용해서 자원을 지정하고 자원의 상태에 대한 조작을 요청한다.
- 행위: Method
    HTTP 프로토콜의 Method를 사용  
    프로토콜 메서드는 `GET`,`POST`,`PUT`,`DELETE`가 있다.

- 표현(Representation)
    클라이언트가 서버로 요청을 보냈을 때 서버가 응답으로 보내주는 자원의 상태   
    REST에서 하나의 자원은 JSON,XML,TEXT,RSS등 여러 형태의 Representationmd로 나타낼 수 있다.  
    JSON,XML이 일반적임  
## 특징
1. server- client 구조
2. stateless
3. Cacheable
4. Layered System
5. 자체 표현 구조
6. Uniform Interface

### Server-Client 구조
```
REST서버: API 제공, 비즈니스 로직 처리 및 저장
클라이언트: 사용자 인증, 컨텍스트(세션,로그인 정보)등을 직접 관리
```
자원이 있는 쪽이 서버, 자원을 요청하는 쪽이 클라이언트인 구조로 역할이 확실하게 구분이 되기 때문에 클라이언트, 서버에서 개발해야 할 내용이 명확해지고 서로간의 의존성이 줄어든다.

### Stateless 무상태성
HTTP 프로토콜이 stateless 프로토콜이므로 REST 또한 무상태성을 가짐  
상태 정보를 따로 저장, 관리하지 않는다. 즉, 세션정보나 쿠키정보를 별도로 저장 및 관리하지 않기 때문에 API서버는 들어오는 요청만 처리하면 된다.  
-> 서비스의 자유도가 높아지고 구현이 단순해진다.  

### Cacheable 캐시가능
HTTP가 가진 [캐싱 기능](https://developer.mozilla.org/ko/docs/Web/HTTP/Caching)이 적용 가능하다.  
HTTP protocol 표준에서 사용하는 `Last-Modified 태그`나 `E-tag`를 이용하면 캐싱 구현이 가능  
대량의 요청을 효율적으로 처리하기 위해 캐시가 요구된다.  

### Layere System
API 서버로 순수 비즈니스 로직을 수행하고 그 앞단에 사용자 인증, 암호화 등을 하는 계층을 추가하는 등 유연성있는
구조로 구현할 수 있다.

### 자체 표현 구조
Method(동사) + URI(명사)로 이뤄져 있어서 API자체만 보고도 이해할 수 있다.

### Uniform Interface 인터페이스 일관성
URI로 지정한 자원에 대한 조작을 통일되고 한정적인 인터페이스로 수행  
HTTP 표준 프로토콜에 따르는 모든 플랫폼에서 사용이 가능하다.(특정 언어, 기술에 따라 사용이 안되거나 바뀌는 경우가 없다.)  

# REST API
## API
`Application Programmingn Interface`  
 요청과 응답을 사용하여 두 애플리케이션이 서로 통신하는 방법  
## REST API
REST를 기반으로 서비스 API를 구현한 것  
### REST API 설계 
1. URI는 정보의 자원을 표현해야 한다.  
- resource는 동사가 아닌 **명사**로, 대문자보다는 **소문자**를 사용  
2. 자원에 대한 행위는 HTTP Method로 표현  
* `GET` : 조회  
* `PUT` : 수정
* `DELETE` : 삭제
* `POST` : 생성
EX) 
```
GET /members/delete/1 보다는
DELETE /members/1이 맞는 표현  
```
### URI 규칙
- `/`는 계층 관계를 나타내는데 사용  
- 마지막 문자로 `/`를 사용하지 않는다.  
- `-`는 가독성을 높이는데 사용할 수 있다
- `_`는 URI에 사용하지 않는다
- URI 경로에는 소문자가 적합하다
- 파일 확장자는 URI에 포함되지 않는다. 


# RESTful
- `REST`라는 아키텍처를 구현하는 웹 서비스를 나타내기 위해 사용되는 용어
    즉, `REST API`를 제공하는 웹 서비스를 `RESTful`하다고 할 수 있습니다.  

- 목적: 이해하기 쉽고 사용하기 쉬운 REST API 만드는 것
- 근본 목적이 성능 향상이 아닌 api의 이해, 호환성을 높이는 것이므로, 성능이 중요한 상황에서는 굳이 RESTful한 API를 구현할 필요는 없다. 


### 참고
[[Network] REST란? REST API란? RESTful이란?](https://gmlwjd9405.github.io/2018/09/21/rest-and-restful.html)
[REST란](https://hckcksrl.medium.com/rest란-c602c3324196)
[REST API 제대로 알고 사용하기](https://meetup.toast.com/posts/92)