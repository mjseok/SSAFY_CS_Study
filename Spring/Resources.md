[공식문서](https://docs.spring.io/spring-framework/docs/current/reference/html/core.html#resources)

## Spring의 `Resource`란?

`java.net.URL` 클래스는 모든 low-level resource까지 접근 할 수 없다. 이런 한계를 넘어서기 위해서 스프링에서 추가로 구현한 것이며, Resource에 대한 접근을 추상화하기 위한 인터페이스이다.

- `java.net.URL`을 Resource로 감싸서 추상화 한다.
- `org.springframework.core.io` 패키지에 속해 있다.

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

### ClassPathResource

- classpath로 부터 resource를 얻어올 때 필요.

### FileSystemResource

- file을 다루기 위한 것

### PathResource

- Path api를 통해 `java.nio.file.Path`를 다루는 작업을 수행한다.
- URL, File과 `WritableResources`를 다룬다.

### ServletContextResource

- 상대 웹 루트 주소내에서 상대 주소를 해석하는 `ServletContext` resource 구현체
- Stream 접근, URL 접근이 가능하고 `java.io.File`접근도 가능하지만, 파일 접근은 리소스가 실제로 파일시스템에 있거나 웹 저장소(archive)가 확대될 때만 접근 가능하다.(java.io.File access only when the web application archive is expanded and the resource is physically on the filesystem. )

### InputStreamResource

- 다른 resource와는 다르게 already-opened 리소스이다.
- `isOpen()`메서드를 사용하면 true를 return한다.

### ByteArrayResource

- 주어진 byte 배열을 구현하는 리소스
- 지정된 바이트 배열에서 contents를 load할 떄 유용하다.
