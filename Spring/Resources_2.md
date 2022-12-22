_2.4~2.6_

# `ResourceLoader` Interface

[문서]  
`Resource` 인스턴스를 반환 할 수 있는 객체를 의미한다.
모든 application context는 `ResourceLoader`인터페이스를 구현할 수 있다. 즉, 어떤 application context를 사용해도 `Resource`인스턴스를 얻을 수 있다.

```java
public interface ResourceLoader {

    Resource getResource(String location);

    ClassLoader getClassLoader();
}
```

##

어떤 application context의 `getResource()`를 호출할 때 파라미터로 제공하는 location path에 적절한 prefix가 없으면 적합한 `Resource`타입을 리턴하게 된다.

예시)

```java
Resource template = ctx.getResource("some/resource/path/myTemplate.txt");
```

이 코드가 `ClassPathXmlApplicationContext`에서 실행되면 `ClassPathResource`가 리턴되고 `FileSystemXmlApplicationContext`에서 실행되면 `FileSystemResource`를 리턴한다. `WebApplicationContext`인스턴스라면 `ServletContextResource`를 리턴한다.

1. 특정 어플리케이션 컨텍스트에 적합한 방식으로 알아서 리소스를 로드할 수 있다.
2. prefix를 사용하면 해당 리소스를 리턴하도록 강제할 수도 있다.  
   ex) `classpath:`이라는 prefix를 사용하면 어플리케이션 컨텍스트 타입과 상관없이 `ClassPathResource`를 리턴할 수 있다.

```java
Resource template = ctx.getResource("classpath:some/resource/path/myTemplate.txt");
//classpath:로 resource를 강제시킴
Resource template = ctx.getResource("file:///some/resource/path/myTemplate.txt");
Resource template = ctx.getResource("https://myhost.com/resource/path/myTemplate.txt");
```

# `ResourcePatternResolver` Interface

[문서]

- `ResourceLoader`인터페이스를 확장한 것
- 목적 : location pattern 을 읽고 `Reource`객체로 해석하기

```text
❓ location pattern : *,**,?와 같은 특수문자를 이용한 경로 표현
대부분의 URL Mapping설정은 Ant Pattern으로 한다.
예시 ) "/member/*.jsp"
```

```java
public interface ResourcePatternResolver extends ResourceLoader {

    String CLASSPATH_ALL_URL_PREFIX = "classpath*:";
    // class path에서 일치하는 모든 리소스를 의미하는 prefix를 정의
    Resource[] getResources(String locationPattern) throws IOException;
}
```

`PathMathcingResourcePatternResolver`는 `ApplicationContext`외부에서 사용할 수 있는 standalone(독립적인) 구현체입니다.  
`Resource[]` bean 프로퍼티를 채워넣기 위해 `ResourceArrayPropertyEditor`에서도 사용한다.  
`PathMathcingResourcePatternResolver`는 지정된 리소스 경로를 일치하는 하나 이상의 `Resource`객체들을 정할 수 있다. -> source path는 대상 `Resource`에 대한 일대일 매핑가능한 경로를 쓰거나 `classpth:*`처럼 prefix를 쓰거나 또는 ant style로 작성하면 된다.
사용 예시)

```java
PathMatchingResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
resource = resolver.getResource("file:pom.xml");
```

파일시스템 파일을 가져올 때 prefix로 `file:`을 사용했고 현재 사용자 경로 아래의 `pom.xml`파일을 가져오는 코드입니다.

# `ResourceLoaderAware` 인터페이스

- `ResourceLoader`를 제공받을 것으로 예상되는 구성요소를 식별하는 callback interface이다.

```java
public interface ResourceLoaderAware {

    void setResourceLoader(ResourceLoader resourceLoader);
}
```

`ApplicationContext`는 `ReourceLoader`이기 때문에 bean은 `ApplicationContextAware`를 구현할 수 있고 resource를 load할 수 있다.  
하지만, 일반적으로 `ResourceLoader`인터페이스를 사용하는 것이 좋다.(why? resource로딩 인터페이스와만 관련이 있고 ApplicationContext인터페이스와는 관련없이 하는 것이 좋다.)

[정리]

### `ResourceLoader`

- 자원을 가져오는 클래스들이다.
- `getResource()`를 사용하면 하나의 Resource를 얻는다.
- 모든 ApplicationContext들은 이 인터페이스를 구현하고 있기 때문에 `Resource`들을 가져오는 기능을 가지고 있다.

### `ResourcePatternResolver`

- `ResourceLoader`의 확장판
- 패턴에 맞는 Resource들을 전부 가져올 수 있다.
- 모든 ApplicationContext에 있는 ResourceLoader 구현체들은 사실 이 인터페이스를 구현하고 있다.

### `ResourceLoaderAware`

- 다른 클래스가 context내에 있는 ResourceLoader를 사용하고 싶을 때 이를 구현하면 된다.
- `resourceLoader를 아는(Aware)상태가 된다.
- Context에 Bean으로 등록되어야 하니까 Component로 등록되어야 한다.
- Context가 `ResourceLoaderAware`를 구현한 클래스에 넣어준다.(Context 자체가 ResourceLoader를 구현한 구현체)

즉,  
스프링은 Resource를 사용하는 클래스를 ResourceLoader로 지정해줘야하고 이에 또 확장 기능을 하는 ResourcePatternResolver를 만들고 이것을 다른 클래스에서 사용하도록 ResourceLoaderAware라는 클래스를 만듦

```text
파일을 다른 이름으로 => Resource
파일을 가져온다 => ResourceLoader
그것을 전략적으로 더 세분화 시킨다 => ResourcePatternResolver
파일을 가져오는 것을 데려온다 => ResourceLoaderAware
```

-> 객체지향 프로그래밍을 위한 것  
-> 책임을 최대한 조각내는 것

## 참고

[스프링 Core 문서 읽고 정리하기 - 2. Resource ~ 5. Aop](https://appleg1226.tistory.com/26)
[[RTFM] Spring Core - 02. Resources](https://wbluke.tistory.com/65)
