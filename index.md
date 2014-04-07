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
- [Vertical margins often collapse](#vertical-margins-collapse)
- [Styling table rows](#styling-table-rows)
- [Firefox and `<input>` buttons](#buttons-firefox)
- [Firefox inner outline on buttons](#buttons-firefox-outline)
- [Always set a `type` on `<button>`s](#buttons-type)
- [Internet Explorer's selector limit](#ie-selector-limit)
- [Position explained](#position-explained)
- [Position and width](#position-width)
- [Fixed position and transforms](#position-transforms)


<a name="doctype"></a>
### doctype을 지정하기
doctype을 언제나 삽입하라. 나는 다음의 간단한 HTML5 doctype을 추천한다:

```html
<!DOCTYPE html>
```

[doctype을 빼먹을 경우](http://quirks.spec.whatwg.org) 기형인 테이블, input에서, 페이지가 quirks mode로 렌더링 될 경우엔 더 많은 문제가 발생할 수 있다.


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

**Help!** *이에 관한 Apple이나 WebKit 버그 리포트가 있다면 그걸 넣고 싶다. 데스크탑 사파리가 아니라 오직 모바일 사파리에서만 발생하기 때문에 어디에 제보해야 하는지 확실하지 않다.*


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

**Pro-Tip!** *float를 clear할 때는 `/* clearfix */` 같은 주석을 넣어 미래의 자신과 당신의 협업자가 행복할 수 있도록 하라.*


<a name="floats-computed-height"></a>
### float와 계산된 높이
float된 요소만을 가진 부모 요소는 계산된 `height; 0;`를 가지게 된다. 부모에 clearfix를 적용하여 브라우저가 높이를 계산하도록 강제하라.


<a name="floats-block-level"></a>
### float된 것은 블록 레벨
`float`를 가진 요소는 자동으로 `display: block;`이 된다. `float`가 자동으로 `display`를 overried하기 때문에 둘 다 설정할 필요는 없다.

```css
.element {
  float: left;
  display: block; /* 필요 없음 */
}
```

**재밌는 사실:** *수년 전까지 우리는 IE6에서의 [double margin 버그](http://www.positioniseverything.net/explorer/doubled-margin.html)를 피하기 위해 대부분의 float에 `display: inline;`을 지정해 줘야 했다. 하지만, 그런 시간은 이미 지났다.*


<a name="vertical-margins-collapse"></a>
### Vertically adjacent margins collapse
Top and bottom margins on adjacent elements (one after the other) can and will collapse in many situations, but never for floated or absolutely positioned elements. [Read this MDN article](https://developer.mozilla.org/en-US/docs/Web/CSS/margin_collapsing) or the CSS2 spec's [collapsing margin section](http://www.w3.org/TR/CSS2/box.html#collapsing-margins) to find out more.

Horizontally adjacent margins will **never collapse**.


<a name="styling-table-rows"></a>
### Styling table rows
Table rows, `<tr>`s, do not receive `border`s unless you set `border-collapse: collapse;` on the parent `<table>`. Moreover, if the `<tr>` and children `<td>`s or `<th>`s have the *same* `border-width`, the rows will not see their border applied. [See this JS Bin link for an example.](http://jsbin.com/yabek/2/)


<a name="buttons-firefox"></a>
### Firefox and `<input>` buttons

For reasons unknown, Firefox applies a `line-height` to submit and button `<input>`s that cannot be overridden with custom CSS. You have two options in dealing with this:

1. Stick to `<button>` elements
2. Don't use `line-height` on your buttons

Should you go with the first route (and I recommend this one anyway because `<button>`s are great), here's what you need to know:

```html
<!-- Not so good -->
<input type="submit" value="Save changes">
<input type="button" value="Cancel">

<!-- Super good everywhere -->
<button type="submit">Save changes</button>
<button type="button">Cancel</button>
```

Should you wish to go the second route, just don't set a `line-height` and use *only* `padding` to vertically align button text. [View this JS Bin example](http://jsbin.com/yabek/4/) in Firefox to see the original problem and the workaround.

**Good news!** *It looks like [a fix for this](https://bugzilla.mozilla.org/show_bug.cgi?id=697451#c43) might be coming in Firefox 30. That's good news for our future selves, but be aware this doesn't fix older versions.*


<a name="buttons-firefox-outline"></a>
### Firefox inner outline on buttons

Firefox [adds an inner outline](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/button#Notes) to buttons (`<input>`s and `<button>`s) on `:focus`. Apparently it's for accessibility, but its placement seems rather odd. Use this CSS to override it:

```css
input::-moz-focus-inner,
button::-moz-focus-inner {
  padding: 0;
  border: 0;
}
```

You can see this fix in action in the same [JS Bin example](http://jsbin.com/yabek/4/) mentioned in the previous section.

**Pro-Tip!** *Be sure to include some focus state on buttons, links, and inputs. Providing an affordance for accessibility is paramount, both for pro users who tab through content and those with vision impairments.*


<a name="buttons-type"></a>
### Always set a `type` on `<button>`s
The default value is `submit`, meaning any button in a form can submit the form. Use `type="button"` for anything that doesn't submit the form and `type="submit"` for those that do.

```html
<button type="submit">Save changes</button>
<button type="button">Cancel</button>
```

For actions that require a `<button>` and are not in a form, use the `type="button"`.

```html
<button class="dismiss" type="button">x</button>
```

**Fun fact:** *Apparently IE7 doesn't properly support the `value` attribute on `<button>`s. Instead of reading the attribute's content, it pulls from the innerHTML (the content between the opening and closing `<button>` tags). However, I don't see this as a huge concern for two reasons: IE7 usage is way down, and it seems rather uncommon to set both a `value` and the innerHTML on `<button>`s.*


<a name="ie-selector-limit"></a>
### Internet Explorer's selector limit
Internet Explorer 9 and below have a max of 4,096 selectors per stylesheet. There is also a limit of 31 combined stylesheets and `<style></style>` includes per page. Anything after this limit is ignored by the browser. Either split your CSS up, or start refactoring. I'd suggest the latter.

As a helpful side note, here's how browsers count selectors:

```css
/* One selector */
.element { }

/* Two more selectors */
.element,
.other-element { }

/* Three more selectors */
input[type="text"],
.form-control,
.form-group > input { }
```


<a name="position-explained"></a>
### Position explained
Elements with `position: fixed;` are placed relative to the browser viewport. Elements with `position: absolute;` are placed relative to their closest parent with a position other than `static` (e.g., `relative`, `absolute`, or `fixed`).


<a name="position-width"></a>
### Position and width
Don't set `width: 100%;` on an element that has `position: [absolute|fixed];`, `left`, and `right`. The use of `width: 100%;` is the same as the combined use of `left: 0;` and `right: 0;`. Use one or the other, but not both.


<a name="position-transforms"></a>
### Fixed position and transforms
Browsers break `position: fixed;` when an element's parent has a `transform` set. Using transforms creates a new containing block, effectively forcing the parent to have `position: relative;` and the fixed element to behave as `position: absolute;`.

[See the demo](http://jsbin.com/yabek/1/) and read [Eric Meyer's post on the matter](http://meyerweb.com/eric/thoughts/2011/09/12/un-fixing-fixed-elements-with-css-transforms/).
