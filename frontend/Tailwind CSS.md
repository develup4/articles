# Tailwind CSS

`Tailwind CSS`는 **Post CSS 플러그인**으로 모든 HTML 파일, JavaScript 구성 요소 및 기타 템플릿에서 클래스 이름을 검색한 다음 정적 CSS 파일을 작성하여 작동한다. 빠르고 유연하며 안정적이고 런타임이 없다.

## Why not inline?

### 도입 배경(Atomic CSS)

<aside>
💡 [https://tech.kakao.com/2022/05/20/on-demand-atomic-css-library/](https://tech.kakao.com/2022/05/20/on-demand-atomic-css-library/)

</aside>

## 핵심 개념

### 효용 우선

Tailwind를 사용하면 **HTML에서 직접 기존 클래스를 적용하여 요소의 스타일을 지정**할 수 있다. 이 접근 방식을 사용하면 사용자는 CSS를 한 줄도 작성하지 않고도 완전한 사용자 지정 구성 요소 디자인을 구현할 수 있다.

특히나 React와 같은 JSX와 컴포넌트 기반 프레임워크(라이브러리)를 사용하면 **JavaScript 파일만으로 프론트엔드를 구성**할 수도 있다.

이는 다수의 사람들에게 선호되지 않는 방식일 수 있지만, 이는

- 클래스 이름을 만드는데 에너지를 낭비하지 않게 해준다.
- CSS의 크기가 커지는 것을 막을 수 있다.
- CSS는 전역적이지만 HTML의 클래스는 로컬이므로 변경에 더 안전하다.

## 제공 요소

<aside>
💡 [https://tailwindcss.com/docs/](https://tailwindcss.com/docs/) 의 Layout 항목 아래로부터 원하는 항목을 찾아서 사용한다.

</aside>

### 상태 표현

<aside>
💡 [https://tailwindcss.com/docs/hover-focus-and-other-states](https://tailwindcss.com/docs/hover-focus-and-other-states) 를 참조한다.

</aside>

## Configurations

### Preflight

Tailwind CSS의 설치 및 설정은 공식 홈페이지를 참고한다. 다만 그 과정에서 아래의 기본 스타일이 적용되게 되는데, 이를 `Preflight`라고 한다. 이는 브라우저간 불일치를 완화하고 디자인 시스템의 제약 조건 내에서 더 쉽게 작업할 수 있도록 설계된 Tailwind 프로젝트용 기본 스타일 세트이다.

```css
@tailwind base;
@tailwind components;
@tailwind utilities;
```

Preflight에 있는 대부분의 스타일은 눈에 띄지 않게 하기 위한 것이다. 즉, 단순히 사용자가 예상한 대로 동작하게 만들 뿐이다.

- 기본 여백이 제거된다.
- 제목에 스타일이 지정되지 않는다.
- 목록에 스타일이 지정되지 않는다.
- 이미지는 블록 요소이다.
- 테두리 스타일은 전역적으로 재설정된다.
- 버튼에는 기본 윤곽선이 있다.

### 지시문

- `@tailwind`는 Tailwind CSS 프로젝트에 특별한 기능을 제공하는 지시문이다.
- `@layer`는 사용자 정의 스타일이 속한 버킷을 알린다.
- `@apply`는 기존 유틸리티 클래스를 사용자 정의 CSS에 인라인하는데 사용한다.

### 확장

기본 스타일을 변경하고 싶을 때 확장할 수 있다.

```css
@tailwind base;

@layer base {
  h1 {
    @apply text-2xl;
  }
}

@tailwind components;
@tailwind utilities;
```

### 비활성화

기본 스타일을 비활성화하고 싶을 때, 아래와 같이 설정한다.

```css
// tailwind.config.js
module.exports = {
  coreplugins: {
    preflight: false;
  }
}
```

### Customizing

```jsx
// tailwind.config.js
module.exports = {
  content: ["./src/**/*.{html,js}"],
  theme: {
    colors: {
      blue: "#1fb6ff",
      purple: "#7e5bef",
      pink: "#ff49db",
      orange: "#ff7849",
      green: "#13ce66",
      yellow: "#ffc82c",
      "gray-dark": "#273444",
      gray: "#8492a6",
      "gray-light": "#d3dce6",
    },
    fontFamily: {
      sans: ["Graphik", "sans-serif"],
      serif: ["Merriweather", "serif"],
    },
    extend: {
      spacing: {
        "8xl": "96rem",
        "9xl": "128rem",
      },
      borderRadius: {
        "4xl": "2rem",
      },
    },
  },
};
```

### 임의 값을 사용해서 사용자 정의 스타일 추가

**Tailwind CSS 3.0**부터 동적 바인딩이 적용되면서 아래와 같은 방식도 가능하게 되었다. 이전 버전을 사용해봤다면 정말 유용한 기능임을 알 수 있다.

```html
<div class="“top-[117px]" bg-[#bada55]”></div>
```

---

[1] Tailwind CSS Docs, [https://tailwindcss.com/docs/](https://tailwindcss.com/docs/)
[2] Kakao Tech, [https://tech.kakao.com/2022/05/20/on-demand-atomic-css-library/](https://tech.kakao.com/2022/05/20/on-demand-atomic-css-library/)
