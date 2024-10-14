# ArrayBuffer

JavaScript에서 이진 데이터를 다룰 수 있도록 해주는 객체입니다.\
숫자, 문자, 이미지 데이터처럼 메모리에 저장된 원시 데이터(raw data)를 효율적으로 처리할 수 있습니다.\
즉, 고정된 크기의 바이트 배열을 나타내며, 메모리의 특정 크기만큼의 공간을 예약해 둔 것이며, 이 공간을 사용하여 다양한 형식의 이진 데이터를 저장하고 조작할 수 있습니다.

주로 네트워크에서 전송되는 이진 데이터를 처리할 때, 파일 시스템에서 이진 데이터를 읽고 쓸 때, 이미지나 비디오 데이터를 처리할 때 사용됩니다.

## 기본 개념

### 크기 고정

ArrayBuffer는 생성할 때 미리 크기를 정해야 합니다.\
한 번 크기가 정해지면 나중에 변경할 수 없습니다.

### 바이트 단위

ArrayBuffer는 바이트 단위로 데이터를 저장합니다.\
1바이트는 8비트이고, 각 바이트에는 0~255 사이의 값을 저장할 수 있습니다.

### 생성 예시

`ArrayBuffer`는 데이터를 읽고 쓰기 위해 `TypedArray`(`Uint8Array`, `Int16Array` 등)를 사용해야합니다.

```js
// 8바이트 크기의 ArrayBuffer 생성
let buffer = new ArrayBuffer(8);

// Uint8Array를 통해 buffer를 읽고 쓸 수 있음
// Uint8Array는 ArrayBuffer 위에서 8비트 부호 없는 정수 (0~255)를 다룰 수 있게 해줌
let view = new Uint8Array(buffer);

// 첫 번째 바이트에 값 255 저장
view[0] = 255;

console.log(view[0]);  // 255 출력
```
