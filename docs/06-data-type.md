# 06. Data Type

- 원시 타입(primitive type) : number, string, boolean, undefined, null, symbol
- 객체 타입(object/reference type) : object, array, function

## Data type의 필요성

1. 값 할당 시 확보해야 하는 메모리 공간의 크기 결정
   - `number` : 8byte (64bit 부동소수점 형식이므로)
   - `number`와 `string`을 제외한 나머지 타입들의 메모리 크기는 javascript engine에 따라 다르다.
2. 값 참조 시 한 번에 읽어들여야 하는 메모리 공간의 크기 결정
   - 값이 할당될 때 사용된 메모리 크기를 정확히 참조해야 정확한 값을 얻을 수 있음
3. 참조한 값을 어떤 type으로 해석할지 결정
   - 메모리에 저장된 2진수를 어떤 type으로 해석할 것인지 결정
   - `0100 0001`은 `number` type에선 숫자 `65`이지만 `string` type에선 `A` (ASCII)
   - 저장된 값의 타입 정보를 **symbol table**로부터 읽어서 해석 결과를 결정한다.
     - symbol table : 식별자를 key로 하여 바인딩된 값의 메모리 주소, 데이터 타입, 스코프 등을 관리함

### Dynamic Typing

- 정적 타입 언어 : 변수를 선언할 때 타입 지정
  - 지정된 타입의 값만 할당 가능
  - Runtime에 변수의 타입 변경 불가능
- 동적 타입 언어 : 변수 타입이 runtime에 결정됨
  - 변수 선언 시 타입을 지정하지 않으므로, 어떤 값이든 자유롭게 할당(또는 재할당)할 수 있음
  - **값이 변수에 할당되는 시점**에 타입이 결정됨 (type inference)
  - 즉, **할당되어 있는 값에 의해 타입이 결정됨**
- Javascript는 동적 타입 언어이므로, **값을 확인하기 전 까지 타입을 확신할 수 없음**
- Javascript engine에 의해 암묵적으로 타입이 자동으로 변환되기도 함
- 즉, 유연성은 높지만 신뢰성은 떨어진다.
- 동적 타입 언어 사용 시 주의할 점
  1. 변수의 개수를 최소화하여 꼭 필요할 때만 선언하고 사용한다.
     - 변수 개수가 많아질수록 값이 중간에 변경될 가능성도 커지므로 오류가 발생할 확률도 높아진다.
  2. 변수 유효 범위(scope)를 최대한 좁게 만든다.
     - 변수 scope가 넓을 수록 영향을 받는 코드가 늘어나므로 오류가 발생할 확률도 높아진다.
  3. 전역 변수를 사용하지 않는다.
     - 어디서든 참조/변경 가능하므로 의도치 않은 오류가 발생할 확률이 높고 디버깅을 어렵게 한다.
  4. 변수보다 상수(`const`)를 사용해서 값이 변경되는 것을 막는다.
  5. 변수의 목적이나 의미를 파악할 수 있게 네이밍한다.
     - 오해하지 않도록 코드를 작성해야 한다.
     - 이해하기 쉬운 코드 == 가독성이 좋은 코드 == 좋은 코드 (Good!)

## Number

- Javascript는 모든 숫자를 `number` 타입 하나로 표현한다.
- `number` 타입 값은 64bit 부동소수점 형식을 따른다. 즉, 모든 숫자는 javascript에서 **실수**이다.
  - `1 === 1.0`
  - 정수끼리 나누어도 결과는 실수로 반환된다.
- 모든 숫자 literal은 64bit 부동소수점 형식의 2진수로 저장된다.
- 2진수, 8진수 16진수 등의 값을 참조해도 10진수로 해석된다.
  ```javascript
  var binary = 0b1000001;
  var octal = 0o101;
  var hex = 0x41;
  console.log(binary); // 65
  console.log(octal); // 65
  console.log(hex); // 65
  console.log(binary === octal); // true
  console.log(octal === hex); // true
  ```
- 특수 값
  - `Infinity` : `0`으로 나눈 결과
  - `-Infinity` : `-0`으로 나눈 결과
  - `NaN` : `number` 타입이 아닌 값과 연산한 결과 (e.g. `1 * "string"`)

## String

- 0개 이상의 16비트 유니코드 문자(UTF-16)의 집합
- 키워드, 식별자 같은 토큰들과 구별하기 위해 작은 따옴표, 큰 따옴표, 백틱(ES6+) 등으로 감싸서 표현
- Immutable value

### Template literal

- 따옴표 대신 백틱을 사용하는 특수한 문자열
- Multi-line
  - 일반 문자열은 `\n`, `\t`등의 escape sequence를 사용해야 함
  - Template literal에서는 escape sequence 없이 줄바꿈 허용
    ```javascript
    var template = `<ul>
        <li><a href="#">Home</a></li>
    </ul>`;
    ```
- Expression interpolation : `${}`으로 표현식을 감싸면 평가된 결과가 문자열로 타입 변환되어 삽입된다.
  ```javascript
  var first = "Chamsol";
  var last = "Kim";
  console.log(`My name is ${first} ${last}`);
  ```

## Boolean

- 논리적 참 거짓 표현
- `true`, `false`

## Undefined

- `undefined` : 변수 선언 후 javascript engine에 의해 암묵적으로 초기화되는 값
- Javascript engine이 사용하는 값이므로 개발자가 의도적으로 사용하지 않는게 좋다.

## null

- `null` : 변수에 값이 없다는 의미
- `null` 할당의 의미
  - Javascript는 새로운 값을 할당할 때 새로운 메모리를 확보한 뒤 값을 저장한다.
  - `null`을 할당하면 이전에 값을 저장했던 메모리를 더 이상 참조하지 않으면서 현재 할당된 메모리에는 값을 저장하지 않게 된다.
  - 즉, **할당되어 있던 값에 대한 참조를 명시적으로 제거하는 행위이다.**
  - 참조가 제거된 메모리는 가비지 컬렉터에 의해 정리된다.
- 사용되는 경우
  - 함수가 유효한 값을 반환할 수 없을 때 (e.g. `document.querySelector`가 조건에 맞는 HTML element를 찾을 수 없을 때)
  - 개발자가 명시적으로 값이 없음을 나타내고 싶을 때 `undefined` 대신 `null` 사용

## Symbol

- Immutable primitive type
- 다른 값과 중복되지 않는 유일한 값
- 이름이 충돌할 위험이 없는 객체의 유일한 프로퍼티 키를 만들 때 사용
