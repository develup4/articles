# **Element**

```jsx
const element = <h1>Hello, world!</h1>;
```

- 브라우저 DOM 엘리먼트와 달리 React 엘리먼트는 `일반 객체(Plain Object)`이며 쉽게 생성할 수 있습니다.
- React 엘리먼트는 `불변객체(Immutable Object)`입니다. 엘리먼트를 생성한 이후에는 해당 엘리먼트의 자식이나 속성을 변경할 수 없습니다.

Element는 Component의 **구성요소**입니다. 겉모습만 보면 화살표 함수를 사용한 Component와 크게 차이 없지만, 개념을 확실하게 구분하도록 합니다. Component는 Props를 입력받아 State를 조작하고 저러한 Element를 뿌려주는 역할을 합니다.

# **Component**

- 개념적으로 컴포넌트는 **JavaScript 함수와 유사**합니다. `props`라고 하는 임의의 입력을 받은 후, 화면에 어떻게 표시되는지를 기술하는 React 엘리먼트를 반환합니다.
- 컴포넌트의 이름은 항상 대문자로 시작합니다. React는 소문자로 시작하는 컴포넌트를 DOM 태그로 처리합니다.
- props는 읽기 전용입니다. **모든 React 컴포넌트는 자신의 props를 다룰 때 반드시 순수 함수처럼 동작해야 합니다.**

```jsx
// 순수 함수
function sum(a, b) {
  return a + b;
}

// 안순수한(?) 함수
function withdraw(account, amount) {
  account.total -= amount;
}
```

- 클래스 컴포넌트는 항상 props로 기본 constructor를 호출해야 합니다.
- **데이터는 아래로 흐릅니다 .** 컴포넌트는 자신의 state를 자식 컴포넌트에 props로 전달할 수 있습니다.
- 가끔 다른 컴포넌트에 의해 렌더링될 때 컴포넌트 자체를 숨기고 싶을 때가 있을 수 있습니다. 이때는 렌더링 결과를 출력하는 대신 **null을 반환**하면 해결할 수 있습니다.
- 어떤 것이 Component가 되어야 할까요? 한가지 테크닉은 **단일 책임 원칙**이다. 하나의 컴포넌트는 한가지 일을 하는 것이 이상적이라는 원칙입니다.
- 모든 컴포넌트가 자신의 이벤트 핸들러에서 `setState()`를 호출할 때까지 **React는 리렌더링을 하지 않고 내부적으로 기다리고** 있습니다. 이를 통해 불필요한 렌더링을 방지하면서 성능을 향상시킵니다.
- 즉각적인 업데이트를 위한 트릭으로는 객체 대신 함수를 전달하는 방법이 있습니다. `상태 업데이터` 패턴입니다.

```jsx
this.setState((state) => {
  return { count: state.count + 1 };
});
```

# **Event**

- React의 이벤트는 소문자 대신 `캐멀케이스(camelCase)`를 사용합니다.
- JSX를 사용하여 문자열이 아닌 함수로 이벤트 핸들러를 전달합니다.
- React에서는 false를 반환해도 기본 동작을 방지할 수 없습니다. **반드시 preventDefault를 명시적으로 호출**해야 합니다.

```jsx
function ActionLink() {
	function handleClick(e) {
		e.preventDefault();
		console.log('The link was clicked.');
	}
	return (
		<a href=“sharp” onClick={handleClick}>
			Click me
		</a>
	);
}
```

- 여기서 `e`는 **합성 이벤트**입니다. React는 W3C 명세에 따라 합성 이벤트를 정의하기 때문에 **브라우저 호환성에 대해 걱정할 필요가 없습니다.**

# **Context**

- `Context`를 이용하면 단계마다 일일이 props를 넘겨주지 않고도 컴포넌트 트리 전체에 데이터를 제공할 수 있습니다.
- Context의 주된 용도는 다양한 레벨에 네스팅된 많은 컴포넌트에게 데이터를 전달하는 것입니다. C**ontext를 사용하면 컴포넌트를 재사용하기가 어려워**지므로 꼭 필요할 때만 쓰세요.
- 흔히 예시로 드는 선호 로케일, 테마, 데이터 캐시 등을 관리하는 데 있어서는 일반적으로 context를 사용하는 게 가장 편리합니다.

```jsx
const MyContext = React.createContext(defaultValue);
<MyContext.Provider value={/* 어떤 값 */}>
```

# **JSX**

- React는 별도의 파일에 마크업과 로직을 넣어 기술을 인위적으로 분리하는 대신, 둘 다 포함하는 `컴포넌트`라고 부르는 느슨하게 연결된 유닛으로 관심사를 분리합니다.

    <aside>
    💡 HTML, CSS, JavaScript로 나뉜 영역을 합치고 Component라는 단위로 바라보겠다는 것은 객체지향의 캡슐화 입장에서 아주 응원하고 싶은 방향이다. 이런 관점에서 Tailwind처럼 CSS도 같이 들어온다면 좋겠지만, 디자이너의 일하는 영역을 고려할 필요가 있다.
    
    </aside>


- `JSX`도 **표현식**입니다 컴파일이 끝나면, JSX 표현식이 정규 JavaScript 함수 호출이 되고 JavaScript 객체로 인식됩니다.
- JSX는 HTML보다는 JavaScript에 가깝기 때문에, React DOM은 HTML 어트리뷰트 이름 대신 camelCase 프로퍼티 명명 규칙을 사용합니다. 예를 들어, JSX에서 class는 **className**가 되고 tabindex는 **tabIndex**가 됩니다.
- 모든 항목은 렌더링 되기 전에 문자열로 변환됩니다. 이런 특성으로 인해 `XSS(Cross-Site-Scripting)` 공격을 방지할 수 있습니다.
- 근본적으로, JSX는 React.createElement(component, props, ...children) 함수에 대한 `문법적 설탕(Syntax Sugar`)을 제공할 뿐입니다.

```jsx
<MyButton color="blue" shadowSize={2}>
  {" "}
  Click Me{" "}
</MyButton>;
// ===
React.createElement(MyButton, { color: "blue", shadowSize: 2 }, "Click Me");
```

- 점 표기법을 사용할 수 있습니다.

```jsx
import React from "react";

const MyComponents = {
  DatePicker: function DatePicker(props) {
    return <div>Imagine a {props.color} datepicker here.</div>;
  },
};

function BlueDatePicker() {
  return <MyComponents.DatePicker color="blue" />;
}
```

- JSX 내에서 `Expression`을 사용할 수 있습니다. 하지만 **Statement는 불가능**합니다.

```jsx
<MyComponent foo={1 + 2 + 3 + 4} />
```

- 문자열 리터럴은 prop으로 넘겨줄 수 있습니다. 아래의 두 JSX 표현은 동일한 표현입니다.

```jsx
<MyComponent message="hello world" />
<MyComponent message={'hello world'} />
```

- Prop에 어떤 값도 넘기지 않을 경우, 기본값은 true입니다. 아래의 두 JSX 표현은 동일한 표현입니다.

```jsx
<MyTextBox autocomplete />
<MyTextBox autocomplete={true} />
```

- 여는 태그와 닫는 태그가 있는 JSX 표현에서 두 태그 사이의 내용은 **props.children이라는 특수한 prop**으로 넘겨집니다.

# Virtual DOM

- `Virtual DOM`은 UI의 이상적인 또는 **가상적인 표현을 메모리에 저장**하고 ReactDOM과 같은 라이브러리에 의해 **실제 DOM과 동기화**하는 프로그래밍 개념입니다. 이 과정을 `재조정`이라고 합니다. 이 접근방식이 React의 `선언적 API`를 가능하게 합니다. React에게 원하는 UI의 상태를 알려주면 DOM이 그 상태와 일치하도록 합니다. 이러한 방식은 앱 구축에 사용해야 하는 어트리뷰트 조작, 이벤트 처리, 수동 DOM 업데이트를 추상화합니다.
- **비교 알고리즘(Diffing Algorithm)**을 가지고 있습니다.
- **재조정 (Reconciliation)** 문서를 참고하길 권합니다. 자세히 알아볼 필요가 있습니다.

# **기타**

- `map()` 함수가 너무 중첩된다면 컴포넌트로 추출 하는 것이 좋습니다. Map에서 `Key props`를 요구하는 이유는 **Re-rendering 판단을 위해 변화를 감지**하기 위해서이다.
- React 애플리케이션 안에서 변경이 일어나는 데이터에 대해서는 `진리의 원천(Source of truth)`을 하나만 두어야 합니다. 다른 컴포넌트 간에 존재하는 state를 동기화시키려고 노력하는 대신 **하향식 데이터 흐름**에 기대는 걸 추천합니다.
- 어떤 컴포넌트들은 어떤 자식 엘리먼트가 들어올 지 미리 예상할 수 없는 경우가 있습니다. 범용적인 박스 역할을 하는 Sidebar 혹은 Dialog와 같은 컴포넌트에서 특히 자주 볼 수 있습니다. 이러한 컴포넌트에서는 특수한 Children props을 사용하여 자식 엘리먼트를 출력에 그대로 전달하는 것이 좋습니다.

```jsx
function FancyBorder(props) {
	return (
		<div className={‘FancyBorder FancyBorder-‘ + props.color}>
			{props.children}
		</div>
	);
}
```

- 파일구조와 관련하여 완전히 난관에 봉착해있다면, 모든 파일을 하나의 폴더에 보관하는 방법으로 우선 시작해보세요. 결국에는 프로젝트가 충분히 커져서 일부 파일을 나머지로부터 분리해 보관하기 원하게 될 것입니다. 그 시점까지 어떤 파일들을 가장 자주 묶어서 수정하는지 충분히 알 수 있게 될 것입니다. 일반적으로, 자주 함께 변경되는 파일들을 같이 보관하는 것이 좋은 방법입니다. 이러한 원칙을 `코로케이션(colocation)`이라고 부릅니다.
- **render 함수 내에서 화살표 함수를 사용하면 컴포넌트가 렌더링할 때마다 새로운 함수를 만들기 때문에 엄격한 비교에 의해 최적화가 깨질 수 있습니다.**
- onClick 또는 onScroll과 같은 이벤트 핸들러를 사용하고 있을 때 콜백이 너무 빠르게 호출되지 않도록 콜백이 실행되는 속도를 제어할 수 있습니다. 다음의 함수들을 사용하면 됩니다.
  - `throttling`: 시간 기반 빈도에 따른 변경 샘플링. **마지막 함수가 호출된 후 일정시간이 지나기 전에 다시 호출되지 않도록 하는 것.**
  - `debouncing`: 비활성 주기 이후에 변경 적용. **연이어 호출되는 함수들 중 마지막 함수 혹은 처음만 호출하도록 하는 것.**
- **HOC(High order component)**
- Production 빌드를 통해 최종 산출물의 성능을 높힐 수 있습니다.
- `Portal`의 전형적인 유스케이스는 부모 컴포넌트에 overflow: hidden이나 z-index가 있는 경우이지만, 시각적으로 자식을 튀어나오도록 보여야 하는 경우도 있습니다. 예를 들어, 다이얼로그, 호버카드나 툴팁과 같은 것입니다.

```jsx
render() {
	// React는 새로운 div를 생성하지 *않고* domNode 안에 자식을 렌더링합니다.
	// domNode는 DOM 노드라면 어떠한 것이든 유효하고, 그것은 DOM 내부의 어디에 있든지 상관없습니다.
	return ReactDOM.createPortal(
		this.props.children,
		domNode
	);
}
```

- 반적인 React의 데이터 플로우에서 props는 부모 컴포넌트가 자식과 상호작용할 수 있는 유일한 수단입니다. 자식을 수정하려면 새로운 props를 전달하여 자식을 다시 렌더링해야 합니다. 그러나, 일반적인 데이터 플로우에서 벗어나 직접적으로 자식을 수정해야 하는 경우도 가끔씩 있습니다.
- **render prop은 무엇을 렌더링할지 컴포넌트에 알려주는 함수입니다.**
