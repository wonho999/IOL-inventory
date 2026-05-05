# IOL 재고관리 — 개발 환경 세팅 가이드

## 전체 구조

```
메인 PC (집/개인)
├── VSCode + Git 설치됨
├── 터미널 작업 가능
└── Claude Desktop 설치

회사 PC
├── 브라우저만 사용 가능
├── 방법 A: 메인 PC 원격 접속 (Chrome Remote Desktop)
└── 방법 B: GitHub Codespaces (브라우저 VSCode)

공통
└── Claude.ai 웹 (어디서든 작업 이어가기)
```

---

## STEP 1 — 메인 PC 초기 세팅 (한 번만)

### 1-1. 저장소 클론

```bash
git clone https://github.com/wonho999/IOL-inventory
cd IOL-inventory
```

### 1-2. 프로젝트 파일 구조 만들기

```bash
# 현재 index.html 은 그대로 두고
# CLAUDE.md 추가 (방금 만든 파일)
# .vscode 설정 추가

mkdir -p .vscode
```

### 1-3. 프로젝트 루트 파일 구성

```
IOL-inventory/
├── index.html           ← 실사용 (개인정보 포함)
├── index_public.html    ← GitHub 공개용 (익명)
├── CLAUDE.md            ← Claude Code/Desktop 가이드 ✅
├── CLAUDE_CONTEXT_v2.md ← 새 대화 시작용 컨텍스트 ✅
├── .vscode/
│   └── settings.json    ← 아래 내용
└── .gitignore           ← 아래 내용
```

### 1-4. `.vscode/settings.json`

```json
{
  "editor.defaultFormatter": "esbenp.prettier-vscode",
  "editor.formatOnSave": false,
  "editor.tabSize": 2,
  "files.associations": {
    "*.html": "html"
  },
  "liveServer.settings.port": 5500,
  "liveServer.settings.doNotShowInfoMsg": true
}
```

### 1-5. `.gitignore`

```
.DS_Store
*.log
node_modules/
.env
```

### 1-6. VSCode 확장 설치 (권장)

```
Live Server          — 저장 즉시 브라우저 자동 새로고침
Prettier             — 코드 포맷
GitLens              — Git 히스토리 시각화
```

---

## STEP 2 — Claude Desktop 세팅 (메인 PC)

Claude Desktop은 **CLAUDE.md를 자동으로 읽어서** 프로젝트 컨텍스트를 유지해요.

### 2-1. Claude Desktop 설치

```
https://claude.ai/download
```

### 2-2. 프로젝트 폴더 열기

Claude Desktop → 새 대화 → 프로젝트 폴더 지정
또는 터미널에서:

```bash
cd IOL-inventory
claude  # Claude Code CLI 사용 시
```

### 2-3. CLAUDE.md 자동 인식 확인

Claude Desktop에서 대화 시작하면 자동으로 `CLAUDE.md` 읽음
→ 프로젝트 규칙, 탭 구조, 함수 목록을 매번 붙여넣을 필요 없음

---

## STEP 3 — 회사 PC 세팅 (두 가지 방법)

### 방법 A: Chrome Remote Desktop (추천)

메인 PC를 원격으로 그대로 사용. VSCode, Claude Desktop 다 쓸 수 있어요.

**메인 PC에서 (한 번만):**
```
1. Chrome 설치
2. chrome://apps → Chrome 원격 데스크톱 설치
3. 원격 액세스 활성화 → PIN 설정
4. 메인 PC 절전 모드 해제 설정
   (설정 → 전원 → 화면 끄기: 안 함)
```

**회사 PC에서:**
```
1. Chrome 열기
2. remotedesktop.google.com
3. 메인 PC 선택 → PIN 입력
4. 끝 — 메인 PC 화면 그대로 사용
```

### 방법 B: GitHub Codespaces (메인 PC 없어도 OK)

저장소를 브라우저 VSCode로 바로 편집. 코드 실행/미리보기 가능.

```
1. github.com/wonho999/IOL-inventory
2. < > Code 버튼 → Codespaces → New codespace
3. 브라우저에서 VSCode 열림
4. CLAUDE.md 있으니 규칙 자동 로드
5. 터미널에서: node --check index.html 가능
```

---

## STEP 4 — 일상 작업 흐름

### 메인 PC에서 작업할 때

```bash
cd IOL-inventory
code .                    # VSCode 열기
# 또는 Claude Desktop 열기

# 작업 후
git add .
git commit -m "feat: 주문 시트 상태 색상 추가"
git push
```

### 회사 PC에서 작업할 때

**Remote Desktop로:**
→ 메인 PC 그대로 사용, 위와 동일

**Codespaces로:**
→ 브라우저 VSCode에서 편집 후 `Commit & Push` 클릭

### Claude.ai 웹에서 작업할 때 (어디서든)

```
1. claude.ai 접속
2. 새 대화 시작
3. CLAUDE_CONTEXT_v2.md 내용 첫 메시지에 붙여넣기
4. 이어서 작업
```

---

## STEP 5 — GitHub Pages 배포 확인

```bash
# index_public.html → index.html 로 복사해서 push하면 자동 배포
cp index_public.html index.html
git add index.html
git commit -m "deploy: 공개 버전 업데이트"
git push
# 1~2분 후 https://wonho999.github.io/IOL-inventory/ 확인
```

**주의**: `index.html`(실사용)과 `index_public.html`(공개) 구분 유지

---

## 자주 쓰는 명령어

```bash
# 문법 검사
node --check index.html

# Live Server 시작 (VSCode에서)
Ctrl+Shift+P → Live Server: Open with Live Server

# Git 상태 확인
git status
git log --oneline -5

# 원격 저장소에 올리기
git add .
git commit -m "메시지"
git push origin main
```

---

## 트러블슈팅

| 문제 | 해결 |
|------|------|
| GitHub Pages 404 | Settings → Pages → Source: main / (root) 확인 |
| 저장소 URL 대소문자 | `IOL-inventory` (대문자 IOL) 확인 |
| node --check 오류 | HTML에서 script 블록만 추출해서 별도 검사 |
| 구글 시트 CORS | 파일 → 웹에 게시 → CSV 선택 후 URL 사용 |
