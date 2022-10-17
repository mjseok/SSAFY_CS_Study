> 자바 라이브러리에는 close 메서드를 호출해 직접 닫아줘야 하는 자원이 있다. 그 예로는 InputStream, OutputStream, java.sql.Connection 등이 있다. 

<br>


- 꼭 회수해야 하는 자원을 다룰 때는 try-finally 말고, **try-with-resources문**을 사용한다.

- 코드가 더 간결해지고 놓치는 예외정보 없이 사용할 수 있다.

  <br>

---

<br>

**try-catch-finally문**

1. try 블록 안에서 예외가 발생하면 그 순간에 코드 실행을 중단한다.
2. 발생된 예외종류가 catch 블럭에서 지정한 예외와 일치하면 그 안의 코드를 실행한다.
3. catch 블록 안의 코드가 모두 실행되면 try catch 블록 다음에 등장하는 코드를 실행한다.

4. finally 구문은 예외처리의 발생여부를 떠나 무조건 실행되도록 하는 구문이다.

<br>

### try-finally 문을 사용할 경우

```java
public class FirstException extends RuntimeException {
}
```

<br>

```java
public class SecondException extends RuntimeException {
}
```

<br>

```java
public class MyResource implements AutoCloseable {

    public void doSomething() throws FirstException {
        System.out.println("doing something");
        throw new FirstException();
    }

    @Override
    public void close() throws SecondException {
        System.out.println("clean my resource");
        throw new SecondException();
    }
}
```

<br>

```java
public class AppRunner {

	public static void main(String[] args) {
		MyResource myResource = new MyResource();
		
//		myResource.doSomething();
//		myResource.close();
		
		try {
			myResource.doSomething();
		}finally {
			myResource.close();
		}
	}
}
```

<br>


**실행결과**

```java
doing something
Exception in thread "main" clean my resource
item9.SecondException
	at item9.MyResource.close(MyResource.java:13)
	at item9.AppRunner.main(AppRunner.java:12)
```

- 예외는 try문과 finally문 안에서 모두 발생하지만 두번째 에러인 close에 대한 예외 정보만 확인할 수 있다.
- 스택 추적 내역에 첫 번째 예외에 관한 정보는 남지 않게 된다.
- 처음 발생한 예외를 확인할 수 없기 때문에 디버깅이 어렵다.

<br>

**자원이 둘 이상이면 try-finally 방식은 가독성이 떨어진다**

```java
static void copy(String src, String dst) throws IOException {
        InputStream in = new FileInputStream(src);
        try {
            OutputStream out = new FileOutputStream(dst);
            try {
                byte[] buf = new byte[BUFFER_SIZE];
                int n;
                while ((n = in.read(buf)) >= 0) {
                    out.write(buf, 0, n);
                }
            } finally {
                out.close();
            }
        } finally {
            in.close();
        }
```

- 자원이 둘 이상이면 코드가 복잡해진다.

 <br> 

---

<br>

이러한 문제점들을 자바 7의 **try-with-resources**문을 사용하여 해결할 수 있다.

- try-with-resources문을 사용하기 위해서는 <u>AutoCloseable 인터페이스</u>를 구현해야 한다. 
- void를 반환하는 close 메서드 하나만 정의한 인터페이스이다.

```java
public interface AutoCloseable {
    void close() throws Exception;
}
```

![](https://velog.velcdn.com/images/rlooo/post/3928f373-a02b-4fed-87a3-87d4b2e1e78d/image.PNG)

<br>

### try-with-resources 문을 사용할 경우

```java
public class AppRunner2 {

	public static void main(String[] args) {
		try (MyResource myResource = new MyResource()){
			myResource.doSomething();
		}
	}
}
```

- 괄호() 안에 객체를 생성하는 문장을 넣으면 try 블럭을 벗어나는 순간 자동적으로 close()가 호출된다.
- 단, 클래스가 AutoCloseable 이라는 인터페이스를 구현한 것이어야만 한다.

- 두 문장 이상을 넣을 경우 ;으로 구분한다.

- 짧고 읽기 수월하다.

<br>  

**실행결과**

```java
doing something
clean my resource
Exception in thread "main" item9.FirstException
	at item9.MyResource.doSomething(MyResource.java:7)
	at item9.AppRunner2.main(AppRunner2.java:7)
	Suppressed: item9.SecondException
		at item9.MyResource.close(MyResource.java:13)
		at item9.AppRunner2.main(AppRunner2.java:8)
```

- 에러가 여러개 발생하면 프로그래머에게 보여줄 예외 하나만 기록되고 <u>나머지는 숨겨진다.</u>

- 버려지는 것이 아니라 스택 추적 내역에 ‘숨겨졌다(suppressed)’는 꼬리표를 달고 출력된다.

<br>

- 자바 7에서 Throwable에 추가된 getSuppressed 메서드를 이용하면 프로그램 코드에서 가져올 수도 있다.

```java
void addSuppressed(Throwwable exception) 
Throwable[] getSuppressed() 
```

- addSuppressed() : suppressed된 예외를 추가
- getSuppressed() : suppressed된 예외(배열)를 반환

<br>


**복수의 자원을 처리하는 try-with-resources문**

```java
static void copy(String src, String dst) throws IOException {
        try (InputStream in = new FileInputStream(src);
             OutputStream out = new FileOutputStream(dst)) {
            byte[] buf = new byte[BUFFER_SIZE];
            int n;
            while ((n = in.read(buf)) >= 0) {
                out.write(buf, 0, n);
            }
        }
    }
```

<br>

**try-with-resources와 catch문을 함께 쓸 수 있다**

```java
static String firstLineOfFile(String path, String defaultVal) {
        try (BufferedReader br = new BufferedReader(new FileReader(path))) {
            return br.readLine();
        } catch (IOException e) {
            return defaultVal;
        }
    }
```

- 위 코드는 파일을 열거나 데이터를 읽지 못했을 때 예외를 던지는 대신 기본값을 반환한다.

- catch절을 함께 써서 try문을 중첩하지 않고 다수의 예외를 처리할 수 있다.

 <br> 

---

<br>

### REFERENCE

- https://mozi.tistory.com/m/550
