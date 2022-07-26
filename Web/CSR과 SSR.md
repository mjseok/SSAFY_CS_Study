# CSR과 SSR

## **CSR (Client Side Rendering)**

- **정의**
    - 사용자의 요청에 따라 서버 응답을 받아 클라이언트 쪽에서 렌더링 하는 방식
- **과정**
    1. 유저가 Website 요청을 보낸다.(URL를 입력하여 사이트에 접근한다)
    2. CDN이 HTML, JS 파일로 접근할  수 있는 링크를 클라이언트로 보낸다.
    3. 클라이언트는 HTML과 JS를 다운 받는다.(화면에 아무것도 안 보인다)
    4. 다운이 완료된 JS가 실행된다. 이때 API가 호출되어 서버로 데이터를 요청한다. (화면에 구조가 형성된다)
    5. 서버가 API 요청에 응답하여 데이터를 보낸다.
    6. 받아온 데이터를 화면에 표시한다. (화면에 콘텐츠가 보인다)
- **장단점**
    - **장점**
        - 초기화면 렌더링을 제외하면 속도가 빠르다.
        - 필요한 부분만 요청하고 응답하기 때문에 서버 부하가 적다.
        - 화면 깜박임이 없어 사용자 친화적이다.
    - **단점**
        - JS를 사용하여 유저와 상호작용 후 페이지에 콘텐츠가 로드되기 때문에 웹 크롤러가 페이지를 색인화 하려고 하면 빈 페이지처럼 보이게 되어 SEO에 불리하다.
        - 모든 JS 파일을 다운 받아야 하기 때문에 초기 화면 렌더링 속도가 느리다.

#
## **SSR (Server Side Rendering)**

- **정의**
    - 서버에서 렌더링이 일어나 완전하게 만들어진 html를 받아와 페이지 전체를 렌더링 하는 방식
- **과정**
    1. 유저가 Website 요청을 보낸다.(URL를 입력하여 사이트에 접근한다)
    2. 서버는 즉시 렌더링 가능한 완전한 HTML 파일을 생성한다.
    3. 클라이언트에서 전달 받아 HTML을 렌더링 한다.(화면에 콘텐츠가 보인다)
    4. 클라이언트가 JS를 다운 받는다. 이때 사이트 조작은 불가능하다.(조작은 기억한다)
    5. 다운이 완료되면 브라우저가 JS가 실행한다. 이때 기억하던 조작이 실행되고, 이후부터 상호 조작이 가능하다.
- **장단점**
    - **장점**
        - 이미 콘텐츠가 구성된 페이지가 있기 때문에 SEO에 유리하다
        - 초기 로딩이 빠르다.
    - **단점**
        - TTV(Time To View)와 TTI(Time To Interact) 간에 시간 간격이 존재하여 반응이 느리거나 없을 수 있고, 매 요청마다 새로 고침 되기 때문에 화면 깜빡임이 있어 사용자 경험이 다소 떨어진다.
        - 매 요청마다 서버에서 페이지를 구성하는 모든 리소스를 준비해서 응답하기 때문에 서버 부하가 심하다.