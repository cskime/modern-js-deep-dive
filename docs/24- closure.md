# Closure

## Summary

- 클로저 : 외부 함수보다 오래 유지되고 이미 생명 주기가 종료된 외부 함수의 지역 변수를 참조하는 중첩 함수
- 렉시컬 스코프 관점에서 클로저의 동작
  - 외부 함수의 생명 주기가 먼저 종료되면 외부 함수의 실행 컨텍스트가 실행 컨텍스트 스택에서 pop되어 제거된다.
  - 하지만, 중첩 함수 객체의 `[[Environment]]` 내부 슬롯에 외부 함수의 렉시컬 스코프 참조가 저장되어 있으므로, 외부 함수의 렉시컬 스코프는 가비지 컬렉션 대상에서 제외된다.
  - 따라서, 클로저는 외부 함수가 먼저 종료되더라도 외부 함수의 지역 변수에 계속 접근할 수 있다.
  - 클로저가 외부 함수의 렉시컬 스코프 참조를 계속 유지하더라도, 참조하지 않는 식별자는 브라우저 최적화에 의해 제거된다.
- 생성자 함수를 즉시 실행 함수를 이용해서 클로저로 반환하면, 즉시 실행 함수의 지역 변수를 생성자 함수가 생성하는 객체의 private 변수처럼 만들 수 있다.

## 렉시컬 스코프

- Lexical scope(Static scope)
  - 함수를 정의한 위치에 따라 상위 스코프가 결정되는 것
  - 상위 스코프에 대한 참조가 함수 정의가 평가되는 시점에 함수가 정의된 위치에 의해 결정되는 것
- 스코프의 실체는 실행 컨텍스트의 렉시컬 환경
- Scope chain : 렉시컬 환경이 '외부 렉시컬 환경에 대한 참조'를 통해 상위 렉시컬 환경과 연쇄적으로 연결된 것
- 상위 스코프 : 렉시컬 환경의 '외부 렉시컬 환경에 대한 참조'에 저장할 참조값 == 상위 렉시컬 환경에 대한 참조
- 함수의 상위 스코프를 결정한다 == 렉시컬 환경의 외부 렉시컬 환경에 대한 참조에 저장할 참조값을 결정한다.

## 함수 객체의 `[[Environment]]` 내부 슬롯

- 함수 정의가 평가되어 함수 객체를 생성할 때 `[[Environment]]`에
  - 자신의 정의된 위치에 의해 결정된 상위 스코프의 참조를 저장한다.
  - 현재 실행 중인 실행 컨텍스트의 렉시컬 환경을 저장한다.
- *전역에서 정의*된 함수 선언문이 평가되어 생성되는 함수 객체의 `[[Environment]]` 내부 슬롯에는 *전역 렉시컬 환경의 참조*가 저장된다.
- *함수 내부에서 정의*된 함수 선언문이 평가되어 생성되는 함수 객체의 `[[Environment]]` 내부 슬롯에는 *외부 함수 렉시컬 환경의 참조*가 저장된다.
- 즉, 함수 객체의 `[[Environment]]` 내부 슬롯에 저장된 현재 실행 중인 실행 컨텍스트의 렉시컬 환경의 참조가 상위 스코프 == '외부 렉시컬 환경에 대한 참조'에 저장될 값
- `[[Environment]]` 내부 슬롯의 관점에서 실행 컨텍스트와 렉시컬 환경의 생성 과정
  1. 실행 컨텍스트 생성
  2. 실행 컨텍스트 스택에 push
  3. 렉시컬 환경 생성 후 바인딩
  4. 함수 선언문 평가 후 함수 객체 생성
  5. 함수 객체의 `[[Environment]]`에 현재 렉시컬 환경 등록 (이 함수가 정의되어 평가되는 위치가 현재 실행 컨텍스트이므로)

## 클로저와 렉시컬 환경

- MDN 정의 : 함수와 그 함수가 선언된 렉시컬 환경과의 조합
  - 그 함수가 선언된 렉시컬 환경 == 상위 스코프 == 함수가 선언될 때 실행 중인 실행 컨텍스트의 렉시컬 환경
- Closure : **외부 함수보다 오래 유지**되고 **이미 생명 주기가 종료된 외부 함수의 지역 변수를 참조**하는 중첩 함수
- 두 가지 조건을 모두 만족해야 클로저로 인정된다.
  - 외부 함수보다 오래 유지되더라도, 외부 함수의 지역 변수를 참조하지 않으면 클로저가 아니다. -> 외부 함수의 변수를 참조해야 한다.
  - 외부 함수의 지역 변수를 참조하더라도, 외부 함수보다 먼저 종료되면 클로저가 아니다. -> 외부 함수에서 반환되어야 한다.
- 자유 변수(free variable) : 클로저에 의해 참조되는 상위 스코프 변수
- 클로저가 상위 스코프를 기억하고 있지만 *실제로 참조하는 식별자만 기억하고 나머지는 최적화를 통해 제거*된다.

## 클로저 동작 과정

```js
const x = 1;

// 1.
function outer() {
  const x = 10;
  const inner = function () {
    console.log(x);
  }; // 2.
  return inner;
}

const innerFunc = outer(); // 3.
innerFunc(); // 4. 10
```

**`outer` 함수가 종료된 후에도 `inner` 함수가 생존해 있으면서 `outer` 함수의 지역 변수 식별자에 접근할 수 있다.**

1. `outer` 함수가 평가되어 함수 객체 생성
2. 현재 실행 중인 전역 렉시컬 환경을 `outer` 함수 객체의 `[[Environment]]` 내부 슬롯에 상위 스코프로서 저장
3. `outer` 함수를 호출하면 `outer` 함수의 실행 컨텍스트가 생성되어 스택에 Push되고 렉시컬 환경 생성
4. `outer` 함수 객체의 `[[Environment]]` 내부 슬롯에 저장된 전역 렉시컬 환경을 `outer` 함수의 렉시컬 환경의 "외부 렉시컬 환경에 대한 참조"에 할당
5. `inner` 중첩 함수가 평가되어 함수 객체 생성 (함수 표현식으로 정의했으므로 런타임에 평가)
6. 현재 실행 중인 `outer` 함수 렉시컬 환경을 `inner` 함수 객체의 `[[Environment]]` 내부 슬롯에 상위 스코프로서 저장
7. `outer` 함수의 실행이 종료되면 `inner` 함수를 반환하면서 `outer` 함수의 생명 주기 종료되고 `outer` 함수의 실행 컨텍스트가 스택에저 제거됨(pop)
8. 이 때, `outer` 함수의 렉시컬 환경은 `inner` 함수의 `[[Environment]]` 내부 슬롯에 의해 참조되고 inner`함수는 전역 변수`innerFunc`에 의해 참조됨
9. 따라서, `outer` 함수의 실행 컨텍스트는 제거되지만 렉시컬 환경은 여전히 참조되고 있기 때문에 가비지 컬렉션 대상에서 제외되어 계속 유지된다.
10. `inner` 함수가 `innerFunc`에 의해 호출되면 `inner` 함수의 실행 컨텍스트가 생성되어 스택에 Push되고 `inner` 함수 객체의 `[[Environment]]`에 저장된 참조가 "외부 렉시컬 환경에 대한 참조"에 할당됨
11. `inner` 함수는 상위 스코프를 따라 `outer` 함수의 렉시컬 환경에 접근하고, 식별자 `x`의 값을 참조해서 console에 출력한다.

## 클로저의 활용

### 상태(state)를 안전하게 변경하고 유지하는 목적

- 클로저를 사용해서 상태를 안전하게 **은닉**(information hiding)하고 특정 함수에게만 상태 변경을 허용한다.
- 즉, 클로저는 **이전 상태를 유지**하면서 **상태를 은닉**하여 **의도치 않은 상태 변경을 막고 특정 함수만 변경 가능**하게 만들기 위해 사용한다.
  ```js
  const counter = (function () {
    let num = 0; // state
    // 클로저 반환
    return function () {
      return ++num;
    };
  })();
  console.log(increase()); // 1
  console.log(increase()); // 2
  console.log(increase()); // 3
  ```
  - 상위 스코프인 즉시 실행 함수의 렉시컬 환경에 저장된 변수에 접근
  - 즉시 실행 함수가 실행된 후 곧바로 종료되어도 즉시 실행 함수의 렉시컬 환경은 `counter`로 반환되는 내부 함수에 의해 참조가 유지됨
  - 따라서, 클로저 `counter`를 연달아 실행하면 즉시 실행 함수의 렉시컬 환경에 저장된 상태 변수 `num`의 값을 계속 증가시킨다.
- 함수 대신 객체를 반환하는 예시
  ```js
  const counter = (function () {
    let num = 0; // state
    return {
      increase() {
        return ++num;
      },
      decrease() {
        return --num;
      },
    };
  })();
  console.log(increase()); // 1
  console.log(increase()); // 2
  console.log(decrease()); // 1
  console.log(decrease()); // 0
  ```
  - 반환되는 객체 리터럴은 스코프를 갖지 않는다.
  - 즉, increase와 decrease method의 상위 스코프는 즉시 실행 함수의 렉시컬 환경이다.
  - 따라서, increase와 decrease method는 즉시 실행 함수의 렉시컬 환경에 저장된 상태 변수 `num`을 공유한다.

### 함수형 프로그래밍에서 가변 데이터를 피하고 불변성을 지향하여 Side Effect를 최소화하려는 목적

```js
function makeCounter(aux) {
  let counter = 0; // free variable
  return function () {
    counter = aux(counter); // delegate the state change to the sub function `aux`
    return counter;
  };
}

// Sub Functions
function increase(n) {
  return ++n;
}

function decrease(n) {
  return --n;
}

const increaser = makeCounter(increase);
console.log(increaser()); // 1
console.log(increaser()); // 2

const decreaser = makeCounter(decrease);
console.log(decreaser()); // -1
console.log(decreaser()); // -2
```

- `makeCounter(aux)`는 `makeCounter` 함수의 스코프에 속한 `counter` 변수를 기억하는 클로저를 반환한다.
- `makeCounter(aux)`를 호출할 때 마다 새로운 `makeCounter` 함수 실행 컨텍스트의 렉시컬 환경이 생성된다.
- 즉, `makeCounter(aux)`가 각각 생성한 `increaser`와 `decreaser` 클로저는 **자신만의 독립된 렉시컬 환경**을 갖는다.
- 따라서, `increaser`와 `decreaser`는 **자유 변수 `counter`를 서로 공유하지 못한다.**
- 렉시컬 환경을 공유하는 `counter` 클로저를 만들기 위해서는 `makeCounter`가 두 번 이상 호출되면 안된다.
- 함수가 **한 번만 호출되는 것을 보장**하기 위해 즉시 실행 함수를 사용한다.

  ```js
  const counter = (function () {
    let counter = 0;
    return function (aux) {
      counter = aux(counter);
      return counter;
    };
  })();

  // Sub Functions
  function increase(n) {
    return ++n;
  }

  function decrease(n) {
    return --n;
  }

  console.log(counter(increase)); // 1
  console.log(counter(increase)); // 2
  console.log(counter(decrease)); // 1
  console.log(counter(decrease)); // 0
  ```

## 캡슐화와 정보 은닉

- 캡슐화(encapsulation) : 객체의 상태(state)를 나타내는 property와 property를 참조하고 조작하는 동작인 method를 하나로 묶는 것
- 캡슐화를 정보 은닉(information hiding)을 위해 사용하기도 한다. (private, protected, public)
- Javascript는 접근 제한자가 없으므로 객체의 모든 property와 method가 외부에 공개된다.
- 즉시 실행 함수 등으로 은닉을 구현할 수 있어 보이지만, **private 변수의 상태가 의도치 않게 외부에서 변경되는 경우가 존재**하므로 근본적인 해결 방법이 아니다.

  ```js
  // Person은 클로저로 반환된다.
  const Person = function () {
    let _age = 0; // private
    function Person(name, age) {
      this.name = name; // public
      _age = age;
    }
    Person.prototype.sayHi = function () {
      console.log(`Hi! I'm ${_age}`);
    };
    return Person;
  };
  const me = new Person("kim", 20);
  me.sayHi(); // Hi! I'm 20
  const you = new Person("lee", 30);
  you.sayHi(); // Hi! I'm 30
  me.sayHi(); // Hi! I'm 30 -> _age 값이 변경된다.
  ```

  - `Person`은 단 한번만 실행되어 생성된 클로저이다.
  - 따라서, 이 클로저를 통해 생성한 `me`, `you`의 `sayHi()` 함수는 상위 스코프가 **즉시 실행 함수의 렉시컬 환경**으로 동일하다.
  - 즉, `me`와 `you`의 `sayHi()`는 `_age` 변수를 공유하기 때문에 `you`를 생성할 때 `_age`를 `30`으로 변경하면 `me`도 영향을 받는다.

## 클로저와 함수 레벨 스코프

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // 3 3 3
}
```

- 위 코드에서 `for`문 변수 `i`는 `var` keyword로 선언했으므로 함수 레벨 스코프를 갖기 때문에 *전역 변수로 선언*된다.
- 따라서, 첫 번째 `for`문이 종료되면 전역 변수 `i`는 `3`이 된다.
- 두 번째 `for`문이 `funcs`를 순회하며 저장된 함수를 호출하면, 각 함수들은 **전역 변수 `i`**를 반환하므로 모두 3으로 출력될 것이다.
- 이 문제를 해결하기 위해, **함수가 반환하는 값이 전역 변수로 선언되지 않도록** 스코프를 변경한다.

```js
var funcs = [];

for (var i = 0; i < 3; i++) {
  funcs[i] = (function (id) {
    return function () {
      return id;
    };
  })(i);
}

for (var j = 0; j < funcs.length; j++) {
  console.log(funcs[j]()); // 0 1 2
}
```

- 즉시 실행 함수를 사용해서 `funcs[i]`에 함수를 저장한다.
- 즉시 실행 함수가 반환하는 함수는 상위 스코프인 *즉시 실행 함수의 렉시컬 스코프*에 저장된 매개변수 `id`를 참조하는 클로저다.
- `for`문이 실행되면 각 loop마다 즉시 실행 함수가 새로운 클로저를 생성하므로, `funcs` 배열에 저장된 각 클로저들은 **독립된 스코프를 갖는다.**
- 따라서, 두 번째 `for`문을 실행하면 각 클로저들이 자신의 독립된 스코프에 저장된 `id` 식별자의 값을 참조한다.
- 각 클로저의 `id` 식별자는 `i`가 순차적으로 증가함에 따라 0, 1, 2가 저장되므로 정상적으로 출력된다.
- 이 문제는 ES6+에서 `let`, `const`를 사용하여 블록 레벨 스코프를 갖도록 만들면 쉽게 해결할 수 있다.

```js
const funcs = [];

for (let i = 0; i < 3; i++) {
  funcs[i] = function () {
    return i;
  };
}
```

- `i`는 `for`문이 실행될 때 마다 블록 내부 지역 변수로 선언되므로 `funcs` 배열의 다른 클로저들과 **독립된 스코프에 저장**된다.
- 고차함수를 활용하여 변수와 반복문을 사용하지 않음으로써 문제를 해결할 수도 있다.

```js
const funcs = Array.from(new Array(3), (_, i) => () => i);
funcs.forEach((f) => console.log(f())); // 0 1 2
```
