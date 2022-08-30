## Writer: 박찬형

### DB 커넥션 풀(DBCP; Database Connection Pool)이란?
- 웹 컨테이너(WAS)가 실행되면서 DB와 connection을 해놓은 객체를 pool에 저장해 두고 client에서 connection 요청을 받았을 때 pool에 저장된 connection을 사용해 통신한 뒤 다시 pool에 반환하는 기법
- DB와의 커넥션을 관리하는 객체를 지칭하기도 한다.

### 커넥션 풀을 왜 쓰는가?
- DB와 통신을 하려면 새로운 connection을 맺은 다음 작업을 수행하고 다시 connection을 끊어야 하는 과정이 필요하다.
- 아래는 JDBC를 사용하는 예제 샘플 소스코드

```java
Connection conn = null;
PreparedStatement  pstmt = null;
ResultSet rs = null;

try {
    sql = "SELECT * FROM T_BOARD"

    // 1. 드라이버 연결 DB 커넥션 객체를 얻음
    connection = DriverManager.getConnection(DBURL, DBUSER, DBPASSWORD);

    // 2. 쿼리 수행을 위한 PreparedStatement 객체 생성
    pstmt = conn.createStatement();

    // 3. executeQuery: 쿼리 실행 후
    // ResultSet: DB 레코드 ResultSet에 객체에 담김
    rs = pstmt.executeQuery(sql);
    } catch (Exception e) {
    } finally {
        conn.close();
        pstmt.close();
        rs.close();
    }
}
```
- DB와 실시간 통신 방식으로 자주 연결한다면 매번 connect, close에 처리 비용이 생기게 되는데, 이때 connection pool 방식을 사용하면 반복적인 처리 비용을 줄일 수 있다.
- 물리적인 DB Connection 부하를 줄일 수 있다.
- 따라서, Connection을 생성하고 닫는 시간이 소모되지 않기 때문에 그만큼 애플리케이션의 성능(실행 속도)이 향상된다.
- 한 번에 생성될 수 있는 connection 수를 제어하기 때문에 동시 접속자가 몰려도 웹 애플리케이션이 쉽게 다운되지 않는다.

### 실시간 통신과 커넥션 풀을 사용했을때의 차이점
- ➕ Connect, close 비용을 줄일 수 있어, 어느 정도 수준의 많은 request를 처리할때 실시간 통신보다 유리하다.
- ➖ Connection도 결국은 객체기 때문에 메모리를 차지하게 된다. 따라서, connection pool을 무작정 많이 늘리면 메모리를 사용해 오히려 성능이 떨어질 수 있다.

### References
- https://hyuntaeknote.tistory.com/12
- https://linked2ev.github.io/spring/2019/08/14/Spring-3-%EC%BB%A4%EB%84%A5%EC%85%98-%ED%92%80%EC%9D%B4%EB%9E%80/
