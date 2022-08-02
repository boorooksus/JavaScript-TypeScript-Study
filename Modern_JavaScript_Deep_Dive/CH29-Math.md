Modern JavaScript Deep Dive 스터디 - CH29 Math

참고 자료: ⟪모던 자바스크립트 Deep Dive⟫(이웅모 지음,위키북스, 2020)

---

<br />

# 1. Math

- 표준 빌트인 객체
- 생성자 함수가 아님 -> `new` 키워드 없이 정적 메서드와 정적 프로퍼티만 사용

---

</ br>

# 2. Math 프로퍼티

### 1) Math.PI

- 원주율 파이 반환

```javascript
Math.PI; // -> 3.141592653589793
```

---

</ br>

# 3. Math 메서드

### 1) Math.abs

- 숫자의 절대값을 반환

```javascript
Math.abs(-1); // -> 1
Math.abs("-1"); // -> 1
Math.abs(""); // -> 0
Math.abs([]); // -> 0
Math.abs(null); // -> 0
Math.abs(undefined); // -> NaN
Math.abs({}); // -> NaN
Math.abs("string"); // -> NaN
Math.abs(); // -> NaN
```

### 2) Math.round

- 인수로 전달된 숫자의 소수점 이하를 반올림

```javascript
Math.round(1.4); // -> 1
Math.round(1.6); // -> 2
Math.round(-1.4); // -> -1
Math.round(-1.6); // -> -2
Math.round(1); // -> 1
Math.round(); // -> NaN
```

### 3) Math.ceil

- 인수로 전달된 숫자의 소수점 이하를 올림

```javascript
Math.ceil(1.4); // -> 2
Math.ceil(1.6); // -> 2
Math.ceil(-1.4); // -> -1
Math.ceil(-1.6); // -> -1
Math.ceil(1); // -> 1
Math.ceil(); // -> NaN
```

### 4) Math.floor

- 인수로 전달된 숫자의 소수점 이하를 내림

```javascript
Math.floor(1.9); // -> 1
Math.floor(9.1); // -> 9
Math.floor(-1.9); // -> -2
Math.floor(-9.1); // -> -10
Math.floor(1); // -> 1
Math.floor(); // -> NaN
```

### 5) Math.sqrt

- 제곱근을 리턴

```javascript
Math.sqrt(9); // -> 3
Math.sqrt(-9); // -> NaN
Math.sqrt(2); // -> 1.414213562373095
Math.sqrt(1); // -> 1
Math.sqrt(0); // -> 0
Math.sqrt(); // -> NaN
```

### 6) Math.random

- 0 이상 1 미만 랜덤 숫자 리턴

```javascript
Math.random(); // 0에서 1 미만의 랜덤 실수(0.8208720231391746)

/*
1에서 10 범위의 랜덤 정수 취득
1) Math.random으로 0에서 1 미만의 랜덤 실수를 구한 다음, 10을 곱해 0에서 10 미만의
랜덤 실수를 구한다.
2) 0에서 10 미만의 랜덤 실수에 1을 더해 1에서 10 범위의 랜덤 실수를 구한다.
3) Math.floor로 1에서 10 범위의 랜덤 실수의 소수점 이하를 떼어 버린 다음 정수를 반환한다.
*/
const random = Math.floor(Math.random() * 10 + 1);
console.log(random); // 1에서 10 범위의 정수
```

### 7) Math.pow

- 숫자의 거듭제곱 결과 리턴
- 인수는 base(밑), exponent(지수) 순으로 받음
- `Math.pow` 대신 `지수 연산자`를 사용하는게 가독성 더 좋음

```javascript
Math.pow(2, 8); // -> 256
Math.pow(2, -1); // -> 0.5
Math.pow(2); // -> NaN

// ES7 지수 연산자
2 ** (2 ** 2); // -> 16
Math.pow(Math.pow(2, 2), 2); // -> 16
```

### 8) Math.max

- 인수 중에서 가장 큰 수를 반환
- 인수 없으면 `-Infinity` 리턴

```javascript
Math.max(1); // -> 1
Math.max(1, 2); // -> 2
Math.max(1, 2, 3); // -> 3
Math.max(); // -> -Infinity
```

- 배열의 요소들 중에서 최대값을 구하는 경우, 아래 방식 사용
  - `Function.prototype.apply` 메서드
  - 스프레드 문법

```javascript
// 배열 요소 중에서 최대값 취득
Math.max.apply(null, [1, 2, 3]); // -> 3

// ES6 스프레드 문법
Math.max(...[1, 2, 3]); // -> 3
```

### 9) Math.min

- 전달받은 인수 중에서 가장 작은 수를 반환
- 인수 없으면 `Infinity` 리턴

```javascript
Math.min(1); // -> 1
Math.min(1, 2); // -> 1
Math.min(1, 2, 3); // -> 1
Math.min(); // -> Infinity
```

- 배열의 요소들 중에서 최소값을 구하는 경우, 아래 방식 사용
  - `Function.prototype.apply` 메서드
  - 스프레드 문법

```javascript
// 배열 요소 중에서 최소값 취득
Math.min.apply(null, [1, 2, 3]); // -> 1

// ES6 스프레드 문법
Math.min(...[1, 2, 3]); // -> 1
```
