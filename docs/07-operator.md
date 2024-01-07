# 07. Operators

- Operator : 하나 이상의 표현식을 대상으로 연산을 수행하여 하나의 값을 만듦
- Operand : 연산하는 대상 == 값으로 평가될 수 있는 표현식
- Side effect : Operand의 값이 변경되는 것
- 요약
  - Arithmetic
    - Binary : `+`, `-`, `*`, `/`, `%`
    - Unary : `++`, `--`, `+`, `-`
  - Assignment : `=`, `==`, `-=`, `*=`, `/=`, `%=`, `**=`
  - Comparison
    - Equality
      - Loose : `==`
      - Strict : `===`
      - Nan : `Number.isNaN(e)`
      - `Object.is(e1, e2)`
    - Greater/Smaller : `>`, `>=`, `<`, `<=`
  - Ternary : `e ? te : fe`
  - Logical : `||`, `&&`, `!`
  - Type
    - `typeof`
    - `typeof null;` => `"object"`
    - `typeof undeclared;` => `"undefined"`
  - Exponent : `**`, `Math.pow(b,e)`

## 1. 산술 연산자 (Arithmetic operator)

- 수학적 계산
- 산술 연산이 불가능한 경우 `NaN` 반환
  - 산술 연산은 숫자(`number` type)에 대해 이루어지므로
- 이항 산술 연산자 : Operand 두 개
  - `+`, `-`, `*`, `/`, `%`
  - Side effect 없음
- 단항 산술 연산자 : Operand 한 개
  - 증가/감소 연산자
    - `++`, `--`
    - 피연산자 값을 `+1` 또는 `-1` 연산 (Side effect)
    - 연산자 위치에 따라 다르게 동작
      - `x++` : 선할당 후증가
      - `++x` : 선증가 후할당
  - 부호 연산자
    - `+` : 아무 영향도 없음
    - `-` : 부호 반전 (양수 -> 음수, 음수 -> 양수)
    - `number` type이 아닌 값에 사용할 경우, **숫자 타입으로 변환한 값을 새로 생성해서 반환**
      ```javascript
      var x = "1";
      console.log(+x); // 1
      var y = true;
      console.log(+y); // 1
      var z = "Hello";
      console.log(+z); // NaN
      ```
- 문자열 연결 연산자
  - `+`는 Operand 중 하나가 `string` type이면 문자열 연산자로 동작한다.
  - 이 때, javascript engine에 의한 암묵적 타입 변환이 일어난다.
- 산술 연산에서 암묵적 타입 변환
  - Operand 중 하나가 `number` type 이라면, 다른 operand를 `number` type 값으로 변환한다. -> 산술 연산자로 동작
  - Operand 중 하나가 `string` type 이라면, 다른 operand를 `string` type 값으로 변환한다. -> 문자열 연결 연산자로 동작

## 2. 할당 연산자

- Operand를 좌변에 있는 변수에 할당
- 종류 : `=`, `+=`, `-=`, `*=`, `/=`, `%=`
- 할당문은 값으로 평가되는 표현식으로, **좌변 변수에 할당되는 값으로 평가**됨
  - 변수에 동일한 값을 연쇄적으로 할당 가능 (`a = b = c = 0;`)

## 3. 비교 연산자

- 두 개의 operand를 비교한 결과를 `boolean` type 값으로 반환
- 동등과 일치
  - 동등(`==`, `!=`) : 암묵적으로 타입을 변환하여 타입이 일치된 두 값을 비교
    - `5 == "5"` 표현식은 암묵적으로 `5`가 문자열 `"5"`로 변환된 후 비교하여 `true`를 반환한다.
  - 일치(`===`, `!==`) : 암묵적 타입 변환 없이 두 값 비교
  - 동등 연산자는 결과를 예측하기 어려우므로(안티패턴임) 일치 연산자를 사용한다.
  - `NaN`의 일치 비교
    - `NaN`은 자신과 일치하지 않는 유일한 값
    - `Number.isNaN()`을 사용하거나 `Object.is(a,b)`를 사용한다.
      ```javascript
      NaN === NaN; // false
      Number.isNaN(NaN); // true
      Number.isNaN(1 + undefined); // true
      Object.is(NaN, 1 + undefined); // true
      ```
  - 양의 0(`+0`)과 음의 0(`-0`) 비교
    - Javascript는 `+0`과 `-0`의 동등/일치 연산을 모두 `true`로 반환한다. (같은 0이라고 판단하는 듯)
    - `Object.is(+0, -0)`은 `false`를 반환한다. (양에서 0으로 수렴하는 것과 음에서 0으로 수렴하는 것으로 이해한다면, `false`가 더 맞는 결과처럼 보인다.)
- 대소 비교
  - `>`, `>=`, `<`, `<=`

## 4. 삼항 연산자

- Ternary operator
- `statement ? true_value : false_value`
- 값으로 평가할 수 있는 표현식이므로 값처럼 사용할 수 있다.
- 수행할 문이 두 줄 이상이라면 `if-else`를 사용하는게 좋다.

## 5. 논리 연산자

- 논리 연산 (or, and, not)
- `||`, `&&`, `!`
- Operand가 `boolean` type이 아니면 암묵적 타입 변환이 일어난다.
  ```javascript
  !0; // true
  !"Hello"; // false;
  ```
- OR 또는 AND 연산의 결과는 두 operand 중 한쪽으로 평가되므로 `boolean`이 아닐 수도 있다.

## 6. 타입 연산자

- `typeof` : Operand의 data type을 문자열로 반환
- 반환될 수 있는 값 : `"string"`, `"number"`, `"boolean"`, `"undefined"`, `"symbol"`, `"object"`, `"function"`
- 주의할 점
  - `typeof`로 `null`을 연산했을 때 `"null"`이 아닌 `"object"`를 반환하는 버그가 있으므로, `null` type을 확인할 땐 일치 연산자를 사용한다.
    ```javascript
    var foo = null;
    typeof foo === null; // false
    foo === null; // true
    ```
  - 선언하지 않은 변수에 대해 연산하면 `ReferenceError`가 아닌 `undefined`가 반환된다.
    ```javascript
    typeof undeclared; // undefined
    ```

## 7. 지수 연산자

- ES7+ 사용 가능
- 좌변을 밑(base), 우변을 지수(exponent)로 하는 거듭 제곱 연산
- `**` 연산자 또는 `Math.pow(b,e)` function 사용
  ```javascript
  2 ** 2; // 4
  Math.pow(2, 2); // 4
  ```
- 연산자들 중에 가장 우선순위가 높다.
  ```javascript
  2 * 5 ** 2; // 50 -> 2 * (5 ** 2)
  ```
