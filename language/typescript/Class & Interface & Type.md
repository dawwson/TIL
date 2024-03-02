# Class & Interface & Type

## Class(+ JS Class와의 차이점)

### 1. 속성 초기화 코드를 생략할 수 있다.

- 생성자의 매개변수 타입 선언과 동시에 접근 제어자(+readonly)만 사용하면 속성 초기화를 할 수 있다.

- 이때는 속성이 public이어도 명시해야 한다.

  ```js
  // JS
  class User {
    constructor(firstName, lastName) {
      this.firstName = firstName;
      this.lastName = lastName;
    }
  }
  ```

  ```ts
  // TS
  // 컴파일하면 위 JS 코드와 동일하다.
  class User {
    constructor(private firstName: string, private lastName: string) {}
  }
  ```

### 2. 접근 제한자(access modifier)를 지원한다.

- 접근 제한자는 TS 스펙이다. 컴파일하면 사라진다.

- JS에서는 private과 같은 용도로 `#` prefix를 붙인다.

- TS에는 public, protected, private이 있다.

  - public : 어디서나 자유롭게 접근 가능

  - protected : 내 클래스를 상속한 자식 클래스 내에서 까지만 접근 가능

  - private : 내 클래스에서만 접근 가능

- JS, TS 모두 접근 제한자를 생략하면 기본적으로 public이다.

### 3. 추상 클래스(abstract class)를 지원한다.

- abstract class는 TS 스펙이다. 컴파일하면 `abstract` 키워드가 사라진 일반 클래스가 된다.

- 상속받는 클래스가 어떻게 동작해야할 지 알려주기 위한 클래스로, interface와 유사하다.

  - interface가 클래스나 객체의 뼈대를 만든다면, abstract class는 클래스 전용으로 클래스의 뼈대를 만든다.

- abstract class로 직접 인스턴스를 만들 수 없다. `extends` 키워드로 자식 클래스에 상속시켜야 한다.

- 속성이나 메서드 앞에 `abstract` 키워드를 붙여서 나를 상속하는 다른 클래스에서 이 속성이나 메서드를 구현하게 한다.

  - 추상 메서드는 call signature만 작성한다.

  - 추상 메서드가 있는 경우, abstract class를 상속받는 자식 클래스는 추상 메서드를 반드시 구현해야 한다.

  ```ts
  abstract class User {
    constructor(protected firstName: string, protected lastName: string) {}

    abstract getFullName(): void;
  }

  class Player extends User {
    constructor(private firstName: string, private lastName: string) {}

    getFullName() {
      return `${this.firstName} ${this.lastName}`;
    }
  }
  const player = new Player("Timothee", "Chalamet");
  player.getFullName();
  ```

<br>

## Interface

- interface는 클래스나 객체의 모양을 만들어준다.

- interface는 TS 스펙으로, 컴파일하면 사라진다.

- 선언을 병합할 수 있다.

  ```ts
  interface User {
    firstName: string
  }

  interface User {
    lastName: string
  }

  interface User {
    health: number
  }

  const user: User {
    firstName: "Timothee",
    lastName: "Chalamet",
    health: 10
  }
  ```

- `extends`로 interface를 확장하여 새로운 interface를 만들 수 있다.

  ```ts
  interface Animal {
    name: string;
  }

  interface Bear extends Animal {
    honey: boolean;
  }

  const bear = getBear();
  bear.name;
  bear.honey;
  ```

  <br>

## Type

- type은 객체의 모양을 만들거나, 특정 값을 가지도록 제한할 수도 있다.

- type은 TS 스펙으로, 컴파일하면 사라진다.

- 타입 별칭(type alias)을 만들 수 있다.

  ```ts
  type Health = 1 | 5 | 10;
  type Team = "red" | "yellow" | "blue";

  type Player = {
    healthBar: number;
    team: Team;
  };

  const player: Player = {
    healthBar: 10,
    team: "blue",
  };
  ```

- `&`로 type을 확장하여 새로운 type을 만들 수 있다.

  ```ts
  type Animal = {
    name: string;
  };

  type Bear = Animal & {
    honey: boolean;
  };

  const bear = getBear();
  bear.name;
  bear.honey;
  ```

<br>

## Abstract class vs Interface

### 공통점

- 클래스의 모양을 정의할 수 있다.

### 차이점

- abstract class는 컴파일하면 일반 클래스가 되고, interface는 컴파일하면 사라진다.

- 클래스는 다중 상속을 지원하지 않으므로 하나의 abstract class만 상속(extends)할 수 있고, interface는 여러 개를 구현(implements)할 수 있다.

- abstract class는 메서드에 구현을 포함할 수 있고, interface는 메서드에 구현을 포함할 수 없다(call signature만 써야 한다).

### 결론

- 클래스의 모양을 정의할 때 공통된 기능을 가지도록 구현 메서드를 제공한다면 abstract class를 사용한다.

- 그렇지 않다면 컴파일하면 사라지는 interface를 사용하길 권장한다.

<br>

## Interface vs Type

### 공통점

- 객체의 모양을 정의할 수 있다.

- abstract class를 대체할 수 있다.

  ```ts
  type PlayerA = {
    firstName: string;
  };

  interface PlayerB {
    firstName: string;
  }

  class User implements PlayerA {
    constructor(public firstName: string) {}
  }
  // 또는
  class User implements PlayerB {
    constructor(public firstName: string) {}
  }
  ```

- 컴파일하면 사라진다.

### 차이점

- 정의하는 방식이 다르다.

  - `interface Y { }`

  - `type X = { }`

- 확장 방식이 다르다.

  - `interface A extends B { }`

  - `type A = B & { }`

- interface는 선언을 병합할 수 있지만, type은 선언을 병합할 수 없다.

- type은 특정 값으로 타입을 제한하는 경우에도 사용할 수 있어 활용도가 좀 더 높다.

### 결론

- interfacesms 객체 지향 프로그래밍의 개념을 활용해서 디자인되었고, type은 좀 더 유연하게 사용할 수 있다.

- 클래스나 객체의 모양을 정의하고 싶으면 interface, 이외에는 타입 별칭이나 특정값으로 타입을 제한하는 경우 type 사용을 권장한다.

<br>

## 참고

> 1. 블로그
>    - [타입스크립트 클래스 · 객체 지향 문법 💯 총정리](https://inpa.tistory.com/entry/TS-%F0%9F%93%98-%ED%83%80%EC%9E%85%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8-%ED%81%B4%EB%9E%98%EC%8A%A4-%C2%B7-%EA%B0%9D%EC%B2%B4-%EC%A7%80%ED%96%A5-%EB%AC%B8%EB%B2%95-%F0%9F%92%AF-%EC%B4%9D%EC%A0%95%EB%A6%AC)
>    - [private, protected 프로퍼티와 메서드](https://ko.javascript.info/private-protected-properties-methods)
>    - [Interface vs Abstract Class in Typescipt](https://hrishikeshpathak.com/blog/interface-vs-abstract-class-typescript/)
>    - [TypeScript - 인터페이스 vs abstract class](https://bitkunst.tistory.com/entry/TypeScript-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4-vs-%EC%B6%94%EC%83%81-%ED%81%B4%EB%9E%98%EC%8A%A4)
> 2. 타입스크립트 공식 문서
>    - [Differences Between Type Aliases and Interfaces](https://www.typescriptlang.org/docs/handbook/2/everyday-types.html#differences-between-type-aliases-and-interfaces)
> 3. 도서
>    - Do it! 타입스크립트 프로그래밍
> 4. 강의
>    - 노마드 코더 - 타입스크립트로 블록체인 만들기
