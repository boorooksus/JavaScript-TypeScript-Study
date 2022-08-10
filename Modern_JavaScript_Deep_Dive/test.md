Modern JavaScript Deep Dive 스터디 - CH19 프로토타입

참고 자료: ⟪모던 자바스크립트 Deep Dive⟫"(이웅모 지음,위키북스, 2020)

---

# 1. 객체지향 프로그래밍

- 여러 개의 독립적 단위, 즉 객체(object)의 집합으로 프로그래밍을 표현하려는 프로그래밍 패러다임
  > ### **객체(object)**
  >
  > - 상태(state) 를 나타내는 `프로퍼티(property)` + 상태를 조작하는 행동(behavior) 을 표현하는 `메서드(method)` 로 구성
  >   => 상태 데이터와 동작을 하나의 논리적인 단위로 묶은 복합적인 자료구조
- 자바스크립트는 객체 기반의 프로그래밍 언어
- 자바스크립트를 이루고 있는 거의 모든 것이 객체
- 자바스크립트는 프로토타입 기반의 객체지향 프로그래밍 언어

> ### **추상화(abstraction)**
>
> - 객체의 다양한 속성 중에서 프로그램에 필요한 속성만 간추려 내여 표현하는 것
> - 속성을 통해 여러 개의 값을 하나의 단위로 구성한 복합적인 자료구조를 객체 라고 표현할 수 있음

---

# 2. 상속(inheritance)

- 어떤 객체의 프로퍼티 or 메서드를 다른 객체가 상속받아 그대로 사용할 수 있는 것
- 자바스크립트는 프로토타입을 기반으로 상속을 구현 -> 불필요한 중복을 제거 → 코드를 재사용
- 객체지향 프로그래밍의 핵심 개념
- 상속을 사용하지 않는 예시(메모리 낭비)

```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
  this.getArea = function () {
    // Math.PI는 원주율을 나타내는 상수다.
    return Math.PI * this.radius ** 2;
  };
}

// 반지름이 1인 인스턴스 생성
const circle1 = new Circle(1);
// 반지름이 2인 인스턴스 생성
const circle2 = new Circle(2);

// Circle 생성자 함수는 인스턴스를 생성할 때마다 동일한 동작을 하는
// getArea 메서드를 중복 생성하고 모든 인스턴스가 중복 소유한다.
// getArea 메서드는 하나만 생성하여 모든 인스턴스가 공유해서 사용하는 것이 바람직하다.
console.log(circle1.getArea === circle2.getArea); // false

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

- 상속을 사용하는 예시

```javascript
// 생성자 함수
function Circle(radius) {
  this.radius = radius;
}

// Circle 생성자 함수가 생성한 모든 인스턴스가 getArea 메서드를
// 공유해서 사용할 수 있도록 프로토타입에 추가한다.
// 프로토타입은 Circle 생성자 함수의 prototype 프로퍼티에 바인딩되어 있다.
Circle.prototype.getArea = function () {
  return Math.PI * this.radius ** 2;
};

// 인스턴스 생성
const circle1 = new Circle(1);
const circle2 = new Circle(2);

// Circle 생성자 함수가 생성한 모든 인스턴스는 부모 객체의 역할을 하는
// 프로토타입 Circle.prototype으로부터 getArea 메서드를 상속받는다.
// 즉, Circle 생성자 함수가 생성하는 모든 인스턴스는 하나의 getArea 메서드를 공유한다.
console.log(circle1.getArea === circle2.getArea); // true

console.log(circle1.getArea()); // 3.141592653589793
console.log(circle2.getArea()); // 12.566370614359172
```

---

# 3. 프로토타입(prototype)

- 객체 간 상속을 구현하기 위해 사용
- 모든 객체(인스턴스)는 자신의 프로토타입, 즉 상위(부모) 객체 역할을 하는 prototype 의 모든 프로퍼티와 메서드를 상속받음
- 프로토타입을 상속받은 하위(자식) 객체는 상위 객체의 프로퍼티를 자신의 프로퍼티처럼 자유롭게 사용 가능
- 모든 객체는 `[[Prototype]]` 이라는 내부 슬롯을 가짐 -> 이 내부 슬롯의 값은 프로토타입(null인 경우도 있음)
- `[[Prototype]]` 에 저장되는 프로토타입은 객체 생성 방식 에 의해 결정
  > - 객체 리터럴로 생성된 객체의 prototype: Object.prototype
  > - 생성자 함수에 의해 생성된 객체의 prototype: 생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체
- 모든 객체는 하나의 프로토타입을 가짐
- 모든 프로토타입은 생성자 함수와 연결됨

### 1) `__proto__` 접근자 프로퍼티

- 모든 객체는 `__proto__` 접근자 프로퍼티를 통해 자신의 프로토타입, 즉 자신의 `[[Prototype]]` 내부 슬롯이 가리키는 프로토타입에 **간접적으로** 접근 가능

### 2) `__proto__` 주의사항

(1) ` __proto__`는 접근자 프로퍼티

- 자바스크립트는 원칙적으로 내부 슬롯과 내부 메서드에 직접 접근하거나 호출 불가 -> ` __proto__` 접근자 프로퍼티를 통해 간접적으로 프로토타입에 접근

(2) `__proto__` 접근자 프로퍼티는 상속을 통해 사용

- `__proto__` 접근자 프로퍼티는 객체가 직접 소유하는 프로퍼티가 아니라, `Object.proptotype`의 프로퍼티

```javascript
const person = { name: "Lee" };

// person 객체는 __proto__ 프로퍼티를 소유하지 않는다.
console.log(person.hasOwnProperty("__proto__")); // false

// __proto__ 프로퍼티는 모든 객체의 프로토타입 객체인 Object.prototype의 접근자 프로퍼티다.
console.log(Object.getOwnPropertyDescriptor(Object.prototype, "__proto__"));
// {get: ƒ, set: ƒ, enumerable: false, configurable: true}

// 모든 객체는 Object.prototype의 접근자 프로퍼티 __proto__를 상속받아 사용할 수 있다.
console.log({}.__proto__ === Object.prototype); // true
```

(3) `__proto__` 접근자 프로퍼티를 통해 프로토타입에 접근하는 이유

- `상호 참조` 에 의해 프로토타입 체인(참조 루프)이 생성되는 것을 방지하기 위해서
- 프로토타입 체인은 단방향 링크드 리스트로 구현되어야 함 -> 순환 참조하는 프로토타입 체인이 만들어지면 무한 루프에 빠져 에러를 발생

```javascript
const parent = {};
const child = {};

// child의 프로토타입을 parent로 설정
child.__proto__ = parent;
// parent의 프로토타입을 child로 설정
parent.__proto__ = child; // TypeError: Cyclic __proto__ value
```

(4) **proto** 접근자 프로퍼티를 코드 내에서 직접 사용하는 것은 권장하지 않음

- 모든 객체가 **proto** 접근자 프로퍼티를 사용할 수 있는 것은 아니기 때문
- 프로토타입의 참조를 취득하고 싶은 경우 `Object.getPrototpyeOf` 메서드 이용
- 프로토타입 교체에 경우 `Object.setPrototypeOf` 메서드를 사용

```javascript
const obj = {};
const parent = { x: 1 };

// obj 객체의 프로토타입을 취득
Object.getPrototypeOf(obj); // obj.__proto__;
// obj 객체의 프로토타입을 교체
Object.setPrototypeOf(obj, parent); // obj.__proto__ = parent;

console.log(obj.x); // 1
```

### 3) 함수 객체의 prototype 프로퍼티

- 함수 객체만이 소유하는 `prototype` 프로퍼티는 생성자 함수가 생성할 인스턴스의 프로토타입을 가리킴

```javascript
// 함수 객체는 prototype 프로퍼티를 소유한다.
(function () {}.hasOwnProperty("prototype")); // -> true

// 일반 객체는 prototype 프로퍼티를 소유하지 않는다.
({}.hasOwnProperty("prototype")); // -> false
```

> 질문: 모든 객체는 `[[Prototype]]` 내부 슬롯을 가지지만, `prototype` 이라는 이름의 프로퍼티는 함수만 가진다?

- 생성자 함수로 호출할 수 없는 non-constructor인 `화살표 함수`와 `ES6 메서드 축약 표현`으로 정의한 메서드는 **prototype 프로퍼티를 소유하지 않으며 프로토타입도 생성하지 않음**

```javascript
// 화살표 함수는 non-constructor다.
const Person = (name) => {
  this.name = name;
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(Person.hasOwnProperty("prototype")); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(Person.prototype); // undefined

// ES6의 메서드 축약 표현으로 정의한 메서드는 non-constructor다.
const obj = {
  foo() {},
};

// non-constructor는 prototype 프로퍼티를 소유하지 않는다.
console.log(obj.foo.hasOwnProperty("prototype")); // false

// non-constructor는 프로토타입을 생성하지 않는다.
console.log(obj.foo.prototype); // undefined
```

- 모든 객체가 가지고 있는 `__proto__` 접근자 프로퍼티와 함수 객체만이 가지고 있는 `prototype` 프로퍼티는 동일한 프로토타입을 가리킴 But 사용 주체가 다름

|             | 소유        | 값              | 사용 주체   | 사용 목적                                                                         |
| ----------- | ----------- | --------------- | ----------- | --------------------------------------------------------------------------------- |
| `__proto__` | 모든 객체   | 프로토타입 참조 | 모든 객체   | 객체가 자신의 프로토타입에 접근</br> 또는 교체하기 위해 사용                      |
| `prototype` | constructor | 프로토타입 참조 | 생성자 함수 | 생성자 함수가 자신이 생성할 객체(인스턴스)의</br> 프로토타입을 할당하기 위해 사용 |

```javascript
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 결국 Person.prototype과 me.__proto__는 결국 동일한 프로토타입을 가리킨다.
console.log(Person.prototype === me.__proto__); // true
```

### 4) 프로토타입의 constructor 프로퍼티와 생성자 함수

- 모든 프로토타입은 constructor 프로퍼티를 가짐
- constructor 프로퍼티는 자신을 참조하고 있는 생성자 함수를 가리킴
- 이 연결은 생성자 함수가 생성될 때, 즉 함수 객체가 생성될 때 이뤄짐

```javascript
// obj 객체를 생성한 생성자 함수는 Object다.
const obj = new Object();
console.log(obj.constructor === Object); // true

// add 함수 객체를 생성한 생성자 함수는 Function이다.
const add = new Function("a", "b", "return a + b");
console.log(add.constructor === Function); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// me 객체를 생성한 생성자 함수는 Person이다.
const me = new Person("Lee");
console.log(me.constructor === Person); // true
```

---

# 4. 리터럴 표기법에 의해 생성된 객체의 생성자 함수와 프로토타입

```javascript
// 객체 리터럴
const obj = {};

// 함수 리터럴
const add = function (a, b) {
  return a + b;
};

// 배열 리터럴
const arr = [1, 2, 3];

// 정규표현식 리터럴
const regexp = /is/gi;
```

- 리터럴 표기법으로 생성된 객체의 프로토타입의 경우, constructor 프로퍼티가 가리키는 생성자 함수는 반드시 객체를 생성한 생성자 함수가 아닐 수 있음

```javascript
// obj 객체는 Object 생성자 함수로 생성한 객체가 아니라 객체 리터럴로 생성했다.
const obj = {};

// 하지만 obj 객체의 생성자 함수는 Object 생성자 함수다.
console.log(obj.constructor === Object); // true
```

```javascript
// foo 함수는 Function 생성자 함수로 생성한 함수 객체가 아니라 함수 선언문으로 생성했다.
function foo() {}

// 하지만 constructor 프로퍼티를 통해 확인해보면 함수 foo의 생성자 함수는 Function 생성자 함수다.
console.log(foo.constructor === Function); // true
```

- 리터럴 표기법에 의해 생성된 객체는 추상 연산 `OrdinaryObjectCreate` 를 호출하여 빈 객체를 생성하고 프로퍼티를 추가
- 프로토타입과 생성자 함수는 단독으로 존재할 수 없고 언제나 쌍으로 존재
- => 리터럴 표기법에 의해 생성된 객체도 상속을 위해 프로토타입이 필요 -> 가상적인 생성자 함수를 가짐
  > ### **ECMAScript의 `OrdinaryObjectCreate` 추상연산 호출에 의한 객체 생성**
  >
  > - 추상연산 : ECMAScript 내부 동작의 구현 알고리즘. ECMAScript 사양에서 설명을 위해 사용되는 함수와 유사한 의사 코드
  > - `OrdinaryObjectCreate`를 호출하면, 기본적으로 `Object.prototype`를 프로토타입으로 갖는 빈 객체를 생성
  > - Object 생성자 함수 호출과 객체 리터럴의 평가는 OrdinaryObjectCreate를 호출 해서 빈 객체를 생성하는다는 점은 동일 **But** `new.target` 의 확인, 프로퍼티 추가하는 처리 등 세부 처리에서 차이가 있음
  > - => 객체 리터럴에 의해 생성된 객체 !== Object 생성자 함수가 생성한 객체

---

# 5. 프로토타입의 생성 시점

- 프로토타입은 생성자 함수가 생성되는 시점에 같이 생성
- 프로토타입과 생성자 함수는 단독으로 존재할 수 없고, 쌍으로 존재하기 때문

### 1) 사용자 정의 생성자 함수와 프로토타입 생성 시점

- constructor(함수 선언문, 함수 표현식, 클래스)는 함수 정의가 평가되어 함수 객체를 생성하는 시점에 프로토타입도 더불어 생성
- 생성된 프로토타입은 오직 constructor 프로퍼티만을 갖는 객체
- 프로토타입도 객체이며, 모든 객체는 프로토타입을 가짐
- => 프로토타입도 자신의 프로토타입을 가짐
- 생성된 프로토타입의 프로토타입은 `Object.prototype`
  ![constructor](https://user-images.githubusercontent.com/55964775/178539339-eec271fc-24f8-4748-952b-a4d4b024ec2e.png)

### 2) 빌트인 생성자 함수와 프로토타입 생성 시점

- 빌트인 생성자 함수: `Object`, `String`, `Number`, `Function`, `Array`, `RegExp`, `Date`, `Promise`
- 빌트인 생성자 함수도 생성되는 시점에 프로토타입이 생성 -> 일반 함수와 동일
- 객체가 생성되기 이전에 생성자 함수와 프로토타입은 이미 객체화 되어 존재 -> 모든 빌트인 생성자 함수는 전역 객체가 생성되는 시점에 생성
- -> 이후 생성자 함수 또는 리터럴 표기법으로 객체를 생성하면 프로토타입은 생성된 객체의 `[[Prototype]]` 내부 슬롯에 할당

```javascript
// 전역 객체 window는 브라우저에 종속적이므로 아래 코드는 브라우저 환경에서 실행해야 한다.
// 빌트인 객체인 Object는 전역 객체 window의 프로퍼티다.
window.Object === Object; // true
```

---

# 6. 객체 생성 방식과 프로토타입의 결정

> ### **객체 생성 방식**
>
> - 객체 리터럴
> - Object 생성자 함수
> - 생성자 함수
> - Object.create 메서드
> - 클래스(ES6)

- 위의 여러가지 객체 생성 방식들은 모두 추상 연산 `OrdinaryObjectCreate` 호출에 의해 객체가 생성됨
- 추상 연산 `OrdinaryObjectCreare`가 빈 객체 생성
  -> 객체에 추가할 프로퍼티 목록이 인수로 전달될 경우 프로퍼티를 객체에 추가
  -> 인수로 전달받은 프로토타입을 자신이 생성한 객체의 `[[Prototype]]` 내부 슬롯에 할당
  -> 생성한 객체를 반환

### 1) 객체 리터럴에 의해 생성된 객체의 프로토타입

- 객체 리터럴에 의해 생성되는 객체의 프로토타입은 `Object.prototype`

```javascript
const obj = { x: 1 };

// 객체 리터럴에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

### 2) Object 생성자 함수에 의해 생성된 객체의 프로토타입

- Object 생성자 함수에 의해 생성되는 객체의 프로토타입은 `Object.prototype`

```javascript
const obj = new Object();
obj.x = 1;

// Object 생성자 함수에 의해 생성된 obj 객체는 Object.prototype을 상속받는다.
console.log(obj.constructor === Object); // true
console.log(obj.hasOwnProperty("x")); // true
```

### 3) 생성자 함수에 의해 생성된 객체의 프로토타입

- 생성자 함수에 의해 생성되는 객체의 프로토타입은 `생성자 함수의 prototype 프로퍼티에 바인딩되어 있는 객체`

```javascript
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");
const you = new Person("Kim");

Object.getPrototypeOf(me) === Person.prototype; // -> true
```

- Object 생성자 함수와 객체 리터럴로 생성된 객체의 프로토타입인 `Object.prototype` 과 달리, 오로지 constructor 프로퍼티만 존재
  ![constructor func](https://user-images.githubusercontent.com/55964775/178544616-eab5bc73-e84b-4251-b326-93c94da89a6c.png)

---

# 7. 프로토타입 체인

- 프로토타입의 프로토타입은 언제나 `Object.prototype`
- 자바스크립트는 객체의 프로퍼티(메서드 포함)에 접근하려고 할 때, 해당 객체에 접근하려는 프로퍼티가 있는지 확인
  -> 프로퍼티가 없다면 `[[Prototype]]` 내부 슬롯의 참조값 을 따라, 자신의 부모 역할을 하는 프로토타입의 프로퍼티를 순차적으로 검색
  => 이를 `프로토타입 체인` 이라고 함
- 자바스크립트가 객체지향 프로그래밍의 상속을 구현하는 메커니즘이됨

```javascript
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// hasOwnProperty는 Object.prototype의 메서드다.
// me 객체는 프로토타입 체인을 따라 hasOwnProperty 메서드를 검색하여 사용한다.
me.hasOwnProperty("name"); // -> true
// hasOwnProperty 메서드가 me 객체에 프로퍼티에 존재하는지 검색
// -> 프로퍼티가 없음
// -> me 객체의 [[Prototype]] 내부 슬롯의 참조값을 통해, Person.prototype 객체의 프로퍼티를 검색
// -> Person.prototype에도 hasOwnProperty 메서드가 없음
// -> [[Prototype]] 내부 슬롯의 참조값을 통해 Object.prototype 프로퍼티를 검색
// -> Object.prototype 에는 hasOwnProperty 메서드가 있음!
// -> 자바스크립트 엔진은 Object.prototype.hasOwnProperty 메서드를 호출
```

- 프로토타입 체인의 최상위에 위치하는 객체는 언제나 `Object.prototype`
- -> 모든 객체는 `Object.prototype`을 상속받음
- `Object.prototype` 을 프로토타입 체인의 종점(end of prototype chain) 이라고 함
- `Object.prototype` 의 프로토타입은 없다. 즉, `null`
- `Object.prototype`에도 없는 프로퍼티를 조회할 경우, undefined 를 반환

> ### **프로토타입 체인 vs 스코프 체인**
>
> - **프로토타입 체인**: 상속과 프로퍼티 검색을 위한 메커니즘
> - **스코프 체인**: 식별자 검색을 위한 메커니즘

---

# 8. 오버라이딩과 프로퍼티 섀도잉

> `프로토타입 프로퍼티`: 프로토타입이 소유한 프로퍼티
> `인스턴스 프로퍼티`: 인스턴스가 소유한 프로퍼티

### 1) 오버라이딩(overriding)

- 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식

> ### **오버라이딩 vs 오버로딩**
>
> `오버라이딩(overriding)`: 상위 클래스가 가지고 있는 메서드를 하위 클래스가 재정의하여 사용하는 방식
> `오버로딩`: 함수의 이름은 동일하나, 매개변수의 타입 또는 개수가 다른 메서드를 구현 -> 매개변수에 의해 메서드를 구별하여 호출하는 방식.
> 바스크립트는 오버로딩을 지원하지 않지만 arguments 객체를 사용하여 구현할 수는 있음

### 2) 프로퍼티 섀도잉(property shadowing)

- 상속 관계에 의해 프로퍼티가 가려지는 현상
- 프로토타입 프로퍼티와 같은 이름의 프로퍼티를 인스턴스에 추가 -> 프로토타입 메서드는 인스턴스 메서드로 인해 프로퍼티가 가려짐

```javascript
const Person = (function () {
  // 생성자 함수
  function Person(name) {
    this.name = name;
  }

  // 프로토타입 메서드
  Person.prototype.sayHello = function () {
    console.log(`Hi!`);
  };

  // 생성자 함수를 반환
  return Person;
})();

const me = new Person("Lee");

// 인스턴스 메서드
me.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};

// 인스턴스 메서드가 호출된다. 프로토타입 메서드는 인스턴스 메서드에 의해 가려진다.
me.sayHello(); // Hey! My name is Lee
```

- 프로토타입 프로퍼티를 덮어쓰는 것은 아님!. 하위 객체를 통해 프토토타입의 프로퍼티를 변경 또는 삭제하는 것은 불가능
  -> 오버라이딩 개념을 통해, 프로토타입에 프로퍼티를 추가(get 엑세스) 는 가능하나, 프로퍼티를 변경/삭제(set 엑세스) 는 불가

```javascript
// 인스턴스 메서드를 삭제한다.
delete me.sayHello;
// 인스턴스에는 sayHello 메서드가 없으므로 프로토타입 메서드가 호출된다.
me.sayHello(); // Hi!
```

```javascript
// 프로토타입 메서드 변경
Person.prototype.sayHello = function () {
  console.log(`Hey! My name is ${this.name}`);
};
me.sayHello(); // Hey! My name is Lee

// 프로토타입 메서드 삭제는 불가
delete Person.prototype.sayHello;
me.sayHello(); // TypeError: me.sayHello is not a function
```

---

# 9. 프로토타입의 교체

- 프로토타입은 다른 객체로 변경 가능
  -> 부모 객체인 프로토타입을 동적으로 변경 가능
  -> 객체 간의 상속 관계를 동적으로 변경
- 프로토타입 교체 방법은 **생성자 함수에 의한 교체**, **인스턴스에 의해 의한에 의한 교체** 가 있음
- **But** 프로토타입 교체를 통한 객체 간의 상속 관계 변경은 번거롭고 바람직하지 않음
  -> `직접 상속` 이나 `ES6+ 의 클래스`를 사용하는게 바람직

### 1) 생성자 함수에 의한 프로토타입 교체

```javascript
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  // Person.prototype에 객체 리터럴을 할당
  // -> Person 생성자 함수가 생성할 객체의 프로토타입을 객체 리터럴로 교체
  // -> 객체 리터럴에는 constructor 프로퍼티가 없음
  // -> me 의 생성자 함수는 Person이 아닌 Object로 바뀜
  Person.prototype = {
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Lee");

// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

- 프로토타입을 교체하게 되면 constructor프로퍼티와 생성자 함수간의 연결이 파괴됨
- constructor 프로퍼티를 추가하여 프로토타입의 constructor 프로퍼티를 되살릴 수 있음

```javascript
const Person = (function () {
  function Person(name) {
    this.name = name;
  }

  // 생성자 함수의 prototype 프로퍼티를 통해 프로토타입을 교체
  Person.prototype = {
    // constructor 프로퍼티와 생성자 함수 간의 연결을 설정
    constructor: Person,
    sayHello() {
      console.log(`Hi! My name is ${this.name}`);
    },
  };

  return Person;
})();

const me = new Person("Lee");

// constructor 프로퍼티가 생성자 함수를 가리킨다.
console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false
```

### 2) 인스턴스에 의한 프로토타입의 교체

- 인스턴스의 `__proto__` 접근자 프로퍼티 또는 `Object.setPrototypeOf` 메서드를 통해 프로퍼티 교체
- 생성자 함수의 prototype 프로퍼티에 다른 임의의 객체를 바인딩 -> 미래에 생성할 인스턴스의 프로토타입을 교체하는 것

```javascript
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  },
};

// me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee

// 프로토타입을 교체하면 constructor 프로퍼티와 생성자 함수 간의 연결이 파괴된다.
console.log(me.constructor === Person); // false
// 프로토타입 체인을 따라 Object.prototype의 constructor 프로퍼티가 검색된다.
console.log(me.constructor === Object); // true
```

- constructor 프로퍼티를 추가하여 프로토타입의 constructor 프로퍼티를 되살릴 수 있음

```javascript
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {
  // constructor 프로퍼티와 생성자 함수 간의 연결을 설정
  constructor: Person,
  sayHello() {
    console.log(`Hi! My name is ${this.name}`);
  },
};

// 생성자 함수의 prototype 프로퍼티와 프로토타입 간의 연결을 설정
Person.prototype = parent;

// me 객체의 프로토타입을 parent 객체로 교체한다.
Object.setPrototypeOf(me, parent);
// 위 코드는 아래의 코드와 동일하게 동작한다.
// me.__proto__ = parent;

me.sayHello(); // Hi! My name is Lee

// constructor 프로퍼티가 생성자 함수를 가리킨다.
console.log(me.constructor === Person); // true
console.log(me.constructor === Object); // false

// 생성자 함수의 prototype 프로퍼티가 교체된 프로토타입을 가리킨다.
console.log(Person.prototype === Object.getPrototypeOf(me)); // true
```

---

# 10. instanceof 연산자

- 이항 연산자
- <span style="color:orange">좌변</span>에 <span style="color:orange">객체를 가리키는 식별자</span>, <span style="color:skyblue">우변</span>에 <span style="color:skyblue">생성자 함수를 가리키는 식별자</span>를 피연산자로 받음

```javascript
[객체] instanceof [생성자 함수]
```

- 우변의 피연산자가 함수가 아닌 경우 `TypeError`가 발생
- 우변의 생성자 함수의 `prototype`에 바인딩된 객체가 좌변의 객체의 **프로토타입 체인 상에 존재하는지 체크**
  - 존재하면 true, 존재하지 않으면 false
- 프로토타입의 constructor 프로퍼티가 가리키는 생성자 함수를 찾는 것이 아님!

```javascript
// 생성자 함수
// 생성자 함수
function Person(name) {
  this.name = name;
}

const me = new Person("Lee");

// 프로토타입으로 교체할 객체
const parent = {};

// 프로토타입의 교체
Object.setPrototypeOf(me, parent);

// Person 생성자 함수와 parent 객체는 연결되어 있지 않다.
console.log(Person.prototype === parent); // false
console.log(parent.constructor === Person); // false

// parent 객체를 Person 생성자 함수의 prototype 프로퍼티에 바인딩한다.
Person.prototype = parent;

// Person.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Person); // true

// Object.prototype이 me 객체의 프로토타입 체인 상에 존재하므로 true로 평가된다.
console.log(me instanceof Object); // true
```

---

# 11. 직접 상속

### 1) `Object.create` 메서드에 의한 직접 상속

- 명시적으로 프로토타입을 지정 -> 새로운 객체를 생성
- 첫 번째 매개변수에는 생성할 객체의 프로토타입으로 지정할 객체를 전달
- 두 번째 매개변수에는 생성할 객체의 프로퍼티 키와 프로퍼티 디스크립터 객체로 이뤄진 객체를 전달
  - 두 번째 인수는 옵션이므로 생략 가능

```javascript
// 프로토타입이 null인 객체를 생성한다. 생성된 객체는 프로토타입 체인의 종점에 위치한다.
// obj → null
let obj = Object.create(null);
console.log(Object.getPrototypeOf(obj) === null); // true
// Object.prototype을 상속받지 못한다.
console.log(obj.toString()); // TypeError: obj.toString is not a function

// obj → Object.prototype → null
// obj = {};와 동일하다.
obj = Object.create(Object.prototype);
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

// obj → Object.prototype → null
// obj = { x: 1 };와 동일하다.
obj = Object.create(Object.prototype, {
  x: { value: 1, writable: true, enumerable: true, configurable: true },
});
// 위 코드는 다음과 동일하다.
// obj = Object.create(Object.prototype);
// obj.x = 1;
console.log(obj.x); // 1
console.log(Object.getPrototypeOf(obj) === Object.prototype); // true

const myProto = { x: 10 };
// 임의의 객체를 직접 상속받는다.
// obj → myProto → Object.prototype → null
obj = Object.create(myProto);
console.log(obj.x); // 10
console.log(Object.getPrototypeOf(obj) === myProto); // true

// 생성자 함수
function Person(name) {
  this.name = name;
}

// obj → Person.prototype → Object.prototype → null
// obj = new Person('Lee')와 동일하다.
obj = Object.create(Person.prototype);
obj.name = "Lee";
console.log(obj.name); // Lee
console.log(Object.getPrototypeOf(obj) === Person.prototype); // true
```

- 다른 객체 생성 방식과 마찬가지로 추상 연산 `OrdinaryObjectCreate`를 호출
- 다른 점은 객체를 생성하면서 직접적으로 상속을 구현한다는 것
  - new 연산자 없이도 객체를 생성 가능
  - 프로토타입을 지정하면서 객체를 생성 가능
  - 객체 리터럴에 의해 생성된 객체도 상속 가능
- ESLint 에서는 `Object.create`의 빌트인 메서드를 객체가 직접 호출하는 것을 권장하지 않음
  - `Object.create` 메서드를 통해 프로토타입 체인의 종점에 위치하는 객체를 생성할 수 있기 때문
  - `Object.prototype`의 빌트인 메서드는 간접적으로 호출하는 것이 바람직

```javascript
// 프로토타입이 null인 객체, 즉 프로토타입 체인의 종점에 위치하는 객체를 생성한다.
const obj = Object.create(null);
obj.a = 1;

console.log(Object.getPrototypeOf(obj) === null); // true

// obj는 Object.prototype의 빌트인 메서드를 사용할 수 없다.
console.log(obj.hasOwnProperty("a")); // TypeError: obj.hasOwnProperty is not a function
```

### 2) 객체 리터럴 내부에서 proto 에 의한 직접 상속

- Object.create 로 객체를 생성할 때, 두 번째 파라미터로 프로퍼티를 정의하는 것에 번거로움
  -> ES6에서는 객체 리터럴 내부에서 `__proto__` 접근자 프로퍼티를 사용하여 직접 상속을 구현

```javascript
const myProto = { x: 10 };

// 객체 리터럴에 의해 객체를 생성하면서 프로토타입을 지정하여 직접 상속받을 수 있다.
const obj = {
  y: 20,
  // 객체를 직접 상속받는다.
  // obj → myProto → Object.prototype → null
  __proto__: myProto,
};
/* 위 코드는 아래와 동일하다.
const obj = Object.create(myProto, {
  y: { value: 20, writable: true, enumerable: true, configurable: true }
});
*/

console.log(obj.x, obj.y); // 10 20
console.log(Object.getPrototypeOf(obj) === myProto); // true
```

> ### 질문: `__proto__`를 이용한 프로토타입 교체와 상속

[여기](#1-)

---

# 12. 정적 프로퍼티/메서드

- 생성자 함수로 인스턴스를 생성하지 않아도 참조/호출할 수 있는 프로퍼티/메서드
- 생성자 함수도 객체
  -> 생성자 함수도 프로퍼티나 메서드를 소유 가능
  -> 생성자 함수가 소유한 프로퍼티나 메서드를 정적 프로퍼티/메서드
- 생성자 함수가 소유하고 있는 정적 프로퍼티/메서드는인스턴스에서 직접 참조/호출할 수 없음
