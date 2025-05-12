# Namespace editor

## Enumerations

### 1. `AccessibilitySupport`

에디터의 접근성(스크린리더 등) 지원 상태를 정의합니다.

- **값 목록**
  - `Unknown`
  - `Disabled`
  - `Enabled`

```js
monaco.editor.create(document.getElementById("container"), {
  value: 'console.log("Hello")',
  language: "javascript",
  accessibilitySupport: monaco.editor.AccessibilitySupport.Enabled,
});
```

### 2. `ContentWidgetPositionPreference`

컨텐츠 위젯의 위치 선호도를 설정합니다.

- **값 목록**
  - `EXACT`
  - `ABOVE`
  - `BELOW`

```js
const widget = {
  getId: () => "my.content.widget",
  getDomNode: () => {
    const node = document.createElement("div");
    node.innerText = "My Widget";
    return node;
  },
  getPosition: () => ({
    position: { lineNumber: 5, column: 1 },
    preference: [monaco.editor.ContentWidgetPositionPreference.BELOW],
  }),
};
editor.addContentWidget(widget);
```

### 3. `CursorChangeReason`

커서 이동의 원인을 구분합니다.

- **값 목록**
  - `NotSet`
  - `ContentFlush`
  - `RecoverFromMarkers`
  - `Explicit`
  - `Paste`
  - `Undo`
  - `Redo`

```js
editor.onDidChangeCursorPosition((e) => {
  console.log(e.reason);
});
```

### 4. `DefaultEndOfLine`

모델의 기본 줄바꿈 문자를 지정합니다.

- **값 목록**
  - `LF`
  - `CRLF`

```js
const model = monaco.editor.createModel(
  "const x = 1;",
  "javascript",
  undefined,
  monaco.editor.DefaultEndOfLine.LF
);
```

### 5. `EditorAutoIndentStrategy`

자동 들여쓰기 전략을 설정합니다.

- **값 목록**
  - `None`
  - `Keep`
  - `Brackets`
  - `Advanced`
  - `Full`

```js
monaco.editor.create(document.getElementById("container"), {
  value: 'function test() {\nconsole.log("hi");\n}',
  language: "javascript",
  autoIndent: monaco.editor.EditorAutoIndentStrategy.Full,
});
```

### 6. `EditorOption`

`editor.updateOptions()` 등에서 사용하는 옵션 키 열거형입니다.

```js
editor.updateOptions({
  [monaco.editor.EditorOption.fontSize]: 16,
  [monaco.editor.EditorOption.readOnly]: true,
});
```

### 7. `EndOfLinePreference`

EOL 문자 선택 시 모델이 선호하는 방식입니다.

- **값 목록**
  - `TextDefined`
  - `LF`
  - `CRLF`

```js
const eol = model.getEOL(monaco.editor.EndOfLinePreference.LF);
```

### 8. `EndOfLineSequence`

EOL 문자열을 설정할 때 사용합니다.

- **값 목록**
  - `LF`
  - `CRLF`

```js
model.setEOL(monaco.editor.EndOfLineSequence.CRLF);
```

### 9. `InjectedTextCursorStops`

삽입된 텍스트에서 커서가 멈출 위치를 제어합니다.

- **값 목록**
  - `Both`
  - `Left`
  - `Right`
  - `None`

```js
model.applyEdits([
  {
    range: new monaco.Range(1, 1, 1, 1),
    text: "",
    forceMoveMarkers: true,
    injectedText: {
      content: "Injected",
      cursorStops: monaco.editor.InjectedTextCursorStops.Both,
    },
  },
]);
```

### 10. `MinimapPosition`

미니맵 데코레이션의 위치를 지정합니다.

- **값 목록**
  - `Inline`
  - `Gutter`

```js
editor.deltaDecorations(
  [],
  [
    {
      range: new monaco.Range(3, 1, 3, 1),
      options: {
        minimap: {
          position: monaco.editor.MinimapPosition.Gutter,
        },
      },
    },
  ]
);
```

### 11. `MouseTargetType`

마우스가 위치한 요소의 유형을 반환합니다.

- **예시 값**
  - `ContentText`
  - `ContentEmpty`
  - `Scrollbar`
  - `OverlayWidget`
  - `OutsideEditor`

```js
editor.onMouseDown((e) => {
  console.log(e.target.type); // MouseTargetType 열거형 값
});
```

### 12. `OverlayWidgetPositionPreference`

오버레이 위젯의 위치 선호도입니다.

- **값 목록**
  - `TopRightCorner`
  - `BottomRightCorner`
  - `TopCenter`

```js
const overlayWidget = {
  getId: () => "my.overlay.widget",
  getDomNode: () => {
    const node = document.createElement("div");
    node.innerText = "Overlay";
    return node;
  },
  getPosition: () => ({
    preference: monaco.editor.OverlayWidgetPositionPreference.TopRightCorner,
  }),
};
editor.addOverlayWidget(overlayWidget);
```

### 13. `OverviewRulerLane`

오버뷰 룰러 마커의 위치를 정의합니다.

- **값 목록**
  - `Left`
  - `Center`
  - `Right`
  - `Full`

```js
editor.deltaDecorations(
  [],
  [
    {
      range: new monaco.Range(6, 1, 6, 1),
      options: {
        overviewRuler: {
          color: "rgba(255,0,0,0.5)",
          position: monaco.editor.OverviewRulerLane.Right,
        },
      },
    },
  ]
);
```

### 14. `RenderLineNumbersType`

라인 번호의 표시 방식을 설정합니다.

- **값 목록**
  - `Off`
  - `On`
  - `Relative`
  - `Interval`

```js
monaco.editor.create(document.getElementById("container"), {
  language: "javascript",
  lineNumbers: "relative",
});
```

### 15. `RenderMinimap`

미니맵 렌더링 유형을 정의합니다.

- **값 목록**
  - `None`
  - `Text`
  - `Blocks`

```js
monaco.editor.create(document.getElementById("container"), {
  language: "javascript",
  minimap: {
    enabled: true,
    renderCharacters: false,
  },
});
```

### 16. `ScrollType`

스크롤 방식(즉시 or 부드럽게)을 지정합니다.

- **값 목록**
  - `Smooth`
  - `Immediate`

```js
editor.setScrollTop(200, monaco.editor.ScrollType.Smooth);
```

### 17. `ScrollbarVisibility`

스크롤바의 가시성 모드를 설정합니다.

- **값 목록**
  - `Auto`
  - `Hidden`
  - `Visible`

```js
monaco.editor.create(document.getElementById("container"), {
  language: "javascript",
  scrollbar: {
    vertical: "hidden",
    horizontal: "auto",
  },
});
```

### 18. `TextEditorCursorBlinkingStyle`

커서 깜빡임 스타일을 지정합니다.

- **값 목록**
  - `Hidden`
  - `Blink`
  - `Smooth`
  - `Phase`
  - `Expand`
  - `Solid`

```js
monaco.editor.create(document.getElementById("container"), {
  language: "javascript",
  cursorBlinking: "phase",
});
```

### 19. `TextEditorCursorStyle`

커서의 스타일을 지정합니다.

- **값 목록**
  - `Line`
  - `Block`
  - `Underline`
  - `LineThin`
  - `BlockOutline`
  - `UnderlineThin`

```js
monaco.editor.create(document.getElementById("container"), {
  language: "javascript",
  cursorStyle: "block",
});
```

### 20. `TrackedRangeStickiness`

트래킹 범위가 확장되는 방향을 설정합니다.

- **값 목록**
  - `AlwaysGrowsWhenTypingAtEdges`
  - `NeverGrowsWhenTypingAtEdges`
  - `GrowsOnlyWhenTypingBefore`
  - `GrowsOnlyWhenTypingAfter`

```js
editor.deltaDecorations(
  [],
  [
    {
      range: new monaco.Range(3, 1, 3, 5),
      options: {
        className: "highlighted-range",
        stickiness:
          monaco.editor.TrackedRangeStickiness.NeverGrowsWhenTypingAtEdges,
      },
    },
  ]
);
```

### 21. `WrappingIndent`

자동 줄바꿈 시 들여쓰기 방식을 설정합니다.

- **값 목록**
  - `None`
  - `Same`
  - `Indent`
  - `DeepIndent`

```js
monaco.editor.create(document.getElementById("container"), {
  language: "javascript",
  wordWrap: "on",
  wrappingIndent: "indent", // or WrappingIndent.Indent
});
```

> 누락된 Enum (`GlyphMarginLane`, `MinimapSectionHeaderStyle`, `PositionAffinity`, `ShowLightbulbIconMode`)은 현재 Monaco Editor API에서 사용 예제가 없어 제외되었습니다.

## Classes

### 1. `ApplyUpdateResult<T>`

에디터 설정 업데이트 결과를 나타내는 클래스입니다.

- **속성**
  - `newValue: T` — 업데이트된 새로운 값
  - `didChange: boolean` — 값이 실제로 변경되었는지 여부

```ts
const result = new monaco.editor.ApplyUpdateResult("newValue", true);
console.log(result.newValue); // 'newValue'
console.log(result.didChange); // true
```

### 2. `BareFontInfo`

폰트 관련 기본 정보를 담는 클래스입니다.

- **속성**
  - `fontFamily: string`
  - `fontWeight: string`
  - `fontSize: number`
  - `letterSpacing: number`
  - `lineHeight: number`
  - `pixelRatio: number`

```ts
const fontInfo = new monaco.editor.BareFontInfo();
console.log(fontInfo.fontFamily);
```

### 3. `ConfigurationChangedEvent`

에디터 설정이 변경되었을 때 발생하는 이벤트 클래스입니다.

- **메서드**
  - `hasChanged(option: EditorOption): boolean` — 특정 옵션이 변경되었는지 확인

```ts
editor.onDidChangeConfiguration((event) => {
  if (event.hasChanged(monaco.editor.EditorOption.fontSize)) {
    console.log("Font size changed");
  }
});
```

### 4. `FindMatch`

검색 결과를 나타내는 클래스입니다.

- **속성**
  - `range: monaco.Range` — 매치된 텍스트의 범위
  - `matches: string[]` — 매치된 문자열 배열

```ts
const matches = model.findMatches("function", true, false, false, null, true);
matches.forEach((match) => {
  console.log(match.range);
  console.log(match.matches);
});
```

### 5. `FontInfo`

폰트 관련 상세 정보를 담는 클래스입니다.

- **속성**
  - `isMonospace: boolean`
  - `typicalFullwidthCharacterWidth: number`
  - `typicalHalfwidthCharacterWidth: number`
  - `spaceWidth: number`
  - `maxDigitWidth: number`

```ts
const options = editor.getOptions();
const fontInfo = options.get(monaco.editor.EditorOption.fontInfo);
console.log(fontInfo.isMonospace);
```

### 6. `TextModelResolvedOptions`

텍스트 모델의 설정 정보를 담는 클래스입니다.

- **속성**
  - `tabSize: number`
  - `indentSize: number`
  - `insertSpaces: boolean`
  - `defaultEOL: monaco.editor.DefaultEndOfLine`
  - `trimAutoWhitespace: boolean`

```ts
const model = editor.getModel();
const options = model.getOptions();
console.log(options.tabSize);
```

## Interface

### 1. `IStandaloneCodeEditor`

웹 환경에서 사용하는 **코드 에디터의 전체 기능**을 포함하는 인터페이스입니다.  
`monaco.editor.create()`로 반환되는 객체의 타입입니다.

#### IStandaloneCodeEditor 주요 메서드

| 메서드                                | 설명                         |
| ------------------------------------- | ---------------------------- |
| `getValue()`                          | 에디터의 현재 문자열 내용    |
| `setValue(value)`                     | 내용을 설정                  |
| `getModel()`                          | 현재 모델(`ITextModel`) 반환 |
| `onDidChangeModelContent(cb)`         | 내용 변경 시 콜백            |
| `addCommand(keys, handler)`           | 단축키 등록                  |
| `trigger(source, handlerId, payload)` | 명령 실행 (undo, redo 등)    |
| `layout()`                            | 에디터 리사이징              |
| `focus()`                             | 포커스 주기                  |

#### trigger

```js
trigger(source, handlerId, payload);
```

| `handlerId`                               | 설명                      |     |
| ----------------------------------------- | ------------------------- | --- |
| `'undo'`                                  | 실행 취소                 |     |
| `'redo'`                                  | 다시 실행                 |     |
| `'editor.action.commentLine'`             | 현재 줄 주석 처리         |     |
| `'editor.action.formatDocument'`          | 문서 포맷팅               |     |
| `'editor.action.triggerSuggest'`          | 자동 완성 제안 표시       |     |
| `'editor.action.showHover'`               | 호버 정보 표시            |     |
| `'editor.action.rename'`                  | 변수 이름 변경            |     |
| `'editor.action.goToDeclaration'`         | 선언 위치로 이동          |     |
| `'editor.action.quickFix'`                | 빠른 수정 제안            |     |
| `'editor.action.referenceSearch.trigger'` | 참조 검색 시작            |     |
| `'editor.action.selectAll'`               | 전체 선택                 |     |
| `'editor.action.indentLines'`             | 줄 들여쓰기               |     |
| `'editor.action.outdentLines'`            | 줄 내어쓰기               |     |
| `'editor.action.insertLineAfter'`         | 현재 줄 아래에 새 줄 삽입 |     |
| `'editor.action.insertLineBefore'`        | 현재 줄 위에 새 줄 삽입   |     |
| `'editor.action.deleteLines'`             | 현재 줄 삭제              |     |
| `'editor.action.copyLinesDownAction'`     | 현재 줄 아래로 복사       |     |
| `'editor.action.copyLinesUpAction'`       | 현재 줄 위로 복사         |     |
| `'editor.action.moveLinesDownAction'`     | 현재 줄 아래로 이동       |     |
| `'editor.action.moveLinesUpAction'`       | 현재 줄 위로 이동         |     |

```js
const newEditor = monaco.editor.create(monacoEl.current, {
  value: "",
  language: "plaintext",
});

// ctrl/cmd + z
newEditor.addCommand(monaco.KeyMod.CtrlCmd | monaco.KeyCode.KeyZ, () =>
  newEditor.trigger("keyboard", "undo", null)
);

// ctrl/cmd + shift + z
newEditor.addCommand(
  monaco.KeyMod.CtrlCmd | monaco.KeyMod.Shift | monaco.KeyCode.KeyZ,
  () => newEditor.trigger("keyboard", "redo", null)
);

return () => editor.dispose(); // 반드시 해제
```

#### IStandaloneCodeEditor 예제

```ts
const editor = monaco.editor.create(domEl, {
  value: "",
  language: "javascript",
});
editor.setValue("function hi() {}");
```

### 2. `ICodeEditor`

`IStandaloneCodeEditor`의 상위 타입으로,  
모든 Monaco 에디터의 **기본 기능 정의**를 포함합니다.

> 거의 모든 메서드는 `IStandaloneCodeEditor`에 포함되므로  
> 직접 타입으로 지정할 일은 드물지만, 구조 이해에 중요합니다.

#### ICodeEditor 포함 메서드 예시

- `getValue()`, `setValue()`, `getSelection()`, `setSelection()`
- `getModel()`, `onDidChangeCursorPosition(cb)`
- `getPosition()`, `setPosition()`, `trigger(...)`

### 3. `ITextModel`

에디터가 바인딩하는 **텍스트 문서 모델**의 인터페이스입니다.  
`editor.getModel()`을 통해 얻을 수 있습니다.

#### ITextModel 주요 메서드

| 메서드              | 설명               |
| ------------------- | ------------------ |
| `getValue()`        | 모델의 전체 문자열 |
| `setValue(text)`    | 내용 설정          |
| `getLineCount()`    | 줄 수 반환         |
| `getLineContent(n)` | 특정 줄의 텍스트   |
| `findMatches(...)`  | 전체 문자열 검색   |
| `getEOL()`          | EOL 문자 반환      |
| `setEOL(eol)`       | EOL 문자 변경      |

#### ITextModel 예제

```ts
const model = editor.getModel();
console.log(model.getLineCount());
```

### 4. `CompletionItemProvider`

자동완성 기능을 구현할 때 사용하는 인터페이스입니다.  
`monaco.languages.registerCompletionItemProvider()`에 등록합니다.

#### CompletionItemProvider 필수 메서드

| 메서드                                    | 설명                  |
| ----------------------------------------- | --------------------- |
| `provideCompletionItems(model, position)` | 자동완성 목록 반환    |
| `resolveCompletionItem?(item)`            | (선택) 세부 정보 제공 |

#### CompletionItemProvider 예제

```ts
monaco.languages.registerCompletionItemProvider("javascript", {
  provideCompletionItems: () => ({
    suggestions: [
      {
        label: "log",
        kind: monaco.languages.CompletionItemKind.Function,
        insertText: "console.log($1);",
        insertTextRules:
          monaco.languages.CompletionItemInsertTextRule.InsertAsSnippet,
      },
    ],
  }),
});
```

### 5. `Range`

문서 상의 위치 구간을 정의하는 기본 인터페이스입니다.  
대부분의 API에서 범위를 지정할 때 사용됩니다.

#### Range 생성자

```ts
new monaco.Range(startLineNumber, startColumn, endLineNumber, endColumn);
```

#### Range 예제

```ts
editor.deltaDecorations(
  [],
  [
    {
      range: new monaco.Range(2, 1, 2, 10),
      options: {
        inlineClassName: "highlighted",
      },
    },
  ]
);
```
