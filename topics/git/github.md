# Github

Github은 깃 저장소를 위한 호스팅 플랫폼입니다.\
깃허브를 사용하면 클라우드에 깃 저장소를 넣을 수 있습니다.

깃은 로컬 컴퓨터에서 실행하는 버전 제어 시스템입니다.\
인터넷이 필요하지 않으며 계정도 필요하지 않습니다.

반면 깃허브는 웹 사이트입니다.\
깃 저장소를 위한 호스팅 플랫폼입니다.\
접근하려면 인터넷과 계정이 필요합니다.\
깃 저장소를 위한 호스팅 플랫폼입니다.

## `git clone <url>`

git clone 명령은 깃허브의 일부가 아니라 깃의 일부입니다.\
git clone은 여러분의 컴퓨터에 없는 저장소를 컴퓨터로 가져옵니다.\
클론하는 프로젝트의 깃 전체 기록에 접근할 수 있습니다.

url만 안다면 저장소를 컴퓨터에 클론할 수는 있습니다.\
하지만 푸시하는 것은 허용되지 않습니다.

## SSH Keys

Secure Shell의 약자로 이 프로토콜을 사용하면, 매번 이메일이나 사용자 이름과 비밀번호를 쓰지 않고도 인증할 수 있습니다.\
SSH 키 중 하나를 생성한 다음, 깃허브에 알려주면 됩니다.

[Connecting to GitHub with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)

### SSH Key 유무 확인

`~/.ssh` 폴더 안에 어떤 SSH 관련 파일들이 있는지 확인하는 명령어입니다.

```bash
ls -al ~/.ssh
```

아래의 파일 중 하나도 없다면 아직 SSH Key가 없다는 의미입니다.

- `id_rsa.pub`
- `id_ecdsa.pub`
- `id_ed25519.pub`

### SSH Key 생성

`your_email@example.com`에 깃헙 이메일을 입력하여 SSH Key를 생성합니다.

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

키를 생성하면 저장할 파일을 요청합니다.\
원한다면 파일 이름을 만들 수도 있지만 일반적으로는 엔터를 눌러서 기본 주소를 사용합니다.

이후 비밀번호를 입력하고, 비밀번호 확인을 합니다.\
비밀번호 입력하지 않고 엔터를 누르면 비밀번호 없이 사용하게 됩니다.

### SSH 키를 SSH 에이전트에 추가

#### 1. ssh-agent 실행

```bash
eval "$(ssh-agent -s)"
```

- SSH 에이전트를 백그라운드에서 실행합니다.
- 환경에 따라 다음 명령어를 사용할 수도 있습니다:
  - `sudo -s -H`
  - `exec ssh-agent bash` 또는 `exec ssh-agent zsh`ㅇ

#### 2. 설정 파일 만들기 (`~/.ssh/config`)

macOS에서는 패스워드를 저장하려면 설정 파일이 필요합니다.

`config` 파일을 열어서 확인합니다.

```bash
open ~/.ssh/config
```

##### (1) config 파일이 없다면 생성

```bash
touch ~/.ssh/config
```

##### (2) 있다면 아래 내용 추가

```text
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

- `UseKeychain`: macOS 키체인에 패스프레이즈 저장 (패스워드를 안 만들었으면 `UseKeychain`은 생략해도 됩니다.)
- `IdentityFile`: 사용할 SSH 키의 경로입니다.

> 에러가 난다면 아래처럼 `IgnoreUnknown`을 먼저 추가해보세요:

```text
Host github.com
  IgnoreUnknown UseKeychain
```

#### 3. SSH 키를 ssh-agent에 등록하고 키체인에 저장

```bash
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

- 이 명령어는 패스워드를 macOS 키체인에 저장해줍니다.
- 패스워드를 만들지 않았다면 `--apple-use-keychain` 없이 실행해도 됩니다.

> macOS Monterey(12.0) 이전이라면 `-K` 옵션을 사용해야 할 수 있어요.

#### 4. 공개키를 GitHub에 등록

1. `~/.ssh/id_ed25519.pub` 파일을 열고 내용을 복사합니다.

2. GitHub 웹사이트에 들어갑니다:
   - Settings → SSH and GPG Keys
   - [New SSH key] 버튼 클릭
   - 복사한 키를 붙여넣고 저장

#### (선택) 자동 등록을 위해 `.zshrc` 또는 `.bashrc`에 명령 추가

`.zshrc` 또는 `.bashrc` 파일에 아래 내용을 추가하면 됩니다:

```bash
eval "$(ssh-agent -s)"
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
```

이제 Mac을 켤 때마다 자동으로 SSH 키가 등록됩니다.

## Git 원격 저장소(remote)

Git에서 `remote`는 원격 저장소의 주소(URL)에 붙인 이름(별칭)입니다.\
보통은 한 프로젝트에서 여러 저장소를 다루기 때문에, URL마다 별칭을 붙여 관리합니다.

### 1. 원격 저장소 확인하기

등록된 원격 저장소 이름을 확인합니다:

```bash
git remote
```

이름과 연결된 URL(주소), 방향(fetch/push)을 함께 보려면:

```bash
git remote -v
```

예시 출력:

```bash
origin    git@github.com:zimablue14/my-app.git (fetch)
origin    git@github.com:zimablue14/my-app.git (push)
upstream  git@github.com:company/my-app.git   (fetch)
upstream  git@github.com:company/my-app.git   (push)
```

#### origin과 upstream

Git에서는 원격 저장소에 별칭(alias)을 붙여서 관리합니다.\
가장 자주 쓰이는 별칭은 origin과 upstream입니다.

- `origin`: 보통 내가 처음 클론하거나 직접 만든 저장소입니다.
- `upstream`: 내가 fork(복사)해온 원본 저장소입니다.\
  회사나 오픈소스 프로젝트의 공식 저장소가 이에 해당하며, 최신 변경 사항을 가져오는 용도로 사용합니다.

> 참고: `fetch`, `push`는 로컬 ↔ 원격 간 데이터 흐름 방향입니다.

### 2. 원격 저장소 추가하기

```bash
git remote add <별칭> <URL>
```

예시:

```bash
git remote add origin https://github.com/zimablue14/repo.git
git remote add upstream https://github.com/company/repo.git
```

### 3. 원격 저장소 수정 및 삭제

#### URL 수정

```bash
git remote set-url <별칭> <새로운 URL>
```

#### 이름 변경

```bash
git remote rename <old> <new>
```

#### 삭제

```bash
git remote remove <별칭>
```

### 4. `origin`과 `upstream`이 왜 필요한가요?

- `origin`: 내가 fork(복사)하거나 직접 만든 저장소 → 여기에 push함.
- `upstream`: 원본 저장소. 회사, 오픈소스 등 최신 변경사항을 가져오기 위해 fetch/rebase 용도로 사용.

#### 예시 상황

```bash
# origin에는 내 계정 저장소가 연결되어 있음
# upstream은 회사 저장소 (원본)

origin    git@github.com:zimablue14/my-app.git (push)
upstream  git@github.com:company/my-app.git   (fetch)
```

> 이럴 땐 upstream에서 최신 코드를 가져오고(fetch),  
> 작업이 끝난 코드는 내 origin으로 push합니다.

### 5. fetch와 push는 어떤 역할인가요?

| 명령어 | 방향 | 설명 |
| | ----- | ------------------------------------------- |
| fetch | 원격 → 로컬 | 원격 저장소의 최신 변경사항을 가져오기만 함 |
| push | 로컬 → 원격 | 내 로컬 커밋을 원격 저장소로 보냄 |

### 요약

- `git remote -v`: 원격 저장소 목록 확인
- `git remote add`: 원격 저장소 연결
- `origin`: 내 저장소 / `upstream`: 원본 저장소
- fetch는 가져오기, push는 보내기
- 실무에서는 업데이트는 upstream에서 fetch, 작업 결과는 origin에 push합니다.

### 참고 명령어 정리

```bash
# 원격 저장소 확인
git remote -v

# 원격 저장소 추가
git remote add origin https://github.com/zimablue14/repo.git

# 원격 저장소 URL 변경
git remote set-url origin https://new-url.com

# 원격 저장소 삭제
git remote remove origin
```

## Git Push

내 로컬 저장소의 변경 내용을 가리키고 있는 원격 저장소에 업로드하는 Git 명령어입니다.

가리키고 있는 원격 저장소가 여러개인 경우 어디로 푸시할지, 대상 저장소 및 푸시할 브랜치를 알려줘야 합니다.

```bash
git push <remote> <branch>

git push origin main
```

가리키고 있는 원격 저장소가 하나인 경우 `git push`만 입력해도 됩니다.

## Github Repo 만들기

### 컴퓨터에 기존 저장소가 있는 경우

1. Github에 빈 저장소를 만듭니다.

2. 빈 저장소를 원격 저장소로 추가합니다.("https://..." 주소의 레포지토리를 origin이란 별칭으로 추가함)

   ```bash
   git remote add origin https://...
   ```

3. 로컬의 변경사항을 커밋합니다.\
   (아직 커밋하지 않았다면 먼저 커밋합니다)

   ```bash
   git add .
   git commit -m "초기 커밋"
   ```

4. 원격 저장소에 푸시합니다.

   ```bash
   git push -u origin main
   ```
