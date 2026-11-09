# beplay — Codex CLI / Claude Code

AI와 함께 HTML 앱을 만들고 미리보기와 검사를 수행하는 도구입니다. 회사 앱을 조회하거나 게시하려면 별도로 회사 Google 로그인과 해당 앱 권한이 필요합니다. 설치 묶음에 회사 데이터나 인증 자격증명은 없습니다.

## AI 자동 준비 (Windows / macOS)

앱 제작 요청에는 작업 폴더의 Playground 스킬과 필요한 Bun 준비가 포함됩니다. AI가 직접 처리하며 설치 여부를 재질문하거나 설치 안내, 명령 실행, 새 채팅 시작을 사용자에게 맡기지 않습니다. 호스트의 실행 권한·조직 정책과 로그인 동의는 우회하지 않습니다.

1. 현재 호스트의 사용 가능 스킬과 작업 폴더의 `.agents/skills/playground-app` (Codex), `.claude/skills/playground-app` (Claude)을 확인합니다. 사용 가능한 기존 스킬을 우선 사용하고 중복 설치하지 않습니다. 소스 저장소의 `packages/harness/plugin`은 실행기 없는 원본일 수 있으므로 설치본으로 취급하지 않습니다.
2. 없으면 아래 **v0.3.3 배포 스크립트**를 AI가 내려받아 실행합니다. 해당 Release가 아직 없거나 다운로드가 실패하면 다른 버전으로 추측해서 대체하지 않습니다. 설치기는 Bun 1.4 이상을 재사용하고, 없거나 오래되었으면 공식 Bun 설치기를 통해 사용자 전용 `~/.beplay/runtime/bun-1.4.0`에 준비합니다. 기존 Bun과 영구 PATH·셸 프로필을 변경하지 않습니다. Windows는 PowerShell과 Windows 10 1809 이상이 필요합니다.
3. 설치 경로의 `SKILL.md`를 **현재 세션에서 직접 읽고** 지침을 적용합니다. 자동 스킬 탐색 갱신이나 재시작을 기다리지 않습니다. `bun`이 PATH에 없거나 1.4 미만이면 macOS의 `~/.beplay/runtime/bun-1.4.0/bin/bun`, Windows의 `$env:USERPROFILE\.beplay\runtime\bun-1.4.0\bin\bun.exe`를 직접 실행합니다.
4. 해당 실행기로 `<skill>/bin/beplay.js setup --operation diagnose --workspace <작업폴더> --host <codex|claude> --json`과 `<skill>/bin/beplay.js help --json`을 실행합니다. 설치 파일 검증과 실행 성공을 확인한 뒤 스킬의 인터뷰 → 계획 → 구현·검증 흐름을 계속합니다. `diagnose.completed`만으로 스킬 선택·브라우저·게시 성공을 주장하지 않습니다.
5. 기존 설치가 정상이라면 유지합니다. 관리 파일이 변경됐거나 소유권 충돌이 있으면 강제 덮어쓰기·삭제하지 않습니다. 설치 불가 환경에서는 스킬 없이 몰래 대체하지 말고 막힌 기능만 간단히 알립니다. 설치가 앱 게시나 공유 범위를 확대하지 않으며 원래 요청의 파일 제공/미리보기/게시 범위를 따릅니다.

아래 명령은 AI 실행용입니다. 작업 폴더와 호스트는 AI가 실제 값으로 선택합니다.

macOS:
```sh
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.3/install.sh | sh -s -- --workspace "$PWD" --host codex
```

Windows (실행 정책을 변경하지 않음):
```powershell
$installer = Invoke-RestMethod -Uri 'https://github.com/BENOW-HQ/beplay/releases/download/v0.3.3/install.ps1'
& ([scriptblock]::Create($installer)) -Workspace (Get-Location).Path -HostName codex
```

Claude 환경이면 `codex`를 `claude`로 바꿉니다. 원격 다운로드·프로세스 실행이 없는 일반 채팅 환경에서는 이 설치를 수행할 수 없습니다.

## 사용

AI는 설치한 SKILL.md를 현재 세션에서 직접 읽고 실행기를 사용합니다. 다음 대화에서는 아래처럼 명시적으로 호출할 수도 있습니다.

- Codex CLI: `$playground-app beplay help`
- Claude Code: `/playground-app beplay help`

이후 대화 예시:

```text
플레이그라운드에 내가 쓸 체크리스트 앱을 만들어줘.
방금 만든 앱에 완료한 항목을 숨기는 버튼을 넣어줘.
아직 게시하지 말고 미리보기까지만 만들어줘.
```

`be-play`도 같은 이름입니다. 자연어 요청은 AI 대화창에 입력하며 셸에서 직접 실행하지 않습니다. 플레이그라운드에 만들어 달라는 요청에는 미리보기와 검사, 비공개 게시와 게시된 화면 확인까지 포함됩니다. 수정은 같은 앱에 새 버전으로 반영하며, 미리보기까지만 요청하면 게시하지 않습니다. 파일 형식 선택과 전송은 도구가 처리하고, 사용자는 필요한 업무 결정과 로그인·동의만 진행합니다.

게시와 화면 확인에는 지원되는 인증 브라우저 연결이 필요합니다. Claude Desktop의 로컬 샌드박스 검증에는 연결된 공식 Claude in Chrome을 사용합니다. 필요한 브라우저 기능이 없으면 결과와 미리보기를 보존하고 완료하지 못한 단계를 안내합니다. 브라우저 쿠키나 비밀번호를 CLI로 복사하지 마세요.

## 업데이트와 제거

새 버전의 설치 스크립트 URL로 바꾸고 `--operation update`를 전달합니다. 제거는 설치한 버전의 스크립트에 `--operation uninstall`을 전달합니다. 기본 대상은 두 호스트입니다.

```sh
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.3/install.sh | sh -s -- --operation uninstall
```

직접 편집한 스킬 파일은 강제로 덮어쓰거나 삭제하지 않습니다. 앱 파일과 `.beplay` 요구사항 세션은 보존합니다. 새 대화나 폴더 이동에서도 같은 작업을 이어가려면 작업 공간 전체를 보관하세요.

## Windows / 수동 설치

같은 Release의 ZIP을 다운로드해 압축을 풀고 실행하세요. Windows의 미리보기와 썸네일에는 로컬 Chrome 또는 Chromium이 필요합니다.

```powershell
Expand-Archive .\playground-harness-0.3.3.zip .\playground-harness
bun .\playground-harness\beplay.js setup --operation install --workspace . --host all --source .\playground-harness --json
```

설치 후 두 호스트의 명시적 스킬 호출은 위와 같습니다. 공개 다운로드는 로그인 없이 가능하며 회사 앱 사용 단계의 Google 로그인과 권한은 그대로 적용됩니다.
