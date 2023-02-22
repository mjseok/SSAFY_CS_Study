# Java Wrapper class

⚠ Wrapper Class를 알기 전에 알고 있어야 할 것

- 기본형 변수는 Call by value(값에 의한 변경)
- 객체는 Call by Reference(참조에 의한 변경)

## Wrapper class란?

`java.lang`패키지의 클래스들 중 기본형 변수(primitive type)를 감싸는 클래스들이다.
![](https://i.imgur.com/I3ORLpL.png)

## Wrapper class의 필요성

1. 기본 타입의 데이터를 객체로 취급해야 할 때 필요하다.

   - 메서드에 전달된 인수를 수정할 경우
   - 메서드의 인수로 객체 타입만이 요구될 경우

2. java.util패키지의 클래스들은 참조형 변수(객체)만을 다루기 때문
3. ArrayList와 같은 Collection Framework의 데이터 구조는 객체만(참조형 변수만)을 다루기 때문
4. 멀티쓰레딩에서 동기화를 지원하려면 참조형 변수가 필요

## 박싱 & 언박싱

Wrapper Class는 산술 연산을 위해 정의된 클래스가 아니므로, 외부에서 인스턴스에 저장된 값을 변경할 수 없고 값을 참조하기 위해 새로운 인스턴스를 생성하고 생성된 인스턴스의 값만을 참조할 수 있다.

- 박싱 : 기본타입의 데이터를 래퍼 클라스의 인스턴스로 변환하는 과정
- 언박싱: 래퍼 클래스의 인스턴스에 저장된 값을 다시 기본 타입의 데이터로 꺼내는 과정
- JDK 1.5부터는 자바 컴파일러가 자동으로 이를 처리 해줌(오토박싱, 오토언방식)
  예시

```java
Integer num = new Integer(17); // 박싱

int n = num.intValue();        // 언박싱

System.out.println(n); //출력:17



Character ch = 'X'; // Character ch = new Character('X'); : 오토박싱

char c = ch;        // char c = ch.charValue();           : 오토언박싱

System.out.println(c);//출력: X
```

## 더 알아보기

```java
Integer x = 17, y = 17;
    	if (x == y)
			System.out.println("Same");
		else
			System.out.println("Not Same");
```

```java
Integer x = new Integer(17), y = new Integer(17);
    	if (x == y)
			System.out.println("Same");
		else
			System.out.println("Not Same");
```

위의 두 예시 모두 서로 다른 객체를 참조하기 때문에 Not Same이 나온다.

하지만,

```java
Integer x = new Integer(17), y = new Integer(17);
    	if (x.intValue() == y.intValue())
			System.out.println("Same");
		else
			System.out.println("Not Same");
```

위의 예시는 intValue()로 언박싱해서 기본 참조형(int)의 데이터를 꺼내서 비교하기 때문에 Same이 나온다.

```java
Integer x = new Integer(17), y = new Integer(17);
    	if (x.equals(y))
			System.out.println("Same");
		else
			System.out.println("Not Same");
```

위의 예시는 `==` 와 `equals()`의 차이를 알고 있으면 좋다.
`==`연산자는 비교하고자 하는 두 개의 대상의 주소값을 비교한다.
equals메소드는 비교하고자 하는 두 개의 대상의 값 자체를 비교한다.

## 오토박싱, 오토 언박싱

```java
Integer num = new Integer(17); //래퍼 클래스1
        Integer num2 = new Integer(17); //래퍼 클래스2
        int i = 17; //기본타입

        System.out.println("래퍼클래스 == 기본타입 : "+(num == i)); //true
        System.out.println("래퍼클래스.equals(기본타입) : "+num.equals(i)); //true
        System.out.println("래퍼클래스 == 래퍼클래스 : "+(num == num2)); //false
        System.out.println("래퍼클래스.equals(래퍼클래스) : "+num.equals(num2)); //true
    }
}
```

Wrapper클래스와 기본 자료형의 비교에 ==,equals 둘 다 가능한 이유는 컴파일러가 자동으로 오토언박싱을 해주기 때문이다.

Wrapper class내의 equals메서드가 정의된 부분을 보면 알 수 있다.

```java
public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }
```

Wrapper Class안에는 오토박싱을 도와주는 캐시가 있다.

```java
private static class IntegerCache {
        static final int low = -128;
        static final int high;
        static final Integer cache[];

        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127);
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h;

            cache = new Integer[(high - low) + 1];
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++);

            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }

        private IntegerCache() {}
    }

```

-> Integer인스턴스를 미리 생성하고 있음

```java
  public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
    }
```

위의 코드를 보면 캐싱된 값을 사용하고 있다는 것을 알 수 있다.

참고
https://velog.io/@doxxx93/Wrapper-Class%EB%9E%80
http://www.tcpschool.com/java/java_api_wrapper
https://sudo-minz.tistory.com/93
https://applefarm.tistory.com/145
