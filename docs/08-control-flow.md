# 08. Control Flow Statement

- 위에서부터 한 줄씩 실행되는 흐름을 인위적으로 제어하는 방법
- 실행 흐름을 의도적으로 변경하므로 가독성이 떨어질 수 있음 -> 고차 함수로 해결 (map, filter, reduce, foreach 등)
- 요약
  - Conditional Statement
    - `if`
    - `switch`
  - Loop Statement
    - `for`
    - `while`
    - `do...while`
  - break
  - continue

## Block Statement

- 0개 이상의 statement를 중괄호(`{}`)로 묶은 것
- Code block, block 등
- Block statement는 자체 종결성을 가지므로 끝에 세미콜론(`;`)을 붙이지 않는다.

## Conditional Statement

- 조건식(conditional expression)의 평가 결과에 따라 실행될 block 결정
- `if-else if-else`
  ```javscript
  if (expression1) {
    // execute when expression is true
  } else if (expression2) {
    // execute when expression2 is true
  } else {
    // execute when all the expressions are false
  }
  ```
- `switch`
  ```javascript
  switch (expression) {
    case expression1:
      // execute when expression and expression1 is same
      break;
    case expression2:
      // execute when expression and expression2 is same
      break;
    default:
      // execute when all the case expressions don't equal to switch expression
      break;
  }
  ```
  - 각 `case`에서 `break`를 명시적으로 호출하지 않으면, 다음 `case`의 statement가 실행된다. -> fall through
  - 여러 개의 `case` statement를 하나의 조건으로 판단할 경우 fall through가 유용하다.
- 가독성에 따라 `if`문과 `switch`문 중 선택해서 사용한다.

## Loop Statement

- 조건식을 평가한 결과가 `true`일 때 block을 실행하는 동작을 조건식이 `false`가 될 때 까지 반복

### for

- `forEach`, `for ... in`, `for ... of` 등 반복문을 대체하는 기능들도 존재한다.
- 기본적인 반복문의 사용법

  ```javascript
  for (변수 선언문 또는 할당문; 조건식; 증감식) {
      조건식이 참일 때 실행될 코드
  }

  for (var i = 0; i < 2; i++) {
      console.log(i); // 0 1
  }
  ```

- 각 항목은 모두 optional
  - 변수가 필요하지 않다면 변수 선언/할당부 생략
  - 무한 반복하려면 조건식 생략
  - 변수의 증감이 필요하지 않다면 증감식 생략
  - 모두 생략하면 무한루프가 된다.
    ```javascript
    for (;;;) { ... }
    ```

### while

- `for`문은 반복 횟수가 명확할 때 사용
- `while`문은 반복 횟수를 알 수 없을 때 사용 (조건을 만족하지 않게 될 때 까지 무한반복)
- 사용 방법
  ```javascript
  var count = 0;
  while (count < 3) {
    console.log(count++);
  }
  ```

### do...while

- `while`문은 표현식의 첫 번째 평가 결과가 `false`라면 block이 한 번도 실행되지 않음
- `do...while`은 조건식의 평가 결과와 상관 없이 `do` block이 최소 1회 실행
- 사용 방법
  ```javascript
  var count = 0;
  do {
    console.log(count++);
  } while (count < 3);
  ```

## break

- Block을 탈춣하는 키워드
- `break;`를 만나면 block에서 빠져나와서 이후 코드를 실행한다.

### Label Statement

- 식별자가 붙은 statement
  ```javascript
  foo: {
    console.log(1);
    break foo;
    console.log(2); // 실행되지 않음
  }
  ```
- 실행 순서 제어에 사용
  ```javascript
  outer: for (var i = 0; i < 3; i++) {
    for (var j = 0; j < 3; i++) {
      if (i + j === 3) {
        break outer; // 바깥 for문을 탈출하여 이중 반복문을 완전히 빠져나온다.
      }
      console.log(`inner [${i}, ${j}]`);
    }
  }
  ```
- Label statement는 코드 실행 흐름을 복잡하게 만들기 때문에 사용하지 않는게 좋다.

## continue

- 반복문에서 코드 실행을 중단하고 반복문의 증감식으로 실행 흐름을 이동시킨다.
- 반복문을 탈출하지는 않는다.

```javascript
var search = "l";
var count = 0;
for (var i = 0; i < string.length; i++) {
  if (string[i] !== search) continue;
  count++;
}
```
