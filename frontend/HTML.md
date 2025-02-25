# HTML 시작하기

## HTML이란?

`HTML(Hypertext Markup Language)`는 프로그래밍 언어가 아닌 우리가 보는 **웹페이지가 어떻게 구조화**되어 있는지 브라우저로 하여금 알 수 있도록 하는 마크업 언어이다. HTML은 elements로 구성되어 있다.

## HTML 요소(Element)의 구조

1. 여는 태그(Opening tag)
2. 닫는 태그(Closing tag)
3. 내용(Contents)

## 포함된 요소(Nesting elements)

요소 안에 다른 요소가 들어갈 수 있다. 그런 요소는 내포되었다고 표현한다. 내포되는 요소가 먼저 닫혀야하지만 브라우저에 따라 임의의 결과를 나타낼 수 있다. 기본적으로 웹이란 permittable한 정책을 보이기 때문이다.

```html
<p>My cat is <strong>very</strong> grumpy.</p>
```

## 블럭 레벨 요소(Block elements) vs 인라인 요소(Inline elements)

- `블록 레벨 요소(Block-level elements)`는 웹페이지 상에 블록(Block)을 만드는 요소이다. 블록 레벨 요소는 **앞뒤 요소 사이에 새로운 줄(Line)**을 만들고 나타닌다. 즉 블록 레벨 요소 이전과 이후 요소 사이의 줄을 바꿉니다. 블록 레벨 요소는 일반적으로 페이지의 구조적 요소를 나타낼 때 사용된다. 예를 들어 개발자는 블록 레벨 요소를 사용하여 단락(Paragraphs), 목록(lists), 네비게이션 메뉴(Navigation Menus), 꼬리말(Footers) 등을 표현할 수 있다. **블록 레벨 요소는 인라인 요소(Inline elements)에 중첩될(Nested inside)수 없다.** 그러나 블록 레벨 요소는 다른 블록 레벨 요소에 중첩될 수 있다.
- `인라인 요소(Inline elements)`는 **항상 블록 레벨 요소 내에 포함되어 있다.** 인라인 요소는 문서의 한 단락같은 큰 범위에는 적용될 수 없고 문장, 단어 같은 작은 부분에 대해서만 적용될 수 있다. **인라인 요소는 새로운 줄(Line)을 만들지 않는다.** 즉, 인라인 요소를 작성하면 그것을 작성한 단락 내에 나타나게 된다. 예를 들어, 인라인 요소에는 하이퍼링크를 정의하는 요소인 `<a>`, 텍스트(Text)를 강조하는 요소인 `<em>`, `<strong>` 등이 있다.

## 빈 요소(Empty elements, Void elements)

모든 요소가 여는 태그, 내용, 닫는 태그 패턴을 따르는 것은 아니다. 주로 문서에 무언가를 첨부하기 위해 `단일 태그(Single tag)`를 사용하는 요소도 있다. 예를 들어 `<img>` 요소는 해당 위치에 이미지를 삽입하기 위한 요소이다.

## 속성(Attributes)

속성은 요소에 실제론 나타내고 싶지 않지만 추가적인 내용을 담고 싶을 때 사용한다.

```html
<p class="“editor-note”">My cat is very grumpy</p>
```

속성을 사용할 때는 아래 내용을 지켜야 한다.

- 요소 이름 다음에 바로 오는 속성은 요소 이름과 속성 사이에 공백이 있어야 되고, 하나 이상의 속성들이 있는 경우엔 속성 사이에 공백이 있어야 한다.
- 속성 이름 다음엔 등호(=)가 붙는다.
- 속성 값은 열고 닫는 따옴표로 감싸야 합니다. 따옴표는 큰 따옴표나 작은 따옴표나 상관없다.

## 참과 거짓 속성(Boolean attributes)

때때로 값이 없는 속성을 볼 수 있을 텐데 이것은 허용되는 것이다. 일반적으로 그 속성의 이름과 동일한 하나의 값만을 가질 수 있다.

```html
<input type="“text”" disabled="“disabled”" /> <input type="“text”" disabled />
```

## HTML 문서의 구조

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>My test page</title>
  </head>
  <body>
    <img src="images/firefox-icon.png" alt="My test image" />
  </body>
</html>
```

### <!DOCTYPE html>

문서의 형식을 나타낸다. 아주 오래전 HTML 이 막 나왔을 때(1991년 2월 쯤), doctype은 자동 오류 확인이나 다른 유용한 것을 위해 HTML 페이지가 따라야 할 일련의 규칙을 의미했다. 하지만, 최근에는 아무도 그런 것들에 대해 신경쓰지 않는다.

### <html></html>

전체 페이지의 콘텐츠를 포함하며, 기본 요소이다.

### <head></head>

이 요소는 홈페이지 이용자에게는 보이지 않지만 검색 결과에 노출될 키워드, 홈페이지 설명, CSS 스타일, charset 등 HTML 페이지의 모든 내용을 담고 있다.

### <meta charset="utf-8">

이 요소는 문서가 사용해야 할 문자 집합을 utf-8으로 설정한다. utf-8 문자 집합에는 인간의 방대한 주류 기록언어에 있는 대부분의 문자가 포함되어 있어 본질적으로사용할 수 있는 어떠한 문자 컨텐트도 다룰 수 있다. 기타 다른 메타데이터에 대해서는 다음 상위 절에서 다룬다.

### <title></title>

이 요소를 사용하면 페이지 제목이 설정되며 페이지가 로드되는 브라우저 탭에 표시되는 제목으로 사용된다. 이 제목은 브라우저의 북마크 이름으로도 기본적으로 사용된다.

### <body></body>

여기에는 텍스트, 이미지, 비디오, 게임, 재생 가능한 오디오 트랙 등을 비롯하여 페이지에 표시되는 모든 콘텐츠가 포함된다.

## HTML의 메타데이터

HTML의 head는 페이지를 열 때 웹 브라우저에 표시되지 않는다. <title>과 같은 페이지나, CSS의 링크, 파비콘(favicon) 그리고 다른 메타데이터(작성자, 중요한 키워드와 같은 내용)을 포함한다.

### <meta> 요소

- 문서의 character 인코딩을 특정하기

```html
<meta charset="“utf-8”" />
```

- 저자와 설명을 추가
  페이지 콘텐츠 관련 키워드를 포함시키는 것은 검색엔진에서 이 페이지가 더 많이 표시될 가능성이 생기게 할 수 있다.

```html
<meta name="“author”" content="“Chris" Mills” />
<meta name="“description”" content="“The" MDN Learning Area…” />
```

- 소셜 네트워크에서 사용하는 메타데이터
  소셜 네트워크에서 더 풍부한 메타데이터를 제공하기 위해 만든 프로토콜이다.

```html
<meta property="“og:image”" content="“MDN" facebook” />
<meta name="“twitter:title”" content="“MDN" twitter” />
```

- favicon

```html
<link rel=“shortcut icon” href=“favicon.ico” type=“image/x-icon”>
```

- HTML에 CSS와 JavaScript 적용하기

```html
<link rel="“stylesheet”" href="“my-css.css”" />
<script src="“my-js.js”"></script>
```

- `<link>`는 빈요소이다.
- `<script>`는 head에 들어갈 필요가 없다. **</body> 태그의 바로 앞에** 넣어 JavaScript를 적용하기 전 모든 HTML 내용을 브라우저가 읽을 수 있도록 해야 한다.

- 문서의 기본 언어 설정
  HTML 문서는 언어가 설정되어 있으면 검색 엔진에 의해 보다 효과적으로 색인화되며, 시각 장애인이게 유용하다.

```html
<html lang="“en-US”"></html>
```

### Web Security 관련

- `Content Security Policy(CSP)`
  아래와 같은 메타 태그로 정책을 구성할 수 있다.

```html
<meta http-equiv=“Content-Security-Policy” content=“default-src” ‘self; img-src
https://*; child-src ‘none’;” />
```

`CSP`는 XSS(교차 사이트 스크립팅 ) 및 데이터 삽입 공격을 비롯한 특정 유형의 공격을 감지하고 완화하는데 도움이 되는 추가 보안 계층이다. 이러한 공격은 데이터 도난에서 사이트 변조, 맬웨어 배포에 이르기까지 모든것에 사용된다. 참고로 XSS는 공격자가 웹 사이트에 악성 클라이언트측 코드를 삽입할 수 있도록 하는 보안 악용이다. 보안 분야 온라인 커뮤니티인 `OWASP(Open Web Application Security Project)`에 따르면 XSS는 2017년 7번째로 흔한 웹 취약점이었다.

## HTML 공백

HTML 요소 내용 내에서 얼마나 많은 공백을 사용하든 HTML 파서는 코드를 렌더링할 때 각 공백 시퀀스를 단일 공백으로 줄인다. 이는 가독성을 위함이다. HTML의 중첩 요소는 **두 칸의 Indent**로 구분한다.

## Entity references(HTML 특수문자 처리)

HTML에서 `<`, `>`, `&`, `”`는 특수 문자이다. 이들은 HTML 구문의 일부이다. 이를 특수문자를 텍스트에 포함하기 위해서는 앰퍼센트(&)로 시작하고 세미콜론(;)으로 끝나는 구문 안에 Entity reference code를 넣어 처리한다.

```html
<p>In HTML, you define a paragraph using the &lt;p&gt; element.</p>
<span>Ampersand &amp;, Quotation &quot;, Space &nbsp;</span>
```

## HTML 주석

주석을 쓰려면 특수 마커 `<!—`, `—>`으로 주석을 묶는다.

```html
<p>I’m not inside a comment</p>
<!— <p>I am!</p> —>
```

# HTML fundamentals

## 제목과 단락

대부분의 구조화된 텍스트는 기사, 신문, 대학 교과서, 잡지 등 무엇을 읽든지 간에 제목과 단락으로 구성된다.

```html
<p>I am paragraph, oh yes I am.</p>
<h2>I am the title of the story.</h2>
```

## 구조화된 계층을 표현하기

`<h1>`은 이야기의 제목, `<h2>`는 각 장의 제목을 나타내고, `<h3>`는 각 장의 하위 절을 나타내고 이런 식으로 나타낸다. 구조화된 계층이 타당해 보이는 한 연관된 요소들이 정확히 무엇을 나타내는 지는 작성자에게 달려있다. 다만 다음 몇 가지의 관례를 기억하라.

- 가급적이면 페이지 당 하나의 <h1>만을 사용해야 한다. <h1>은 최상위에 있는 표제이며 나머지 다른 표제들은 계층적으로 이것 밑에 위치한다.
- 각 표제들을 계층적으로 올바른 순서로 사용해야 한다. <h3>를 하위 표제로 사용하고 그 다음 <h2>를 하위 표제의 하위로 사용하지 말라.
- 6개의 Heading을 사용할 수 있지만 꼭 필요한 것이 아니라면 **한 페이지에 3개** 이상을 사용하지 않도록 하라. 너무 많은 목차 레벨을 가진 문서는 다루기 힘들고 탐색하기 어렵다. 이러한 상황에서는 가능하다면 컨텐츠를 여러 페이지로 나누는 것이 바람직하다.

## 구조가 필요한 이유

- 웹 페이지를 보는 유저는 필요한 컨텐츠를 빠르게 훑어보는 경향이 있고 자주 `heading`만 읽기도 한다.
- 검색 엔진들은 페이지 내 `heading`을 페이지 검색 순위에 영향을 주는 중요한 키워드로 고려해 indexing한다.
- 심각한 시각 장애인들은 `heading`이 없다면 문서 전체에 대한 음성을 들어야 한다.
- 컨텐츠를 CSS로 꾸미거나, Javascript로 동작을 정의하기 위해서는 컨텐츠를 감싸는 요소가 필요하다.

## 우리는 왜 Semantic을 필요로 할까?

우리는 정확한 요소를 사용하고 있는지, 우리의 컨텐츠에 정확한 의미, 기능, 모습을 담았는지 확실히 해야한다. 이 맥락에서 <h1> 요소 또한. 텍스트에 “내 페이지 최상위 heading”의 역할로 감싸는 `semantic 요소`이다.

```html
<span style=“font-size: 32px; margin: 21px 0;”>Is this a top level heading?</span>
```

컨텐츠에 추가적인 의미를 더하지 않고도 CSS를 통해 최상위 heading처럼 보이게 할 수 있다. 하지만 이는 `semantic 가치`는 없기 때문에 위에서 서술한 추가적인 이득을 얻지는 못한다. **작업에 관계있는 HTML 요소를 사용하는 것이 좋다.**

## 접근성

접근성은 가능한 한 많은 사람이 웹 사이트를 사용할 수 있도록 하는 방법으로 통상적으로 장애인 만을 대상으로 한다고 생각하지만 실제로는 모바일 장치를 사용하는 사람이나 느린 네트워크 연결을 사용하는 사람들 도 포함하고 있다. 접근성은 당연히 지켜져야 할 일이지만 일부 국가에서는 법의 일부이기도 하며, 서비스 사용이나 제품 구매가 불가능했던 사람들을 불러 모아 중요한 소비자 들로 만들 수도 있다.

## WAI-ARIA

`Web Accessibility Initiative - Accessible Rich Internet Applciation`의 줄임말로 W3C에서 작성한 사양이다. WAI-ARIA는 브라우저와 보조 기술이 인식하고 사용자에게 진행 상황을 알리는데 사용할 수 있는 의미 체계를 추가하여 이러한 문제를 해결할 수 있는 기술이다. 사양에는 세 가지 주요 기능이 정의되어 있다.

- 역할: 요소가 무엇인지를 정의한다. `role=“navigation”`
- 속성: 추가 의미나 의미를 부여하는데 사용할 수 있는 요소의 속성을 정읳나다. `aria-required=“true`
- 상태: 요소의 현재 조건을 정의하는 특수 속성이다. `aria-disabled=“true”`

## 리스트

- Unordered

```html
<ul>
  <li>milk</li>
  <li>eggs</li>
  <li>bread</li>
</ul>
```

- Ordered

```html
<ol>
  <li>milk</li>
  <li>eggs</li>
  <li>bread</li>
</ol>
```

### Nesting lists

```html
<ol>
  <li>milk</li>
  <li>eggs</li>
  <li>bread</li>
  <ul>
    <li>nesting</li>
    <li>lists</li>
  </ul>
</ol>
```

### Description lists

이 리스트의 목적은 용어 및 정의, 질문 및 답변과 같은 일련의 항목 및 관련 설명을 표시하는 것이다. 브라우저에서 제공하는 기본 스타일에 의해 정의, 답변과 같은 하위 항목에 대해서 들여쓰기가 적용된다. 하나의 `<dt>`에는 여러개의 `<dd>`가 존재할 수 있다.

```html
<dl>
  <dt>soliloquy</dt>
  <dd>In drama, where a character speakers to themselves</dd>
  <dd>in wirting, a section of content that is related to the current topic</dd>
</dl>
```

## 중요와 강조

### 중요(Emphasis)

글에서는 단어에 강세를 두기 위해 이탤릭체로 표현하는 경향이 있다.

```html
<p>I am<em>glad</em> you weren’t<em>late</em>.</p>
```

위와 같이 표현하는 경우 상대가 조금 늦게 도착한 것에 대해 비꼬거나 짜증을 표현하는 느낌이다.

### 강조(Strong importance)

중요한 단어를 강조하기 위해 글자를 두껍게 표현한다. 이런 요소는 문서를 더 유용하게 만들 뿐만 아니라 화면판독기에 인식되면 다른 톤의 목소리로 표현된다.

```html
<p>This liquid is <strong>highly toxic</strong>. You may die.</p>
```

### Italic, bold, underline…

`<b>`, `<i>`, `<u>`의 경우 브라우저에서 CSS의 지원이 형편없는 경우를 대비해 만들어졌으나 더 이상 사용해서는 안된다. 위에서 설명한 요소를 대신 사용하도록 한다.

## 인용구

`<blockquote>`, `<q>` 요소를 통해 인용구를 나타낼 수 있다. 전자는 이름에서 알 수 있을 블록요소이며, <q>는 인라인 요소이다. 브라우저 기본 스타일은 인용구를 표현할 때, 들여쓰기 된 단락으로 나타낸다. 그리고 `cite` 속성에 출처를 표기한다.

```html
<blockquote cite="“[1]" 생활코딩”>
  <p>생코생코</p>
</blockquote>
```

## 약어

머리글자 또는 약어를 나타낼 때 사용하는 요소이다. `<abbr>` 요소로 나타낼 수 있다. 용어의 전체 뜻은 마우스를 올려 놓으면 Tooltip에 표시된다. 역시나 약어를 나타내기 위한 `<acronym>` 요소가 있지만 사용중지되었으므로 `<abbr>`을 사용하자.

```html
<p>We use <abbr title="“Hypertext" Markup Language”>HTML</abbr></p>
```

## 주소와 첨자

```html
<address>Chris Mills, Manchester, The Grim North, UK</address>
<p>Caffeine’s chemical formula is C<sub>8></sub>H<sub>10</sub></p>
<p></p>
<p>If x<sup>2</sup> is 9</p>
```

## 시간과 날짜 표시

기계가 인식하기 어려운 날짜를 기계가 인식하게 하는데 유용하다.

```html
<time datetime="“2016-01-20”">20 January 2016</time>
```

## 컴퓨터 코드

`<code>`는 소스코드, `<samp>`는 컴퓨터 프로그램의 출력을 나타낸다.

```html
<code>alert(“no!”);</code>
<var>const a = 3;</var>
<samp>$ ping mozilla.org</samp>
```

### <pre> 요소

스페이스나 탭, 엔터 등이 그대로 표시되는 태그이다. 글자로 아트 등을 만들때 사용한다.

## input

### label

`<label>`은 그냥 텍스트의 표현일 뿐이지만 굳이 `<input>`과 매핑하는 이유는 label을 클릭시 input에 포커스가 가기 때문이다.

```html
<label for="“id1”">라벨</label> <input id="“id1”" type="“number”" />
```

### 속성

> readonly: 수정이 안되고 전송은 되도록 한다.
> disable: 수정이 안되고 전송도 안되도록 한다.
> autofocus: 자동으로 포커스 될지를 지정한다.
> value: 초기값을 지정한다.

## form

양식 데이터를 보낼 수 있다.

```html
<form action=“https://example.com> <form action=“/something method=“POST”>
```

- 두 번째 경우에는 상대주소를 표현한 것이다.
- https 프로토콜을 사용하는 URL을 지정하는 경우 요청은 암호화되어 안전하다. 하지만 http 프로토콜을 사용하는 경우에는 브라우저가 보안 경고를 표시한다.

## button

### 입력 유형

> submit: 양식 데이터를 서버로 보낸다.
> reset: 모든 양식 위젯을 기본값으로 재설정한다.
> button: 자동 효과는 없지만 JavaScript 코드를 사용하여 사용자가 지정할 수 있는 버튼이다.

```html
<button type=“submit”>Submit</button>
<button type=“reset”>Reset</button>
<button type=“button” onClick={()=>{}}>func</button>
```

## fieldset

요소 내부를 Grouping한다. 예를 들어 아래 코드처럼 fieldset을 비활성화하면 내부가 모두 비활성화된다.

```html
<fieldset id="“asfd”" disable>
  <p>a1</p>
  <p>b1</p>
</fieldset>
```

# 하이퍼링크 만들기

## 하이퍼링크랑 무엇인가?

`하이퍼링크`는 웹이 제공하는 가장 흥미로운 혁신 중 하나이다. **하이퍼링크는 웹이 시작된 이래 웹의 특성이었다.** 하이퍼링크는 웹을 웹 다워보이도록 만든다.

## 링크의 구조

```html
<a href=“<https://www.mozilla.org>” title=“Mozilla”>the Mozilla homepage</a>
```

- `href 속성`은 사이트의 주소가 포함된 링크를 제공한다.
- `title`은 해당 페이지에 어떤 종류의 정보가 포함되어 있는지 또는 알아야할 사항, 링크에 대한 보충할만한 유용한 정보를 포함할 때 사용한다. 이것은 마우스를 오버했을 때의 Tooltip에 대한 기능도 제공해줄 수 있다.

## 스타일

사람들은 밑줄을 하이퍼링크와 강하게 연관시킨다. 앞서 설명한 underline 요소를 주의해서 사용해야하는 이유이다.

## Block level 링크들

어떤 내용이든 link로 바꿀 수 있다. `Block level 요소` 역시 link가 될 수 있다. 예를 들어 링크로 바꾸고 싶은 이미지가 있다면 `<a>`, `</a>` 사이에 그 이미지를 넣기만 하면 된다.

```html
<a href=“<https://www.mozilla.org>” title=“Mozilla”>
	<img src=“mozilla-image.png” alt=“mozilla”>
</a>
```

## URL과 path에 대한 기본 지침

`URL(Uniform Resource Locator)`는 무언가가 웹 상의 어디에 위치하는지 결정하는 하나의 텍스트 문자열이다. URL은 파일을 찾기 위해 `path`를 사용한다. `path`는 **파일이 파일 시스템 어디에 있는지**를 구체적으로 명시한다.

## Root

Top level `index.html`을 포함하는 경로가 일반적으로 Root이다. 대부분의 웹서버는 URL에서 명시하지 않는 경우 페이지를 불러오기 위해 `index.html`과 같은 **랜딩 페이지**를 찾는다. Root는 로컬 단에서 다룰 때 전체 웹사이트가 모두 들어갈 수 있는 하나의 디렉토리이다.

## path 나타내기

`Root`를 기준으로 아래와 같이 표현할 수 있다.

```html
<a href=“contacts.html”>contacts page</a> <!— 같은 디렉토리 —>
<a href=“proejcts/index.html”>project homepage</a> <!— 하위 디렉토리로 하향 이동 —>
<a href=“../pdfs/project-brief.pdf”>project brief</a> <!— 부모 디렉토리로 상향 이동 —>
```

## 문서 조각(Document fragments)

문서 상단이 아닌 HTML 문서 내부의 `특정 부분(fragment)`에 링크할 수 있다. 만약 특정 ID에 연결하려면 URL 끝에 `해시 기호`를 포함하면 된다. 혹은 fragments를 단독으로 사용하여 동일한 문서의 다른 부분에 연결할 수 있다.

```html
<a href="“contacts.html#Mailing_address”">mailing address</a>
<a href="“#Mailing_address”">company mailing address</a>

<h2 id="“Mailing_address”">Mailing address</h2>
```

## 주의사항

- 링크 텍스트의 일부로 URL을 작성하자 말라. URL은 보기 흉하며, 화면 판독기가 글자로 URL을 읽어낼 때 이상하게 들린다.
- 링크 텍스트에 `link`, `link to`라고 쓰지말라. 그것은 단지 소음이다. 링크에는 일반적으로 다른 색상으로 스타일링되고 밑줄이 그어져 있기 때문에 사용자들은 이를 파악할 수 있다.
- 링크 라벨은 가능한 짧게 유지하자. 긴 링크는 특히 전체 내용을 읽어야하는 스크린 리더 사용자들을 짜증나게 한다.
- 가능한 상대링크를 사용하라. 우선 짧은 링크는 스캔하기 쉬우며, 브라우저가 파일을 찾는 성능에 이점이 있다.
- 비 HTML 리소스 연결 시 명확한 표식을 남겨라. PDF나 워드 문서가 다운로드 되거나 비디오, 오디오 등이 갑자기 스트리밍되면 혼란을 야기할 수 있다.

```html
<a href=“large-report.pdf”>Download the sales report (PDF, 10MB)</a>
<a href=“/video-stream/”>Watch video (stream opens in separate tab, HD quality)</a>
```

## 다운로드 연결 시 download attribute 사용

브라우저에서 열지 않고 다운로드할 리소스에 연결하는 경우 다운로드 속성을 사용하여 기본 저장 파일 이름을 제공할 수 있다.

```html
<a href=“<https://download.mozilla.org?product=firefox-39.0-SSL”> download=“firefox-39-installer.exe”>Download Firefox 39 for Windows</a>
```

## 이메일 링크

<a> 태그 안에 `mailto:` URL 스키마를 사용하여 구현할 수 있다.

```html
<a href="“<mailto:asdf@gmail.com">”>Send email</a>
```

# HTML과 디버깅

HTML은 브라우저가 구문 분석하기 전에 다른 형식으로 컴파일되지 않으며 결과를 표시한다. 해석되지도 컴파일되지도 않는다. 브라우저가 HTML을 구문 분석하는 방식은 프로그래밍 언어가 실행되는 방식보다 훨씬 `유연(**permissive**)`하다. **브라우저 자체에서 구문 분석을 하기 때문에** HTML 자체는 문법 오류가 발생하지 않으므로 **문법 오류가 있어도 페이지는 계속 표시**된다. 브라우저에는 잘못된 마크 업을 해석하는 규칙이 내장되어 있기 때문이다.

# Document and website structure

## 문서의 기본 섹션

웹페이지는 서로 많이 다르게 보일 수 있지만, 페이지가 전체화면 비디오 혹은 게임이거나 예술 프로젝트, 좋지 않은 구조를 가지고 있지 않은 이상에는 대부분 유사한 구성요소를 가지고 있다.

## header

일반적으로 큰 제목과 로고 등이 있는 큰 띠. 한 웹페이지에서 주요 정보가 있는 곳이다. `<header>` 요소를 통해 시맨틱 마크업이 가능하다.

## navigation bar

홈페이지의 메인 섹션으로 연결한다. 대부분 메뉴 버튼이나 링크, 탭으로 표현된다. 헤더와 같이 이 항목은 대부분 한 페이지로 부터 다른 페이지로 넘어가도록 구성되어 있다. 많은 웹 디자이너들은 네비게이션 바를 개별적인 구성요소로 사용하기 보다는 header bar의 일부로 다루지만 이는 필수사항이 아니다. `<nav>` 요소를 통해 시맨틱 마크업이 가능하다.

## main content

웹 페이지에서 메인 컨텐츠를 포함하고 있는 중심의 큰 부분으로, 이 부분은 각 페이지마다 다른 웹 사이트의 한 부분이다. `<main>`, `<article>`, `<section>`, `<div>` 등의 요소를 통해 시맨틱 마크업이 가능하다. 다만 `<div>`는 사용하기 너무 편리해서 너무 많이 사용하기 쉽다. 의미론적 가치가 없기 때문에 사용을 최소화할 필요가 있다.

## side bar

주변의 정보, 링크, 인용 부호, 광고 등이다. `<aside>` 요소를 통해 시맨틱 마크업이 가능하다.

## footer

페이지 바닥의 줄로 일반적으로 작은 정보, 저작권 정보 또는 연락처 등을 포함하고 있다. `<footer>` 요소를 통해 시맨틱 마크업이 가능하다.

## <br>, <hr>

`<br>`은 줄넘김, `<hr>`은 문서에 수평선을 만든다. 텍스트의 주제 변경을 나타낼 수 있다.

# Multimedia and embedding

## Images in HTML

### Alternative text

`alt` 속성은 이미지가 보여지지 않을 경우 이미지를 설명할 수 있는 글을 값으로 가진다. 이는 검색엔진이 활용할 수 있으며, 시각장애인에게 정보를 제공할 수도 있다. 장식, 내용, 링크, 텍스트 등을 값으로 넣는다.

```html
<img src=“images/dinosaur.jpg” alt=“dinosour”>
```

### Width and height

평범한 경우 이 속성들은 디스플레이에 큰 차이를 주지 않는다. 다만 이미지가 아직 로딩되지 않았을때에도 브라우저는 지정된 크기의 공간을 이미지를 위해 할당해둔다.

```html
<img src=“images/dinosaur.jpg” alt=“dinosour” width=“400” height=“300”>
```

### Title

타이틀은 검색엔진에 정보를 전달할 뿐만 아니라 Tooltip을 통해 정보를 제공한다.

```html
<img src=“images/dinosaur.jpg” alt=“dinosour” title=“A-Rex on display”>
```

## Responsive images

### 개념

- `아트 디렉션(art direction)`: 이미지의 의도가 제대로 전달되도록 기기에 따라 사진의 핵심을 확대해서 보여주거나 하는 방식을 말한다. 예를 들어 좁은 화면에서 사이트를 볼 때 이미지의 중요한 상세를 보여주는 자른 이미지를 보여주는것 등이 있다.
- `해상도 전환(resolution switching)`: 작은 모바일 화면에서 페이지에 큰 이미지를 포함해서 대역폭을 낭비하지 않도록 기기에 따라 적당한 사이즈를 제공한다.

### HTML 만으로 반응형 이미지를 만드는 방법

```html
<img
  srcset="“elva-320w.jpg"
  320w,
  elva-480w.jpg
  480w,
  elva-800w.jpg
  800w”
  size="“(max-width:320px)"
  280px,
  (max-width:
  480px)
  440px,
  800px”
  src="“elva-fairy-800w.jpg”"
/>
```

### CSS나 자바스크립트로 해결할 때의 문제점

브라우저가 페이지를 불러오기 시작할 때, 메인 파서가 CSS와 자바스크립트를 로드하고 해석하기 전에 이미지들을 다운로드하기 시작한다. 따라서 `srcset`과 같은 해결책을 구현해야 한다.

## Audio and video content

`HTML5`에서 대표적으로 추가된 요소이다. 이는 곳 **플래시와 같은 서드파티와의 결별을 의미**했다.

```html
<audio controls>
  <source src=“viper.mp3” type=“audio/mp3”> <source src=“viper.ogg”
  type=“audio/ogg”>
  <p>
    Your browser doesn’t support HTML audio. Here is a
    <a href="“viper.mp3”">link to the audio</a> instead.
  </p>
</audio>

<video
  controls
  width="“400”"
  height="“400”"
  autoplay
  loop
  muted
  poster="“poster.png”"
>
  <source src=“rabbit320.mp4” type=“video/mp4”> <source src=“rabbit320.webm”
  type=“video/webm”>
  <p>
    Your browser doesn’t support HTML video. Here is a
    <a href="“rabbit320.mp4”">link to the video</a> instead.
  </p>
</video>
```

## iframe

```html
<iframe src=“<https://developer.mozilla.org>” width=“100%” height=“500” allowfullscreen sandbox>
	<p>
		<a href=“/en-US/docs/Glossary>
			Fallback link for browsers that don’t support iframes
		</a>
	</p>
</iframe>
```

`iframe`은 보안적 위험이 존재한다. 이를 최소화하기 위해 적어도,

- https 링크를 사용한다.
- sandbox 속성을 반드시 사용한다. 모던한 브라우저에서는 도움을 준다.
- 애초에 되도록 사용하지 않는다.

## table

```html
<table>
  <tr>
    <td>Data 1</td>
    <td>Data 2</td>
  </tr>
  <tr>
    <td>Calcutta</td>
    <td>Orange</td>
  </tr>
  <tr>
    <td>Robots</td>
    <td>Jazz</td>
  </tr>
</table>
```

# Dealing with files

## 포장과 공백에 대한 여담

디렉토리와 파일의 이름을 지을 때는 **공백없이 영문 소문자**로 지어야 한다. 많은 컴퓨터, 특히 웹 서버들은 영문 대소문자를 구분하며 공백을 일관되게 처리하지 않기 때문이다. 예를 들어 어떤 시스템은 공백을 “%20”으로 대체한다. 또한 밑줄문자보다는 **대시(하이픈)**으로 단어를 구분하는 것이 좋다.

## 웹사이트의 공통적인 파일 구조

- `index.html`: 처음 웹사이트에 방문하면 사람들이 볼 수 있는 텍스트나 이미지 등을 포함하는 내용을 가진다.
- `images 디렉토리`: 사이트에서 사용할 모든 이미지를 포함한다.
- `styles 디렉토리`: 사이트에서 사용하는 모든 CSS 코드를 포함한다.
- `scripts 디렉토리`: 사이트에서 사용하는 모든 자바스크립트 코드를 포함한다.

---

[1] MDN Web Docs, [http://developer.mozilla.org/ko/docs/Learn](http://developer.mozilla.org/ko/docs/Learn)
