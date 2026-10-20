<div align="center">

# beplay

### 말로 만들고, 확인하고, 함께 쓰는 업무 앱

Codex CLI와 Claude Code에서 사용하는 **BENOW Playground 앱 제작 도구**

[macOS](#macos-설치) · [Windows](#windows-설치) · [For LLM](#for-llm) · [사용 예시](#이렇게-요청하세요) · [설치 옵션](#설치-옵션) · [릴리스](https://github.com/BENOW-HQ/beplay/releases) · [보안 정책](SECURITY.md)

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

**내 운영체제의 안내만 따라 하면 됩니다.** Codex CLI 또는 Claude Code는 먼저 설치해 두세요. beplay는 두 도구에 사용할 스킬을 설치합니다.

[macOS 설치](#macos-설치) · [Windows 설치](#windows-설치) · [설치 후 시작하기](#설치-후-시작하기) · [AI에게 설치 맡기기](#for-llm)

### macOS 설치

**① 터미널을 열고 Bun을 확인하세요.**

```sh
bun --version
```

`1.4.0` 이상이면 다음 단계로 넘어갑니다. Bun이 없다면 아래 [공식 설치 명령](https://bun.sh/docs/installation)을 실행한 뒤 터미널을 새로 열고 버전을 확인하세요. 이미 설치된 구버전은 기존 설치 방식으로 업데이트하세요.

```sh
curl -fsSL https://bun.com/install | bash
```

**② 앱을 만들 폴더에서 설치하세요.** 아래는 홈 폴더에 `beplay-workspace`를 만들고 두 스킬을 설치하는 예시입니다. 기존 프로젝트에 설치하려면 첫 두 줄 대신 그 폴더로 이동하세요.

```sh
mkdir -p "$HOME/beplay-workspace"
cd "$HOME/beplay-workspace"
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/install.sh | sh
```

마지막에 `beplay install complete`가 나오면 완료입니다. 설치 중 `curl`, `unzip`, `shasum` 누락이 표시되면 해당 도구를 준비한 뒤 같은 폴더에서 다시 실행하세요.

### Windows 설치

**① PowerShell을 열고 Bun을 확인하세요.** 명령 프롬프트(cmd)가 아닌 **PowerShell**에서 실행합니다.

```powershell
bun --version
```

`1.4.0` 이상이면 다음 단계로 넘어갑니다. Bun이 없다면 아래 [공식 설치 명령](https://bun.sh/docs/installation)을 실행한 뒤 PowerShell을 새로 열고 버전을 확인하세요. 이미 설치된 구버전은 기존 설치 방식으로 업데이트하세요.

```powershell
powershell -c "irm bun.sh/install.ps1|iex"
```

**② 아래 블록을 통째로 복사해 실행하세요.** 홈 폴더에 `beplay-workspace`를 만들고, 공개 ZIP 다운로드 → 체크섬 확인 → 두 스킬 설치까지 수행합니다. 기존 프로젝트에 설치하려면 `$beplayWorkspace` 값만 그 폴더의 절대 경로로 바꾸세요.

```powershell
$ErrorActionPreference = 'Stop'
$beplayWorkspace = Join-Path $env:USERPROFILE 'beplay-workspace'
New-Item -ItemType Directory -Force -Path $beplayWorkspace | Out-Null
Set-Location -LiteralPath $beplayWorkspace
$beplayTemp = Join-Path ([IO.Path]::GetTempPath()) ('beplay-' + [guid]::NewGuid())
New-Item -ItemType Directory -Path $beplayTemp | Out-Null
try {
    $beplayZip = Join-Path $beplayTemp 'package.zip'
    Invoke-WebRequest -UseBasicParsing -Uri 'https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/playground-harness-0.3.0.zip' -OutFile $beplayZip
    if ((Get-FileHash -LiteralPath $beplayZip -Algorithm SHA256).Hash -ne 'b9c39c2c97fafe54430abcf15894f6b8902d2398d0516ce10fda5df89cce2ae8') {
        throw '다운로드 검증 실패: 설치를 중단합니다.'
    }
    $beplayPackage = Join-Path $beplayTemp 'package'
    Expand-Archive -LiteralPath $beplayZip -DestinationPath $beplayPackage
    bun (Join-Path $beplayPackage 'beplay.js') setup --operation install --workspace $beplayWorkspace --host all --source $beplayPackage --json
    if ($LASTEXITCODE -ne 0) { throw '설치에 실패했습니다. 위 오류를 확인하세요.' }
    Write-Host 'beplay install complete'
} finally {
    Remove-Item -LiteralPath $beplayTemp -Recurse -Force
}
```

`beplay install complete`가 나오면 완료입니다. Windows에서 앱 미리보기와 썸네일까지 사용하려면 Chrome 또는 Chromium도 필요합니다. 실행 정책을 변경하거나 관리자 PowerShell을 열 필요는 없습니다.

### 설치 후 시작하기

두 운영체제 모두 설치한 작업 폴더에 다음 스킬이 생깁니다.

| 호스트 | 설치 위치 |
| :--- | :--- |
| Codex CLI | `.agents/skills/playground-app/` |
| Claude Code | `.claude/skills/playground-app/` |

같은 폴더에서 `codex` 또는 `claude`를 새로 실행하고 **대화창**에 입력하세요.

**Codex CLI**

```text
$playground-app beplay create 우리 팀 체크리스트 앱을 만들어줘. 할 일을 추가하고 완료 표시할 수 있게 해줘. 검사와 미리보기까지 진행하고 아직 게시하지 마.
```

**Claude Code**

```text
/playground-app beplay create 우리 팀 체크리스트 앱을 만들어줘. 할 일을 추가하고 완료 표시할 수 있게 해줘. 검사와 미리보기까지 진행하고 아직 게시하지 마.
```

도움말은 각각 `$playground-app beplay help`, `/playground-app beplay help`로 확인할 수 있습니다. beplay 설치기는 전역 PATH와 셸 설정을 변경하지 않습니다. 별도로 실행한 Bun 공식 설치기는 Bun 환경을 구성할 수 있습니다.

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

## For LLM

### AI에게 설치 맡기기

Codex CLI나 Claude Code에 아래 요청을 전달하세요. AI가 터미널을 실행할 수 있는 환경이어야 합니다.

```text
https://github.com/BENOW-HQ/beplay/blob/main/README.md 의 For LLM 섹션을 읽고,
현재 작업 폴더에 beplay를 설치해줘.
운영체제와 Bun 버전을 확인하고 Codex CLI·Claude Code 스킬을 둘 다 설치한 뒤,
설치된 실행기의 도움말과 진단 결과까지 검증해줘.
기존 파일은 보존하고, 앱 생성이나 게시는 하지 마.
```

### Agent installation protocol

When the user requests installation, carry out the steps below with your available terminal tools. If you cannot execute commands, say so and provide the matching OS instructions; do not claim installation succeeded.

**1. Resolve the environment and target.**

- Detect the actual OS and shell. Use the macOS/POSIX installer on macOS and the PowerShell ZIP flow on native Windows. WSL installs into the WSL environment; do not describe that as a native Windows installation.
- Use the user's selected workspace, or the current project directory if unambiguous. Ask only when the target is unclear. Do not install into the skill directory, filesystem root, or a guessed home directory. The home workspace in the human examples is only an example.
- Check `bun --version`; require 1.4.0 or later. If missing, use the official Bun instructions linked above within the user's authorization and the host's approval rules. Preserve an existing runtime installation method when upgrading. Do not install a second package-manager copy or disable sandbox/PowerShell policies to proceed.
- Check required download/extraction tools and whether `codex` / `claude` is available. Missing host CLIs do not prevent installing their skills, but report which host can actually be launched. Installing the host applications themselves is separate from installing beplay skills.

**2. Install the pinned release in the selected workspace.**

| Contract | Value |
| :--- | :--- |
| Version | `0.3.0` |
| Release | `https://github.com/BENOW-HQ/beplay/releases/tag/v0.3.0` |
| POSIX bootstrap | `https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/install.sh` |
| ZIP | `https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/playground-harness-0.3.0.zip` |
| ZIP SHA-256 | `b9c39c2c97fafe54430abcf15894f6b8902d2398d0516ce10fda5df89cce2ae8` |
| Bootstrap SHA-256 | `5ead268d8fbe18452c44472ffa635b58e5eb5158d8d8c2ec14dadd83301b4bc0` |
| Host selection | `all` (both skills), unless the user explicitly selects one |

- macOS: use the command in [macOS 설치](#macos-설치), passing `--workspace` when needed. For file-based execution, download `install.sh` to a unique temporary path, verify its SHA-256 above, then run `sh <downloaded-file> --workspace <absolute-workspace> --host all`. The bootstrap verifies the ZIP before installation.
- Windows: use the complete block in [Windows 설치](#windows-설치), replacing only the workspace selection with the user's target. Verify the ZIP before extracting or executing it, check Bun's exit code, and remove only the temporary folder created by this attempt.
- Use the public release without GitHub tokens, company cookies, or production credentials. Do not guess a `latest` installer, different version, or checksum. If the download/hash fails, stop and report the exact failed step.
- Preserve existing apps, `.beplay` sessions, and unrelated skills. A different installed package or `INSTALL_CONFLICT` is not permission to delete files or receipts. Use `update` only when the user requested an update; do not downgrade a newer installation. Do not use `--adopt` or force an overwrite to make installation pass.

**3. Verify the installation from the selected workspace.**

Run these commands and require successful exits. They read local state and do not create or publish an app.

```sh
bun .agents/skills/playground-app/bin/beplay.js help --json
bun .claude/skills/playground-app/bin/beplay.js help --json
bun .agents/skills/playground-app/bin/beplay.js setup --operation diagnose --workspace . --host all --json
```

The same commands work in PowerShell. For an explicitly selected single host, run only that host's help and diagnose commands.

- Both help results must report `event: "help"` and the same command list.
- Diagnose must report `event: "setup.diagnosed"` and `runtime.supported: true`. Host executable paths may be null; disclose this rather than claiming those applications are installed.
- Confirm each selected host has `SKILL.md` and `bin/beplay.js`, and `.beplay/integrations/codex.json` / `claude.json` exists for the corresponding installation.
- CLI help success proves the installed runner works. It does not prove the host selected the skill or that company login works. Ask the user to start a new host session and invoke `$playground-app beplay help` or `/playground-app beplay help` when host discovery cannot be checked directly.

**4. Report the result and stop.**

Return the workspace, release version, selected hosts, checks actually run, and the next chat invocation. Distinguish installed skills from available host applications. Installation alone does not authorize app creation, browser login, publication, or modification of existing company apps.

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
