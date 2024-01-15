# let, const Keyword and Block Level Scope

## Summary

- ES6+ 에서는 `var` 대신 `let`, `const` 변수를 사용한다.
- `let`, `const`도 호이스팅이 발생하지만, 선언과 초기화 시점이 분리되어 **호이스팅이 발생하지 않는 것 처럼** 보인다.
- `let`, `const`는 block level scope를 인정하고 중복 선언을 허용하지 않으므로 의도치 않은 재할당으로부터 안전하다.
- `const`는 선언과 동시에 초기화되어야 한다.

## var의 문제점

- 중복 선언이 허용되므로 의도하지 않게 변수의 값이 변경될 수 있다.
  ```js
  var x = 1;
  var y = 1;
  var x = 100; // 중복 선언할 때 사용한 `var`는 무시된다.
  console.log(x); // 100
  var y; // 초기화문이 없는 중복 선언문은 무시된다.
  console.log(y); // 1
  ```
- 함수 레벨 스코프(function level scop)만을 지역 스코프(local scope)로 인정하므로 의도치 않은 중복 선언이 발생할 수 있다.
  ```js
  var i = 10;
  // 선언식의 var가 무시되며 전역 변수 i에 재할당됨
  for (var i = 0; i < 5; i++) {
    console.log(i); // 0, 1, 2, 3, 4
  }
  console.log(i); // 10 -> 5로 의도치 않게 값이 변경됨
  ```
- 변수 호이스팅으로 인해 가독성이 떨어지고 오류가 발생할 가능성을 높인다.
  ```js
  console.log(x); // undefined
  x = 10;
  console.log(x); // 10
  var x;
  ```

## let 변수

- ES6에서 `var` 키워드의 단점을 보완하기 위해 추가
- 변수 중복 선언 금지
- 블록 레벨 스코프(block level scope) 사용
- 변수 호이스팅
  - `var` 키워드는 런타임 이전에 선언과 동시에 `undefined`로 **초기화**됨
  - `let` 키워드는 런타임 이전에 선언되지만 런타임에 **선언문이 실행될 때 초기화**됨
  - 즉, `var` 변수는 런타임 이전에 선언과 초기화가 동시에 일어나지만, `let` 변수는 **선언과 초기화가 분리**되어 진행된다.
  - 결과적으로 **호이스팅이 발생하지 않는 것 처럼 동작한다.**
    ```js
    console.log(foo); // ReferenceError: foo is not defined
    let foo;
    console.log(foo); // undefined
    foo = 1;
    console.log(foo); // 1
    ```
    - Scope 시작 지점부터 초기화 시작 지점까지는 `let` 변수를 참조할 수 없다. => **일시적 사각지대(Temporal Dead Zone: TDZ)**
    - 호이스팅은 발생했지만 초기화는 되지 않았으므로 `ReferenceError`가 발생한다.
    - 변수 선언문 이후부터는 변수를 참조해도 error가 발생하지 않는다. (초기화문이 없더라도 `undefined`로 초기화 될 것이므로 error가 없다.)
  - ES6+ 에서 사용 가능한 `let`, `const`, `class`는 변수 호이스팅이 발생하지 않는 것 처럼 동작한다. (실제로는 호이스팅이 일어나지만)

## const 변수 (== 상수)

- `const` 키워드는 상수를 선언하기 위해 사용한다.
  - 재할당이 금지된다. -> 쉽게 바뀌지 않는 값, 고정된 값에 사용
  - 원시 값을 할당한 경우, 변수 값을 변경할 수 없다. (immutable value)
  - 객체를 할당한 경우, 재할당은 불가능하지만 **변수 값 자체는 변경할 수 있다.** (mutable value 이므로)
- `const` 변수는 **선언과 동시에 초기화 되어야 한다.**
- Block level scope를 사용한다.
- 변수 호이스팅이 **발생하지 않는 것처럼** 동작한다.
- 상수 이름은 대문자를 사용한 스네이크 케이스로 만든다.

## var, let, const 변수 사용 권장

- ES6+ 에서는 `var` 변수를 사용하지 않는다.
- 재할당이 필요할 때만 `let` 변수를 사용한다.
- 일반적인 상황에서는 `const` 변수를 사용한다.
