# File & FileReader

## File

`File` 객체는 `Blob`에서 상속되며, 파일 시스템과 관련된 기능이 추가된 객체입니다.\
주로 사용자 파일 입력(예: `<input type="file">`)을 처리할 때 사용됩니다.

File을 얻는 방법은 두 가지가 있습니다.

### File을 얻는 첫 번째 방법

`Blob`과 유사한 생성자를 사용하는 방법입니다.

```js
new File(fileParts, fileName, [options])
```

- `fileParts` - `Blob`/ `BufferSource`/ `String` 값들의 배열입니다.
- `fileName` - 파일 이름을 나타내는 문자열입니다.
- `options` - 선택적 객체입니다.
  - `lastModified` - 마지막으로 수정된 시간의 타임스탬프(정수형 날짜)입니다.

### File을 얻는 두 번째 방법

`<input type="file">` 태그나 드래그 앤 드롭 등의 브라우저 인터페이스를 통해 파일을 얻는 방법이 있습니다.\
이 경우 파일 정보는 운영체제(OS)로부터 가져옵니다.

`File` 객체는 `Blob`을 상속하기 때문에, `Blob`의 속성을 모두 가지며, 추가적으로 다음과 같은 속성을 가집니다.

- `name` – 파일 이름,
- `lastModified` – 마지막 수정된 타임스탬프.

다음은 `<input type="file">`로부터 `File` 객체를 가져오는 방법입니다:

```html
<input type="file" onchange="showFile(this)">

<script>
function showFile(input) {
  let file = input.files[0]; // 첫 번째 파일 가져오기

  alert(`File name: ${file.name}`); // 예: my.png
  alert(`Last modified: ${file.lastModified}`); // 예: 1552830408824
}
</script>
```

#### 참고

input 요소는 여러 파일을 선택할 수 있으므로 `input.files`는 파일들의 배열과 유사한 객체입니다.\
위 예제에서는 한 개의 파일만 선택했으므로 `input.files[0]`을 사용해 첫 번째 파일을 가져옵니다

## FileReader

`Blob` 및 `File` 객체로부터 데이터를 읽어오는 데 특화된 객체입니다.\
이 객체는 파일이나 블롭 데이터를 비동기적으로 읽어들이며, 읽기 작업 중 발생하는 이벤트를 통해 데이터를 제공합니다.\
이는 디스크로부터 데이터를 읽는 데 시간이 걸릴 수 있기 때문입니다.

### 생성자

```js
let reader = new FileReader(); // 인수 없이 생성
```

### 주요 메서드

- `readAsArrayBuffer(blob)` – 데이터를 이진 형식인 `ArrayBuffer`로 읽습니다.

- `readAsText(blob, [encoding])` – 데이터를 텍스트 문자열로 읽으며, 인코딩을 선택할 수 있습니다(기본값은 utf-8).

- `readAsDataURL(blob)` – 데이터를 base64로 인코딩된 데이터 URL로 읽습니다.

    ```js
    instanceOfFileReader.readAsDataURL(blob);
    ```

- `abort()` – 읽기 작업을 취소합니다.

읽기 방식은 사용할 데이터의 형식에 따라 선택합니다:

- `readAsArrayBuffer`는 이진 파일을 읽어 저수준 작업에 사용할 때 적합합니다.
- `readAsText`는 텍스트 파일을 문자열로 읽을 때 사용합니다.
- `readAsDataURL`은 이미지를 `<img>` 태그의 `src` 속성에 넣거나 다른 태그에 사용할 때 유용합니다.

`FileReader`는 이벤트 기반으로 동작하며, 읽기 작업이 진행됨에 따라 여러 이벤트가 발생합니다.

- `loadstart` – 읽기 시작.
- `progress` – 읽는 중.
- `load` – 에러 없이 완료.
- `abort` – abort()가 호출됨.
- `error` – 읽는 도중 에러 발생.
- `loadend` – 읽기 작업이 완료됨(성공 또는 실패 여부와 무관).

읽기가 완료되면 다음 속성을 통해 결과에 접근할 수 있습니다.

- `reader.result` – 읽기가 성공하면 이곳에 결과가 저장됩니다.
- `reader.error` – 읽기 중 에러가 발생한 경우 에러 정보가 여기에 저장됩니다.

`load`와 `error` 이벤트는 가장 자주 사용되는 이벤트입니다.

### 파일 읽기 예시

다음은 파일을 읽는 간단한 예시입니다:

```html
<input type="file" onchange="readFile(this)">

<script>
function readFile(input) {
  let file = input.files[0]; // 첫 번째 파일 선택

  let reader = new FileReader(); // FileReader 객체 생성

  reader.readAsText(file); // 텍스트로 파일 읽기

  // onload는 읽기 동작이 성공적으로 완료되었을 때마다 발생하는 이벤트 핸들러
  reader.onload = function() {
    console.log(reader.result); // 파일 내용 출력
  };

  reader.onerror = function() {
    console.log(reader.error); // 에러가 발생하면 에러 출력
  };
}
</script>
```

### 블롭을 위한 FileReader

`FileReader`는 파일뿐만 아니라 블롭(Blob)도 읽을 수 있습니다.\
블롭을 다른 형식으로 변환할 때 유용합니다.

- `readAsArrayBuffer(blob)` – `ArrayBuffer`로 변환.
- `readAsText(blob, [encoding])` – 문자열로 변환(`TextDecoder`의 대안).
- `readAsDataURL(blob)` – base64 데이터 URL로 변환(읽기).

### Web Workers에서의 FileReaderSync

Web Workers에서는 동기식으로 동작하는 `FileReaderSync`가 있습니다.\
이 메서드는 이벤트를 발생시키지 않고, 일반 함수처럼 결과를 반환합니다.\
이는 웹 페이지의 렌더링에 영향을 주지 않으므로 Web Workers에서 지연이 발생해도 큰 문제가 되지 않기 때문에 사용됩니다.

## 자료

- [File and FileReader](https://javascript.info/file)
