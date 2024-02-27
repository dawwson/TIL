# `for...in` vs `for...of`

## `for...in`

```
for (variable of iterable) {
  statement;
}
```

- `for...in` 문은 상속된 열거 가능한 속성들을 포함하여 객체에서 문자열로 키가 지정된 모든 열거 가능한 속성에 대해 반복한다.

### 열거 가능한 속성?

- JS는 프로토타입 기반 객체 지향 언어이며, 프로토타입 체인을 통해 속성을 상속한다.  
  <img width="461" alt="스크린샷 2024-02-27 오후 8 26 24" src="https://github.com/dawwson/TIL/assets/45624238/d920ef4a-56c6-48d9-9628-1beb9d8c730c">

- JS의 모든 객체는 부모 역할의 객체와 연결되어 있어, 부모 객체의 프로퍼티 또는 메소드를 상속받아 사용할 수 있다. 이러한 부모 객체를 프로토타입(Prototype)이라 한다.

- 직접 생성한 속성은 기본적으로 열거 가능하며, 프로토타입 체인을 통해 상속받은 속성은 속성 명세의 `enumerable` 값에 따라 달라진다.

- `enumerable`는 `Object.getOwnPropertyDescriptor()` 함수를 사용하여 확인할 수 있다. `enumerable: false` 라면 열거 가능하지 않은 속성이며, `enumerable: true` 라면 열거 가능한 속성이다.

  ```js
  const desc = Object.getOwnPropertyDescriptor(Object.prototype, "toString");

  console.log(desc);

  /*
  { value: [Function: toString],
    writable: true,
    enumerable: false,
    configurable: true }
  */
  ```

- 프로토타입으로부터 열거 가능하지 않은 속성을 상속받았으나 프로토타입 체인 상에 있는 속성과 객체 자체에 있는 속성이 충돌한다면, 객체 자체의 프로퍼티를 우선한다.

  ```js
  const obj = {
    a: 1,
    b: 2,
    toString: function () {
      console.log("Custom toString");
    },
  };

  for (const key in obj) {
    console.log(obj[key]);
    /*
    1
    2
    ƒ () {
      console.log('Custom toString');
    }
    */
  }
  ```

<br>

## `for...of`

```
for (variable of iterable) {
  statement;
}
```

- `for...of` 문은 반복가능한 객체(Array, Map, Set, String, TypedArray, Arguments 객체 등)에 대해서 반복한다.

- `[Symbol.iterator]` 속성이 있는 모든 컬렉션 요소에 대해 반복한다.

### [Symbol.iterator] 속성?

- ES6에서 도입된 **이터레이션 프로토콜**(iteration protocol)은 데이터 컬렉션을 순회하기 위한 프로토콜(미리 약속된 규칙)이다.

- 이터레이션 프로토콜을 준수한 객체는 `for…of` 문으로 순회할 수 있고 Spread 문법의 피연산자가 될 수 있다.

- 이터레이션 프로토콜에는 **이터러블 프로토콜**(iterable protocol)과 **이터레이터 프로토콜**(iterator protocol)이 있다. 이터러블 프로토콜을 준수한 객체를 **이터러블**(iterable)이라 한다.

- 이터러블은 `Symbol.iterator` 메소드를 구현하거나 프로토타입 체인에 의해 상속한 객체이다.

- Array, String, Map, Set, TypedArray(Int8Array, Uint8Array, Uint8ClampedArray, Int16Array, Uint16Array, Int32Array, Uint32Array, Float32Array, Float64Array), DOM data structure(NodeList, HTMLCollection), Arguments는 ES6에서 제공하는 빌트인 이터러블이다.

  <img width="576" alt="스크린샷 2024-02-27 오후 8 28 44" src="https://github.com/dawwson/TIL/assets/45624238/be127fa6-4ab5-4c48-9f44-c7de609f264e">

<br>

## 결론

- 객체의 속성을 반복할 때는 `for...in`을 사용한다.
- 배열, 문자열, Map, Set 등을 반복할 때는 `for...of`를 사용한다.

<br>

## 참고

> 1. MDN
>    - [for...in](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for...in)
>    - [for...of](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Statements/for...of)
>    - [상속과 프로토타입](https://developer.mozilla.org/ko/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
>    - [Iteration protocols](https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Iteration_protocols)
> 2. 블로그
>    - [[JavaScript] for ...in, for ...of의 차이](https://doozi0316.tistory.com/entry/JavaScript-for-in-for-of%EC%9D%98-%EC%B0%A8%EC%9D%B4)
> 3. PoiemaWeb
>    - [5.14 Prototype](https://poiemaweb.com/js-prototype)
>    - [6.10 Iteration & for...of](https://poiemaweb.com/es6-iteration-for-of)
> 4. 프로그래머스 Q&A
>    - [for-in 루프에서 열거 가능한 속성과 그렇지 않은 속성의 차이](https://qna.programmers.co.kr/questions/9810/for-in-%EB%A3%A8%ED%94%84%EC%97%90%EC%84%9C-%EC%97%B4%EA%B1%B0-%EA%B0%80%EB%8A%A5%ED%95%9C-%EC%86%8D%EC%84%B1%EA%B3%BC-%EA%B7%B8%EB%A0%87%EC%A7%80-%EC%95%8A%EC%9D%80-%EC%86%8D%EC%84%B1%EC%9D%98-%EC%B0%A8%EC%9D%B4)
