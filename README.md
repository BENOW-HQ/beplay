# beplay — Codex CLI / Claude Code

AI와 함께 HTML 앱을 만들고 미리보기·검사를 수행하는 도구입니다. 회사 앱을 조회하거나 게시하려면 별도로 회사 Google 로그인과 해당 앱 권한이 필요합니다. 설치 묶음에 회사 데이터나 인증 자격증명은 없습니다.

## 한 줄 설치 (macOS / POSIX 셸)

준비물: Bun 1.4 이상, curl, unzip, shasum 또는 sha256sum. 사용할 Codex CLI 또는 Claude Code는 먼저 설치하세요. 설치기는 런타임·호스트를 자동 설치하거나 PATH·셸 설정을 변경하지 않습니다.

앱을 만들 작업 폴더에서 실행하세요. 배포 파일은 이 저장소의 버전별 Releases에서 제공합니다.

```sh
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/install.sh | sh
```

현재 폴더의 `.agents/skills/playground-app`과 `.claude/skills/playground-app`에 동시에 설치합니다. 스크립트에 고정된 SHA-256으로 ZIP을 확인한 뒤 기존 파일 소유권 검사를 적용합니다. 관리자 권한은 필요하지 않습니다. 설치 파일을 공개해도 회사 앱 접근 권한은 부여되지 않습니다.

다른 작업 폴더나 한 호스트만 선택할 수도 있습니다.

```sh
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/install.sh | sh -s -- --workspace '/path/to/workspace' --host codex
```

## 사용

설치한 폴더에서 `codex` 또는 `claude`를 새로 실행하고 대화창에 입력합니다.

- Codex CLI: `$playground-app beplay help`
- Claude Code: `/playground-app beplay help`

이후 대화 예시:

```text
beplay create 할 일을 추가하고 완료 표시하는 체크리스트 앱을 만들어줘. 검사와 미리보기까지 진행하고 아직 게시하지 마.
beplay preview
beplay publish 확인한 앱을 첫 버전으로 올려줘.
```

`be-play`도 같은 이름입니다. 자연어 요청은 AI 대화창에 입력하며 셸에서 직접 실행하지 않습니다. 제작·수정만 요청하면 게시하지 않습니다. 브라우저 도구가 파일 전송을 지원하지 않으면 로그인·게시 파일 선택·게시 결과 전달을 직접 수행합니다. 브라우저 쿠키나 비밀번호를 CLI로 복사하지 마세요.

## 업데이트와 제거

새 버전의 설치 스크립트 URL로 바꾸고 `--operation update`를 전달합니다. 제거는 설치한 버전의 스크립트에 `--operation uninstall`을 전달합니다. 기본 대상은 두 호스트입니다.

```sh
curl --proto '=https' --proto-redir '=https' -fsSL https://github.com/BENOW-HQ/beplay/releases/download/v0.3.0/install.sh | sh -s -- --operation uninstall
```

직접 편집한 스킬 파일은 강제로 덮어쓰거나 삭제하지 않습니다. 앱 파일·`.beplay` 요구사항 세션은 보존합니다. 새 대화나 폴더 이동에서도 같은 작업을 이어가려면 작업 공간 전체를 보관하세요.

## Windows / 수동 설치

같은 Release의 ZIP을 다운로드해 압축을 풀고 실행하세요. Windows의 미리보기·썸네일에는 로컬 Chrome 또는 Chromium이 필요합니다.

```powershell
Expand-Archive .\playground-harness-0.3.0.zip .\playground-harness
bun .\playground-harness\beplay.js setup --operation install --workspace . --host all --source .\playground-harness --json
```

설치 후 두 호스트의 명시적 스킬 호출은 위와 같습니다. 공개 다운로드는 로그인 없이 가능하며 회사 앱 사용 단계의 Google 로그인·권한은 그대로 적용됩니다.
