# JAYDEN FINANCE — Claude Code 인수인계 문서

## 프로젝트 개요
재훈님(최재훈, Jayden)의 개인 재무 관리 웹앱.
투자자산, 부채, 할부, 실물자산, 현금흐름, 목표, 분석(AI 포함)을 관리.

---

## 배포 정보
- **GitHub**: `jediknight3/jayden-finance` → `index.html`
- **URL**: `https://jediknight3.github.io/jayden-finance`
- **GAS URL**: `https://script.google.com/macros/s/AKfycby7U9KPP2zBONy9oc9Pyf8vGHcbZcyv63Z_jd4mT9MqZ9OzKrho9hKmFws5yqLqo5VW2A/exec`
- **구글 시트**: 투자데이터, 계좌데이터, 부채데이터, 실물자산데이터, 현금흐름데이터, 목표데이터, 고정지출데이터, 할부데이터

---

## 현재 파일 상태
- **파일명**: `JAYDEN_FINANCE.html` (GitHub 올릴 때 `index.html`로 변경)
- **크기**: 238KB, 5,252줄, 158개 함수
- **DB 구조**: `invest, bank, debt, real, cf, goals, fixed, install`

---

## 페이지 구조 (8개)
| 페이지 | 설명 |
|--------|------|
| home | 순자산 대시보드, 자산구성, 현금흐름, 순자산추이 차트 |
| assets | 투자자산(요약/월별추이/히트맵/진단), 예금/현금, 실물자산(양도세계산기) |
| debt | 대출(서브탭), 할부(서브탭) |
| cashflow | 이번달, 고정지출, 월별추이 |
| goals | 목표 달성률, 목표 카드, 수정/삭제 |
| input | 투자/계좌/부채/💳할부/실물/현금흐름/🔒고정 입력 |
| analysis | 순자산분석/목표시뮬/대출분석/세금/🤖AI리포트 |
| settings | GAS URL, 캐시초기화, CSV내보내기, Apps Script 코드 복사 |

---

## GAS (Google Apps Script) 구조
GAS 코드는 HTML 안의 `const GAS_CODE` 변수에 포함되어 있고,
설정 탭 → 📋 복사로 클립보드에 복사 가능.

### GAS 핵심 함수
- `doGet(e)` — 모든 요청 처리 (read/upsert/delete/bulkInit/ai)
- `handleAI(prompt)` — Anthropic API 프록시 (ANTHROPIC_API_KEY 스크립트 속성 필요)
- `readAll(sheetKey)` — 시트 데이터 전체 읽기
- `handleUpsert(sheet, rec)` — 데이터 저장/수정

### GAS 시트 매핑
```javascript
const SHEETS = {
  invest: '투자데이터',
  bank:   '계좌데이터',
  debt:   '부채데이터',
  real:   '실물자산데이터',
  cf:     '현금흐름데이터',
  goals:  '목표데이터',
  fixed:  '고정지출데이터',
  install:'할부데이터',
}
```

### GAS 헤더
```javascript
const HEADERS = {
  invest:  ['id','acct','month','dep','wd','depCum','wdCum','curr'],
  bank:    ['id','name','type','balance','rate','memo'],
  debt:    ['id','name','company','type','balance','rate','rateType','repayType','monthly','start','end','memo'],
  real:    ['id','name','type','buyPrice','currPrice','buyDate','memo'],
  cf:      ['id','month','inout','category','amount','memo'],
  goals:   ['id','name','type','targetAmount','currentAmount','targetDate','memo'],
  fixed:   ['id','name','cat','amount','day','status','start','end','autoApply','memo'],
  install: ['id','name','cat','company','totalAmt','months','monthly','rate','repayType','day','start','end','remainMonths','status','memo'],
}
```

---

## 핵심 JS 함수 목록

### 데이터
- `syncAll()` — GAS에서 전체 동기화
- `gasUpsert(sheet, rec)` — 데이터 저장
- `gasDelete(sheet, id)` — 데이터 삭제
- `saveCache() / loadCache()` — localStorage 캐시
- `renderAll()` — 전체 화면 재렌더

### 계산
- `getNetWorth()` — 순자산 계산 `{total, invest, bank, real, debt, net}`
- `getInvTotals()` — 투자자산 합계 `{curr, prin, pnl, rate}`
- `getMonthlyTotals2()` — 월별 투자 합계 배열
- `getCfForMonth(mo)` — 해당월 현금흐름
- `getInstallRemain(r)` — 할부 잔여 횟수
- `getAllInvLatest()` — 계좌별 최신 데이터

### 렌더링
- `renderHome/Assets/Debt/Cashflow/Goals/InputPage/Analysis/Settings()`
- `renderDebt() / renderDebtLog()` — 대출 목록
- `renderInstallList() / renderInstallLog()` — 할부 목록
- `renderGoals()` — 목표 카드
- `renderWealthAnalysis()` — 순자산 분석
- `runGoalSim()` — 목표 시뮬레이션
- `runDebtSim()` — 대출 시뮬레이터
- `calcHealthInsurance()` — 건강보험료
- `calcFinanceIncome()` — 금융소득 종합과세
- `genAIReport(type)` — AI 리포트 생성 (monthly/annual/invest/advice)
- `buildShortPrompt(type)` — AI 프롬프트 생성
- `renderMarkdown(text)` — 마크다운 렌더링

### UI
- `goPage(id)` — 페이지 이동
- `setInputTab(t)` — 입력 탭 전환
- `setAnalysisTab(t)` — 분석 탭 전환
- `setDebtSub(t)` — 부채 서브탭 (loan/install)
- `setRealSub(t)` — 실물자산 서브탭 (list/tax)
- `bindComma(id)` — 숫자 입력 콤마 바인딩
- `fmtN(v) / fmtM(v) / fmtP(v)` — 숫자/억원/퍼센트 포맷
- `showToast(msg, type)` — 토스트 알림
- `applyLayout()` — 반응형 레이아웃 (모바일/태블릿/데스크탑)

---

## 미완성 / 해결 필요 사항

### 🔴 긴급
1. **AI 리포트 권한 문제** — GAS에서 `UrlFetchApp` 권한 승인 필요
   - Apps Script → `testFetch` 함수 실행 → 권한 승인
   - 또는 `appsscript.json`에 `script.external_request` 권한 추가 후 재배포
   - `ANTHROPIC_API_KEY` 스크립트 속성에 이미 설정됨

### 🟡 개선 필요
2. **AI 디버그 코드 제거** — 현재 genAIReport에 단계별 디버그 메시지 표시 중 → 완료 후 제거
3. **데이터 동기화 차이** — 패드/폰 간 캐시 불일치 → 캐시 초기화 후 재동기화로 해결

### 🟢 추가 예정 기능
- 투자 벤치마크 (KOSPI/S&P500 비교)
- 월말 리마인더 알림
- 전체 데이터 엑셀 내보내기

---

## 반응형 브레이크포인트
- **모바일**: ~768px (아이폰) — 바텀 네비게이션
- **태블릿**: 768px~ (아이패드) — 바텀 네비게이션 + 넓은 레이아웃
- **데스크탑**: 1024px~ (PC/맥) — 사이드바 220px

---

## 주요 버그 수정 이력
- HTML div 불균형 (page-cashflow, page-goals, page-debt) → 수정 완료
- GAS 문자열 숫자 NaN → Number() 변환으로 해결
- `page-inner` CSS 전역 적용 → 1024px 미디어쿼리로 이동
- `seg-tab` 모바일 잘림 → `overflow-x: auto` 추가
- `makeTxnRow` 메모 input 우측 잘림 → `min-width:0` 추가
- syncAll에 install 누락 → 추가 완료
- buildShortPrompt `\n` 실제 줄바꿈 삽입 버그 → raw string으로 수정

---

## 개발 환경 참고
- 단일 HTML 파일 (CSS + JS 인라인)
- 외부 라이브러리 없음 (순수 Vanilla JS)
- localStorage 캐시 키: `jf_db`, `jf_gas`
- 숫자 필드는 GAS에서 문자열로 오므로 항상 `Number()` 변환 필요
- div open/close 균형 반드시 유지 (브라우저 파싱 오류 원인)
