# generateInternalStream(options)

내부 스트림 구현을 사용하여 zip 파일 전체를 생성합니다.  
고급 사용자용이며, JSZip 내부에서 사용하는 스트림 로직에 접근할 수 있습니다.

**Returns** : [StreamHelper]({{site.baseurl}}/documentation/api_streamhelper.html}) 인스턴스를 반환합니다.

**Since**: v3.0.0

## Arguments

| name    | type   | default | description                                                                                                                |
| ------- | ------ | ------- | -------------------------------------------------------------------------------------------------------------------------- |
| options | object |         | zip 파일 생성을 위한 설정. [`generateAsync()`의 옵션]({{site.baseurl}}/documentation/api_jszip/generate_async.html)과 동일 |

**Metadata** : [generateAsync()의 onUpdate 콜백]({{site.baseurl}}/documentation/api_jszip/generate_async.html#onupdate-callback) 참조

## Examples

```js
zip
  .generateInternalStream({ type: "uint8array" })
  .accumulate()
  .then(function (data) {
    // data는 생성된 zip 파일 전체를 포함하는 Uint8Array
  });
```
