# String vs StringBuffer, StringBuilder

## 공통점

- 문자열을 다루는 대표적인 클래스
- 모두 String을 저장하고 관리하는 클래스

## 차이점

|             | String     | StringBuffer | StringBuilder |
| ----------- | ---------- | ------------ | ------------- |
| 저장위치    | StringPool | Heap         | Heap          |
| Modifiable  | 불변       | 가변         | 가변          |
| thread-safe | O          | O            | X             |
| 동기화      | O          | O            | X             |
| 성능        | 빠름       | 느림         | 빠름          |

- 연산이 많은 경우 성능만 보면, StringBuilder > StringBuffer >> String
  하지만, 상황에 맞게 사용하는게 좋음

```
String   :  문자열  연산이  적고  멀티쓰레드  환경일  경우

StringBuffer   :   문자열  연산이  많고  멀티쓰레드  환경일  경우

StringBuilder   :   문자열  연산이  많고  단일쓰레드이거나  동기화를  고려하지  않아도  되는  경우
```

# String

📌 `불변(immutable)` , `Strnig pool`

## 불변

불변 = 한 번 저장된 값이 절대 변하지 않는다.

```java
String str = "hello";
str = str + "world";
System.out.println(str); //hello world
```

![그림1](https://user-images.githubusercontent.com/57527380/199336438-1176978a-15d2-4f79-a43b-748b22b26351.png)

## String Pool

String 객체 생성하는 방식은 `new String()`, `리터럴("")` 이 있다.

### String str = new String("hello")

- Heap영역에 메모리가 할당된다.
- 같은 문자열이라도 다른 객체이기 때문에 새로운 객체가 메모리에 할당된다.

### String str = "hello"

- String pool에 저장
- 내부적으로 `intern()`메소드 호출된다.  
  ❓ `intern()`
- 주어진 문자열("hello")이 String pool에 존재하는지 확인한 후, 리터럴을 가리키는 참조를 가져오고 없으면 String pool에 리터럴을 넣고 그 주소를 가져오는 메서드

![image](https://user-images.githubusercontent.com/57527380/199326368-d0387187-c435-4fc2-b156-a19cd6150ff0.png)

```java
@Test
public void stringInternTest() {
    String a = "Hello";
    String b = new String("Hello");

    System.out.println("a == b ? : " + (a == b)); // false
    System.out.println("a == b.intern() ? : " + (a == b.intern())); // true
}
```

-> `intern()`메서드를 사용하면 메모리 낭비를 줄일 수 있다.

## 왜 String 객체는 불변일까?(불변으로 얻는 장점이 뭘까)

1. 캐싱 기능에 의한 메모리 절약과 속도 향상

   - 리터럴로 생성할 경우, 같은 문자열을 여러개 생성하지 않고 string pool에 등록되어 있는 객체를 참조하기 때문

2. thread-safe
   - String 객체는 불변이기 때문에 여러 쓰레드에서 동시에 특정 String 객체를 참조하더라도 안전하다.
3. 보안기능
   - 중요한 데이터를 문자열로 다루는 경우 강제로 해당 참조에 대한 문자열 값을 바꾸는 것이 불가능하기 때문에 보안에 유리하다.

하지만, 문자열 추가,수정,삭제와 같은 연산이 많아지면 힙에 Garbage가 많이 생성되고 힙 메모리 부족으로 성능이 저하될 수 있다. 그래서, `StringBuffer`, `StringBuilder`클래스가 도입됨.

# StringBuffer, StringBuilder

📌 `가변` , `append()`, `delete()` 메소드 사용

```java
StringBuffer sb = new StringBuffer("hello");
sb.append("world");

```

![image](https://user-images.githubusercontent.com/57527380/199337289-d084df98-046a-4693-a395-bb49a25bdd3a.png)

## 차이점

`StringBuffer`

- 멀티쓰레드 환경에서 주로 사용(동기화(`synchronized`) 지원)
- 예시
  A스레드, B스레드 모두 StringBuffer 객체의 append()메서드를 사용하려고 할 경우,

1. A스레드 : sb의 append()동기화 블록에 접근 및 실행
2. B스레드 : A스레드 sb의 append() 동기화 블록에 들어가지 못하고 `block`상태가 됨
3. A스레드 : sb의 append()동기화 블록에서 탈출
4. B스레드 : `block` -> `running`상태로 변하고 sb의 append()접근 및 실행  
   `StringBuilder`

- 싱글 쓰레드 환경에서 주로 사용(동기화 지원x)

```java
//////////StringBuffer//////////
    @Override
    public synchronized StringBuffer append(String str) {
        toStringCache = null;
        super.append(str);
        return this;
    }

//////////StringBuilder///////////
    @Override
    public StringBuilder append(String str) {
        super.append(str);
        return this;
    }
```

## 참고

[신입 개발자 기술면접 질문 정리 - 자바](https://dev-coco.tistory.com/153#%F-%-F%--%A-%--String%-C%--StringBuffer%-C%--StringBuilder%EC%-D%--%--%EC%B-%A-%EC%-D%B-%EB%A-%BC%--%EC%--%A-%EB%AA%--%ED%--%B-%EC%A-%BC%EC%--%B-%EC%-A%---)  
[[Java] String, StringBuffer, StringBuilder 차이와 장단점](https://dev-coco.tistory.com/25)  
[what is java String pool](https://www.digitalocean.com/community/tutorials/what-is-java-string-pool)
[자바스프링 풀](https://jake-seo-dev.tistory.com/17)
[StringBuffer와 StringBuilder는 무슨 차이가 있는가](https://velog.io/@heoseungyeon/StringBuilder%EC%99%80-StringBuffer%EB%8A%94-%EB%AC%B4%EC%8A%A8-%EC%B0%A8%EC%9D%B4%EA%B0%80-%EC%9E%88%EB%8A%94%EA%B0%80)
