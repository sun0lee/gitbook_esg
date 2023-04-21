---
description: Cascading Style Sheets
---

# CSS

웹 페이지의 디자인과 레이아웃을 담당하는 스타일 시트 언어. HTML은 구조와 콘텐츠를 담당하고, CSS는 이를 꾸미는 역할을 담당.

CSS를 사용하면 HTML 문서에 있는 요소들의 크기, 색상, 폰트, 위치, 배경 등 다양한 스타일을 적용할 수 있음. 웹 페이지의 디자인을 통일성 있게 만들거나, 다양한 디바이스에 대응하여 반응형 웹 디자인을 구현할 수 있음.

선택자(Selector)와 선언부(Declaration)로 구성됨.

* 뭐를 : 선택자(Selector)는 CSS 규칙을 적용할 HTML요소(element)를 선택 (h1, p ...)
* 어떻게 할지 : 선언부(Declaration Block)는 선택한 요소에 적용할 스타일을 정의. 선언부는 프로퍼티(스타일 종류)와 값(스타일 속성 값)의 쌍으로 이루어짐.

```css
h1 {
  color: blue;
  font-size: 24px;
}

p {
  color: red;
  font-size: 16px;
}

/* HTML에서 class="highlight 와 같이 class로 정의된 요소에 스타일 지정
   HTML에서 class는 특정 요소에 대해 CSS 스타일을 적용하기 위해 지정하는 속성(attribute) 중 하나
    클래스는 하나 이상의 HTML 요소에 적용할 수 있으며, 
    동일한 클래스를 가진 여러 요소를 선택하여 스타일을 일관되게 적용할 수 있음 */
.highlight {
  background-color: yellow;
}
.text-center {
   text-align: center;
}
.red-text {
   color: red;
}
```

```html
<p class="text-center red-text">중앙 정렬된 빨간색 텍스트</p> 
<-- 하나의 요소에 여러 클래스를 지정하면 각 클래스에 해당하는 스타일 규칙들이 독립적으로 모두 적용 -->
```

Cascading은 "폭포처럼 내려오는" 또는 "계단식으로 내려가는" 것을 의미. CSS에서 Cascading은 여러 가지 규칙들 중에서 적용 우선순위가 높은 것부터 적용하면서 스타일링을 적용하는 방식을 의미함. 이때 적용 우선순위는 선택자의 구체성, 스타일 규칙의 명시성, 그리고 스타일 규칙이 선언된 위치 등에 의해 결정됨.

Cascading은 스타일 규칙의 충돌을 해결하고, 복잡한 스타일링 구조에서도 일관성 있게 스타일링을 적용할 수 있도록 도와줌.&#x20;

1. 중요도(!important) - !important가 선언된 스타일은 다른 모든 스타일보다 우선
2. 직접 선언(Inline styles) - HTML 요소의 style 속성에 직접 선언된 스타일은 다른 모든 스타일보다 우선
3. 아이디(Id) 선택자 - 아이디 선택자는 클래스 선택자보다 우선
4. 클래스(Class) 선택자 - 클래스 선택자는 태그 선택자보다 우선
5. 태그 선택자 - 태그 선택자는 전역 선택자보다 우선
6. 전역(Global) 선택자 - 스타일 시트의 맨 위에 선언된 스타일은 다른 스타일보다 우선&#x20;

따라서, 선언된 스타일이 동일한 우선순위를 가지면, 나중에 선언된 스타일이 적용됨.&#x20;
