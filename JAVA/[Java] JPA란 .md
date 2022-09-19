# [Java] JPA란?

JPA에 대해 설명하세요. 그리고 JPA를 사용하면 좋은 점은 뭘까요?

- JPA는 자바에서 ORM(Object-Relational Mapping) 기술 표준으로 사용되는 인터페이스의 모음입니다.
- 즉, 실제적으로 구현된 것이 아닌 구현된 클래스와 매핑을 해주기 위해 사용되는 프레임워크입니다.
- JPA를 구현한 대표적인 오픈소스로는 Hibernate가 있습니다.

먼저 ORM에 대해 알아보겠습니다.

## ORM(Object-Relational Mapping)

![1](https://user-images.githubusercontent.com/48662662/191042467-9c05a6a5-f897-4d80-b694-586cd22b2261.png)

- ORM은 프로그래밍 언어의 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑(연결)해주는 도구입니다.
- ORM은 객체와 데이터베이스의 관계를 매핑해주는 도구입니다.
- ORM은 MVC 패턴에서 모델(Model)을 기술하는 도구입니다.
- ORM은 객체와 모델 사이의 관계를 기술하는 도구입니다.

→ 즉, **애플리케이션 Class와 RDB(Relational DataBase)의 테이블을 매핑**한다는 뜻이며, 기술적으로는 **어플리케이션의 객체를 RDB 테이블에 자동으로 영속화 해주는 것**이라고 보면 됩니다.

- 영속화(영원히 지속하다): JAVA라는 것은 JVM이라는 세상에 존재하게 됩니다. 즉, JVM 밖에서는 지속될 수 없는 것입니다. JVM 이 사라지면 JAVA 객체도 사라지게 되는데 JPA는 JVM 밖에서도 유지하고 싶었던 것입니다. 그래서 JVM에 있던 객체를 JVM 밖에서도 지속하고 싶다는 생각으로 영속화라는 개념이 나오게 되었습니다. 그게 데이터베이스가 될 수도 텍스트 파일이 될 수도 있는데 중요한 것은 JVM 외부에서 객체를 유지하고 싶다는 것입니다.

### 장점

- SQL문이 아닌 Method를 통해 DB를 조작할 수 있어서, 개발자는 객체 모델을 이용하여 비즈니스 로직을 구성하는데에만 집중할 수 있습니다. (내부적으로는 쿼리를 생성하여 DB를 조작하지만 개발자가 이를 신경 쓰지 않아도 됩니다.)
- 선언문, 할당 등의 부수적인 코드가 줄어들어, 각종 객체에 대한 코드를 별도로 작성하여 코드의 가독성을 높일 수 있습니다.
- 객체지향적인 코드 작성이 가능합니다. 오직 객체지향적 접근만 고려하면 되기 때문에 생산성이 증대됩니다.
- 매핑하는 정보가 Class로 명시 되었기 때문에 ERD를 보는 의존도를 낮출 수 있고 유지보수 및 리팩토링에 유리합니다.
    - e.g.) 기존 방식에서 MySQL 데이터베이스를 사용하다가 PostgreSQL로 변환한다고 가정하면, 새로 쿼리를 짜야하는 경우가 생깁니다. 이런 경우에 ORM을 사용하면 쿼리를 수정할 필요가 없습니다.

### 단점

- 프로젝트의 규모가 크고 복잡하여 설계가 잘못된 경우, 속도 저하 및 일관성을 무너뜨리는 문제점이 생길 수 있습니다.
- 복잡하고 무거운 Query는 속도를 위해 별도의 튜닝이 필요하기 때문에 결국 SQL문을 써야할 수 도 있습니다.

## JPA(Java Persistence API)

- Java 진영에서 ORM(Object-Relational Mapping) 기술 표준으로 사용하는 인터페이스 모음입니다.
- 자바 어플리케이션에서 관계형 데이터베이스를 사용하는 방식을 정의한 인터페이스입니다.
- 인터페이스이기 때문에 Hibernate, OpenJPA 등이 JPA를 구현합니다.

![2](https://user-images.githubusercontent.com/48662662/191042529-ebbefd66-1a09-4e1d-9f97-12bd7e3d7078.png)

### 왜 JPA를 사용해야 할까?

- JPA는 반복적인 CRUD SQL을 처리해주고 매핑된 관계로 SQL을 생성하고 실행합니다.
    - 개발자가 반복적인 CRUD SQL을 작성하고 객체를 SQL에 매핑하는데 시간을 쓰지 않아도 됩니다.
    - 추가적으로 JPA는 네이티브 SQL이란 기능을 제공하는데 관계 매핑이 어렵거나 성능에 대한 이슈가 우려되는 경우 SQL을 직접 작성하여 사용할 수 있습니다.
    
- **SQL이 아닌 객체 중심으로 개발할 수 있습니다.**
    - 생산성이 증대되고 유지보수도 수월합니다.
    
- 패러다임의  불일치를 해결합니다.
    - e.g.) JAVA에서는 **부모 클래스와 자식 클래스의 관계 (상속관계)**가 존재하는데 데이터베이스에서는 이러한 객체의 상속관계를 지원하지 않습니다. 이런 상속관계를 JPA는 아래와 같은 방식으로 해결하였습니다.
    - 
        
        ![3](https://user-images.githubusercontent.com/48662662/191042560-db80f518-051a-4199-9400-e8109e2f7499.png)

        
        - 위의 구조에서 Album 클래스를 저장한다고 가정해봅시다.
        
        ```java
        // Album 객체저장
        jpa.persist(album);
        ```
        
        - JPA는 위의 코드를 다음과 같은 쿼리로 변환해서 실행합니다.
        
        ```java
        INSERT INTO ITEM (ID, NAME, PRICE) .....
        INSERT INTO ALBUM (ARTIST) .....
        ```
        
        - 조회할 때는 두 테이블을 엮어서 가져오게 됩니다.
        
        ```java
        // JAVA 코드
        String albumId = "id100";
        Album album = jpa.find(Album.class, albumId);
        
        // 변환된 쿼리
        SELECT I.*, A.*
          FROM ITEM I
          JOIN ALBUM A ON I.ITEM_ID = A.ITEM_ID
        ```
        
    
    - 객체지향에는 **연관관계**도 있습니다. 이것은 Class에서 또 다른 Class Type을 필드 변수로 가지고 있는 것을 말합니다. 객체관계와 이를 테이블 구조로 나타낸 아래 그림을 보겠습니다.
        
        ![4](https://user-images.githubusercontent.com/48662662/191042603-8e1149ff-294a-4189-be82-51a9ddec7556.png)

        
        - Member 클래스가 Team 타입의 team 필드 변수를 가지고 있는 형태인데 코드로 나타내면 아래와 같습니다.
        
        ```java
        class Member {
         String id;
         Team team;
         String username;
        }
        
        class Team {
         Long id;
         String name;
        }
        ```
        
        - 여기서  Team 객체를 참조하는 필드를 가지고 있는 Member 객체는 어떻게 저장할까요? 위에서 봤던 상속구조와 똑같습니다.
        
        ```java
        Member member = new Member();
        member.setId("100");
        member.setUsername("dbjh");
        
        Team team = new Team();
        team.setName("dev_team");
        
        member.setTeam(team);
        jpa.persist(member);
        ```
        
        - MemBer 객체의 team 필드에 Team 객체를 set하고 Member 객체를 DB에 저장하게 되면 JPA는 아래와 같은 코드를 데이터베이스에서 실행합니다.
        
        ```java
        INSERT INTO MEMBER (ID, TEAM_ID, USERNAME) ....
        INSERT INTO TEAM (ID, NAME) ....
        ```
        
        - 이렇게 저장 후 Member 객체만 조회하면, Team 객체 정보도 가져와서 Member 객체의 team 필드에 주입해주기 때문에 아래와 같이 사용할 수 있습니다.
        
        ```java
        // JAVA 코드
        Member member = jpa.find(Member.class, memberId);
        Team team = member.getTeam();
        
        // 변환된 쿼리
        SELECT M.*, T.*
         FROM MEMBER M
         JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID
        ```
        
    

## JPA의 저장 및 조회

- 저장
![5](https://user-images.githubusercontent.com/48662662/191042710-90c4785b-0989-44fc-b0ef-ef8f1e9a24a9.png)


- 조회
![7](https://user-images.githubusercontent.com/48662662/191042727-ec2a3c1b-bf6d-47c7-8f6d-2f74014171a6.png)

- 참고로 JPA는 수정 메소드를 제공하지 않습니다.
    - 수정이 필요할 때는 JPA가 매핑된 객체(테이블 데이터)를 조회해서 값을 변경 후 커밋하면 DB서버에 UPDATE문을 전송하여 UPDATE를 실행합니다.

### REFERENCE

[https://dbjh.tistory.com/77](https://dbjh.tistory.com/77)

[https://hanamon.kr/orm이란-nodejs-lib-sequelize-소개/](https://hanamon.kr/orm%EC%9D%B4%EB%9E%80-nodejs-lib-sequelize-%EC%86%8C%EA%B0%9C/)

[https://velog.io/@monkeydugi/JPA에서-영속화라는-의미는-뭘까](https://velog.io/@monkeydugi/JPA%EC%97%90%EC%84%9C-%EC%98%81%EC%86%8D%ED%99%94%EB%9D%BC%EB%8A%94-%EC%9D%98%EB%AF%B8%EB%8A%94-%EB%AD%98%EA%B9%8C)
