Modern JavaScript Deep Dive 스터디 - CH20 Strict Mode

참고 자료: ⟪모던 자바스크립트 Deep Dive⟫"(이웅모 지음,위키북스, 2020)

---

# 1. strict mode

- 자바스크립트 언어의 문법을 좀 더 엄격히 적용 -> 오류 발생 가능성을 낮추는 도구
- 전역의 선두 또는 함수 몸체의 선두에 `'use strict';`를 추가하여 적용

```javascript
// strict mode 적용 X
function foo() {
  x = 10;
}
foo();

console.log(x); // ?

// strict mode 적용 O
("use strict");

function foo() {
  x = 10; // ReferenceError: x is not defined
}
foo();
// 암묵적 전역에 대해 오류 발생시킴
```

- 함수의 선두에 선언한 경우, 해당 함수 내에서만 적용

```javascript
function foo() {
  "use strict";

  x = 10; // ReferenceError: x is not defined
}
foo();
```

- 코드의 선두에 위치시키지 않으면 strict mode가 정상적으로 동작하지 않음

```javascript
function foo() {
  x = 10; // 에러를 발생시키지 않는다.
  ("use strict");
}
foo();
```

- `ESLint`를 이용하여 `strict mode`와 같은 효과를 낼 수 있다.

---

# 2. 주의점

### 1) 전역에 strict mode를 적용하는 것은 피하자

- strict mode는 각 스크립트 단위로 적용 -> 여러 스크립트가 통합되어 사용되는 경우 stirict mode와 non-strict mode가 혼용 -> 오류 발생

```javascript
<!DOCTYPE html>
<html>
<body>
  <script>
    'use strict';
  </script>
  <script>
    x = 1; // 에러가 발생하지 않는다.
    console.log(x); // 1
  </script>
  <script>
    'use strict';

    y = 1; // ReferenceError: y is not defined
    console.log(y);
  </script>
</body>
</html>
```

- 특히, 외부 서드파티 라이브러리르 사용하는 경우가 non-strict mode 일 수 있음
- strict mode를 사용할 때, **즉시 실행 함수**로 스크립트를 감싸서 스코프를 구분해서, 다른 스크립트 파일에 영향이 받질 않도록 사용하는 것이 바람직함

```javascript
// 즉시 실행 함수의 선두에 strict mode 적용
(function () {
  "use strict";

  // Do something...
})();
```

### 2) 함수 단위로 strict mode를 적용하는 것도 피하자

- 어떤 함수는 strict mode를 사용 , 어떤 함수는 strict mode를 사용하지 않는 것은 바람직하지 않음
- strict mode 는 **즉시 실행 함수**로 감싼 스크립트 단위로 적용하는 것이 바람직

---

# 3. strict mode가 발생시키는 에러

### 1) 암묵적 전역

- 선언하지 않은 변수를 참조 -> `ReferenceError`

```javascript
(function () {
  "use strict";

  x = 1;
  console.log(x); // ReferenceError: x is not defined
})();
```

### 2) 변수, 함수, 매개변수의 삭제

- delete 연산자로 변수, 함수, 매개변수를 삭제 -> `SyntaxError`

```javascript
(function () {
  "use strict";

  var x = 1;
  delete x;
  // SyntaxError: Delete of an unqualified identifier in strict mode.

  function foo(a) {
    delete a;
    // SyntaxError: Delete of an unqualified identifier in strict mode.
  }
  delete foo;
  // SyntaxError: Delete of an unqualified identifier in strict mode.
})();
```

### 3) 매개변수 이름의 중복

- 중복된 매개변수 이름을 사용 -> `SyntaxError`

```javascript
(function () {
  "use strict";

  //SyntaxError: Duplicate parameter name not allowed in this context
  function foo(x, x) {
    return x + x;
  }
  console.log(foo(1, 2));
})();
```

### 4) with 문의 사용

- with 문을 사용 -> `SyntaxError`
- with문은 성능과 가독성이 나빠지는 문제가 있음 -> 사용하지 않는 것이 바람직함

```javascript
(function () {
  "use strict";

  // SyntaxError: Strict mode code may not include a with statement
  with ({ x: 1 }) {
    console.log(x);
  }
})();
```

---

# 4. strict mode 적용에 의한 변화

### 1) 일반 함수의 this

- strict mode에서 함수를 **일반 함수**로서 호출하면 this에 undefined가 바인딩
- 생성자 함수가 아닌 일반 함수 내부에서는 this 를 사용할 필요가 없기 때문

```javascript
(function () {
  "use strict";

  function foo() {
    console.log(this); // undefined
  }
  foo();

  function Foo() {
    console.log(this); // Foo
  }
  new Foo();
})();
```

### 2) arguments 객체

- strict mode에서는 매개변수에 전달된 인수를 재할당하여 변경해도 arguments 객체에 반영되지 않음

```javascript
(function (a) {
  "use strict";
  // 매개변수에 전달된 인수를 재할당하여 변경
  a = 2;

  // 변경된 인수가 arguments 객체에 반영되지 않는다.
  console.log(arguments); // { 0: 1, length: 1 }
})(1);
```
