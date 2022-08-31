Modern JavaScript Deep Dive 스터디 - CH37 Set과 Map

참고 자료: ⟪모던 자바스크립트 Deep Dive⟫(이웅모 지음,위키북스, 2020)

---

<br></br>

# 1. Set

- 중복되지 않는 유일한 값들의 집합
- 요소의 순서에 의미가 없다.
- 인덱스로 접근이 불가능하다.
- 배열의 중복 요소 제거에 활용 가능하다

```javascript
const uniq = (array) => [...new Set(array)];
console.log(uniq([2, 1, 2, 3, 4, 3, 4])); // [2, 1, 3, 4]
```

### 1) Set 객체의 생성

```javascript
const set = new Set();
console.log(set); // Set(0) {}

const set1 = new Set([1, 2, 3, 3]);
console.log(set1); // Set(3) {1, 2, 3}

const set2 = new Set("hello");
console.log(set2); // Set(4) {"h", "e", "l", "o"}
```

### 2) 요소 개수 확인

```javascript
const { size } = new Set([1, 2, 3, 3]);
console.log(size); // 3
```

### 3) 요소 추가

- `Set.prototype.add`

```javascript
const set = new Set();
console.log(set); // Set(0) {}

set.add(1);
console.log(set); // Set(1) {1}

set.add(1).add(2).add(3);
console.log(set); // Set(2) {1, 2, 3}
```

> ### `NaN`과 `+0`/`-0`
>
> - 자바스크립트에서는 NaN을 다르다고 평가하지만, Set는 같다고 평가한다.
> - 0과 -0은 자바스크립트, Set 모두 같다고 평가한다.

```javascript
const set = new Set();

console.log(NaN === NaN); // false
console.log(0 === -0); // true

// NaN과 NaN을 같다고 평가하여 중복 추가를 허용하지 않는다.
set.add(NaN).add(NaN);
console.log(set); // Set(1) {NaN}

// +0과 -0을 같다고 평가하여 중복 추가를 허용하지 않는다.
set.add(0).add(-0);
console.log(set); // Set(2) {NaN, 0}
```

### 4) 요소 존재 여부 확인

- `Set.prototype.has`

```javascript
const set = new Set([1, 2, 3]);

console.log(set.has(2)); // true
console.log(set.has(4)); // false
```

### 5) 요소 삭제

- `Set.prototype.delete`

```javascript
const set = new Set([1, 2, 3]);

// 요소 2를 삭제한다.
set.delete(2);
console.log(set); // Set(2) {1, 3}

// 요소 1을 삭제한다.
set.delete(1);
console.log(set); // Set(1) {3}

// 존재하지 않는 요소 0을 삭제하면 에러없이 무시된다.
set.delete(0);
console.log(set); // Set(1) {3}
```

- `delete`는 연속 사용이 불가능하다.

```javascript
const set = new Set([1, 2, 3]);

// delete는 불리언 값을 반환한다.
set.delete(1).delete(2); // TypeError: set.delete(...).delete is not a function
```

### 6) 요소 일괄 삭제

- `Set.prototype.clear`

```javascript
const set = new Set([1, 2, 3]);

set.clear();
console.log(set); // Set(0) {}
```

### 7) 요소 순회

- `Set.prototype.forEach` 를 통해 순회 가능하다.
  - 첫 번째 인자와 두 번째 인자는 값, 세 번째 인자는 Set이다.
  - 배열의 forEach와 인자를 통일하기 위해 첫 번째와 두 번째 인자 값이 동일하다.

```javascript
const set = new Set([1, 2, 3]);

set.forEach((v, v2, set) => console.log(v, v2, set));
/*
1 1 Set(3) {1, 2, 3}
2 2 Set(3) {1, 2, 3}
3 3 Set(3) {1, 2, 3}
*/
```

- Set 객체는 Set.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.

```javascript
const set = new Set([1, 2, 3]);

// Set 객체는 Set.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in set); // true

// 이터러블인 Set 객체는 for...of 문으로 순회할 수 있다.
for (const value of set) {
  console.log(value); // 1 2 3
}

// 이터러블인 Set 객체는 스프레드 문법의 대상이 될 수 있다.
console.log([...set]); // [1, 2, 3]

// 이터러블인 Set 객체는 배열 디스트럭처링 할당의 대상이 될 수 있다.
const [a, ...rest] = [...set];
console.log(a, rest); // 1, [2, 3]
```

---

<br></br>

# 2. Map

- 키와 값의 쌍으로 이루어진 컬렉션이다.
- 객체와 다르게 키로 객체를 포함한 모든 값을 사용할 수 있으며, 이터러블이다.

```javascript
const map = new Map();

const lee = { name: "Lee" };
const kim = { name: "Kim" };

// 객체도 키로 사용할 수 있다.
map.set(lee, "developer").set(kim, "designer");

console.log(map);
// Map(2) { {name: "Lee"} => "developer", {name: "Kim"} => "designer" }
```

### 1) Map 객체의 생성

```javascript
const map = new Map();
console.log(map); // Map(0) {}

const map1 = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);
console.log(map1); // Map(2) {"key1" => "value1", "key2" => "value2"}

const map2 = new Map([1, 2]); // TypeError: Iterator value 1 is not an entry object
```

### 2) 요소 개수 확인

- `Map.prototype.size`

```javascript
const { size } = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);
console.log(size); // 2
```

- `size` 값을 임의로 변경하면, 무시된다.

```javascript
const map = new Map([
  ["key1", "value1"],
  ["key2", "value2"],
]);

console.log(Object.getOwnPropertyDescriptor(Map.prototype, "size"));
// {set: undefined, enumerable: false, configurable: true, get: ƒ}

map.size = 10; // 무시된다.
console.log(map.size); // 2
```

### 3) 요소 추가

- `Map.prototype.set`

```javascript
const map = new Map();
console.log(map); // Map(0) {}

map.set("key1", "value1");
console.log(map); // Map(1) {"key1" => "value1"}

const map1 = new Map();

map1.set("key1", "value1").set("key2", "value2");

console.log(map1); // Map(2) {"key1" => "value1", "key2" => "value2"}
```

> ### `NaN`과 `+0`/`-0`
>
> - 자바스크립트에서는 NaN을 다르다고 평가하지만, Map은 같다고 평가한다.
> - 0과 -0은 자바스크립트, Map 모두 같다고 평가한다.

```javascript
const map = new Map();

console.log(NaN === NaN); // false
console.log(0 === -0); // true

// NaN과 NaN을 같다고 평가하여 중복 추가를 허용하지 않는다.
map.set(NaN, "value1").set(NaN, "value2");
console.log(map); // Map(1) { NaN => 'value2' }

// +0과 -0을 같다고 평가하여 중복 추가를 허용하지 않는다.
map.set(0, "value1").set(-0, "value2");
console.log(map); // Map(2) { NaN => 'value2', 0 => 'value2' }
```

### 4) 요소 취득

- `Map.prototype.get`
- 존재하지 않는 키는 `undefined` 반환

```javascript
const map = new Map();

const lee = { name: "Lee" };
const kim = { name: "Kim" };

map.set(lee, "developer").set(kim, "designer");

console.log(map.get(lee)); // developer
console.log(map.get("key")); // undefined
```

### 5) 요소 존재 여부 확인

- `Map.prototype.has`

```javascript
const lee = { name: "Lee" };
const kim = { name: "Kim" };

const map = new Map([
  [lee, "developer"],
  [kim, "designer"],
]);

console.log(map.has(lee)); // true
console.log(map.has("key")); // false
```

### 6) 요소 삭제

```javascript
const lee = { name: "Lee" };
const kim = { name: "Kim" };

const map = new Map([
  [lee, "developer"],
  [kim, "designer"],
]);

map.delete(kim);
console.log(map); // Map(1) { {name: "Lee"} => "developer" }
```

- 존재하지 않는 키에 대해서는 무시된다.

```javascript
const map = new Map([["key1", "value1"]]);

// 존재하지 않는 키 'key2'로 요소를 삭제하려 하면 에러없이 무시된다.
map.delete("key2");
console.log(map); // Map(1) {"key1" => "value1"}
```

### 7) 요소 일괄 삭제

- `Map.prototype.clear`

```javascript
const lee = { name: "Lee" };
const kim = { name: "Kim" };

const map = new Map([
  [lee, "developer"],
  [kim, "designer"],
]);

map.clear();
console.log(map); // Map(0) {}
```

### 8) 요소 순회

- `Map.prototype.forEach` 를 이용하여 순회 가능

```javascript
const lee = { name: "Lee" };
const kim = { name: "Kim" };

const map = new Map([
  [lee, "developer"],
  [kim, "designer"],
]);

map.forEach((v, k, map) => console.log(v, k, map));
/*
developer {name: "Lee"} Map(2) {
  {name: "Lee"} => "developer",
  {name: "Kim"} => "designer"
}
designer {name: "Kim"} Map(2) {
  {name: "Lee"} => "developer",
  {name: "Kim"} => "designer"
}
*/
```

- Map은 이터러블 이므로 `for...of`로도 순회 가능하다.

```javascript
const lee = { name: "Lee" };
const kim = { name: "Kim" };

const map = new Map([
  [lee, "developer"],
  [kim, "designer"],
]);

// Map 객체는 Map.prototype의 Symbol.iterator 메서드를 상속받는 이터러블이다.
console.log(Symbol.iterator in map); // true

// 이터러블인 Map 객체는 for...of 문으로 순회할 수 있다.
for (const entry of map) {
  console.log(entry); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
}

// 이터러블인 Map 객체는 스프레드 문법의 대상이 될 수 있다.
console.log([...map]);
// [[{name: "Lee"}, "developer"], [{name: "Kim"}, "designer"]]

// 이터러블인 Map 객체는 배열 디스트럭처링 할당의 대상이 될 수 있다.
const [a, b] = map;
console.log(a, b); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
```

- `Map.prototype.keys`, `Map.prototype.values`, `Map.prototype.entries`를 이용하여 원하는 방식으로 순회할 수 있다.

```javascript
const lee = { name: "Lee" };
const kim = { name: "Kim" };

const map = new Map([
  [lee, "developer"],
  [kim, "designer"],
]);

// Map.prototype.keys는 Map 객체에서 요소키를 값으로 갖는 이터레이터를 반환한다.
for (const key of map.keys()) {
  console.log(key); // {name: "Lee"} {name: "Kim"}
}

// Map.prototype.values는 Map 객체에서 요소값을 값으로 갖는 이터레이터를 반환한다.
for (const value of map.values()) {
  console.log(value); // developer designer
}

// Map.prototype.entries는 Map 객체에서 요소키와 요소값을 값으로 갖는 이터레이터를 반환한다.
for (const entry of map.entries()) {
  console.log(entry); // [{name: "Lee"}, "developer"]  [{name: "Kim"}, "designer"]
}
```
