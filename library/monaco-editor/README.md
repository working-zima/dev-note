---
description: VS Code에서 사용하는 코드 에디터 Monaco Editor에 관한 내용입니다.
---

# Monaco Editor

## 설치

```bash
npm install monaco-editor
```

설치 시 다음과 같은 디렉토리 구조가 제공됩니다.

- `/esm`: ESM(ECMAScript Module) 버전 (Webpack 등과 호환)
- `/dev`: AMD(Asynchronous Module Definition) 번들, **비압축**
- `/min`: AMD(Asynchronous Module Definition) 번들, **압축됨**
- `/min-maps`: 압축 버전용 소스맵
- `monaco.d.ts`: 에디터의 API를 정의 (이 파일만 버전 관리되며 나머지는 내부 구현으로 간주되어 버전 간 변경 가능성 있음)

**권장:**  
개발 시에는 `/dev` 버전을 사용하고,  
배포 시에는 `/min` 버전을 사용하는 것이 좋습니다.

## 핵심 개념

### Models (모델)

에디터의 텍스트 데이터 (가상의 파일).\
열려 있는 파일을 나타내며, 파일의 실제 존재 여부와 무관하게 다음을 포함합니다:

- 텍스트 콘텐츠
- 언어 정보
- 편집 기록

### URIs

각 모델은 고유한 URI로 식별됩니다.\
동일한 URI를 가진 모델은 두 개 존재할 수 없습니다.

예시:

- 파일 경로: `file:///`
- 메모리 기반 경로: `inmemory://model/1`  
  → 이후 생성되는 모델은 `/2`, `/3` 식으로 번호 증가

### Editors (에디터)

사용자에게 보이는 실제 UI 요소.\
Model을 렌더링하는 실제 화면상의 컴포넌트.\
모델을 DOM에 붙이고, 뷰 상태 관리 및 명령 실행 등의 작업을 수행합니다.

### Providers (제공자)

스마트 기능 제공 (자동 완성, 호버 등).  
LSP(Language Server Protocol)와 유사하며 다음을 고려해야 합니다:

- 모델 기반으로 작동
- 파일 URI가 중요 (예: TypeScript의 import 해석, JSON 스키마 적용 등)

### Disposables (자원 해제 객체)

생성된 객체의 리소스를 해제하는 메커니즘 (.dispose()).\
많은 객체가 `.dispose()` 메서드를 갖고 있으며, 자원 해제를 위해 사용됩니다.

예:

- `model.dispose()` → URI 해제
- 에디터 dispose → 리스너 제거 및 메모리 해제

## 문서 및 학습 자료

- [AMD 버전 통합 예제](https://github.com/microsoft/monaco-editor/blob/main/docs/integrate-amd.md)
- [ESM 버전 통합 예제](https://github.com/microsoft/monaco-editor/blob/main/docs/integrate-esm.md)
- [Playground에서 API 실습 및 커스터마이징 실험](https://microsoft.github.io/monaco-editor/playground.html?source=v0.52.2#example-creating-the-editor-hello-world)
- [`monaco.d.ts`](https://github.com/microsoft/monaco-editor/blob/gh-pages/node_modules/monaco-editor/monaco.d.ts)에서 [API 문서](https://microsoft.github.io/monaco-editor/docs.html) 직접 확인 가능
- [접근성 가이드: 모든 사용자에게 접근 가능한 에디터 만들기](https://github.com/microsoft/monaco-editor/wiki/Accessibility-Guide-for-Integrators)
- [Monarch Playground: 새로운 언어용 토크나이저 만들기](https://microsoft.github.io/monaco-editor/monarch.html)
- [StackOverflow: 질문 검색 및 해결](https://stackoverflow.com/questions/tagged/monaco-editor)
- [GitHub Issues: 버그 리포트 및 기능 요청 (중복 확인 필수)](https://github.com/microsoft/monaco-editor/issues)

## 자주 묻는 질문 (FAQ)

**VS Code와 Monaco Editor의 관계는?**  
→ Monaco Editor는 VS Code의 소스에서 추출되며, 웹에서 실행할 수 있도록 필요한 서비스에 쉼(Shim)을 둔 버전입니다.

**VS Code 버전과 Monaco Editor 버전은 연관 있나요?**  
→ 없습니다. Monaco Editor는 별도의 라이브러리이며 버전이 다릅니다.

**VS Code 확장 프로그램을 Monaco Editor에서 사용할 수 있나요?**  
→ 아니요.  
단, [LSP](https://microsoft.github.io/language-server-protocol/) 기반이며 JS로 작성된 서버일 경우 가능성은 있습니다.

**왜 웹 워커를 사용하나요?**  
→ 언어 서비스는 무거운 작업을 UI 스레드 외부에서 처리하기 위해 웹 워커를 사용합니다.  
리소스 오버헤드는 거의 없으므로 작동만 잘 되면 신경 쓸 필요 없습니다.

**loader.js는 무엇인가요? require.js는 사용 가능한가요?**  
→ loader.js는 VS Code에서 사용하는 AMD 로더입니다. require.js 사용 가능.

**"Could not create web worker" 경고가 뜨는데요?**  
→ HTML5 보안 정책상 `file://`에서 로드된 페이지는 웹 워커를 만들 수 없습니다.  
`http://` 또는 `https://`로 웹 서버를 통해 로드하세요.

**모바일 브라우저 지원 여부는?**  
→ 지원하지 않습니다.

**TextMate 문법을 왜 지원하지 않나요?**  
→ [`monaco-tm`](https://github.com/bolinfest/monaco-tm) 사용 시  
`monaco-editor`, `vscode-oniguruma`, `vscode-textmate`를 조합하여 TextMate 문법 지원 가능.
