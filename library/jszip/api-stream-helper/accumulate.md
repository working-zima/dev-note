# accumulate([updateCallback])

스트림 전체를 읽어 완성된 데이터를 반환합니다.\
비동기적으로 작동하며, 중간 처리 상태를 확인할 수 있는 콜백 함수도 제공합니다.

**Returns** : 전체 데이터를 포함하는 [Promise](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)

**Since**: v3.0.0

## Arguments

| name           | type     | description                                              |
| -------------- | -------- | -------------------------------------------------------- |
| updateCallback | function | 스트림 처리 중 주기적으로 호출되는 콜백 함수 (선택 사항) |

`updateCallback`은 [`on(event)` 메서드]({{site.baseurl}}/documentation/api_streamhelper/on.html)에서 설명한 것과 동일한  
메타데이터 객체를 인자로 받습니다. 예: `percent`, `currentFile` 등

## Example

```js
zip
  .generateInternalStream({ type: "uint8array" })
  .accumulate(function updateCallback(metadata) {
    // metadata에는 예: currentFile, percent 등이 포함됨
  })
  .then(function (data) {
    // data는 전체 zip 파일을 포함하는 Uint8Array
  });
```
