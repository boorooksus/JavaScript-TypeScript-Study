# 1. var 키워드로 선언한 변수의 문제점

### 1) 변수 중복 선언 허용

- var키워드로 선언한 변수는 중복 선언이 가능

```javascript
var x = 1;
var y = 1;

// var 키워드로 선언된 변수는 같은 스코프 내에서 중복 선언을 허용
// 초기화문이 있는 변수 선언문은 자바스크립트 엔진에 의해 var 키워드가 없는 것처럼 동작
var x = 100;
// 초기화문이 없는 변수 선언문은 무시
var y;

console.log(x); // 100
console.log(y); // 1
```

### 2) 함수 레벨 스코프

- var 키워드로 선언한 변수는 함수의 코드 블록만을 지역 스코프로 인정
- -> 나머지 코드 블록 내에 선언한 변수는 모두 전역 변수

```javascript
var i = 10;

// for문의 i도 전역 변수 -> 이미 선언된 전역 변수 i가 있으므로 중복 선언.
for (var i = 0; i < 5; i++) {
  console.log(i); // 0 1 2 3 4
}

// 의도치 않게 i 변수의 값이 변경
console.log(i); // 5
```

### 3) 변수 호이스팅

- 가독성을 떨어뜨리고 오류를 발생시킬 가능성 ↑

  > **호이스팅**

  - 변수 선언이 스코프의 시작 부분으로 끌어 올려진 것처럼 동작
  - 스코프 단위로 동작
  - 선언문 이전에 참조하는 경우 undefined를 반환
  - 전역 변수 호이스팅: 전역 변수의 선언이 전역 스코프 선두로 끌어 올려진 것처럼 동작 -> 전역 전체에서 변수 유효
  - 지역 변수 호이스팅: 지역 변수 선언이 지역 스코프 선두로 끌어 올려진 것처럼 동작 -> 함수 전체에서 변수 유효

> **var 키워드 단점을 보완하기 위해 ES6에서는 새로운 변수 선언 키워드인 let과 const를 도입**

---

# 2. let 키워드

### 1) 변수 중복 선언 금지

- let 키워드로 이름이 같은 변수를 중복 선언하면 문법 에러(SyntaxError)가 발생

```javascript
let bar = 123;
// 같은 스코프 내에서 중복 선언을 허용하지 않음
let bar = 456; // SyntaxError: Identifier 'bar' has already been declared
```

### 2) 블록 레벨 스코프(block-level scope)

- 모든 코드 블록(함수, if문, for문, while문, try - catch문 등)을 지역 스코프로 인정

```javascript
let foo = 1; // 전역 변수

{
  let foo = 2; // 지역 변수
  let bar = 3; // 지역 변수
}

console.log(foo); // 1
console.log(bar); // ReferenceError: bar is not defined
```

### 3) 변수 호이스팅

- 변수 선언문 이전에 참조하면 참조 에러(ReferenceError)가 발생
- 변수 호이스팅이 발생하지 않는 것처럼 동작 But 변수 호이스팅은 발생

```javascript
let foo = 1; // 전역 변수

{
  console.log(foo); // ReferenceError: Cannot access 'foo' before initialization
  let foo = 2; // 지역 변수
}
```

- 선언 단계와 초기화 단계가 분리되어 진행
- TDZ: Temporal Dead Zone. 변수가 호이스팅된 스코프의 시작점부터 let키워드로 선언된 선언문을 만나기 전 까지의 구간을 일시적 사각지대. 이 지점에서 변수를 참조하려고 하면 `ReferenceError` 발생

```javascript
// 런타임 이전에 선언 단계가 실행. 아직 변수가 초기화되지 않음.
// 초기화 이전의 일시적 사각 지대에서는 변수를 참조할 수 없음.
console.log(foo); // ReferenceError: foo is not defined

let foo; // 변수 선언문에서 초기화 단계가 실행된다.
console.log(foo); // undefined

foo = 1; // 할당문에서 할당 단계가 실행된다.
console.log(foo); // 1
```

> 전역 객체

- var 키워드로 선언한 전역 변수 -> 전역 객체 `window`의 프로퍼티
- 전역 객체는 브라우저 환경 내에서 참조 가능
- 전역 객체의 프로퍼티를 참조할 때 window를 생략 가능
- **let 키워드 로 선언한 전역 변수는 전역 객체의 프로퍼티가 아님** -> window 객체에 접근 불가
- let 전역 변수는 DER에 존재하기 때문
- DER: 전역 렉시컬 환경의 선언적 환경 레코드. 보이지 않는 개념적인 블록

```javascript
// 이 예제는 브라우저 환경에서 실행해야 한다.

// 전역 변수
var x = 1;
// 암묵적 전역
y = 2;

// var 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티다.
console.log(window.x); // 1
// 전역 객체 window의 프로퍼티는 전역 변수처럼 사용할 수 있다.
console.log(x); // 1

console.log(window.y); // 2
console.log(y); // 2
```

```javascript
// 이 예제는 브라우저 환경에서 실행해야 한다.

let x = 1;

// let, const 키워드로 선언한 전역 변수는 전역 객체 window의 프로퍼티가 아니다.
console.log(window.x); // undefined
console.log(x); // 1
```

---

# 3. const 키워드

- 상수(constant) 를 선언하기 위해 사용. BUT 반드시 상수만을 위해 사용하는 것은 아님
- const키워드의 특징은 let키워드와 대부분 동일(여기서는 차이점 위주로 설명)

### 1) 선언과 초기화

- 반드시 선언과 동시에 초기화 해야함
- 그렇지 않으면 `SyntaxError` 발생

```javascript
const foo; // SyntaxError: Missing initializer in const declaration
```

### 2) 재할당 금지

```javascript
const foo = 1;
foo = 2; // TypeError: Assignment to constant variable.
```

### 3) 상수(constant)

- const 키워드 로 선언한 변수에 원시 값 을 할당한 경우 변수 값을 변경할 수 없음 -> 상수로 사용
- 상수는 상태 유지와 가독성, 유지보수의 편의를 위해 적극적으로 사용을 권장
- 스네이크 케이스(snakeCase)로 표현하는 것이 일반적

```javascript
// 세율을 의미하는 0.1은 변경할 수 없는 상수로서 사용될 값이다.
// 변수 이름을 대문자로 선언해 상수임을 명확히 나타낸다.
const TAX_RATE = 0.1;

// 세전 가격
let preTaxPrice = 100;

// 세후 가격
let afterTaxPrice = preTaxPrice + preTaxPrice * TAX_RATE;

console.log(afterTaxPrice); // 110
```

### 4) const 키워드와 객체

- const 키워드로 선언된 변수에 객체를 할당한 경우 값을 변경 가능
- const 키워드 는 재할당을 금지할 뿐 불변 을 의미하지는 않음 -> 체가 변경되더라도 변수에 할당된 참조 값은 변경되지 않음

```javascript
const person = {
  name: "Lee",
};

// 객체는 변경 가능한 값이다. 따라서 재할당없이 변경이 가능하다.
person.name = "Kim";

console.log(person); // {name: "Kim"}
```

---

# 4. var vs let vs const

- 변수 선언에는 기본적으로 `const`를 사용하고 `let`은 재할당이 필요한 경우에 한정해 사용 권장
- 되도록이면 `var` 키워드는 사용하지 않는게 안전함
