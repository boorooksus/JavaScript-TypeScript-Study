Modern JavaScript Deep Dive 스터디 - CH31 RegExp

참고 자료: ⟪모던 자바스크립트 Deep Dive⟫(이웅모 지음,위키북스, 2020)

---

<br />

# 1. 정규 표현식이란?

- 특정 패턴과 일치하는 문자열을 검색, 추출, 치환할 수 있는 기능
- 반복문과 조건문 없이 패턴을 정의하고 테스트 가능
- 주석이나 공백을 허용하지 않고, 여러가지 기호를 사용 -> 가독성이 좋지 않다는 단점 존재

---

# 2. 정규 표현식의 생성

- 2가지 방법 존재
- `정규 표현식 리터럴`
- `RegExp 생성자 함수`
- 일반적으로 `정규 표현식 리터럴`을 사용
  - `정규 표현식 리터럴`은 `패턴`과 `플래그`로 구성
    ![regExp2](https://user-images.githubusercontent.com/55964775/183555594-a46cde3d-e937-47fd-998c-11359b8dd4af.png)

---

# 3. RegExp 메서드

### 1) RegExp.prototype.exec

- 정규 표현식 패턴 매칭 결과를 배열로 리턴
- 매칭 결과가 없는 경우 null 리턴
- `g 플래그`를 지정해도 **첫 번째 매칭 결과만 리턴**하므로 주의

```javascript
const target = "Is this all there is?";
const regExp = /is/;

regExp.exec(target); // -> ["is", index: 5, input: "Is this all there is?", groups: undefined]
```

### 2) RegExp.prototype.test

- 정규 표현식 패턴을 검색하여 매칭된 결과를 불리언 값으로 리턴

```javascript
const target = "Is this all there is?";
const regExp = /is/;

regExp.test(target); // -> true
```

### 3) String.prototype.match

- `String` 표준 빌트인 객체에서 제공하는 메서드
- 정규 표현식 패턴의 매칭 결과를 배열로 반환
- `exec` 메서드와 달리, `g 플래그`를 지정하면 모든 매칭 결과가 배열로 반환

```javascript
const target = "Is this all there is?";
const regExp = /is/;

target.match(regExp); // -> ["is", index: 5, input: "Is this all there is?", groups: undefined]

const target2 = "Is this all there is?";
const regExp2 = /is/g;

target.match(regExp2); // -> ["is", "is"]
```

---

# 4. 플래그

- 정규 표현식 검색 방식을 설정하기 위해 사용
- 총 6개가 존재 (맨 위 3개가 많이 쓰임)

  | 플래그 | 의미        | 설명                                                       |
  | ------ | ----------- | ---------------------------------------------------------- |
  | `i`    | Ignore case | 대소문자를 구분하지 않고 패턴을 검사                       |
  | `g`    | Global      | 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색 |
  | `m`    | Multi line  | 문자열이 행이 바뀌더라도 패턴 검색을 계속한다.             |
  | `d`    | Has line    | 부분 문자열 일치에 대해 인덱스 생성                        |
  | `s`    | Dot all     | 개행 문자가 `.`과 일치함                                   |
  | `u`    | Unicode     | 패턴을 유니코드 코드 포인트의 시퀀스로 간주함              |
  | `y`    | Sticky      | 대상 문자열의 현재 위치에서 탐색을 시작함                  |

---

# 5. 패턴

### 1) 문자열 검색

- 패턴으로 지정한 문자 또는 문자열을 검색

```javascript
const target = "Is this all there is?";

// 'is' 문자열과 매치하는 패턴. 플래그가 생략되었으므로 대소문자를 구별한다.
let regExp = /is/;
regExp.test(target); // -> true
target.match(regExp);
// -> ["is", index: 5, input: "Is this all there is?", groups: undefined]

// 플래그 i를 추가하면 대소문자를 구별하지 않는다.
regExp = /is/i;
target.match(regExp);
// -> ["Is", index: 0, input: "Is this all there is?", groups: undefined]

// 플래그 g를 추가하면 대상 문자열 내에서 패턴과 일치하는 모든 문자열을 전역 검색한다.
regExp = /is/gi;
target.match(regExp); // -> ["Is", "is", "is"]
```

### 2) 임의의 문자열 검색

- `마침표(.)`는 임의의 문자 한 개를 의미
- 문자의 내용은 무엇이 오든 상관없음 (공백 포함)

```javascript
const target = "Is this all there is?";

// 임의의 3자리 문자열을 대소문자를 구별하여 전역 검색
const regExp = /.../g;

target.match(regExp); // -> ["Is ", "thi", "s a", "ll ", "the", "re ", "is?"]
```

### 3) 반복 검색

- `{m.n}`: 앞선 패턴이 최소 m번, 최대 n번 반복되는 문자열을 의미
- `{n}`: 앞선 패턴이 n번 반복되는 문자열을 의미
- `{n,}`: 앞선 패턴이 최소 n번 반복되는 문자열을 의미
  - 콤마(,)뒤에 공백이 있으면 정상 작동하지 않으니 주의
- `+`: 앞선 패턴이 최소 한번 이상 반복되는 문자열을 의미 ( === `{1,n}`)
- `?`: 앞선 패턴이 최대 한 번(0번 포함) 이상 반복되는 문자열을 의미 ( === `{0,1}` )

```javascript
let target = "A AA B BB Aa Bb AAA";

// 'A'가 최소 1번, 최대 2번 반복되는 문자열을 전역 검색한다.
let regExp = /A{1,2}/g;
target.match(regExp); // -> ["A", "AA", "A", "AA", "A"]

// 'A'가 최소 2번 이상 반복되는 문자열을 전역 검색한다.
regExp = /A{2,}/g;
target.match(regExp); // -> ["AA", "AAA"]

// 'A'가 최소 한 번 이상 반복되는 문자열('A, 'AA', 'AAA', ...)을 전역 검색한다.
regExp = /A+/g;
target.match(regExp); // -> ["A", "AA", "A", "AAA"]

target = "color colour";
// 'colo' 다음 'u'가 최대 한 번(0번 포함) 이상 반복되고 'r'이 이어지는 문자열 'color', 'colour'를 전역 검색한다.
regExp = /colou?r/g;
target.match(regExp); // -> ["color", "colour"]
```

### 4) OR 검색

- `|`: or 의미
  - `+`와 함께 사용하면 분해되지 않은 단어 레벨로 검색

```javascript
const target = "A AA B BB Aa Bb";

// 'A' 또는 'B'를 전역 검색한다.
let regExp = /A|B/g;
target.match(regExp); // -> ["A", "A", "A", "B", "B", "B", "A", "B"]

// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ...
regExp = /A+|B+/g;
target.match(regExp); // -> ["A", "AA", "B", "BB", "A", "B"]
```

- `[ ]` 내의 문자는 or로 동작
  - 뒤에 `+`까지 붙이면 앞선 패턴을 한 번 이상 반복
  - 내부에 `-`를 사용하면 범위를 지정하는 의미
  - `\d`: 숫자를 의미
  - `\D`: 숫자를 제외한 모든 문자를 의미. `\d`와 정반대
  - `\w`: 알파벳, 숫자, 언더스코어를 의미
  - `\W`: 알파벳, 숫자, 언더스코어를 제외한 모든 문자를 의미. `\w`와 정반대

```javascript
let target = "A AA B BB Aa Bb";

// 'A' 또는 'B'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ...
let regExp = /[AB]+/g;
target.match(regExp); // -> ["A", "AA", "B", "BB", "A", "B"]

target = "A AA BB ZZ Aa Bb";
// 'A' ~ 'Z'가 한 번 이상 반복되는 문자열을 전역 검색한다.
// 'A', 'AA', 'AAA', ... 또는 'B', 'BB', 'BBB', ... ~ 또는 'Z', 'ZZ', 'ZZZ', ...
regExp = /[A-Z]+/g;
target.match(regExp); // -> ["A", "AA", "BB", "ZZ", "A", "B"]

target = "AA BB Aa Bb 12";
// 'A' ~ 'Z' 또는 'a' ~ 'z'가 한 번 이상 반복되는 문자열을 전역 검색한다.
regExp = /[A-Za-z]+/g;
target.match(regExp); // -> ["AA", "BB", "Aa", "Bb"]

target = "AA BB 12,345";
// '0' ~ '9' 또는 ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
regExp = /[0-9,]+/g;
target.match(regExp); // -> ["12,345"]

// 위와 같은 결과
regExp = /[\d,]+/g;
target.match(regExp); // -> ["12,345"]

target = "Aa Bb 12,345 _$%&";
// 알파벳, 숫자, 언더스코어, ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
regExp = /[\w,]+/g;
target.match(regExp); // -> ["Aa", "Bb", "12,345", "_"]
// 알파벳, 숫자, 언더스코어가 아닌 문자 또는 ','가 한 번 이상 반복되는 문자열을 전역 검색한다.
regExp = /[\W,]+/g;
target.match(regExp); // -> [" ", " ", ",", " $%&"]
```

### 5) NOT 검색

- `[ ]` 내의 `^`은 not을 의미

```javascript
const target = "AA BB 12 Aa Bb";

// 숫자를 제외한 문자열을 전역 검색한다.
const regExp = /[^0-9]+/g;
target.match(regExp); // -> ["AA BB ", " Aa Bb"]
```

### 6) 시작 위치로 검색

- `[ ]` 안에서 사용되지 않은 `^`는 문자열의 시작을 의미

```javascript
const target = "https://swmaestro.org";

// 'https'로 시작하는지 검사한다.
const regExp = /^https/;
regExp.test(target); // -> true
```

### 7) 마지막 위치 검색

- `$`는 문자열의 마지막을 의미

```javascript
const target = "https://swmaestro.org";

// 'org'로 끝나는지 검사
const regExp = /org$/;
regExp.test(target); // -> true
```

---

# 6. 자주 사용하는 정규표현식

### 1) 특정 단어로 시작하는지 검사

```javascript
const url = "https://example.com";

// 'http://' 또는 'https://'로 시작하는지 검사한다.
/^https?:\/\//.test(url); // -> true
/^(http|https):\/\//.test(url); // -> true
```

### 2) 특정 단어로 끝나는지 검사

```javascript
const fileName = "index.html";

// 'html'로 끝나는지 검사한다.
/html$/.test(fileName); // -> true
```

### 3) 숫자로만 이루워진 문자열인지 검사

```javascript
const target = "12345";

// 숫자로만 이루어진 문자열인지 검사한다.
/^\d+$/.test(target); // -> true
```

### 4) 하나 이상의 공백으로 시작하는지 검사

```javascript
const target = " Hi!";

// 하나 이상의 공백으로 시작하는지 검사한다.
/^[\s]+/.test(target); // -> true
```

### 5) 패스워드로 사용 가능한지 검사

```javascript
const id = "abc123";
// 알파벳 대소문자 또는 숫자로 시작하고 끝나며 4 ~ 10자리인지 검사한다.
/^[A-Za-z0-9]{4,10}$/.test(id); // -> true
```

### 6) 메일 주소 형식에 맞는지 검사

```javascript
const email = "ungmo2@gmail.com";

/^[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*@[0-9a-zA-Z]([-_\.]?[0-9a-zA-Z])*\.[a-zA-Z]{2,3}$/.test(email); // -> true
```

- 인터넷 메시지 형식(internet message format) 규약인 RFC 5322에 맞는 정교한 패턴 매칭이 필요할 경우, 더욱 복잡한 패턴 사용이 필요

```javascript
(?:[a-z0-9!#$%&'*+/=?^_`{|}~-]+(?:\.[a-z0-9!#$%&'*+/=?^_`{|}~-]+)*|"(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21\x23-\x5b\x5d-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])*")@(?:(?:[a-z0-9](?:[a-z0-9-]*[a-z0-9])?\.)+[a-z0-9](?:[a-z0-9-]*[a-z0-9])?|\[(?:(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)\.){3}(?:25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?|[a-z0-9-]*[a-z0-9]:(?:[\x01-\x08\x0b\x0c\x0e-\x1f\x21-\x5a\x53-\x7f]|\\[\x01-\x09\x0b\x0c\x0e-\x7f])+)\])
```

### 7) 핸드폰 번호 형식에 맞는지 검사

```javascript
const cellphone = "010-1234-5678";

/^\d{3}-\d{3,4}-\d{4}$/.test(cellphone); // -> true
```

### 8) 특수 문자 포함 여부 검사

```javascript
const target = "abc#123";

// A-Za-z0-9 이외의 문자가 있는지 검사한다.
/[^A-Za-z0-9]/gi.test(target); // -> true

/[\{\}\[\]\/?.,;:|\)*~`!^\-_+<>@\#$%&\\\=\(\'\"]/gi.test(target); // -> true
```
