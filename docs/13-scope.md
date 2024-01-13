# Scope

- Scope : 식별자의 유효 범위
- 모든 식별자는 **선언된 위치**에 의해 scope가 결정된다.
- 식별자 결정(identifier resolution)
  - 같은 이름의 식별자 두 개 이상의 scope가 충돌할 때, Javascript engine이 어떤 식별자를 참조할 것인지 결정하는 것
  - Scope가 다르면 이미 선언된 이름으로 다른 식별자를 선언할 수 있다. => namespace처럼 동작

## Summary

- `var` keyword로 선언된 변수는 function level scope를 따르므로 code block에서 의도하지 않게 재할당 될 수 있다.
- ES6+에서 사용할 수 있는 `let`, `const`는 block level scope를 따르기 때문에 명확한 scope로 동작하여 비교적 안전하다.
- Javascript의 변수와 함수는 모두 식별자에 저장되므로, 같은 이름의 식별자가 경합할 때 scope chain을 따라 상위 scope로 올라가며 식별자를 검색한다.

## Scope 종류

- 전역(global) : 코드의 가장 바깥 영역
  - Global scope : 코드 전체 범위
  - Global variable : 가장 바깥 영역에 선언된 변수
- 지역(local) : 함수 내부
  - Local scope : 함수 block 범위
  - Local scope : 함수 안에서 선언된 변수
- **변수는 자신의 scope 및 하위 scope에서만 유효하다.**

## Scope chain

- Scope는 함수 중첩에 의해 **계층 구조**를 갖는다.
- Scope chain : 모든 scope들이 서로 상위/하위 scope 관계를 형성하며 **계층적으로** 연결된 것
- 변수 검색은 **하위에서 상위 scope 방향**으로 이동하며 이루어진다.
  - Global scope <- Outer local scope <- Inner local scope
  - Global scope는 모든 scope의 최상위 scope이다.
  - 변수 검색은 하위 scope 방향으로 일어나지 않는다. => **상위 scope에서 하위 scope에 선언된 변수를 참조할 수 없다.**
- 함수도 scope를 갖는다.
  - 함수 선언문은 런타임 이전에 평가되어 함수 객체를 생성하고, 암묵적으로 생성된 식별자에 함수 객체의 참조가 할당된다.
  - 즉, **scope는 식별자를 검색하는 규칙**이다.

## Function level scope

- Block level scope : 함수를 비롯한 모든 code block이 local scope를 만드는 특성
- Function level scope : 함수 code block에서만 local scope를 만드는 특성 => **`var` 키워드로 선언한 변수**
  ```js
  var x = 1; // A
  if (true) {
    var x = 10; // B
  }
  console.log(x); // 10
  ```
  - `var` 키워드로 전역 변수 `x`가 선언되었다.
  - `if` code block 안에서 선언된 `x`는 scope를 갖지 않는다.
  - 이 때, js engine에 의해 B의 `var` 키워드는 무시되고 **전역 변수의 재할당**이 일어난다.
- ES6+ 에서 지원되는 `let`, `const`는 block level scope를 지원한다.
  - Function level scope에서는 의도하지 않은 재할당이 발생하므로, 변수 선언 시 `let`과 `const`를 사용하는게 좋겠다.

## Lexical scope

- 함수의 상위 scope가 결정되는 방식
  - Dynamic scope : 함수가 **호출된 위치**에 의해 결정
  - Static scope(Lexical scope) : 함수가 **정의된 위치**에 의해 결정
- Javascript는 lexical scope를 따르므로, **js 함수의 상위 scope는 항상 자신이 정의된 scope이다.**
- 함수 선언문 또는 함수 표현식이 실행되어 생성된 함수 객체는 상위 scope를 기억했다가 필요할 때 참조한다.
- Lexical scope 예시

  ```javascript
  var x = 1;

  function foo() {
    var x = 10;
    bar();
  }

  function bar() {
    console.log(x);
  }

  foo(); // A
  bar(); // B
  ```

  - `bar`가 선언된 위치는 전역 scope 이므로 상위 scope가 전역 scope다.
  - 함수 선언문 `foo`와 `bar`는 전역 코드가 실행되기 전에 평가되어 함수 객체가 생성된다.
  - 함수 `foo`와 `bar`는 호출될 때 함수 객체에 저장된 상위 scope를 사용한다.
  - A가 실행되면 `1`이 출력된다.
    - `foo` 안에서 선언한 변수 `x`는 local variable이므로, 전역 변수 `x`와 별개로 선언된다.
    - 따라서, 이어 실행되는 `bar()`는 전역 변수 `x`를 참조하여 `1`을 출력한다.
  - B가 실행되면 `1`이 출력된다.
    - `bar`는 전역 scope이므로 전역 변수 `x`를 참조하여 `1`이 출력된다.
    - 이 때, 위에서 `foo`가 실행되었지만 `foo` 안에 있는 `x`는 local variable이므로 전역 변수 `x`에 재할당되지 않았다.
