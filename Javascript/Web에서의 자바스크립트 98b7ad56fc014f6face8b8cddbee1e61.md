# Web에서의 자바스크립트

생성일: 2023년 3월 14일 오후 9:41
tags: Web, 자바스크립트

# 스크립트 로딩 전략

JavaScript는 `<script>` 요소에서 인라인 혹은 외부 스크립트를 가져와서 사용한다. 하지만 HTML은 순서 그대로 불러오기 때문에 JavaScript가 조작하려는 요소보다 먼저 불러와버리면 코드가 올바르게 동작하지 않는다. 고전적인 해결책은 스크립트 요소를 본문의 맨 마지막(`</body>` 태그 바로 앞)에 배치하는 것이지만 이 방법은 DOM을 모두 불러오기 전까지는 스크립트의 로딩과 실행이 완전히 중단된다는 단점이 있다. 이 단점은 성능 저하로 이어질 수 있다. 이를 해결하기 위한 최신 기법은 아래와 같다.

```jsx
<script src=“script.js” asnyc></script>
<script src=“script.js” defer></script>
```

`async` 특성은 스크립트를 가져오는 동안 페이지 로딩을 중단하지 않는다. 다만 스크립트 다운로드가 끝나면 바로 실행되며, 실행 도중에는 페이지 렌더링이 중단된다. **의존성없는 다수의 백그라운드 스크립트를 최대한 빨리 불러 와야할 때** 사용한다.  `defer` 특성은 반면 모든 페이지 컨텐츠를 불러오고 나서야 실행되므로 일반적으로 DOM 조작이 필요할 때 유용하다.

# Window 객체

브라우저의 요소들과 자바스크립트 엔진, 그리고 모든 변수를 담고 있는 객체이다. 브라우저를 구성하는 탭, 주소창, 북마크, 툴바 등 전체 브라우저를 담당하는 것이 `window 객체`, 웹사이트만을 담당하는 것이 `document 객체`이다. (node.js가 아닌 환경에서) Window 객체는 모든 객체의 조상으로 **전역 객체**라고 부르며, 따라서 코드에서 생략이 가능하다. 예를 들어 `window.parseInt()`가 아닌  그냥 `parseInt()`를 호출할 수 있는 이유가 된다.

# BOM(Browser object model)

document 객체의 모델인 DOM처럼 브라우저의 정보를 담는 객체 모델은 BOM 또한 존재한다. 그 중 자주 쓰이는 전역객체는 아래와 같다.

- navigator
- screen
- location
- history
- document

# 이벤트

이벤트란 프로그래밍하고 있는 시스템에서 일어나는 사건이나 발생이다. 시스템은 이벤트가 발생될 때 몇몇 종류의 신소를 생산 또는 발생시키고, 이벤트가 발생되었을 때 사건이 자동적으로 취해질 수 있는 메커니즘(이벤트 핸들러 혹은 이벤트 리스너)을 제공한다. 웹의 경우에, 이벤트는 브라우저 윈도우 내에서 발생되고, 그것이 거주하는 특정한 **요소에 부착**되는 경향이 있다. 웹 이벤트는 코어 JavaScript 언어의 일부가 아니라 **브라우저에 내장된 API의 일부로서 정의**된다. 반례로 Node.js와 같은 JavaScript 런타임에서의 이벤트 모델은 이벤트를 주기적으로 발산하는 `emitter`에 의존하고 있다.

```jsx
const button = document.querySelector(‘button’);
button.addEventListener(‘click’, () => { });
```

# 이벤트 객체

아래 코드에서 `e`를 이벤트 객체라고 한다. 이름은 어떤 것이든 괜찮지만 보통 `e`, `evt`, `event` 등이 쓰인다. **e.target**에서 target 프로퍼티는 **이벤트가 발생한 요소에 대한 참조**이다.

```jsx
function bgChange(e) {
	e.target.style.backgroundColor = ‘rgb(100, 100, 100)’;
	console.log(e);
}
button.addEventListener(‘click’, bgChange);
```

### 기본 행동 방지하기

이벤트에 대한 요소의 기본 행동을 방지해야하는 경우들이 있다. 예를 들어 form 내부에서 버튼을 눌렀을 때 submit 동작 등을 실행하지 않아야하는 경우도 있을 것이다. 이런 경우 이벤트 객체의 `preventDefault()`를 활용한다.

```jsx
function bgChange(e) {
	e.preventDefault();	// 기본 행동을 방지하는 코드
	e.target.style.backgroundColor = ‘rgb(100, 100, 100)’;
}
```

### 이벤트 버블링

- 부모 요소를 가지고 있는 요소에 이벤트가 발생하였을 때, 브라우저는 요소의 가장 바깥쪽의 조상(<html>)이 캡처링 단계에 대해 그것이 등록된 이벤트 핸들러가 있는지를 확인하기 위해 검사하고, 만약 그렇다면 실행한다.
- 그러고서 <html> 내부에 있는 다음 요소로 이동하고 같은 것을 하고, 그리고서 그 다음 요소로 이동하고, 실제로 선택된 요소에 닿을때까지 반복한다.

### 이벤트 캡처링

버블링과 정확히 반대의 일이 일어난다. 선택된 요소에서 시작하여 <html> 요소에 닿을 때까지 반복한다.

### stopPropagation()

표준 이벤트 객체는 `stopPropagation()` 함수를 가지고 있는데, 핸들러의 이벤트 객체가 호출되었을 때 첫 번째 핸들러가 실행되지만 이벤트가 더 이상 위로 전파되지 않도록 만들어주는 함수이다. 이벤트 버블링이나 캡처링으로 인한 부작용을 해결할 수 있다.

### 이벤트 위임(Event delegation)

이벤트 버블링과 캡처링이 부작용만 있는 것은 아니다. 다수의 자식요소에 대한 이벤트 등을 처리할 때 부모에게 이벤트 처리를 위임하는 등의 설정이 가능하다.

### Client-side storage

현대의 브라우저들은 사용자의 허락 하에 사용자 컴퓨터에 웹사이트 정보를 저장할 수 있는 다양한 방법을 제공한다. 그리고 필요할 때 그 정보들을 읽어온다. Client-side storage는 데이터를 저장하고 가져올 수 있는 JavaScript API로 구성되어 있다. 활용례는 다음과 같으며 그 이상으로 무궁무진하다.

- 웹 사이트에 대한 선호를 개인화하기
- 이전 활동 기록 저장하기
- 사이트 다운로드가 빨라지고 오프라인에서 사용할 수 있게 하기

### Cookie

구식의 저장 방식이다. 쿠키는 ID나 액세스 토큰 사용자와 개인화에 대한 정보를 주로 가진다.

### Local Storage API

HTML5에서 추가된 `Local Storage`의 한 가지 주요 기능은 페이지 로드 사이에 그리고 브라우저가 종료된 후에도 데이터가 유지된다는 것이다. Local Storage는 각 도메인에 따라 별도의 스토리지를 가지며 다른 도메인의 웹 사이트에서 참조할 수 없다. 이는 보안 문제를 방지하기 위함이다.

```jsx
localStorage.setItem(‘name’, ‘Chris’);
let name = localStorage.getItem(‘name’);
localStorage.removeItem(‘name’);
```

### Session Storage API

반면 `Session Storage`의 데이터는 브라우저가 종료되면 제거된다. 일회성의 정보를 저장하는데 쓰인다. 하지만 비밀번호와 같은 중요한 정보는 저장하지 않도록 주의해야 한다.

```jsx
window.sessionStorage.setItem(‘name’, ‘Chris’);
let name = window.sessionStorage.getItem(‘name’);
window.sessionStorage.removeItem(‘name’);
```

### IndexedDB API

브라우저에서 사용할 수 있는 완전한 데이터베이스 시스템이며, 그 유형은 숫자나 단순한 값으로 제한되지 않는다. 비디오, 이미지 등 거의 모든 것을 저장할 수 있다. 하지만 위 Web Storage에 비해 더 복잡한 경향이 있다. 또한 API의 동작은 **비동기식**으로 동작 함을 주의한다.

```jsx
let request = window.indexedDB.open(‘notes_db’, 1);
request.onerror = () => {
	console.log(“Database failed to open”);
}
request.onsuccess = () => {
	db = request.result;
	let objectStore = db.creatgeObjectStore(‘notes_os’, {key: 1, autoIncrement: true});
	objectStore.createIndex(‘title’, ‘title’, {unique: false});
};
```

### Cache API

최신 브라우저에서는 새로운 Cache API를 제공한다. 이 API는 특정 요청에 대한 HTTP 응답을 저장한다.

---

[1] MDN Web Docs, [http://developer.mozilla.org/ko/docs/Learn/](http://developer.mozilla.org/ko/docs/Learn/)