# CSS 애니메이션 vs JS 애니메이션

### CSS 애니메이션

- HTML 요소에 적용되는 CSS 스타일을 다른 CSS 스타일로 부드럽게 변화시킨다.
- 특징
  - 외부 라이브러리를 필요로 하지 않는다.
  - 메인 쓰레드가 아닌 별도의 Compositor Thread에서 그려지기 때문에 메인 쓰레드에서 작업하는 JS보다 효율적이다. [자세히](https://d2.naver.com/helloworld/5237120)
- 트랜지션(transition)

  - CSS 프로퍼티의 값이 변화할 때, 프로퍼티 값의 변화가 일정 시간에 걸쳐 일어나도록 하는 것이다.
  - CSS 애니메이션보다는 제한적이다. 트랜지션은 프로퍼티값이 다른 값으로 변화할 때 주로 사용하며, 요소의 로드와 함께 자동으로 발동되지 않는다. 가상 클래스 선택자(Pseudo-Class Selector) 또는 자바스크립트의 이벤트와 같은 부수적 액션에 의해 발동된다.
  - 트랜지션 가능한 CSS 프로퍼티 종류
    | 종류 | 프로퍼티 |
    | ---------- | ----------------------------------------------------------------------------------------------------------------------- |
    | Box model | width height, max-width, max-height, min-width, min-height, padding, margin, border-color, border-width, border-spacing |
    | Background | background-color, background-position |
    | Position | top, left, right, bottom |
    | Text | color font-size, font-weight, letter-spacing, line-heighttext-indent, text-shadow, vertical-align, word-spacing |
    | ETC | opacity, outline-color, outline-offset, outline-width, visibility, z-index |

  - 코드 예시

  ```css
  #animation-el {
    position: absolute;
    left: 0px;
    transition-property: left;
    transition-duration: 3s;
  }

  #animation-el:hover {
    left: 100px;
  }
  ```

- 키프레임(@keyframes)

  - 애니메이션 중간중간의 특정 지점들을 거칠 수 있는 키프레임들을 설정함으로써 CSS 애니메이션 과정의 중간 절차를 제어할 수 있게 한다.
  - 브라우저가 자동으로 애니메이션 처리하는 것보다 세밀하게 중간 동작을 제어할 수 있다.
  - 코드 예시

  ```css
  @keyframes animationName {
    from {
      left: 0px;
    }
    to {
      left: 100px;
    }
  }

  @keyframes animationName2 {
    0% {
      left: 0px;
    }
    50% {
      left: 30px;
    }
    100% {
      left: 100px;
    }
  }

  #animation-el {
    position: absolute;
    animation-name: animationName;
    /*animation-name: animationName2;*/
    animation-duration: 3s;
    animation-iteration-count: infinite;
  }
  ```

#

### JS 애니메이션

- CSS로 처리하기에는 훨씬 복잡하고 무거운 애니메이션 작업들을 효율적이고, 세밀하게 다루기 위해 사용한다.
- 보통 setInterval을 이용하여 시간 단위로 조작하여 애니메이션을 구현한다.
- 특징
  - 브라우저 호환성이 좋아 크로스 브라우징 측면에서 유리하다.
  - GPU를 통한 하드웨어 가속을 제어할 수 있다. CSS 애니메이션의 경우 특정 속성에 의한 GPU가속이 됨으로서 저사양의 컴퓨팅인 경우에 성능 하락을 발생 시킬 수 있으나 이를 막을 수 있다.
- 코드 예시
  ```css
  #animation-el {
    position: absolute;
    left: 0px;
  }
  ```
  ```jsx
  let el = document.querySelector("#animation-el");
  setInterval(function () {
    el.style.left += 1;
  }, 30);
  ```

#

### CSS 애니메이션 VS JS 애니메이션

- CSS 애니메이션의 장점
  - 간편하다.
  - CSS 애니메이션은 JavaScript 기반 애니메이션 실행과 비교하여 나은 렌더링 성능이 제공된다.(frame-skipping)
  - 브라우저는 애니메이션의 성능을 효율적으로 최적화 할 수 있다.
- JS 애니메이션의 장점
  - 세밀한 제어가 가능하다.
  - 브라우저 호환성이 좋다.
- 무엇을 사용하는 것이 좋은가?
  - 비교적 작은 효과나 CSS만으로도 충분한 효과를 볼 수 있는 것은 CSS를 사용한다.
  - 세밀한 제어와 크로스 브라우징을 위해서는 자바스크립트 애니메이션 사용이 좋다.
- 애니메이션 라이브러리
  - JS 기반 애니메이션: [anime](https://yamoo9.github.io/anime/)
  - CSS 기반 애니메이션: [animate.style](https://animate.style/)
