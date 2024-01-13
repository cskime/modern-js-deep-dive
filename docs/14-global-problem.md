# Problem of The Global Variable

## Summary

- Global variable의 life cycle은 프로그램의 life cycle과 같다.
  - 브라우저 : 웹페이지 == 전역 객체
  - node.js : node.js 프로그램
- Local variable의 life cycle은 함수 life cycle과 같다.
  - **클로저**는 함수 life cycle보다 오래 유지될 수 있다.
- Hoisting이 일어나는 시점은 **scope 실행 전**이다. (항상 가장 먼저 일어나는 것은 아니다. => global scope 한정)
- Global variable은 어디서든 접근하고 값을 변경할 수 있어 의도하지 않은 문제가 발생할 수 있으므로, 특별한 이유가 없다면 local variable을 사용한다.

## 변수의 생명 주기

- Life cycle : 변수가 선언되고 메모리에서 해제될 때 까지의 주기
- 변수의 life cycle은 변수가 선언된 scope의 life cycle과 같다.

### 지역 변수의 생명 주기

- 지역 변수의 life cycle은 함수의 life cycle과 같다.
- 호이스팅과 scope
  - 식별자 선언문이 런타임 이전에 가장 먼저 실행됨 => **global scope** 한정 설명
  - Local scope에서는 **함수가 실행되기 이전에** 지역 변수 선언문이 먼저 실행되고 `undeinfed`로 초기화된다.
  - 즉, **호이스팅은 scope 단위로 동작한다.** => 변수 선언이 **scope의 선두**로 끌어 올려진 것처럼 동작하는 특성
- 지역 변수가 함수보다 오래 생존할 수도 있다.
  - 변수는 scope가 메모리에서 해제될 때 까지 유효하다.
  - 할당된 메모리 공간은 더 이상 참조되지 않을 때 가비지 컬렉터에 의해 메모리에서 해제된다.
  - 즉, 누군가 scope를 참조하고 있다면 scope는 소멸하지 않을 수 있다. => **클로저 특성**
  - 함수가 생성한 scope가 다른 곳에서 참조되고 있다면, 함수가 종료되어도 scope는 살아있다.

### 전역 변수의 생명 주기

- 전역 변수의 life cycle은 프로그램의 life cycle과 같다.
- `var` 키워드로 선언한 전역 변수는 전역 객체의 프로퍼티가 된다.
- `var` 키워드로 선언한 전역 변수의 life cycle은 전역 객체의 life cycle과 같다.
  - 브라우저에서 전역 객체는 웹페이지를 닫기 전 까지 유효하다.
  - 즉, `var` 전역 변수는 웹페이지 life cycle과 같다.
- 전역 객체 : javascript engine이 가장 먼저 생성하는 객체
  - 브라우저 : `window`
  - node.js : `global`
  - ES11+ : `globalThis`

## 전역 변수의 문제

1. 암묵적 결합(implicit coupling) : 모든 코드가 전역 변수를 참조하고 변경할 수 있음
2. 긴 생명 주기 : 오랫동안 메모리를 점유하며, `var` 키워드의 중복 선언에 의한 재할당 등 의도치 않은 상태 변경이 일어날 수 있다.
3. Scope chain의 가장 최상단에 존재하므로 식별자 검색 시간이 가장 느리다.
4. 서로 다른 파일들도 하나의 global scope를 공유하므로 같은 이름으로 선언된 전역 변수나 전역 함수가 문제를 일으킬 수 있다.

## 전역 변수 사용 억제

- 특별한 이유가 없다면 항상 지역 변수를 사용한다.
- 모든 코드를 즉시 실행 함수(`()`)로 만들어서 지역 변수로 바꾼다. => 라이브러리에서 많이 사용하는 방법
  ```javascript
  (function () {
    var foo = 10;
    // ...
  })();
  console.log(foo); // ReferenceError: foo is not defined
  ```
- namespace 객체를 생성한다. => 전역 객체를 만드는 것이므로 큰 차이가 없다.
- 모듈 패턴(Module pattern)

  - 관련된 변수와 함수를 즉시 실행 함수로 감싸서 하나의 모듈을 만든다.
  - **클로저**를 사용해서 전역 변수를 억제한다.
  - 모듈 패턴을 적용하면 캡슐화, 은닉화를 구현할 수 있다.
  - Javascript는 접근 제한자가 없지만, 접근 제한을 구현할 수 있다.
  - 구현 예시

    ```javascript
    var Counter = (function () {
        // private
        var num = 0;

        // public : return object에 넣는 propert나 method만 public access가 된다.
        return {
            increase() {
                return ++num;
            }
            decrease() {
                return --num;
            }
        }
    })
    ```

- ES6 모듈
  - ES6+ 에서 파일 자체적인 모듈 scope를 제공한다.
  - `var` 키워드로 선언한 전역 변수도 전역 객체에 포함되지 않는다.
  - 모듈로 사용하는 파일 확장자를 `.mjs`로 만들고, `<script>`에 `type="module"` attribute를 추가한다.
    ```html
    <script type="module" src="lib.mjs"></script>
    ```
  - Transfiling이나 bundling이 추가로 필요하기 때문에, 실제로는 webpack 등 모듈 번들러를 더 많이 사용한다.
