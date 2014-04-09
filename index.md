---
layout: default
---

### Contents

- [doctype을 지정하기](#doctype)
- [박스 모델 계산](#box-model-math)
- [rem 단위와 모바일 사파리](#rems-mobile-safari)
- [float 먼저](#floats-first)
- [float와 clear](#floats-clearing)
- [float와 계산된 높이](#floats-computed-height)
- [float된 것은 블록 레벨](#floats-block-level)
- [수직 margin은 자주 충돌한다](#vertical-margins-collapse)
- [표의 줄 꾸미기](#styling-table-rows)
- [Firefox와 `<input>` 버튼](#buttons-firefox)
- [버튼에서 Firefox의 내부 outline](#buttons-firefox-outline)
- [언제나 `<button>`에 `type`을 지정하라](#buttons-type)
- [Internet Explorer의 selector 제한](#ie-selector-limit)
- [position 설명](#position-explained)
- [position과 너비](#position-width)
- [fixed position과 transform](#position-transforms)


<a name="doctype"></a>
### doctype을 지정하기
doctype을 언제나 삽입하라. 나는 다음의 간단한 HTML5 doctype을 추천한다:

```html
<!DOCTYPE html>
```

[doctype을 빼먹을 경우](http://quirks.spec.whatwg.org) 기형인 표, input에서, 페이지가 quirks mode로 렌더링 될 경우엔 더 많은 문제가 발생할 수 있다.


<a name="box-model-math"></a>
### 박스 모델 계산
`width`가 설정된 요소는 `padding` 이나 `border-width`가 있을 경우 더 *넓어진다*. 이 문제를 피하기 위해선, 이젠 일반적인 [`box-sizing: border-box;` reset](http://www.paulirish.com/2012/box-sizing-border-box-ftw/)을 사용하라.


<a name="rems-mobile-safari"></a>
### rem 단위와 모바일 사파리
모바일 사파리가 `rem`을 모든 속성 값에 지원하기 때문에, `rem`이 크기의 미디어 쿼리에 사용된 경우 페이지의 텍스트가 다른 크기로 깜빡이는 경우가 생긴다.

이젠 `rem` 대신 `em`을 사용하라.

```css
html {
  font-size: 16px;
}

/* 모바일 사파리에서 깜빡이는 버그가 생김 */
@media (min-width: 40rem) {
  html {
    font-size: 20px;
  }
}

/* 모바일 사파리에서 잘 됨 */
@media (min-width: 40em) {
  html {
    font-size: 20px;
  }
}
```

**도와줘!** *이에 관한 Apple이나 WebKit 버그 리포트가 있다면 그걸 넣고 싶다. 데스크탑 사파리가 아니라 오직 모바일 사파리에서만 발생하기 때문에 어디에 제보해야 하는지 확실하지 않다.*


<a name="floats-first"></a>
### float 먼저
float된 요소는 문서에서 순서 상으로 가장 먼저 와야 한다. float된 요소는 뭔가로 감싸지 않으면 내용 밑에 나타나는 대신 밑으로 내려가지는 효과가 생긴다.

```html
<div class="parent">
  <div class="float">Float</div>
  <div class="content">
    <!-- ... -->
  </div>
</div>
```


<a name="floats-clearing"></a>
### float와 clear
float를 썼으면, *아마도* clear할 필요가 있다. `float`된 요소 뒤에 따라오는 모든 내용은 clear되기 전까지 그 요소를 감싸게 될 것이다. float를 clear하기 위해서는 다음 중 하나를 사용하라.

float를 clear하려면 별개의 class로 된 [작은 clearfix](http://nicolasgallagher.com/micro-clearfix-hack/)를 사용하라.

```css
.clearfix:before,
.clearfix:after {
  display: table;
  content: "";
}
.clearfix:after {
  clear: both;
}
```

혹은 부모 요소에 `overflow`를 `auto`나 `hidden`으로 지정해도 좋다.

```css
.parent {
  overflow: auto; /* clearfix */
}
.other-parent {
  overflow: hidden; /* clearfix */
}
```

`overflow`는 부모 안에 있는 위치가 지정된 요소같은 곳에서 의도치 않은 부작용이 생길 수도 있다는 것을 명심하라.

**전문팁!** *float를 clear할 때는 `/* clearfix */` 같은 주석을 넣어 미래의 자신과 당신의 협업자가 행복할 수 있도록 하라.*


<a name="floats-computed-height"></a>
### float와 계산된 높이
float된 요소만을 가진 부모 요소는 계산된 `height; 0;`를 가지게 된다. 부모에 clearfix를 적용하여 브라우저가 높이를 계산하도록 강제하라.


<a name="floats-block-level"></a>
### float된 것은 블록 레벨
`float`를 가진 요소는 자동으로 `display: block;`이 된다. `float`가 자동으로 `display`를 override하기 때문에 둘 다 설정할 필요는 없다.

```css
.element {
  float: left;
  display: block; /* 필요 없음 */
}
```

**재밌는 사실:** *수년 전까지 우리는 IE6에서의 [이중 margin 버그](http://www.positioniseverything.net/explorer/doubled-margin.html)를 피하기 위해 대부분의 float에 `display: inline;`을 지정해 줘야 했다. 하지만, 그런 시간은 이미 지났다.*


<a name="vertical-margins-collapse"></a>
### 수직으로 인접한 margin은 충돌한다

인접한 요소에 적용된 위 아래 margin은 여러 상황에서 충돌할 수 있으며 충돌하지만, float되거나 절대적 위치가 지정된 요소는 그렇지 않다. 자세한 내용은 [이 MDN 글](https://developer.mozilla.org/en-US/docs/Web/CSS/margin_collapsing)을 읽어보거나, CSS2 규격의 [충돌하는 margin 섹션](http://www.w3.org/TR/CSS2/box.html#collapsing-margins)을 읽어보라.

수평으로 인접한 margin은 **절대 충돌하지 않는다**.


<a name="styling-table-rows"></a>
### 표의 줄 꾸미기
표의 줄, `<tr>`은 부모인 `<table>`에 `border-collapse: collapse;`를 지정하지 않는 이상 `border`가 적용되지 않는다. 거기다, `<tr>`과 자식인 `<td>`이나 `<th>`가 *같은* `border-width`를 가지고 있다면 그 줄에는 테두리가 적용되지 않는다. [예시로 이 JS Bin 링크를 보라.](http://jsbin.com/yabek/2/)


<a name="buttons-firefox"></a>
### Firefox와 `<input>` 버튼

이유는 알 수 없지만, Firefox는 다른 CSS로 override할 수 없는 `line-height`을 submit과 button `<input>`에 적용시킨다. 이걸 처리하는 두가지 방법이 있다:

1. `<button>` 요소에 붙이기
2. 버튼에 `line-height`를 쓰지 않기

첫번째 방법으로 가야 한다면 (그리고 어쨌든 `<button>`은 대단하기 때문에 이 방법을 추천한다), 알아야 할 것이 있다:

```html
<!-- 좋지 않다 -->
<input type="submit" value="저장하기">
<input type="button" value="취소">

<!-- 어느 상황에서도 좋다 -->
<button type="submit">저장하기</button>
<button type="button">취소</button>
```

두번째 방법으로 가야 한다면, 그냥 `line-height`를 지정하지 않고 *오직* `padding`을 이용해서 버튼의 텍스트를 수직 정렬하라. Firefox에서 [이 JS Bin 예시](http://jsbin.com/yabek/4/)를 보고 문제점과 해결책을 보라.

**좋은 소식!** *Firefox 30에 [이 문제에 관한 해결책](https://bugzilla.mozilla.org/show_bug.cgi?id=697451#c43)이 나올 것 같다. 미래의 우리에게는 좋은 소식이지만, 구버전에서는 그대로라는 것을 조심하라.*


<a name="buttons-firefox-outline"></a>
### 버튼에서 Firefox의 내부 outline

Firefox는 버튼(`<input>`과 `<button>`)의 `:focus`시 [내부 outline을 추가한다](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#Notes). 접근성을 위한 것으로 보이지만 위치는 조금 이상하다. 이 CSS로 그걸 override하라:

```css
input::-moz-focus-inner,
button::-moz-focus-inner {
  padding: 0;
  border: 0;
}
```

전 섹션에서 언급한 같은 [JS Bin 예시](http://jsbin.com/yabek/4/)에서 이 fix가 작동하는 것을 볼 수 있다.

**전문팁!** *버튼과 링크, input에 focus 상태를 꼭 추가하라. 접근성을 위한 유도는 내용을 tab으로 이동하는 고급 사용자나 시각장애가 있는 사람 모두에게 중요하다.*


<a name="buttons-type"></a>
### 언제나 `<button>`에 `type`을 지정하라
기본 값은 `submit`이라는 것은 form의 모든 버튼이 form을 submit할 수 있다는 뜻이다. form을 submit하지 않는 버튼에는 `type="button"`을 지정하고, 하는 버튼에는 `type="submit"`을 지정하라,

```html
<button type="submit">저장하기</button>
<button type="button">취소</button>
```

form 안에 없지만 필요한 `<button>`에는 `type="button"`을 지정하라.

```html
<button class="dismiss" type="button">x</button>
```

**재밌는 사실:** *IE7은 `<button>`에서 `value` 속성을 지원하지 않는 것 같다. 속성의 값을 읽는 대신 innerHTML(`<button>`의 열고 닫는 태그 사이의 내용)을 가져온다. 어쨌든, 나는 두가지 이유로 이게 큰 문제라고 생각하지 않는다: IE7의 사용률은 하락하고 있고, `<button>`에 `value`와 innerHTML을 둘 다 지정하는 건 흔하지 않기 때문이다.*


<a name="ie-selector-limit"></a>
### Internet Explorer의 selector 제한
Internet Explorer 9 이하는 스타일시트마다 4096개의 selector 제한이 있다. 또한 한 페이지에 도합 31개의 스타일시트와 `<style></style>` 제한이 있다. CSS를 나누거나, 리팩토링하라. 나는 후자를 추천한다.

유용한 정보로, 여기에 브라우저가 selector를 세는 방법이 있다:

```css
/* selector 1개 */
.element { }

/* selector 2개 더 */
.element,
.other-element { }

/* selector 3개 더 */
input[type="text"],
.form-control,
.form-group > input { }
```


<a name="position-explained"></a>
### position 설명
`position: fixed;`가 지정된 요소는 브라우저의 viewport에 상대적으로 놓여진다. `position: absolute;`가 지정된 요소는 가장 가까운 `static`이 아닌(예를 들어 `relative`, `absolute`, 혹은 `fixed`) 부모의 위치에 상대적으로 놓여진다.


<a name="position-width"></a>
### position과 너비
`position: [absolute|fixed];`, `left`, 그리고 `right`가 지정된 요소에 `width: 100%;`를 지정하지 말라. `width: 100%;`는 `left: 0;`와 `right: 0;`를 지정한 것과 같다. 둘 중 하나만 쓰지, 둘 다 쓰지는 말라.


<a name="position-transforms"></a>
### fixed position과 transform
브라우저는 요소의 부모에 `transform`이 지정되어 있을 경우 `position: fixed;`를 무시한다. transform을 사용하는 것은 감싸는 블록을 새로 생성해 부모에 `position: relative;`를 지정하고, 고정된 요소를 `position: absolute;`처럼 작동하게 한다.

[예시](http://jsbin.com/yabek/1/)를 보고 [관련된 Eric Meyer의 글](http://meyerweb.com/eric/thoughts/2011/09/12/un-fixing-fixed-elements-with-css-transforms/)을 읽어보라.
