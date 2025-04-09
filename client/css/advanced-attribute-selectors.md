# Advanced Attribute Selectors

Advanced Attribute Selectors는 CSS의 선택자 중 하나로, 요소의 속성을 특정하는 방법 중 하나입니다.

일반적인 속성 선택자인 `[attribute]`를 넘어서 특정 속성 값이나 특정 패턴을 가진 요소를 선택하는 더 복잡한 방법을 제공합니다.

## Element with Attribute (`[]`)

type 속성이 있는 요소를 모두 선택할 수 있습니다.\
모든 `input` 요소에 적용 가능하고 `disabled` 속성으로도 선택할 수 있습니다.

```css
/*
type 속성을 가지고 있는 요소 선택
*/

[type] {
  color: red;
}
```

```html
<input type="text" />
```

## Element with Specific Attribute Value (`[=]`)

`[attribute=value]`: 해당 속성 값이 **정확히 일치**하는 요소를 선택합니다.
단순히 '속성'만으로 선택하는 것 이상으로 해당 속성을 가진 요소들의 집합을 제한할 수 있습니다.

```css
/*
type 속성이 email인 값을 가지고 있는 요소 선택
*/

[type="email"] {
  color: red;
}
```

```html
<input type="email" />
```

예를 들어, `<a>` 태그의 `target` 속성이 정확히 `_blank`인 링크를 선택하려면

```css
a[target="_blank"] {
}
```

를 사용합니다.

## Element with Specific Attribute Value in List (`~`)

해당 속성 값에 **지정된 단어를 포함**하는 요소를 선택합니다.\
단어 사이에 공백이 있을 때만 작동합니다.\
목록에서 특정 속성 값을 가진 요소를 선택하고 싶다고 선언할 수도 있습니다.

```css
/* 
목록에 en-us가 있는 요소를 모두 선택하라고 선언 
목록에 다른 값이 있어도 되지만 en-us는 꼭 있어야 함
*/

[lang~="en-us"] {
  color: red;
}
```

```html
<p lang="en-us en-gb"></p>
```

예를 들어, `<ul>` 태그의 `class` 속성에 `list`라는 단어가 되어 있을 경우에는

```css
ul[class~="list"] {
}
```

를 사용합니다.

## Element with Specific Attribute Value/ Value- (`-`)

해당 속성 값이 주어진 값과 일치하거나 값 뒤에 하이픈(-)이 붙은 경우를 선택합니다.\
이 속성 값을 접두사로 사용하는 요소를 찾으라고 선언할 수 있습니다.

```css
/* 
lang이 en과 일치하거나 en 뒤에 하이픈(-)이 붙은 경우
*/

[lang|="en"] {
  color: red;
}
```

```html
<p lang="en-us"></p>
```

## Element with Specific Attribute Value Prefix (`^`)

특정 속성 값 접두사를 가진 요소를 선택할 수도 있습니다.\
즉, 해당 속성 값이 주어진 값으로 시작하는 요소를 선택합니다.

```css
/* 
해시(#) 기호로 시작하는 href 속성이 있는 모든 요소 선택
*/

[href^="#"] {
  color: red;
}
```

```html
<a href="#all">Link</a>
```

## Element with Specific Attribute Value Suffix (`$`)

특정 속성 값 접미사를 가진 요소를 선택할 수도 있습니다.

```css
/* 
href 속성이 있는 요소 중 .de로 끝나는 모든 요소
*/

[href$=".de"] {
  color: red;
}
```

```html
<a href="ab.de">Link</a>
```

## Element with At Least One Attribute Value (`*`)

속성 값에 주어진 값이 포함되어 있는 요소를 선택합니다.

```css
/* 
src 속성이 있는 모든 요소가 대상이며 콘텐츠의 일부로 cdn을 포함하는 값을 선택
*/

[src*="cdn"] {
  color: red;
}
```

```html
<img src="i.cdn.com" />
```

## Check Values Case-Insensitively (`i`)

대괄호를 닫기 전에 i라는 문자를 추가하여 필요에 따라 대소문자를 구분하는 기능을 켜거나 끌 수도 있습니다.

`i`를 추가하지 않는 기본값은 대소문자를 구분합니다.

`i`를 사용해면 해당 기능을 끄고 찾고 있는 값이 소문자로 되어 있든 대문자로 되어 있든 소문자와 대문자가 섞여 있든 상관없이 요소를 선택할 수 있습니다.

```css
/* 
src 속성이 있는 모든 요소가 대상이며 콘텐츠의 일부로 cdn을 포함하는 값을 선택
*/

[src*="cdn i"] {
  color: red;
}
```

```html
<img src="i.CDN.com" />
```
