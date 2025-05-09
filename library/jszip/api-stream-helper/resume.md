# resume()

정지(paused)된 스트림을 다시 시작합니다.  
`resume()`이 호출되면 스트림은 `data` 이벤트 전송을 재개합니다.

**Returns** : 현재 `StreamHelper` 객체를 반환하며, 체이닝이 가능합니다.

**Since**: v3.0.0

## Example

```js
zip
  .generateInternalStream({ type: "uint8array" })
  .on("data", function () {
    // 데이터 처리
  })
  .resume(); // 스트림 시작
```
