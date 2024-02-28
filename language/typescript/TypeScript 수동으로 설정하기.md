# TypeScript 수동으로 설정하기

## 개요

- 프레임워크를 쓰면 타입스크립트가 설정된 프로젝트를 자동으로 만들어주지만, 간혹 타입스크립트 설정을 건드려야 할 때가 있다.

- 그때를 위해 타입스크립트를 수동으로 설정하는 방법을 기록해두기로 하였다.

- NodeJS가 설치되어 있다고 가정한다.

<br>

## 수동 설정 방법

1. package.json 생성

   ```
   npm init -y
   ```

2. 타입스크립트 설치

   ```
   npm install -D typescript
   ```

3. tsconfig.json 생성

   ```
   tsc -init
   ```

4. tsconfig.json 작성

   ```json
   {
     "include": ["src"], // 컴파일 할 파일의 경로
     "compilerOptions": {
       "outDir": "build", // 컴파일 한 파일을 저장할 위치
       "target": "ES6", // 자바스크립트 어떤 버전으로 컴파일 할 건지(모던 브라우저는 모두 es6를 지원하기 때문에 es6 권장)
       "lib": ["ES6", "DOM"] // 런타임 환경("DOM"을 추가하면 브라우저 API도 자동완성 기능 제공)
     }
   }
   ```

5. 컴파일
   ```
   tsc
   ```
