Modern JavaScript Deep Dive 스터디 - CH22 this

참고 자료: ⟪모던 자바스크립트 Deep Dive⟫"(이웅모 지음,위키북스, 2020)

---

- 객체지향 프로그래밍의 객체
  - 상태(state)를 나타내는 프로퍼티
  - 동작(behavior)를 나타내는 메서드
    -> 하나의 논리적인 단위로 묶은 복합적인 자료구조

---

# 1. this 키워드

- 자신이 속한 객체 또는 자신이 생성할 인스턴스를 가리키는 자기 참조 변수(self-referencing variable)
- this를 통해 자신이 속한 객체 또는 자신이 생성할 인스턴스의 프로퍼티나 메서드를 참조 가능
- 자바스크립트 엔진에 의해 암묵적으로 생성되며, 코드 어디서든 참조 가능
- 함수를 호출하면 arguments 객체와 this가 암묵적으로 함수 내부에 전달됨
- this 가리키는 값, 즉 this 바인딩은 함수 호출 방식에 의해 동적으로 결정

> ### this binding
>
> - 식별자와 값을 연결하는 과정
> - this 바인딩은 `this`와 `this가 가리킬 객체`를 바인딩하는 것

---

# 2. 함수 호출 방식과 this 바인딩

- this 바인딩(this 바인딩될 값)은 함수 호출 방식에 따라 동적으로 결정

```javascript
// this 바인딩은 함수 호출 방식에 따라 동적으로 결정된다.
const foo = function () {
  console.dir(this);
};

// 동일한 함수도 다양한 방식으로 호출할 수 있다.

// 1. 일반 함수 호출
// foo 함수를 일반적인 방식으로 호출
// foo 함수 내부의 this는 전역 객체 window를 가리킨다.
foo(); // window

// 2. 메서드 호출
// foo 함수를 프로퍼티 값으로 할당하여 호출
// foo 함수 내부의 this는 메서드를 호출한 객체 obj를 가리킨다.
const obj = { foo };
obj.foo(); // obj

// 3. 생성자 함수 호출
// foo 함수를 new 연산자와 함께 생성자 함수로 호출
// foo 함수 내부의 this는 생성자 함수가 생성한 인스턴스를 가리킨다.
new foo(); // foo {}

// 4. Function.prototype.apply/call/bind 메서드에 의한 간접 호출
// foo 함수 내부의 this는 인수에 의해 결정된다.
const bar = { name: "bar" };

foo.call(bar); // bar
foo.apply(bar); // bar
foo.bind(bar)(); // bar
```

- 렉시컬 스코프와 this 바인딩은 결정 시기가 다름

  - 렉시컬 스코프는 함수 정의가 평가되어 함수 객체가 생성되는 시점에 상위 스코프를 결정 BUT this 바인딩은 함수 호출 시점에 결정

  > ### 렉시컬 스코프
  >
  > - 함수의 상위 스코프를 결정하는 방식

  > ### 함수 호출 방식
  >
  > - 일반 함수 호출
  > - 메서드 호출
  > - 생성자 함수 호출
  > - `Function.prototype.apply/call/bind` 메서드에 의한 간접 호출

### 1) 일반 함수 호출

- 기본적으로 `this`에는 `전역 객체(global object)`가 바인딩
  - 전역 함수 + 일반 함수로 호출된 모든 함수(중첩함수, 콜백함수 포함)

```javascript
function foo() {
  console.log("foo's this: ", this); // window
  function bar() {
    console.log("bar's this: ", this); // window
  }
  bar();
}
foo();
```

- 단, `strict mode` 에서 this를 참조할 경우는 `undefined`가 바인딩

```javascript
function foo() {
  "use strict";

  console.log("foo's this: ", this); // undefined
  function bar() {
    console.log("bar's this: ", this); // undefined
  }
  bar();
}
foo();
```

- 중첩 함수나 콜백 함수의 경우, 용도가 일반적으로 외부 함수를 돕는 헬퍼 함수의 역할 을 하지만, this가 가리키는 객체가 전역 객체 일 경우는 헬퍼 함수의 역할을 하기 힘듦

```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    console.log("foo's this: ", this); // {value: 100, foo: ƒ}
    // 콜백 함수 내부의 this에는 전역 객체가 바인딩된다.
    setTimeout(function () {
      console.log("callback's this: ", this); // window
      console.log("callback's this.value: ", this.value); // 1
    }, 100);
  },
};

obj.foo();
```

-> 메서드 내부의 중첩 함수나 콜백 함수의 this 바인딩 을 메서드의 this 바인딩과 일치시키기 위한 방법은 다음과 같음

- 메서드의 this 바인딩할 객체를 변수에 할당
- `Function.prototype.apply/bind` 에 의한 바인딩

```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    // this 바인딩(obj)을 변수 that에 할당한다.
    const that = this;

    // 콜백 함수 내부에서 this 대신 that을 참조한다.
    setTimeout(function () {
      console.log(that.value); // 100
    }, 100);
  },
};

obj.foo();
```

```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 콜백 함수에 명시적으로 this를 바인딩한다.
    setTimeout(
      function () {
        console.log(this.value); // 100
      }.bind(this),
      100
    );
  },
};

obj.foo();
```

- 화살표 함수의 this는 상위 스코프의 this를 가리킴

```javascript
var value = 1;

const obj = {
  value: 100,
  foo() {
    // 화살표 함수 내부의 this는 상위 스코프의 this를 가리킨다.
    setTimeout(() => console.log(this.value), 100); // 100
  },
};

obj.foo();
```

### 2) 메서드 호출

- 메서드를 호출한 객체, 즉 메서드를 호출할 때 메서드 이름 앞의 마침표(.) 연산자 앞에 기술한 객체가 바인딩
- 주의할 점은 메서드 내부의 this는 메서드를 소유한 객체가 아닌, **메서드를 호출한 객체에 바인딩 된다는 점**

```javascript
const person = {
  name: "Lee",
  getName() {
    // 메서드 내부의 this는 메서드를 호출한 객체에 바인딩된다.
    return this.name;
  },
};

// 메서드 getName을 호출한 객체는 person이다.
console.log(person.getName()); // Lee
```

- 메서드는 특정 객체에 포함된 것이 아닌, **독립적으로 존재하는 별도의 객체**
  -> 다른 객체의 프로퍼티에 할당 가능 + 일반 변수에 할당하여 일반 함수로 호출 가능

```javascript
const anotherPerson = {
  name: "Kim",
};
// getName 메서드를 anotherPerson 객체의 메서드로 할당
anotherPerson.getName = person.getName;

// getName 메서드를 호출한 객체는 anotherPerson이다.
console.log(anotherPerson.getName()); // Kim

// getName 메서드를 변수에 할당
const getName = person.getName;

// getName 메서드를 일반 함수로 호출
console.log(getName()); // ''
// 일반 함수로 호출된 getName 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
// 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
// Node.js 환경에서 this.name은 undefined다.
```

- 프로토타입 메서드 내부에서 사용된 this도 일반 메서드와 마찬가지로 해당 메서드를 호출한 객체에 바인딩

```javascript
function Person(name) {
  this.name = name;
}

Person.prototype.getName = function () {
  return this.name;
};

const me = new Person("Lee");

// getName 메서드를 호출한 객체는 me다.
console.log(me.getName()); // ① Lee

Person.prototype.name = "Kim";

// getName 메서드를 호출한 객체는 Person.prototype이다.
console.log(Person.prototype.getName()); // ② Kim
```

### 3) 생성자 함수 호출

- 생성자 함수 내부의 this -> 생성자 함수가 (미래에) 생성할 인스턴스가 바인딩

```javascript
// 생성자 함수
function Circle(radius) {
  // 생성자 함수 내부의 this는 생성자 함수가 생성할 인스턴스를 가리킨다.
  this.radius = radius;
  this.getDiameter = function () {
    return 2 * this.radius;
  };
}

// 반지름이 5인 Circle 객체를 생성
const circle1 = new Circle(5);
// 반지름이 10인 Circle 객체를 생성
const circle2 = new Circle(10);

console.log(circle1.getDiameter()); // 10
console.log(circle2.getDiameter()); // 20
```

### 4) Function.prototype.apply/call/bind 메서드에 의한 간접 호출

- `apply`, `call`, `bind`는 `Function.prototype`의 메서드
  -> 모든 함수가 이 메서드들을 상속 받아 사용 가능
- `apply`, `call`
  - 본질적은 기능은 함수를 호출하는 것
  - 함수를 호출하면서 첫 번째 인수로 전달한 특정 객체를 호출한 함수의 this에 바인딩
  - `apply`는 호출할 함수의 인수를 배열로 두 번째 인수에 전달
  - `call`은 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달

```javascript
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

console.log(getThisBinding()); // window

// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
console.log(getThisBinding.apply(thisArg)); // {a: 1}
console.log(getThisBinding.call(thisArg)); // {a: 1}
```

```javascript
function getThisBinding() {
  console.log(arguments);
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// getThisBinding 함수를 호출하면서 인수로 전달한 객체를 getThisBinding 함수의 this에 바인딩한다.
// apply 메서드는 호출할 함수의 인수를 배열로 묶어 전달한다.
console.log(getThisBinding.apply(thisArg, [1, 2, 3]));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}

// call 메서드는 호출할 함수의 인수를 쉼표로 구분한 리스트 형식으로 전달한다.
console.log(getThisBinding.call(thisArg, 1, 2, 3));
// Arguments(3) [1, 2, 3, callee: ƒ, Symbol(Symbol.iterator): ƒ]
// {a: 1}
```

- `arguments` 객체와 같은 유사 배열 객체에 배열 메서드를 사용하는 경우에 효과적

```javascript
function convertArgsToArray() {
  console.log(arguments);

  // arguments 객체를 배열로 변환
  // Array.prototype.slice를 인수없이 호출하면 배열의 복사본을 생성한다.
  const arr = Array.prototype.slice.call(arguments);
  // const arr = Array.prototype.slice.apply(arguments);
  console.log(arr);

  return arr;
}

convertArgsToArray(1, 2, 3); // [1, 2, 3]
```

- `bind`
  - 본질적인 기능은 함수를 호출하진 않고, this로 사용할 객체만 전달하는 것
  - 첫 번째 인수로 전달한 값으로 this 바인딩이 교체된 함수를 새롭게 생성해 반환

```javascript
function getThisBinding() {
  return this;
}

// this로 사용할 객체
const thisArg = { a: 1 };

// bind 메서드는 첫 번째 인수로 전달한 thisArg로 this 바인딩이 교체된
// getThisBinding 함수를 새롭게 생성해 반환한다.
console.log(getThisBinding.bind(thisArg)); // getThisBinding
// bind 메서드는 함수를 호출하지는 않으므로 명시적으로 호출해야 한다.
console.log(getThisBinding.bind(thisArg)()); // {a: 1}
```

- `메서드의 this와 메서드 내부의 중첩 함수` or `콜백 함수의 this가 불일치하는 문제 해결`에 효과적

```javascript
const person = {
  name: "Lee",
  foo(callback) {
    // ①
    setTimeout(callback, 100);
  },
};

person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // ② Hi! my name is .
  // 일반 함수로 호출된 콜백 함수 내부의 this.name은 브라우저 환경에서 window.name과 같다.
  // 브라우저 환경에서 window.name은 브라우저 창의 이름을 나타내는 빌트인 프로퍼티이며 기본값은 ''이다.
  // Node.js 환경에서 this.name은 undefined다.
});
```

```javascript
const person = {
  name: "Lee",
  foo(callback) {
    // bind 메서드로 callback 함수 내부의 this 바인딩을 전달
    setTimeout(callback.bind(this), 100);
  },
};

person.foo(function () {
  console.log(`Hi! my name is ${this.name}.`); // Hi! my name is Lee.
});
```
