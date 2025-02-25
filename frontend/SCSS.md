# SCSS

`SCSS`는 CSS의 **Pre-Processor**이다. 이런한 류의 Pre-Processor는 `Stylus`, `Less`와 같은 것들이 있는데 이들은 컴파일되면 일반적인 CSS로 변환이 된다. 하지만 SCSS는 CSS를 **Programming Language**처럼 만들어준다는 것이 차별점이라 할 수 있겠다.

## 변수 선언

$(달러) 표시로 변수를 선언할 수 있다.

```scss
$bg: #e7473c;
$title: 32px;
```

## Import

```scss
@import "_variables";

h2 {
  color: $bg;
}
```

## Nesting

특히 아래 예제처럼 `nesting`을 통해 `box` 안의 `h2`에게만 효과를 적용하는 등의 유용한 표현이 가능하다. 이러한 사용법을 통해 class 지옥에서 조금은 해방될 수 있는 것이다.

```scss
.box {
  margin-top: 20px;
  &:hover {
    background-color: green;
  }
  h2 {
    color: blue;
    &:hover {
      color: red;
    }
  }
  button {
    color: red;
  }
}
```

## Mixin

### 정의하기

아래와 같이 여러 줄의 CSS를 하나의 `Mixin`으로 정의할 수 있다. 아래 `$color`처럼 파라메터를 받을 수도 있다.

```scss
@mixin link($color) {
  text-decoration: none;
  display: block;
  color: $color;
}
```

### 사용하기

이렇게 만든 Mixin은 아래와 같이 `@include`를 통해 사용할 수 있다.

```scss
@import "_mixins";

a {
	margin-bottom: 10px;
	&:nth-child(odd) {
	@include link(red);
}

button {
	color: red;
	&:nth-child(even) {
		@include link(blue);
	}
}
```

### 조건문 사용

그리고 심지어는 아래와 같이 if문도 사용이 가능하다. 처음 설명처럼 프로그래밍 언어에 가까워지는 것이다.

```scss
@mixin link($text) {
  text-decoration: none;
  display: block;
  @if $text == "odd" {
    color: blue;
  } @else {
    color: red;
  }
}
```

## Extends

`%(퍼센트)` 기호를 통해 세트를 만들어서 아래처럼 확장 사용이 가능하다.

```scss
%button {
  font-family: inherit;
  border-radius: 7px;
  font-size: 12px;
  text-transform: uppercase;
  padding: 5px 10px;
  background-color: peru;
  color: white;
  font-weight: 500;
}
```

```scss
@import “_buttons”;

a {
  @extend %button;
  text-decoration: none;
}

button {
  @extend %button;
  border: ;
}
```

## 반응형 웹

아래와 같이 단말 크기에 따른 반응형 웹이 가능하다.

```scss
$minIphone: 500px;
$maxIphone: 690px;
$minTablet: $minIphone + 1;
$maxTablet: 1120px;

@mixin responsive($device) {
  @if $device == “iphone” {
    @media screen and (min-width: $minIphone) and (max-width: $maxIphone) {
      @content;
    }
  } @else if $device == “tablet” {
    @media screen and (min-width: $minTablet) and (max-width: $maxTablet) {
      @content;
    }
  } @else if $device == “iphone-l” {
    @media screen and (max-width: $minIphone) and (max-width: $maxIphone) and (orientation: landscape) {
      @content;
    }
  } @else if $device == “ipad-l” {
    @media screen and (min-width: $minTablet) and (max-width: $maxTablet) and (orientation: landscape) {
      @content;
    }
  }
}

@import “_mixins”;

h1 {
  color: red;
  @include responsive(“iphone”) {
    color: yellow; // @content가 될것이다
  }
  @include responsive(“iphone-l”) {
    font-size: 60px; // @content가 될것이다
  }
  @include responsive(“tablet”) {
    color: green; // @content가 될것이다
  }
}
```

---

[1] 노마드코더 유튜브
