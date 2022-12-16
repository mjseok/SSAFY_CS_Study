[ReactiveX](https://reactivex.io/intro.html)

> 예시는 자바를 중심으로 설명되어 있습니다.
> 

ReactiveX는 observable sequences를 사용해서 비동기 및 이벤트 기반 프로그램을 구성하기 위한 라이브러리입니다.

Observer pattern을 확장해서 데이터나 이벤트의 시퀀스를 지원하고, 명시적으로 시퀀스를 구성할 수 있도록 연산자를 제공합니다. (low-level threading, synchronization, thread-safety, concurrent data structures, non-blocking I/O 등에 대한 우려를 추상화할 수 있습니다.)

**Observables**은 여러 종류의 비동기 시퀀스에 접근하는 이상적인 방법으로써, 다양한 접근 방법 차이를 줄일 수 있습니다.

|  | single items | multiple items |
| --- | --- | --- |
| synchronous | T getData() | Iterable<T> getData() |
| asynchronous | Future<T> getData() | Observable<T> getData() |

가끔 ReactiveX를 “Functional Reactive Programming”으로 부르는 경우가 있습니다. ReactiveX도 물론 functional하고 반응형일 수 있지만 “Functional Reactive Programming”은 완전히 다른 종류입니다.

[Function Reactive Programming](https://github.com/conal/talk-2015-essence-and-origins-of-frp)은 시간이 지남에 따라 **지속적으로** 변하는 값에 대해서 작동하는 반면,
ReactiveX는 시간이 지남에 따라 발생하는 **결과값(이산값)**에 대해 작동합니다. 

## Why Use Observables?

ReactiveX Observable 모델을 사용하면 Array와 같은 컬렉션 데이터를 다루는 것처럼 간단하게 비동기 이벤트를 처리할 수 있습니다. 콜백 지옥에서 벗어나 가독성이 향상되고 버그가 덜 발생할 것입니다.

### Observables Are Composable

Java Futures와 같은 기술은 single level의 비동기 실행에서는 직관적이지만 중첩되면 복잡해지기 시작합니다. 

Futures를 사용해서 조건부 비동기 실행 흐름을 최적으로 구성하는 것은 어렵습니다(또는 각 요청에 대한 대기 시간이 런타임에 따라 다르기 때문에 불가능합니다). 물론 이 작업을 수행할 수는 있지만, 빠르게 복잡해지거나 오류가 발생하기 쉬워집니다. 또는 `Future.get()` 을 조기에 차단해 비동기 실행의 이점을 잃어버릴 수 있습니다.

반면, ReactiveX Observables는 비동기 데이터의 흐름과 시퀀스를 구성할 수 있습니다.

### Observables Are Flexible

ReactiveX Observables는 단일 스칼라 값의 발생뿐만 아니라, 시퀀스 값이나 무한 스트림도 지원합니다. `Observable`은 이러한 use case에 사용할 수 있는 단일 추상화 객체입니다. Observable 객체는 Iterable 객체와 관련된 유연성을 모두 갖추고 있습니다. 

Observable은 Iterable의 [동기적 pull]에 대한 [비동기적 push] **쌍**입니다. 

`Asynchronous push (Observable)` ←→ `Synchronous pull (Iterable)`

| event | Iterable (pull) | Observable (push) |
| --- | --- | --- |
| retrieve data | T next() | onNext(T) |
| discover error | throws Exception | onError(Exception) |
| complete | !hasNext() | onCompleted() |

### Observables Are Less Opinionated

ReactiveX는 특정 동시성 또는 비동기성 소스에 치우쳐있지 않아, thread-pools, event loops, non-blocking I/O, actors (e.g. Akka) 등 사용자의 필요, 스타일, 전문성에 맞게 구현할 수 있습니다. Client code는 기본 구현이 blocking 방식이든 non-blocking 방식이든 상관 없이 Observables와의 모든 상호작용을 비동기적으로 처리합니다. 

**Observable을 구현하는 방법**

| pubilc Observable<data> getData(); |
| --- |
| call와 동일한 thread에서 동기적으로 작동하는지
별개의 thread에서 비동기적으로 작동하는지
어떤 순서로든 데이터를 caller에게 반환할 수 있는 여러 threads로 작업을 분할하는지
thread pool 대신 actor(또는 여러 actor)를 사용하는지
비동기 네트워크 접근을 위해 NIO와 함께 event-loop를 사용하는지
콜백 thread에서 작업 thread를 분리하기 위해 event-loop를 사용하는지 |

**옵저버의 관점에서는 중요하지 않습니다.**

ReactiveX를 사용하면 Observable의 consumer를 손상시키지 않으면서 Observable의 기본 특성을 근본적으로 변경할 수 있습니다. 

### Callbacks Have Their Own Problems

Callback은 아무것도 block하지 않음으로써 `Future.get()`의 조기 blocking 문제를 해결합니다. 응답이 준비됐을 때 실행하기 때문에 자연스럽게 효율성이 높아집니다.

하지만 Futures와 마찬가지로 single level의 비동기 실행에서는 사용하기 쉽지만, 중첩되면 다루기 힘들어집니다.

### ReactiveX Is a Polyglot Implementation

ReactiveX는 현재 다양한 언어로 구현되어 있으며, 더 많은 언어들도 빠른 속도로 추가되고 있습니다.

## Reactive Programming

ReactiveX는 Observable을 filter, select, transform, combine, compose 등을 수행할 수 있는 연산자 집합을 제공합니다. 이를 통해 효율적인 구성 및 실행이 가능합니다.

Observable class의 “push”를 Iterable class의 “pull”과 동일하게 생각할 수 있습니다. Iterable의 consumer는 값이 도착할 때까지 producer와 thread block에서 값을 pull합니다. 대조적으로, Observable의 producer는 값을 사용할 수 있을 때마다 consumer에게 값을 push합니다. 어떤 값들이 동기적으로, 또는 비동기적으로 도착할 수 있기 때문에 이러한 방식이 더 유연합니다.

**Example code**

| Iterable | Observable |
| --- | --- |
| getDataFromLocalMemory()
  .skip(10)
  .take(5)
  .map({ s -> return s + " transformed" })
  .forEach({ println "next => " + it }) | getDataFromNetwork()
  .skip(10)
  .take(5)
  .map({ s -> return s + " transformed" })
  .subscribe({ println "onNext => " + it }) |

Observable 타입을 Iterable 타입과 일치시키기 위해서 [the Gang of Four’s Observer pattern](http://en.wikipedia.org/wiki/Observer_pattern)에 두 가지 누락된 의미론을 추가합니다.

1. Producer는 더이상 사용할 수 있는 데이터가 없다는 신호를 consumer에게 보낼 수 있음
    1. Iterable의 foreach loop는 더이상 사용할 수 있는 데이터가 없는 경우 완료되고 정상적으로 반환됨
    2. Observable은 observer의 onCompleted 메서드를 호출)
2. Producer는 오류가 발생했다는 신호를 consumer에게 보낼 수 있음
    1. Iterable은 오류가 발생한 경우 예외를 발생시킴
    2. Observable은 observer의 onError 메서드를 호출

이러한 추가로, ReactiveX는 Iterable type과 Observable type을 조화시킵니다. 데이터가 흐르는 방향을 제외한 모든 부분을 일치시킴으로써 Iterable에서 수행할 수 있는 작업을 Observable에서도 수행할 수 있게 됩니다.

---

### 설명 추가 예정
