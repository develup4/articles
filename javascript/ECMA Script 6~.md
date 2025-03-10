# 현재 시간을 밀리초 단위로 바꾸기

```jsx
+new Date(); // 1970년 이후로 지나간 밀리초
```

# 해당 값이 true인지 false인지 체크하기

```jsx
var a = [];
var b = NaN;
!!a; // true
!!b; // false
```

# WeakSet, WeakMap

`Weakly Held`한 특징을 가지고 있다. 오직 객체 만을 가지고 있을 수 있으며, Garbage Collector가 객체를 수거해가면 자료구조에서도 제거되는것이 특징이다.

# for…in

객체의 모든 열거 가능한 문자열 속성(상속된 요소를 포함하여)을 반복한다.

```jsx
const object = { a: 1, b: 2, c: 3 };

for (const property in object) {
  console.log(`${property} ${object[property]}`);
}
```

# for…of

다른 언어의 `foreach`와 유사한 기능을 하는 문법이다.

```jsx
for (const friend of friends) {
  if (friend === "Dal") break;
  console.log(friend);
}
```

# Generator

파이썬의 Generator와 비슷한 기능이다. 즉, `Coroutine`이다. 간단하게 말하자면 Pause가 가능한 함수이기 때문에 정말 편리한 기능인 **async, await**가 이것을 통해 구현되어 있다.

```jsx
function* listPeople() {
  yield "dal";
  yield "flynn";
}

const list = listPeople();
console.log(list()); // 그냥 호출시 suspended 객체 리턴
list.next();
list.next(); // yield로 반환되는 값과 마지막 yield인지 여부가 반환된다.
```

# Proxy

C#의 프로퍼티(Setter/Getter)와 같은 기능이다.

```jsx
const user = {
  name: "asdf",
  age: 3,
};

// 일반적으로 프록시 세계에서 아래와 같은 userFilter의 역할을 trap이라고 한다.
const userFilter = {
  get: (target, prop, receiver) => console.log("get!"),
  set: (target, prop, receiver) => console.log("set!"),
};

const filteredUser = new Proxy(user, userFilter);

// get! 출력 (target은 user 객체, prop은 "age", receiver는 Proxy 객체이다)
filteredUser.age;

// set! 출력
filteredUser.age = 5;
```

# Symbol

자주 사용할 일은 없지만, 정리하자면 Symbol은 `불변값`이다. 한 번 설정하면 그 값을 바꿀 수 없고 생성할 때마다 새로운 값이 생성된다. 자바스크립트 내부적인 구현에 자주 사용된다.

```jsx
Symbol("creator") === Symbol("creator"); // false
Symbol.for("creator") === Symbol.for("creator"); // true
```

# Template literal

백틱(`)을 통해 만드는 문자열을 `Template literal`이라고 한다.

# 제곱 연산자

이제 Math 객체없이도 손쉽게 제곱 표현이 가능하다.

```jsx
5 ** 3;
i **= 3;
```

# Trailing commas in function

함수의 인자나 매개변수 마지막에 콤마를 붙여도 되게 되었다. 정말 편한 기능이고 Code Formatter 측에서도 엄청 좋아했을 것 같다.

```jsx
function A(a, b, c) {}
```

# Rest

Rest API가 아니라 `나머지`라는 뜻이다. `…`이 파라메터에 들어가면 여러가지 인수가 배열 하나로 축소되며, 이를 통해 관심있는 겄만 사용하고 나머지는 나머지로 처리하기 좋다. 뒤에 나올 Spread와다는 달리 하나의 객체로 축소시키는 방법이지만 문법은 `…`으로 같다.

```jsx
const infiniteArgs = (…kimchi) => console.log(kimchi);
infiniteArgs({“1”, 2, true, "asdfasdf"}); // kimchi는 인자들이 들어있는 배열이 된다.

const bestFriendMaker = ({firstOne, …rest}) => {
	// use firstOne
}
bestFreindMaker(["a", "b", "C"]); // firstOne은 "a"가 된다.
```

# Destructuring

다양하게 활용이 가능하다. 아래 예제를 통해 다양한 활용 방법을 알아보자.

```jsx
const settings = {
	color: {
		theme: "dark"
	}
};

const { color: { theme, valid = true } = {} } = settings;
console.log(color);

// Array Destructuring
const days = ["Mon", "Tue, "Wed"];
cosnt [mon, tue, wed, sun = "Sun"] = days;
console.log(mon, sun);

// Renaming with destructuring
const settings = {
	color: {
		chosen_color: "dark"
	}
};

const {
	color: { chosen_color: chosenColor = "light" }
} = settings;

// chosen_color는 자바스크립트의 convention에 맞지 않는데 Destructuring을 통해 수정이 가능하다.
console.log(chosenColor);

// Parameter Destructuring
const saveSettings = ({follow, alert, color = "blue"}) => {};

saveSettings({
	follow: true,
	alert: true,
	mkt: false
});

// swapping by array destructuring
let mon = "sat";
let sat = "mon";

[sat, mon] = [mon, sat];

// skipping technicque
const numbers = ["1", "2", "3", "4"];
const [ , , rd, th] = numbers;
console.log(rd, th);
```

# Spread

구성요소를 풀어헤치는 기능을 `spread`라고 한다.

```jsx
// Merge array by spread
const friends = [1, 2, 3, 4];
const family = ["a", "b", "c"];
console.log([…friends, …family]);

// Merge object by spread
const sexy = {
	name: "nico",
	age: 24
}

const hello = {
	sexy: true,
}
console.log({…sexy, …hello});

// Add element
const friends = [1, 2, 3, 4];  // friends에는 변화가 없고 새로운 배열이 만들어진다.
const newFriends = […friends, 5];  // 이는 불변성을 활용할때 많이 도움이 된다.
```

# Destructuring, Spread, Rest를 사용하는 패턴

```jsx
const user = {
	name: "nico",
	age: 24,
	password: 12345
};

// 이러면 password 필드가 없는 객체인 cleanUser를 얻을수있다.
const killPassword = ({password, …rest}) => rest;
const cleanUser = killPassword(user);

const user = {
	name: "nico",
	age: 24,
	password: 12345
};

// 필드 추가하기
const setCountry = ({ country = “KR”, …rest }) => ({ country, …rest });
setCountry(user);

// 필드명 수정하기
const user = {
	NAME: "nico",
	age: 24,
	password: 12345
};

const rename = ({ NAME: name, …rest }) => ({ name, …rest });
rename(user);
```

# Value Shorthands

`{alert: alert}`과 같이 **이름이 같은 키와 값**은 `{alert}`와 같이 하나로 줄여서 쓸 수 있다.

# Static class field

```jsx
class Example {
  static name = "exam";
  year = 1994;
}
```

# class decorators

```jsx
function hello(msg) {
  return function (target) {
    target.hello = msg;
  };
}

@hello("안녕")
class Example {}

Example.hello; // 안녕
```

# Object와 Array간 변환

```jsx
// 오브젝트의 값들만 배열로 반환
Object.value(어떤 오브젝트)

// 배열의 배열 리턴(필드와 값이 다 있어서 foreach 같은 걸로 출력하기도 쉽고 활용 가능)
Object.entries(어떤 오브젝트)
>> [[“name”, “a”], [“age”,2]]

// 다시 오브젝트로 만들어준다
Object.fromEntities()
```

# Flatten API

이제 `Flatten`이라는 라이브러리없이도 `Array.flat()`를 통해 배열을 flatten시킬 수 있다. 인자로 배열의 depth를 입력받는다.

# padStart, padEnd

```jsx
String(minutes).padStart(2, 0);  // 원래 변수는 수정되지 않음을 주의한다.
>> 02
```

# trimStart, trimEnd

각각 앞, 뒤에 빈 공간을 제거하는 기능이다. 기존 `trim()`은 양쪽을 모두 제거했지만 필요에 따라 이 기능들은 앞, 뒤만 제거할 수 있다. pad 시리즈와 마찬가지로 결과만 바뀔 뿐 변수 자체는 변화가 없다.

# Nullish coalescing operator(??)

**?? 앞에 값이 null이거나 undefined**이면(`falsy`), 오른쪽 값을 그렇지 않으면 왼쪽 값을 반환하는 **논리연산자**

```jsx
borderColor = { borderColor ?? "black" }

null ?? "hello" // "hello"
undefined ?? "hello" // "hello"
"hi" ?? "hello" // "hi"

// 원래 || 를 통해 쓰기도 했었는데 앞에가 false면 뒤에가 그냥 무시되는 특성때문에 개선된거라 보면 되겠다.
var1 || "novar"
```

# Optional Chaining(?.)

`React`에서 무진장 유용한 기능이다. 백엔드로부터 데이터를 받기전에 undefined라서 발생할 수 있는 오류를 매우 간단한 코드로 피할 수 있게 해준다.

```jsx
if (lyyn.profile && lynn.profile.email && lynn.profile.email.provider)
if (lynn?.profile?.email?.provider)
```

# Promise.allSettled

`Promise.all()`은모두 성공해야 성공인데 `Promise.allSettled`는 일부만 성공해도 진행한다. 예외가 있건 없건 끝까지 진행해서 끝나기만하면 된다.

# Numeric separators

```jsx
1_000_000.000_001 === 1000000.000001;
```

# at

```jsx
// 첨자에서는 안되지만 at은 -인덱스가 가능하다.
console.log(arr.at(-2));
```

# Error cause

```jsx
try {
  // 부가적인 정보를 전달할 수 있게 되었다. 오브젝트든 뭐든 전달이 가능하다.
  throw new Error("DB fail", { cause: { error: "password", value: 1234 } });
} catch (e) {
  console.log(e.message, e.cause);
}
```

# 클래스 속성

더 이상 constructor 안에 쓰지 않아도 되게 되었다.

```jsx
class Example {
  name = "exam"; // 생성자없이 초기화되므로 생성자 필요없다. 그냥 Syntax Sugar이다.
  #age = 28; // #을 붙이면 private이 된다

  static description = "asfd"; // 스태틱 키워드도 추가되었다.
}
```

---

[1] 노마드코더, ES6의 정석
[2] 제로초 블로그, [https://www.zerocho.com](https://www.zerocho.com/)
