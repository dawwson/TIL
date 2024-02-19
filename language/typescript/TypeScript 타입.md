# TypeScript 타입

## Object

### 객체 타입 지정 방법

- 예시
  ```ts
  const player: { name: string; age: number } = {
    name: "kim",
  };
  ```

### Type Alias

TODO:

### Index signature

TODO:

<br>

## 함수

### 함수 타입 지정 방법

TODO:

### Call signature

TODO:

<br>

## optional

- 물음표(?)를 붙여서 특정 프로퍼티나 함수의 매개변수를 옵셔널로 만들 수 있다.
- 예시

  ```ts
  // 인터페이스 프로퍼티에 적용
  interface PaintOptions {
    shape: Shape;
    xPos?: number;
    yPos?: number;
  }

  // 객체 프로퍼티에 적용
  const player: { name: string, age?: number } = {
    name: 'kim'
  };

  // ERROR
  if (player.age < 10) {...}
  ```

<br>

## readonly

- 읽기 전용으로 만든다. 즉, `불변성`을 가지게 만들어준다.

  - 불변이란?

    - 변수가 const로 선언되거나 readonly를 명시하고 있으면 초깃값을 항상 유지한다. 이런 변수를 변경할 수 없다는 의미로 '불변(immutable)' 변수라고 한다.

  - 적용 대상 : 변수, 객체 프로퍼티, 인터페이스 프로퍼티, 클래스 프로퍼티, 함수의 매개변수 등

  - 예시

    ```ts
    type Player = {
      readonly name: string;
    };

    const makePlayer = (name: string): Player => ({ name });

    const player = makePlayer("kim");
    player.name = "lee"; // ERROR
    ```

### const와의 차이

- const 키워드가 있는데 readonly가 또 필요할까?

- readonly는 프로퍼티를 불변으로 만들어준다.

  - TS에서 인터페이스 프로퍼티, 클래스 프로퍼티, 함수의 매개변수 등은 let, const 키워드 없이 선언한다. 이러한 것들에 불변성을 주려면 readonly를 쓰면 된다.

- readonly는 배열과 객체를 불변으로 만들어준다.

  - 배열과 리터럴 객체는 const로 선언하더라도 그 안의 요소들을 변경할 수 있다.

  - 배열의 요소와 리터럴 객체의 프로퍼티에도 readonly로 불변성을 줄 수 있다.

- 예시

  ```ts
  const numbers: readonly number[] = [1, 2, 3, 4];
  numbers.push(5); // ERROR

  function forcePure(array: readonly number[]) {
    array.push(1); // ERROR
  }
  ```

<br>

## Tuple

- 요소의 타입이 모두 같으면 배열, 다를 수도 있으면 튜플이다.

- 항상 정해진 개수의 요소를 가져야 하는 배열의 타입을 지정할 수 있다.

- 배열은 튜플에 명시적으로 지정된 형식에 따라 요소 순서를 설정해야 하고, 추가되는 요소 또한 튜플에 명시된 타입만 사용 가능하다.

- 예시

  ```ts
  const player: [string, number, boolean] = ["nico", 12, true];
  player[0] = 1; // ERROR
  player[0] = "lynn"; // OK

  // readonly와 조합해서 쓸 수도 있다.
  const player: readonly [string, number, boolean] = ["nico", 12, true];
  player[0] = "lynn"; // ERROR
  ```

<br>

## any

- 말 그대로 아무 타입이나 될 수 있다.

- TS의 타입 검사를 빠져나오고 싶을 때 사용한다.

- 사실상 JS를 쓰는 것과 같으므로 신중하게 사용해야 한다.

- 예시

  ```ts
  const a: any[] = [1, 2, 3, 4];
  const b: any = true;

  a + b; // OK
  ```

<br>

## unknown

- 변수의 타입을 미리 알 수 없을 때 사용한다.

- any 처럼 어떠한 타입의 값이라도 할당할 수 있지만, unknown 타입의 값을 사용하기 전에 먼저 타입 확인 작업을 해야 한다.

- any보다 타입 안정성을 유지하면서 동적인 타입 처리가 필요할 때 사용한다.

- 예시

  ```ts
  let x: unknown = 10;
  let y: string;

  // ERROR
  y = x;

  // OK
  if (typeof x === "string") {
    y = x;
  }
  ```

<br>

## never

TODO:

<br>

## void

TODO:
