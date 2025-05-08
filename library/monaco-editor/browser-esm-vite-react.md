# monaco-editor/samples/browser-esm-vite-react 에 사용된 코드 분석

## main

`import "./userWorker"`로 프로젝트에서 웹 워커 경로를 인식

### 웹 워커

브라우저에서 백그라운드 작업을 할 수 있게 해주는 기술입니다.\
기본적으로 자바스크립트는 한 번에 한 가지 작업만 실행할 수 있는 싱글 스레드(Single-threaded)입니다.\
따라서 무거운 계산 작업이 있으면 화면 렌더링이 멈추거나 클릭이 안 되는 현상이 발생할 수 있습니다.

웹 워커는 별도의 스레드(작업 공간)에서 코드가 실행되기 때문에 메인 UI 스레드를 멈추지 않고도 무거운 작업을 할 수 있게 해줍니다.
Monaco는 타입스크립트 타입 검사, 자동완성 제공, JSON 스키마 검사, 포맷팅 등 무거운 작업을 하기 때문에 언어별 Worker를 만들어 백그라운드에서 실행됩니다.

```tsx
import React from "react";
import ReactDOM from "react-dom";

import { Editor } from "./components/Editor";

import "./userWorker";

ReactDOM.render(
  <React.StrictMode>
    <Editor />
  </React.StrictMode>,
  document.getElementById("root")
);
```

### Monaco에서 웹 워커 쓰는 구조

```plain
monaco-editor/
├─ esm/
│  └─ vs/
│     ├─ editor/
│     │  └─ editor.worker.ts   ← 기본 에디터 워커
│     ├─ language/
│     │  ├─ typescript/
│     │  │  └─ ts.worker.ts    ← TS/JS용 워커
│     │  ├─ json/
│     │  │  └─ json.worker.ts  ← JSON용 워커
```

## useWorker

### ?worker 옵션

vite에서 `?worker`를 붙이면 웹 워커로 번들되도록 지정합니다.\
이걸 지정하지 않으면 웹 워커가 JS로 로드되지 않고 에러가 납니다.

### MonacoEnvironment.getWorker(...)

Monaco는 언어별로 다른 Worker를 사용합니다.\
이 함수는 각 언어 요청(label)에 따라 적절한 Worker 클래스를 반환합니다.

### typescriptDefaults.setEagerModelSync(true)

즉시 모델 동기화 설정입니다.\
기본적으로는 사용자가 멈췄을 때 백그라운드 동기화를 하는데, `setEagerModelSync(true)`를 설정하면 모델 내용을 항상 Worker와 실시간 동기화합니다.

```ts
import * as monaco from "monaco-editor";
import editorWorker from "monaco-editor/esm/vs/editor/editor.worker?worker";
import jsonWorker from "monaco-editor/esm/vs/language/json/json.worker?worker";
import cssWorker from "monaco-editor/esm/vs/language/css/css.worker?worker";
import htmlWorker from "monaco-editor/esm/vs/language/html/html.worker?worker";
import tsWorker from "monaco-editor/esm/vs/language/typescript/ts.worker?worker";

// @ts-ignore
self.MonacoEnvironment = {
  getWorker(_: any, label: string) {
    if (label === "json") {
      return new jsonWorker();
    }
    if (label === "css" || label === "scss" || label === "less") {
      return new cssWorker();
    }
    if (label === "html" || label === "handlebars" || label === "razor") {
      return new htmlWorker();
    }
    if (label === "typescript" || label === "javascript") {
      return new tsWorker();
    }
    return new editorWorker();
  },
};

monaco.languages.typescript.typescriptDefaults.setEagerModelSync(true);
```

## components/Editor

### useState – 에디터 인스턴스를 상태로 저장

생성된 Monaco 에디터 인스턴스를 저장합니다.\
이미 생성한 editor가 있으면 또 만들지 않게 하고 `dispose()` 같은 클린업을 위해 참조를 보관하는 목적입니다.

### useRef – DOM 요소 직접 조작

Monaco Editor는 `textarea`나 React식 렌더링이 아니라, 직접 HTML 요소에 attach해야 합니다.\
그래서 `div` DOM 요소를 직접 가리키는 `ref`가 필요하고, 그걸 `monaco.editor.create(...)`에 넘깁니다.

### monaco.editor.create(...)

Monaco Editor 인스턴스를 생성하고, 특정 DOM에 바인딩합니다.

#### value

에디터에 들어갈 초기 코드 (기본 텍스트)

#### language

Monaco가 내부적으로 사용하는 언어 엔진 (자동완성, 오류 표시 등).

### model 없이 직접 value 지정

내부적으로 자동으로 `inmemory://` URI가 부여된 모델을 생성합니다.

여러 파일 관리하려면 `monaco.editor.createModel(...)`로 별도로 모델을 만들 수 있습니다.

### dispose()

컴포넌트 언마운트 시 에디터 인스턴스를 제거하여 메모리 누수를 막습니다.

### ref={monacoEl} – 실제 DOM에 에디터 연결

Monaco는 내부적으로 canvas 기반 UI를 직접 만들어서 붙입니다.\
이 `div`에만 editor가 "그려지기" 때문에 반드시 `ref`가 필요합니다.

```tsx
import { useRef, useState, useEffect } from "react";

import * as monaco from "monaco-editor/esm/vs/editor/editor.api";
import styled from "styled-components";

const EditorWrapper = styled.div`
  width: 100vw;
  height: 100vh;
`;

export const MonacoEditor = () => {
  const [editor, setEditor] =
    useState<monaco.editor.IStandaloneCodeEditor | null>(null);
  const monacoEl = useRef(null);

  useEffect(() => {
    // monacoEl.current가 존재하는지 확인
    if (monacoEl) {
      setEditor((editor) => {
        // editor가 이미 있다면 재생성하지 않음
        if (editor) return editor;

        // monaco.editor.create(...)로 에디터 생성
        return monaco.editor.create(monacoEl.current!, {
          value: ["function x() {", '\tconsole.log("Hello world!");', "}"].join(
            "\n"
          ),
          language: "typescript",
        });
      });
    }

    return () => editor?.dispose(); // 컴포넌트 언마운트 시 .dispose()로 리소스 해제
  }, [monacoEl.current]);

  return <EditorWrapper ref={monacoEl}></EditorWrapper>;
};
```
