# 10. Object Literal

## Summary

- Object : 원시 값을 제외한 값
- Object literal : 중괄호(`{}`) 안을 property와 method로 구성한 object 생성 방법
  - Property : key-value로 이루어진 object의 state를 나타내는 data 값
  - Method : value가 function인 property
- Object literal 사용법
  - Property key
    - 문자열 또는 Symbol 사용
      - 문자열이 아닌 값을 사용하면 암묵적으로 `string` type 변환
      - 식별자 네이밍 규칙을 만족한다면 따옴표(`""`) 생략 가능
    - 문자열로 평가될 수 있는 표현식을 사용해서 key를 동적으로 생성 가능
  - Property 사용
    - 마침표(`.`) 또는 대괄호(`[]`)에 key를 사용하여 property에 접근
      - 마침표 접근법은 따옴표(`""`)를 생략한 형태로만 사용 가능
      - 대괄호 접근법을 사용할 경우 항상 따옴표(`""`)로 문자열을 감싸야 함
      - 존재하지 않는 property에 접근하면 `undefined` 반환
    - 존재하지 않는 key에 값을 할당하여 property를 동적으로 추가 가능
    - `delete` keyword와 함께 property를 참조하여 property 삭제
- ES6+ object literal 확장
  - Property 축약 : property value로 사용한 변수가 property key와 이름이 같을 때, key 생략 가능
  - Method 축약 : Method 선언 시 `function` keyword 생략 가능 (다르게 동작하는 method로 선언됨)
  - Computed property name : Object literal을 선언할 때 key에 문자열로 평가되는 표현식 사용 가능

## Object

- 원시 값을 제외한 나머지 값(함수, 배열, 정규 표현식 등)
- Mutable value
- 구성
  - Property : 객체의 상태(state)를 나타내는 값 (data)
  - Method : Property(상태 데이터)를 참조하고 조작할 수 있는 동작 (behavior)

## Object Literal

- `new` 연산자 없이 변수에 할당되는 시점에 object literal을 해석해서 객체 인스턴스 생성
- `{ }` 안에 0개 이상의 property 또는 method를 정의

  ```javascript
  // Object literal
  var person = {
    name: "Kim",
    sayHello: function () {
      console.log(`Hello! My name is ${this.name})`);
    },
  };

  // Empty object
  var empty = {};
  ```

- 중괄호(`{}`)로 된 표현식 뒤에는 세미콜론을 붙이지 않지만, 객체는 값이므로 세미콜론을 붙여야 함

## Property

- Property key : 모든 문자열 또는 Symbol 값 (빈 문자열 포함)
- Property value : Javascript에서 사용할 수 있는 모든 값

### Property Key

- Key는 문자열이므로 따옴표(`""`)로 묶어야 함
- 단, javascript 식별자 네이밍을 따른다면 생략 가능
  ```javascript
  var person = {
    firstName: "Chamsol",
    "last-name": "Kim",
  };
  ```
  - `"last-name"`에 따옴표를 사용하지 않으면 산술 연산 표현식(`last-name`)으로 인식. 이 때, `last`나 `name`이라는 변수를 선언하지 않았다면 `SyntaxError` 발생
- Key 동적 생성 : 대괄호(`[]`) 안에 **문자열로 평가할 수 있는 표현식** 사용

  ```javascript
  // ES5
  var obj = {};
  var key = "Hello";
  obj[key] = "world";

  // ES6+
  var obj = {
    [key]: "world";
  }
  ```

- Key 암묵적 타입 변환 : 문자열이 아닌 값을 key로 사용했을 때 암묵적 타입 변환에 의해 문자열로 사용
- 중복 property가 선언된 경우, 나중에 선언된 property value로 덮어씀
  ```javascript
  var obj = {
    name: "Kim",
    name: "Chamsol",
  };
  console.log(obj); // {name: "Chamsol"}
  ```

### Property 접근

- Property key에 접근하는 방법
  - 마침표 표기법(dot notation) : 점(`.`)을 사용해서 **따옴표 없이** key에 접근
    ```javascript
    var person = { name: "Kim" };
    person.name; // "Kim"
    ```
  - 대괄호 표기법(bracket notation) : 대괄호(`[]`) 안에 **따옴표로 감싼 문자열**을 넣어서 접근
    ```javascript
    var person = { name: "Kim" };
    person["name"]; // "Kim"
    ```
- 존재하지 않는 key로 접근하면 `undefined` 반환
  ```javascript
  var person = { name: "Kim" };
  person.firstName; // undefined
  ```
- **식별자 네이밍 규칙을 따르지 않는 key는 반드시 대괄호 표기법을 사용**해야 함 -> key를 문자열로 사용해야 하기 때문
  ```javascript
  var person = {
    "last-name": "Kim",
    1: 10, // key 암묵적 타입 변환: 1 -> "1"
  };
  // person.last-name;
  person["last-name"];
  ```
  - 마침표 표기법으로 규칙을 따르지 않는 key에 접근할 때, `last`와 `name` 변수의 산술 연산으로 인식
  - 이 때, 브라우저와 nodeJS 환경에서 결과가 다름
    - `person.last`가 먼저 평가됨 -> 존재하지 않는 key이므로 `undefined` 반환
    - 브라우저 : `name`이 window 이름을 가리키는 전역 변수로 사용되므로 `undefined - ""`로 연산되어 `NaN` 반환
    - nodeJS : `name`이라는 변수는 존재하지 않으므로 `ReferenceError` 반환

### Property 동적 생성

- 존재하지 않는 key에 값 할당
  ```javascript
  var person = { name: "Kim" };
  person.age = 33;
  console.log(person); // {name:"Kim",age:33}
  ```

### Property 삭제

- `delete` keyword와 함께 key에 접근
  ```javascript
  var person = { name: "Kim", age: 33 };
  delete person.age;
  console.log(person); // {name:"Kim"}
  ```
- 존재하지 않는 property를 삭제하려고 하면 아무 일도 일어나지 않음 -> 문맥상 맞지 않다.

## Method

- Javascript function은 일급 객체이므로 property value로 사용할 수 있다.
- 일반 함수와 구분하기 위해 function을 value로 갖는 property를 '**method**'라고 부른다.

```javascript
var circle = {
  radius: 5,
  getDiameter: function () {
    return 2 * this.radius;
  },
};
console.log(circle.radius); // property 참조. 5 반환
console.log(circle.getDiameter()); // method 실행. 10 반환
```

## ES6+ 추가된 Object Literal 확장 기능

1. Property 축약 : property value에로 사용하는 변수 이름과 property key 이름이 동일할 때, property key를 생략해도 자동 생성됨

   ```javascript
   var x = 1,
     y = 2;

   // ES5
   var obj = { x: x, y: y };
   console.log(obj); // {x:1, y:2}

   // ES6+
   var obj = { x, y };
   console.log(obj); // {x:1, y:2}
   ```

2. Computed Property Name : 대괄호(`[]`) 안에 `string` type으로 평가될 수 있는 표현식을 사용해서 동적으로 key 생성

   ```javascript
   var prefix = "prop";
   var i = 0;

   // ES5
   var obj = {};
   obj[prefix + "-" ++i] = i;
   obj[prefix + "-" ++i] = i;
   obj[prefix + "-" ++i] = i;
   console.log(obj); // {prop-1:1,prop-2:2,prop-3:3}

   // ES6+
   var obj = {
    [`${prefix}-${++i}`]: i,
    [`${prefix}-${++i}`]: i,
    [`${prefix}-${++i}`]: i,
   };
   console.log(obj); // {prop-1:1,prop-2:2,prop-3:3}
   ```

3. Method 축약 : Method를 정의할 떄 `function` keyword 생략 -> 이 때, 축약 method는 property에 할당한 함수와 다르게 동작함

   ```javascript
   // ES5
   var obj = {
     sayHi: function () {
       console.log("Hi!");
     },
   };

   // ES6+
   var obj = {
     sayHi() {
       console.log("Hi");
     },
   };
   ```
