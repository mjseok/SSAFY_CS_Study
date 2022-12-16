Garbage Collection은 heap 메모리를 관리한다. Java에서는 heap에서 참조하지 못하는 메모리는 JVM이 자동으로 반환해준다.

<br>

## 가비지 컬렉션 과정

 <br>

GC에 대해서 알아보기 전 **"stop-the-world"**에 대해서 알아보자.

<u>**stop-the-world**란 GC를 실행하기 위해 JVM이 애플리케이션 실행을 멈추는 것이다.</u> 

- stop-the-world가 발생하면 GC를 실행하는 스레드를 제외한 나머지 스레드는 모두 작업을 멈춘다. GC를 완료한 이후에 중단된 작업을 다시 시작한다. 
- GC 튜닝은 이 stop-the-world 시간을 줄이는 것이다.

<br>

---

<br>

Java에서는 개발자가 프로그램 코드로 메모리를 명시적으로 해제하지 않기 때문에 **가비지 컬렉터(Garbage Collector)**가 더 이상 필요 없는 객체를 찾아 지우는 작업을 한다. 

- 명시적으로 해제하려고 해당 객체를 null로 지정하거나 `System.gc()` 메서드를 호출하는 것은 지양해야 한다.  `null`로 지정하는 것은 큰 문제가 안 되지만, `System.gc()` 메서드를 호출하는 것은 시스템의 성능에 매우 큰 영향을 끼치므로 절대로 사용하면 안 된다 

<br>

이 가비지 컬렉터는 두 가지 가설 하에 만들어졌다. 

**weak generational hypothesis**

- 대부분의 객체는 금방 접근 불가능 상태(unreachable)가 된다.
- 오래된 객체에서 젊은 객체로의 참조는 아주 적게 존재한다.

<br>

이 가설을 바탕으로 HotSpot VM에서는 크게 2가지 물리적 공간을 나누었다. 바로 **Young 영역**과 **Old 영역**이다.

![](https://velog.velcdn.com/images/rlooo/post/bce7f214-1d81-4209-83f7-724991072730/image.PNG)




- **Young (Yong Generation) 영역**

  - 새롭게 생성한 객체의 대부분이 여기에 위치한다. 
  - 대부분의 객체가 금방 접근 불가능 상태가 되기 때문에 매우 많은 객체가 Young 영역에 생성되었다가 사라진다. 
  - 이 영역에서 객체가 사라질때 **Minor GC**가 발생한다고 말한다.

  <br>

- **Old(Old Generation) 영역**

  - 접근 불가능 상태로 되지 않아 Young 영역에서 살아남은 객체가 여기로 복사된다. 
  - 대부분 Young 영역보다 크게 할당하며, 크기가 큰 만큼 Young 영역보다 GC는 적게 발생한다. 
  - 이 영역에서 객체가 사라질 때 **Major GC(혹은 Full GC)**가 발생한다고 말한다.

  <br>

  **GC 영역 및 데이터 흐름도**

  <br>

![](https://velog.velcdn.com/images/rlooo/post/face384e-783f-47a7-a105-1e92a5b5ff55/image.png)


<br>

- Permanent Generation(이하 Perm) 영역
  -  객체나 억류(intern)된 문자열 정보를 저장하는 곳이다.
  - Old 영역에서 살아남은 객체가 영원히 남아 있는 곳은 아니다.
  - 이 영역에서 GC가 발생할 수도 있는데, 여기서 GC가 발생해도 Major GC의 횟수에 포함된다.

<br>

---

<br>

## Young 영역의 구성

<br>

Young 영역은 3개의 영역으로 나뉜다.

- Eden 영역
- Survivor 영역(2개)

<br>

각 영역의 처리 절차를 순서에 따라 기술하면 다음과 같다.

1) 새로 생성한 대부분의 객체는 Eden 영역에 위치한다.
2) Eden 영역에서 GC가 한 번 발생한 후 살아남은 객체는 Survivor 영역 중 하나로 이동된다.
3) Eden 영역에서 GC가 발생하면 이미 살아남은 객체가 존재하는 Survivor 영역으로 객체가 계속 쌓인다.
4) 하나의 Survivor 영역이 가득 차게 되면 그 중에서 살아남은 객체를 다른 Survivor 영역으로 이동한다. 그리고 가득 찬 Survivor 영역은 아무 데이터도 없는 상태로 된다.
5) 이 과정을 반복하다가 계속해서 살아남아 있는 객체는 Old 영역으로 이동하게 된다.

<br>

다음 그림은 Minor GC를 통해서 Old 영역까지 데이터가 쌓인 것을 나타낸 것이다.

![](https://velog.velcdn.com/images/rlooo/post/7cd0c669-782c-4f96-b0de-b85ddda3afdb/image.png)


<br>

- 이를 보면 Survivor 영역 중 하나는 반드시 비어 있는 상태로 남아 있어야 한다. 
- 만약 두 Survivor 영역에 모두 데이터가 존재하거나, 두 영역 모두 사용량이 0이라면 시스템은 정상적인 상황이 아니라고 생각하면 된다.

<br>

---

<br>

## Old 영역에 대한 GC

Old 영역은 기본적으로 데이터가 가득 차면 GC를 실행한다. 

<br>

---

<br>

## GC 방식

GC 알고리즘은 모두 설정을 통해 Java에 적용할 수 있다. 따라서 개발자는 상황에 따라 필요한 GC 방식을 설정해서 사용할 수 있다.

<br>

GC 방식에 따라서 처리 절차가 달라진다. GC 방식은  5가지가 있다.

- Serial GC
- Parallel GC
- Parallel Old GC(Parallel Compacting GC)
- Concurrent Mark & Sweep GC(이하 CMS)
- G1(Garbage First) GC

<br>

**Serial GC (-XX:+UseSerialGC)**

운영서버에서 절대 사용하면 안되는 방식이다. Serial GC는 데스크톱의 <u>CPU 코어가 하나만 있을 때 사용하기 위해서 만든 방식이다.</u> GC를 처리하는 쓰레드가 1개이기 때문에 Serial GC를 사용하면 애플리케이션의 성능이 많이 떨어진다. 따라서 stop-the-world 시간이 가장 길다.

- mark-sweep-compact 알고리즘을 사용한다.
  1. Old 영역에 살아 있는 객체를 식별(mark)한다.
  2. Old의 앞 부분부터 확인하여 살아 있는 것만 남긴다. (sweep)
  3. 각 객체들이 연속되게 쌓이도록 힙의 가장 앞 부분부터 채워서 객체가 존재하는 부분과 객체가 없는 부분으로 나눈다.(Compaction).

Serial GC는 적은 메모리와 CPU 코어 개수가 적을 때 적합한 방식이다.

![](https://velog.velcdn.com/images/rlooo/post/d8a9862b-58e0-445f-96b5-e838e9f13e50/image.PNG)


<br>

**Parallel GC (-XX:+UseParallelGC)**

Parallel GC는 Serial GC와 기본적인 알고리즘은 같다. <u>Young 영역의 Minor GC를 멀티 쓰레드로 수행한다. (Old 영역은 여전히 싱글 쓰레드이다.)</u>

그러나 Serial GC는 GC를 처리하는 스레드가 하나인 것에 비해, <u>Parallel GC는 GC를 처리하는 쓰레드가 여러 개이다.</u> 

&rarr;따라서 Serial GC보다 빠른게 객체를 처리할 수 있다. 

Parallel GC는 메모리가 충분하고 코어의 개수가 많을 때 유리하다. 

Parallel GC는 Throughput GC라고도 부른다.

<br>

다음은 Serial GC와 Parallel GC의 스레드를 비교한 그림이다.

![](https://velog.velcdn.com/images/rlooo/post/1d46e96b-a144-4304-a772-92fcb42584b5/image.png)


<br>

**Parallel Old GC(-XX:+UseParallelOldGC)**

Parallel GC를 개선한 버전이다. Parallel GC와 비교하여 Old 영역의 GC 알고리즘만 다르다. <u>Young 영역 뿐만 아니라, Old 영역에서도 멀티 쓰레드로 GC를 수행한다.</u>

- 이 방식은 mark-summary-compaction 알고리즘을 사용한다. 
- <u>Summary 단계는 앞서 GC를 수행한 영역에 대해서 별도로 살아 있는 객체를 식별한다</u>는 점에서 Mark-Sweep-Compaction 알고리즘의 Sweep 단계와 다르다.
-  **Sweep은 단일 스레드**가 Old 영역 전체를 훑어 살아있는 객체만 찾아내는 방식이지만, **Summary는 여러 스레드**가 Old 영역을 분리하여 훑는다. 

<br>

![](https://velog.velcdn.com/images/rlooo/post/81364b06-68d0-4dd9-b644-1b45ebe358e1/image.PNG)




<br>

**CMS GC (-XX:+UseConcMarkSweepGC)**

어플리케이션의 쓰레드와 GC 쓰레드가 동시에 실행되어 stop-the-world 시간을 최대한 줄이기 위해 고안된 GC이다.

<br>

단, GC 과정이 매우 복잡해진다.

1. Initial Mark 단계 : 클래스 로더에서 가장 가까운 객체 중 살아 있는 객체만 찾는다.
2.  Concurrent Mark 단계에서는 방금 살아있다고 확인한 객체에서 참조하고 있는 객체들을 따라가면서 확인한다. 
   - 이 단계의 특징은 다른 스레드가 실행 중인 상태에서 동시에 진행된다는 것이다.
3.  Remark 단계에서는 Concurrent Mark 단계에서 새로 추가되거나 참조가 끊긴 객체를 확인한다.
4. Concurrent Sweep 단계에서는 쓰레기를 정리하는 작업을 실행한다. 
   - 이 작업도 다른 스레드가 실행되고 있는 상황에서 진행한다.

<br>

-  stop-the-world 시간이 매우 짧다. 
- 모든 애플리케이션의 응답 속도가 매우 중요할 때 CMS GC를 사용하며, Low Latency GC라고도 부른다.

<br>

**CMS GC의 단점**

- 다른 GC 방식보다 메모리와 CPU를 더 많이 사용한다.
- Compaction 단계가 기본적으로 제공되지 않는다.
  - 조각난 메모리가 많아 Compaction 작업을 실행하면 다른 GC 방식의 stop-the-world 시간보다 stop-the-world 시간이 더 길기 때문이다.

결국 CMS GC는 Java9 버젼부터 deprecated 되었고 결국 Java14에서는 사용이 중지되었다.

![](https://velog.velcdn.com/images/rlooo/post/3d9652a2-6b43-4b50-9135-6ddbe2211fd2/image.PNG)


<br>

**G1(Garbage First) GC**

CMS GC를 대체하기 위해 만들어졌다. Java 9+ 버전의 디폴트 GC로 지정되었다.

<br>

기존의 GC 알고리즘에서는 Heap 영역을 물리적으로 고정된 Young / Old 영역으로 나누어 사용하였지만,
G1 GC는 아예 이러한 개념을 뒤엎는 <u>Region</u>이라는 개념을 새로 도입하여 사용한다. <u>전체 Heap 영역을 Region이라는 영역으로 체스같이 분할하여 상황에 따라 Eden, Survivor, Old 등 역할을 고정이 아닌 동적으로 부여하여 사용한다.</u>

![](https://velog.velcdn.com/images/rlooo/post/4d4c319a-f8f9-4fe8-ba23-12abaee6df2f/image.PNG)




<br>

G1 GC는 바둑판의 각 영역에 객체를 할당하고 GC를 실행한다. 해당 영역이 꽉 차면 다른 영역에서 객체를 할당하고 GC를 실행한다. 

&rarr; 즉, 지금까지 설명한 Young의 세가지 영역에서 데이터가 Old 영역으로 이동하는 단계가 사라진 GC 방식이라고 이해하면 된다. 

<br>

![](https://velog.velcdn.com/images/rlooo/post/1dc094c0-e18e-4988-9321-d59922d994a3/image.png)


<br>

**G1 GC의 효율성**

이전의 GC들처럼 일일히 메모리를 탐색해 객체들을 제거하지 않는다. 메모리가 많이 차있는 영역(region)을 인식하는 기능을 통해 메모리가 많이 차있는 영역을 우선적으로 GC한다. 

&rarr; 즉, G1 GC는 Heap Memory 전체를 탐색하는 것이 아닌 영역(region)을 나눠 탐색하고 영역(region)별로 GC가 일어난다.

또한 이전의 GC 들은 Young Generation에 있는 객체들이 GC가 돌때마다 살아남으면 Eden → Survivor0 → Survivor1으로 순차적으로 이동했지만, <u>G1 GC에서는 순차적으로 이동하지는 않는다.</u>

대신 G1 GC는 더욱 <u>효율적이라고 생각하는 위치로 객체를 Reallocate(재할당) 시킨다.</u>
ex. Survivor1 영역에 있는 객체가 Eden 영역으로 할당하는 것이 더 효율적이라고 판단될 경우 Eden 영역으로 이동시킨다.

<br>

---

<br>

## REFERENCE

- https://d2.naver.com/helloworld/1329

- https://inpa.tistory.com/entry/JAVA-%E2%98%95-%EA%B0%80%EB%B9%84%EC%A7%80-%EC%BB%AC%EB%A0%89%EC%85%98GC-%EB%8F%99%EC%9E%91-%EC%9B%90%EB%A6%AC-%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC
