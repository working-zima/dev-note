# Nodemailer 사용 방법

## 설정 방법

### Nodemailer 설치

```bash
npm install nodemailer
```

### Transporter 객체 생성

`transporter`는 이메일을 전송할 수 있는 객체입니다.

```javascript
let transporter = nodemailer.createTransport(transport[, defaults]);
```

- `transporter`: 메일 전송을 담당하는 객체.

- `transport`: 전송 설정 객체, 연결 URL 또는 전송 플러그인 인스턴스.

- `defaults`: 메일 옵션에 대한 기본값을 정의하는 객체(선택사항).

Note: `transporter` 객체는 한 번만 생성하면 됩니다. 동일한 객체를 사용하여 여러 번 이메일을 전송할 수 있습니다.

### SMTP를 이용한 전송

SMTP 기반 `transporter` 설정 방법은 SMTP Transporter 설정 문서를 참조하세요.

### 플러그인을 이용한 전송

플러그인을 이용한 `transporter` 설정 방법은 플러그인 Transporter 설정 문서를 참조하세요.

## 이메일 전송

`transporter` 객체를 생성한 후, 이메일을 전송하려면 아래 메서드를 사용합니다:

```jsx
transporter.sendMail(data[, callback]);
```

- `data`: 이메일 내용 정의 (사용 가능한 옵션은 Message Configuration 문서 참고).

- `callback`: 선택적 콜백 함수로, 이메일 전송 완료 또는 실패 시 실행.

### callback의 파라미터

- `err`: 메시지 전송 실패 시 반환되는 오류 객체.
- `info`: 결과 정보를 포함한 객체(전송 메커니즘에 따라 형식이 다름).

  - `info.messageId`: 대부분의 전송 메커니즘에서 사용된 최종 Message-ID 값.
  - `info.envelope`: 메시지의 Envelope 객체.
  - `info.accepted`: SMTP 전송 시 수락된 수신자 주소 배열.
  - `info.rejected`: SMTP 전송 시 거절된 수신자 주소 배열.
  - `info.pending`: Direct SMTP 전송 시 임시로 거절된 수신자 주소 배열(서버 응답 포함).
  - `response`: SMTP 전송 시 서버의 마지막 응답 문자열.

  참고: 여러 수신자가 포함된 경우, 최소한 한 명이라도 메시지를 수락하면 메시지가 성공적으로 전송된 것으로 간주됩니다.

### Promise를 사용한 전송

콜백 함수를 사용하지 않으면, `sendMail()` 메서드는 Promise 객체를 반환합니다.\
Nodemailer는 내부적으로 Promise를 사용하지 않지만, 편의를 위해 반환값을 Promise로 래핑합니다.

예제:

```jsx
async function sendEmail() {
  try {
    const info = await transporter.sendMail({
      from: "sender@example.com",
      to: "recipient@example.com",
      subject: "Test Email",
      text: "This is a test email!",
    });
    console.log("Message sent: %s", info.messageId);
  } catch (error) {
    console.error("Error occurred: %s", error.message);
  }
}

sendEmail();
```
