# CSS 첫 번째 단계

`CSS(Cascading Style Sheets)`c는 컨텐츠의 글꼴, 색상, 크기 및 간격을 변경하거나 여러 열로 분할하거나 애니메이션 및 기타 장식 기능을 추가하는 등의 웹 페이지 스타일 및 레이아웃에 사용된다.

> Cascading Style Sheet: Cascading은 위에서 아래로 흐른다는 뜻이다. 즉, 위에서 아래로 브라우저가 읽어서 적용한다는 뜻이며 같은 속성이 여러개 있어도 제일 마지막에 정의된 녀석이 적용된다.

## CSS 구문

CSS는 `규칙 기반 언어`이다. 웹 페이지의 **특정 요소 또는 요소 그룹**에 적용할 스타일 그룹을 지정하는 규칙을 정의한다. 예를 들어 “내 페이지의 제목이 빨간색이며 큰 텍스트로 표시되기를 원합니다”의 경우,

```css
h1 {
  color: red;
  font-size: 5em;
}
```

규칙은 `selector`와 함께 열린다. 이는 스타일을 지정할 HTML 요소를 **선택**한다. 위의 경우 `<h1>`을 스타일링한다. 그 다음 `중괄호`는 **속성과 값의 형태**를 취하는 하나 이상의 선언이 있다. 값과 쌍을 이루는 속성을 `CSS 선언(declaration)`이라고 한다. CSS 선언은 CSS 선언 블록 안에 있다.

## CSS Specifications

모든 웹 표준 기술(HTML, CSS, JavaScript 등)은 표준 조직 `W3C`, `WHATWG`, `ECMA`, `Khronos`와 같은 거대한 문서(Specification)으로 정의된다. 이중 CSS는 W3C 안의 `CSS Working Group`이라는 곳에서 개발되었다. 이 그룹은 브라우저 공급 업체 및 CSS에 관심있는 다른 회사의 대표로 구성되어 있다. 독립적인 목소리로 행동하는 초청된 전문가도 존재한다. CSS의 중요한 점은 **오래된 웹 사이트를 망가뜨릴 수 있는 방식으로 변경하지 않도록 매우 열심히 노력**한다는 점이다. 2000년에 구축된 웹 사이트는 여전히 웹 브라우저에서 사용할 수 있다.

## 문서에 CSS 추가하기

`styles.css` 파일을 HTML 문서의 `<head>` 안에 다음 행을 추가하면 된다.

```html
<link rel="“stylesheet”" href="“styles.css”" />html
```

### 내부 스타일 시트

재활용성이 떨어지는 문제가 있다.

```html
<html>
  <head>
    <style>
      h1 {
        color: red;
      }
    </style>
  </head>
</html>
```

### 인라인 스타일

매우 권장되지 않는 방식이다. 인라인으로 사용하고 싶다면 나중에 **tailwind css**와 같은 라이브러리를 사용하자.

```html
<h1 style="“color:" blue;”>title</h1>
html
```

### class 추가하기

HTML 요소 이름을 기반으로 스타일링하는 경우, 문서에서 해당 유형의 모든 요소가 동일하게 적용된다. 다른 요소를 변경하지 않고 요소의 하위 부분을 선택할 수 있는 방법으로는 class를 추가하고 해당 class에만 스타일을 적용하는 방법이 있다.

```css
<p class=“special” > 클래스 스타일링</p > .special {
  color: orange;
}
```

## 문서에서의 위치에 따라 스타일 지정하기

### 중첩(descendant combinator)

문서의 위치에 따라 무언가 다르게 보일 때가 있다. 예를 들어 `<li>`요소에 **중첩**된 `<em>`만 스타일을 적용하려면 **descendant combinator**라는 선택자를 사용할 수 있다.

```css
li em {
  color: purple;
}
```

### 동일한 계층(adjacent sibling combinator)

HTML의 동일한 계층 구조 수준에서 제목 바로 다음에 오는 단락의 스타일을 지정할 수 있다.

```css
h1 + p {
	font-size: 200%;
}c
```

### 상태에 따른 스타일링

예를 들어 기본 스타일링인 링크의 밑줄을 제거하는 경우, 접근성을 떨어뜨릴 수 있음을 주의한다.

```css
a:link {
  color: pink;
}

a:visited {
  color: green;
}

a:hover {
  text-decoration: none;
}
```

### 선택자 결합

```css
body h1 + p .special {
  color: yellow;
}
```

위의 경우 `<body>` 안에 있는 `<h1>` 바로 뒤에 오는 `<p>` 안에 있는 special class를 가진 요소를 스타일링한다.

## How CSS is structured

### 함수(function)

CSS의 대부분의 값은 비교적 간단한 키워드 또는 숫자이지만, 함수의 형태를 취하는 몇 가지 값이 존재한다.

```css
.box {
  width: calc(90% - 30px);
  transform: rotate(0.8turn);
}
```

### 공백(whitespace)

브라우저는 CSS 내부의 많은 공백을 무시하는 경향이 있다.

### 주석(comments)

CSS의 주석은 `/*`로 시작하고 `*/`로 끝난다.

### 속기(shorthands)

`font`, `background`, `padding`, `border`, `margin` 등 일부 속성은 속기 속성이라고 불른다. 속성을 한 줄로 설정할 수 있어 코드를 깔끔하게 만들 수 있다.

```css
padding: 10px 15px 15px 5px; /* 시계방향(위, 오른쪽, 아래, 왼쪽) */
background: red url(bg.png) 10px 10px repeat-x fixed;
```

## CSS는 실제로 어떻게 작동합니까?

1. 브라우저는 (네트워크에서 HTML을 수신하여) HTML을 로드한다.
2. HTML을 DOM(Document Object Model)로 변환한다. DOM은 메모리 상의 문서를 나타낸다.
3. 브라우저는 포함된 리소스, 특히 CSS를 가져온다.
4. 브라우저는 가져온 CSS를 구문 분석하고 선택자 유형별로 다른 규칙을 `buckets`로 정렬한다. 이 단계를 **render tree**라고 한다.
5. render tree는 규칙이 적용된 후에 표시되어야 하는 구조로 배치된다.
6. 페이지의 시각적 표시가 화면에 표시된다. 이를 painting 단계라고 한다.

### CSSOM

CSS 객체 모델로서 DOM과 유사한 독립적인 데이터 구조이다. 브라우저는 CSS 규칙을 이해하고 사용할 수 있는 스타일 맵으로 변환하고 노드 트리를 구성한다.

### 브라우저에서 인식하지 못하는 CSS를 발견하면?

아무것도 하지 않으며 CSS의 다음 단계로 넘어간다.

# CSS 구성 블록

## 규칙 충돌

### 계단식(cascade)

동일한 우선 순위를 갖는 두 규칙이 적용될 때, CSS에서 마지막에 나오는 규칙이 사용된다.

### 우선순위

**더 구체적인 규칙이 우선 순위를 갖는다.** 아래의 경우 class 선택자가 더 높은 우선순위를 갖고 빨간 컬러를 갖게 된다.

```css
.main {
	color: red;
}

h1 {
	color: blue;
}

<h1 class=“main”>title</h1>
```

### 상속(inheritance)

부모 요소에서 설정된 일부 CSS 속성 값은 자식 요소에 의해 상속되며, 일부는 그렇지 않다. 예를 들어 `color`, `font-family` 등은 해당 요소 내부의 모든 요소에도 적용된다. `width`와 같은 일부 요소는 상속되지 않는다.

```css
/* body 내부에 모두 blue가 기본 컬러가 된다 */
body {
  color: blue;
}
```

상속을 제어하는 특수 속성값도 존재한다.

```css
a {
  color: initial; /* 브라우저의 기본 값을 사용한다. 설정된 값이 없다면 부모에게 상속받는다 */
}

.my {
  color: inherit; /* 부모의 속성을 따른다 */
}

p {
  color: unset; /* 상속되는 속성이라면 inherit 아니라면 initial을 따른다 */
}
```

## 계단식(cascade) 이해하기

1. Importance
2. 우선 순위
3. 소스 순서

### CSS 위치의 영향

1. 사용자 스타일 시트의 중요한 선언
2. 작성자 스타일 목록에서 중요한 선언
3. 작성자 스타일 시트의 일반적인 선언
4. 사용자 스타일 시트의 일반적인 선언
5. 브라우저의 기본 스타일

### Zero CSS를 적용하는 이유

브라우저의 기본 스타일은 디자인에 해로운 요소가 될 수 있다. 예를 들어 <body>의 상단에는 기본적인 틈이 존재한다. 마진을 0으로 줘서 해결할 수 있지만 이러한 유형의 요소는 많이 존재한다. Zero CSS 라이브러리를 찾아 적용하여 이를 해결하고 프로젝트를 시작하자.

## 선택자란 무엇인가?

규칙 내의 CSS 속성값을 적용하기 위해 어떤 HTML 요소를 선택해야 하는지 브라우저에 알려주는 요소 및 기타 용어의 패턴이다. 선택자에 의해 선택된 요소들은 **선택자의 Subject**로 지칭된다.

## 선택자의 유형

### Type, class 및 ID 선택자

```css
h1 {
} /* type */
.box {
} /* class */
#unique {
} /* id */
```

### 속성 선택자

요소의 특정 속성이 있는지에 따라 혹은 특정 값을 가진 속성의 존재 여부에 따라 스타일이 적용된다.

```css
a[title] {
}
a[href=“<https://example.com>”]
{
}
```

### 동위 선택자

```css
div ~ p {
}
```

### Pseudo-classes

특정 상태를 스타일링한다. `active`, `hover`, `checked`, `focus`, `enabled`, `disabled` 등이 있다.

```
a:hover { }
```

### Pseudo-elements

요소의 특정 부분(예를 들어 첫 번째 라인)에 적용

```css
Pseudo-elements p::first-line {
}
```

### 구조선택자

해당 수열에 해당하는 요소에 적용된다.

```css
li: nth-child(2n+1); /* li의 자식 중이 아니라 li 중에서 2n+1번째임을 주의한다 */
```

### 결합자(combinators)

```css
article > p {
} /* <article>의 자식인 <p>에 적용 */
```

### 여러 요소 선택하기

요소의 여러 타입을 선택하고 모두에게 하나의 rule set을 적용할 수도 있다. 여러 선택자는 콤마로 구분한다.

```css
p,
li,
h1 {
  color: red;
}
```

### 전체 적용

모든 요소에 적용된다.

```css
* {
}
```

## The box model

CSS에는 크게 두 가지 박스 유형이 있다. 이러한 특성은 박스가 페이지 대열 측면 및 다른 박스와 관련하여 박스의 작동방식을 나타낸다.

### 블록 박스

- 블록 박스는 인라인 방향으로 연장되어 상위 컨테이너에서 사용 가능한 공간을 채운다. 대부분의 경우 박사는 사용 가능한 공간을 100% 채우면서 상위 컨테이너의 너비만큼 된다는 의미이다.
- 블록 박스는 새 줄로 행갈이를 한다.
- width, height 속성이 적용된다.
- **블록박스의 패딩과 여백, 테두리로 인해 다른 요소들이 블록 박스로 부터 밀려난다.**

### 인라인 박스

- 인라인 박스는 새 줄로 행갈이를 하지 않는다.
- width, height 속성은 무시된다.
- **패딩과 여백, 테두리는 다른 인라인 박스들이 해당 박스로부터 멀어지게 하지 않는다.**
- 좌우로만 margin, padding을 가질 수 있다.

### inline-block

블록이면서 가로로 배열된다. 하지만 비추천으로 이런 용도라면 `flex`를 쓰자.

```css
display: inline-block;
```

## 표준 CSS box model

box에서 width와 height를 부여하면 `content box`의 너비와 높이가 정의된다. 그런 다음 **패딩과 테두리는 박스의 너비와 높이에 추가**되어 박스가 점유하는 전체 크기가 정해진다.

## 대체 CSS box model

박스의 실제 크기를 얻기 위해 테두리와 패딩을 추가하는 것이 불편하여 추가된 방식이 대체 박스 모델이다. 이 모델에서 너비는 페이지에서 표시되는 박스의 너비이므로 content box의 너비는 너비에서 패딩 및 테두리 너비를 뺀 값이다. 기본적으로 브라우저는 **기본 박스 모델**을 사용하므로 대체 박스 모델을 사용하려면 아래와 같이 사용한다. 참고로 인터넷 익스플로러는 기본적으로 대체 박스 모델을 사용하며 전환도 불가능하다.

```css
html {
  box-sizing: border-box;
}
```

## 여백 상쇄 정복(Collapsing margin)

여러 블록의 위쪽 및 아래쪽은 바깥 여백(마진)은 경우에 따라 제일 큰 여백의 크기를 가진 단일 여백으로 결합(상쇄)된다. 이런 동작을 여백 상쇄라고 부른다. 단, 플로팅 요소와 절대위치를 지정한 요소의 여백은 절대 상쇄되지 않는다.
[https://developer.mozilla.org/ko/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing](https://developer.mozilla.org/ko/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)

## 컨텐츠 overflow

`Overflow`는 박스에 내용이 너무 많을 때 발생한다. CSS는 overflow를 관리하기 위한 다양한 도구를 제공한다. CSS는 기본적으로 **데이터 손실**을 피하려고 한다. 가능하면 내용을 숨기지 않는다는 의미이다. 박스를 넘쳐 흐르는 한이 있더라도 컨텐츠를 모두 표시한다. 그리고 아래와 같이 overflow를 제어할 수 있다.

```css
overflow: hidden;
overflow: scroll;
overflow-y: scroll;
overflow: scroll hidden; /* x축은 scroll, y축은 hidden */
overflow: auto; /* 스크롤 막대가 필요하지 않을 경우 표시하지 않음 */
```

## CSS의 단위

[https://developer.mozilla.org/ko/docs/Learn/CSS/Building_blocks/Values_and_units](https://developer.mozilla.org/ko/docs/Learn/CSS/Building_blocks/Values_and_units)

`1.3em`: 요소의 글꼴 크기의 1.3배
`1vh`: viewport 높이의 1%

### Viewport 단위

Viewport는 사이트를 보는데 사용하는 브라우저에서의 페이지 가시 영역이다.

## CSS에서 항목 크기 조정

### 고유 크기(intrinsic size)

CSS의 영향을 받기 전의 자연스로운 크기를 말한다. 예를 들어 비어 있는 `<div>` 고유 크기가 없다. 하지만 내용물이 생기면 <div>의 크기는 내용의 크기로 확장되고 이것이 요소의 고유 크기가 된다.

## CSS 방법론

### OOCSS

Nicole Sullivan에 의한 접근 방식이다. OOCSS의 기본 아이디어는 CSS를 재사용 가능한 개체로 분리하여 사이트에서 필요한 모든 위치에서 사용할 수 있도록 하는 것이다.

```css
.comment {
  display: grid;
  grid-template-columns: 1fr 3fr;
}

.comment img {
  border: 1px solid grey;
}

.comment .content {
  font-size: 0.8rem;
}

.list-item {
  display: grid;
  grid-template-columns: 1fr 3fr;
  border-bottom: 1px solid grey;
}

.list-item .content {
  font-size: 0.8rem;
}
```

위 CSS에서는 `comment` 구성 요소 부분에 대한 많은 규칙으로 클래스를 만든 다음 `list-item`과 거의 동일한 규칙으로 호출된 `comment` 클래스를 생성한다. 약간의 차이를 제외하고, 이 두 구성요소의 차이점은 list-item에는 아래족 테두리가 있고, comment의 이미지에는 테두리가 있지만, list-item의 이미지에는 테두리가 없다는 것이다.

### BEM

BEM은 블록 요소 수정자를 나타낸다. BEM에서 블록은 **버튼, 메뉴 또는 로고와 같은 독립 실행형 엔티티**이다. 요소는 목록 항목이나 해당 블록에 연결된 제목과 같은 것이다. 수정자는 스타일이나 동작을 변경하는 블록 또는 요소의 플래그이다. 이 방식에서는 CSS 클래스의 이름에 **대시와 밑줄을 광범위하게 사용**하기 때문에 BEM을 사용하는 코드를 인식할 수 있다. BEM은 OOCSS에 비해 엄격한 명명 규칙을 사용하며 더 대중적으로 사용된다.

```html
<form class="form form--theme-xmas form--simple">
  <input class="form__input" type="text" />
  <input class="form__submit form__submit--disabled" type="submit" />
</form>
j
```

### CSS 방법론의 변천사

[https://tech.kakao.com/2022/05/20/on-demand-atomic-css-library/](https://tech.kakao.com/2022/05/20/on-demand-atomic-css-library/)

### Defining variables

현재의 CSS는 custom property를 가질 수 있다. 즉, 일종의 변수를 가질 수 있는 것이다. 이것에 preprocessor가 더해지면 SCSS와 같은 멋진 라이브러리가 탄생할 수 있다.

```css
$base-color: #c6538c;

.alert {
  border: 1px solid $base-color;
}
```

# Styling Text

## 웹 안전 글꼴

일반적으로 모든 시스템에서 사용할 수 있는 글꼴의 수는 한정되어 있다. 이런 글꼴을 `웹 안전 글꼴`이라고 한다.

## Font stacks

웹 페이지에서 글꼴의 사용가능 여부를 보장할 수 없으므로 브라우저에서 선택할 수 있는 글꼴 스택을 제공할 수 있다. 여기에는 여러 글꼴 이름으로 구성된 `font-family` 값이 포함된다.

```
p {
	font-family: “Trebuchet MS”, Verdana, sans-serif;
}

```

## 온라인 글꼴 서비스 사용

구글이나 어도비 등 여러 서비스가 온라인 글꼴을 제공하며 <link> 태그를 통해 import 할 수 있다.

# CSS Layout

## Normal Flow

`Normal Flow`는 페이지 레이아웃을 전혀 제어하지 않을 경우 브라우저가 기본값으로 HTML 페이지를 배치하는 방법을 말한다. CSS를 통해 Normal Flow를 벗어난 레이아웃을 구성할 수 있다.

### 블록 요소의 경우

부모 요소의 너미 100%와 자체 내용물의 최대 높이를 가지며, 가로 쓰기 모드에 수직 대열을 갖게 된다.

### 인라인 요소의 경우

다른 요소와 같은 라인데 차례로 자리 잡으며 내용물만큼의 공간을 갖는다. 충분한 공간이 없을 경우 overflow되어 새로운 줄에 나타난다.

## Flexbox

`Flexbox`는 행과 열 형태로 항목 무리를 배치하는 1차원 레이아웃 메서드이다. 항목은 부족한 공간에 맞추기 위해 축소되거나 여분의 공간을 채우기 위해 변형된다.

- Flexbox는 **Flex Container**로 지정한 부모를 통해 자식의 위치를 조절할 수 있다.(align-self 속성으로 자식 스스로 배치를 변경할 수 있기는 하다.)
- Flexbox는 같은 라인에 배치하는데만 신경쓰기 때문에 width 등을 설정하더라도 무시될 수 있다. (flex-wrap 속성을 통해 정책을 정할 수 있다.)
- 배치의 방향은 아래와 같이 두 방향인데 `flex-direction` 속성에 의해 서로 바뀔 수 있으므로 단순하게 가로, 세로라고 할 수는 없다.
  - `기본 축(main axis)`: flex item이 배치되고 있는 방향으로 진행하는 축이다. main axis 방향으로 아이템을 옮기기 위해서는 `justify-content`를 사용한다.
  - `교차 축(cross axis)`: flex item이 내부에 배치되는 방향에 직각을 이루는 축이다. cross axis 방향으로 아이템을 옮기기 위해서는 `align-items`를 사용한다.

### Ordering

아래와 같이 배치의 우선순위를 직접 설정할 수 있다. 하지만 HTML 순서 자체가 변하는 것은 아니라는 것을 주의한다.

```css
child:nth-child(2) {
  order: 1;
}
```

## Grid

그리드 레이아웃은 웹 페이지를 위한 2차원 레이아웃 시스템이다. 이 기능을 통해 컨텐츠를 행과 열에 배치할 수 있으며 복잡한 레이아웃을 직관적으로 구축할 수 있는 많은 기능을 가진다. 하나의 그리드는 `columns`, `rows`로 구성되며, 각 행과 열 사이의 공백을 `grutters`라고 부른다.

```css
.father {
  display: grid;
  grid-template-columns: 20px 55px 89px 100px; /* column이 몇 개인지 간격은 어떤지 설정 */
  grid-template-rows: 100px 50px 300px; /* 이번엔 row */
  column-gap: 10px; /* 열간 간격 */
  row-gap: 10px; /* 행간 간격 */
  gap: 10px; /* 위 두줄과 같다 */
}
```

### Grid Template Area

좀 더 Grid를 편하게 구성하기 위한 속성이다.

```css
.grid {
  display: grid;
  grid-template-columns: auto 200px; /* 가능한만큼 크게 200px까지 */
  grid-template-rows: 100px repeat(2, 200px) 100px; /* == 100px 200px 200px 100px */
  grid-template-areas:
    “header header header header”
    “content content content nav”
    “content content content nav”
    “footer footer footer footer”;
}

.grid {
  display: grid;
  gap: 10px;
  grid-template-columns: repeat(4, 100px);
  grid-template-rows: repeat(4, 100px);
}

.grid {
  display: grid;
  gap: 5px;
  height: 50vh;
  grid-template:
    “header header header header” 1fr /* 높이를 지정할 수 있다 */
    “content content content nav” 2fr
    “footer footer footer footer” 1fr / 1fr 1fr 1fr 1fr; /* 슬러쉬 뒤에는 가로의 길이를 지정해주면 된다(repeat는 불가능)*/
}
```

### 단위

Grid는 `fr`(fraction) 단위를 사용하는 것이 좋다. 이는 px과 같은 단위인데 사용 가능한 공간을 의미한다. 반응형 디자인에서 유연하게 배치하는데 유용하기 때문이다.

```css
grid-template-rows: 4fr 1fr 1fr; /* 2/3 1/6 1/6 */
```

### auto-fill, auto-fit

반응형 웹을 만드는데 중요한 auto-fill과 auto-fit이다.

```css
.grid:first-child {
  grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
}

.grid:last-child {
  grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
}cs
```

## Floats

원래의 목적은 텍스트 열 내부에 float하는 이미지를 포함하고, 이미지의 주변을 텍스트가 둘러쌀 수 있도록 배치하기 위해 등장한 속성이었다. 현재에는 무엇이든 float하기 위해 사용한다.

```css
.box {
  float: left;
}
```

## Positioning

- `position: absolute; (절대 포지셔닝)`: **페이지의 좌측 최상단을 기준**으로 배치한다.
- `position: relative; (상대 포지셔닝)`: **Normal Flow에서 위치할 곳을 기준**으로 이동한다.
- `position: fixed; (고정 포지셔닝)`: 뷰포트를 기준으로 고정된다.
- `position: sticky;( 흡착 포지셔닝)`: 뷰포트 내에서는 이동하지만 넘어가게 되면 고정 포지셔닝처럼 화면에 붙게된다.
- `z-index`: 스택의 순서를 조절하기 위한 속성이다.

## Multiple-column layout

다단 레이아웃 혹은 매거진 레이아웃이라고 부른다. 신문이나 논문과 같이 여러 열로 정보를 배치한다.

```html
<div class="“container”">
  <h1>Title</h1>
  <p>paragraph1</p>
  <p>paragraph2</p>
</div>

<style>
  .container {
    column-width: 200px;
  }
</style>
```

## Responsive design

다양한 화면 크기가 등장함에 따라 반응형 디자인이라는 개념이 등장했다. 서로 다른 화면 너비와 해상도 등에 맞게 웹페이지의 조판과 모양을 변경할 수 있는 아이디어인 것이다. 이 아이디어는 멀티 디바이스 웹에 대한 설계방식을 바꾸게 되었다. 반응형 디자인은 `Media Query` 덕분에 등장할 수 있었다.

```css
@media screen and (width: 600px) {
  body {
    color: red;
  }
}
```

## Legacy layout methods

`<table>`은 과거 레이아웃을 위해 많이 사용되었지만, 유연하지 못하여 더 이상 그 용도로는 쓰이지 않는다. `float` 속성 역시 다단 레이아웃을 생성할 용도로 쓰였으나, flexbox, grid의 등장으로 원래 목적으로 돌아갔다.

## 요소를 숨기는 방법

- `opacity`: opacity 속성을 0으로 설정하면 모습을 감춘다. 스크린 리더에 따라 읽히지 않을 수도 있다.
- `visibility`: visibility 속성을 **hidden**으로 설정하면 보이지 않을 뿐만 아니라 마우스 오버 효과나 포커스, 클릭 등의 이벤트도 무효로 만든다. 스크린 리더에게도 읽히지 않는다.
- `display`: display 속성을 **none**으로 설정하면 보이지도 않고 효과도 사라지며, **자리도 차지하지 않게** 된다.

---

[1] MDN Web Docs, [http://developer.mozilla.org/ko/docs/Learn](http://developer.mozilla.org/ko/docs/Learn)
[2] Kakao Tech, [https://tech.kakao.com/2022/05/20/on-demand-atomic-css-library/](https://tech.kakao.com/2022/05/20/on-demand-atomic-css-library/)
[3] Nomad Coder, CSS Layout 마스터클래스
