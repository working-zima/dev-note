# 클린코드 for 자바스크립트(에러)

## 유효성 검사

유효성 검사는 사용자의 입력 값이 유효한지 검증하는 것입니다.
유효성 검사는 할 수 있는 모든 곳에서 사용하는 것이 좋습니다.

### 예시

- 전화번호
  : 숫자 외 입력하지 못하도록 설정

- 이메일
  : 사용자의 입력이 이메일 포맷에 맞는지 검증
  이메일 포맷이 맞는 경우에만 서버와 통신 => 서버와의 비용을 절감

### 방법

- 정규식

- Javascript 문법

- 웹 표준 API
  : `Input`의 `type`

이외에도 schema등 라이브러리로 유효성 검사에 도움을 받을 수 있습니다.

## try ~ catch

프로그래머가 모든 에러를 예측하여 처리하는 것은 거의 불가능합니다.
그렇기 때문에 에러의 발생이 예상되는 코드 혹은 발생시킬 코드에 `try`, `catch`를 사용하여 예외 처리를 해야합니다.

### try ~ catch를 잘못 사용한 사례

#### try에 중요하지 않다고 생각하는 핸들링을 포함시키지 않는 경우

```javascript
function handleSubmit(input) {
  // 중요하지 않다고 생각하는 핸들링까지 모두 포함
  try {
    // 예외가 예상되는 코드 혹은 발생시킬 코드
  } catch (error) {
    // 예외를 처리하는 코드
  }
}
```

#### 중복된 try

```javascript
function handleSubmit(input) {
  try {
    // 예외가 예상되는 코드 혹은 발생시킬 코드
    try {
      // 중복 된 try
    } catch (error) {}
  } catch (error) {
    // 예외를 처리하는 코드
  }
}
```

#### catch의 부실한 예외처리

`catch`에서 본인을 위한 예외처리만 사용하는 경우가 많습니다.
이외에도 사용자를 위한 예외처리와 사용 제안, 로깅 등을 모두 사용하는 것이 좋습니다.

```javascript
function handleSubmit(input) {
    try {
  } catch (error) {
    // 1. 개발자를 위한 예외처리 => 본인, 동료 개발자에게 제안
    console.warn(error) or console.error(error)

    // 2. 사용자를 위한 예외처리 => 사용자가 볼 수 있다는 생각
    alert(error.message) // '잠시만 기다려주세요, 다시 시도해주세요'

    // 3. 사용자에게 사용을 제안 => 사용자를 어딘가로 이동 시켜 다시 한번 알려주기
    history.back()
    history.go('안전한 어딘가')
    clear()
    element.focut()

    // 4. 에러 로그 수집
    sentry.전송()

    // 5. 비추천하지만 재귀
    handleSubmit(input)

  } finally {
    // 데이터 분석을 위한 로그
  }
}
```

## 사용자에게 알려주기

사용자는 동료 개발자, 내가 만든 앱을 이용하는 사용자를 말합니다.

### 동료 개발자에게 new 키워드를 사용하라고 알려주기

```javascript
function React() {
  if (!new.target) {
    throw new ReferenceError("생성자입니다.");
  }
}

React(); // ReferenceError

const react = new React();
```

## 참고

[클린코드 자바스크립트(JavaScript)](https://www.udemy.com/course/clean-code-js/)
