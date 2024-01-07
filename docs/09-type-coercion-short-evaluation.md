# 09. Type Coercion and Short-circuit Evaluation

## 타입 변환(Type Coercion)

- 타입 변환 : 값의 타입을 다른 타입으로 변환하는 것
- 원시 값은 immutable value 이므로, 타입이 변경된 새로운 값을 생성하는 것
- Javascript engine에 의해 암묵적으로 변환되기도 하고, 개발자가 명시적으로 변환하기도 함
- 암묵적 타입 변환이 일어나는지, 어떤 타입의 어떤 값으로 변환되는지, 표현식의 결과가 어떻게 될지 **예측 가능해야 함**

### 암묵적 타입 변환(implicit coercion) 또는 타입 강제 변환(type coercion)

- Javascript engine이 코드의 **문맥(context)**을 고려하여 암묵적으로 데이터 타입을 강제로 변환하는 것
- 숫자와 문자열을 더하는 표현식에서는 문맥상 모든 피연산자가 `string` type이어야 함 (e.g. `1 + "2"`)
- 이 때, javascript는 에러를 발생시키지 않도록 암묵적으로 타입을 변환하여 표현식을 평가한다.

### 명시적 타입 변환(explicit coercion) 또는 타입 캐스팅(type casting)

- 개발자가 의도적으로 타입을 변환하는 것
- Built-in 생성자 함수(`String()`, `Number()`, `Boolean()`)를 `new` 없이 사용
- Built-in method 사용
- 암묵적 타입 변환 활용

### String type으로 변환하기

- 암묵적 변환

  - `+` 연산자는 피연산자 중 하나 이상이 `string` type일 때 문자열 연결 연산자로 동작
  - 이 때, 문맥상 모든 피연산자가 `string` type이어야 함 -> 연산 결과를 `string` type으로 만들기 위해
  - Javscript engine은 `string` type이 아닌 값을 `string` type으로 강제 변환

    ```javascript
    // 1. 값이 그대로 문자열이 되는 경우
    1 + ""; // "1"
    NaN + ""; // "NaN"
    null + ""; // "null"
    (functio(){}) + ""; // "function(){}"

    // 2. 값과 문자열이 다른 format이 되는 경우
    ({}) + ""; // [object Object]
    Math + ""; // [object Math]
    [] + ""; // ""
    [10, 20] + ""; // "10,20"
    Array + ""; // function Array() { [navtive code] }
    ```

- 명시적 변환

  ```javascript
  // 1. 생성자 함수 사용
  String(1);

  // 2. Built-in method 사용 (Object.prototype.toString)
  NaN.toString();

  // 3. 암묵적 변환 활용
  Infinity + "";
  ```

### Number type으로 변환하기

- 암묵적 변환
  - 산술 연산자 및 비교 연산자는 문맥상 모든 피연산자가 `number` type이어야 함
  - 이 때, javascript engine은 `number` type이 아닌 값을 `number` type으로 강제 변환
  - 변환 가능한 경우
    - `string` type은 문자열 안의 숫자가 그대로 `number` type 값으로 변환
    - `boolean` type은 `true` -> `1`, `false` -> `0`
    - `null` tpye은 `0`으로 변환
    - 빈 배열은 `0`으로 변환
  - 변환할 수 없는 경우 (`NaN` 반환)
    - 숫자가 아닌 문자열
    - `undefined`
    - `object`
    - 원소가 있는 배열
    - 함수
- 명시적 변환

  ```javascript
  // 1. 생성자 함수 사용
  Number("0");

  // 2. Built-in method 사용 (parseInt(), parseFloat())
  parseInt("0");
  parseFloat("10.53");

  // 3. 암묵적 변환 활용
  +"-1"; // + 단항 산술 연산자 사용
  "1" * 1; // * 산술 연산자 사용 -> 1을 곱하여 문맥을 숫자로 만드는 동시에 원래 값을 유지시킴
  ```

### Boolean type으로 변환

- 암묵적 변환
  - Conditional statement는 문맥상 평가된 결과값이 `boolean` type이어야 함
  - Javascript engine은 conditional statement의 평가된 결과값을 `number` type으로 강제 변환
  - Falsy(`false`로 평가되는 값)로 평가되는 경우
    - `false`
    - `undefined`
    - `null`
    - `NaN`
    - `0`, `-0`
    - 빈 문자열(`""`)
  - 이외의 값들은 모두 Truthy(`true`로 평가되는 값)로 평가된다.
- 명시적 변환

  ```javascript
  // 1. 생성자 함수 사용
  Boolean("x"); // true
  Boolean(""); // false

  // 2. !! 연산자 사용
  !!NaN; // false
  !!Infinity; // true
  ```

## 단축 평가(Short-circuit Evaluation)

- 단축 평가 : 표현식을 평가하는 도중에 평가 결과가 확정된 경우, 나머지 평가 과정을 생략하고 **현재 평가된 결과를 그대로 반환**하는 것

### 논리 연산에서 단축 평가

- `&&` 또는 `||` 연산자 표현식의 평가 결과는 `boolean` type이 아닐 수도 있다.
  - `&&` 연산은 두 피연산자 중 하나가 `false`면 전체 결과도 `false`로 확정된다.
  - `||` 연산은 두 피연산자 중 하나가 `true`면 전체 결과도 `true`로 확정된다.
  - `Falsy && anything` 또는 `Truthy || anything` 연산에서 오른쪽 피연산자를 평가하지 않고 왼쪽 피연산자의 평가 결과를 반환한다.
  - 왼쪽 피연산자가 `boolean` type이 아니라면, Truthy와 Falsy를 판단할 때 암묵적으로 `boolean` type으로 변환해서 체크한다.
  - 단, 연산 결과는 원래 값으로 반환된다.
- 즉, 두 논리 연산자는 **단축 평가에 의해 마지막으로 평가된 표현식의 값**을 반환한다.

  ```javascript
  // AND 연산에서 왼쪽 피연산자가 true일 때만 오른쪽 피연산자가 평가되고, 피연산자의 값을 연산 결과로 반환한다.
  // "cat"은 암묵적으로 true로 변환되어 평가에 사용되므로, 왼쪽 피연산자가 "cat"이면 연산 결과는 항상 오른쪽 피연산자의 값이 된다.
  true && "cat"; // "cat"
  false && "cat"; // false
  "cat" && true; // true
  "cat" && false; // false
  "cat" && "dog"; // "dog"

  // OR 연산에서 왼쪽 피연산자가 false일 때만 오른쪽 피연산자가 평가되고, 피연산자의 값을 연산 결과로 반환한다.
  // "cat"은 암묵적으로 true로 변환되어 평가에 사용되므로, 왼쪽 피연산자가 "cat"이면 연산 결과는 항상 왼쪽 피연산자의 값이 된다.
  true || "cat"; // true
  false || "cat"; // "cat"
  "cat" || true; // true
  "cat" || false; // false
  "cat" || "dog"; // "cat"
  ```

- 이 특성을 활용하여 `if` 문을 대체할 수 있다.

  - Condition이 `true`일 때

    ```javascript
    var done = true;
    var message = "";

    // 1. if문
    if (done) message = "Done";

    // 2. 논리 연산 단축 평가
    message = done && "Done";
    ```

  - Condition이 `false`일 때

    ```javascript
    var done = false;
    var message = "";

    // 1. if문
    if (!done) message = "Done";

    // 2. 논리 연산 단축 평가
    message = done || "Done";
    ```

- 논리 연산 단축 평가의 활용 예시

  - Instance가 `null` 또는 `undefined`인지 확인한 뒤 property르 참조하는 경우

    ```javascript
    var elem = null;

    // null에 접근하면 TypeError 발생
    var value = elem.value;

    // null 또는 undefined는 Falsy 값이므로 elem.value 표현식을 평가하지 않음
    // && 연산 단축 평가에 의해 null을 반환하며 error가 발생하지 않음
    var value2 = elem && elem.value;
    ```

  - 함수 매개변수에 기본값을 설정하는 경우

    ```javascript
    function getStringLength(str) {
      // 매개변수를 전달하지 않으면 str은 Falsy 값인 undefined로 초기화
      // || 연산 단축 평가에 의해 str에 빈 문자열이 할당됨
      str = str || "";
      return str.length;
    }
    getStringLength(); // 0
    ```

### 옵셔널 체이닝 (Optional Chaining)

- `?.` : 왼쪽 피연산자가 `null` 또는 `undefined`인 경우 `undefined`를 반환하고, 그렇지 않으면 오른쪽의 property 참조를 이어간다.
- ES11+ 에서 도입된 연산자로, 이전에는 `&&` 단축 평가를 사용했음
  ```javascript
  var elem;
  // Before ES11
  var value = elem && elem.value;
  // After ES11+
  var value = elem?.value;
  ```
- 하지만, `0`이나 빈 문자열(`""`)은 `null` 또는 `undefined`와 같은 Falsy 값이지만 체크할 때는 해당 값들이 좌항에서 객체로 평가되어 의도하지 않은 동작을 할 때도 있었다.
  ```javascript
  var str = "";
  var length = str && str.length;
  // 실제론 length를 참조할 수 있지만, 빈 문자열이 Falsy 값이므로 str.length 표현식이 평가되지 않음
  console.log(length);
  ```
- `?.` 연산자는 `null`, `undefined`가 아닌 Falsy 값에 대해서도 의도한 동작을 수행한다.
  ```javascript
  var str = "";
  var length = str?.str.length;
  console.log(length); // 0
  ```

### null 병합 연산자 (nullish coalescing)

- `??` : 좌항 피연산자가 `null` 또는 `undefined`인 경우 우항 피연산자를 반환하고, 그렇지 않으면 좌항 피연산자를 반환한다.
- ES11+ 에서 도입된 연산자로, 이전에는 `||` 단축 평가를 사용했음

  ```javascript
  // Before ES11
  var foo = null || "default string";
  // After ES11+
  var foo = null ?? "default string";

  console.log(foo); // "default string"
  ```

- `||` 단축 평가를 사용할 때, 좌항이 `null` 또는 `undefined`가 아닌 Falsy 값인 경우 의도하지 않은 동작을 할 때가 있었음
  ```javascript
  var foo = "" || "default string";
  // 실제론 빈 문자열을 반환해야 하지만, 빈 문자열이 Falsy 값이므로 좌항 표현식이 평가되지 않음
  console.log(foo); // "default string"
  ```
- `??` 연산자는 `null`, `undefined`가 아닌 Falsy 값에 대해서도 의도한 동작을 수행한다.
  ```javascript
  var foo = "" ?? "default string";
  console.log(foo); // ""
  ```
