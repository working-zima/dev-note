# 강의 학습 목표

글을 읽기 전에 개발 환경 세팅은 언제든 바뀔 수 있다는 것을 알고 계셔야 합니다.  
그렇기 때문에 앞으로 나오게 될 내용이 정상적으로 작동하지 않거나 효율적인 내용이 아닐 수 있습니다.  
따라서 구체적인 내용보다는 전체적인 흐름을 파악한다는 느낌으로 읽으시는 것을 추천드립니다.

우리는 `Node.js`를 설치하고, 프로젝트를 진행할 수 있는 `Node.js` 패키지를 만듭니다.  
코드 퀄리티를 일정 수준 이상으로 유지할 수 있도록 `lint`와 `test`를 실행할 수 있는 상태를 만듭니다.

## JavaScript 개발 환경 (Node.js) 세팅

[Node.js 홈페이지](https://nodejs.org/en)에는 크게 `LTS`와 `Current` 버전을 지원합니다.  
`LTS`버전은 앞의 숫자가 짝수이며 안정적이고 장기적으로 지원합니다.  
`Current`버전은 앞의 숫자가 홀수이며 가장 최신의 업데이트를 반영합니다.  
특별한 목적이 없다면 주로 이전`LTS`와 최신 `LTS` 버전을 사용하는 것이 무난하다고 알고 있습니다.

### 노드 버전을 관리해야하는 이유

개발자마다 서로 다른 노드 버전을 사용할 수 있기 때문에 버전을 변경하며 사용하야 하는 경우가 있습니다.  
노드 설치 또는 버전 관리는 `nvm`과 ` fnm`` 등에서 기능을 제공하고 있습니다.  
여기서는 `fnm`으로 노드 버전을 관리해보겠습니다.

### fnm 설치

#### Mac, Linux 사용자

[Homebrew](https://brew.sh/)를 이용해
`fnm`을 설치할 수 있습니다.

```bash
brew install fnm
```

`~/.bashrc` 또는 `~/.zshrc`에 다음을 추가합니다.

현재 터미널에서 바로 사용하고 싶다면 아래의 명령을 그대로 입력합니다.

```bash
eval "$(fnm env)"
```

#### Windows 사용자

Windows 사용자는
[Scoop](https://scoop.sh/) 또는
[Chocolatey](https://chocolatey.org/)를 사용해
`fnm`을 설치할 수 있습니다.

```bash
scoop install fnm

# 또는

choco install fnm
```

### Node.js 설치

설치 가능한 버전 확인.

```bash
fnm ls-remote
```

LTS(Long Term Support) 버전을 설치.

```bash
fnm install --lts
```

Node.js의 최신의 LTS 버전 버전을 현재 터미널 세션에서 사용할 수 있도록 설정.

```bash
fnm use lts-latest
```

현재 사용 중인 Node.js 버전을 `fnm`의 기본 버전으로 설정.

```bash
fnm default $(fnm current)
```

설치된 상태 확인.

```bash
fnm list

fnm current
```

### NPM 업그레이드

```bash
npm install -g npm
```

### 프로젝트 폴더 생성

프로젝트 이름을 `my-project`라고 했을 때 다음과 같이 폴더를 만들고
사용할 Node.js 버전을 잡아줍니다.  
fnm이 설정된 기본 버전으로 설정된 Node.js 버전을 현재 터미널 세션에서 사용하도록 지시합니다.

```bash
mkdir my-project
cd my-project
fnm use default
```

현재 사용 중인 Node.js 버전을 확인하고 그 버전 정보를 `.nvmrc` 파일에 저장.

```bash
echo "$(fnm current)" > .nvmrc
```

나중에 시스템에 설치된 Node.js 버전과 프로젝트에서 사용하는 Node.js 버전이
다른 상황이 오더라도 `fnm use` 명령을 통해 프로젝트에서 사용하고 있는 버전을
쉽게 사용할 수 있습니다.

또는 `.nvmrc` 파일을 확인함으로써 어떤 버전으로 개발했는지 알 수 있습니다.

```bash
cat .nvmrc
```

## TypeScript + React + Jest + ESLint + Parcel(번들러, 빌드 도구, 만능 도구) 개발 환경 세팅
