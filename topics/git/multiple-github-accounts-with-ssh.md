# 재택근무용 GitHub 계정 설정 가이드

## 1. 목표

- 개인용 GitHub 계정과 회사용 GitHub 계정을 한 컴퓨터에서 동시에 사용
- 각 계정에 맞는 SSH 키를 생성하고 충돌하지 않도록 Git에서 자동으로 선택하도록 설정

즉, 집 컴퓨터에서도 회사 GitHub 계정으로 안전하게 작업할 수 있도록 SSH 설정을 추가하는 것

## 2. SSH 키 생성

SSH 키는 GitHub에 로그인할 때 비밀번호 대신 사용하는 “디지털 열쇠”입니다.
집 컴퓨터에는 아직 회사 계정용 열쇠가 없기 때문에 하나 새로 만들어야 합니다.

### 회사 계정용 SSH 키 생성

```bash
ssh-keygen -t ed25519 -C "you@company.com"
```

- `ssh-keygen`: SSH 키를 만드는 명령어
- `t ed25519`: 키의 종류. ed25519는 최신/빠르고 안전한 알고리즘
- `C`: 주석(comment)처럼 붙이는 이름. 보통 이메일 주소를 씁니다.

메시지가 뜨면 다음과 같이 입력:

```bash
Enter file in which to save the key (/Users/yourname/.ssh/id_ed25519):
> /Users/yourname/.ssh/id_ed25519_company
```

이건 키 파일을 어떤 이름으로 저장할지 물어보는 거예요.

기존 개인 키(`id_rsa`)와 겹치지 않게 `id_ed25519_company` 라고 입력해줍니다.

이후 질문에서는 비밀번호를 설정할 수 있습니다.

```bash
Enter passphrase for "/Users/zimab/.ssh/id_ed25519_company" (empty for no passphrase):
```

비밀번호를 생략하고 싶다면 Enter를 눌러도 됩니다.

생성되면 다음 두 파일이 생깁니다.

- `~/.ssh/id_ed25519_company` (비밀키)
- `~/.ssh/id_ed25519_company.pub` (공개키)

### 최총 SSH 키 구성

| 용도             | 경로 (예시)                                    | 설명                       |
| ---------------- | ---------------------------------------------- | -------------------------- |
| 개인 GitHub 계정 | `~/.ssh/id_ed25519` 또는 `id_ed25519_personal` | 평소 쓰던 개인용 GitHub 키 |
| 회사 GitHub 계정 | `~/.ssh/id_ed25519_company`                    | 회사 프로젝트용 별도 키    |

### 구조 예시

`~/.ssh` 디렉토리에 이런 구성:

```bash
.ssh/
├── id_ed25519                  // 개인용 키 (기존 사용 중)
├── id_ed25519.pub
├── id_ed25519_company          // 새로 만든 회사용 키
├── id_ed25519_company.pub
├── config                      // 계정 구분을 위한 설정 파일
```

## 3. `~/.ssh/config` 파일 설정

이 파일을 통해 **회사 계정과 개인 계정을 구분**할 수 있습니다.

```bash
nano ~/.ssh/config
```

### 아래 내용 추가

```bash
# 개인 계정 (기본값)
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa

# 회사 계정
Host github-company
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_company
```

※ `Host github-company`는 나중에 `git@github-company:...` 형식으로 clone할 때 사용됩니다.

### 깃 명령어 차이

- `git clone git@github.com:...` → 개인 계정으로 인증됨
- `git clone git@github-company:...` → 회사 계정으로 인증됨

### 저장 및 종료

- `Ctrl + O` → 저장
- `Enter` → 확인
- `Ctrl + X` → 종료

## 4. 권한 설정

```bash
chmod 600 ~/.ssh/config
```

## 5. GitHub 회사 계정에 공개키 등록

1. 회사 GitHub에 로그인 (https://github.com/settings/keys)
2. **Settings → SSH and GPG keys → New SSH key**
3. **Title**: `Home PC SSH` (어떤 이름이든 OK)
4. **Key**: `id_ed25519_company.pub` 파일 내용을 복사해 붙여넣기

   ```bash
   cat ~/.ssh/id_ed25519_company.pub
   ```

   - `cat`은 파일 내용을 출력해주는 명령어입니다.
   - `.pub` 파일은 **공개키**이고, 이걸 GitHub에 붙여넣어야 합니다.

   이렇게 생긴 줄이 나오면 이 줄 전체를 **마우스로 복사**합니다**.**

   ```bash
   ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBYxxxxxxxxxxxx you@company.com
   ```

5. [Add SSH key] 클릭

## 6. Clone 시에는 Host 별칭 사용

회사 저장소를 clone할 때는 아래처럼 **별칭(host)** 을 사용합니다:

```bash
git clone git@github-company:회사계정이름/레포이름.git
```

예를 들어 회사에서 `github.com/zimablue14/migrate-cms-react` 저장소가 있다면:

```bash
git clone git@github-company:zimablue14/migrate-cms-react.git
```

원래는 이런 명령어:

```bash
git clone git@github.com:zimablue14/migrate-cms-react.git
```

## 보너스: 회사 프로젝트에는 회사 이름/이메일 설정

```bash
cd ~/company-project
git config user.name "Your Company Name"
git config user.email "you@company.com"
```
