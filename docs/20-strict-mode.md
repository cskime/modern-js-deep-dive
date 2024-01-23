# Strict Mode

> 이제 strict mode는 거의 사용하지 않지만 개념만 이해하고 넘어간다.

- ES5에서 추가된 구문 (`'use strict';`)
- ES6에서 도입된 class와 module은 기본적으로 strict mode가 적용된다.
- ESLint를 사용하는게 더 유용하다.
- 오류를 발생시킬 확률이 높거나 javascript 엔진 최적화 작업에 문제를 일으킬 수 있는 코드에 대해 명시적인 error를 발생시킴
  - 선언하지 않은 변수를 참조하는 경우 `ReferenceError` -> 암묵적 전역(Implicit global) 방지
  - `delete` 연산자로 변수, 함수, 매개변수를 삭제하는 경우 `SyntaxError`
  - 중복된 매개변수 이름을 사용하는 경우 `Syntax Error`
  - `with`문을 사용하는 경우 `SyntaxError`
- Strict mode를 사용할 때 변화
  - 일반 함수에서 `this`가 `undefined`로 초기화된다.
  - 함수에서 매개변수에 다른 값을 재할당해도 `arguments` object에 반영되지 않는다.
- Strict mode는 script 단위로 적용된다.
- 전역에 strict mode를 적용하는 것을 피하자
- 함수 단위로 strict mode를 적용하는 것을 피하자
