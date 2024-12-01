# 사용 방법

## 새로운 훅 추가하기

훅을 추가하는 방법은 파일을 생성하는 것만큼 간단합니다. 선호하는 편집기, 스크립트, 또는 기본 `echo` 명령어를 사용하여 수행할 수 있습니다. 예를 들어, Linux/macOS에서는 다음과 같이 작성합니다:

```shell
echo "npm test" > .husky/pre-commit
```

## 초기화 파일

Husky는 훅을 실행하기 전에 로컬 명령을 실행할 수 있는 기능을 제공합니다. 다음 파일에서 명령을 읽습니다:

- `$XDG_CONFIG_HOME/husky/init.sh`
- `~/.config/husky/init.sh`
- `~/.huskyrc` (더 이상 권장되지 않음)

Windows에서는: `C:\Users\yourusername\.config\husky\init.sh`

## Git 훅 건너뛰기

### 단일 명령에 대해

대부분의 Git 명령에는 훅을 건너뛸 수 있는 `-n/--no-verify` 옵션이 포함되어 있습니다:

```sh
git commit -m "..." -n # Git 훅 건너뛰기
```

이 플래그가 없는 명령의 경우, 다음과 같이 `HUSKY=0`을 사용해 훅을 임시로 비활성화할 수 있습니다:

```shell
HUSKY=0 git ... # 모든 Git 훅을 임시로 비활성화
git ... # 훅이 다시 실행됩니다
```

### 여러 명령에 대해

긴 작업(예: rebase/merge) 동안 훅을 비활성화하려면 다음을 사용하세요:

```shell
export HUSKY=0 # 모든 Git 훅 비활성화
git ...
git ...
unset HUSKY # 훅을 다시 활성화
```

### GUI나 전역 설정에 대해

GUI 클라이언트나 전역적으로 Git 훅을 비활성화하려면 husky 설정을 수정하세요:

```sh
# ~/.config/husky/init.sh

export HUSKY=0 # Husky가 설치되지 않으며, 훅이 실행되지 않음
```

## CI 서버 및 Docker

CI 서버나 Docker에서 Git 훅 설치를 방지하려면 `HUSKY=0`을 사용하세요. 예를 들어, GitHub Actions에서는 다음과 같이 설정할 수 있습니다:

```yml
# https://docs.github.com/en/actions/learn-github-actions/variables

env:
  HUSKY: 0
```

`dependencies`만 설치하고(`devDependencies`는 제외) 있을 경우, `"prepare": "husky"` 스크립트가 실패할 수 있습니다. 이는 Husky가 설치되지 않기 때문입니다.

여러 가지 해결 방법이 있습니다.

### `prepare` 스크립트를 실패하지 않도록 수정하기

```json
// package.json
"prepare": "husky || true"
```

위 방법은 출력에 `command not found`라는 오류 메시지를 남길 수 있습니다. 이를 조용히 처리하려면 `.husky/install.mjs` 파일을 생성하세요:

```js
// .husky/install.mjs

// 프로덕션 환경 및 CI에서 Husky 설치 건너뛰기
if (process.env.NODE_ENV === "production" || process.env.CI === "true") {
  process.exit(0);
}
const husky = (await import("husky")).default;
console.log(husky());
```

이후, `prepare`에 다음과 같이 사용하세요:

```json
"prepare": "node .husky/install.mjs"
```

## 커밋 없이 훅 테스트하기

훅을 테스트하려면 훅 스크립트에 `exit 1`을 추가하여 Git 명령을 중단시킬 수 있습니다:

```shell
# .husky/pre-commit

# 작업 중인 스크립트

# ...

exit 1
```

그 후, 다음과 같이 테스트합니다:

```shell
git commit -m "testing pre-commit code"

# 커밋이 생성되지 않습니다
```

## Git 루트 디렉터리가 아닌 프로젝트

Husky는 보안상의 이유로 상위 디렉터리(`../`)에 설치되지 않습니다. 하지만 `prepare` 스크립트에서 디렉터리를 변경할 수 있습니다.

예를 들어, 아래와 같은 프로젝트 구조를 고려하세요:

```graph
.
├── .git/
├── backend/ # package.json 없음
└── frontend/ # Husky가 포함된 package.json
```

`prepare` 스크립트를 다음과 같이 설정합니다:

```json
"prepare": "cd .. && husky frontend/.husky"
```

훅 스크립트에서 관련 하위 디렉터리로 디렉터리를 다시 변경합니다:

```shell
# frontend/.husky/pre-commit

cd frontend
npm test
```

## 비쉘(non-shell) 훅

스크립팅 언어가 필요한 스크립트를 실행하려면, 다음 패턴을 사용하세요:

### `pre-commit` 훅 및 NodeJS 예제

1. 훅의 엔트리포인트 생성:

   ```shell
   .husky/pre-commit
   ```

2. 파일에 다음 내용 추가:

   ```shell
   node .husky/pre-commit.js
   ```

3. `.husky/pre-commit.js` 파일에 NodeJS 코드를 작성합니다:

   ```javascript
   // NodeJS 코드 작성
   // ...
   ```

## Bash

훅 스크립트는 최적의 호환성을 위해 POSIX 준수여야 합니다. 모든 사용자가 `bash`를 사용하는 것은 아니기 때문입니다(예: Windows 사용자).

그렇긴 하지만, 팀에서 Windows를 사용하지 않는 경우 Bash를 다음과 같이 사용할 수 있습니다:

```shell
# .husky/pre-commit

bash << EOF
# Bash 스크립트를 여기에 작성
# ...
EOF
```

## Node 버전 관리자와 GUI

Node 버전 관리자(`nvm`, `n`, `fnm`, `asdf`, `volta` 등)를 사용하여 GUI에서 Git 훅을 실행할 경우, `PATH` 환경 변수 문제로 인해 `command not found` 오류가 발생할 수 있습니다.

### PATH와 버전 관리자 이해하기

`PATH`는 명령어를 찾기 위해 디렉터리 경로 목록을 포함하는 환경 변수입니다. `PATH`에 명령어가 없으면 `command not found` 오류가 발생합니다.

터미널에서 `echo $PATH`를 실행하여 현재 PATH를 확인할 수 있습니다.

버전 관리자는 다음과 같이 동작합니다:

1. 셸 시작 파일(`.zshrc`, `.bashrc` 등)에 초기화 코드를 추가하여 터미널이 열릴 때마다 실행됩니다.
2. Node 버전을 홈 폴더의 특정 디렉터리에 다운로드합니다.
   예를 들어, 두 가지 Node 버전이 있다면:

```shell
~/version-manager/Node-X/node
~/version-manager/Node-Y/node
```

터미널을 열면 버전 관리자가 초기화되어 `Node-Y`와 같은 버전을 선택하고, 해당 경로를 `PATH`에 추가합니다:

```shell
echo $PATH

# 출력

~/version-manager/Node-Y/:...
```

이제 `node`는 `Node-Y`를 참조합니다. `Node-X`로 전환하면 `PATH`도 변경됩니다:

```shell
echo $PATH

# 출력

~/version-manager/Node-X/:...
```

문제는 GUI가 터미널 외부에서 실행될 경우, 버전 관리자가 초기화되지 않아 `PATH`에 Node 설치 경로가 포함되지 않는 데 있습니다. 이로 인해 GUI에서 Git 훅이 실패할 수 있습니다.

### 해결 방법

Husky는 각 Git 훅 실행 전에 `~/.config/husky/init.sh`를 소스로 사용합니다. 버전 관리자 초기화 코드를 여기에 복사하여 GUI에서도 실행되도록 설정할 수 있습니다.

`nvm` 예제:

```shell
# ~/.config/husky/init.sh

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # nvm 초기화
```

또는, 셸 시작 파일이 빠르고 간단하다면 이를 직접 소스로 사용할 수도 있습니다:

```shell
# ~/.config/husky/init.sh

. ~/.zshrc
```

## 수동 설정(Manual setup)

Git을 설정하고 Husky가 `.husky/` 디렉터리에 파일을 생성하도록 해야 합니다.

리포지토리에서 한 번 `husky` 명령을 실행합니다. 이상적으로는 이를 `package.json`의 `prepare` 스크립트에 포함시켜 각 설치 후 자동으로 실행되도록 설정하는 것이 좋습니다(권장).

### npm 설정

`package.json`의 `scripts` 섹션에 다음을 추가하세요:

```json
{
  "scripts": {
    "prepare": "husky"
  }
}
```

### `prepare` 한 번 실행하기

```shell
npm run prepare
```

`.husky/` 디렉터리에 `pre-commit` 파일 생성:

```shell
# .husky/pre-commit

npm test
```
