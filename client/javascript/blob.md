# blob(blob Binary Large Object)

대형 이진 객체를 의미하며 JavaScript에서 이진 데이터(binary data)를 다룰 때 사용되는 인터페이스입니다.\
Blob은 주로 파일을 나타내며, 이미지, 비디오, 텍스트 파일 등을 처리하는 데 유용합니다.\
파일 시스템을 직접 다루지 않으면서도 파일이나 데이터를 조작할 수 있도록 해줍니다.

## blob 특징

- 불변성: 한 번 생성된 Blob은 내부 데이터를 수정할 수 없습니다.\
데이터를 변경하려면 새로운 Blob 객체를 생성해야 합니다.

- 크기와 형식 지정: Blob은 크기(바이트 단위)와 데이터 형식을 지정할 수 있습니다.\
예를 들어, 텍스트를 text/plain으로, 이미지를 image/png 형식으로 저장할 수 있습니다.

- 부분 데이터 처리: Blob은 slice() 메서드를 사용해 데이터를 부분적으로 가져올 수 있습니다.

## Blob 생성 방법

```js
const options = { type: "application/json" };
const array = ["Hello, World!"];

const data = new Blob(array, options);
```

### option

- `type` (Optional)\
블롭에 저장할 데이터의 MIME 유형입니다.\
기본 값은 빈 문자열('')입니다.

- `endings` (Optional)
데이터가 텍스트일 때, 개행 문자(`\n`)를 어떻게 해석할지 지정합니다.\
기본 값인 `transparent`는 개행 문자를 바꾸지 않고 블롭 데이터로 복사합니다.\
`nativ`e를 지정하면 호스트 시스템 컨벤션에 맞춰서 변환합니다.

## Blob 프로퍼티

### Blob.size

`Blob` 인터페이스의 `size` 속성은 Blob 또는 File의 크기를 바이트 단위로 반환합니다.

### Blob.type

`Blob` 객체의 `type` 속성은 데이터의 MIME 유형을 반환합니다.

```html
<input type="file" id="input" multiple />
<output id="output">파일 선택...</output>
```

```js
const input = document.getElementById("input");
const output = document.getElementById("output");

input.addEventListener("change", (event) => {
  output.innerText = "";

  for (const file of event.target.files) {
    output.innerText += `${file.name}의 크기는 ${file.size} 바이트입니다.\n`;
  }
});

// 스크린샷 2024-10-03 13.42.00.png의 크기는 617136 바이트입니다.
```

#### MIME type

MIME type은 파일의 형식을 나타내는 문자열로 파일과 같이 송신되는데 content의 형식을 나타내기 위해 사용합니다.\
"media type"으로 사용하며, 예를 들면 오디오 파일은 audio/ogg로 그림 파일은 image/png로 분류할 수 있습니다.

```html
<input type="file" id="input" multiple />
<output id="output">이미지 파일 선택...</output>
```

```js
// 우리 애플리케이션에서는 GIF, PNG, JPEG 이미지만 허용
const allowedFileTypes = ["image/png", "image/jpeg", "image/gif"];

const input = document.getElementById("input");
const output = document.getElementById("output");

input.addEventListener("change", (event) => {
  const files = event.target.files;

  // input으로 파일을 받았는지 확인
  if (files.length === 0) {
    output.innerText = "이미지 파일 선택...";
    return;
  }

  // input으로 받은 이미지 파일의 type이 "image/png", "image/jpeg", "image/gif" 인지 확인
  if (Array.from(files).every((file) => allowedFileTypes.includes(file.type))) {
    // "image/png", "image/jpeg", "image/gif" 타입이라면
    output.innerText = "모든 파일 사용 가능!";
  } else {
    // "image/png", "image/jpeg", "image/gif" 타입이 아니라면
    output.innerText = "이미지 파일만 선택하세요.";
  }
});
```

## blob 메서드

### arrayBuffer()

`Blob` 객체의 데이터를 [ArrayBuffer](./array-buffer.md) 형태로 읽는 비동기 메서드입니다.\
`ArrayBuffer`는 고정 크기의 원시 이진 데이터 버퍼로, 이를 통해 바이트 단위로 데이터를 다룰 수 있습니다.

```js
blob.arrayBuffer().then((buffer) => {
  console.log(buffer); // ArrayBuffer 객체 출력
});
```

### slice()

데이터를 일부만 잘라서 새로운 `Blob`을 생성합니다.

```js
const part = data.slice(0, 5); // "Hello"만 잘라냄
```

### stream()

`Blob` 객체의 내용을 `ReadableStream` 객체로 반환합니다.\
`ReadableStream`은 데이터를 일정한 조각(chunks)으로 비동기적으로 읽을 수 있도록 해줍니다.\
즉, 데이터를 한꺼번에 메모리로 읽어오지 않고, 데이터를 작은 조각으로 나누어 순차적으로 읽고 처리할 수 있습니다.

```js
const readableStream = blob.stream(); // Blob 데이터를 읽기 위한 ReadableStream을 반환
```

### text()

`Blob` 데이터를 문자열로 변환하여 읽을 수 있습니다.

```js
data.text().then(text => {
  console.log(text); // "Hello, World!"
});
```
