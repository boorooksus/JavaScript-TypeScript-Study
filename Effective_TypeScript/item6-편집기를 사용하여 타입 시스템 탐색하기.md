# Effective TypeScript 스터디 - Item6: 편집기를 사용하여 타입 시스템 탐색하기

참고 자료:
⟪Effective TypeScript⟫(댄 밴더캄 지음, 인사이트, 2021)
⟪빠르게 마스터하는 타입스크립트⟫(코딩애플 강의, coding apple, 2021)

---

타입스크립트를 설치하고 실행하기 위한 방법은 아래 두 가지가 있다.

> 1. 타입스크립트 컴파일러 tsc
> 2. 단독으로 실행할 수 있는 타입스크립트 서버인 tsserver

## tsc 설치 및 사용 방법

### 1) Node.js 설치

### 2) 터미널에 명령어 입력

```bash
npm install -g typescript
```

> **에러가 발생한 경우**

    - 윈도우에서 허가되지 않은 script 실행불가 에러: Powershell 관리자 권한으로 실행 -> 'Set-ExecutionPolicy Unrestricted' 입력 -> y 선택
    - 맥에서 보안 에러: 명령어 앞에 sudo 붙이기

### 3) 터미널에 'tsc --init' 명령어 입력 또는 tsconfig.json 파일 생성 후 아래 작성

```javascript
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",
    "noImplicitAny": true,
    "strictNullChecks": true
  }
}
```

- target: 타입스크립트파일을 어떤 버전의 자바스크립트로 바꿔줄지(es5, es2016, esnext, ...)
- module: 자바스크립트 파일간 import 문법을 구현할 때 어떤 문법을 쓸지(commonjs는 require 문법, es2015와 esnext는 import 문법)
- noImplicitAny: any라는 타입이 의도치않게 발생할 경우 에러 (필수 아님)
- strictNullChecks: null, undefined 타입에 이상한 조작하면 에러 (필수 아님)

- 추가 옵션들

```javascript
{
  "compilerOptions": {
    "target": "es5",
    "module": "commonjs",

    "strict": true, // strict 관련, noimplicit 관련 모드 전부 켜기
    "noImplicitAny": true, // any 타입 금지 여부
    "strictNullChecks": true, // null, undefined 타입 체크 강하게
    "strictFunctionTypes": true, // 함수파라미터 타입 체크 강하게
    "strictPropertyInitialization": true, // class constructor 작성시 타입 체크 강하게
    "noImplicitThis": true, // this 키워드가 any 타입일 경우 에러
    "alwaysStrict": true, // 자바스크립트 "use strict" 모드 켜기

    "allowJs": true, // js 파일을 ts에서 import해서 쓸 수 있는지
    "checkJs": true, // 일반 js 파일에서도 에러 체크
    "jsx": "preserve", // tsx 파일을 jsx로 어떻게 컴파일할 것인지 ('preserve', 'react-native', 'react')
    "declaration": true, // 컴파일시 .d.ts 파일도 자동으로 생성 (현재쓰는 모든 타입이 정의된 파일)
    "outFile": "./", // 모든 ts파일을 js파일 하나로 컴파일 (module이 none, amd, system일 때만 가능)
    "outDir": "./", // js파일 아웃풋 경로 변경
    "rootDir": "./", // 루트경로 변경 (js 파일 아웃풋 경로에 영향)
    "removeComments": true, // 컴파일시 주석 제거

    "noUnusedLocals": true, // 쓰지 않는 지역 변수 있으면 에러
    "noUnusedParameters": true, // 쓰지 않는 파라미터 있으면 에러
    "noImplicitReturns": true, // 함수에서 return 없으면 에러
    "noFallthroughCasesInSwitch": true // switch문 이상하면 에러
  }
}
```

### 4) ts 파일 만들어서 작성 시작

### 5) ts 사용하려면 js파일로 변환해야함. 터미널에 아래 입력

```
tsc -w
또는
tsc 파일이름.ts
```

-w 옵션은 watch 옵션. ts파일을 생성하면, 변환된 js 파일도 같이 생성됨. 그리고 ts 파일 저장할 때마다 같이 업데이트 된다. HTML 파일에서는 ts파일이 아닌 이 js파일 불러와 사용한다.
