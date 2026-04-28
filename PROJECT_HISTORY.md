# 프로젝트 히스토리

## 프로젝트 개요

안과 클리닉 백내장 수술용 IOL(인공수정체) 도수별 재고관리 시스템.

**핵심 사용자**: Wonho (안과 클리닉 의료 행정/운영)

**최종 목표 (장기)**: 다병원 SaaS · 모바일 앱 · 의료기기/제약 광고 수익화

**현재 단계**: Phase 1 — 단일 HTML 파일 기반 데모 / 실사용 검증

---

## 진화 과정

### v1 — 통합 운영관리 데모
- 5개 탭 대시보드: 수술현황·예측·재고·재무·설정
- Excel 입력 → 차트 시각화 (Chart.js)
- 1,724건 샘플 데이터 (2023~2024년 시뮬레이션)
- 앙상블 수요 예측 (선형회귀 + 지수평활 + 계절성)
- 보관: 참고용 (별도 파일)

### v2 — 통계 대시보드 확장
- 컬럼 매핑·목표선·발주서 출력·월간보고서
- 화면 텍스트 편집(라벨 시스템) 시도
- 한계: 통계 위주여서 실제 일일 운영에는 부적합

### **v3 — 도수별 재고 전용** (현재)
- 방향 전환: 통계 대시보드 → 일일 운영 도구
- IOL 모델별 / 도수별 / CYL별 셀 그리드
- 색상 코드 + 수술 일정 연동
- 화면 텍스트 100% 커스터마이징
- 그룹 접기/펼치기 + 모델별 상태 기억
- 자동 셀 크기 + 줌 컨트롤

---

## 핵심 설계 결정사항

### 데이터 구조
```javascript
STATE = {
  models: [          // 렌즈 모델 목록
    {
      id: 'eyhance',
      name: '아이핸스',
      abbr: 'ICB00',
      brand: 'J&J',
      type: 'sphere' | 'toric',
      color: '#388bfd',
      sphMin: 9, sphMax: 28, sphStep: 0.5,
      cylVals: [],   // 토릭만
      minStock: 1,
      stock: { '20.0': 3, '20.5_1.50': 2 }   // 키: 도수 또는 도수_CYL
    }
  ],
  schedule: [        // 오늘 수술 일정
    { mid, sph, cyl, eye }
  ],
  activeId: 'eyhance'   // 현재 선택된 모델
}

LABELS = { /* 화면 텍스트 50+ 항목 */ }
GOPTS  = { greenMin, cellSize, zoomIdx }
COLLAPSE = { [modelId]: { [groupKey]: true/false } }
```

localStorage 키: `iol3_state`, `iol3_labels`, `iol3_gopts`, `iol3_collapse`

### 셀 색상 로직
```
재고=0           → 🔴 빨강
재고≤최소재고     → 🟡 노랑 (수술예정 있으면 🔴)
수술 후≤최소재고  → 🟡 노랑
그 외             → 🟢 초록
```

### UI 레이아웃
```
[상단 NAV — 로고·병원명·날짜·설정·모델추가·주문서·초기화]
[좌측 모델 목록] [중앙 그리드] [우측: 일정·주문]
```

### 토릭 그리드 진화
1. **초기**: 2D 표 (구면×CYL) — 세로로만 길어짐
2. **개선**: CYL별 그룹화 + 단초점과 동일한 auto-fill 그리드
3. 셀 = 구면도수(위) + CYL태그(중간) + 재고(아래)

### 자동 셀 크기 (CSS Grid)
```css
.sphere-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(var(--cell-min), 1fr));
}
```
- `auto-fill` + `minmax`로 화면 너비에 따라 자동 줄바꿈
- ResizeObserver로 패널 크기 변경 시 재계산
- 줌 7단계: 65~150%

### 그룹 접기/펼치기
- 단초점: 5D 단위 그룹 (`sph_9.0`, `sph_14.0` 등)
- 토릭: CYL별 그룹 (`cyl_1.0`, `cyl_1.5` 등)
- localStorage에 모델별 독립 저장
- CSS `max-height` transition으로 부드러운 애니메이션

---

## 거부된 아이디어

- **차트 위치 자유 변경 / 드래그 레이아웃** — 효용 대비 복잡도 너무 큼. 보류
- **백엔드 즉시 도입** — Phase 1은 단일 HTML로 검증 후 결정
- **사용자 권한 시스템** — 단일 사용자 단계에서 불필요

---

## 알려진 제약

- localStorage는 브라우저·기기별로 독립 → 데이터 동기화 X
- 5MB 이상 저장은 권장 안 됨 (충분한 용량이지만)
- Safari 시크릿 모드는 localStorage 제한 있음
- 인쇄 시 일부 색상은 컬러 프린터 필요

---

## 향후 로드맵 (Phase 2~3)

### Phase 2 (1~3개월) — 클라우드 SaaS
- React 마이그레이션 → Vercel 배포
- FastAPI 백엔드 (기존 분석 코드 재사용)
- Supabase (PostgreSQL + 인증 + 파일저장)
- 병원별 멀티테넌트
- 실시간 동기화 (여러 PC·모바일 간)

### Phase 3 (3~6개월) — 모바일 + 수익화
- React Native (iOS+Android, 코드 70% 재사용)
- 구독 모델 (병원당 월 3~10만원)
- 의료기기·제약 타겟 광고
- 익명화 트렌드 리포트 판매

---

## 파일 구조

```
iol-inventory/
├── index.html              # 메인 앱 (단일 파일, ~2,200줄)
├── README.md               # 프로젝트 소개
├── SETUP.md                # 환경 셋업 가이드
├── PROJECT_HISTORY.md      # 이 파일
├── CLAUDE_CONTEXT.md       # Claude 새 대화 시 줄 컨텍스트
├── .gitignore              # Git 무시 규칙
└── .github/
    └── workflows/
        └── pages.yml       # GitHub Pages 자동 배포
```

---

## 작업 환경

- **메인 PC**: 개인 고사양 PC (Git + VSCode + Claude Code)
- **회사 PC**: 브라우저만 (GitHub.com 웹 편집기 + Codespaces)
- **모바일**: GitHub.com 웹 + 배포된 앱 사용
- **저장소**: GitHub `iol-inventory`
- **배포**: GitHub Pages (모바일·회사 접근용)
