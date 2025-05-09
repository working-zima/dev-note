# JSZip.loadAsync(data [, options])

이 메서드는 다음 코드의 단축 표현입니다:

```js
var zip = new JSZip();
zip.loadAsync(data, options);
```

자세한 설명은 [loadAsync 문서]({{site.baseurl}}/documentation/api_jszip/load_async.html)를 참고하세요.

## Examples

```js
dataAsPromise.then(JSZip.loadAsync).then(function (zip) {
  // zip 처리
});
```

다음 코드와 동일합니다:

```js
JSZip.loadAsync(dataAsPromise).then(function (zip) {
  // zip 처리
});
```
