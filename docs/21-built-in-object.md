# Built-in Object

- Javascript 객체
  - 표준 built-in 객체 : ECMA 사양에 정의되어 전역 객체의 property로 제공됨
  - Host 객체 : ECMA 사양에 없는 실행 환경에서 추가로 제공하는 객체
    - 브라우저의 Client Side Web API
    - Node.js에서 제공하는 API
  - 사용자 정의 객체

## Summary

- `Math`, `Reflect`, `JSON`을 제외한 built-in 객체들은 생성자 함수를 사용할 수 있다.
- 원시 값에 해당하는 built-in 객체도 정의되어 있으므로, 원시 값을 객체로 생성할 수도 있다.
- 원시 값을 리터럴로 생성한 뒤 객체처럼 사용하면 built-in 객체로 취급되어 임시 객체가 생성되고, 사용 후 곧바로 해제된다.
- Built-in 객체는 런타임 이전에 전역 객체에 미리 선언된다.
- 전역 객체의 property
  - Built-in 객체
  - 실행 환경 별 Host 객체
  - `var` 키워드로 선언한 전역 변수/함수
  - 암묵적 전역

## Built-in Object

- Math, Reflect, JSON을 제외한 표준 빌트인 객체는 생성자 함수 객체
- 생성자 함수로 표준 빌트인 객체 인스턴스를 만들 수 있다.
  ```js
  const stringObject = new String("Kim");
  console.log(typeof stringObject); // object, not a string
  ```
- 생성자 함수로 만든 객체이므로 prototype을 갖는다.
  ```js
  // String object의 prototype과 String 생성자 함수의 prototype이 같다.
  console.log(Object.getPropertyOf(stringObject) === String.prototype); // true
  ```
- Prototype method와 static method를 제공한다.
  ```js
  const numberObject = new Number(1.5);
  console.log(numberObject.toFixed()); // 2 -> prototype method
  console.log(Number.isInteger(0.5)); // false -> static method
  ```

## Primitive Value and Wrapper Object

- 원시 값도 객체 처럼 property, method를 사용할 수 있다.
  ```js
  const string = "Hello";
  console.log(string.length);
  console.log(string.toUpperCase());
  ```
- Javascript engine은 원시값을 **객체 처럼** 접근할 때 마다 임시로 생성되는 **wrapepr object**를 생성한다.

  - `.` 문법을 사용할 때 임시 객체의 인스턴스가 생성된다.
  - 원래 원시 값은 `[[{TYPE}Data]]` 내부 슬롯에 할당된다.
  - Wrapper 객체의 처리가 종료되면 `[[{TYPE}Data]]` 내부 슬롯에 할당했던 원시 값을 되돌리고, wrapper object는 **가비지 컬렉션 대상**이 되어 메모리에서 해제된다.

    ```js
    const str = "Hello";

    // Wrapper object가 생성되고, 그 object에 `name` property를 동적으로 추가했다.
    str.name = "Kim";

    // `name`에 값을 할당할 때 생성된 wrapper object와
    // `name`에 다시 접근할 때 wrapper object는 서로 다르기 떄문에
    // 여기서는 `undefined`가 반환된다.
    console.log(str.name); // undefined

    // Wrapper 객체의 처리가 끝나면 다시 원시 값으로 되돌리기 때문에
    // 이후 `str`에 참조하면 `string` 타입의 원시 값을 반환한다.
    console.log(typeof str, str); // string hello
    ```

- 문자열, 숫자, 불리언, 심벌은 wrapper object에 의해 **객체처럼 사용할 수 있으므로**, 생성자 함수로 직접 객체를 만들 필요가 없다. (권장하지 않는다.)
- `null`, `undefined` 등 다른 원시값은 wrapper object를 만들지 않으므로 객체처럼 사용할 수 없다.

## Global Object

- 런타임 이전에 가장 먼저 생성되는 객체
- 브라우저 환경에서는 `window`, Node.js 환경에서는 `global`
- ES11에서 도입된 `globalThis`는 실행 환경에 따라 나뉘던 전역 객체 식별자를 통합한 식별자로, 실행 환경에 맞는 식별자를 반환한다.
  ```js
  globalThis === window; // true in browser
  globalThis === global; // true in Node.js
  ```
- 전역 객체의 property
  1. 표준 빌트인 객체
  2. Host 객체
     - 브라우저 : Client side Web API
     - Node.js : Node.js 고유 API
  3. `var` 키워드로 선언한 전역 변수와 전역 함수
     - `let`, `const`로 선언한 변수는 다른 block에 선언되므로 전역 객체로 접근할 수 없다.
     - 다른 block : 전역 렉시컬 환경의 선언적 환경 레코드
  4. 암묵적 전역 : 선언하지 않은 변수에 값을 할당할 때
- 전역 객체의 특징
  - 직접 생성할 수 없다.
  - 전역 객체의 property를 참조할 때 `window`/`global`을 생략할 수 있다.
  - 서로 다른 script 또는 javascript 파일들이 **하나의 전역 객체를 공유**한다.

### Built-in Global Property

- `Infinity` : 무한대
- `Nan` : Not a number
- `undefined` : undefined (primitive value)

### Built-in Global Function

- `eval()` : Javascript code 문자열을 받아서 실행한다.
  - **보안에 취약하고 최적화가 수행되지 않으므로 사용하지 않는다.**
  - 표현식 : 런타임에 평가하여 값 생성
    ```js
    eval("1 + 2"); // 3
    eval("1 + 2; 3 + 4;"); // 7 -> 모두 평가하지만 마지막 값만 반환
    ```
  - 표현식이 아닌 문 : 코드 실행 (변수 선언 및 초기화 등)
    ```js
    eval("var x = 5"); // undefined
    console.log(x); // 5
    ```
  - 호출된 위치에 해당하는 **기존 scope를 런타임에 동적으로 수정**한다.
    ```js
    var x = 1;
    function foo() {
      eval("var x = 2;");
      console.log(x); // 2 -> eval이 x의 scope를 함수 내부로 수정함
    }
    foo();
    console.log(x); // 1
    ```
  - Strict mode에서는 기존 scope를 수정하지 않고 자체 scope를 생성한다.
    ```js
    var x = 1;
    function foo() {
      "use strict";
      eval("var x = 2; console.log(x);"); // 2
      console.log(x); // 1 -> eval이 자체 scope를 가지므로 x는 전역 scope를 유지함
    }
    foo();
    console.log(x); // 1
    ```
  - `let`, `const` 키워드를 사용한 변수 선언문은 암묵적으로 strict mode가 적용된다.
- `isFinite(number)` : `number`가 유한수이면 `true` 반환
- `isNaN(number)`
  - `number`가 `NaN` type이면 `true` 반환
  - 인수를 숫자 타입으로 변환 후 검사
- `parseFloat(string)`
  - `string`이 실수로 해석될 수 있다면 실수 반환
  - 공백으로 구분된 문자열은 첫 번째 문자열만 변환
  - 변환할 수 없다면 `NaN` 반환
- `parseInt(string, number)`
  - `string`이 정수로 해석될 수 있다면 정수 반환
  - `string`이 문자열이 아니면 문자열로 변환 후 검사
  - `number`에 진법을 나타내는 기수(2~36)를 사용하여 `string`을 **해당 진법으로 인식** 후 결과는 **10진법으로 반환**
    ```js
    parseInt("10"); // 10 -> 기본 10진수
    parseInt("10", 2); // 2 -> "10"을 2진법으로 해석한 뒤 10진수로 변환
    ```
  - `string`이 `0x` 또는 `0X`로 시작하면 **16진수로 해석 후 10진법으로 반환** (2진수(`0b`)와 8진수(`0o`)는 제대로 해석하지 못함)
    ```js
    parseInt("0xf"); // 15
    ```
  - `Number.prototype.toString(number)` method를 사용하면 숫자를 진법이 적용된 문자열로 변환할 수 있다.
    ```js
    15.toString(2); // 1111 -> 10진수 15를 2진수로 변환하고 문자열로 반환
    ```
  - `string`이 `number`에 해당하는 진법으로 변환될 수 없다면 `NaN` 반환
    ```js
    parseInt("A0"); // NaN -> 10진법으로는 A를 표현할 수 없음
    ```
  - `string`의 두 번째 문자부터는 이후 문자열을 무시하고 첫 번째 문자만 변환한다.
    ```js
    parseInt("5A23"); // 5 -> 5는 10진법 변환 가능, A는 불가능하므로 이후 문자열은 무시됨
    ```
- `encodeURI(uri)` / `decodeURI(uri)` : URI 전체 encoding/decoding
  - `encodeURI(uri)`는 인수를 **완전한 URI**로 취급
- `encodeURIComponent(comp)` / `decodeURIComponent(comp)` : URI의 component를 받아서 encoding/decoding
  - `encodeURIComponent(comp)`는 인수를 **query string의 일부**로 취급

#### URI와 Encoding/Decoding

- URI : Resource를 나타내는 주소. **URI = URL + URN**
  - URL : Scheme(`https`) + Domain(`www.domain.com`) + Port(`1000`) + Path(`/docs/search`)
  - URN : Query(`category=js&lang=en`) + Fragment(`#intro`)
- _escape 처리_
  - 네트워크를 통해 정보를 공유할 때 **어떤 시스템에서도 읽을 수 있는 ASCII charset으로 변환**하는 것
  - 의미를 갖는 문자(`%`, `?`, `#`), URL에 올 수 없는 문자(한글, 공백 등), 시스템에 의해 해석될 수 있는 문자(`<`, `>`)에 대해 escape 처리
  - 알파벳, 0~9 숫자, 일부 문자(`-`, `_`, `.`, `!`, `~`, `*`, `'`, `(`, `)` 등)는 제외
- Encoding : 문자열을 escape 처리하는 것
- Decoding : 문자열을 escape 처리 이전으로 되돌리는 것

### 암묵적 전역

```js
var x = 10;

function foo() {
  y = 20; // window.y = 20;
  console.log(x + y);
}
foo(); // 30

console.log(window.x); // 10 -> 전역 변수이므로 전역 객체에 추가됨
console.log(window.y); // 20 -> 전역 객체의 property로 추가됨

delete x;
delete y;

console.log(x); // 10
console.log(y); // undefined -> object property이므로 삭제됨
```

- 암묵적 전역을 일으키는 식별자는 전역 변수의 property로 추가된다.
  - 전역 객체의 property는 전역 객체(e.g. `window`)를 생략하고 호출할 수 있다.
  - 즉, 선언하지 않고 호출하는 변수는 **전역 객체에 새로운 property를 동적으로 추가**하는 것과 같다.
- 객체의 property이므로 **변수 호이스팅이 일어나지 않는다.**
- 객체의 property이므로 `delete` 연산자로 삭제할 수 있다.
