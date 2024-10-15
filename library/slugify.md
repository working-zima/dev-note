# Slugify

slugify는 문자열을 슬러그화(sanitize)하는 라이브러리로, 주로 URL에서 사용하기 쉽게 문자열을 변환하는 데 사용됩니다.\
이 코드는 기본적으로 공백을 하이픈(`-`)으로 바꾸고, 영어 이외의 문자나 특수 문자를 처리하여 간단한 형태로 변환합니다.

slugify는 문자열을 웹에서 사용할 수 있도록 안전하게 변환하며, 다양한 옵션을 통해 매우 유연하게 사용할 수 있습니다.

## 예시

```js
var slugify = require('slugify');
slugify('some string');  // some-string
```

## 기본 기능

문자열의 공백을 기본적으로 하이픈(`-`)으로 변환하여 URL 친화적인 형태로 만들어 줍니다.\
예를 들어 `'some string'`은 `'some-string'`이 됩니다.

```js
slugify('some string', '_');  // some_string
```

구분자로 하이픈(`-`) 대신 언더스코어(`_`)를 사용하고 싶을 경우, 두 번째 인자로 원하는 구분자를 전달할 수 있습니다.

## 옵션 설명

slugify 함수는 다양한 옵션을 받을 수 있으며, 이를 통해 슬러그 변환 과정을 세부적으로 조정할 수 있습니다.

```js
slugify('some string', {
  replacement: '-',  // 공백을 대체할 문자, 기본값은 하이픈(`-`)
  remove: undefined, // 정규식을 통해 제거할 문자를 지정 (기본값 없음)
  lower: false,      // 소문자로 변환 여부, 기본값은 `false`
  strict: false,     // 구분 문자 외의 특수 문자를 제거할지 여부, 기본값은 `false`
  locale: 'vi',      // 사용할 로케일 지정
  trim: true         // 앞뒤 구분 문자를 제거할지 여부, 기본값은 `true`
});
```

- `replacement`: 기본적으로 공백을 하이픈으로 변환하지만, 다른 문자로 대체할 수 있습니다.

- `remove`: 정규식을 사용하여 제거할 문자를 지정할 수 있습니다.\
예를 들어 `remove: /[*+~.()'"!:@]/g`는 특수 문자를 제거합니다.

- `lower`: 소문자로 변환할지 여부를 설정할 수 있습니다.\
기본값은 `false`로 대소문자를 구분하지만, `true`로 설정하면 모든 문자를 소문자로 변환합니다.

- `strict`: `true`로 설정하면 구분 문자가 아닌 모든 특수 문자를 제거합니다.

- `locale`: 특정 로케일에 맞춰 문자 변환을 적용할 수 있습니다.\
예를 들어 `locale: 'vi'`는 베트남어에 맞춰 처리합니다.

- `trim`: 구분 문자(예: 하이픈(`-`))가 문자열의 앞뒤에 있으면 제거할지 여부를 설정합니다.

## 문자의 제거 예시

```js
slugify('..', { remove: /[*+~.()'"!:@]/g });
```

이 코드는 지정된 특수 문자 `*+~.()'"!:@`를 결과에서 제거하여 슬러그화합니다.\
이때 정규식은 반드시 전역(global) 플래그를 사용해야 합니다.

## 유니코드 및 문자 확장

```js
slugify.extend({'☢': 'radioactive'});
slugify('unicode ♥ is ☢');  // unicode-love-is-radioactive
```

이 코드는 기본 charMap을 확장하여 유니코드 문자를 원하는 방식으로 변환할 수 있게 해줍니다.\
예를 들어, 방사능 기호(`☢`)를 `"radioactive"`로 변환하도록 지정합니다.

## 자료

[slugify](https://www.npmjs.com/package/slugify)
