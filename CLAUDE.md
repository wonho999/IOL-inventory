# CLAUDE.md — IOL 재고관리 프로젝트

> 이 파일은 Claude Code / Claude Desktop 이 자동으로 읽는 프로젝트 가이드입니다.
> Karpathy 스타일 가이드 + IOL 프로젝트 특화 규칙을 통합했습니다.

---

## 1. Think Before Coding

**가정하지 마라. 혼란을 숨기지 마라. 트레이드오프를 명시해라.**

구현 전:
- 가정을 명시적으로 서술하라. 불확실하면 물어라.
- 여러 해석이 가능하면 선택지를 제시하라 — 조용히 하나만 고르지 마라.
- 더 단순한 방법이 있으면 말해라. 필요하면 반론을 제기해라.
- 불명확한 부분이 있으면 멈춰라. 무엇이 불명확한지 명시하고 물어라.

## 2. Simplicity First

**문제를 해결하는 최소한의 코드. 추측성 코드 금지.**

- 요청받지 않은 기능은 추가하지 마라.
- 단일 사용 코드에 추상화 레이어 금지.
- 요청하지 않은 "유연성"이나 "설정 가능성" 금지.
- 불가능한 시나리오에 대한 에러 핸들링 금지.
- 200줄로 쓴 코드가 50줄로 가능하면 다시 써라.

스스로 물어라: "시니어 엔지니어가 이게 과도하게 복잡하다고 할까?" 그렇다면 단순화해라.

## 3. Surgical Changes

**꼭 필요한 것만 건드려라. 내가 만든 문제만 정리해라.**

기존 코드 편집 시:
- 인접 코드, 주석, 포맷을 "개선"하지 마라.
- 망가지지 않은 것을 리팩토링하지 마라.
- 다르게 하고 싶더라도 기존 스타일을 따라라.
- 관련 없는 데드코드를 발견하면 언급만 하고 삭제하지 마라.

내 변경이 고아(orphan)를 만들었을 때:
- 내 변경으로 사용되지 않게 된 import/변수/함수는 제거해라.
- 기존에 있던 데드코드는 요청받지 않으면 제거하지 마라.

테스트: 변경된 모든 줄이 사용자 요청과 직접 연결되어야 한다.

## 4. Goal-Driven Execution

**성공 기준을 정의하라. 검증될 때까지 반복해라.**

작업을 검증 가능한 목표로 변환:
- "유효성 검사 추가" → "잘못된 입력에 대한 테스트 작성 후 통과시키기"
- "버그 수정" → "버그를 재현하는 테스트 작성 후 통과시키기"

멀티스텝 작업은 간단한 계획을 먼저 서술:
```
1. [단계] → 검증: [확인방법]
2. [단계] → 검증: [확인방법]
```

---

## 5. IOL 프로젝트 특화 규칙

### 절대 규칙
- **단일 index.html 유지** — React/Vue/npm/빌드도구 금지
- **localStorage prefix**: 항상 `iol3_`
- **외부 의존성 없음** — 구글 시트 fetch만 예외, 오프라인 동작 필수
- **문법 검사 필수** — 코드 변경 후 반드시 `node --check` 실행

### 개인정보 보호
- 실제 이름/차트번호를 코드에 하드코딩 금지
- `index_public.html` = 익명 버전, localStorage 저장 차단
- 더미 데이터는 `환자01`, `주문자A` 같은 익명 형식 사용

### 데이터 구조 원칙
```javascript
// 단일 진실 소스 — SHEET_DATA 한 배열이 모든 것의 기반
// STATE.schedule 별도 저장 안 함 → SHEET_DATA에서 자동 산출
// 재고 = received + in_progress 행의 도수별 카운트
```

### 렌즈 라이프사이클 상태
```
decided     🟡  렌컴일만 있음 (주문 대기)
ordered     🟠  주문자 있음 (입고 대기)
received    🔵  수령자 있음, 수술 전 (현재 재고)
in_progress 🟢  수술일 = 오늘
used        ⚫  수술일 < 오늘 (통계)
incomplete  🔴  도수/CYL/눈 누락
```

### UI 컨벤션
- 한국어 UI 우선, 모든 텍스트는 `LABELS` 객체로 관리
- 색상 변수: `--g2`(충분) `--y2`(주의) `--r2`(없음) `--b2`(예정) `--pur`(토릭)
- 폰트: Noto Sans KR + JetBrains Mono (`--mo`)
- 모바일 분기: `@media (max-width: 600px)`

### 저장 함수 규칙
```javascript
save()   // STATE 변경 후
saveL()  // LABELS 변경 후
saveG()  // GOPTS 변경 후
saveC()  // COLLAPSE 변경 후
```

---

## 6. 프로젝트 컨텍스트

```
저장소: https://github.com/wonho999/IOL-inventory
배포:   https://wonho999.github.io/IOL-inventory/
파일:   index.html (실사용) + index_public.html (GitHub 공개용)
```

### 탭 구조
```
🏠 홈/대시보드  → 오늘 수술 + 주간 + 경고
📊 주문 시트    → 메인 입력, 상태 색상, 구글 시트 동기화
📦 재고 현황   → 자동 산출, 그리드, 우클릭 메뉴
📈 통계        → 월별/모델별 분석
⚙️ 설정        → 렌즈 모델, 연동, 라벨, 백업
```

### 핵심 함수 목록 (빠른 참조)
```javascript
getRowStatus(row, today)    // 행 상태 자동 판별
computeStock()              // SHEET_DATA → 도수별 재고 산출
getSpheres(m)               // 모델 도수 목록 (customDiops/excludedDiops 반영)
findModelBySheetName(name)  // 시트 렌즈명 → 시스템 모델 fuzzy 매핑
normalizeDate(s)            // M/D, 엑셀시리얼 등 → YYYY-MM-DD
buildDashData()             // SHEET_DATA + ORDER_DATA → 대시보드용 일정
```
