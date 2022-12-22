## 포인트컷 API
* ``Pointcut``: 부가 기능 적용 여부를 판단하는 필터링 로직, 주로 클래스와 메서드 이름으로 필터링 한다. 이름 그대로 어떤 포인트(point)에 기능을 적용할지 말지에 대해 잘라서(cut) 구분하는 것이다.
* ``Advice``: 프록시가 호출하는 부가 기능, 프록시 로직이다.
* ``Advisor``: ``Pointcut`` 1개 + ``Advice`` 1개

조언(``Advice``)를 어디(``Pointcut``)에 할 것인가?  
조언자(``Advisor``)는 어디(``Pointcut``)에 조언(``Advice``)을 해야할지 알고 있다.  

<br/>
1. 개념
스프링의 포인트컷 모델은 어드바이스 타입과 독립적으로 포인트컷을 재사용할 수 있다. 그래서 다른 대상 어드바이스에 같은 포인트컷을 사용할 수 있다.

```java
public interface Pointcut {

    ClassFilter getClassFilter();

    MethodMatcher getMethodMatcher();
}
```
Pointcut 인터페이스를 두 부분으로 분리해서 클래스와 메서드 매칭 부분과 세밀한 구성 작업(다른 메서드 매처(matcher)와의 "결합" 작업같은)을 재사용할 수 있다.

ClassFilter 인터페이스는 포인트컷을 주어진 대상 클래스의 세트로 제한하는데 사용한다. matches() 메서드가 항상 true를 반환한다면 모든 대상 클래스가 매치될 것이다.

```java
public interface ClassFilter {

    boolean matches(Class clazz);
}
```
일반적으로는 MethodMatcher 인터페이스가 더 중요하다. 전체 인터페이스는 다음에 나와있다.

```java
public interface MethodMatcher {

    boolean matches(Method m, Class targetClass);

    boolean isRuntime();

    boolean matches(Method m, Class targetClass, Object[] args);
}
```
matches(Method, Class) 메서드는 대상 클래스의 주어진 메서드가 매치될 것인지를 검사하는데 사용한다. 이 평가작업은 메서드를 호출할 때마다 검사하는 것을 피하기 위해서 AOP 프록시를 생성할 때 수행할 수 있다. 2개의 아규먼트로 매칭하는 메서드가 주어진 메서드에 대해서 true를 반환하고 MethodMatcher에 대해서 isRuntime() 메서드가 true를 반환하면 3개의 아규먼트로 매칭하는 메서드는 메서드가 호출될 때마다 실행될 것이다. 이는 대상 어드바이스가 실행되기 이전에 포인트컷이 메서드 호출에 전달된 아규먼트를 바로 볼 수 있도록 한다.

대부분의 MethodMatcher는 static이다. 즉 MethodMatchers의 isRuntime() 메서드는 false를 반환한다. 이 경우에 3개의 아규먼트로 매칭하는 메서드는 실행되지 않을 것이다.
> AOP 프레임워크가 AOP 프록시를 생성할 때 포인트컷의 평가 결과를 캐시할 수 있도록 가능한한 포인트컷을 스태틱으로 만들어라.


코드를 통해 바로 살펴보겠다. 이번에는 ``save()``와 ``find()`` 두 메서드를 가지고 있는 인터페이스와 클래스를 이용해보겠다.  

```java
public interface ServiceInterface {
    void save();

    void find();
}

@Slf4j
public class ServiceImpl implements ServiceInterface {

    @Override
    public void save() {
        log.info("save 호출");
    }

    @Override
    public void find() {
        log.info("find 호출");
    }
}
```

```java
@Test
public void advisor() {
  ServiceInterface target = new ServiceImpl();
  ProxyFactory factory = new ProxyFactory(target);
  DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(Pointcut.TRUE, new BepozAdvice());

  factory.addAdvisor(advisor);
  ServiceInterface proxy = (ServiceInterface) factory.getProxy();

  proxy.save();
  proxy.find();
}
/*
BepozAdvice - BepozAdvice.invoke()
BepozAdvice - chicken is god
ServiceImpl - save 호출
BepozAdvice - BepozAdvice.invoke()
BepozAdvice - chicken is god
ServiceImpl - find 호출
```

``Advisor`` 인터페이스의 가장 일반적인 구현체인 ``DefaultPointcutAdvisor``를 생성했다. 파라미터로 ``Pointcut``과 ``Advice``를 전달하면된다. ``Poinrtcut.TRUE``는 항상 true를 전달하는 ``Pointcut``이다. ``addAdvisor``를 통해 팩토리에 추가해준다.  

<img src="https://user-images.githubusercontent.com/45073750/147382875-e62b6efc-f8ac-4e79-9d9f-95f747af7c34.png" alt="image" style="zoom:67%;" />

다음과 같이 참고하게 될 것이다.  
이제 ``Pointcut``을 직접 만들어서 메서드에 따라 ``Advice``를 적용시킬지 말지에 대해 판별하게 해보겠다.  

``Pointcut``은 ``ClassFilter``와 ``MethodMatcher`` 이렇게 두 개의 타입으로 이루어진다.  
클래스와 메서드가 둘 다 맞아야지만 true를 반환한다.  

```java
@Slf4j
public class MyPointcut implements Pointcut {

    @Override
    public ClassFilter getClassFilter() {
        log.info("MyPointcut.getClassFilter()");
        return ClassFilter.TRUE;
    }

    @Override
    public MethodMatcher getMethodMatcher() {
        log.info("MyPointcut.getMethodMatcher()");
        return new MyMethodMatcher();
    }
}
```

```java
@Slf4j
public class MyMethodMatcher implements MethodMatcher {

    private final String MATCH_NAME = "save";

    @Override
    public boolean matches(Method method, Class<?> targetClass) {
        boolean result = method.getName().equals(MATCH_NAME);
        log.info("포인트컷 호출 method={} targetClass={}", method.getName(), targetClass);
        log.info("포인트컷 결과 result={}", result);
        return result;
    }

    @Override
    public boolean isRuntime() {
        return false;
    }

    @Override
    public boolean matches(Method method, Class<?> targetClass, Object... args) {
        return false;
    }
}
```

```java
@Test
public void customAdvisor() {
  ServiceInterface target = new ServiceImpl();
  ProxyFactory factory = new ProxyFactory(target);
  DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(new MyPointcut(), new BepozAdvice());

  factory.addAdvisor(advisor);
  ServiceInterface proxy = (ServiceInterface) factory.getProxy();

  proxy.save();
  proxy.find();
}
/*
MyPointcut - MyPointcut.getClassFilter()
MyPointcut - MyPointcut.getMethodMatcher()
MyMethodMatcher - 포인트컷 호출 method=save targetClass=class com.example.advancedpractice.advice.ServiceImpl
MyMethodMatcher - 포인트컷 결과 result=true
BepozAdvice - BepozAdvice.invoke()
BepozAdvice - chicken is god
ServiceImpl - save 호출
MyPointcut - MyPointcut.getClassFilter()
MyPointcut - MyPointcut.getMethodMatcher()
MyMethodMatcher - 포인트컷 호출 method=find targetClass=class com.example.advancedpractice.advice.ServiceImpl
MyMethodMatcher - 포인트컷 결과 result=false
ServiceImpl - find 호출
```

결과 로그를 보면 ``find()`` 호출 시에는 내가 생성한 ``MyMethodMatcher``에서 false를 return 하기 때문에 부가 ``BepozAdvice`` 호출이 되지 않는 것을 확인할 수가 있다.  

이번에는 ``BepozPointcut``이 아닌 스프링에서 제공하는 ``NamedMatchMethodPointcut``을 사용해보겠다.  

```java
@Test
public void nameMatchMethodPointcut() {
  ServiceInterface target = new ServiceImpl();
  ProxyFactory factory = new ProxyFactory(target);
  NameMatchMethodPointcut pointcut = new NameMatchMethodPointcut();
  pointcut.setMappedNames("save");

  DefaultPointcutAdvisor advisor = new DefaultPointcutAdvisor(pointcut, new BepozAdvice());

  factory.addAdvisor(advisor);
  ServiceInterface proxy = (ServiceInterface) factory.getProxy();

  proxy.save();
  proxy.find();
}
/*
BepozAdvice - BepozAdvice.invoke()
BepozAdvice - chicken is god
ServiceImpl - save 호출
ServiceImpl - find 호출
```

이렇게 스프링은 여러 ``Pointcut``을 제공한다.(``JdkRegexpMethodPointcut``, ``TruePointcut`` ...)  
그 중에서 aspectJ 표현식을 사용하는 ``AspectJExpressionPointcut``을 특히나 많이 사용하게 된다.  
