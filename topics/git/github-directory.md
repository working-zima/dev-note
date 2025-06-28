# GitHub 리포지토리에서 파일 관리

## .github 디렉토리

**협업과 자동화**에 필요한 설정들을 넣는 곳이에요.

### 정리된 폴더 구조 예시

```plain
.github/
├── ISSUE_TEMPLATE/
│   ├── bug_report.md
│   └── feature_request.md
├── PULL_REQUEST_TEMPLATE.md
├── workflows/
│   └── ci.yml
├── CODEOWNERS
├── dependabot.yml
├── [SECURITY.md](http://security.md/)
├── [CONTRIBUTING.md](http://contributing.md/)
└── CODE_OF_CONDUCT.md
```

### 1. `ISSUE_TEMPLATE/` 폴더

> 이슈를 만들 때 템플릿을 제공해주는 폴더예요.

- 이 안에 `.md` 파일을 여러 개 만들 수 있어요. 예:
  - `bug_report.md`: 버그 제보용
  - `feature_request.md`: 기능 요청용
  - `question.md`: 질문용

각 파일 안에는 이슈 작성자가 채워야 할 양식을 만들 수 있어요.

📌 **예시**

`ISSUE_TEMPLATE/bug_report.md`

```markdown
markdown
복사편집

name: Bug Report
about: 앱에서 발견한 버그를 알려주세요
title: "[Bug] "
labels: bug
assignees: ''

### 어떤 버그인가요?

버그 내용을 자세히 적어주세요.

### 어떻게 재현할 수 있나요?

1. 어떤 화면에서
2. 어떤 행동을 했을 때
3. 어떤 결과가 나왔는지 적어주세요.
```

### 2. `PULL_REQUEST_TEMPLATE.md`

> PR(Pull Request)을 만들 때 자동으로 나오는 설명 양식이에요.

개발자가 PR을 보낼 때, 어떤 변경을 했는지, 왜 했는지를 더 명확하게 기록할 수 있게 도와줘요.

📌 **예시**

```markdown
markdown
복사편집

## PR 설명

- 어떤 기능을 추가/수정했나요?

## 체크리스트

- [ ] 로컬에서 테스트 완료
- [ ] 관련 이슈 연결

## 기타 참고사항

- 관련된 디자인 링크나 참고자료가 있다면 적어주세요.
```

> 여러 개의 PR 템플릿이 필요하면 PULL_REQUEST_TEMPLATE/ 폴더로 관리할 수 있어요.

### 3. `workflows/` 폴더

> GitHub Actions를 위한 자동화 설정 파일들을 넣는 폴더예요.

파일 확장자는 `.yml` 또는 `.yaml`이고, 예를 들어 코드 푸시(push)할 때 자동으로 테스트하거나 빌드할 수 있어요.

📌 **예시**

`.github/workflows/ci.yml`

```yaml
yaml
복사편집
name: CI

on:
  push:
    branches: [main]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Node 설치
        uses: actions/setup-node@v3
        with:
          node-version: '20'
      - run: npm install
      - run: npm test

```

### 4. `CODEOWNERS`

> 특정 폴더나 파일에 대해 리뷰어를 자동으로 지정할 수 있는 파일이에요.

📌 **예시**

```md
# 모든 코드 변경은 @zimablue14가 검토

- @zimablue14

# src 디렉토리는 @frontend-dev가 담당

/src/ @frontend-dev
```

### 5. `FUNDING.yml`

> 오픈소스를 후원할 수 있는 버튼을 추가해요.
>
> (GitHub Sponsors, Patreon, Buy Me a Coffee 등)

📌 **예시**

```yaml
yaml
복사편집
github: zimablue14
patreon: username
buy_me_a_coffee: username

```

### 6. `dependabot.yml`

> 프로젝트에서 사용하는 라이브러리(의존성)를 자동으로 최신 상태로 유지해주는 봇 설정이에요.

이 파일을 설정하면 GitHub이 자동으로 PR을 보내서 패키지를 업데이트해줘요.

📌 예시

```yaml
yaml
복사편집
version: 2
updates:
  - package-ecosystem: "npm"
    directory: "/"  # 루트 디렉토리에 있는 package.json
    schedule:
      interval: "weekly"

```

📝 초보용 요약:

- `npm` 프로젝트에서
- 매주 한 번
- 패키지 업데이트 PR을 자동으로 생성해줘요.

### 7. `SECURITY.md`

> 보안 관련 문제가 생겼을 때 어떻게 신고하고 처리하는지를 알려주는 문서예요.

오픈소스는 누구나 볼 수 있기 때문에, 보안 문제가 생기면 공개적으로 말하기보다 **비공개 채널**을 통해 알려주는 게 좋아요.

📌 예시

```markdown
markdown
복사편집

# 보안 정책

보안 관련 취약점을 발견하셨다면 공개 이슈 대신 이메일로 제보해주세요:

📧 security@example.com

신고해주시면 빠르게 검토하고 대응하겠습니다.
```

### 8. `CONTRIBUTING.md`

> 외부 기여자(contributor)가 이 프로젝트에 기여하려면 어떤 방식으로 참여해야 하는지 알려주는 가이드예요.

📌 예시

```markdown
markdown
복사편집

# 기여 방법 안내

1. 이슈를 먼저 만들어주세요.
2. PR을 보낼 땐 `main` 브랜치가 아닌 `dev` 브랜치에 보내주세요.
3. 커밋 메시지는 다음 규칙을 따라주세요: `type: 설명`

예시:
feat: 검색 기능 추가
fix: 로그인 에러 수정
```

📝 초보자에게 중요한 이유:

- 어떻게 참여할 수 있는지,
- PR을 어디로 보내야 하는지,
- 커밋 메시지는 어떤 형식이어야 하는지를 미리 알려줘요.

### 9. `CODE_OF_CONDUCT.md`

> 오픈소스 커뮤니티에서 서로를 존중하며 활동하기 위한 행동 기준을 정한 문서예요.

📌 예시 요약:

```markdown
markdown
복사편집

# 행동 강령

우리는 모든 참가자가 서로 존중하는 환경을 지향합니다.

- 차별, 괴롭힘, 공격적인 언행은 금지
- 누구나 자유롭게 기여할 수 있는 환경을 만들 것

위반 시에는 GitHub 또는 이메일로 신고해 주세요.
```

📝 초보자에게 중요한 이유:

- 오픈소스는 전 세계 사람들이 함께하는 곳이기 때문에,
- 건강하고 안전한 커뮤니티 문화를 유지하기 위한 기본 약속이에요.
