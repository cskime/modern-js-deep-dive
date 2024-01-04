# 03. Variable

- 할당(assignment) : 변수에 값을 저장하는 것
- 참조(reference) : 변수의 값을 읽어들이는 것
- 변수 이름 또는 식별자 : 메모리 주소를 기억하여 식별할 수 있는 이름

## Declaration

- 데이터를 저장하기 위한 메모리 공간 확보
- 메모리 주소와 변수 이름 연결
- 확보된 메모리 공간은 해제할 때 까지 다른 곳에서 사용할 수 없음
- 변수 선언 키워드
  - `var`
  - `let` (ES6+)
  - `const` (ES6+)
  - `var`의 문제점을 해결하기 위해 나온 것이 `let`과 `const`
- `undefined`
  - Javascript의 원시 타입 값
  - 변수를 선언한 직후 암묵적으로 초기화된 값
  - 메모리 공간을 할당했을 때, 이전에 저장된 쓰레기 값이 사용되는 문제를 방지하기 위해 선언과 동시에 `undefined`로 초기화
- 실행 컨텍스트(execution context)
  - Javascript의 변수 이름을 비롯한 모든 식별자들이 저장되는 곳
  - Javascript engine이 소스코드를 평가하고 실행하기 위해 필요한 환경을 제공하고 실행 결과를 관리하는 영역
  - 변수의 이름과 값은 key/value 형식의 객체로 등록되어 관리됨
  - Javascript는 실행 컨텍스트를 통해 등록된 변수들을 인식함

## 호이스팅 (Hoisting)

- Javascript 변수를 참조하려면 반드시 같은 이름으로 변수를 선언해야 한다. 선언하지 않은 변수를 참조하면 `ReferenceError`를 반환한다.
  ```javascript
  console.log(score); // ReferenceError: score is not defined
  ```
- 하지만 변수를 참조한 뒤에 선언하면 `ReferenceError`가 아닌 `undefined`가 반환되는데, 이것은 **변수가 선언되는 시점이 runtime이 아니기 때문이다.**
  ```javascript
  console.log(score); // undefined
  var score;
  ```
  - Javascript engine은 소스코드를 실행하기 전에 평가 과정을 거친다.
  - 평가 과정에서 변수를 포함한 모든 선언문을 소스코드에서 찾아 먼저 실행한다.
  - 소스코드 평가가 끝난 뒤 모든 선언문을 제외한 코드를 실행한다.
- 즉, **javascript에서 선언문은 가장 먼저 실행되므로 선언되는 위치와 상관 없이 참조할 수 있다.**
- 변수 선언문이 코드의 선두로 끌어 올려진 것처럼 동작하는 특징을 **변수 호이스팅(variable hoisting)**이라고 한다.

## 값 할당 (Assignment)

- Javascript는 변수의 선언과 할당을 한 번에 하더라도 2개의 구문으로 나누어 실행한다.

  ```javascript
  // 선언과 할당 분리
  var score;
  score = 80;

  // 선언과 할당 단축
  var score = 80;
  ```

- 변수를 선언하는 시점과 값을 할당하는 시점이 다르다.
  - 변수 선언 : Runtime 전 평가 과정
  - 값 할당 : Runtime
- 변수에 값을 할당할 때, 새로운 메모리 공간을 확보한 뒤 그곳에 값을 다시 저장한다. (이전에 저장되어 있던 `undefined`를 지우는게 아님)

  ```javascript
  console.log(score); // 2

  score = 80; // 3
  var score; // 1

  console.log(score); // 4
  ```

  1. 선언문이 가장 먼서 실행되며 메모리 공간을 확보한다.
  2. 선언 뒤 아직 값을 할당하지 않았으므로 `undefined` 출력
  3. 값을 할당하기 전에 새로운 메모리 공간을 확보한 뒤 `80` 저장
  4. 새로 할당한 메모리 공간에 값을 저장하여 `80` 출력
