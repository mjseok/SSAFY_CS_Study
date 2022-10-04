_[REST](https://github.com/mjseok/SSAFY_CS_Study/blob/main/Network/RESTful%EC%9D%B4%EB%9E%80.md)에 대해 발표를 한 후 질문을 받았는데, 답을 하려고보니 내가 `WAS`, `stateless`,`stateful`에 대해 제대로 이해하지 못한 것 같아서 이를 정리해봤다._

# ❓ Question

- 일반적인(예를 들면 회원 관리하는) 웹 서비스 만드는데 REST 서버를 사용하고 싶다면 반드시 state를 관리할 수 있는 WAS도 같이 써야 하는지?

# Stateless vs Stateful

상태가 있다, 없다
= 사용자나 클라이언트의 컨텍스트를 서버 쪽에서 유지한다, 안한다
= HTTP세션과 같은 컨텍스트 저장소에 상태 정보를 저장한다,안한다

## Stateful

```
Server와 Client끼리 세션의 State(상태)에 기반하여 Client에 Response를 보낸다.
이를 위해서 세션 상태를 포함한 Client와의 세션 정보를 Server에 저장한다.
이것이 Stateful하다는 것이다.
```

**Stateful특성**

1. 세션 정보를 Server에 저장한다.
2. 세션 State에 따라서 응답이 달라진다.

**Stateful예시**

- Stateful한 구조를 가진 프로토콜은 TCP, SMTP 등등이 있다.

### TCP

그림 예시) TCP
![image](https://user-images.githubusercontent.com/57527380/193814532-7fdbc651-3d5b-415e-b838-c0cf0974edc8.png)
-> 그림에 보이는 `SYN_SENT`, `LISTEN`, `ESTABLISHED` 등등이 여기서 말하는 **'상태'** 이다.

TCP는 Connection(3-handShake)을 맺고 서버가 클라이언트에 대한 정보와 상태를 유지시키고 있기 때문에 Stateful이라고 함.

client로부터 받은 패킷헤더를 파싱해서 앞으로 받을 데이터의 크기, sequence번호 등을 저장하는데 이 받은 크기의 데이터를 다 받을 때까지 세션을 유지하고 크기, sequence번호 등의 세션정보를 서버에 저장한다.

### SMTP

SMTP -> 메일 서버로 메일 보낼 때 상태를 가지고 송신자 주소, 수신자 주소, 메일 데이터 전송, 종료 등을 전송 종료 명령이 보내질 때까지 계속 유지됨

## Stateless

```
server의 응답이 client와의 세션 상태와 독립적이고 server는 단순히 요청이 오면 응답을 보내는 역할만 수행하고 서버에 저장되는 상태값이 없다.
```

**Stateless특성**

1. 세션 정보를 Server에 저장하지 않는다.
2. 세션 State와 응답은 독립적이다.

**Stateless예시**

- Stateless 구조를 가진 프로토콜은 UDP, HTTP 등등이 있다.

### HTTP

![image](https://user-images.githubusercontent.com/57527380/193772744-1a1b9a9a-38ba-421d-8982-11b5c3a9d34c.png)  
HTTP는 통신 시 정보를 한 번씩 주고받은 후 바로 끊는 형태로 처리된다.  
 이렇게 상태 정보를 저장하지 않는 통신형태를 stateless라고 하며, HTTTP는 대표적인 stateless 프로토콜입니다.

그렇기 때문에 웹 서비스에서는 상태 정보 유지를 위한 **별도의 처리**가 필요하다.

서버가 클라이언트를 식별할 수 없을 때, 식별하기 위한
**별도의 처리**

- Cookie, Session 방식
- OAuth, JWT와 같은 토큰 기반 인증 방식  
  토큰 기반 인증방식은 암호화를 해주기 때문에 보안적인 부분에서 쿠키,세션보다 더 뛰어남.
- 외부 DB에 저장  
  상태를 유지하면서 연속된 응답을 해야 할 때 쿠키라는 기술을 사용함  
  즉, http는 stateless이고 바로바로 연결을 끊기 때문에 여러 단계의 흐름을 처리 할 때 각 요청이 동일한 사용자가 보낸 것인지 다른 사용자가 보낸 것인지 판단하지 못한다.이런 경우 여러 건의 요청 처리를 동일한 사용자 접속 세션으로 인식할 수 있도록 cookie를 사용한다.

## Stateful VS Stateless

### Stateful

![image](https://user-images.githubusercontent.com/57527380/193823596-f694b416-3097-436c-aff5-62ede42cad0b.png)
만약에, server1에 클라이언트의 세션정보가 저장되면 server2에는 해당 정보가 없으므로 세션인증을 처음부터 다시 해줘야하기 때문에 clientA의 요청은 server1으로 연결되도록 관리해줘야 한다.

### Stateless

![image](https://user-images.githubusercontent.com/57527380/193823645-68373de8-07cb-4f1d-a211-88d5e494aca0.png)
clientA의 요청이 sever1, server2 어디에 연결되더라도 DB를 통해 정보에 접근이 가능하다.

즉, Stateless한 구조를 따르면, Scailing이 자유롭다는 장점이 있다.  
트래픽 급증으로 clientA가 server2로 연결된 경우 세션 정보를 다시 옮겨줘야하지만 stateless는 이런 걱정이 필요없습니다.

# WAS

![image](https://user-images.githubusercontent.com/57527380/193819875-a494f5a5-dd67-4371-abc6-b7c045d41764.png)

웹 서버와 웹 컨네이너를 합친 것으로, **동적 컨텐츠**를 제공하기 위해서 만들어진 Application서버입니다.  
 HTTP프로토콜을 기반으로 어플리케이션을 수행할 수 있도록 해주는 미들웨어이고 데이터베이스 서버와 같이 수행된다.  
-> DB접속 기능 제공, 여러 트랜잭션 및 비즈니스 로직 수행

# ❗ Answer

1. 정적 컨텐츠냐 동적 컨텐츠냐 -> WAS쓰냐 마냐
2. 여러 요청에 대해 사용자 식별이 필요하냐 마냐 -> 따로 처리 해주냐 마냐
3. REST API -> HTTP기반 -> stateless -> 연결 상태를 저장하지 않는다.

즉, 정적 컨텐츠만 제공하는 서버일 경우에는 WAS없이 REST API 사용가능  
REST와는 별개로 HTTP 동적 컨텐츠를 요청하면 WAS로 넘겨서 처리받고 이 결과를 WAS에게 받아서 클라이언트로 넘겨주는 것이고, 동적 컨텐츠를 사용하냐마냐와는 상관없이 여러 건의 요청을 동일한 사용자가 보내는 것인지 아닌지를 확인하기 위해서는 쿠키,세션이 필요하다

- 그러나, 일반적으로(통상적으로) API 서버가 API형태의 WAS를 구현하는 것을 말하고 RESTful API도 API(=Application Programming Interface)이기 때문에, Restful한 API를 제공하는 것은 WAS를 개발하는 것과 같다고 볼 수 있다.

## 참고

[REST의 이해](https://yangbongsoo.gitbook.io/study/undefined-1/rest)  
[Stateless vs Stateful](https://5equal0.tistory.com/entry/StatefulStateless-Stateful-vs-Stateless-%EC%84%9C%EB%B9%84%EC%8A%A4%EC%99%80-HTTP-%EB%B0%8F-REST)  
[WebServer와 WAS](https://code-lab1.tistory.com/199)  
[REST API와 WAS](https://okky.kr/articles/965447)
