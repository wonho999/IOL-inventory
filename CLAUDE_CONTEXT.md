# Claude 새 대화 시 컨텍스트

> 새 대화를 시작할 때 이 문서 전체를 첫 메시지에 붙여넣고 작업을 요청하세요.
> 그러면 Claude가 프로젝트 맥락을 빠르게 파악하고 일관된 작업을 이어갈 수 있습니다.

---

## 프로젝트 한 줄 요약

**한국 안과 클리닉의 IOL(인공수정체) 도수별 재고관리 시스템.**
단일 HTML 파일(~2,200줄). Vanilla JS · localStorage 기반. 외부 의존성 없음.

## 사용자 정보

- **이름**: Wonho
- **역할**: 안과 클리닉 의료 행정/운영
- **회사 PC 제약**: 브라우저만 사용 가능 (Git, Node.js 등 설치 불가)
- **개인 PC**: 고사양, Git + VSCode + Claude Code 사용 가능
- **장기 목표**: 다병원 SaaS, 모바일 앱, 의료기기·제약 광고 수익화
- **현재 단계**: Phase 1 — 단일 HTML 데모로 실사용 검증

## 기술 스택 (현재)

- Vanilla HTML/CSS/JS — **외부 의존성 없음**
- 단일 `index.html` 파일
- localStorage 기반 영속성
- CSS Grid `auto-fill` + `aspect-ratio` 반응형
- Chart.js 안 씀 (v3에서 통계 대시보드 제거)
- 한글 UI

## 데이터 구조 핵심

```javascript
STATE = {
  models: [{
    id, name, abbr, brand,
    type: 'sphere' | 'toric',
    color, sphMin, sphMax, sphStep,
    cylVals: [],      // 토릭만
    minStock: 1,
    stock: { '20.0': 3, '20.5_1.50': 2 }  // 키 형식: '도수' 또는 '도수_CYL'
  }],
  schedule: [{ mid, sph, cyl, eye }],
  activeId
}

LABELS   = { /* 50+ 화면 텍스트 */ }   // localStorage: iol3_labels
GOPTS    = { greenMin, zoomIdx }       // localStorage: iol3_gopts
COLLAPSE = { [modelId]: { [groupKey]: true/false } }  // iol3_collapse
```

## 셀 상태 색상 규칙

```javascript
function cellCls(stock, sched, minStock) {
  if (stock === 0) return 'r';                          // 🔴
  if (stock <= minStock) return sched > 0 ? 'r' : 'y';  // 🟡 (수술시 🔴)
  if (sched > 0 && (stock - sched) <= minStock) return 'y';  // 🟡
  return 'g';                                            // 🟢
}
```

## 구현된 주요 기능

- ✅ 단초점/다초점 모델 (5D 단위 그룹)
- ✅ 토릭 모델 (CYL별 그룹, 셀에 구면+CYL+재고 3단)
- ✅ 빠른 입력: `20.0D 3` `18.5 +1.50 2` `-1`(사용)
- ✅ 스캔 입력 모달 (여러 줄 한번에)
- ✅ 수술 일정 입력 → 셀에 파란 점, 수술 후 재고 부족시 빨간 경고
- ✅ 그룹 접기/펼치기 + 모델별 상태 기억
- ✅ 자동 셀 크기 (CSS Grid auto-fill + ResizeObserver)
- ✅ 줌 컨트롤 (7단계: 65%~150%)
- ✅ 화면 텍스트 100% 편집 (`data-label` 시스템)
- ✅ 주문서 출력 / Excel 내보내기
- ✅ JSON 백업/복원

## 코드 컨벤션

- **변수 선언 순서**: `const` `let` 모두 호이스팅 안 됨 → 사용 전 선언
- **상태 변수는 스크립트 최상단 STATE 블록에 모음**
- **함수 이름은 짧고 의미 분명하게** (`renderSphere`, `applyLabels`, `toggleGroup`)
- **HTML 텍스트는 모두 `data-label` 속성으로** (실시간 편집을 위해)
- **localStorage 키 prefix**: `iol3_*`
- **거의 모든 변수명은 영문**, **사용자 보이는 텍스트만 한글**

## 절대 하지 말 것

- ❌ React, Vue 같은 프레임워크 도입 (Phase 1은 단일 HTML 유지)
- ❌ 빌드 도구 도입 (npm, webpack 등 — 그냥 `index.html` 열면 동작해야 함)
- ❌ 외부 API 호출 (오프라인에서도 동작해야 함)
- ❌ 차트 다시 추가 (v3에서 의도적으로 제거 — 일일 운영 도구로 단순화)
- ❌ 의료 정보를 외부에 전송 (개인정보 + HIPAA 유사 우려)

## 작업 흐름

새 기능 추가 시:
1. 기존 코드 패턴 따르기 (특히 `renderSphere`/`renderToric` 구조)
2. localStorage 키 prefix `iol3_` 유지
3. 영향받는 함수 모두 갱신 — `selectModel`, `renderModelList` 등
4. CSS는 BEM 비슷한 짧은 클래스명 (`.sg`, `.dcell`, `.smc-h` 등)
5. 변경 후 문법 검사 권장 (`node --check` 가능)

## 작업 시 자주 묻는 것 / 답

- **"이 기능 어디 추가해야 해?"** → PROJECT_HISTORY.md 확인 후 결정
- **"이거 백엔드로 빼야 하지 않을까?"** → Phase 1은 단일 HTML 유지 / Phase 2부터 백엔드
- **"npm install 해도 돼?"** → ❌ 외부 의존성 추가 금지 (단일 HTML 원칙)

## 현재 작업물 위치

- GitHub 저장소: `<사용자명>/iol-inventory`
- 메인 파일: `index.html`
- 배포 URL: `https://<사용자명>.github.io/iol-inventory/`

## 마지막 작업 내용

(여기에 직전 작업 요약을 매번 갱신해주면 좋습니다)

> 예시: "그룹 접기/펼치기 기능 추가, 모델별 상태 기억"
