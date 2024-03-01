# TypeScript 타입

## Object

```ts
변수명: { key1: 타입, key2: 타입, ... }
```

- 변수 선언문을 확장해 위와 같은 형태로 타입을 명시할 수 있다.
- 타입 주석(type annotation)이라고 한다.
- 예시

  ```ts
  const player: { name: string; age: number } = {
    name: "kim",
  };
  ```

<br>

## optional

```
변수/프로퍼티명?: 타입
```

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

## Type Alias

```ts
type 새로운 타입 = 기존 타입
```

- type 키워드로 기존에 존재하는 타입을 이름만 바꿔서 사용할 수 있게 해준다.

- 타입을 재사용하고 싶을 때 사용할 수 있다.

- 코드가 깔끔하고 명확해질 때까지만 사용하면 된다.

- 예시

  ```ts
  type Age = number;

  type Player = {
    name: string;
    age?: Age;
  };

  const player: Player = {
    name: "es",
  };
  ```

<br>

## Call signature

```
(매개변수1: 타입, 매개변수2: 타입, ...) => 반환값 타입
```

- 함수의 타입을 말한다. '함수 시그니처'라고도 한다.

- 함수의 매개변수 타입과 반환 타입을 작성한다.

- `type`을 사용하면 더 깔끔하게 정의할 수 있다.

- 예시

  ```ts
  type Add = (a: number, b: number) => number;

  const add: Add = (a, b) => a + b;
  ```

### Overloading

- 같은 이름의 함수가 매개변수의 개수나 타입이 다를 때, 반환 타입이 다를 때 오버로딩이라고 한다.
- 여러 개의 call signature를 가질 수 있다.
- 예시

  ```ts
  // ❌ Don't
  type Add = {
    (a: number, b: number): number;
    (a: number, b: number, c: number): number;
  };

  // 또는

  // ✅ Do: 반환 타입이 같다면 옵셔널 매개변수 사용을 권장한다.
  type Add = {
    (a: number, b: number, c?: number): number;
  };

  const add: Add = (a, b, c) => {
    if (c) {
      return a + b + c;
    }
    return a + b;
  };
  ```

  <br>

## Generic

- 함수나 클래스의 선언 시점이 아닌, 사용 시점에 타입을 선언할 수 있는 방법을 제공한다.

- 변수/함수명 뒤에 `<타입 변수 이름>`을 붙인 형태로 사용한다. 타입 변수의 이름은 아무거나 해도 상관없지만, 관습적으로 알파벳 대문자 한글자로 처리하는 편이다.(T, V, E, ...)

- 라이브러리나 다른 개발자가 사용할 기능을 만들 때 유용하다.

### 제네릭 함수 타입

- 함수 오버로딩을 할 때 허용하는 고정 타입이 많아질수록 코드의 가독성이 떨어진다.

  ```ts
  // call signature
  type SuperPrint = {
    // concrete type: number, boolean, string
    (arr: number[]): void;
    (arr: boolean[]): void;
    (arr: string[]): void;
    (arr: (number | boolean)[]): void;
  };

  const superPrint: SuperPrint = (arr) => {
    arr.forEach((i) => console.log(i));
  };

  superPrint([1, 2, 3, 4]);
  superPrint([true, true, false]);
  superPrint(["a", "b", "c"]);
  superPrint([1, 2, true, false]);
  ```

- 이럴 때 제네릭을 사용하면 타입을 변수화하여 유연하게 함수를 사용할 수 있다.

  ```ts
  // 방법 1: type(또는 interface)으로 타입 지정
  type SuperPrint = {
    <T>(arr: T[]): T;
  };
  const superPrint: SuperPrint = (arr) => arr[0];

  // 방법 2: 함수 선언식
  function superPrint<T>(arr: T[]) {
    return a[0];
  }

  // 방법 3: 함수 표현식
  const superPrint = <T>(arr: T[]): T => arr[0];

  const a = superPrint([1, 2, 3, 4]); // a: number
  const b = superPrint([true, true, false]); // b: boolean
  const c = superPrint(["a", "b", "c"]); // c: string
  const d = superPrint([1, 2, true, false]); // d: number | boolean
  ```

### 제네릭 객체 타입

- 객체에도 제네릭 타입을 적용할 수 있다.
- 예시

  ```ts
  type Player<T> = {
    name: string;
    extraInfo: T;
  };

  type ExtraInfo = {
    favoriteFood: string;
  };

  const nico: Player<ExtraInfo> = {
    name: "nico",
    extraInfo: {
      favoriteFood: "kimchi",
    },
  };
  ```

<br>

## readonly

- 읽기 전용으로 만든다. 즉, `불변성`을 가지게 만들어준다.

  - 불변이란?

    - 변수가 const로 선언되거나 readonly를 명시하고 있으면 초깃값을 항상 유지한다. 이런 변수를 변경할 수 없다는 의미로 '불변(immutable)' 변수라고 한다.

- 적용 대상 : 변수, 객체 프로퍼티, 인터페이스 프로퍼티, 클래스 프로퍼티, 함수의 매개변수 등

  - 클래스 프로퍼티에 대해서, 값을 공개는 하고 싶지만 수정은 못하게 하고 싶을 때 readonly를 사용할 수도 있다.

- 예시 1

  ```ts
  type Player = {
    readonly name: string;
  };

  const makePlayer = (name: string): Player => ({ name });

  const player = makePlayer("kim");
  player.name = "lee"; // ERROR
  ```

- 예시 2

  ```ts
  class Word {
    constructor(public readonly term: string, public readonly def: string) {}
  }
  ```

### const와의 차이?

- const 키워드가 있는데 readonly가 또 필요할까?

- readonly는 프로퍼티를 불변으로 만들어준다.

  - TS에서 인터페이스 프로퍼티, 클래스 프로퍼티, 함수의 매개변수 등은 let, const 키워드 없이 선언한다. 이러한 것들에 불변성을 주려면 readonly를 쓰면 된다.

- readonly는 배열과 객체를 불변으로 만들어준다.

  - 배열과 리터럴 객체는 const로 선언하더라도 그 안의 요소들을 변경할 수 있다.

  - 배열의 요소와 리터럴 객체의 프로퍼티에도 불변성을 주고 싶으면 readonly를 사용하면 된다.

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

## void

- 값을 반환하지 않는 함수의 반환 타입이다.

- 함수의 반환 타입으로만 사용할 수 있다.

- 어떠한 함수의 반환값

- 예시
  ```ts
  function print(): void {
    console.log("hi!");
  }
  ```

<br>

## never

- 변수의 타입으로 사용되면 절대 발생할 수 없는 값이라는 의미이다.

- 보통 함수의 반환 타입으로 사용되며, 함수가 결코 값을 반환하지 않음을 의미한다.

- 주로 예외를 던지거나 무한 루프와 같은 작업을 하는 함수에서 반환 타입으로 사용된다.

- 예시

  ```ts
  function fail(msg: string): never {
    throw new Error(msg);
  }

  function fn(x: string | number) {
    if (typeof x === "string") {
      // do something
    } else if (typeof x === "number") {
      // do something else
    } else {
      x; // has type 'never'!
    }
  }
  ```

<br>

### void vs never

- void와 never 둘 다 반환값이 없다는 것은 같다.

- void는 반환값을 생성하지 않고 작업을 수행하는 목적임을 의미하고, never는 함수가 정상적으로 종료되지 않음을 의미한다.

<br>

## 참고

> 1. 블로그
>    - [Understanding the Distinctions: never vs void](https://www.linkedin.com/pulse/understanding-distinctions-never-vs-void-shashank-shekhar)
>    - [타입스크립트 Generic 타입 정복하기](https://inpa.tistory.com/entry/TS-%F0%9F%93%98-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-Generic-%ED%83%80%EC%9E%85-%EC%A0%95%EB%B3%B5%ED%95%98%EA%B8%B0)
> 2. 도서
>    - Do it! 타입스크립트 프로그래밍
> 3. 강의
>    - 노마드 코더 - 타입스크립트로 블록체인 만들기
> 4. [타입스크립트 공식문서](https://www.typescriptlang.org/docs/handbook/2/functions.html#other-types-to-know-about)
