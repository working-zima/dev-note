# on(event, callback)

`StreamHelper`에서 이벤트 리스너를 등록합니다.

**Returns** : 현재 `StreamHelper` 객체를 반환하며, 체이닝이 가능합니다.

**Throws** : 지원되지 않는 이벤트 이름을 사용할 경우 예외를 발생시킵니다.

## Arguments

| name     | type     | description                                                          |
| -------- | -------- | -------------------------------------------------------------------- |
| event    | string   | 이벤트 이름. 지원되는 이벤트는 `data`, `end`, `error` 세 가지입니다. |
| callback | function | 해당 이벤트 발생 시 호출할 함수. 아래에 각 이벤트별 설명 제공        |

콜백 함수 내에서의 `this`는 현재 `StreamHelper` 인스턴스를 가리킵니다.

### data 이벤트 콜백

파라미터 2개를 받습니다:

1. 현재 처리 중인 데이터 청크 (지정된 출력 타입에 따라 예: `Uint8Array`, `string` 등)
2. 메타데이터 객체 (예: `currentFile`, `percent` 등, 메서드마다 다름)

### end 이벤트 콜백

파라미터를 받지 않습니다.

### error 이벤트 콜백

`Error` 객체 하나를 파라미터로 받습니다.

## Example

```js
zip
  .generateInternalStream({ type: "uint8array" })
  .on("data", function (data, metadata) {
    // data는 Uint8Array (출력 타입이 uint8array이기 때문)
    // metadata에는 currentFile, percent 등이 포함될 수 있음
  })
  .on("error", function (e) {
    // e는 Error 객체
  })
  .on("end", function () {
    // 완료 시 호출됨. 파라미터 없음
  })
  .resume(); // 반드시 호출해야 스트림이 시작됨
```
