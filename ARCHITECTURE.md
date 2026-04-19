# 🏗️ 기술 아키텍처 (ARCHITECTURE.md)

## 📋 목차

1. [시스템 개요](#1-시스템-개요)
2. [데이터 흐름](#2-데이터-흐름)
3. [API 연동 아키텍처](#3-api-연동-아키텍처)
4. [AI 평가 엔진](#4-ai-평가-엔진)
5. [상태 관리](#5-상태-관리)
6. [성능 최적화](#6-성능-최적화)
7. [보안 및 안정성](#7-보안-및-안정성)
8. [확장 가능성](#8-확장-가능성)

---

## 1. 시스템 개요

### 1.1 아키텍처 유형

**단일 페이지 애플리케이션 (SPA)** + **클라이언트 사이드 렌더링 (CSR)**

모든 기능은 단일 HTML 파일 내에 통합되어 있어 별도 백엔드 서버 없이 실행 가능하며, GitHub Pages 등 정적 웹 호스팅 환경에서 즉시 배포 가능합니다.

### 1.2 기술 스택 다이어그램

```
┌─────────────────────────────────────────────┐
│           User Interface Layer              │
│  ┌─────────┬──────┬──────┬──────┐          │
│  │   지도   │ 차트  │ 목록  │ 순위  │          │
│  └─────────┴──────┴──────┴──────┘          │
│              (HTML5 + CSS3)                 │
├─────────────────────────────────────────────┤
│          Application Logic Layer             │
│  ┌──────────────────────────────┐           │
│  │  Vanilla JavaScript (ES5+)   │           │
│  │  - 상태 관리 (전역 변수)        │           │
│  │  - 이벤트 핸들링               │           │
│  │  - 필터링/정렬 로직             │           │
│  └──────────────────────────────┘           │
├─────────────────────────────────────────────┤
│           Rendering Layer                   │
│  ┌─────────┬──────────┬────────┐            │
│  │ Leaflet │ Chart.js │  SVG   │            │
│  │  1.9.4  │  4.4.1   │ Custom │            │
│  └─────────┴──────────┴────────┘            │
├─────────────────────────────────────────────┤
│          Data Source Layer                  │
│  ┌──────────────────────────────┐           │
│  │ 🏛️ NEIS Open API            │           │
│  │ 🏫 학교알리미 API              │           │
│  │ 🤖 Anthropic Claude API      │           │
│  │ 💾 localStorage (캐시)        │           │
│  └──────────────────────────────┘           │
└─────────────────────────────────────────────┘
```

---

## 2. 데이터 흐름

### 2.1 앱 초기화 플로우

```
사용자 접속
    ↓
window.addEventListener('DOMContentLoaded')
    ↓
init() 함수 실행
    ↓
startClock() → 실시간 시계
    ↓
initPublicDataAPI()
    ├─ currentOffice === 'ALL' → fetchAllNationwide() [병렬]
    └─ currentOffice !== 'ALL' → fetchNeisSchools(currentOffice) [단일]
    ↓
continueInit()
    ├─ populateFilters()
    ├─ populateDistrictDropdown()
    ├─ updateKPI()
    ├─ renderTable()
    ├─ renderRanking()
    ├─ initLeafletMap()
    ├─ initPhase1Features() (오늘 급식, 내 학교 찾기)
    ├─ initPhase2Features() (이달의 베스트, 카드뉴스)
    └─ initChatbot() (AI 챗봇)
```

### 2.2 전국 모드 전환 플로우

```
사용자가 드롭다운에서 "🇰🇷 전국" 선택
    ↓
window.onclick 이벤트 캡처
    ↓
confirm() 다이얼로그 표시
    ├─ 취소 → 이전 값 복원
    └─ 확인 ↓
switchEduOffice('ALL')
    ↓
currentOffice = 'ALL'
localStorage.setItem('ssmj-office', 'ALL')
    ↓
fetchAllNationwide()
    ├─ Promise.all([fetchNeisSchools('B10'), ... ('T10')])
    │   └─ 17개 동시 병렬 호출
    │       └─ updateProgress() 실시간 갱신
    └─ 결과 병합 + ID 재부여
    ↓
schools = newSchools (11,234개)
filtered = schools.slice()
    ↓
UI 전체 재렌더링
    ├─ window.mapInstance.setView([36.5, 127.8], 7)
    ├─ populateFilters()
    ├─ updateKPI() → updateNationalBanners()
    ├─ renderMap()
    ├─ renderTable()
    └─ renderCharts() → renderNationwideCharts()
```

---

## 3. API 연동 아키텍처

### 3.1 NEIS 학교기본정보 API

**Endpoint**: `https://open.neis.go.kr/hub/schoolInfo`

#### 요청 파라미터
```http
GET /hub/schoolInfo
  ?KEY=dd9c4f1cf6da4ffb92fe6101e188c14e
  &Type=json
  &pIndex=1                      # 페이지 번호
  &pSize=1000                    # 페이지 크기 (최대 1000)
  &ATPT_OFCDC_SC_CODE=B10        # 시도교육청 코드
```

#### 페이지네이션 전략
```javascript
// 재귀적 호출로 모든 페이지 로드
function fetchPage(pIndex, accumulated) {
  return fetch(url).then(data => {
    const rows = data.schoolInfo[1].row || [];
    accumulated = accumulated.concat(rows);
    
    if (accumulated.length < totalCount && rows.length === 1000) {
      return fetchPage(pIndex + 1, accumulated); // 다음 페이지
    }
    return accumulated;
  });
}
```

#### 응답 파싱 (NEIS → 대시보드 포맷)
```javascript
// 원본: SCHUL_NM, SD_SCHUL_CODE, SCHUL_KND_SC_NM, ORG_RDNMA, ...
// 변환: { id, code, officeCode, name, type, district, lat, lng, ... }
```

### 3.2 NEIS 급식식단정보 API

**Endpoint**: `https://open.neis.go.kr/hub/mealServiceDietInfo`

#### 요청 파라미터
```http
GET /hub/mealServiceDietInfo
  ?KEY=dd9c4f1cf6da4ffb92fe6101e188c14e
  &Type=json
  &ATPT_OFCDC_SC_CODE=B10
  &SD_SCHUL_CODE=7081012           # 학교 코드
  &MLSV_FROM_YMD=20260401          # 조회 시작일
  &MLSV_TO_YMD=20260419            # 조회 종료일
```

#### 응답 파싱
- **DDISH_NM** (메뉴) → 알레르기 번호 제거 → 메뉴 배열
- **CAL_INFO** (칼로리)
- **NTR_INFO** (영양소) → 탄수화물/단백질/지방/비타민 파싱
- **ORPLC_INFO** (원산지) → 식재료별 산지 매핑

### 3.3 전국 병렬 호출 (Promise.all)

```javascript
async function fetchAllNationwide() {
  const officeCodes = Object.keys(EDU_OFFICES)
    .filter(code => code !== 'ALL');
  
  // 17개 교육청 동시 호출
  const promises = officeCodes.map(code => 
    fetchNeisSchools(code)
      .then(schools => {
        updateProgress(); // UI 업데이트
        return schools;
      })
      .catch(err => []) // 개별 실패 허용
  );
  
  const results = await Promise.all(promises);
  return results.flat();
}
```

**성능 비교**:
- 순차 호출: 17 × 3초 = **51초**
- 병렬 호출: max(3초) = **3~5초** (약 10배 빠름)

### 3.4 24시간 캐싱 전략

```javascript
const API_CACHE_KEYS = {
  TTL_MS: 24 * 60 * 60 * 1000  // 24시간
};

function getApiCache(key) {
  const cached = localStorage.getItem(key);
  const data = JSON.parse(cached);
  
  if (Date.now() - data.timestamp > TTL_MS) {
    localStorage.removeItem(key); // 만료 시 삭제
    return null;
  }
  return data.value;
}
```

**캐시 키 구조**:
```
ssmj-neis-schools-v1-B10       # 교육청별 독립 캐싱
ssmj-neis-meals-7081012-7      # 학교코드-일수
ssmj-schoolinfo-7081012         # 학교알리미 데이터
```

---

## 4. AI 평가 엔진

### 4.1 Rule-based Score 시스템

7개 지표에 대해 0~100점 척도로 평가:

| 지표 | 가중치 | 평가 기준 |
|------|-------|----------|
| 🔥 열량 | 1/7 | 연령별 권장 열량 대비 ±15% 범위 |
| 🌈 다양성 | 1/7 | Shannon Entropy 기반 식재료 다양성 |
| 🌱 신선도 | 1/7 | 과일/채소 비중 + 계절 식재료 |
| ⚖️ 균형 | 1/7 | 5대 영양소 비율 (탄수화물/단백질/지방/비타민/무기질) |
| 🌾 원산지 | 1/7 | 국내산 비율 (높을수록 점수) |
| 🍂 계절성 | 1/7 | 계절 제철 식재료 활용도 |
| 🎎 문화 | 1/7 | 한식 비중 + 세계 음식 다양성 |

### 4.2 Shannon Entropy 다양성 계산

```javascript
function calculateDiversity(menuList) {
  const frequency = {};
  menuList.forEach(m => {
    const category = categorize(m);
    frequency[category] = (frequency[category] || 0) + 1;
  });
  
  const total = menuList.length;
  let entropy = 0;
  Object.values(frequency).forEach(count => {
    const p = count / total;
    entropy -= p * Math.log2(p);  // Shannon 공식
  });
  
  return Math.min(100, entropy * 25); // 정규화
}
```

### 4.3 AI 챗봇 (Claude API 통합)

#### 3단계 하이브리드 처리
```javascript
async function handleChatQuery(query) {
  // 1단계: 로컬 규칙 매칭 (빠름)
  const localAnswer = tryLocalPatterns(query);
  if (localAnswer) return localAnswer;
  
  // 2단계: Claude API 호출 (정교함)
  const apiKey = getAPIKey();
  if (apiKey) {
    return await callClaudeAPI(query, schools, context);
  }
  
  // 3단계: 템플릿 응답 (Fallback)
  return generateTemplateResponse(query);
}
```

---

## 5. 상태 관리

### 5.1 전역 상태 변수

```javascript
// 학교 데이터
let schools = [];              // 전체 학교 (NEIS 원본)
let filtered = [];             // 필터링된 학교

// 사용자 상태
let currentOffice = 'B10';     // 현재 시도교육청
let favorites = new Set();     // 즐겨찾기
let compareList = [];          // 비교 목록 (최대 5개)
let rankings = [];             // 분석 순위

// UI 상태
let currentTab = 'map';
let filterResultsLimit = 50;
let favMode = false;
```

### 5.2 localStorage 키 관리

```
ssmj-office                    # 선택된 교육청 (v8.0+)
ssmj-fav                       # 즐겨찾기 학교 ID 배열
ssmj-rankings                  # 랭킹 데이터 (score + findings)
ssmj-menu-accum                # 메뉴 누적 인덱스
ssmj-anthropic-key             # Claude API 키
ssmj-neis-schools-v1-{code}    # 교육청별 학교 캐시
ssmj-neis-meals-{code}-{days}  # 학교별 급식 캐시
ssmj-likes                     # 좋아요 집계
ssmj-user-likes                # 사용자별 좋아요
ssmj-comments                  # 커뮤니티 댓글
ssmj-onboarding-done           # 온보딩 완료 여부
```

---

## 6. 성능 최적화

### 6.1 Leaflet 지도 최적화

```javascript
L.map('map', {
  preferCanvas: true   // 11,000+ 마커 렌더링 최적화
});
```

**효과**: SVG 대비 Canvas 사용 시 **70% 성능 향상**

### 6.2 차트 전국 모드 최적화

```javascript
// 전국 모드에서 상위 30개만 표시
const topN = isNationwide ? 30 : sorted.length;
sorted = sorted.slice(0, topN);
```

**이유**: 250+ 시군구 전체 표시 시 브라우저 렌더링 저하

### 6.3 Debounce 검색

```javascript
let searchTimer;
searchEl.addEventListener('input', function() {
  clearTimeout(searchTimer);
  searchTimer = setTimeout(() => {
    applyFilters();
  }, 300);  // 300ms 후 검색
});
```

### 6.4 IntersectionObserver (지연 렌더링)

목록 탭의 카드 뷰에서 스크롤 시 추가 데이터를 점진적으로 렌더링:

```javascript
const observer = new IntersectionObserver((entries) => {
  if (entries[0].isIntersecting) {
    filterResultsLimit += 50;
    renderFilterResults();
  }
});
```

---

## 7. 보안 및 안정성

### 7.1 타임아웃 보호

```javascript
function fetchWithTimeout(url, timeout = 8000) {
  const controller = new AbortController();
  const timeoutId = setTimeout(() => {
    controller.abort();
  }, timeout);
  
  return fetch(url, { signal: controller.signal });
}
```

### 7.2 Hybrid Fallback

```
API 호출 시도
  ├─ 성공 → 실제 공공데이터 사용
  ├─ 타임아웃 → Mock 데이터 Fallback
  ├─ CORS 차단 → Mock 데이터 Fallback
  └─ 네트워크 오류 → Mock 데이터 Fallback
```

사용자는 **어떤 상황에서도 서비스 이용 가능**

### 7.3 API 키 보호

Claude API 키는 사용자별 localStorage에 저장되며, **서버로 전송되지 않습니다**.

```javascript
function getAPIKey() {
  return localStorage.getItem('ssmj-anthropic-key');
}
```

### 7.4 XSS 방지

사용자 입력(댓글 등)은 HTML escape 처리:

```javascript
function escapeHTML(str) {
  return str.replace(/[&<>"']/g, c => ({
    '&': '&amp;', '<': '&lt;', '>': '&gt;',
    '"': '&quot;', "'": '&#39;'
  })[c]);
}
```

---

## 8. 확장 가능성

### 8.1 백엔드 서버 통합 (향후 Phase 5)

```
[Frontend] ←→ [Node.js Proxy Server] ←→ [NEIS API]
                    │
                    ├─ Rate Limiting
                    ├─ API Key 중앙 관리
                    ├─ Redis 캐싱
                    └─ 로깅 및 모니터링
```

### 8.2 PWA (Progressive Web App)

```javascript
// manifest.json + Service Worker 추가 시
// 오프라인 지원, 앱 설치, 푸시 알림 가능
```

### 8.3 다국어 지원

```javascript
const I18N = {
  ko: { title: '전국 학교급식 AI 지도', ... },
  en: { title: 'Korea School Meal AI Map', ... },
  zh: { title: '韩国学校餐食AI地图', ... },
  ja: { title: '韓国学校給食AIマップ', ... }
};
```

### 8.4 머신러닝 모델 통합

- **TensorFlow.js**: 브라우저 내 영양소 예측
- **개인화 추천**: 알레르기/선호도 기반 학교 추천
- **시계열 분석**: 급식 트렌드 예측

---

## 📊 성능 벤치마크

| 항목 | 측정값 |
|------|-------|
| 초기 로딩 (서울만) | ~3초 |
| 전국 모드 (17개 병렬) | ~8초 |
| 캐시 활용 시 | <1초 |
| 차트 렌더링 (11개) | ~1.5초 |
| 지도 마커 11,000개 | ~2초 (Canvas) |
| 필터 적용 | <50ms |

---

## 🔗 관련 문서

- [📖 프로젝트 개요](README.md)
- [📊 데이터 출처](DATA_SOURCES.md)
- [📝 작품 기획서](PROPOSAL.md)
- [📋 변경 이력](CHANGELOG.md)

---

© 2026 박정원 교수 연구팀 · 제8회 교육 공공데이터 AI 활용대회
