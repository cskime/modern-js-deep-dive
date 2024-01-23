# 05. Expression and Statement

- 평가(evaluate) : 값을 생성하거나 참조하는 것
- 문(statement) : 프로그램의 최소 실행 단위
- 표현식(expression) : Javascript engine에 의해 평가되어 값을 생성하거나 참조할 수 있는 문
- 값(value) : 표현식이 평가된 결과

## 값(value)

- 표현식(expression)이 평가(evaluate)되어 생성된 결과
- 평가(evaluate) : 식을 해석해서 값을 생성하거나 참조하는 것
- `10 + 20` 이라는 표현식은 평가되어 `30`이라는 결과를 생성한다.

## 리터럴(literal)

- 사람이 이해할 수 있는 문자 또는 약속된 기호를 사용해 값을 생성하는 표기법
- 사람이 이해할 수 있는 문자 : 아라비아 숫자(`3`), 알파벳, `null`, `undefined` 등
- 약속된 기호를 사용하는 문자 : 객체, 배열, 함수 등 (`[]`, `{}`, `//`, `""`)

## 표현식(expression)

- 값으로 평가될 수 있는 문(statement)
  - "값으로 평가된다" = 새로운 값을 생성하거나 기존 값을 참조한다.
    - 새로운 값 생성 : `50 + 50;`
    - 기존 값 참조 : `score;`
- Literal은 javascript engine에 의해 평가되어 값을 생성하므로 그 자체로 표현식이다.
- 값이 위치할 수 있는 자리에는 표현식도 위치할 수 있다.

  ```javascript
  var x = 1 + 2;
  x + 3; // x는 3으로 평가되기 때문에 피연산자 자리에 올 수 있다.
  ```

## 문(statement)

- 프로그램을 구성하는 최소 실행 단위
- 토큰(token) : 문법적으로 더 이상 나눌 수 없는 코드의 기본 요소 (e.g. 키워드, 변수 이름, 값, 연산자, 함수 등)
- 프로그램에서 한 줄을 실행하는 것은 컴퓨터에게 명령을 내리는 것이므로, **문 == 명령문** 이다.
- 종류 : 선언문, 할당문, 조건문, 반복문

## 세미콜론

- `;` : Javascript에게 문의 종료 지점을 알려줌
- 코드 블럭(`{}`)은 세미콜론을 붙이지 않음
- Javascript engine은 세미콜론 자동 삽입 기능(ASI, Automatic Semicolon Insertion)을 지원하지만, 명시하는 것을 권장함

## 표현식과 문

- 변수 선언문은 값으로 평가될 수 없으므로 표현식이 아님
  ```javascript
  var x;
  ```
- 표현식인 문 : 값으로 평가될 수 있는 문

  ```javascript
  var x;
  x = 1 + 2; // 완전한 문이면서 그 자체로 표현식
  x = 10; // 완전한 문이면서 그 자체로 표현식 (literal은 표현식이다.)
  ```

- 표현식인 문은 값으로 평가될 수 있으므로 값처럼 사용될 수 있다.
  ```javascript
  var foo = (x = 100); // -> literal이 100이라는 값으로 평가되어 x에 저장되고, x는 평가되어 foo의 값으로 저장된다.
  console.log(foo); // 100;
  ```