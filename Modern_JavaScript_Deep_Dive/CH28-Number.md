Modern JavaScript Deep Dive 스터디 - CH28 Number

참고 자료: ⟪모던 자바스크립트 Deep Dive⟫(이웅모 지음,위키북스, 2020)

---

<br />

# 1. Number 생성자 함수

- `new` 연산자와 함께 호출하여 Number 인스턴스를 생성
  - 인수로 문자열 숫자 값을 전달: 인수를 숫자로 강제 타입 변환
    -> `[[NumberData]]` 내부 슬롯에 변환된 숫자를 할당한 `Number 래퍼 객체` 생성
  - 인수로 숫자가 아닌 값을 전달: `[[NumberData]]` 내부 슬롯에 `NaN`을 할당한 `Number 래퍼 객체` 생성

```javascript
// 문자열 타입 => 숫자 타입
Number("0"); // -> 0
Number("-1"); // -> -1
Number("10.53"); // -> 10.53

// 불리언 타입 => 숫자 타입
Number(true); // -> 1
Number(false); // -> 0

let numObj = new Number("10");
console.log(numObj); // Number {[[PrimitiveValue]]: 10}

numObj = new Number("Hello");
console.log(numObj); // Number {[[PrimitiveValue]]: NaN}
```

---

</ br>

# 2. Number 프로퍼티

### 1)Number.EPSILON

- 1과 1보다 큰 숫자 중에서 가장 작은 숫자와의 차이
- 약 2.2204460492503130808472633361816 \* 10^-16
- 부동소수점 산술의 오차 문제에 의한 정확한 계산을 위해 사용

```javascript
0.1 + 0.2; // -> 0.30000000000000004
0.1 + 0.2 === 0.3; // -> false

function isEqual(a, b) {
  // a와 b를 뺀 값의 절대값이 Number.EPSILON보다 작으면 같은 수로 인정한다.
  return Math.abs(a - b) < Number.EPSILON;
}

isEqual(0.1 + 0.2, 0.3); // -> true
```

### 2) Number.MAX_VALUE

- 자바스크립트에서 표현할 수 있는 가장 큰 양수 값
- 1.7976931348623157 \* 10^308
- Number.MAX_VALUE < Infinity

### 3) Number.MIN_VALUE

- 자바스크립트에서 표현할 수 있는 가장 작은 양수
- 5 \* 10^-324
- 0 < Number.MIN_VALUE

### 4) Number.MAX_SAFE_INTEGER

- 자바스크립트에서 안전하게 표현할 수 있는 가장 큰 정수값
- 9007199254740991

### 5) Number.MIN_SAFE_INTEGER

- 자바스크립트에서 안전하게 표현할 수 있는 가장 작은 정수값
- -9007199254740991

### 6) Number.POSITIVE_INFINITY

- `Infinity`와 같음

### 7) Number.NEGATIVE_INFINITY

- `-Infinity`와 같음

### 8) Number.NaN

- 숫자가 아님(Not-a-Number)
- `Number.NaN` == `window.NaN`

---

</ br>

# 3. Number 메서드

### 1) Number.isFinite

- 인수로 전달된 값이 `Infinity` 또는 `-Infinity`가 아닌지 검사

```javascript
// 인수가 정상적인 유한수이면 true를 반환한다.
Number.isFinite(0); // -> true
Number.isFinite(Number.MAX_VALUE); // -> true
Number.isFinite(Number.MIN_VALUE); // -> true

// 인수가 무한수이면 false를 반환한다.
Number.isFinite(Infinity); // -> false
Number.isFinite(-Infinity); // -> false
```

- 빌트인 전역 함수 `isFinite`와 차이점 존재
  - `Number.isFinite`는 전달받은 인수를 숫자로 암묵적 타입 변환하지 않음 -> 인수가 숫자값이 아니면, 항상 `false`

```javascript
// Number.isFinite는 인수를 숫자로 암묵적 타입 변환하지 않는다.
Number.isFinite(null); // -> false

// isFinite는 인수를 숫자로 암묵적 타입 변환한다. null은 0으로 암묵적 타입 변환된다.
isFinite(null); // -> true
```

### 2) Number.isInteger

- 인수로 전달된 숫자값이 정수(integer)인지 검사
- 암묵적 타입 변환은 하지 않음

```javascript
// 인수가 정수이면 true를 반환한다.
Number.isInteger(0); // -> true
Number.isInteger(123); // -> true
Number.isInteger(-123); // -> true

// 0.5는 정수가 아니다.
Number.isInteger(0.5); // -> false
// '123'을 숫자로 암묵적 타입 변환하지 않는다.
Number.isInteger("123"); // -> false
// false를 숫자로 암묵적 타입 변환하지 않는다.
Number.isInteger(false); // -> false
// Infinity/-Infinity는 정수가 아니다.
Number.isInteger(Infinity); // -> false
Number.isInteger(-Infinity); // -> false
```

### 3) Number.isNaN

- 인수로 전달된 숫자값이 NaN인지 검사
- 암묵적 타입 변환은 하지 않음
  - 숫자가 아닌 값은 항상 `false`
- 빌트인 함수 `isNaN`과 차이 존재
  - 빌트인은 암묵적 타입 변환 존재

```javascript
// 인수가 NaN이면 true를 반환한다.
Number.isNaN(NaN); // -> true

// Number.isNaN은 인수를 숫자로 암묵적 타입 변환하지 않는다.
Number.isNaN(undefined); // -> false

//undefined는 NaN으로 암묵적 타입 변환된다.
isNaN(undefined); // -> true
```

### 4) Number.isSafeInteger

- 안전한 정수인지 검사
- 암묵적 타입 변환 X

```javascript
// 0은 안전한 정수이다.
Number.isSafeInteger(0); // -> true
// 1000000000000000은 안전한 정수이다.
Number.isSafeInteger(1000000000000000); // -> true

// 10000000000000001은 안전하지 않다.
Number.isSafeInteger(10000000000000001); // -> false
// 0.5은 정수가 아니다.
Number.isSafeInteger(0.5); // -> false
// '123'을 숫자로 암묵적 타입 변환하지 않는다.
Number.isSafeInteger("123"); // -> false
// false를 숫자로 암묵적 타입 변환하지 않는다.
Number.isSafeInteger(false); // -> false
// Infinity/-Infinity는 정수가 아니다.
Number.isSafeInteger(Infinity); // -> false
```

### 5) Number.prototype.toExponential

- 숫자를 지수 표기법으로 변환하여 문자열로 반환

```javascript
(77.1234).toExponential(); // -> "7.71234e+1"
(77.1234).toExponential(4); // -> "7.7123e+1"
(77.1234).toExponential(2); // -> "7.71e+1"
```

- 정수를 변환하는 경우 사용 주의
  - 그냥 쓰면 소수점과 메서드 사용 구분이 안돼어 에러 발생함

```javascript
// 에러
77.toExponential(); // -> SyntaxError: Invalid or unexpected token

// 올바른 사용법
(77).toExponential(); // -> "7.7e+1"
// 또는
77 .toExponential(); // -> "7.7e+1"
```

### 6) Number.prototype.toFixed

- 숫자를 반올림하여 문자열로 반환
- 인수로 반올림할 소수점 자리수 전달
  - 인수 생략시 기본값은 0

```javascript
// 소수점 이하 반올림. 인수를 생략하면 기본값 0이 지정된다.
(12345.6789).toFixed(); // -> "12346"
// 소수점 이하 1자리수 유효, 나머지 반올림
(12345.6789).toFixed(1); // -> "12345.7"
// 소수점 이하 2자리수 유효, 나머지 반올림
(12345.6789).toFixed(2); // -> "12345.68"
// 소수점 이하 3자리수 유효, 나머지 반올림
(12345.6789).toFixed(3); // -> "12345.679"
```

### 7) Number.prototype.toPrecision

- 인수로 전달받은 전체 자리수까지 유효하도록 나머지 자리수를 반올림하여 문자열로 반환
- 전체 자릿수로 표현할 수 없는 경우, 지수 표기법으로 결과를 반환
- 인수로 전체 자리수를 나타내는 수 전달
  - 인수를 생략 시, 기본값은 0

```javascript
// 전체 자리수 유효. 인수를 전달하지 않으면 기본값 0이 전달된다.
(12345.6789).toPrecision(); // -> "12345.6789"
// 전체 1자리수 유효, 나머지 반올림
(12345.6789).toPrecision(1); // -> "1e+4"
// 전체 2자리수 유효, 나머지 반올림
(12345.6789).toPrecision(2); // -> "1.2e+4"
// 전체 6자리수 유효, 나머지 반올림
(12345.6789).toPrecision(6); // -> "12345.7"
```

### 8) Number.prototype.toString

- 숫자를 문자열로 변환
- 인수로 기수(radix) 전달
  - 인수 생략시, 기본값은 10 (10진법)

> n진수 문자열을 n진수 정수로 변환할 땐 `parseInt` 메서드 사용

```javascript
// 인수를 생략하면 10진수 문자열을 반환한다.
(10).toString(); // -> "10"
// 2진수 문자열을 반환한다.
(16).toString(2); // -> "10000"
// 8진수 문자열을 반환한다.
(16).toString(8); // -> "20"
// 16진수 문자열을 반환한다.
(16).toString(16); // -> "10"
```
