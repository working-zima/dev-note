# internalStream(type)

지정한 형식으로 파일 내용을 스트림으로 반환합니다.  
결과는 [StreamHelper]({{site.baseurl}}/documentation/api_streamhelper.html}) 객체입니다.

**Returns** : 지정된 형식의 내용을 담은 [StreamHelper]({{site.baseurl}}/documentation/api_streamhelper.html})

## Arguments

| name | type   | description                                                                                             |
| ---- | ------ | ------------------------------------------------------------------------------------------------------- |
| type | string | 반환할 데이터 형식. 사용 가능한 값: `string`, `binarystring`, `uint8array`, `arraybuffer`, `nodebuffer` |

## Example

```js
zip
  .file("my_text.txt")
  .internalStream("string")
  .on("data", function (data) {
    // data 조각 처리
  })
  .on("error", function (e) {
    // 오류 처리
  })
  .on("end", function () {
    // 스트림 종료 처리
  });
```
