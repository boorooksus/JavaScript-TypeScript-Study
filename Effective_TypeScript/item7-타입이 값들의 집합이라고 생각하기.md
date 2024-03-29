# Effective TypeScript 스터디 - Item7: 타입이 값들의 집합이라고 생각하기

참고 자료:
⟪Effective TypeScript⟫(Jesse Hallett, 인사이트, 2021)
⟪When to use `never` and `unknown` in TypeScript⟫(Jesse Hallett, https://blog.logrocket.com/when-to-use-never-and-unknown-in-typescript-5e4d6c5799ad/, 29 JAN 2019)
⟪타입스크립트 타입 레벨 프로그래밍⟫(gomjellie, https://velog.io/@gomjellie/You-dont-know-type, 02 MAY 2022)

---

## 1. 타입 시스템

타입 스크립트의 타입 시스템은 작은 순수 함수형 언어다

### 타입 시스템이 할 수 있는것들`

- 함수
- 분기 - Conditional Branching
- 변수 대입
- 루프 - Loops
- 동일성 체크

### 타입 시스템이 할 수 없는것들

- 변경 가능한 상태 - Mutable state
- 파일 입출력 - I/O
- 고계도 함수 - HOC
  > - 트릭을 쓰면 가능하다  
  >   참고: https://overcurried.com/%ED%83%80%EC%9E%85%20%EB%A0%88%EB%B2%A8%20%EA%B3%A0%EC%B0%A8%20%ED%95%A8%EC%88%98/

## 2. 타입은 집합

![diagram](https://cdn-images-1.medium.com/max/1600/1*_wh7P-jdH2o9xHgFi1bqbw.png)

- 타입레벨에서 우리가 항상 다루는것은 집합이다
- number, string, boolean, symbol, undefined, null은 무한 집합이라고 생각한다.
- `A | B`은 A와 B의 합집합을 나타낸다(`union 타입`)
  ```javascript
  type ABType = A | B;
  type ABLiteral = "A" | "B";
  ```
- `A & B`은 A와 B의 교집합을 나타낸다(`interaction`)
  ```javascript
  type AType = ABType & A;
  ```
- unknown은 모든집합의 상위집합(superset)이다 (`전체집합`)
  ```javascript
  let unkownValue: unknown;
  unknownValue = "hello";
  unknownValue = 29;
  unknownValue = { name: "james", age: "29" };
  // 모두 가능
  ```
- never은 모든집합의 부분집합(subset)이다 (`공집합`)

  - never가 공집합이라는 특성에 의해서 A | never는 A
  - never가 공집합이라는 특성에 의해서 A & never는 never

  ```javascript
  let neverValue: nver;

  neverValue = 3; // Type 'number' is not asssignable to tpye 'never'
  ```

- any는 모든 타입의 부분집합이자 상위집합이다
  - A | any는 any
  - A & any는 any
- `unit 타입`은 원소가 하나로만 이루어진 집합이다.

  ```javascript
  type A = "A";
  type Twelve = 12;
  ```
