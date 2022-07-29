Modern JavaScript Deep Dive 스터디 - CH26 ES6 함수의 추가 기능

참고 자료: ⟪모던 자바스크립트 Deep Dive⟫(이웅모 지음,위키북스, 2020)

---

<br />

# 1. 함수의 구분

- `ES6 이전`: 함수는 별다른 구분 없이 다양한 목적으로 사용
  - 특별한 제약 X, 생성자 함수로 호출되지 않아도 프로토타입 객체를 생성
  - -> 혼란, 실수를 유발, 성능 저하
- `ES6 이후`: 사용 목적에 따라 세 가지 종류로 구분

| ES6 함수 종류      | `constructor` | `protptype` | `super` | `arguments` |
| :----------------- | :-----------: | :---------: | :-----: | :---------: |
| 일반 함수(Normal)  |       O       |      O      |    X    |      O      |
| 메서드(Method)     |       X       |      X      |    O    |      O      |
| 화살표 함수(Arrow) |       X       |      X      |    X    |      X      |

---

# 2. 메서드

- ES6 사양에서 메서드는 <span style="color:orange">메서드 축약 표현으로 정의된 함수</span>만을 의미

```javascript
const obj = {
  x: 1,
  // foo는 메서드이다.
  foo() {
    return this.x;
  },
  // bar에 바인딩된 함수는 메서드가 아닌 일반 함수이다.
  bar: function () {
    return this.x;
  },
};

console.log(obj.foo()); // 1
console.log(obj.bar()); // 1
```

- 인스턴스를 생성할 수 없는 `non-constructor` -> 생성자 함수로서 호출 불가
- 내부 슬롯`[[HomeObject]]`(자신을 바인딩한 객체를 가리킴) 를 가짐 -> `super` 키워드를 사용 가능

```javascript
const base = {
  name: "Lee",
  sayHi() {
    return `Hi! ${this.name}`;
  },
};

const derived = {
  __proto__: base,
  // sayHi는 ES6 메서드다. ES6 메서드는 [[HomeObject]]를 갖는다.
  // sayHi의 [[HomeObject]]는 sayHi가 바인딩된 객체인 derived를 가리키고
  // super는 sayHi의 [[HomeObject]]의 프로토타입인 base를 가리킨다.
  sayHi() {
    return `${super.sayHi()}. how are you doing?`;
  },
};

console.log(derived.sayHi()); // Hi! Lee. how are you doing?
```

> - ES6 메서드
>   - 본연의 기능(`super`)를 추가
>   - 의미적으로 맞지 않는 기능(`constructor`)은 제거
> - => 메서드를 정의할 때, 프로퍼티 값으로 익명 함수 표현식을 할당하는 ES6 이전의 방식은 사용하지 않는 것 권장

---

<br />

# 3. 화살표 함수

- 화살표(`=>`)를 사용하여 기존의 함수 정의 방식보다 간략하게 함수를 정의
- 표현과 내부 동작 모두 기존 함수보다 간략함
- 콜백 함수 내부에서 `this`가 전역 객체를 가리키는 문제를 해결하기 위한 대안으로 유용

### 1) 화살표 함수 정의

#### 1-1) 함수 정의

- 함수 선언문으로 정의할 수 없고 함수 표현식으로 정의
- 호출 방식은 기존 함수와 동일

```javascript
const multiply = (x, y) => x * y;
multiply(2, 3); // -> 6
```

#### 1-2) 매개변수 선언

```javascript
// 매개변수가 여러개일 경우, 소괄호 안에 매개변수를 선언
const arrow = (x, y) => { ... };

// 매개변수가 한 개인 경우, 소괄호 생략 가능
const arrow = x => { ... };

// 매개변수가 없는 경우, 소괄호 생략 불가
const arrow = () => { ... };
```

#### 1-3) 함수 몸체 정의

- 함수 몸체가 하나의 문으로 구성된다면 함수 몸체를 감싸는 중괄호 {}를 생략 가능
  - 이때 함수 몸체 내부의 문이 값으로 평가될 수 있는 표현식인 문이라면 암묵적으로 반환

```javascript
// concise body
const power = (x) => x ** 2;
power(2); // -> 4

// 위 표현은 다음과 동일하다.
// block body
const power = (x) => {
  return x ** 2;
};
```

- 함수 몸체의 문이 표현식이 아닌 문이라면 중괄호 생략 불가
- 객체 리터럴을 반환하는 경우, 객체리터럴을 소괄호로 감싸줘야 함

```javascript
const create = (id, content) => ({ id, content });
create(1, "JavaScript"); // -> {id: 1, content: "JavaScript"}

// 위 표현은 다음과 동일하다.
const create = (id, content) => {
  return { id, content };
};
```

- 함수 몸체가 여러 개의 문으로 구성된다면 함수 몸체를 감싸는 중괄호를 생략할 수 없음
  - 이때 반환값이 있다면 명시적으로 반환해야 함

```javascript
const sum = (a, b) => {
  const result = a + b;
  return result;
};
```

- 화살표 함수도 즉시 실행 함수로 사용 가능

```javascript
const person = ((name) => ({
  sayHi() {
    return `Hi? My name is ${name}.`;
  },
}))("Lee");

console.log(person.sayHi()); // Hi? My name is Lee.
```

- 화살표 함수는 일급 객체이므로, `Array.prototype.map`, `Array.prototype.filter`, `Array.prototype.reduce` 같은 고차 함수에 인수로 전달 가능
  - 이 경우, 일반 함수보다 표현이 간결하고 가독성이 좋아짐

```javascript
// ES5
[1, 2, 3].map(function (v) {
  return v * 2;
});

// ES6
[1, 2, 3].map((v) => v * 2); // -> [ 2, 4, 6 ]
```

### 2) 화살표 함수와 일반 함수의 차이

- 화살표 함수는 인스턴스를 생성할 수 없는 `non-constructor`
  - -> `prototype` 프로퍼티가 없고, 프로토타입도 생성하지 않음
- 중복된 매개변수 이름을 선언할 수 없음

```javascript
const Foo = () => {};
// 화살표 함수는 prototype 프로퍼티가 없다.
Foo.hasOwnProperty("prototype"); // -> false
```

- 함수 자체의 `this`, `arguments`, `super`, `new.target` 바인딩을 갖지 않음
  - -> 화살표 함수 내부에서 참조할 땐, 스코프 체인을 통해 상위 스코프의 `this`, `arguments`, `new.target`을 참조

### 3) this

- 화살표 함수가 일반 함수와 구별되는 가장 큰 특징
- `this`는 함수가 어떻게 호출되었는지에 따라 `this`에 바인딩할 객체가 동적으로 결정
- 화살표 함수 내부에서 `this`를 참조하면 상위 스코프의 `this`를 그대로 참조
  - 이를 lexical this라고 함
- 만약 화살표 함수와 화살표 함수가 중첩되어 있다면, 스코프 체인상에서 가장 가까운 상위 함수 중에서 화살표 함수가 아닌 함수의 `this`를 참조

### 4) super

- 화살표 함수는 함수 자체의 `super` 바인딩을 갖지 않음
- `this`와 마찬가지로 상위 스코프에서 참조

```javascript
class Base {
  constructor(name) {
    this.name = name;
  }

  sayHi() {
    return `Hi! ${this.name}`;
  }
}

class Derived extends Base {
  // 화살표 함수의 super는 상위 스코프인 constructor의 super를 가리킨다.
  sayHi = () => `${super.sayHi()} how are you doing?`;
}

const derived = new Derived("Lee");
console.log(derived.sayHi()); // Hi! Lee how are you doing?
```

### 5) arguments

- 화살표 함수는 함수 자체의 `arguments` 바인딩을 갖지 않음
- `this`와 마찬가지로 상위 스코프의 `arguments`를 참조
  - -> 화살표 함수 자신에게 전달된 인수 목록을 확인할 수 없음 -> 그다지 유용하지 않음
- => 화살표 함수로 가변 인자 함수를 구현해야 할 땐 `rest 파라미터`를 이용

```javascript
function foo(...rest) {
  // 매개변수 rest는 인수들의 목록을 배열로 전달받는 Rest 파라미터다.
  console.log(rest); // [ 1, 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);
```

---

<br />

# 4. Rest 파라미터

### 1) 기본 문법

- 매개변수 이름 앞에 세개의 점 `...`을 붙여서 정의한 매개변수
- 함수에 전달된 인수들의 목록을 배열로 전달
- 일반 매개변수와 Rest 파라미터를 함께 사용할 땐, rest 파라미`는 마지막에 전달해야 함
- Rest 파라미터는 단 하나만 선언 가능

```javascript
function foo(param, ...rest) {
  console.log(param); // 1
  console.log(rest); // [ 2, 3, 4, 5 ]
}

foo(1, 2, 3, 4, 5);

function bar(param1, param2, ...rest) {
  console.log(param1); // 1
  console.log(param2); // 2
  console.log(rest); // [ 3, 4, 5 ]
}

bar(1, 2, 3, 4, 5);

function foo(...rest, param1, param2) { }

foo(1, 2, 3, 4, 5);
// SyntaxError: Rest parameter must be last formal parameter

function foo(...rest1, ...rest2) { }

foo(1, 2, 3, 4, 5);
// SyntaxError: Rest parameter must be last formal parameter
```

- Rest 파라미터는 함수의 `length 프로퍼티`에 영향을 주지 않음

```javascript
function foo(...rest) {}
console.log(foo.length); // 0

function bar(x, ...rest) {}
console.log(bar.length); // 1

function baz(x, y, ...rest) {}
console.log(baz.length); // 2
```

### 2) Rest 파라미터와 arguments 객체

- Rest 파라미터를 사용하여, 가변 인자 함수의 인수 목록을 배열로 직접 전달 받을 수 있음
- ES6 `메서드`는 Rest 파라미터와 arguments 객체를 모두 사용할 수 있지만, `화살표 함수`로 가변 인자 함수를 구현해야 할 때는 반드시 Rest 파라미터를 사용
  - 화살표 함수는 arguments 객체를 가지고 있지 않기 때문

---

<br />

# 5. 매개변수 기본값

- 함수를 호출할 때, 매개변수의 개수만큼 인수를 전달하지 않아도 에러 발생X
  - 인수가 전달되지 않은 매개변수의 값은 `undefined`
- ES6에서 도입된 매개변수 기본값을 사용해 인수 체크 및 초기화 가능
  - Rest 파라미터에서는 기본값을 지정할 수 없음

```javascript
function sum(x = 0, y = 0) {
  return x + y;
}

console.log(sum(1, 2)); // 3
console.log(sum(1)); // 1


function foo(...rest = []) {
  console.log(rest);
}
// SyntaxError: Rest parameter may not have a default initializer
```

- 매개변수 기본값은 `length` 프로퍼티와 `arguments` 객체에는 영향을 주지 않음

```javascript
function sum(x, y = 0) {
  console.log(arguments);
}

console.log(sum.length); // 1

sum(1); // Arguments { '0': 1 }
sum(1, 2); // Arguments { '0': 1, '1': 2 }
```
