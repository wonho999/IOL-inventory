# 환경 셋업 가이드

## 0. 전체 그림

```
┌─────────────────────────────────────────────────────┐
│  GitHub 저장소 (중앙 허브)                          │
│  https://github.com/<사용자명>/iol-inventory         │
└──────────┬──────────────┬──────────────┬────────────┘
           │              │              │
   ┌───────▼─────┐  ┌─────▼─────┐  ┌────▼───────┐
   │  개인 PC     │  │  회사 PC   │  │  모바일      │
   │  (메인 개발) │  │  (브라우저) │  │  (확인·편집) │
   │              │  │            │  │              │
   │  Git + VS   │  │  github.com│  │  github.com │
   │  Code +     │  │  웹 편집기  │  │  웹 편집기   │
   │  Claude     │  │  또는       │  │              │
   │  Code       │  │  Codespaces│  │              │
   └─────────────┘  └────────────┘  └──────────────┘

         어디서 작업하든 GitHub를 거쳐 동기화됩니다.
```

---

# 1단계: GitHub 저장소 생성 (PC 어느 쪽이든 OK · 5분)

브라우저로 [github.com](https://github.com) 접속 후 로그인.

1. 우상단 `+` → `New repository` 클릭
2. 입력:
   - **Repository name**: `iol-inventory` (원하는 다른 이름도 가능)
   - **Description**: `IOL 도수별 재고관리 시스템` (선택)
   - **Public** 또는 **Private** 선택
     - Public: GitHub Pages가 무료, 누구나 코드를 볼 수 있음
     - Private: 코드 비공개, GitHub Pages는 유료(Pro+) 필요
   - ⚠ **README, .gitignore, license는 추가하지 마세요** (이미 만들어 둔 파일을 올릴 거라 충돌 방지)
3. `Create repository` 클릭

그러면 텅 빈 저장소가 만들어지고 안내 페이지가 보입니다. 이 페이지의 URL을 메모해 두세요. 예:
```
https://github.com/myname/iol-inventory.git
```

---

# 2단계: 개인 PC 셋업 (메인 작업 머신 · 약 15분)

## 2-1. Git 설치

[git-scm.com/download/win](https://git-scm.com/download/win) 에서 다운로드 → 기본 옵션으로 설치 → 설치 후 PowerShell이나 cmd 열고 확인:
```bash
git --version
# git version 2.xx.x 가 보이면 OK
```

## 2-2. Git 사용자 정보 등록 (한번만)
```bash
git config --global user.name "Wonho"
git config --global user.email "your@email.com"
```

## 2-3. VS Code 설치

[code.visualstudio.com](https://code.visualstudio.com) 에서 다운로드 → 설치.

설치 옵션 중 **"Add to PATH"**, **"Add 'Open with Code' action"** 두 개 체크하면 편함.

## 2-4. (선택) Claude Code 설치

Claude를 명령줄에서 직접 호출하면서 코드를 수정할 수 있는 툴.

```bash
npm install -g @anthropic-ai/claude-code
```

먼저 Node.js가 필요하면 [nodejs.org](https://nodejs.org)에서 LTS 버전 설치 후 위 명령어 실행. Claude Pro/Max 구독이 있으면 무료로 사용 가능.

설치 후 프로젝트 폴더에서 `claude` 입력하면 시작됩니다.

## 2-5. 저장소 다운로드 후 첫 푸시

이 가이드에 함께 받은 `iol-inventory.zip` 압축을 원하는 폴더에 풀어주세요. 예: `C:\Users\Wonho\Documents\iol-inventory`

PowerShell 또는 cmd로 해당 폴더 들어가서:
```bash
cd C:\Users\Wonho\Documents\iol-inventory

git init
git add .
git commit -m "Initial commit: IOL 재고관리 시스템 v3"
git branch -M main
git remote add origin https://github.com/<사용자명>/iol-inventory.git
git push -u origin main
```

마지막 푸시할 때 GitHub 로그인 창이 뜹니다. 브라우저로 인증하면 됩니다.

이제 GitHub 저장소를 새로고침하면 모든 파일이 올라가 있을 거예요.

## 2-6. GitHub Pages 활성화 (모바일·회사에서 앱 자체 사용하려면)

브라우저로 GitHub 저장소 → `Settings` → 좌측 `Pages` →
- **Source**: `Deploy from a branch`
- **Branch**: `main` / `/ (root)` 선택 → `Save`

1~2분 후 `https://<사용자명>.github.io/iol-inventory/` 로 앱이 배포됩니다.

이제 회사·모바일에서도 이 URL로 앱 자체를 쓸 수 있습니다. **단, localStorage는 기기별로 별도 저장이라 데이터는 동기화되지 않습니다.** (저장은 ⚙ 설정 → 데이터 → JSON 내보내기로 백업 가능)

---

# 3단계: 일상 작업 흐름

## 개인 PC에서 작업할 때

```bash
# 1. 작업 시작 전 — 최신 상태 받기 (회사·모바일에서 변경한 게 있을 수 있음)
git pull

# 2. VS Code로 폴더 열기
code .

# 3. 파일 수정 (또는 claude 명령으로 Claude Code 시작)
claude

# 4. 작업 끝나면 저장
git add .
git commit -m "셀 크기 자동 조정 추가"
git push
```

## 회사 PC (브라우저만 가능) — 두 가지 방법

### 방법 A: GitHub.com 웹 편집기 (간단한 수정용)
1. github.com에서 저장소 열기
2. 수정할 파일 클릭 → 우상단 ✏ 연필 아이콘
3. 수정 → 하단 `Commit changes` 클릭

### 방법 B: GitHub Codespaces (개발 환경 그대로)
1. github.com에서 저장소 열기
2. 녹색 `Code` 버튼 → `Codespaces` 탭 → `Create codespace on main`
3. 브라우저 안에서 VS Code가 통째로 열림 (Claude Code도 사용 가능)
4. 수정 후 `Source Control` 탭 → 커밋 메시지 → 체크 → ↑ 누르면 푸시

무료 한도는 월 60시간 — 일반 사용에는 충분합니다.

## 모바일

### 코드 보기·간단한 수정
- 브라우저로 github.com 저장소 → 파일 → ✏ 연필
- 또는 GitHub 모바일 앱 설치

### 앱 자체 사용 (재고 확인·입력)
- 모바일 브라우저로 `https://<사용자명>.github.io/iol-inventory/` 접속
- 첫 화면에 "홈 화면에 추가" 하면 앱처럼 사용 가능

---

# 4단계: 데이터 동기화

⚠ **중요**: 코드는 Git으로 동기화되지만, **재고 데이터는 브라우저 localStorage**에 저장됩니다. 브라우저별·기기별로 독립적입니다.

여러 기기에서 같은 데이터를 보려면:

### 권장: 한 기기를 "마스터"로 정하기
- 예: 회사 PC에서만 실제 재고 입력
- 다른 기기에서 보려면 ⚙ 설정 → 💾 데이터 → ⬇ JSON 내보내기 → 다른 기기에서 ⬆ 불러오기

### 향후 옵션 (Phase 2)
실시간 동기화가 필요해지면 PROJECT_HISTORY.md의 SaaS 로드맵 참고 (Supabase 백엔드 도입).

---

# 5단계: 문제 해결

### "git push 인증 실패"
GitHub 비밀번호로는 더 이상 푸시가 안 됩니다. **Personal Access Token** 사용:
1. github.com → 우상단 프로필 → Settings → 좌측 맨 아래 Developer settings
2. Personal access tokens → Tokens (classic) → Generate new token
3. `repo` 권한 체크 → 생성된 토큰 복사
4. `git push`할 때 비밀번호 자리에 토큰 붙여넣기

### "Codespaces 한도 초과"
- 자주 쓰면 월 60시간을 넘을 수 있음
- 사용 후 반드시 `Stop codespace` 클릭
- 안 쓸 때는 `Delete codespace`로 삭제 → 다음에 새로 생성

### "GitHub Pages가 안 보여요"
- 첫 배포는 1~5분 걸림
- Settings → Pages에서 배포 상태 확인
- 이전에 Pages 설정한 적 있으면 캐시 문제일 수 있음 → 시크릿 모드로 접속

### "데이터가 사라졌어요"
- 브라우저 localStorage가 지워진 것임 (시크릿 모드, 캐시 삭제 등)
- 정기적으로 ⚙ 설정 → JSON 내보내기로 백업 권장

---

# 다음 단계 추천

1. **현재 데이터 백업**: 첫 푸시 전에 현재 브라우저에서 ⚙ 설정 → JSON 내보내기 한번 받아두세요
2. **GitHub Pages URL 즐겨찾기**: 회사·모바일에서 바로 접근하도록
3. **Claude와 다음 작업할 때**: `CLAUDE_CONTEXT.md`를 첫 메시지에 붙여넣기
