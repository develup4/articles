# OOP in JavaScript

### Object Prototypes

Javascript에서는 객체를 상속하기 위해 프로토타입이라는 방식을 사용한다. 이를 **프로토타입 기반 언어**라고 부른다. 이는 **모든 객체들이 메소드와 속성들을 상속받기 위한 템플릿으로서 프로토타입 객체(prototype object)를 가진다**는 의미이다. 또한 그 프로토타입 객체도 또 다시 상위 프로토타입 객체로부터 상속받을 수 있다. 이는 난해한 방식이지만 Javascript가 강력하고 유연한 언어로 작동할 수 있는 원동력이기도 하다.

### 프로토타입 객체 이해하기

```jsx
function Person(first, last, age, gender, interests) {
	this.first = first;
	this.last = last;
}

var person1 = new Person(‘Bob’, ‘Smith’, 32, ‘male’, [‘music’, ‘ski’]);
person1.valueOf();	// 여기
```

- 브라우저는 우선 `person1` 객체가 `valueOf()` 메소드를 가지고 있는지 체크한다.
- 없으므로 `person1`의 프로토타입 객체(**Person() 생성자의 프로토타입**)에 `valueOf()` 메소드가 있는지 체크한다.
- 여전히 없으므로 `Person() 생성자`의 프로토타입 객체의 프로토타입 객체(**Object() 생성자의 프로토타입**)가 `valueOf()` 메소드를 가지고 있는지 체크한다. 있으므로 호출하며 끝난다.
- 프로토타입 체인에서 한 객체의 메소드와 속성들이 다른 객체로 **복사되는 것이 아님**을 알 수 있다. 체인을 타고 올라가며 접근할 뿐이다.

### 상속받은 멤버들이 정의된 곳

- 속성과 메소드들은 각 객체가 아니라 객체의 생성자의 `prototype`이라는 속성에 정의되어 있다. 예를 들어 위 예제에서 `valueOf()` 메소드는 `Object.`에 존재하지 않고 `Object.prototype.`에 존재한다. `prototype` 속성도 하나의 객체이며 프로토타입 체인을 통해 상속하고자 하는 속성과 메소드를 담아두는 버킷으로 사용된다. 이러한 프로토타입 상속은 string 객체가 생성되는 즉시 split(), indexOf(), replace() 등의 유용한 메소드를 사용할 수 있는 이유이다.

### 프로토타입 상속

아래와 같이 세 가지 방법이 있다.

```jsx
// 1. create() 메소드는 주어진 객체를 프로토타입 객체로 삼아 새로운 객체를 생성한다
var person2 = Object.create(person1);

// 2. 생성자 속성 활용
var person3 = new person1.constructor(‘Karen’, ‘Stephen’, 26, ‘female’, []);

// 3. call() 활용
function Teacher(first, last, age, gender, interests, subject) {
	// call() 함수는 다른 곳에서 정의된 함수를 현재 컨텍스트에서 실행할 수 있도록 한다
	Person.call(this, first, last, age, gender, interests);
	this.subject = subject;
}
```

---

[1] MDN Web Docs, [http://developer.mozilla.org/ko/docs/Learn/](http://developer.mozilla.org/ko/docs/Learn/)
[2] 노마드코더, ES6의 정석
