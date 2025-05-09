# pause()

실행 중인 스트림을 일시 정지시킵니다.  
정지 상태에서는 `data` 이벤트가 발생하지 않으며,  
`resume()`을 호출해야 다시 데이터 전송이 시작됩니다.

**Returns** : 현재 `StreamHelper` 객체를 반환하며, 체이닝이 가능합니다.

## Example

```js
zip
  .generateInternalStream({ type: "uint8array" })
  .on("data", function (chunk) {
    // 다른 서비스로 데이터를 보낼 때 과부하(backpressure)가 걸리면 일시 정지 가능
    this.pause();
  })
  .resume(); // 최초로 스트림 시작
```
