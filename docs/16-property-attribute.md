# Property Attribute

- 내부 슬롯(internal slot), 내부 메서드(internal method)
  - Javascript가 내부 알고리즘 구현을 위해 사용하는 property와 method
  - 내부 슬롯은 직접 접근할 수 없으며, `[[Prototype]]`만 `__proto__`로 접근 가능

## Property Attribute and Descriptor

- Property attribute
  - Javascript engine이 property 생성 시 자동으로 정의하는 property state를 나타내는 값
  - Property value, writable, enumerable, configurable
- Property descriptor
  - Property attribute 정보를 제공하는 객체
  - `Object.getOwnPropertyDescriptor(object, 'property')`
  - `Object.getOwnPropertyDescriptor(object)` (ES8+)

## Data/Accessor Property

- Data property
  - key-value pair로 구성된 property
  - attributes: value, writable, enumerable, configurable
- Accessor property
  - 값을 갖지 않고 다른 data property의 값을 읽거나 저장할 때 호출되는 접근자 함수(accessor function)로 구성된 property
  - attributes: **get**, **set**, enumerable, configurable
  - `get` : getter function
  - `set` : setter function
- `get` property로 접근하면 getter function 실행 후 반환되는 값이 property value로 반환됨
- `set` property로 값을 할당하면 setter function 실행 후 계산 결과가 property value로 저장됨
- Getter/setter 정의 예시

  ```js
  const person = {
      firstName: "Chamsol",
      lastName: "Kim",

      get fullName() {
          return `${this.firstName} ${this.lastName}`
      }

      set fullName(value) {
          [this.firstName, this.lastName] = value.split(" ");
      }
  }

  person.fullName; // Chamsol Kim
  person.fullName = "Joey Kim";
  person.firstName; // "Joey"
  person.lastName; // "Joey"
  ```

## Define attributes

- `Object.defineProperty(object, key, attributeObject)` 사용
- Data property
  ```js
  Object.defineProperty(person, "firstName", {
    value: "Chamsol",
    writable: true,
    enumerable: true,
    configurable: true,
  });
  ```
- Accessor property
  ```js
  Object.defineProperty(person, "fullName", {
    get() {
        return `${this.firstName} ${this.lastName}`
    }
    set(name) {
        [this.firstName, this.lastName] = name.split(" ");
    }
    enumerable: true,
    configurable: true,
  });
  ```
- Define할 때 설정하지 않은 attribute의 값은 `undefined` 또는 `false`로 초기화

## Object Preventing Mutation

- 객체는 mutable value이지만, 자체적으로 변경되지 못하게 막을 수 있다.
- 확장 금지
  - `Object.preventExtensions(obj)`
  - 동적 프로퍼티 추가 및 `Object.defineProperty(obj)` 사용 금지
- 밀봉
  - `Object.seal(obj)`
  - `Object.isSealed(obj)`로 확인
  - 읽기 및 쓰기만 가능
- 동결
  - `Object.freeze(obj)`
  - `Object.isFrozen(obj)`로 확인
  - 읽기만 가능
- Deep Freeze
  - 객체를 동결해도 중첩 object는 변경할 수 있다. (shallow freeze)
  - 중첩 object까지 변경하려면 모든 property에 대해 재귀적으로 freeze해야 한다.
    ```js
    function deepFreeze(target) {
      if (target && typeof target === "object" && !Object.isFrozen(target)) {
        Object.freeze(target);
        Object.keys(target).forEach((key) => deepFreeze(target[key]));
      }
      return target;
    }
    ```
