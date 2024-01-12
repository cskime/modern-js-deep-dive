# Function

- Javascript 함수는 **일급 객체**이므로 변수에 할당하거나 인수로 사용할 수 있다.
- 일련의 과정을 문(statement)으로 구현하고 코드 블럭으로 감싸서 하나의 **실행 단위**로 정의한 것
  - 인수(argument) : 함수 외부에서 내부로 전달하는 입력(input)값
  - 매개변수(parameter) : 함수 내부로 입력을 전달받는 변수
  - 반환값(return value) : 출력(output)
- 함수를 사용하는 이유
  - 코드의 재사용 및 유지보수 편의성 증가
  - 적절한 함수 이름을 통한 코드의 가독성 향상

## Summary

- 함수는 일급 객체로 변수(식별자)에 할당할 수 있다.
- 함수 리터럴은 문맥에 따라 함수 선언문과 함수 표현식으로 평가된다.
  - 함수 선언문
    - 선언과 동시에 함수 객체가 할당된다. (호이스팅이 발생)
    - 함수 이름을 생략할 수 없다.
  - 함수 표현식(권장)
    - 선언 후 실행될 때 함수 객체가 할당된다.
    - 익명 함수로 사용할 수 있다.
- 함수를 호출할 때는 함수 이름이 아닌 함수 참조를 저장하고 있는 식별자가 사용된다.
- 함수 parameter는 argument의 타입과 개수를 제한하지 않는다.
- 함수 parameter는 변수와 같이 동작하여 `undefined`로 초기화된 뒤 argument가 재할당된다.
- Side effect를 최소화하기 위해 가능한 순수 함수로 작성하는게 좋다.

## 함수 리터럴(function literal)

```javascript
function add(x, y) {
  return x + y;
}
```

- `function` 키워드 + 함수 이름 + param list + body
- 함수 이름은 함수 내부에서만 접근할 수 있는 식별자
- 함수 리터럴의 이름은 생략 가능
  - 기명 함수(named function) : 이름이 있는 함수
  - 익명 함수(anonymous function) : 이름을 생략한 함수

## 함수 정의(function definition)

### 함수 선언문과 함수 표현식

- 함수 리터럴은 문맥에 따라 두 가지 방식으로 해석된다.
  - 함수 선언문
    ```javascript
    function add(x, y) {
      return x + y;
    }
    ```
    - 함수 선언문은 함수 이름을 생략할 수 없다.
  - 함수 표현식
    ```javascript
    var add = function (x, y) {
      return x + y;
    };
    ```
  - 함수 선언문은 함수 이름을 생략할 수 없지만, 표현식은 생략할 수 있다.
  - 함수 표현식은 JS engine에 의해 평가되어 함수 객체를 생성하고 변수에 할당할 수 있다.
- 함수 선언문과 표현식의 내부 동작 차이
  - Javascript engine은 정의된 함수를 메모리에 저장한다.
  - JS engine은 함수 선언문에 대해 **암묵적으로 함수 이름과 동일한 식별자를 생성한 뒤 함수 주소를 참조**한다.
  - JS engine은 함수 표현식에 대해서는 식별자를 암묵적으로 생성하지 않는다.
  - 이 때, **함수 이름은 함수 내부에서만 접근할 수 있으므로 식별자에 할당하지 않은 함수 표현식은 호출할 수 없다.**
    ```javascript
    (function bar() {
      console.log("bar");
    }); // 그룹 연산자(`()`) 안에서 함수 표현식으로 해석된 뒤 식별자(변수)에 할당되지 않음
    bar(); // ReferenceError: bar is not defined
    ```
  - 반면에, 함수 식별자는 암묵적으로 생성된 식별자에 함수 참조가 저장되어 있으므로 호출할 수 있다.
    ```javascript
    function foo() {
      console.log("foo");
    }
    foo();
    ```
  - 즉, 함수를 호출할 때 사용하는 이름은 함수 이름이 아니라 **함수 객체의 참조를 저장하고 있는 식별자 이름**이다.
    ```javascript
    var add = function foo(x, y) {
      return x + y;
    };
    add(2, 5); // 함수 이름이 아닌 식별자 이름으로 호출해야 한다.
    foo(2, 5); // ❌ 함수 이름은 함수 외부에서 사용할 수 없다.
    ```
- 함수 선언문의 변수 할당
  - 함수 선언문은 표현식이 아니므로 함수 객체로 평가될 수 없어 변수에 할당할 수 없다.
  - 하지만, 함수를 변수에 할당할 때는 javascript engine이 문맥에 따라 선언문이 아닌 **표현식**으로 해석되므로 함수 객체를 만들고 변수에 할당할 수 있다.
  - 즉, 기명 함수 표현식을 변수에 할당하는 것이 함수 선언문을 변수에 할당되는 것처럼 보이지만 실제론 아니다.
- 함수 생성 시점과 호이스팅
  - 함수 선언문
    - 함수 선언문은 변수와 같이 **런타임 이전에** javascript engine에 의해 먼저 평가되어 함수 객체가 생성된다. => **함수 호이스팅**
    - 생성된 함수 객체의 참조는 JS engine에 의해 암묵적으로 생성된 식별자에 저장된다.
    - 따라서, **함수 선언문은 선언문 이전에 함수를 호출할 수 있다.**
      ```javascript
      console.log(add(2, 5)); // 7
      function add(x, y) {
        return x + y;
      }
      ```
  - 함수 표현식
    - 함수 표현식은 런타임에 평가되어 함수 객체가 생성되고 변수에 할당된다.
    - 즉, 함수 표현식에서는 **변수**가 선언되는 것이므로 **변수 호이스팅**이 일어난다.
    - 함수 표현식 이전에 함수를 호출하려고 하면 변수 호이스팅에 의해 초기화된 `undefined`를 호출하므로 에러가 발생한다.
      ```javascript
      console.log(sub(2, 5)); // TypeError: sub is not a function
      var sub = function (x, y) {
        return x - y;
      };
      ```
    - 따라서, **함수 표현식으로 정의한 함수는 반드시 표현식 이후에 호출해야 한다.**
  - **함수 선언문보다 함수 표현식을 사용할 것이 권장된다.**
  - 호이스팅 : 함수 선언문이 코드 선두로 끌어 올려진 것 처럼 동작하는 것
    - 변수 호이스팅이 일어날 때는 선언된 변수가 undefined로 초기화된다.
    - 함수 호시트잉이 일어날 때는 암묵적으로 생성된 식별자가 함수 객체로 초기화된다.

### 생성자 함수

```javascript
var add = new Function("x", "y", "return x + y");
```

- Javascript built-in `Function` 생성자 함수 사용
- 함수 선언문이나 함수 표현식으로 생성한 함수와 다르게 동작
- **권장되지 않음**

### 화살표 함수(ES6+)

```javascript
var add = (x, y) => x + y;
```

- ES6에서 도입된 문법
- `function` 키워드를 화살표(`=>`)를 사용해서 축약
- 항상 익명 함수로 정의
- 기존 함수의 내부 동작도 간략화 되어 있음
  - 생성자 함수로 사용할 수 없음
  - `this` 바인딩 방식이 다름
  - `prototype` 프로퍼티가 없음
  - `arguments` 객체를 생성하지 않음

## 함수 호출(function call/invoke)

### Argument

- Argument는 값으로 평가될 수 있는 표현식이다.
- Argument의 개수는 parameter와 별개로 제한이 없다.
  - Argument가 parameter 수 보다 적으면, 나머지 parameter는 `undeinfed` 초기값을 가진다.
    ```javascript
    function add(x, y) {
      return x + y;
    }
    add(2); // 2 + undefined = NaN
    ```
  - Argument가 parameter 수 보다 많으면, 나머지 argument들은 `arguments` 객체에 저장된 후 무시된다. (화살표 함수 제외)
    ```javascript
    function add(x, y) {
      console.log(arguments); // Arguments(3) [ 2, 5, 10, callee: f, Symbol(Symbol.iterator): f ]
      return x + y;
    }
    add(2, 5, 10); // 2 + 5 = 7
    ```
- Argument의 타입은 제한이 없다.
  - Javascript는 동적 타입 언어
  - 값이 할당되는 시점에 값에 따라 타입이 정해지므로, **parameter type을 미리 정해둘 수 없다**.
  - 따라서, 함수를 정의할 때 의도에 맞는 argument가 전달되었는지 확인할 필요가 있다.
    - 함수 가장 위에서 parameter type을 체크한다.
      ```javascript
      function add(x, y) {
        if (typeof x !== "number" || typeof y !== "number") {
          throw new TypeError("The arguments must be a number type.");
        }
      }
      add(2); // TypeError
      add("a", "b"); //TypeError
      ```
    - Typescript 같은 정적 타입을 선언할 수 있는 상위 확장을 통해 컴파일 타임에 부적절한 호출을 막는다.
    - `arguments` 객체의 argument 개수를 확인한다.
    - 단축 평가를 사용하여 매개변수에 기본값을 할당한다.
    - 매개변수 기본값을 활용한다. (ES6+, 값이 `undefined` parameter에만 유효)

### Parameter

- 함수 외부의 값을 내부로 전달하기 위해 parameter를 통해 argument를 전달한다.
- 변수와 동일하게 취급되어 `undefined`로 초기화된 후 argument가 재할당된다.
- Parameter의 scope는 함수 내부로 제한된다.
- Parameter 개수는 적을 수록 좋다.
  - Parameter 개수가 3개를 넘어간다면 객체 1개에 property로 담아서 전달하는게 좋다.
  - 단, 객체는 mutable value이므로 함수 내부에서 변경되어 side effect를 일으킬 수 있으므로 유의해야 한다.

### Return

- 함수 호출은 표현식이다. 이 표현식은 반환값(return value)로 평가된다.
- `return` 키워드는 함수 실행을 중단시키고, `return` 뒤에 오는 표현식을 평가해 반환한다.
- `return`을 생략하거나 뒤에 표현식을 지정하지 않으면 `undefined`가 반환된다.
- 일반적으로 `return`은 함수 내부에서만 사용할 수 있지만, node.js 환경에서는 파일별로 독립적인 scope를 가지므로 파일 자체에서 사용해도 문제가 없다.

## 순수 함수

- Parameter는 변수와 같이 동작하므로,
  - argument가 원시 값이라면 값에 의한 전달(pass by value) 방식으로 동작한다.
  - argument가 객체라면 참조에 의한 전달(pass by reference) 방식으로 동작한다.
- 원시 값은 immutable value이므로 함수 내부에서 parameter 값을 변경해도 원본 값이 영향을 받지 않지만, 객체는 mutable value이므로 함수 내부에서 객체를 변경하면 원본 값도 영향을 받는다.

  ```javascript
  function change(primitive, object) {
    primitive += 100; // primitive에는 원시값 자체가 복사되므로 원본 number의 값은 변하지 않는다.
    object.name = "Lee"; // object에는 객체의 참조가 복사되므로 원본 person의 값이 변경된다.
  }

  var number = 100;
  var person = { name: "Kim" };
  change(number, person);

  console.log(number); // 100;
  console.log(person); // { name: "Lee" }
  ```

- 순수 함수
  - 외부 상태를 변경하지 않고, 외부 상태에 의존하지도 않는 함수
    - **Functional programming paradigm** : 변수 사용을 억제하거나 생명 주기를 최소화해서 상태 변경을 피해 오류를 최소화하려는 개발 방법
  - 같은 input을 넣으면 항상 같은 output을 반환한다.
    - 외부 상태에 의존하여 외부에서 변경된 상태에 의해 output이 변경되면 안된다.
    - 외부 상태에 의존하지 않더라도 호출할 때 마다 output이 변경되면 안된다. (e.g. 함수 내부에서 타이머 사용)
    - Parameter로 객체를 전달받으면 안된다. (객체는 mutable value이므로 원본 값을 변경하여 output이 변경될 수 있음)
  - 함수가 외부의 상태를 변경하면 상태 변화를 추적하기 어려워서 문제를 야기한다.
  - 이런 문제를 해결하기 위해 '**불변 객체**'를 만들어서 사용한다.
  - 객체의 방어적 복사(defensive copy)를 통해 원본 객체를 deep copy하여 새로운 객체를 생성하고 **재할당으로 교체**한다.

## 함수 형태

- 즉시 실행 함수 : 정의와 동시에 실행되는 함수
  - 그룹 연산자(`()`) 안에 기명/익명 함수 정의
    ```javascript
    (function () {
      //...
    })();
    ```
    - 이 때, 함수 리터럴로 평가되고 식별자에 할당하지 않으므로 다시 호출할 수 없다.
  - 즉시 실행 기호 사용
    ```javascript
    !function () {
      //...
    };
    +function () {
      //...
    };
    ```
  - 인자를 전달하고 값을 반환할 수 있다.
    ```javascript
    var res = (function (a, b) {
      return a * b;
    })(3, 5);
    console.log(res); // 15
    ```
- 재귀 함수 : 재귀 호출하는 함수
  - 반드시 **탈출 조건**을 만들어야 함
  - 너무 많은 재귀 호출에 의해 stack overflow error가 발생하지 않도록 주의
- 중첩 함수(또는 내부 함수) : 함수 내부에 정의된 함수
  - 외부 함수 : 중첩 함수를 포함하는 함수
- 콜백 함수 : Parameter를 통해 다른 함수 내부로 전달되는 함수
  - 함수에서 상황에 따라 변경되는 로직을 추상화해서 함수 내부로 전달됨
  - 고차 함수(high-order function) : 외부에서 콜백 함수를 전달받는 함수 (e.g. `map()`, `filter()`, `reduce()`)
