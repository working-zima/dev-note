# JSZip.external

JSZip는 플랫폼에 따라 존재하지 않을 수 있는 객체들을 사용하는데, 이 경우 자체적으로 shim(대체 구현)을 사용합니다.\
상황에 따라 이 객체들에 접근하거나 교체하는 것이 유용할 수 있습니다.

`JSZip.external`은 다음 속성을 포함합니다:

- `Promise` : [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise) 구현체  
  사용 가능한 경우에는 전역 객체(global object)를 우선 사용합니다.

## Example

```js
// Bluebird를 대신 사용
JSZip.external.Promise = Bluebird;

// 기본 내장 Promise 객체를 사용
JSZip.external.Promise = Promise;
```
