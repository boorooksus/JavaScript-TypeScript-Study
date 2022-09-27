# Effective TypeScript 스터디 - Item8: 타입 공간과 값 공간의 심벌 구분하기

참고 자료:
⟪Effective TypeScript⟫(Jesse Hallett, 인사이트, 2021)
⟪빠르게 마스터하는 타입스크립트⟫(코딩애플 강의, coding apple, 2021)

---

## 값의 공간, 타입의 공간

- 타입스크립트에서는 값의 공간과 타입의 공간이 다르다. 타입 스크립트의 심벌(symbol)은 타입 공간이나 값 공간에 존재한다
- 똑같은 이름의 심벌이 각각의 공간에서 각자 존재할 수 있다. 하지만 이는 권장하지 않는다.

```javascript
interface Param {
  radius: number;
  height: numbe;r
}

const Param = (radis: number, height: number) => ({radius, height});

// param 함수의 시그니처 매개변수 타입은 인터페이스 Param과 같으나 서로 아무 연관이 없다.

console.log(instanceof Param)  //  함수 Param 타입 의미(interface 아님)
```

## typeof

- `값의 관점`에서는 런타임에 식별자의 타입을 가리키는 문자열을 반환하는 연산자이다.
- `타입의 관점`에서는 타입스크립트 타입으로 반환한다.

```javascript
// 인자의 타입이 number이면 배열에 삽입하는 함수
// typeof를 이용한 narrowing
function 함수(x: number | string) {
  // typeof 외에 in, instanceof 도 narrowing으로 사용 가능
  if (typeof x === "number") {
    // 연산
    console.log(x + 1);
    // number type 배열에 할당
    let array: number[] = [];
    array.push(x);
  } else {
    // narrowing 할 때 else 꼭 넣어줘야 에러 안생김
    console.log("x is string");
  }
}
```

- interface나 type의 경우에는 타입의 공간에서만 존재한다. 따라서, 자바스크립트로 트랜스파일을 하게 되면 사라지게 된다.
- 그러나 `Class`는 타입의 공간과 값의 공간에 동시에 존재한다.
  - 클래스는 내부적으로 함수다
  - 클래스 내부적으로 생성자 함수를 이용하여 만든 인스턴스 타입을 자바스크립트로 컴파일하면 object가 된다.
  - 타입의 공간에서 클래스의 타입은 typeof 클래스이며, 인스턴스의 타입은 클래스이다.
