# Prototype

- Javascript는 prototype 기반 객체 지향 언어

## Summary

- Prototype은 생성자 함수가 생성한 객체들에게 property 및 method를 **상속**해주기 위한 객체이다. (~= 상속 관계에서 상위 객체)
- Prototype을 통해 객체간에 **같은 코드가 중복 생성되는 문제**를 막을 수 있다.
- 모든 객체는 prototype을 간접 참조하는 `__proto__`를 갖는다.
  - 직접 상속을 통해 생성한 객체는 prototype을 갖지 않으므로 `__proto__`를 직접 사용하지 않는게 좋다.
  - `Object.getPrototypeOf()`, `Object.setPrototypeOf()`를 사용하는게 안전하다.
- 리터럴 표기법으로 생성한 객체들은 **가상 생성자 함수의 prototype**을 상속받는다.
  - 객체 리터럴 : `Object.prototype`
  - 함수 리터럴 : `Function.prototype`
- 생성자 함수로 생성한 객체들은 생성자 함수의 prototype을 상속받는다.
  - `SomeConstructorFunction.prototype`
- Prototype은 생성자 함수에 의해 생성되므로, object / constructor / prototype은 모두 연결되어 있다.

  ```js
  /* [ Object -> Prototype ]
    - 생성자 함수 : Object
    - Prototype : Object.prototype
  */
  const obj = {};
  obj.__proto___;

  /* [ Constructor -> Prototype ]
    - 생성자 함수 : Foo
    - Prototype : Foo.prototype
  */
  function Foo() {}
  Foo.prototype;

  /* [ Prototype -> Constructor ]
    - 생성자 함수 : Person
    - Prototype : Person.prototype
  */
  function Person(name) {
    this.name = name;
  }
  const me = new Person("Kim");
  console.log(me.constructor === Person);
  ```

  - `obj.__proto__` -> prototype 참조 (`__proto__`는 prototype으로부터 상속받은 것)
  - `SomeConstructorFunction.prototype` -> 생성할 객체의 prototype 참조
  - `functionObj.constructor` -> 객체를 생성한 생성자 함수 참조 (`constructor`는 prototype으로부터 상속받은 것)

## 객체지향 프로그래밍(Object Oriented Programming)

- 추상화(abstraction) : 필요한 속성들만 간추려 내는 것
- 객체(object) : 상태(state)와 동작(behavior)을 하나의 단위로 묶은 자료구조
  - 상태 == 속성(attribute/property)
  - 동작 == 행위(method)

## 상속과 Prototype

- 상속(inheritance) : object의 property와 method를 다른 object와 공유하는 것
- Javascript는 **prototype 기반 상속**으로 불필요한 중복을 제거한다.
  - 생성자 함수를 통해 동일한 구조의 객체를 생성하는 코드를 재사용 할 수 있었다.
  - 하지만, 생성자 함수를 통해 객체를 만들 때 마다 완전히 똑같은 method를 여러 개 생성하여 불필요하게 메모리를 차지한다는 문제가 있다.
  - Javascript는 객체들이 property 및 method를 각자 생성하지 않고 prototype에 정의된 property 및 method를 재사용할 수 있게 한다.
    ```js
    function Circle(radius) {
      this.radius = radius;
    }
    Circle.prototype.getArea = function () {
      return Math.PI * this.radius ** 2;
    };
    const circle = new Circle(5);
    circle.getArea(); // `getArea()`는 Circle 생성자 함수가 만드는게 아닌 prototype에서 상속받은 method이다.
    ```
  - 즉, prototype은 일반적인 프로그래밍 언어에서의 **상위(부모) 객체** 역할을 한다.

## Prototype Object 내부 동작

- Prototype object == Prototype == `[[Prototype]]` 내부 슬롯에 저장된 객체
- 모든 object는 **prototype의 참조**를 저장하고 있는 `[[Prototype]]` 내부 슬롯을 가진다.
  - `[[Prototype]]` 내부 슬롯의 값이 `null`인 객체는 prototype이 없다.
- **객체와 prototype과 생성자 함수는 서로 연결되어 있다.**
  - 모든 객체는 prototype을 갖는다.
    - 객체는 `__proto__` access property로 prototype 간접 참조
  - 모든 prototype은 생성자 함수와 연결되어 있다.
    - Prototype은 `constructor` property로 생성자 함수 참조
    - 생성자 함수는 `prototype` property로 prototype 참조

### `__proto__`

- 모든 객체는 `__proto__` 접근자 property를 갖는다.
- 객체는 `__proto__` access property로 prototype을 간접 참조한다.
  ```js
  console.log({}.__proto__ === Object.prototype); // true
  ```
- `__proto__`는 getter/setter 접근자 함수를 통해 prototype을 참조하거나 할당한다.
  ```js
  const obj = {};
  const parent = { x: 1 };
  obj.__proto__; // getter 호출 (get __proto__)
  obj.__proto__ = parent; // setter 호출 (set __proto__)
  console.log(obj.x); // 1
  ```
- 객체가 `__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유
  - `[[Prototype]]` 내부 슬롯에 직접 참조하고 객체를 할당하면 **순환 참조** 문제가 발생함
  - 서로가 자신의 프로토타입이 되는 비정상적인 프로토타입 체인이 만들어져 **무한 루프**에 빠진다.
  - `__proto__`를 통하면 순환 참조가 발생할 때 `TypeError: Cyclic __proto__ value` error를 띄운다.
  ```js
  const parent = {};
  const child = {};
  child.__proto__ = parent;
  parent.__proto__ = child; // TypeError: Cyclic __proto__ value
  ```
- `__proto__`대신 `Object.getPrototypeOf()`, `Object.setPrototypeOf()` 사용이 권장된다.
  - 모든 객체가 `__proto__`를 사용할 수 있는건 아니기 떄문
  - 직접 상속을 통해 `Object.prototype`을 상속받지 않는 객체는 `__proto__` 접근자 프로퍼티를 사용할 수 없다.

### 함수 객체의 `prototype` property

- `prototype` property는 **constructor인 함수 객체**가 생성하는 인스턴스의 prototype을 참조한다.
- 생성자 함수(constructor)가 생성하는 객체도 `__proto__`를 가지므로, `__proto__`와 `prototype`은 **같은 프로토타입을 가리킨다.**
  ```js
  function Person(name) {
    this.name = name;
  }
  const me = new Person("Kim");
  console.log(Person.prototype === me.__proto__); // true
  ```

### 프로토타입의 `constructor` property

- 모든 prototype은 `constructor` property를 갖는다.
- `constructor`는 `prototype` property로 자신을 참조하고 있는 constructor를 가리킨다.
  ```js
  function Person(name) {
    this.name = name;
  }
  const me = new Person("Kim");
  console.log(me.constructor === Person);
  ```
- 이 연결은 constructor가 생성될 때(**함수 객체가 생성될 때**) 만들어진다.

### 리터럴로 생성된 객체의 생성자 함수와 Prototype

- 객체 / 생성자 함수 / 프로토타입은 모두 연결되어 있다.
  - 객체 : `__proto__`로 prototype 참조
  - 생성자 함수 : `prototype`으로 prototype 참조
  - Prototype : `constructor`로 자신을 참조하는 생성자 함수 참조
- 객체를 리터럴로 생성할 때는 생성자 함수를 사용하지 않지만 `constructor`로 `Object` 생성자 함수에 접근할 수 있다.

  ```js
  // 생성자 함수가 아닌 객체 리터럴로 만든 객체이지만 `Object` 생성자 함수를 참조한다.
  const obj = {};
  console.log(obj.constructor === Object);

  // 생성자 함수가 아닌 함수 선언문으로 만든 함수 객체이지만 `Function` 생성자 함수를 참조한다.
  function foo() {}
  console.log(foo.constructor === Function);
  ```

- 리터럴로 생성된 객체도 **상속**을 위해 prototype이 필요하기 때문에, **가상 생성자 함수**를 가진다.
  - Prototype은 생성자 함수와 더불어 생성되기 때문
  - 즉, prototype과 생성자 함수는 항상 쌍(pair)으로 존재한다.
- 따라서, 리터럴로 생성된 객체들도 생성자 함수로 생성된 객체와 **객체로서 동일한 특성**을 갖는다.
- "Prototype의 `constructor`로 연결된 생성자 함수"를 "리터럴 표기법으로 생성한 객체를 만든 생성자 함수"로 생각해도 무방하다.
  - 객체 리터럴의 prototype은 `Object.proottype` (생성자 함수 == `Object`)
  - 함수 리터럴의 prototype은 `Function.proottype` (생성자 함수 == `Function`)
  - 배열 리터럴의 prototype은 `Array.proottype` (생성자 함수 == `Array`)
  - 정규 표현식 리터럴의 prototype은 `RegExp.proottype` (생성자 함수 == `RegExp`)
