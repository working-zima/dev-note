# express-validator

```bash
npm i express-validator
```

## The Validation Chain(유효성 검증)

```tsx
post('route', [유효성 검증], (req, res, next) => {} )
```

Validation Chain은 미들웨어 함수로, 어떤 Express.js 라우트 핸들러에도 전달될 수 있습니다.\
유효성 검사 체인의 주요 기능은 validators, sanitizers, modifiers 세 가지 메서드로 나뉩니다.\
여러 검사 메서드를 연결해 사용하며 아래의 함수로 부터 시작합니다.

- `check`
: 가장 일반적인 유효성 검사 함수입니다.\
이 메소드는 요청 객체의 어떤 부분이든 검사할 수 있습니다.

- `body`
: HTTP 요청의 `body` 부분에서만 작동합니다.\
요청된 바디에 `text`가 있고, `text`에 대한 유효성을 검사하고 싶으면 `body('text')`를 사용하면 됩니다.

- `param`
: url의 경로 매개변수를 검사하는 데 사용됩니다.\
예를 들어, `/user/:id`와 같은 `url`에서 `id` 매개변수의 유효성 검사를 하려면 `param('id')`를 사용하면 됩니다.

- `query`
: url의 특정 쿼리 문자열(`?`이후)에서 유효성 검사를 합니다.\
예를 들어, `/search?query=something`의 query 유효성 검사를 진행하고 싶다면, `query('query')`를 사용하면 됩니다.

- `header`
: 헤더에서 유효성 검사를 합니다.\
예를 들어, 컨텐츠 유형, 인증 정보 등을 포함할 수 있습니다.\
`header('Authorization')`은 `Authorization`에 대한 유효성 검사를 합니다.

### 특징

- `express.js` 라우트 핸들러로 사용하여 자동으로 검증을 실행합니다.
- `express-validator`의 다른 함수들의 매개변수로 사용할 수 있습니다.\
예를 들어 `oneOf()` 또는 `checkExact()` 함수 안에서 사용할 수 있습니다.
- 독립적으로 사용하여 검증이 언제 실행되고 어떻게 실행될지를 완전히 제어할 수 있습니다.

만약 ValidationChain을 매개변수로 받는 함수를 작성 중이라면, TypeScript 타입은 다음과 같이 가져올 수 있습니다.

```bash
import { ValidationChain } from 'express-validator';
```

### Built-in validators

#### .custom()

```tsx
custom(validator: (value, { req, location, path }) => any): ValidationChain
```

이 메서드는 검증 체인에 사용자 정의 검증기 함수를 추가합니다.

- 사용자 정의 검증기가 truthy 값을 반환하면 필드 값은 유효합니다.
- 사용자 정의 검증기가 해결되는 프로미스를 반환하면 필드 값은 유효합니다.
- falsy 값을 반환하거나, 거부되는 프로미스를 반환하거나, 함수가 예외를 `throw`하는 경우 필드 값은 유효하지 않습니다.

일반적인 `.custom()` 사용 예로는 이메일 주소가 이미 존재하는지 확인하는 것입니다.\
이미 존재한다면 에러를 반환합니다:

```tsx
app.post(
  '/signup',
  body('email').custom(async value => {
    const existingUser = await Users.findUserByEmail(value);
    if (existingUser) {
      throw new Error('E-mail already in use');
    }
  }),
  (req, res) => {
    // 요청 처리
  },
);
```

#### .exists()

```tsx
exists(options?: {
  values?: 'undefined' | 'null' | 'falsy',
  checkNull?: boolean,
  checkFalsy?: boolean
}): ValidationChain
```

이 메서드는 필드가 존재하는지 여부를 확인하는 검증기를 추가합니다.

`options.values`에 따라 어떤 값이 존재하는지 결정됩니다.\
기본적으로 `undefined`로 설정되어 있습니다:

options.values | 동작
:-: | :-:
`undefined` | `undefined` 값은 존재하지 않음
`null` | `undefined`와 `null` 값은 존재하지 않음
`falsy` | falsy 값 (빈 문자열, `0`, `false`, `null` 및 `undefined` 값)은 존재하지 않음

`options.checkNull` 및 `options.checkFalsy`는 더 이상 사용되지 않는 옵션으로, 각각 `options.values`를 `null` 및 `falsy`로 설정하는 별칭입니다.

#### .isArray()

```tsx
isArray(options?: { min?: number; max?: number }): ValidationChain
```

이 메서드는 값이 배열인지 확인하는 검증기를 추가합니다.\
이는 JavaScript 함수 `Array.isArray(value)`와 유사합니다.

또한 배열의 길이가 `options.min`보다 크거나 같고/또는 `options.max`보다 작거나 같은지 확인할 수도 있습니다.

#### .isObject()

```tsx
isObject(options?: { strict?: boolean }): ValidationChain
```

이 메서드는 값이 객체인지 확인하는 검증기를 추가합니다.\
`{}`, `{ foo: 'bar' }` 및 `new MyCustomClass()`와 같은 값들은 모두 이 검증기를 통과합니다.

strict 옵션이 false로 설정되어 있으면, 이 검증기는 순수 JavaScript에서 `typeof value === 'object'`와 유사하게 작동하며, 배열과 `null` 값도 객체로 간주합니다.

#### .isString()

```tsx
isString(): ValidationChain
```

이 메서드는 값이 문자열인지 확인하는 검증기를 추가합니다.\
이는 순수 JavaScript에서 `typeof value === 'string'`과 유사합니다.

#### .notEmpty()

```tsx
notEmpty(): ValidationChain
```

이 메서드는 값이 비어 있지 않은 문자열인지 확인하는 검증기를 추가합니다.\
이는 `.not().isEmpty()`와 유사합니다.

#### Standard validators

Validator | Description
:-: | :-:
`contains(str, seed [, options])` | 문자열이 seed를 포함하는지 확인합니다. <br/><br/>options는 기본값이 { ignoreCase: false, minOccurrences: 1 }인 객체입니다.<br />옵션: <br/> ignoreCase: 비교 시 대소문자를 무시합니다. 기본값은 false입니다.<br/>minOccurences: 문자열에서 seed의 최소 발생 횟수입니다. 기본값은 1입니다.
`equals(str, comparison)` | 문자열이 comparison과 일치하는지 확인합니다.
`isAbaRouting(str)` | 문자열이 미국 은행 계좌/수표의 ABA 라우팅 번호인지 확인합니다.
`isAfter(str [, options])` | 문자열이 지정된 날짜 이후의 날짜인지 확인합니다.<br/><br/>options는 기본값이 { comparisonDate: Date().toString() }인 객체입니다.<br/>옵션:<br/>comparisonDate: 비교할 날짜입니다. 기본값은 Date().toString() (현재 시간)입니다.
`isAlpha(str [, locale, options])` | 문자열에 문자 (a-zA-Z)만 포함되어 있는지 확인합니다.<br/><br/>locale은 ['ar', 'ar-AE', 'ar-BH', 'ar-DZ', 'ar-EG', 'ar-IQ', 'ar-JO', 'ar-KW', 'ar-LB', 'ar-LY', 'ar-MA', 'ar-QA', 'ar-QM', 'ar-SA', 'ar-SD', 'ar-SY', 'ar-TN', 'ar-YE', 'bg-BG', 'bn', 'cs-CZ', 'da-DK', 'de-DE', 'el-GR', 'en-AU', 'en-GB', 'en-HK', 'en-IN', 'en-NZ', 'en-US', 'en-ZA', 'en-ZM', 'eo', 'es-ES', 'fa-IR', 'fi-FI', 'fr-CA', 'fr-FR', 'he', 'hi-IN', 'hu-HU', 'it-IT', 'kk-KZ', 'ko-KR', 'ja-JP', 'ku-IQ', 'nb-NO', 'nl-NL', 'nn-NO', 'pl-PL', 'pt-BR', 'pt-PT', 'ru-RU', 'si-LK', 'sl-SI', 'sk-SK', 'sr-RS', 'sr-RS@latin', 'sv-SE', 'th-TH', 'tr-TR', 'uk-UA'] 중 하나이며 기본값은 en-US입니다. locale 목록은 validator.isAlphaLocales입니다. options는 선택적 객체로, ignore 키가 있을 수 있습니다. ignore는 문자열 또는 무시할 문자의 정규 표현식일 수 있습니다. 예: " -"는 공백과 -를 무시합니다.
`isAlphanumeric(str [, locale, options])` | 문자열에 문자와 숫자 (a-zA-Z0-9)만 포함되어 있는지 확인합니다.<br/><br/>locale은 ['ar', 'ar-AE', 'ar-BH', 'ar-DZ', 'ar-EG', 'ar-IQ', 'ar-JO', 'ar-KW', 'ar-LB', 'ar-LY', 'ar-MA', 'ar-QA', 'ar-QM', 'ar-SA', 'ar-SD', 'ar-SY', 'ar-TN', 'ar-YE', 'bn', 'bg-BG', 'cs-CZ', 'da-DK', 'de-DE', 'el-GR', 'en-AU', 'en-GB', 'en-HK', 'en-IN', 'en-NZ', 'en-US', 'en-ZA', 'en-ZM', 'eo', 'es-ES', 'fa-IR', 'fi-FI', 'fr-CA', 'fr-FR', 'he', 'hi-IN', 'hu-HU', 'it-IT', 'kk-KZ', 'ko-KR', 'ja-JP','ku-IQ', 'nb-NO', 'nl-NL', 'nn-NO', 'pl-PL', 'pt-BR', 'pt-PT', 'ru-RU', 'si-LK', 'sl-SI', 'sk-SK', 'sr-RS', 'sr-RS@latin', 'sv-SE', 'th-TH', 'tr-TR', 'uk-UA'] 중 하나이며 기본값은 en-US입니다. locale 목록은 validator.isAlphanumericLocales입니다. options는 선택적 객체로, ignore 키가 있을 수 있습니다. ignore는 문자열 또는 무시할 문자의 정규 표현식일 수 있습니다. 예: " -"는 공백과 -를 무시합니다.
`isAscii(str)` | 문자열에 ASCII 문자만 포함되어 있는지 확인합니다.
`isBase32(str [, options])` | 문자열이 base32로 인코딩되었는지 확인합니다. options는 선택적이며 기본값은 { crockford: false }입니다.<br/>crockford가 true일 경우, [Crockford의 base32 대안][Crockford Base32]을 사용하여 주어진 base32 인코딩 문자열을 검사합니다.
`isBase58(str)` | 문자열이 base58로 인코딩되었는지 확인합니다.
`isBase64(str [, options])` | 문자열이 base64로 인코딩되었는지 확인합니다. options는 선택적이며 기본값은 { urlSafe: false }입니다.<br/>urlSafe가 true일 경우, 문자열이 [URL 안전][Base64 URL Safe] base64로 인코딩되었는지 확인합니다.
`isBefore(str [, date])` | 문자열이 지정된 날짜 이전의 날짜인지 확인합니다.
`isBIC(str)` | 문자열이 BIC (은행 식별 코드) 또는 SWIFT 코드인지 확인합니다.
`isBoolean(str [, options])` | 문자열이 boolean인지 확인합니다.<br/>options는 기본값이 { loose: false }인 객체입니다. loose가 false로 설정된 경우, 벨리데이터는 ['true', 'false', '0', '1']과 엄격히 일치합니다. loose가 true로 설정된 경우, 벨리데이터는 'yes', 'no'를 포함한 유효한 boolean 문자열과 대소문자를 구분하지 않는 문자열과도 일치합니다. (예: ['true', 'True', 'TRUE']).
`isBtcAddress(str)` | 문자열이 유효한 BTC 주소인지 확인합니다.
`isByteLength(str [, options])` | 문자열의 길이(UTF-8 바이트 단위)가 범위 내에 있는지 확인합니다.<br/><br/>options는 기본값이 { min: 0, max: undefined }인 객체입니다.
`isCreditCard(str [, options])` | 문자열이 신용카드 번호인지 확인합니다.<br/><br/> options는 선택적 객체로, provider 키를 가질 수 있습니다. provider의 값은 문자열이어야 하며, 신용카드를 발급하는 회사를 정의합니다. 유효한 값으로는 ['amex', 'dinersclub', 'discover', 'jcb', 'mastercard', 'unionpay', 'visa']가 있으며, 공백일 경우 모든 발급사를 검사합니다.
`isCurrency(str [, options])` | 문자열이 유효한 통화 금액인지 확인합니다.<br/><br/>options는 기본값이 { symbol: '$', require_symbol: false, allow_space_after_symbol: false, symbol_after_digits: false, allow_negatives: true, parens_for_negatives: false, negative_sign_before_digits: false, negative_sign_after_digits: false, allow_negative_sign_placeholder: false, thousands_separator: ',', decimal_separator: '.', allow_decimal: true, require_decimal: false, digits_after_decimal: [2], allow_space_after_digits: false }인 객체입니다.<br/>참고: 배열 digits_after_decimal은 허용되는 정확한 자릿수로 채워지며 범위가 아니라 예를 들어 1~3 범위는 [1, 2, 3]으로 표시됩니다.
`isDataURI(str)` | 문자열이 [데이터 URI 형식][Data URI Format]인지 확인합니다.
`isDate(str [, options])` | 문자열이 유효한 날짜인지 확인합니다. 예: [2002-07-15, new Date()].<br/><br/> options는 객체로, format, strictMode, delimiters 키를 포함할 수 있습니다.<br/><br/>format은 문자열로 기본값은 YYYY/MM/DD입니다.<br/><br/>strictMode는 boolean으로 기본값은 false입니다. strictMode가 true로 설정되면 format과 다른 문자열은 거부됩니다.<br/>
`isDecimal(str [, options])` | 문자열이 10진수 숫자인지 확인합니다.<br/><br/>options는 {force_decimal: false, decimal_digits: '1,', locale: 'en-US'}인 객체입니다.
`isDivisibleBy(str, number)` | 문자열이 number로 나누어 떨어지는지 확인합니다.
`isEAN(str)` | 문자열이 EAN(국제 상품 코드)인지 확인합니다.
`isEmail(str [, options])` | 문자열이 이메일 형식인지 확인합니다.<br/><br/>options는 {allow_display_name: false, require_display_name: false, allow_utf8_local_part: true, require_tld: true, allow_ip_domain: false, domain_specific_validation: false, blacklisted_chars: '', host_blacklist: [], allow_literal: false}인 객체입니다.
`isEmpty(str [, options])` | 문자열이 비어 있는지 확인합니다.<br/><br/>options는 {ignore_whitespace: false}인 객체입니다.
`isEthereumAddress(str)` | 문자열이 이더리움 주소인지 확인합니다.
`isFQDN(str [, options])` | 문자열이 완전한 도메인 이름인지 확인합니다.<br/><br/>options는 {require_tld: true, allow_underscores: false, allow_trailing_dot: false, allow_numeric_tld: false}인 객체입니다.
`isFloat(str [, options])` | 문자열이 부동 소수점 숫자인지 확인합니다.<br/><br/>options는 {min: -Infinity, max: Infinity, gt: -Infinity, lt: Infinity, locale: 'en-US'}인 객체입니다.
`isFullWidth(str)` | 문자열이 풀 와이드(한글과 같이 너비가 큰 문자)인지 확인합니다.
`isHalfWidth(str)` | 문자열이 하프 와이드(알파벳과 같이 너비가 작은 문자)인지 확인합니다.
`isHash(str, algorithm)` | 문자열이 특정 해시 알고리즘의 해시인지 확인합니다.<br/><br/>algorithm은 'md4', 'md5', 'sha1', 'sha256', 'sha384', 'sha512', 'ripemd128', 'ripemd160', 'tiger128', 'tiger160', 'tiger192', 'crc32', 'crc32b' 중 하나입니다.
`isHexColor(str)` | 문자열이 유효한 16진수 색상인지 확인합니다.
`isHexadecimal(str)` | 문자열이 16진수인지 확인합니다.
`isHSL(str)` | 문자열이 HSL 색상 형식인지 확인합니다.
`isIBAN(str)` | 문자열이 IBAN(국제 은행 계좌 번호)인지 확인합니다.
`isIdentityCard(str [, locale])` | 문자열이 유효한 신분증 번호인지 확인합니다.<br/><br/>locale은 'any' 또는 'ES', 'IN', 'IT', 'NO', 'IR', 'MZ', 'TH', 'zh-CN', 'zh-TW' 중 하나입니다.
`isIMEI(str [, options])` | 문자열이 유효한 IMEI(국제 모바일 장비 식별번호)인지 확인합니다.<br/><br/>options는 {allow_hyphens: false}인 객체입니다.
`isIP(str [, version])` | 문자열이 유효한 IP 주소인지 확인합니다.<br/><br/>version은 '4' 또는 '6' 중 하나이며 기본값은 '4'입니다.
`isIPRange(str [, version])` | 문자열이 IP 주소 범위인지 확인합니다.<br/><br/>version은 '4' 또는 '6' 중 하나이며 기본값은 '4'입니다.
`isISBN(str [, version])` | 문자열이 유효한 ISBN인지 확인합니다.<br/><br/>version은 '10' 또는 '13' 중 하나이며 기본값은 '10'입니다.
`isISSN(str [, options])` | 문자열이 유효한 ISSN인지 확인합니다.<br/><br/>options는 {case_sensitive: false, require_hyphen: false}인 객체입니다.
`isISIN(str)` | 문자열이 유효한 ISIN(국제 증권 식별 번호)인지 확인합니다.
`isISO8601(str [, options])` | 문자열이 ISO 8601 날짜 형식인지 확인합니다.<br/><br/>options는 {strict: false, strictSeparator: false}인 객체입니다.
`isISO31661Alpha2(str)` | 문자열이 ISO 3166-1 Alpha-2 국가 코드인지 확인합니다.
`isISO31661Alpha3(str)` | 문자열이 ISO 3166-1 Alpha-3 국가 코드인지 확인합니다.
`isISO4217(str)` | 문자열이 ISO 4217 통화 코드인지 확인합니다.
`isISRC(str)` | 문자열이 유효한 ISRC(국제 표준 녹음 코드)인지 확인합니다.
`isISSN(str [, options])` | 문자열이 유효한 ISSN인지 확인합니다.<br/><br/>options는 {case_sensitive: false, require_hyphen: false}인 객체입니다.
`isJSON(str [, options])` | 문자열이 유효한 JSON인지 확인합니다.
`isJWT(str)` | 문자열이 유효한 JWT(JSON Web Token)인지 확인합니다.
`isLatLong(str [, options])` | 문자열이 유효한 위도 및 경도 좌표인지 확인합니다.
`isLength(str [, options])` | 문자열의 길이가 주어진 범위 내에 있는지 확인합니다.<br/><br/>options는 {min: 0, max: undefined}인 객체입니다.
`isLicensePlate(str [, locale])` | 문자열이 유효한 차량 번호판인지 확인합니다.<br/><br/>locale은 'cs-CZ', 'de-DE', 'de-LI', 'en-GB', 'en-HK', 'en-IE', 'en-IN', 'en-US', 'fi-FI', 'fr-FR', 'pt-BR' 중 하나입니다.
`isLocale(str)` | 문자열이 유효한 로케일인지 확인합니다.
`isLowercase(str)` | 문자열이 모두 소문자인지 확인합니다.
`isLuhnNumber(str)` | 문자열이 Luhn 알고리즘에 따라 유효한 숫자인지 확인합니다.
`isMACAddress(str [, options])` | 문자열이 유효한 MAC 주소인지 확인합니다.<br/><br/>options는 {eui: false}인 객체입니다.
`isMagnetURI(str)` | 문자열이 유효한 Magnet URI인지 확인합니다.
`isMD5(str)` | 문자열이 유효한 MD5 해시인지 확인합니다.
`isMimeType(str)` | 문자열이 유효한 MIME 타입인지 확인합니다.
`isMobilePhone(str [, locale, options])` | 문자열이 유효한 모바일 전화번호인지 확인합니다.<br/><br/>locale은 'any' 또는 특정 지역 코드를 의미합니다. 지역 코드는 다양한 국가 코드 문자열 중 하나일 수 있습니다. options는 {strictMode: false, format: 'default', locale: 'en-US'}인 객체입니다.
`isMongoId(str)` | 문자열이 유효한 MongoDB ObjectId인지 확인합니다.
`isMultibyte(str)` | 문자열이 여러 바이트로 구성된 문자(예: 한글, 일본어 등)를 포함하는지 확인합니다.
`isNumeric(str [, options])` | 문자열이 숫자로만 구성되어 있는지 확인합니다.<br/><br/>options는 {no_symbols: false, locale: 'en-US'}인 객체입니다.
`isOctal(str)` | 문자열이 8진수로 구성되어 있는지 확인합니다.
`isPassportNumber(str [, countryCode])` | 문자열이 유효한 여권 번호인지 확인합니다.<br/><br/>countryCode는 특정 국가 코드를 의미합니다.
`isPort(str)` | 문자열이 유효한 포트 번호인지 확인합니다.
`isPostalCode(str [, locale])` | 문자열이 유효한 우편번호인지 확인합니다.<br/><br/>locale은 'any' 또는 특정 지역 코드를 의미합니다.
`isRFC3339(str)` | 문자열이 유효한 RFC 3339 날짜 형식인지 확인합니다.
`isRgbColor(str [, options])` | 문자열이 유효한 RGB 색상 형식인지 확인합니다.<br/><br/>options는 {includePercentValues: false}인 객체입니다.
`isSemVer(str)` | 문자열이 유효한 SemVer(버전 번호)인지 확인합니다.
`isSlug(str)` | 문자열이 유효한 슬러그(slug)인지 확인합니다.
`isStrongPassword(str [, options])` | 문자열이 강력한 비밀번호인지 확인합니다.<br/><br/>options는 {minLength: 8, minLowercase: 1, minUppercase: 1, minNumbers: 1, minSymbols: 1, returnScore: false, pointsPerUnique: 1, pointsPerRepeat: 0.5, pointsForContainingLower: 10, pointsForContainingUpper: 10, pointsForContainingNumber: 10, pointsForContainingSymbol: 10}인 객체입니다.
`isSurrogatePair(str)` | 문자열이 서로게이트 페어(surrogate pair)를 포함하는지 확인합니다.
`isTaxID(str [, locale])` | 문자열이 유효한 세금 식별 번호(Tax Identification Number)인지 확인합니다.<br/><br/>locale은 'any' 또는 특정 국가 코드를 의미합니다.
`isTime(str [, options])` | 문자열이 유효한 시간 형식인지 확인합니다.<br/><br/>options는 {format: 'HH:mm:ss', loose: false}인 객체입니다.
`isURL(str [, options])` | 문자열이 유효한 URL인지 확인합니다.<br/><br/>options는 {protocols: ['http', 'https', 'ftp'], require_tld: true, require_protocol: false, require_host: true, require_port: false, require_valid_protocol: true, allow_underscores: false, allow_trailing_dot: false, allow_protocol_relative_urls: false, allow_fragments: true, allow_query_components: true, disallow_auth: false, host_whitelist: [], host_blacklist: [], include_hosts: []}인 객체입니다.
`isUUID(str [, version])` | 문자열이 유효한 UUID인지 확인합니다.<br/><br/>version은 '3', '4', '5' 중 하나입니다.
`isUppercase(str)` | 문자열이 모두 대문자인지 확인합니다.
`isVariableWidth(str)` | 문자열이 서로 다른 너비의 문자를 포함하는지 확인합니다.
`isVAT(str [, countryCode])` | 문자열이 유효한 VAT(부가가치세) 번호인지 확인합니다.<br/><br/>countryCode는 특정 국가 코드를 의미합니다.
`isWhitelisted(str, chars)` | 문자열이 지정된 문자 집합에 포함되는지 확인합니다.
`matches(str, pattern [, modifiers])` | 문자열이 지정된 정규 표현식 패턴과 일치하는지 확인합니다.<br/><br/>pattern은 정규 표현식 또는 문자열입니다. modifiers는 선택적인 정규 표현식 플래그입니다.

### Built-in sanitizers

올바른 유효성검사(validation)가 가능하도록 입력값 정제를 합니다.\
유효성 검사를 하기 전 문자열에 blank가 있는지 `trim()`으로 확인하는 등의 과정을 말합니다.

또한 사용자가 전달한 문자열의 양쪽에 공백 문자가 없도록 하거나 이메일 주소를 소문자로 바꾸는 등 전달받는 데이터가 유효할 뿐만 아니라 공백 문자 없이 균일화된 방식으로 저장되도록 여러 작업을 할 수 있어요.\
sanitizing은 실제 값까지 영향을 줄 수 있으니 주의해서 사용해야합니다.

#### .customSanitizer()

```tsx
customSanitizer(sanitizer: (value, { req, location, path }) => any): ValidationChain
```

검증 체인에 사용자 정의 sanitizer 함수를 추가합니다.\
함수가 반환하는 값은 필드의 새로운 값이 됩니다.

```tsx
app.post('/object/:id', param('id').customSanitizer((value, { req }) => {
  // 이 앱에서는 사용자가 MongoDB 스타일의 객체 ID를 가질 경우, 그렇지 않으면 숫자를 반환합니다.
  return req.query.type === 'user' ? ObjectId(value) : Number(value);
})), (req, res) => {
  // 요청 처리
});
```

#### .default()

```tsx
default(defaultValue: any): ValidationChain
```

필드의 값이 빈 문자열, `null`, `undefined` 또는 `NaN`인 경우 기본값으로 대체합니다.

```tsx
app.post('/', body('username').default('foo'), (req, res, next) => {
  // 'bar'     => 'bar'
  // ''        => 'foo'
  // undefined => 'foo'
  // null      => 'foo'
  // NaN       => 'foo'
});
```

#### .replace()

```tsx
replace(valuesFrom: any[], valueTo: any): ValidationChain
```

현재 값이 `valuesFrom`에 포함될 때 필드의 값을 `valueTo`로 대체합니다.

```tsx
app.post('/', body('username').replace(['bar', 'BAR'], 'foo'), (req, res, next) => {
  // 'bar_' => 'bar_'
  // 'bar'  => 'foo'
  // 'BAR'  => 'foo'
  console.log(req.body.username);
});
```

#### .toArray()

```tsx
toArray(): ValidationChain
```

값을 배열로 변환합니다. 이미 배열인 경우 아무 작업도 하지 않습니다.\
`undefined` 값은 빈 배열이 됩니다.

#### .toLowerCase()

```tsx
toLowerCase(): ValidationChain
```

값을 소문자로 변환합니다.\
값이 문자열이 아닌 경우 아무 작업도 하지 않습니다.

#### .toUpperCase()

```tsx
toUpperCase(): ValidationChain
```

값을 대문자로 변환합니다.\
값이 문자열이 아닌 경우 아무 작업도 하지 않습니다.

#### Standard sanitizers

Sanitizer | Description
:-: | :-:
`blacklist(input, chars`) | 입력 문자열에서 지정된 문자를 제거합니다.
`escape(input)` | 입력 문자열에서 HTML 엔티티를 이스케이프합니다.
`ltrim(input [, chars])` | 입력 문자열의 왼쪽 끝에서 지정된 문자를 제거합니다.<br/><br/>chars는 제거할 문자의 집합입니다. 기본값은 공백입니다.
`normalizeEmail(email [, options])` | 이메일 주소를 정규화합니다.<br/><br/>options는 {all_lowercase: true, gmail_lowercase: true, gmail_remove_dots: true, gmail_remove_subaddress: true, gmail_convert_googlemaildotcom: true, outlookdotcom_lowercase: true, outlookdotcom_remove_subaddress: true, yahoo_lowercase: true, yahoo_remove_subaddress: true, icloud_lowercase: true, icloud_remove_subaddress: true}인 객체입니다.
`rtrim(input [, chars])` | 입력 문자열의 오른쪽 끝에서 지정된 문자를 제거합니다.<br/><br/>chars는 제거할 문자의 집합입니다. 기본값은 공백입니다.
`stripLow(input [, keep_new_lines])` | 입력 문자열에서 ASCII 제어 문자를 제거합니다.<br/><br/>keep_new_lines가 true이면 새 줄 문자를 유지합니다.
`toBoolean(input [, strict])` | 입력 값을 부울 값으로 변환합니다.<br/><br/>strict가 true이면 엄격한 변환을 수행합니다(예: "1" 및 "0"이 아닌 경우 false).
`toDate(input)` | 입력 값을 날짜 객체로 변환합니다.
`toFloat(input)` | 입력 값을 부동 소수점 숫자로 변환합니다.
`toInt(input [, radix])` | 입력 값을 정수로 변환합니다.<br/><br/>radix는 진수를 의미하며 기본값은 10진수입니다.
`trim(input [, chars])` | 입력 문자열의 양쪽 끝에서 지정된 문자를 제거합니다.<br/><br/>chars는 제거할 문자의 집합입니다. 기본값은 공백입니다.
`unescape(input)` | 입력 문자열에서 HTML 엔티티를 디코딩합니다.
`whitelist(input, chars)` | 입력 문자열에서 지정된 문자만 남기고 나머지 문자를 제거합니다.

### Modifiers

#### .bail()

```tsx
bail(options?: { level: 'chain' | 'request' }): ValidationChain
```

매개변수:

이름 | 설명
:-: | :-:
`options.level` | 검증 체인을 중지해야 하는 수준을 지정합니다. 기본값은 `chain`입니다.

이전의 어떤 검증기라도 실패하면 검증 체인의 실행을 중지합니다.

이는 데이터베이스나 외부 API를 사용하는 사용자 정의 검증기를 실행하기 전에 실패할 것이 확실한 경우 유용합니다.

`.bail()`은 원하는 경우 동일한 검증 체인에서 여러 번 사용할 수 있습니다:

```tsx
코드 복사
body('username')
  .isEmail()
  // 이메일 형식이 아니면 여기서 중지
  .bail()
  .custom(checkDenylistDomain)
  // 도메인이 허용되지 않으면 이미 존재하는지 확인하지 않음
  .bail()
  .custom(checkEmailExists);
```

`level` 옵션이 `request`로 설정된 경우, 현재 요청에 대한 추가 검증 체인도 실행되지 않습니다. 예를 들어:

```tsx
app.get(
  '/search',
  query('query').notEmpty().bail({ level: 'request' }),
  // `query`가 비어 있으면 다음 검증 체인들이 실행되지 않습니다:
  query('query_type').isIn(['user', 'posts']),
  query('num_results').isInt(),
  (req, res) => {
    // 요청 처리
  },
);
```

#### 주의

`oneOf()` 및 `checkExact()`와 같은 함수는 요청 수준의 bail을 사용할 때 느려질 수 있습니다.\
이는 병렬로 실행되는 검증 체인이 순차적으로 실행되어야 하기 때문입니다.

#### .if()

```tsx
if(condition: CustomValidator | ContextRunner): ValidationChain
```

필드에 대한 검증 체인이 계속 실행될지 여부에 대한 조건을 추가합니다.

조건은 사용자 정의 검증기 또는 `ContextRunner` 인스턴스일 수 있습니다.

```tsx
body('newPassword')
  // 이전 비밀번호가 제공된 경우에만 유효성 검사
  .if((value, { req }) => req.body.oldPassword)
  // 또는 검증 체인을 사용
  .if(body('oldPassword').notEmpty())
  // `oldPassword`가 제공된 경우에만 새 비밀번호의 길이를 확인
  .isLength({ min: 6 });
```

#### .not()

```tsx
not(): ValidationChain
```

체인 내의 다음 검증기의 결과를 부정합니다.

```tsx
check('weekday').not().isIn(['sunday', 'saturday']);
```

#### .optional()

```tsx
optional(options?: boolean | {
  values?: 'undefined' | 'null' | 'falsy',
  nullable?: boolean,
  checkFalsy?: boolean,
}): ValidationChain
```

현재 검증 체인을 선택 사항으로 표시합니다. 선택 사항 필드는 실패 대신 값에 따라 검증을 건너뜁니다.

어떤 값이 선택 사항인지 여부는 `options.values`에 따라 다릅니다.\
기본값은 `undefined`입니다:

`options.values` | 동작
:-: | :-:
`undefined` | `undefined` 값은 선택 사항입니다.
`null` | `undefined` 및 `null` 값은 선택 사항입니다.
`falsy` | Falsy 값(빈 문자열, `0`, `false`, `null` 및 `undefined` 값)은 선택 사항입니다.

`options.nullable` 및 `options.checkFalsy`는 더 이상 사용되지 않는 옵션입니다.\
이는 각각 `options.values`를 `null` 또는 `falsy`로 설정하는 것과 동일합니다.

`options`이 `false`인 경우 필드는 선택 사항이 아닙니다.

#### 정보

검증기 및 sanitizers와 달리 `.optional()`은 위치에 따라 다르지 않습니다.\
체인 내 어디에 있든 값이 해석되는 방식에 영향을 줍니다.

예를 들어, 다음과 같은 경우 차이가 없습니다:

```tsx
body('json_string').isLength({ max: 100 }).isJSON().optional().
```

또는

```tsx
body('json_string').optional().isLength({ max: 100 }).isJSON().
```

#### .withMessage()

```tsx
withMessage(message: any): ValidationChain
```

이전 검증기에 사용될 오류 메시지를 설정합니다.

### 실제 사용 예시

`express-validator/check`의 `body`는 `req.body`에서 오는 값만을 검사합니다.

```tsx
// routes

const { body } = require('express-validator/check');

router.put(
  '/signup',
  [
    body('email') // `req.body`의 `email` 필드에 대해
      .isEmail() // 이메일 주소 유효성 검사
      .withMessage('Please enter a valid email.') // 이메일 주소 유효성 검사가 맞지 않으면 'Please enter a valid email.'라는 오류 메시지를 반환
      .custom((value, { req }) => {
        return User.findOne({ email: value }).then(userDoc => {
          if (userDoc) {
            return Promise.reject('E-Mail address already exists!');
          }
        });
      })
      .normalizeEmail(),
    body('password')
      .trim()
      .isLength({ min: 5 }),
    body('name')
      .trim()
      .not()
      .isEmpty()
  ],
  authController.signup
);
```

### 재사용 가능한 Validation Chain

Validation Chain은 변경 가능합니다.\
따라서 한 체인을 여러 번 호출하면 원래 체인 객체가 업데이트됩니다.\
재사용하려면 "함수"를 사용하여 반환하는 것이 좋습니다.

```tsx
const createEmailChain = () => body('email').isEmail();

app.post('/login', createEmailChain(), handleLoginRoute);
app.post('/signup', createEmailChain().custom(checkEmailNotInUse), handleSignupRoute);
```

함수가 아닌 값을 사용하면 버그가 발생할 수 있습니다.

```tsx
const baseEmailChain = body('email').isEmail();

app.post('/login', baseEmailChain, handleLoginRoute);
app.post('/signup', baseEmailChain.custom(checkEmailNotInUse), handleSignupRoute);
```

### 고급 기능

#### Wildcards

배열의 모든 항목이나 객체의 모든 키에 동일한 규칙을 적용하고 싶을 때 `*`는 Wildcard로 사용됩니다.

```json
{
  "addresses": {
    "home": { "number": 35 },
    "work": { "number": 501 }
  },
  "siblings": [{ "name": "Maria von Validator" }, { "name": "Checky McCheckFace" }]
}
```

`addresses`의 `number`가 모두 정수인지 확인하고 `siblings`의 `name`이 설정되어 있는지 확인하려면 다음과 같은 유효성 검사 체인을 사용할 수 있습니다.

```tsx
app.post(
  '/update-user',
  body('addresses.*.number').isInt(),
  body('siblings.*.name').notEmpty(),
  (req, res) => {
    // 요청 처리
  },
);
```

#### Globstars

글로브스타(**)는 와일드카드를 무한히 깊은 레벨로 확장합니다.\
중첩된 필드의 깊이가 불확실할 때 유용합니다.

```json
{
  "name": "Team name",
  "teams": [{ "name": "Subteam name", "teams": [] }]
}
```

이 구조에서 팀은 다른 팀 안에, 또 다른 팀 안에 있을 수 있습니다.

```tsx
app.put('/update-chart', body('**.name').notEmpty(), (req, res) => {
  // 요청 처리
});
```

글로브스타(`**`)를 사용하여 요청에서 깊이가 얼마든지 깊은 필드를 모두 선택할 수 있습니다.\
다음 예제는 `req.body`의 루트에 있는 필드를 포함하여 모든 `name` 필드가 설정되어 있는지 확인합니다.

### Customizing express-validator

#### Custom Validators

필드 값과 해당 값에 대한 정보를 받아 유효성을 확인하는 함수입니다.\
유효한 경우 truthy value를 반환하고, 유효하지 않은 경우 falsy value를 반환해야 합니다.\
비동기 Custom Validators는 프로미스를 반환할 수 있으며, 이 프로미스가 해결되면 필드가 유효한 것으로 간주됩니다.

```tsx
app.post(
  '/create-user',
  body('email').custom(async value => {
    // 이메일이 사용 중인지 확인
    const user = await UserCollection.findUserByEmail(value);
    if (user) {
      throw new Error('E-mail already in use');
    }
  }),
  (req, res) => {
    // 요청 처리
  },
);
```

```tsx
app.post(
  '/create-user',
  body('password').isLength({ min: 5 }),
  body('passwordConfirmation').custom((value, { req }) => {
    return value === req.body.password;
  }),
  (req, res) => {
    // 요청 처리
  },
);
```

#### Custom Sanitizers

필드 값을 변환하는 함수입니다.\
반환된 값이 필드 값으로 설정됩니다.\
비동기 Custom sanitizers도 프로미스를 반환할 수 있습니다.

```tsx
import { param } from 'express-validator';
import { ObjectId } from 'mongodb';

app.post(
  '/user/:id',
  // ID를 MongoDB ObjectId 형식으로 변환
  param('id').customSanitizer(value => ObjectId(value)),
  (req, res) => {
    // req.params.id는 이제 ObjectId입니다.
  },
);
```

#### Error Messages

필드 값이 유효하지 않은 경우 오류 메시지가 기록됩니다.\
기본 오류 메시지는 `"Invalid value"`입니다.

Validator-level message는 특정 검증기가 실패할 때 적용됩니다.\
`.withMessage()` 메서드를 사용하여 설정할 수 있습니다.

```tsx
body('email').isEmail().withMessage('Not a valid e-mail address');
```

Custom Validator가 예외를 던지면, 던진 값이 오류 메시지로 사용됩니다.\
`.withMessage()`로 메시지를 지정하면 Custom Validator에서 던진 값보다 우선합니다.

```tsx
body('email')
  .isEmail()
  .custom(async value => {
    const existingUser = await Users.findByEmail(value);
    if (existingUser) {
      // 아래 메시지가 오류 메시지로 사용됩니다.
      throw new Error('A user already exists with this e-mail address');
    }
  });
```

Field-level message는 검증 체인을 생성할 때 설정됩니다.\
검증기가 오류 메시지를 덮어쓰지 않으면 기본 메시지로 사용됩니다.

```tsx
body('json_string', 'Invalid json_string')
  // isJSON에 대한 메시지가 지정되지 않았으므로 기본 메시지 "Invalid json_string" 사용
  .isJSON()
  .isLength({ max: 100 })
  // isLength 실패 시 메시지 덮어쓰기
  .withMessage('Max length is 100 bytes');
```

일부 express-validator 함수는 다른 오류 유형을 생성할 수 있으며, 다른 방식으로 오류 메시지를 지정할 수 있습니다.

- `checkExact()`
- `oneOf()`

#### ExpressValidator 클래스

특정 사용자 정의를 재사용하는 유용한 방법은 ExpressValidator 클래스를 사용하는 것입니다.\
이 클래스는 사용자 정의 검증기, 정리기 또는 오류 형식을 지정할 때 사용할 수 있습니다.

```tsx
import { ExpressValidator } from 'express-validator';

const { body, validationResult } = new ExpressValidator(
  {
    isPostID: async value => {
      // 값이 게시물 ID 형식과 일치하는지 확인
    },
  },
  {
    muteOffensiveWords: value => {
      // 공격적인 단어를 ***로 대체
    },
  },
);

app.post(
  '/forum/:post/comment',
  param('post').isPostID(),
  body('comment').muteOffensiveWords(),
  (req, res) => {
    const result = validationResult(req);
    // 새 게시물 유효성 검사 결과 처리
  },
);
```

### 수동으로 유효성 검사 실행하기 (Manually running validations)

직접 미들웨어나 라우트 핸들러에서 이 유효성 검사를 실행할 수도 있습니다.\
`ValidationChain`, `checkExact()`, `checkSchema()`, `oneOf`와 같은 express-validator 함수들이 구현하는 `ContextRunner` 인터페이스를 사용하여 가능합니다.

```tsx
const express = require('express');
const { validationResult } = require('express-validator');

// 여러 라우트에서 재사용할 수 있습니다.
const validate = validations => {
  return async (req, res, next) => {
    // 순차적으로 처리하며 하나의 검증 체인이 실패하면 중단합니다.
    for (const validation of validations) {
      const result = await validation.run(req);
      if (!result.isEmpty()) {
        return res.status(400).json({ errors: result.array() });
      }
    }

    next();
  };
};

app.post('/signup', validate([
  body('email').isEmail(),
  body('password').isLength({ min: 6 })
]), async (req, res, next) => {
  // 요청이 반드시 유효성 검사 오류가 없음이 보장됩니다.
  const user = await User.create({ ... });
});
```

조건부로 유효성 검사하기

```tsx
import { body, matchedData } from 'express-validator';
app.post(
  '/update-settings',
  body('email').isEmail(),
  body('password').optional().isLength({ min: 6 }),
  async (req, res, next) => {
    // 비밀번호가 제공된 경우 반드시 확인 비밀번호도 제공되어야 합니다.
    const { password } = matchedData(req);
    if (password) {
      await body('passwordConfirmation')
        .equals(password)
        .withMessage('비밀번호가 일치하지 않습니다')
        .run(req);
    }

    // 유효성 검사 오류를 확인하고 사용자 설정을 업데이트합니다.
  },
);
```

### Schema validation

객체 기반으로 `validation`과 `sanitization`을 수행하는 것을 말합니다.\
객체의 `key`는 `validation` 또는 `sanitizaion`을 수행할 대상입니다.\
객체의 `value`는 대상에 대해 수행할 규칙(`schema`)입니다.

이렇게 만든 스키마를 checkSchema() 함수에 넣으면, 스키마를 해석해 validation chain을 만들어준다.

#### 스키마 적용 전

```tsx
router.post('/', /* 결제 하기 */
  [
    body('items').isArray().withMessage('items need to be array'),
    body('items.*.bookId').isNumeric().withMessage('bookId need to be number'),
    body('items.*.count').isNumeric().withMessage('count need to be number'),
    body('delivery.address').notEmpty().withMessage('No address'),
    body('delivery.receiver').notEmpty().withMessage('No receiver'),
    body('delivery.contact').isMobilePhone('any').withMessage('Contact format error'),
    body('delivery.contact').matches('^\\d{3}-\\d{3,4}-\\d{4}$', 'g').withMessage('Contact format error'),
    body('delivery.totalPrice').isNumeric().withMessage('Price need to be number'),
    util.validate,
    util.verifyToken,
  ]
```

#### 스키마 적용 후

```tsx
const paymentSchema = {
  items: {
    isArray: true,
    errorMessage: 'Items should be array'
  },
  'items.*.bookId': {
    isNumeric: true,
    errorMessage: 'BookId should be number'
  },
  'items.*.count': {
    isNumeric: true,
    errorMessage: 'Count should be number'
  },
  'delivery.address': {
    notEmpty: true,
    errorMessage: 'No Address'
  },
  'delivery.receiver': {
    notEmpty: true,
    errorMessage: 'No receiver'
  },
  'delivery.contact': {
    matches: {
      options: '^\\d{3}-\\d{3,4}-\\d{4}$',
      errorMessage: 'PhoneNumber format unmatch'
    }
  },
  totalPrice: {
    isNumeric: true,
    errorMessage: 'TotalPrice should be number'
  },
}

router.post('/', /* 결제 하기 */
  [
    checkSchema(paymentSchema),
    util.validate,
    util.verifyToken,
  ],
```

## 검증 결과(validationResult)

유효성 검증을 하면서 생기는 에러 메시지는 `validationResult(req)`로 받을 수 있습니다.

`.array()`는 errors에 있는 에러 객체들을 배열 형태로 반환해 주는 역할을 합니다.

```tsx
// controllers

  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    const error = new Error('Validation failed.');
    error.statusCode = 422;
    error.data = errors.array();
    throw error;
  }
```

### errors 내용 예시

```jsx
{
  "errors": [
    {
      "location": "query",
      "msg": "Invalid value",
      "path": "person",
      "type": "field"
    }
  ]
}
```

위의 내용을 보면 다음과 같은 정보를 알 수 있습니다.

- 요청에 정확히 한 개의 오류가 발생했다.
- 쿼리 문자열에 위치해 있다 (`location: "query"`).
- 오류 메시지는 `"Invalid value"` 이다.
- 이 필드는 `person` 이다.
- 오류가 필드에 있다 (`type: "field"`).

## 참고 자료

- [express-validator](https://express-validator.github.io/docs/guides/getting-started)
- [[Node.js] 유효성 검사(Validation), express-validator 사용법](https://developer-holychan.tistory.com/entry/nodejs-validation-and-sanitization)
- [[Express] Express-validator 스키마](https://velog.io/@anna951/Express-validator-%EC%8A%A4%ED%82%A4%EB%A7%88)
