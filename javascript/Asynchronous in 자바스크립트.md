# What is promise

Promise는 이전 작업이 완료될 때까지 다음 작업을 연기시키거나, 작업 실패를 대응할 수 있는 기능이다. Promise는 **비동기 작업 순서가 정확하게 작동**되게 도움을 준다. Promise는 **어떤 작업의 중간 상태를 나타내는 오브젝트**이다.

### Promise의 상태

- Promise가 생성되면 그 상태는 성공도 실패도 아닌 `pending` 상태라고 부른다.
- Promise 결과가 반환되면 결과에 상관없이 `resolved` 상태라고 부른다.
  - 성공적으로 처리된 Promise는 `fulfilled` 상태이다. 이 상태가 되면 Promise 체인의 다음 `.then()` 블럭에서 사용할 수 있는 Promise 값을 반환한다.
  - 실패한 Promise는 `rejected` 상태이다. 에러 메시지를 포함한 결과가 반환된다. Promise 체이닝의 제일 마지막 `.catch()`에서 상세한 에러 메시지를 확인할 수 있다.
  - Promise의 결과가 fulfilled인지 rejected인지 관계없이 최종 코드 블럭을 실행하려는 경우 `.finally()` 메서드를 사용할 수 있다.

### Promise API

### all()

여러 Promise가 순차적으로 실행되어야 할 경우에 then으로 계속 연결하는것보다 `Promise.all`을 사용해보자. all 안의 Promise들은 끝난 순서대로가 아니라 all에서 등록된 순서대로 결과가 출력된다. 또한 하나라도 Reject되면 전체가 Reject된다.

```jsx
const p1 = new Promise((resolve) => {
	setTimeout(resolve, 3000, “First”);
});

const p2 = new Promise((resolve, reject) => {
	setTimeout(reject, 2000, “second”);
});

const p3 = new Promise((resolve) => {
	setTimeout(resolve, 1000, “third”);
});

const motherPromise = Promise.all([p1, p2, p3]);
motherPromise
	.then((value) => console.log(value))
	.catch((err) => console.log(err));   // p1, p2, p3중하나라도 리젝되면 여기로
```

### race()

말 그대로 레이스이다. 배열 안 Promise 중 가장 빨리 끝나는 reject이나 resolve 하나만 결과를 출력한다. 아래 예제의 경우 p3가 1초로 가장 빠르므로 p3만 뜰 것이다.

```jsx
const motherPromise = Promise.race([p1, p2, p3]);

motherPromise
  .then((value) => console.log(value))
  .catch((err) => console.log(err)); // p1, p2, p3중하나라도 리젝되면 여기로
```

### Custom Promise

```jsx
let timeoutPromise = new Promise((resolve, reject) => {
	setTimeout(function() {
		resolve(“Success”);
	}, 2000);
});
```

### 참고자료

[https://programmingsummaries.tistory.com/32](https://programmingsummaries.tistory.com/325)5

### Worker

워커는 다른 스레드에서 어떤 작업을 실행할 수 있는 기능을 제공한다. 하지만 동기화 문제가 발생할 수 있으므로 DOM 요소에 액세스할 수 없다. 메인 스레드와 워커는 변수를 직접 공유하지 않고, 상대방이 message 이벤트로 수신하는 메시지로 소통한다.

```jsx
const worker = new Worker(‘./generate.js’);
document.querySelector(“#generate”).addEventListener(‘click’, () => {
	worker.postMessage({
		command: ‘generate’,
		quota: ‘quota’
	});
});
```

---

[1] MDN Web Docs, [http://developer.mozilla.org/ko/docs/Learn/](http://developer.mozilla.org/ko/docs/Learn/)
[2] 노마드코더, ES6의 정석
[3] 바보들을 위한 Promise 강의 - 도대체 Promise는 어떻게 쓰는거야?, [programmingsummaries.tistory.com/325/](http://programmingsummaries.tistory.com/325/)
