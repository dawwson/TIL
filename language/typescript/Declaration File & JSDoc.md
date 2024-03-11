# Declaration File & JSDoc

## Declaration File

- TypeScript 선언 파일은 주로 JavaScript로 작성된 라이브러리나 모듈을 TypeScript에서 사용할 때 필요하다.

- 선언 파일은 `.d.ts` 확장자를 가지며 동일한 이름의 라이브러리나 모듈의 타입을 정의한다. Visual Studio Code와 같은 통합개발환경(IDE)에서는 선언 파일을 불러와서 보여주기 때문에 바로 타입을 확인할 수 있다.

- TypeScript는 JavaScript 코드도 같이 쓸 수 있도록 허용하지만, 타입 정의가 없다면 사실 TypeScript를 쓰는 의미가 없다. TypeScript에서 선언 파일은 꼭 필요하다.

- `tsconfig.json`에 `"strict": true`를 설정하면 선언 파일이 없는 JavaScript 패키지에 대해 오류가 발생하며 컴파일도 할 수 없다. `{패키지명}.d.ts` 파일을 작성해주면 오류가 해결된다.

  ```ts
  // index.ts
  import { exit, init } from "myPackage";

  init({ url: "x" });
  exit(1);
  ```

  ```js
  // myPackage.js
  export function init(config) {
    return true;
  }

  export function exit(code) {
    return code + 1;
  }
  ```

  ```ts
  // myPackage.d.ts
  interface Config {
    url: string;
  }
  declare module "myPackage" {
    function init(config: Config): boolean;
    function exit(code: number): number;
  }
  ```

<br>

## JSDoc

- 우리가 오픈 소스에 기여하는 것이 아닌 이상 직접 선언 파일을 작성할 일은 많지 않다.

- 우리는 종종 하나의 프로젝트 안에서 JavaScript 파일과 TypeScript 파일이 함께 쓰이는 경우를 마주치게 된다.

  - e.g. TypeScript로 마이그레이션 하는 경우 등

- 타입 선언 파일을 작성하지 않고, TypeScript와 JavaScript를 함께 쓸 수 없을까?

<br>

### 1. tsconfig.json 파일에 "allowJs": true 옵션 추가

```json
// tsconfig.json
{
  "include": ["src"],
  "compilerOptions": {
    "outDir": "build",
    "target": "ES6",
    "strict": true,
    "allowJs": true
  }
}
```

<br>

### 2. JSDoc 작성

- JavaScript 파일은 그대로 유지하면서 TypeScript가 제공하는 보호 장치를 사용하고 싶다면 JSDoc을 사용한다.

  - 순수한 JavaScript 코드 안에서 `@ts-check`를 주석으로 추가하면 TypeScript처럼 타입 및 에러 체크가 가능하다.

  - 그 다음 JSDoc 형식의 주석을 작성한다.

- 이렇게 할 경우 실제 서비스 중인 프로젝트라면 당장 오류가 날 걱정은 하지 않아도 된다.

  ```js
  // @ts-check
  /**
   * Initialize the project
   * @param {object} config
   * @param {string} config.url
   * @returns {boolean}
   */
  export function init(config) {
    return true;
  }

  /**
   * Exits then program
   * @param {number} code
   * @returns {number}
   */
  export function exit(code) {
    return code + 1;
  }
  ```

<br>

## 참고

> 1. 코드잇 & PoiemaWeb
>    - [타입 정의(d.ts) 파일이 뭔가요?](<https://www.codeit.kr/tutorials/90/%ED%83%80%EC%9E%85%20%EC%A0%95%EC%9D%98(d.ts)%20%ED%8C%8C%EC%9D%BC%EC%9D%B4%20%EB%AD%94%EA%B0%80%EC%9A%94%3F>)
>    - [15.9 JSDoc을 사용하여 자바스크립트에 타입 힌트 제공하기](https://poiemaweb.com/jsdoc-type-hint)
> 2. 타입스크립트 공식 문서
>    - [JS Projects Utilizing TypeScript](https://www.typescriptlang.org/docs/handbook/intro-to-js-ts.html)
> 3. 강의
>    - 노마드 코더 - 타입스크립트로 블록체인 만들기
