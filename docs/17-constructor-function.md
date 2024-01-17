# Create a Object by the Constructor Function

## Summary

- 함수 선언문, 함수 표현식, 클래스에서 `new` 생성자를 사용하면 생성자 함수(constructor)로 동작한다.
- 생성자 함수는 암묵적으로 빈 객체를 만들고 동적으로 property 및 method를 정의한 뒤 `return` 없이 암묵적으로 해당 객체를 반환한다.
- 이 때, 함수 내부에서 `this`로 암묵적으로 생성된 객체 인스턴스에 접근할 수 있다.
- 생성자 함수를 `new` 없이 호출하는 실수를 막기 위한 방법
  - 생성자 함수 이름에 파스칼 케이스 적용
  - Scope-safe constructor pattern을 적용하여 `new` 없이 호출하면 내부적으로 `new` 연산자와 함께 재귀호출하여 **항상 객체가 반환되도록** 처리

## Built-in 생성자 함수

- 원시값 : `String`, `Number`, `Boolean`
- 객체 : `Object`, `Function`, `Array`, `Date`, `RegExp`, `Promise`...

## 생성자 함수

- 객체의 property는 상태(state)를 표현하고, method는 동작(behavior)을 표현한다.
- '동작'은 객체마다 동일한 경우가 많으므로, 같은 동작을 여러 번 정의하는 중복 코드가 발생한다.
- 객체 리터럴은 객체 1개를 만들기 좋지만, 같은 형태의 객체 여러 개를 만들기는 어려움
  - 중복 코드가 발생한다.
- 생성자 함수를 사용하면 구조가 동일한 객체를 여러 개 만들기 좋음
  - 변경되는 값(state)만 parameter로 받아서 **중복 코드를 제거**할 수 있다.

### this

- 객체 자신의 property나 method를 참조하기 위한 자기 참조 변수(self-referencing variable)
  - 객체 method 안에서 객체에 정의된 property에 접근하려면 `this`로 접근해야 한다.
  - 이 method는 객체를 통해서만 호출될 수 있으므로, `this`가 해당 객체를 참조할 것이다.
- 함수도 객체이므로, 함수 내에서 `this`를 참조할 수 있다.
  ```js
  function foo() {
    console.log(this);
  }
  ```
  - 일반 함수로 호출되면 전역 객체(`window`) 참조
    ```js
    foo(); // window
    ```
  - 메서드로 호출되면 메서드를 호출한 객체 참조
    ```js
    const obj = { foo };
    obj.foo(); // obj
    ```
  - 생성자 함수로 호출되면 암묵적으로 생성되는 객체 참조
    ```js
    const instance = new foo(); // instance
    ```

### 동작 방식

```js
function Circle(radius) {
  // 1.
  console.log(this); // Circle {}

  // 2.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };

  // 3.
  // return this;
}
```

1. 암묵적으로 빈 객체(instance)를 생성하고, 이것을 함수의 `this`에 바인딩한다.
   - 생성자 함수 가장 처음에 `this`를 참조하면 **빈 객체**가 반환된다.
2. 함수 내부에서 `this`가 참조하는 인스턴스를 초기화한다.
   - 생성자 함수를 사용해서 만들어진 객체는 property 및 method가 **동적으로 추가**된 것이다.
3. 암묵적으로 함수에서 `this`가 참조하는 인스턴스를 반환한다.
   - 명시적으로 `return`을 사용하면, `this` 대신 이 객체가 반환된다.
   - 원시값을 반환하면 무시되고 `this`가 반환된다.

### 내부 동작

- 함수도 객체이므로, 함수는 모든 객체가 갖고 있는 내부 슬롯과 내부 메서드를 동일하게 가진다.
  - getter, setter 등을 동일하게 가진다.
  - 따라서, 함수에도 property와 method를 정의하고 사용할 수 있다.
    ```js
    function foo() {}
    foo.prop = 10;
    foo.method = function () {
      console.log(this.prop);
    };
    foo.method(); // 10
    ```
- 함수는 함수 객체만을 위한 특별한 내부 메서드를 가진다.
  - `[[Call]]` : 함수가 **일반 함수**로서 호출될 때 호출된다.
  - `[[Construct]]` : 함수가 **생성자 함수**로서 호출될 때 호출된다.
- **함수 정의 방식**에 따라 갖고 있는 내부 메서드가 다르다.
  - 함수 선언문, 함수 표현식, 클래스는 `[[Construct]]`를 가진다.
  - **메서드(ES6의 축약), 화살표 함수**는 `[[Construct]]`를 갖지 않는다.
    - 통상적인 method의 의미와 다르다.
    - ECMAScript 사양에 따라 **ES6부터 추가된 메서드 축약 표현**으로 정의된 함수만을 의미한다.
- 갖고 있는 내부 메서드에 따라 함수를 분류한다.
  - `callable` : `[[Call]]`을 가진 함수
  - `constructor` : `[[Construct]]`를 가진 함수
  - `non-constructor` : `[[Construct]]`를 갖지 않는 함수
  - 즉, 모든 함수는 `callable`이고 ES6 메서드와 화살표 함수를 제외한 모든 함수는 `constructor`이다.
- `constructor`만 생성자 함수로 동작할 수 있다. (`non-constructor`가 `new` 연산자를 사용하면 `TypeError` 발생)
  ```js
  // Method
  const arrow = () => {};
  const obj = {
    x() {},
  };
  new arrow(); // TypeError: obj.x is not a constructor
  new obj.x(); // TypeError: obj.x is not a constructor
  ```
- 즉, `new` 연산자의 유무에 따라 호출되는 내부 메서드가 달라진다. (== 함수 호출 방식이 달라진다.)
  - `new` 연산자가 없으면 `[[Call]]`이 호출된다. -> `callable`로 동작
  - `new` 연산자가 있으면 `[[Construct]]`가 호출된다. -> `constructor`로 동작
  - 생성자 함수로 사용하려고 만든 함수를 `new` 연산자 없이 호출하면 `undefined`가 반환된다. (`return`문이 없으므로)
    ```js
    function Circle(radius) {
      this.radius = radius;
      this.getDiameter = function () {
        return 2 * this.radius;
      };
    }
    const circle = Circle(5); // undefined
    ```
    - `new` 연산자가 없으면 `return`이 암묵적으로 생성되지 않으므로 반환값이 없는 함수가 된다.
    - 이 때, `this`는 전역 객체 `window`이므로 `radius`와 `getDiameter`는 `window`의 property, method가 될 것이다.

## 생성자 함수 예외 처리

- 생성자 함수로 사용하려고 만든 함수를 일반 함수처럼 호출하면 의도와 다르게 동작한다.
  - 원시값 생성자 함수
    - `new` 연산자와 함께 호출하면 객체 반환
    - `new` 연산자 없이 호출하면 해당 type의 원시값을 반환
  - 객체 생성자 함수
    - `new` 연산자 유무에 관계 없이 객체 반환 (`new` 연산자 없이 호출해도 내부적으로 `new` 연산자로 재귀 호출)
- 이런 문제를 막기 위한 방법
  - 생성자 함수의 이름은 파스칼 케이스(대문자로 시작)로 짓는다.
    ```js
    // 일반 함수
    function circleDiameter(radius) {
      return radius * 2;
    }
    const diameter = circleDiameter(5); // 25
    // 생성자 함수
    function Circle(radius) {
      this.radius = radius;
      this.getDiameter = function () {
        return 2 * this.radius;
      };
    }
    const circle = new Circle(5);
    circle.getDiameter(); // 25
    ```
  - `this` 참조를 검사한다. => "Scope-safe Constructor Pattern"
    ```js
    function Circle(radius) {
      // 생성자 함수를 `new` 연산자 없이 호출했다면 `this`가 `Circle` 객체의 인스턴스가 아닐 것이다.
      // 이 때, `new` 연산자로 생성자 함수를 재귀호출하여 반드시 객체로 반환될 수 있게 만든다.
      if (!(this instanceof Circle)) {
        return new Circle(radius);
      }
      ...
    }
    ```
  - (ES6+)`new.target` 참조를 검사한다. ("`new`를 사용한 `target`"의 의미로 기억)
    ```js
    function Circle(radius) {
      // `new.target`은 생성자 함수 자기 자신을 참조한다.
      // `new.target`이 `undefined`라면 생성자 함수가 아니므로, `new` 연산자로 재귀호출하여 객체로 반환되도록 만든다.
      if (!new.target) {
        return new Circle(radius);
      }
      ...
    }
    ```
