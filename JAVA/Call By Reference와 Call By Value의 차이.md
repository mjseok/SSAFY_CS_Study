### 개요
프로그래밍 언어들의 메소드 매개변수 호출 방식에는 여러 가지가 있으며, 호출 방식은 언어마다 다르다.
<br><br><br>

### Call by Value와 Call by Reference
- #### Call by Value
Call by Value 는 메소드를 호출할 때 값을 넘겨주기 때문에 Pass by Value 라고도 부른다.<br>
메소드를 호출하는 호출자(Caller)의 변수와 호출을 당하는 수신자(Callee)의 파라미터는 복사된 **서로 다른 변수**이다.<br>
값만을 전달하기 때문에 수신자의 파라미터를 수정해도 호출자의 변수에는 **아무런 영향이 없다**.<br>

- #### Call by Reference
Call by Reference 는 참조(주소)를 직접 전달하기 때문에 Pass By Reference 라고도 부른다.<br>
참조를 직접 넘기기 때문에 호출자의 변수와 수신자의 파라미터는 **완전히 동일한 변수**이다.<br>
메소드 내에서 파라미터를 수정하면 그대로 원본 변수에 반영된다.<br>
<br><br><br>

### Java의 메소드 매개변수 호출 방식은?
Java로 개발을 해봤다면 메소드로 변수를 넘기고 거기서 값을 수정해본 경험이 있을 것이다.<br>
그래서, Call by Reference 라고 오해할 수 있지만, **Java는 오직 Call by Value 로만 동작**한다. <br>
**(둘을 혼용하는 것도 아니고 Call by Value만을 사용!!!!)**<br>
<br><br><br>

### 직접 호출해보기
![image](https://user-images.githubusercontent.com/44194990/193818990-c30711b7-4098-4d38-a3f4-bfb95524e4e3.png)<br>
Java에서는 Call by Value 방식을 수행할 때, 값을 넘겨받은 메소드에서 값을 복사하여 새로운 지역 변수에 저장한다. <br>
이때, method2는 method1의 변수를 사용한 것이 아니라, 자신이 새롭게 생성한 지역 변수에 method1의 **변수 이름과 변수 값을 복사하여 사용**하는 것이다.<br>
이 때문에 아무리 method2에서 a와 b의 값을 바꾸어도 method1의 a, b에 영향을 끼칠 수 없다. <br>
a, b는 이름만 같을 뿐 다른 주소를 가지는 별개의 변수들이기 때문이다.<br>
<br><br><br>

### 그럼 왜 Call by Reference로 오해하는가?
![image](https://user-images.githubusercontent.com/44194990/193821714-b4094118-c465-4734-9b03-f6f03976ab9b.png)<br>
애초에 '참조 타입' 이라고 이름이 붙은 이유가 Heap Memory 영역에 생성된 객체의 주소값을 참조하기 때문이다.<br>
위의 예시의 경우 method1에서 method2로 넘겨준건 student의 주소값이고, method1이 가지고 있는 주소값과 동일한 주소값을 가지고 이 객체의 상태를 수정하면 당연히 두 student은 동일한 주소를 참조하기 때문에 이러한 결과가 나온다.<br>
<br><br><br>

### 그럼 String형과 Student형의 차이는?
Java에서의 메모리는 Stack 영역과 Heap 영역으로 나뉘어 진다.<br>
Stack의 경우에는 정적으로 할당된 메모리 영역이다.<br>
Stack에는 **Primitive 타입 (boolean, char, short, int, long, float, double) 의 데이터가 값과 같이 할당**된다.<br>
![image](https://user-images.githubusercontent.com/44194990/193830774-e2a8c786-8613-48cd-bd2f-dbcb5a6f1da8.png)<br>
<br>
또, Stack에는 Heap 영역에 생성된 **Object 타입의 데이터의 참조값이 할당**된다.<br>
Heap의 경우에는 동적으로 할당된 메모리 영역이다.<br>
Heap 영역에서는 **모든 Object 타입의 데이터가 할당**이 된다.<br>
Heap 영역의 Object를 가리키는 참조변수가 Stack에 할당이 된다.<br>
![image](https://user-images.githubusercontent.com/44194990/193827352-13b43bae-b70e-4266-af34-138bb9ad2268.png)<br>
<br>
하지만 참조 타입중에 주의해야할 타입이 있는데 그건 바로 String(문자열) 타입이다.<br>
String 타입의 경우 문자열 리터럴(값)이 동일하다면 String 객체가 공유된다.<br>
![image](https://user-images.githubusercontent.com/44194990/193828463-19508617-1f4f-49c7-828f-e4e993d8e6a8.png)<br>
가령 위와 같이 a, b에 같은 값을 저장하고 비교 연산(==)을 수행하면 기본 타입에 의한 결과가 나오게 된다.<br>
하지만, **new 연산자로 새로운 String 객체를 생성해 저장을 한다면, 참조 타입에 의한 결과**가 나오게 된다.<br>
즉, String은 Heap 영역에 생성되는 Object 타입임에도 불구하고, new를 사용하지 않으면, Stack에 생성된 Primitive 타입처럼 사용할 수 있다.<br>
<br><br><br>

### 동일한 변수명, 다른 주소를 쓰고자 한다면?
즉, 동일한 이름을 사용하되 다른 주소를 참조하게 하고자 한다면,<br>
![image](https://user-images.githubusercontent.com/44194990/193823902-224038e1-d78d-4c32-aa0b-f0d994b27e96.png)<br>
다음과 같이 new 키워드를 사용하여 새로운 객체를 생성한 후, 값을 수정해주면 된다.<br>
<br><br><br>
