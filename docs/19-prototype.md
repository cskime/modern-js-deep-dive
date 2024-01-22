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
- Prototype은 생성자 함수에 의해 생성되므로, **object / constructor / prototype은 모두 연결**되어 있다.

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
  - `__proto__`은 순환 참조가 발생할 때 `TypeError: Cyclic __proto__ value` error를 발생시켜 순환 참조를 막는다.
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
- *Prototype의 `constructor`로 연결된 생성자 함수*를 *리터럴 표기법으로 생성한 객체를 만든 생성자 함수*로 생각해도 무방하다.
  - 객체 리터럴의 prototype은 `Object.proottype` (생성자 함수 == `Object`)
  - 함수 리터럴의 prototype은 `Function.proottype` (생성자 함수 == `Function`)
  - 배열 리터럴의 prototype은 `Array.proottype` (생성자 함수 == `Array`)
  - 정규 표현식 리터럴의 prototype은 `RegExp.proottype` (생성자 함수 == `RegExp`)

## Prototype 생성 시점

- Prototype은 생성자 함수와 항상 쌍으로 존재하므로, 생성자 함수가 생성되는 시점에 함께 생성된다.
  - 생성자 함수는 `prototype` property로 prototype 객체 참조
  - Prototype은 `constructor` property로 생성자 함수 참조
- **객체가 생성되기 이전에** 생성자 함수와 prototype이 이미 객체화되어 존재한다.
  - 사용자 정의 생성자 함수
    - 생성자 함수 정의가 평가되어 **함수 객체를 생성하는 시점**에 prototype도 함께 생성된다.
      ```js
      /* 함수 선언문은 런타임 이전에 평가되어 함수 객체가 생성되고, 이 때 prototype도 생성됨
       * 따라서, 함수 선언문 이전에 prototype에 참조할 수 있다.
       */
      console.log(Person.prototype);
      function Person(name) {
        this.name = name;
      }
      ```
    - 이 때 생성된 prototype의 prototype은 `Object.prototype` => _prototype chain_
    - Non-constructor는 prototype이 생성되지 않는다.
      ```js
      const Person = (name) => {
        this.name = name;
      };
      console.log(Person.prototype); // undefined
      ```
  - Built-in 생성자 함수
    - 빌트인 생성자 함수의 객체가 생성될 때 prototype도 함께 생성된다. -> **전역 객체 생성 시점**
- 이후 생성자 함수 또는 객체 리터럴에 의해 생성된 객체의 prototype에 할당된다. 즉, **객체가 prototype을 상속받는다.**

## Prototype 결정 방식

- 객체의 prototype은 **객체 생성 방식**에 따라 결정된다.
  - 객체 리터럴 : `Object.prototype`
  - Object 생성자 함수 : `Object.prototype`
  - 사용자 정의 생성자 함수 : `MyConstructor.prototype` (생성자 함수의 `prototype`에 바인딩 되어 있는 객체)
  - Object.create 메서드
  - 클래스(ES6)
- 추상 연산 `OrdinaryObjectCreate`에 전달되는 prototype 인수가 객체의 prototype이 된다.
  - `OrdinaryObjectCreate`의 동작
    1. 빈 객체 생성
    2. 인수로 전달받은 property 목록을 객체에 추가
    3. 인수로 전달받은 prototype을 생성한 객체의 prototype으로 할당
    4. 생성한 객체 반환

### 객체 리터럴과 Object 생성자 함수의 차이

- 객체 리터럴은 property를 객체 리터럴 내부에 추가한다.
- Object 생성자 함수는 빈 객체를 먼저 생성하고 property를 나중에 추가한다.

## Prototype Chain

- 객체와 상위 prototype 객체들이 연결되어 있는 것
- Javascript engine은 property/method를 호출할 때 **prototype chain을 검색**한다.
  1. 객체의 property에 접근할 때, 해당 객체에 property가 없으면 prototype chain을 따라 거슬러 올라가며 찾는다.
     - Prototype도 객체이므로 상위 prototype을 갖는다.
  2. Prototype에서 property를 발견하면 그 property에 접근한다.
     - 이 때, 호출되는 property가 참조하는 function의 `this`는 property를 호출한 최초 객체가 바인딩 되어 있다. (간접 호출)
  3. Prototype chain에서 property를 찾지 못하면 `undefined`를 반환한다.
     - Chain의 최상위 객체(**종점**)는 `Object.prototype`이다.
     - `Object.prototype`의 prototype은 `null`이다.
- 즉, **prototype은 상속과 property 검색 메커니즘**이다.
  - Scope chain은 식별자를 검색하는 메커니즘 (전역에서 선언된 property는 전역 scope에서 검색)
  - **먼저 scope chain을 통해 식별자를 찾고, 그 식별자가 참조하는 객체에서 prototype chain을 통해 property를 검색**한다.

## Override와 Property Shadowing

- **Property shadowing** : Prototype method와 같은 이름(식별자)의 instance method를 선언하면 prototype method가 가려지는 현상 (overriding과 비슷)
  - Prototype property/method : Prototype이 소유한 property/method
  - Instance property/method : 객체 instance가 소유한 property/method
- 하위 객체를 통해 prototype의 property를 변경/삭제할 수 없다. (get access는 허용되지만 set access는 허용되지 않는다.)
  ```js
  delete me.sayHello; // instance method 삭제
  delete Person.prototype.sayHello; // prototype method 삭제
  ```

## Prototype 교체

- Prototype도 객체이므로, 다른 객체로 동적으로 변경할 수 있다.
- 직접 생성한 객체로 prototype을 교체하면 **생성자 함수와 prototype간 연결이 파괴**되어 직접 연결하는 작업이 필요하다.
- 따라서, Prototype은 직접 교체하지 않는 것이 좋다.

### 생성자 함수에 의한 교체

```js
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  Person.prototype = {
    constructor: Person, // connect
    sayHello() {
      console.log(`Hi! I'm ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("kim");
```

- **생성자 함수와 연결된 prototype** 교체 (*객체 생성 이전*에 교체)
- 생성자 함수는 교체된 prototype과 `prototype` property로 연결되어 있다.
- 교체된 prototype은 직접 생성했으므로 `constructor`가 암묵적으로 생성되지 않는다.
- 즉, 생성자 함수와의 연결이 파괴되었으므로 `constructor` property를 추가해서 직접 연결해야 한다.

### 인스턴스에 의한 교체

```js
function Person(name) {
  this.name = name;
}

const me = new Person("kim");
const parent = {
  constructor: Person, // connection 2
  sayHello() {
    console.log(`Hi! I'm ${this.name}`);
  },
};

Person.prototype = parent; // connection 1

Object.setPrototypeOf(me, parent);

me.sayHello();
```

- **객체에 연결된 prototype** 교체 (*객체 생성 이후*에 교체)
- 생성자 함수는 교체된 prototype과 연결이 파괴되었으므로 `prototype` property로 직접 연결해야 한다. (위 예시에서 `connection 1`)
- 교체된 prototype은 직접 생성했으므로 `constructor`가 암묵적으로 생성되지 않는다.
- 마찬가지로 `constructor` property를 추가해서 직접 연결해야 한다. (위 예시에서 `connection 2`)

## instanceof

- `obj instanceof 생성자함수` : `생성자함수`가 `obj`의 prototype chain 상에 존재하는지 평가하는 연산자
- 즉, **생성자 함수의 prototype에 바인딩된 객체가 prototype 상에 존재하는지** 확인하는 것

  ```js
  function instanceof(instance, constructor) {
    const prototype = Object.getPropertyOf(instance);

    // 종점에 다다를 때 까지 찾지 못하면 `false` 반환
    if (prototype === null)
      return false;

    // instance의 prototype chain을 거슬러 올라가면서 constructor의 prototype을 찾으면 `true` 반환
    return prototype === constructor.prototype || instanceof(prototype, constructor);
  }
  ```

- 객체의 prototype을 변경하는 경우

  ```js
  function Person(name) {
    this.name = name;
  }

  const me = new Person("kim");
  const parent = {};

  Object.setPrototypeOf(me, parent); // 객체의 prototype 변경

  console.log(me instanceof Person); // 1: false
  console.log(me instanceof Object); // 2: true

  Person.prototype = parent; // 생성자 함수와 직접 생성한 prototype 연결

  console.log(me instanceof Person); // 3: true
  ```

  1. `me` 객체의 prototype을 변경했으므로 `Person.prototype`은 chain상에 존재하지 않으므로 `false` 반환
  2. Prototype chain의 종점에는 `Object.prototype`이 있으므로 `true` 반환
  3. `me` 객체의 prototype chain을 `Person` 생성자 함수의 prototype으로 변경했으므로 `true` 반환

- 생성자 함수 안에서 prototype을 변경할 때는 `instanceof` 연산자의 결과가 동일하다.
- 생성자 함수 안에서는 **객체가 생성되기 이전에** prototype을 변경하기 때문에, **생성된 객체의 prototype에는 이미 생성자 함수의 prototype이 할당**되어 있다.

  ```js
  const Person = (function () {
    function Person(name) {
      this.name = name;
    }

    // 여기서 변경한 object가 생성자 함수가 생성하는 객체의 prototype으로 사용된다.
    Person.prototype = { ... }

    return Person;
  })();

  const me = new Person("kim");

  console.log(me instanceof Person); // true
  ```

## 직접 상속

### Object.create()

- `Object.create(prototype, {property: descriptor})` : 명시적으로 prototype을 지정해서 객체 생성

  ```js
  // prototype chain의 종점에 위치하는 객체 생성
  // chain : obj -> null
  let obj = Object.create(null);

  // `obj = {}`와 동일
  // chain : obj -> Object.prototype
  let obj = Object.create(Object.prototype);

  // `obj = {x:1}`과 동일
  // chain : obj -> Object.prototype
  let obj = Object.create(Object.prototype, {
    x: { value: 1, writable: true, enumerable: true, configurable: true },
  });

  // chain : obj -> prototype -> Object.prototype -> null
  const prototype = { x: 10 };
  let obj = Object.create(prototype);

  // `obj = new Person("Kim")`과 동일
  // chain : obj -> Person.prototype -> Object.prototype -> null
  function Person(name) {
    this.name = name;
  }
  let obj = Object.create(Person.prototype);
  ```

  - `new` 연산자 없이 객체 생성 가능
  - Prototype을 지정하면서 객체 생성 가능
  - 객체 리터럴에 의해 생성된 객체도 상속 가능

- ESLint에서 객체가 `Object.prototype`의 method들을 직접 호출하는 것을 금지하는 이유
  - `Object.prototype`은 객체의 prototype chain 종점에 있는 객체로, prototype이 `null`이다.
  - 직접 상속에 의해 prototype이 `null`인 객체를 생성하고 prototype chain의 종점으로 변경할 수 있다.
  - 이 경우, 객체가 `Object.prototype`에 정의된 method(e.g. `hasOwnProperty()`)를 상속받지 못하므로 직접 사용할 수 없는 상황이 된다.
  - 따라서, `Object.prototype`을 통해 간접적으로 호출하는게 좋다.
    ```js
    const obj = Object.create(null);
    obj.a = 1;
    console.log(obj.hasOwnProperty("a")); // TypeError: obj.hasOwnProperty is not a function
    console.log(Object.prototype.hasOwnProperty.call(obj, "a")); // true
    ```

### 객체 리터럴의 `__proto__`를 사용한 직접 상속

- `Object.create()`는 두 번째 인자로 property를 정의하기 번거롭기 때문에 일단 빈 객체를 만들어서 property를 동적으로 추가한다.
- 객체 리터럴은 내부에서 `__proto__`를 정의해서 직접 상속을 구현할 수 있다.
  ```js
  const parent = { x: 10 };
  const obj = {
    __proto__: parent,
    y: 20,
  };
  console.log(Object.getPrototypeOf(obj) === parent); // true
  ```

## Static Property/Method

- 생성자 함수로 instance를 생성하지 않아도 참조/호출할 수 있는 property/method
- 생성자 함수에 property/method를 직접 정의해서 참조/호출한다.

  ```js

  ```
