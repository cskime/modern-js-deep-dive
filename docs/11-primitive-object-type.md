# 11. Primitive Type vs. Object/Reference Type

## Summary

- 원시 타입
  - immutable value
  - 메모리에 값 자체를 저장함
  - pass by value
  - deep copy
- 객체 타입
  - mutable value
  - 메모리에 객체의 참조를 저장함
  - pass by reference
  - shallow copy (property value로 object를 갖는 경우, deep copy를 위한 `lodash` 같은 라이브러리 사용)
- Javascript의 값 전달 방식
  - 변수를 변수에 할당하는 상황에서
  - 원시 값이나 객체 값이나 모두 **변수가 참조하는 메모리 공간에 저장된 값**을 전달함 => pass by value
  - 단, 원시 값은 값 자체가 저장되어 있지만 객체 값은 **객체의 참조**가 저장되어 있음
  - 즉, 원시 값을 저장한 변수를 다른 변수에 할당하면 깊은 복사가 일어나고, 객체의 참조를 저장한 변수를 다른 변수에 할당하면 얕은 복사가 일어난다.

## Primitive Type

- Immutable value
  - 값을 생성한 후 변경할 수 없음 (불변성, immutability)
  - 데이터의 **신뢰성**을 보장한다.
- 변수에 원시 값을 할당하면 새로운 메모리 공간을 확보한 뒤 할당한 값을 저장하고 변수가 참조하던 메모리 공간의 주소를 변경한다.
- `string` type
  - 문자열은 0개 이상의 문자(`char`)로 이루어짐
  - 문자 1개는 2byte 차지, 문자열은 문자의 개수 만큼 메모리 공간을 차지함
  - 즉, 문자열은 필요한 메모리 공간을 미리 알 수 없으므로 일반적으로 배열이나 객체로 처리함
  - 하지만, javascript는 편의성을 위해 `string` type을 원시값으로 제공한다.
  - Javascript의 문자열은 iterable한 유사 배열이므로 character에 index로 접근할 수 있지만, **character 1개만 변경하는 것은 불가능하다.**

### Pass by value

- 변수에 변수를 할당하면 기존 변수의 값 자체가 새 변수에 복사된다.
- 이 때, 두 변수에 저장된 값은 별개의 값으로 **서로 간섭할 수 없다**. (한 쪽을 변경해도 다른 한 쪽이 영향을 받지 않음)
- Javascript engine에 따라 변수가 주소를 참조하는 방식이 다를 수 있다.
  - 변수를 할당할 때 새 메모리 공간을 참조
  - 처음에는 두 변수가 같은 메모리 주소를 참조하고, 둘 중 하나에서 재할당이 일어나면 다른 메모리 주소를 참조 (copy on write)

### Pass by share

- Javascript에는 'pass by value'라는 개념이 없음
- 엄밀하게 따지면 변수에 변수를 할당하면 참조하고 있는 메모리 주소를 전달하기 때문
- **메모리 주소에 접근해서 값을 참조하는 개념**

## Object/Reference Type

- 객체는 runtime에 필요한 메모리 크기가 동적으로 변경될 수 있으므로, 메모리 크기를 사전에 정의해 둘 수 없음
- 원시 값처럼 전체 값을 매번 복사하면 메모리 효율과 성능 면에서 낭비이므로, **객체를 직접 수정할 수 있게 함 => mutable value**
  - Property 동적 추가, 값 갱신, 삭제 가능
  - 이 때, 변경된 객체 값은 변수에 재할당 한 것이 아니므로 **변수의 참조 값은 변경되지 않음**
- 객체가 생성되면 특정 메모리에 저장되고, 객체를 할당한 변수에 접근하면 객체의 **참조 값(reference value)**을 얻음 => "변수가 객체를 참조하고 있다."
- 객체의 참조를 통해 접근하기 때문에, **여러 변수 식별자가 동일한 객체를 참조**할 수 있다.

### 객체의 얕은 복사(Shallow copy)와 깊은 복사(Deep copy)

- Property value로 objct를 갖는 object에 대해
  - 얕은 복사 : 한 단계의 property들만 복사
  - 깊은 복사 : nested object까지 모두 복사
- 또는,
  - 얕은 복사 : 객체를 할당한 변수를 다른 변수에 할당하는 것
  - 깊은 복사 : 원시 값을 할당한 변수를 다른 변수에 할당하는 것 (원시 값은 항상 모든 값이 그대로 복사되므로)

```javascript
const obj = { x: { y: 1 } };

// shallow copy
const c1 = { ...obj };

// deep copy (lodash nodeJS package 사용)
const c2 = require("lodash").cloneDeep(obj);
```

### Pass by reference

- 객체를 참조하는 변수를 다른 변수에 할당하면, **원본의 참조값이 복사**됨
- 결국, **두 변수가 같은 객체를 참조**함 => 하나의 객체를 공유하므로 한 쪽에서 변경하면 다른 변수가 영향을 받음
