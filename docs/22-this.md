# this

## Summary

- **함수 호출 방식**에 따라 `this`가 참조하는 객체가 달라진다.
- 일반 함수 호출 -> 전역 객체 (strict mode에서 `undefined`)
- Method 호출 -> 호출한 객체
- 생성자 함수 호출 -> 생성자 함수가 생성할 객체
- `call`, `apply`, `bind` 간접 호출 -> 인수로 전달한 객체

## this

- 자신이 속한 객체(method 내부) 또는 자신이 생성할 인스턴스(생성자 함수 내부)를 가리키는 **자기 참조 변수**
- **객체의 property와 method에 접근하기 위해** Javascript가 암묵적으로 생성하는 식별자
- `this` 바인딩은 **함수 호출 방식**에 따라 동적으로 결정된다.

## 일반 함수 호출

- 일반 함수로 호출될 때는 함수 내부 `this`에 **전역 객체**가 바인딩된다.
  ```js
  function foo() {
    console.log(this); // window(browser) or global(Node.js)
    function bar() {
      console.log(this); // window(browser) or global(Node.js)
    }
    bar();
  }
  foo();
  ```
- `this`는 객체의 property와 method를 참조하는 것이 목적이므로, 이 경우에 `this`는 의미가 없다.
  - 이 때, 전역 객체가 할당된다는 것은 문맥상 맞지 않음
  - Strict mode를 적용하면 전역 객체가 아니라 `undefined`가 바인딩 된다.
- 중첩 함수나 콜백 함수도 일반 함수로 호출될 때 내부 `this`는 전역 객체가 바인딩된다.
  ```js
  var value = 1;
  const obj = {
    value: 100,
    foo() {
      console.log(this); // `obj` 객체 ({value: 100, foo: f})
      setTimeout(function () {
        console.log(this); // window
        console.log(this.value); // 1 -> 전역 객체에서 접근되는 전역 변수를 참조
      });
    },
  };
  obj.foo();
  ```
- 중첩 함수와 콜백 함수는 외부 함수를 돕는 헬퍼 함수의 역할을 하므로, `this`가 외부 함수의 `this`와 다르게 바인딩되면 문제가 생길 수 있다.
- 따라서, 중첩 함수 또는 콜백 함수를 _헬퍼 함수로_ 사용하는 경우 `this`를 외부 함수의 `this`와 일치시켜야 한다.

### 지역 변수 사용

외부 함수의 `this` 참조를 지역 변수에 담아서 내부 참조로 전달한다.

```js
var value = 1;
const obj = {
  value: 100,
  foo() {
    console.log(this); // `obj` 객체 ({value: 100, foo: f})

    var _this = this; // 외부 함수의 this 참조를 지역 변수로 저장
    setTimeout(function () {
      console.log(_this); // `obj` 객체 ({value: 100, foo: f})
      console.log(_this.value); // 100 -> obj 객체로 property 참조
    }, 100);
  },
};
obj.foo();
```

### call/apply/bind 함수 사용

`Function.prototype.call/apply/bind` 함수를 사용하면 특정 함수의 `this`에 바인딩되는 객체를 지정할 수 있다.

```js
var value = 1;
const obj = {
  value: 100,
  foo() {
    setTimeout(
      function () {
        console.log(this); // `obj` 객체 ({value: 100, foo: f})
        console.log(this.value); // 100 -> obj 객체로 property 참조
      }.bind(this), // 콜백 함수의 this를 외부 함수의 this로 교체
      100
    );
  },
};
obj.foo();
```

### 화살표 함수 사용

- 화살표 함수 내부의 `this`는 상위 scope의 `this`를 가리킨다.
- 콜백 함수의 상위 scope는 `foo` method의 지역 scope이므로 같은 `this`로 바인딩된다.

```js
var value = 1;
const obj = {
  value: 100,
  foo() {
    setTimeout(() => console.log(this.value), 100); // 100
  },
};
obj.foo();
```

## Method 호출

- 객체에 정의한 method를 호출했을 때 내부 `this`에는 **호출한 객체**가 바인딩된다.

  ```js
  const person = {
    name: "kim",
    getName() {
      return this.name;
    },
  };
  console.log(person.getName()); // kim -> `person`이 호출했으므로 `person` 객체의 `name`에 접근

  const getName = person.getName;
  console.log(getName()); // '' -> 전역 변수에 할당했으므로 전역 객체가 호출한 것. 즉, `window.name`에 접근
  ```

- Prototype method 내부의 `this`도 일반 메서드와 같이 **호출한 객체**에 바인딩된다.

  ```js
  function Person(name) {
    this.name = name;
  }
  Person.prototype.getName = function () {
    return this.name;
  };

  const me = new Person("Kim");

  /* Prototype method를 호출했지만 `me` 객체가 호출하고 있으므로
   * `me` 객체에 정의된 `name`에 접근한다.
   */
  console.log(me.getName()); // "Kim"

  Person.prototype.name = "Lee";
  /* Person.prototype이 호출하고 있으므로 Person.prototype.name에 접근한다. */
  console.log(Person.prototype.getName()); // "Lee"
  ```

## 생성자 함수 호출

생성자 함수 내부의 `this`는 **생성자 함수가 생성할 인스턴스**가 바인딩된다.

```js
function Circle(radius) {
  /* 생성자 함수가 생성할 `circle` 객체가 바인딩 되어 있다.
   * `circle`은 `radius`가 5이므로 `getDiameter()`를 호출하면 10이 반환된다.
   */
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

const circle = new Circle(5);
console.log(circle.getDiameter()); // 10
```

## Function.prototype.apply/call.bind Method에 의한 간접 호출

```js
function getThisBinding() {
  console.log(arguments);
  return this;
}

const thisObj = { a: 1 };
console.log(getThisBinding.apply(thisObj, [1, 2, 3]));
console.log(getThisBinding.call(thisObj, 1, 2, 3));
console.log(getThisBinding.bind(thisObj)()); // -> bind는 함수를 호출하지 않으므로 명시적으로 호출
```

- `call`과 `apply` : `this`로 사용할 객체와 인수 리스트를 전달받아 **함수를 호출한다.**
  - `call` : 인수 리스트를 콤마(`,`)로 구분
  - `apply` : 인수 리스트를 array로 구분
- `bind` : `this`로 사용할 객체를 인수로 전달해서 설정한다. 단, **함수를 호출하지 않는다.**
- `bind(obj)`를 사용해서 외부 함수의 `this`와 내부 중첩 함수 또는 콜백 함수의 `this`의 불일치 문제를 해결한다.
  ```js
  const person = {
    name: "Kim",
    foo(callback) {
      /* `callback`은 콜백 함수이지만 외부 함수 `foo`의 `this`를 전달했으므로
       * `callback` 내부에서도 `this`가 `person` 객체를 참조한다.
       */
      setTimeout(callback.bind(this), 100);
    },
  };
  person.foo(function () {
    console.log(this.name); // Kim
  });
  ```
