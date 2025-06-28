# Fork & Clone

## Fork

GitHub에서 **다른 사람의 저장소를 복사(fork)해서 내 GitHub 계정에 새로운 리포지토리를 만드는 것**입니다.

![fork.png](attachment:8f672e49-04b7-43f3-a44c-3ead9590432f:fork.png)

### 예를 들어서

- 누군가 공식 저장소(원본)에 멋진 프로젝트를 만들어 뒀어요.
- 나는 거기에 기여하고 싶지만 **직접 수정할 권한은 없어요**.
- 이럴 때 그 저장소를 **Fork**해서 **내 저장소로 복사**해요.
- **내 저장소에서** 수정한 내용을 원본 저장소에 반영해 달라고 **PR(Pull Request)로 제안**할 수 있어요.

### 기업에서 선호하지 않는 이유?

Fork 방식은 **공개 오픈소스 협업**에는 적합하지만, **기업 환경에서는 다음과 같은 이유로 기피**되기도 해요.

#### 주요 이유

1. **보안 문제**
   - fork된 저장소는 외부 계정에 속해 있고, 민감한 코드가 외부로 나가 관리되면 보안 리스크가 있음
2. **통제 어려움**
   - 여러 명이 fork 후 각자 관리하면, 변경 사항을 추적하고 통제하기 어려움
3. **CI/CD 연결 어려움**
   - fork된 저장소는 기업 내부의 CI/CD 시스템과 쉽게 연동되지 않음
4. **권한 관리의 비일관성**
   - 공식 저장소는 조직 내 권한 체계로 관리되지만, fork는 외부 환경이기 때문에 코드 유출 가능성 존재

> 그래서 기업 내부에서는 일반적으로 feature 브랜치를 사용한 PR 방식을 더 선호합니다.
>
> 반면 오픈소스는 외부 누구나 참여할 수 있어야 하므로 Fork + PR 구조가 더 적합합니다.

### Fork 기반 워크플로우

아래는 일반적인 **Fork 기반 협업 흐름**입니다:

1. **원본 저장소 Fork**
   - GitHub에서 `Fork` 버튼 클릭 → 내 GitHub 계정에 복사됨
2. **Fork된 저장소를 내 컴퓨터에 복사해 작업 시작**

   ```bash
   git clone https://github.com/내계정명/Fork레포이름.git
   cd 프로젝트명
   ```

3. **원본 저장소를 upstream으로 추가**

   ```bash
   git remote add upstream https://github.com/원본계정/Fork레포이름.git
   ```

4. **기능 개발 (브랜치 생성 → 작업 → 커밋)**

   ```bash
   git checkout -b feature/my-awesome-feature
   # 수정 후
   git add .
   git commit -m "feat: 오타 수정"
   ```

5. 내 GitHub(Fork)에 Push하기

   ```bash
   git push origin feature/my-awesome-feature
   ```

6. **GitHub에서 Pull Request 생성**

   GitHub 웹에서 PR(Pull Request) 만들기

   공식 저장소의 main 브랜치에 내 코드 반영 요청

   - base: `원본 저장소`
   - compare: `내 브랜치`

7. **(선택) 원본 저장소 최신 내용 가져오기**

   ```bash
   git fetch upstream
   git checkout main
   git merge upstream/main   # 또는 git rebase upstream/main
   ```

## Git Clone이란?

> GitHub에 있는 저장소를 내 컴퓨터로 복사하는 것이에요.
>
> 복사된 폴더는 단순한 파일 모음이 아니라, **Git으로 관리되는 저장소**입니다.

즉, 클론하면:

- 코드도 가져오고,
- 커밋 기록도 가져오고,
- 리모트(origin) 정보도 같이 저장돼요.

## Clone 후 확인할 수 있는 정보

```bash
git remote -v
```

→ 어떤 원격 저장소(GitHub)와 연결되어 있는지 확인할 수 있어요. 보통 `origin`이라는 이름으로 저장돼 있어요.

## Clone 방식 – 2가지

GitHub에서 Clone 버튼을 누르면 보통 **HTTPS**와 **SSH** 두 가지 방식이 있어요.

### 1. HTTPS

> URL로 접속하는 가장 기본적인 방식

| 특징        | 설명                                                            |
| ----------- | --------------------------------------------------------------- |
| 암호화 방식 | HTTPS (443번 포트 사용)                                         |
| 설정        | 따로 설정 필요 없음                                             |
| 인증        | 예전엔 ID/비번 → 지금은 **PAT 토큰** 필요                       |
| 장점        | 어디서든 잘 통함 (방화벽에 잘 안 막힘)                          |
| 단점        | 로그인 인증이 번거롭고, 도청 가능성 있음 (URL에 토큰 노출 위험) |

### 보호된 리포지토리를 HTTPS로 클론 하는 방법

📌 **GitHub는 2021년부터 ID/비번 방식 막힘**

→ 이제는 **Personal Access Token (PAT)** 사용해야 해요.

### PAT 로 클론하는 방법

1. GitHub → `Settings` → `Developer settings` → `Tokens (classic)`
2. `Generate new token (classic)`
   - scope에서 `workflow`, `repo` 등 체크
3. 생성된 토큰 복사
4. 터미널에서 다음과 같이 클론

```bash
git clone https://x-access-token:PAT토큰값@github.com/계정명/저장소명.git
```

### 2. SSH

> 내 컴퓨터에 있는 비밀 열쇠(Private Key)로 GitHub 문을 여는 방식

| 특징        | 설명                                                   |
| ----------- | ------------------------------------------------------ |
| 암호화 방식 | SSH (22번 포트 사용)                                   |
| 인증        | **키 페어**로 인증 (Private + Public Key)              |
| 장점        | 한 번 설정해두면 로그인 없이 편함                      |
| 단점        | 키 생성, 등록 과정이 번거롭고, 방화벽에서 막힐 수 있음 |

### SSH 설정하는 방법 (처음 1회만 하면 됨)

1. 키 생성

```bash
ssh-keygen -t ed25519 -C "you@example.com"
```

→ 경로: 기본값이면 그냥 Enter 3번

1. 공개 키 확인

```bash
cat ~/.ssh/id_ed25519.pub
```

1. GitHub에 등록

- GitHub → `Settings` → `SSH and GPG keys`
- `New SSH Key` 클릭 → 복사한 공개 키 붙여넣기

1. SSH로 클론

```bash
git clone git@github.com:계정명/저장소명.git
```

## 언제 뭘 쓰면 좋을까?

| 상황                                        | 추천 방식 |
| ------------------------------------------- | --------- |
| 한 번만 클론할 때, 설정 귀찮을 때           | HTTPS     |
| 자주 push/pull 할 프로젝트, 보안 신경 쓸 때 | SSH       |

## Clone 정리

| 항목           | HTTPS                       | SSH                        |
| -------------- | --------------------------- | -------------------------- |
| 인증 방식      | PAT 토큰 (예전엔 ID/비번)   | 공개키 + 개인키            |
| 설정 필요 여부 | 없음                        | 초기에 키 생성 필요        |
| 편리성         | 처음엔 쉬움                 | 한 번 설정해두면 더 편함   |
| 보안성         | URL에 토큰이 노출될 수 있음 | 더 안전                    |
| 포트           | 443 (잘 안 막힘)            | 22 (방화벽에 막힐 수 있음) |

## Fork vs Clone, 헷갈릴 수 없음

| 구분        | Fork                                                 | Clone                                                       |
| ----------- | ---------------------------------------------------- | ----------------------------------------------------------- |
| 뜻          | GitHub에서 **내 계정에 복사본 저장소를 만드는 것**   | GitHub 저장소를 **내 컴퓨터로 복사**하는 것                 |
| 저장 위치   | GitHub (웹 상의 내 계정)                             | 내 컴퓨터 (로컬)                                            |
| 목적        | 수정 권한이 없는 저장소를 **내 소유로 만들기 위해**  | 작업을 시작하기 위해 **코드를 내려받기 위해**               |
| 대표 명령어 | GitHub에서 "Fork" 버튼 클릭                          | `git clone <URL>`                                           |
| 쓰는 상황   | 오픈소스 기여 시 / 권한 없는 저장소 수정하려고 할 때 | 협업 중인 저장소 내려받기 / Fork한 내 저장소 작업 시작할 때 |
