# Git Config

Git을 사용할 때 꼭 알아야 할 설정 명령어와 자격 증명 저장 방식(Credential Helper)에 대해 정리한 문서입니다.

## 1. Git 설정 보기 (Config 확인)

Git은 설정 정보를 여러 곳에서 관리합니다. 현재 설정된 값을 확인하는 방법은 아래와 같습니다:

```bash
cat ~/.gitconfig                     # 사용자 전체 설정 (글로벌)
git config --global --list          # 글로벌 설정 목록 보기
git config --list                   # 로컬 + 글로벌 설정 모두 보기

```

## 2. Git 설정 추가/수정

Git 설정은 **글로벌(Global)** 또는 **로컬(Local)** 범위로 저장할 수 있습니다.

- **글로벌 설정**: 컴퓨터 전체 사용자에게 적용 (`~/.gitconfig`)
- **로컬 설정**: 특정 프로젝트(리포지토리)에만 적용 (`.git/config`)

```bash
git config --global <프로퍼티 이름> <값>     # 글로벌 설정
git config <프로퍼티 이름> <값>             # 현재 프로젝트에만 설정

```

### Git 설정 추가/수정 예시

```bash
git config --global user.name "홍길동"
git config --global user.email "hong@example.com"

```

## 3. Git 설정 삭제 (unset)

설정한 값을 제거하고 싶을 때는 `--unset` 명령어를 사용합니다.

```bash
git config --unset --global <프로퍼티 이름>     # 글로벌 설정 제거
git config --unset <프로퍼티 이름>              # 로컬 설정 제거

```

### Git 설정 삭제 (unset) 예시

```bash
git config --unset --global user.email

```

## 4. 꼭 설정해두면 좋은 Git 기본 정보

```bash
git config --global user.name "내 이름"
git config --global user.email "내 깃허브 이메일"

```

이 설정은 커밋할 때 누가 커밋했는지를 기록해주는 정보입니다.

협업 시 꼭 필요합니다.

## 5. Git Credential Helper란?

> 원격 저장소(GitHub 등)에 접속할 때 사용하는 비밀번호나 토큰을 기억해주는 도구입니다.

Git은 매번 비밀번호를 물어보지 않도록 자격 증명 정보를 저장할 수 있는데, 방식은 아래와 같이 4가지가 있습니다.

### ① `cache` (일시 저장)

- 메모리에 잠깐 저장 (기본 15분)
- 시간 설정 가능

```bash
git config --global credential.helper cache
git config --global credential.helper "cache --timeout=3600"  # 1시간 유지

```

### ② `store` (파일로 저장)

- 평문으로 디스크에 저장 (`~/.git-credentials`)
- 터미널에 한 번 입력한 사용자 정보를 파일에 저장해서 다음부터 자동 입력됨

```bash
git config --global credential.helper store

```

📁 예시 저장 위치:

```plain
<https://username:token@github.com>
```

⚠️ **보안에 주의하세요.**

### ③ `osxkeychain` (Mac 전용)

- macOS의 **Keychain** 기능을 사용해 보안 저장

```bash
git config --global credential.helper osxkeychain

```

### ④ `manager` (Windows 전용)

- Windows Credential Manager와 연동

```bash
git config --global credential.helper manager

```

## 요약

| 항목                      | 설명                            |
| ------------------------- | ------------------------------- |
| `git config`              | Git 설정 추가/수정/삭제         |
| `user.name`, `user.email` | 커밋 작성자 정보                |
| `credential.helper`       | 비밀번호를 어떻게 기억할지 설정 |
| `cache`                   | 메모리에 임시 저장 (기본 15분)  |
| `store`                   | 파일에 저장 (보안 주의)         |
| `osxkeychain`             | Mac 보안 저장소                 |
| `manager`                 | Windows 자격 증명 관리자        |
