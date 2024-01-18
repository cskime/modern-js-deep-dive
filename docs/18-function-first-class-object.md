# Function and First-Class Object

## 일급 객체(first-class object)의 조건

- 일급 객체는 아래 4가지 조건을 만족한다.
  - 익명 함수 리터럴로 생성 (런타임 생성 가능)
  - 변수나 객체, 배열 등의 자료구조에 저장
  - 매개변수에 전달
  - 함수의 반환값으로 사용
- Javascript의 함수는 위 조건을 만족하는 일급 객체다.

## 함수 객체 고유 프로퍼티

### `arguments`

- 함수 내부에서만 사용할 수 있는 `arguments` 객체, 전달한 인수들이 저장되어 있다.
- Javascript 함수는 매개변수 개수와 인수 개수가 일치하는지 검사하지 않는다.
- 인수를 매개변수 개수보다 더 많이 전달하면, 매개변수에 할당되지 않은 인수들은 무시되지만 `arguments`에 저장되어 있다.
- `arguments` 객체 구조

  - `0`~`n` : 전달한 인수들이 전달 순서를 key로 저장되어 있다.
  - `callee` : 함수 자기 자신
  - `length` : 전달된 인수 개수
  - `Symbol(Symbol.iterator)` : `arguments` 객체를 iterable로 만드는 property

### 유사 배열 객체(array-like object)와 Iterable

- 유사 배열 객체(array-like object)
  - 숫자로 된 key와 `length` property를 가지고 있는 객체
  - 배열처럼 index로 값에 접근하거나,
    ```js
    const arrayLikeObj = {
      0: 10,
      1: 20,
      2: 30,
    };
    console.log(arrayLikeObj[2]); // 30 -> index로 접근하는 것 처럼 생겼지만 배열이 아니라 객체다.
    ```
  - `for`문을 순회할 수 있다.
    ```js
    function sum() {
      let res = 0;
      // 매개변수가 없지만 전달된 인수들이 `arguments`에 저장되어 있다.
      // 유사 배열 객체
      for (let i = 0; i < arguments.length; i++) {
        res += arguments[i];
      }
      return res;
    }
    sum(); // 0
    sum(1, 2); // 3
    sum(1, 2, 3); // 6
    ```
  - 실제로 배열은 아니기 때문에 배열 method를 직접 사용할 수 없고 `Function.prototype.call`, `Function.prototype.apply`를 사용해서 **간접 호출**해야 한다.
    ```js
    function sum() {
      const array = Array.prototype.slice.call(arguments);
      return array.reduce(function (pre, cur) {
        return pre + cur;
      }, 0);
    }
    sum(1, 2); // 3
    sum(1, 2, 3, 4, 5); // 15
    ```
  - Rest paremeter : 간접 호출의 번거로움을 해결하기 위해 ES6에서 추가된 문법으로, 가변 인자 함수를 구현할 때 `arguments` 대신 미리 array로 정의된 rest parameter를 이용할 수 있다.
    ```js
    function sum(...args) {
      return args.reduce((pre, cur) => pre + cur, 0);
    }
    ```
- `arguments` 객체는 ES5 까지 유사 배열 객체로 구분됨
- ES6부터는 유사 배열 객체이면서 동시에 **iterable protocol을 준수하는 iterable 객체**로 구분
- `Symbol.iterator`를 key로 하는 method를 구현하면 해당 객체는 iterable이 된다. (e.g. `arguments`)

  ```js
  function multiply(x, y) {
    // method를 호출하여 iterable을 얻는다.
    const iterator = arguments[Symbol.iterator]();

    // Iterator의 `next()`를 호출하여 iterable 객체를 순회한다.
    // Parameter는 두 개이지만 arguments에는 전달된 3개의 인수가 모두 들어있다.
    console.log(iterator.next()); // {value:1, done: false}
    console.log(iterator.next()); // {value:3, done: false}
    console.log(iterator.next()); // {value:5, done: false}

    return x * y;
  }
  multiply(1, 3, 5);
  ```

### 기타 property

- `length` : 함수를 정의할 때 선언한 **매개 변수**의 개수
  - `arguments.length` : 함수에 전달한 **인수**의 개수
- `name` : 함수 객체의 이름
  - 익명 함수 표현식에서 버전 별 동작이 다르다.
  - ES5에서 `name`은 빈 문자열
  - ES6에서 `name`은 함수 객체를 가리키는 **식별자**
  - 예시
    ```js
    const func = function () {};
    console.log(func.name); // ES5에서는 ""
    console.log(func.name); // ES6에서는 "func"
    ```
- `__proto__` : prototype 객체에 간접적으로 접근할 수 있는 접근자 property
- `prototype` : Constructor만 가진 property로, 함수가 생성자 함수로 동작할 때 **함수가 생성할 인스턴스의 prototype 객체**를 가리킴
- `caller` : 함수가 다른 함수 안에서 호출할 때, 자기 자신을 호출한 함수를 가리킴. **비표준이므로 사용 X**
