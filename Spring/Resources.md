_2.1~2.3_
[공식문서](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources)

## Spring의 `Resource`란?

[문서]  
`java.net.URL` 클래스는 모든 low-level resource까지 접근 할 수 없다. 이런 한계를 넘어서기 위해서 스프링에서 추가로 구현한 것이며, Resource에 대한 접근을 추상화하기 위한 인터페이스이다.

- `java.net.URL`을 Resource로 감싸서 추상화 한다.
- `org.springframework.core.io` 패키지에 속해 있다.

[정리]  
`java.net.URL`은 class path기준으로 리소스를 읽어올 수 없다.  
class path?  
 JVM이 프로그램을 실행할 때 클래스 파일을 찾는 데 기준이 되는 파일 경로(`.class`파일이 포함된 파일의 경로)
`java.net.URL` 자바를 이용하여 URL을 다루는 클래스

이 외에도
`java.net.URL`은 ServletContext를 기준으로 상대 경로를 읽어오는 기능이 없음  
 URL prefix를 이용해 새로운 핸들러 등록하여 URL접미사를 만들어 사용할 수 있지만 구현이 복잡하고 편의성이 없다.

## Resource 구현되어 있는 모습을 살펴보자.

```java
public interface Resource extends InputStreamSource {

    boolean exists();

    boolean isReadable();

    boolean isOpen();

    boolean isFile();

    URL getURL() throws IOException;

    URI getURI() throws IOException;

    File getFile() throws IOException;

    ReadableByteChannel readableChannel() throws IOException;

    long contentLength() throws IOException;

    long lastModified() throws IOException;

    Resource createRelative(String relativePath) throws IOException;

    String getFilename();

    String getDescription();
}
```

참고로 `InputStreamSource`인터페이스는 다음과 같이 구현되어 있다.

```java
public interface InputStreamSource {

    InputStream getInputStream() throws IOException;
}
```

## `Resource` 인터페이스의 주요 메서드

`exists()` : 리소스가 있는지 없는지 확인

`isOpen()` : Stream이 열렸는지 확인

`getDescription()` : 전체 경로를 포함한 파일 이름이나 실제 URL을 가져온다.

## Built-in Resource Implementations

Reousrce의 주요 구현체는 다음과 같다.

### UrlResource

- `java.net.URL`을 wrapping한 것
- 다양한 종류의 Resource에 접근 가능하지만 기본적으로는 http, https로 원격 접근한다.
- - url은 기본 프로토콜 http, https, ftp, file, jar와 같은 prefix를 지원한다.

### ClassPathResource

- classpath로 부터 resource를 얻어올 때 필요.
- `classpath` prefix(접두어) 사용하면 classpath기준으로 리소스를 찾는다.

### FileSystemResource

- file을 다루기 위한 것
- filesystem기준으로 리소스를 찾는다.

### PathResource

- Path api를 통해 `java.nio.file.Path`를 다루는 작업을 수행한다.
- URL, File과 `WritableResources`를 다룬다.

### InputStreamResource

- 다른 resource와는 다르게 already-opened 리소스이다.
- `isOpen()`메서드를 사용하면 true를 return한다.

### ByteArrayResource

- 주어진 byte 배열을 구현하는 리소스
- 지정된 바이트 배열에서 contents를 load할 떄 유용하다.

### ServletContextResource

[문서]

- 상대 웹 루트 주소내에서 상대 주소를 해석하는 `ServletContext` resource 구현체
- Stream 접근, URL 접근이 가능하고 `java.io.File`접근도 가능하지만, 파일 접근은 리소스가 실제로 파일시스템에 있거나 웹 저장소(archive)가 확대될 때만 접근 가능하다.(java.io.File access only when the web application archive is expanded and the resource is physically on the filesystem. )
  [정리]
- 웹 어플리케이션 루트에서 상대 경로로 리소스를 찾는 것
- 가장 많이 사용되는 구현체인데, 그 이유는 읽어 들이는 리소스 타입이 ApplicationContext와 관련이 있기 때문이다.

## 리소스 읽어오기

읽어 들이는 Resource의 타입은 location문자열, ApplicationContext의 타입에 따라 결정된다.  
즉,

```
var ctx = new ClassPathXmlApplicationContext("app.xml");
```

이 경우에는 `app.xml`을 가진 ApplicationContext의 타입이 `CalssPathXmlApplicationContext`이기 때문에 app.xml을 찾을 때 classpath를 기준으로 찾게 된다. (= ClassPathResource를 통해 빈을 찾아내거나 빈 주입을 해준다.)

여기서 ApplicationContext의 타입이 WebApplicationContext이하 인 경우에는 ServletContextResource를 쓰게 된다.  
ApplicationContext의 타입에 따라 Resource의 타입이 결정된다.  
예를 들어서, ApplicationContext의 타입이 ClassPathXmlApplicationContext이면 getResource() 메소드에 아무런 문자열을 주지 않아도 classpath 기준으로 읽어오게 된다는 뜻이다.

## 참고

[[스프링 핵심] Resource 추상화](https://it-mesung.tistory.com/50)  
[스프링 문서](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources)  
[Resource 추상화](https://yadon079.github.io/2021/spring/resource-abstraction)
