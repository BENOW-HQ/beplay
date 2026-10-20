<div align="center">

# beplay

### 말로 만들고, 확인하고, 함께 쓰는 업무 앱

Codex CLI와 Claude Code에서 사용하는 **BENOW Playground 앱 제작 도구**

[빠른 시작](#빠른-시작) · [사용 예시](#이렇게-요청하세요) · [설치 옵션](#설치-옵션) · [릴리스](https://github.com/BENOW-HQ/beplay/releases) · [보안 정책](SECURITY.md)

</div>

---

```text
“우리 팀 체크리스트를 만들어줘.
 할 일을 추가하고 완료 표시할 수 있게 해줘.”
```

요구사항을 설명하면 AI가 HTML 앱을 만들고, 미리보기와 동작 검사를 진행합니다.
게시를 요청하면 회사 계정으로 로그인한 Playground에서 같은 앱을 계속 수정하고 사용할 수 있습니다.

| 만들기 | 확인하기 | 이어서 쓰기 |
| :--- | :--- | :--- |
| 자연어로 앱의 목적과 기능 설명 | 합성 데이터로 동작 검사·미리보기 | 게시한 앱을 같은 ID로 수정 |
| 요구사항과 작업 세션 저장 | 화면·썸네일·게시 전 검사 | 기존 작업과 게시 결과를 이어서 확인 |

> [!NOTE]
> **도구 설치는 공개, 회사 앱 접근은 로그인 후.**
> 회사 앱 조회·수정·게시에는 회사 Google 로그인과 앱별 권한이 필요합니다.
> 설치 묶음에는 회사 데이터나 인증 자격증명이 포함되지 않습니다.

## 빠른 시작

### 1. 준비하기

- **Bun 1.4 이상** — `bun --version`으로 확인
- 사용할 **Codex CLI** 또는 **Claude Code**
- macOS / POSIX 셸의 `curl`, `unzip`, `shasum` 또는 `sha256sum`

설치기는 Bun이나 AI 호스트를 자동 설치하지 않습니다. Windows는 [아래 수동 설치](#windows--수동-설치)를 사용하세요.

### 2. 작업 폴더에서 한 줄 설치

앱 파일을 보관할 폴더로 이동한 뒤 실행합니다.

```sh
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/install.sh | sh
```

**Codex CLI와 Claude Code 스킬을 한 번에 설치합니다.** 관리자 권한은 필요하지 않으며, 전역 PATH나 셸 설정은 바꾸지 않습니다.

| 호스트 | 현재 작업 폴더에 생성되는 스킬 |
| :--- | :--- |
| Codex CLI | `.agents/skills/playground-app/` |
| Claude Code | `.claude/skills/playground-app/` |

### 3. AI에게 요청하기

같은 폴더에서 `codex` 또는 `claude`를 새로 실행하고 **대화창**에 입력하세요.

**Codex CLI**

```text
$playground-app beplay create 우리 팀 체크리스트 앱을 만들어줘. 할 일을 추가하고 완료 표시할 수 있게 해줘. 검사와 미리보기까지 진행하고 아직 게시하지 마.
```

**Claude Code**

```text
/playground-app beplay create 우리 팀 체크리스트 앱을 만들어줘. 할 일을 추가하고 완료 표시할 수 있게 해줘. 검사와 미리보기까지 진행하고 아직 게시하지 마.
```

도움말은 각각 `$playground-app beplay help`, `/playground-app beplay help`로 확인할 수 있습니다.

## 이렇게 요청하세요

스킬이 선택된 대화에서는 `beplay` 또는 `be-play`로 이어서 요청할 수 있습니다.

| 하고 싶은 일 | 대화창에 입력할 예시 |
| :--- | :--- |
| 요구사항만 정리 | `beplay interview 팀 체크리스트 요구사항을 정리해줘. 아직 구현하지 마.` |
| 앱 만들기 | `beplay create 정리한 요구사항으로 앱을 만들고 검사해줘.` |
| 화면 확인 | `beplay preview 지금 작업 중인 앱을 보여줘.` |
| 게시 | `beplay publish 확인한 앱을 첫 버전으로 올려줘.` |
| 내 앱 찾기 | `beplay list 내 앱에서 체크리스트를 찾아줘.` |
| 기존 앱 수정 | `beplay update 이 앱에 담당자 기능을 추가해줘.`와 앱 URL 전달 |
| 작업 상태 확인 | `beplay status 검사와 게시 상태를 알려줘.` |
| 앱 보관·복원 | `beplay delete 이 앱을 보관해줘.` / `beplay restore 이 앱을 복원해줘.`와 앱 URL 전달 |

> [!TIP]
> `interview`는 요구사항을 저장하고 멈춥니다. `create`와 `update`는 제작·검사를 진행하며, **게시를 요청하기 전에는 올리지 않습니다.** `delete`는 영구 삭제가 아닌 보관입니다.

위 예시는 셸 명령이 아닌 **AI 대화창 입력**입니다. 스킬이 선택되지 않으면 호스트에 맞는 `$playground-app` 또는 `/playground-app`을 앞에 붙이세요.

## 게시와 이어서 작업하기

1. 미리보기와 검사를 마친 뒤 AI에게 게시를 요청합니다.
2. 회사 계정으로 로그인한 브라우저에서 게시 파일을 선택하고 결과를 확인합니다.
3. 게시 결과를 작업 세션에 기록하면 다음 수정도 같은 앱으로 이어집니다.

AI 브라우저 도구가 로컬 파일 업로드·다운로드를 지원하면 게시 흐름을 연결할 수 있습니다. 지원하지 않는 환경에서는 파일 선택과 게시 결과 전달을 직접 진행하세요. **비밀번호나 브라우저 쿠키를 CLI로 복사하지 마세요.**

새 대화에서도 같은 작업 폴더를 열어 이어서 요청하세요. 폴더를 옮길 때는 앱 파일과 `.beplay` 세션을 함께 보관합니다. 로컬 미리보기는 합성 데이터 검사이며 실제 회사 데이터의 저장·권한 검증을 대신하지 않습니다.

## 설치 옵션

<details>
<summary><strong>다른 폴더에 설치하거나 한 호스트만 선택하기</strong></summary>

대상 폴더는 먼저 만들어 두세요. 공백이 있는 경로는 따옴표로 감쌉니다.

```sh
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/install.sh | sh -s -- --workspace '/path/to/workspace' --host codex
```

| 옵션 | 기본값 | 선택 가능한 값 |
| :--- | :--- | :--- |
| `--workspace` | 현재 폴더 | 기존 작업 폴더 경로 |
| `--host` | `all` | `all`, `codex`, `claude` |
| `--operation` | `install` | `install`, `update`, `uninstall` |

</details>

<details>
<summary><strong>업데이트·제거하기</strong></summary>

업데이트할 때는 [Releases](https://github.com/BENOW-HQ/beplay/releases)에서 **새 버전의 설치 스크립트 URL**을 사용하고 `| sh -s -- --operation update`를 전달하세요. 같은 버전의 같은 묶음은 재설치해도 유지됩니다.

v0.3.0 제거는 설치한 작업 폴더에서 실행합니다.

```sh
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/install.sh | sh -s -- --operation uninstall
```

기본 대상은 두 호스트입니다. 하나만 제거하려면 `--host codex` 또는 `--host claude`를 추가하세요.

앱 소스와 요구사항 세션은 보존합니다. 직접 수정한 스킬 파일은 강제로 덮어쓰거나 삭제하지 않으며, 충돌이 있으면 중단합니다.

</details>

### Windows / 수동 설치

[v0.3.0 ZIP 다운로드](https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/playground-harness-0.3.0.zip) 후 PowerShell에서 실행합니다. `--workspace`에는 앱을 만들 작업 폴더를 지정하세요.

```powershell
Expand-Archive .\playground-harness-0.3.0.zip .\playground-harness
bun .\playground-harness\beplay.js setup --operation install --workspace . --host all --source .\playground-harness --json
```

Bun 1.4 이상이 필요하며, Windows 미리보기·썸네일에는 로컬 Chrome 또는 Chromium이 필요합니다. 설치 후 AI 대화창의 호출 방법은 동일합니다.

## 배포와 보안

- **버전 고정 설치** — 설치 스크립트가 ZIP의 SHA-256을 확인합니다.
- **변경 불가 릴리스** — 발행한 파일은 교체하지 않고 새 버전으로 배포합니다.
- **권한 분리** — 공개 설치와 회사 앱 로그인·접근 권한은 별개입니다.
- **비공개 제보** — 취약점은 [보안 정책](SECURITY.md)에 따라 비공개로 알려 주세요.

GitHub CLI가 해당 명령을 지원하는 환경에서는 릴리스 증명도 확인할 수 있습니다.

```sh
gh release verify v0.3.0 --repo BENOW-HQ/beplay
```

---

<div align="center">

**BENOW Playground** · [릴리스 보기](https://github.com/BENOW-HQ/beplay/releases) · [보안 정책](SECURITY.md)

</div>
