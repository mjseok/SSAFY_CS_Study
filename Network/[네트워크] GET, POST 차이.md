# [네트워크] GET, POST 차이

GET, POST의 차이점에 대해 설명하세요.

> 조회는 GET, 등록은 POST, 수정은 PUT, 삭제는 DELETE를 이용한다.
> 

## GET

GET method는 클라이언트에서 서버로 어떠한 리소스로부터 정보를 요청하기 위해 사용되는 메서드입니다.

- 게시글을 읽는다.
- 즉, 데이터를 **읽거나(Read), 검색(Retrieve)** 할 때 사용되는 method
- GET요청은 오로지 데이터를 읽을 때만 사용되고 수정할 때는 사용하지 않습니다.
- 데이터 변형의 위험없이 사용할 수 있습니다.

- GET은 요청을 전송할 때 URL 주소 끝에 파라미터로 포함되어 전송되며, 이 부분을 **쿼리 스트링(QueryString)**이라고 부릅니다.

e.g.) **`www.example-url.com/resources?name1=정은영&name2=정아영`**

쿼리스트링을 포함한 URL입니다. 파라미터인 name1을 통해 값을 전달받을 수 있습니다. 요청 파라미터가 여러개이면 &로 연결합니다.

- 쿼리스트링을 사용하게 되면 URL에 조회 조건을 표시하기 때문에 특정 페이지를 링크하거나 북마크할 수 있습니다.
- 파라미터에 내용이 노출되기 때문에 민감한 데이터를 다룰 때 GET요청을 사용하면 안됩니다.
- GET 요청은 길이 제한이 있습니다.
    - 길이 제한 표준이 따로 있는 것은 아니고 브라우저마다 제한이 다릅니다.
- GET은 불필요한 요청을 제한하기 위해 요청이 캐시될 수 있습니다.
    - js, css, 이미지 같은 정적 컨텐츠는 데이터양이 크고, 변경될 일이 적어서 반복해서 동일한 요청을 보낼 필요가 없습니다. 정적 컨텐츠를 요청하고 나면 브라우저에서는 요청을 캐시해두고, 동일한 요청이 발생할 때 서버로 요청을 보내지 않고 캐시된 데이터를 사용합니다.
    - (그래서 프론트엔드 개발을 하다보면 정적 컨텐츠가 캐시돼 컨텐츠 내용을 변경해도 내용이 바뀌지 않는 경우가 종종 발생합니다. 이 때 브라우저의 캐시를 지워주면 다시 컨텐츠를 조회하기 위해 서버로 요청을 보내게 됩니다. )
    

## Post

POST method는 리소스를 **생성** 하기 위해서 서버에 데이터를 보내는 데 사용됩니다.

- 게시글을 쓰면 서버에 게시글이 저장된다.
- GET과 달리 전송해야 될 데이터를 HTTP 메세지의 Body에 담아서 전송합니다. Body의 타입은 Content-Type 헤더에 따라 결정됩니다.
    - Content-Type의 종류로는 **application/x-www-form-urlencoded,text/plain, multipart/form-data** 등이 있습니다.
    - e.g.)
    
    ```bash
    HEADER 영역
    
    Content-Type:application/json; charset=UTF-8
    
    ....
    
    BODY 영역
    
    {
    	"name1":"정은영",
    	"name2":"정아영"
    }
    ```
    

- HTTP 메세지의 Body는 길이의 제한없이 데이터를 전송할 수 있습니다.
    - 따라서 POST 요청은 GET과 달리 대용량 데이터를 전송할 수 있습니다.
- POST는 데이터가 Body로 전송되고, 내용이 눈에 보이지 않아 GET보다 보안적인 면에서 안전하다고 생각할 수 있지만, POST 요청도 크롬의 개발자 도구, Fiddler와 같은 툴로 요청 내용을 확인할 수 있기 때문에 민감한 데이터의 경우에는 반드시 암호화해서 전송해야 합니다.
- POST는 생성, 수정, 삭제에 사용할 수 있지만, 생성에는 POST, 수정은 PUT 또는 PATCH, 삭제는 DELETE가 더 용도에 맞는 메소드입니다.

## GET 방식과 POST 방식의 차이

![1](https://user-images.githubusercontent.com/48662662/191043242-951404c5-abff-467d-8f59-ef223186b771.png)


- GET방식은 idempotent, POST방식은 non-idempotent하게 설계 되었습니다.
    - idempotent(멱등성)이란 수학적으로 연산을 여러번 적용하더라도 결과값이 바뀌지 않는다는 성질입니다.
    - 1번을 실행하든 100번을 실행하든 결과가 같은 성질을 이야기합니다.
    - GET은 설계원칙에 따라 서버의 데이터나 상태를 변경시키지 않아야 idempotent합니다.
- 따라서 **GET**은 서버에게 동일한 요청을 여러번 전송하더라도 조회만 하기 때문에 동일한 응답이 돌아옵니다.
- **POST메소드**는 리소스를 새로 생성하거나 업데이트하기 때문에 서버에게 동일한 요청을 여러번 전송하더라도 응답이 다를 수 있습니다.
    - GET은 DB로 따지면 select입니다. select는 데이터를 변경하지 않고 가져오기 때문에 idempotent 성질을 가지고 있기 때문입니다.
    - POST는 DB로 따지면 Create입니다.
    
- GET 방식은 POST방식보다 빠릅니다.
    - GET방식은 캐싱이 가능하고 POST방식은 캐싱이 불가능하기 때문입니다.
    - 캐싱으로 인해 한 번 접근했던 정보에 대해 다시 접근할 때 빠르게 가져올 수 있기 때문에 POST방식보다 빠릅니다.

## REFERENCE

- [https://khj93.tistory.com/entry/GET-방식과-POST-방식-이란-차이점](https://khj93.tistory.com/entry/GET-%EB%B0%A9%EC%8B%9D%EA%B3%BC-POST-%EB%B0%A9%EC%8B%9D-%EC%9D%B4%EB%9E%80-%EC%B0%A8%EC%9D%B4%EC%A0%90)
- [https://velog.io/@songyouhyun/Get과-Post의-차이를-아시나요](https://velog.io/@songyouhyun/Get%EA%B3%BC-Post%EC%9D%98-%EC%B0%A8%EC%9D%B4%EB%A5%BC-%EC%95%84%EC%8B%9C%EB%82%98%EC%9A%94)
- [https://noahlogs.tistory.com/35](https://noahlogs.tistory.com/35)
- [https://hongsii.github.io/2017/08/02/what-is-the-difference-get-and-post/](https://hongsii.github.io/2017/08/02/what-is-the-difference-get-and-post/)
