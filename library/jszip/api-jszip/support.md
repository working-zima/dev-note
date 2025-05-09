# JSZip.support

브라우저 또는 실행 환경이 지원하는 경우, JSZip은 다음과 같은 최신 기능을 활용할 수 있습니다:  
`ArrayBuffer`, `Blob`, `Uint8Array` 등.  
`JSZip.support` 객체를 통해 현재 환경에서 어떤 기능이 지원되는지 확인할 수 있습니다.

각 속성은 해당 기능을 사용할 수 있으면 `true`, 그렇지 않으면 `false`입니다.

- `arraybuffer` : ArrayBuffer를 읽고 생성할 수 있는 경우 true
- `uint8array` : Uint8Array를 읽고 생성할 수 있는 경우 true
- `blob` : Blob을 생성할 수 있는 경우 true
- `nodebuffer` : Node.js 환경에서 Buffer를 읽고 생성할 수 있는 경우 true
- `nodestream` : Node.js 환경에서 Stream을 읽고 생성할 수 있는 경우 true
