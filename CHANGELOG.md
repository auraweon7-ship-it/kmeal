# 📋 변경 이력 (CHANGELOG.md)

이 문서는 프로젝트의 주요 변경 사항을 기록합니다.

형식은 [Keep a Changelog](https://keepachangelog.com/ko/1.0.0/)을 따르며, 
버전 관리는 [Semantic Versioning](https://semver.org/lang/ko/)을 준수합니다.

---

## [v8.2] - 2026-04-19 (Complete Nationwide Edition) 🇰🇷

### ✨ 새 기능 (Added)
- **🍱 목록 & 필터 탭에 시도교육청 필터 추가**
  - 18개 옵션 드롭다운 (전국 + 17개 교육청)
  - 황금색 강조 스타일
  - 각 교육청별 학교 수 동적 표시
  - 활성 필터 칩에 "🏛️ 시도교육청: 서울" 형태 표시

- **📊 차트 탭 전국 데이터 전용 3종 차트**
  - 17개 시도교육청별 학교 수 (Horizontal Bar)
  - 지역별 학교급 분포 (Stacked Bar)
  - 교육청별 직영율 TOP (Bar)
  - 전국 모드에서만 자동 표시

- **🏆 순위 탭 필터 바 추가**
  - 시도교육청 필터 (18개 옵션)
  - 4가지 정렬 방식 (점수↓, 점수↑, 이름, 교육청별)
  - 학교급 필터 (초/중/고)
  - 학교명에 교육청 배지 표시
  - Top 30 → Top 50으로 확대

### 🔧 개선 (Changed)
- `renderCharts()` 함수에 `renderNationwideCharts()` 호출 추가
- `renderRanking()` 함수에 필터/정렬 로직 통합
- 시/군/구별 차트가 전국 모드 시 `[교육청] 시군구` 형식으로 표시
- 전국 모드에서 차트 성능 최적화 (상위 30개 제한)

### 📊 통계
- 검증 항목: **41/41 통과 (100%)**
- 최종 파일 크기: **445KB**

---

## [v8.1] - 2026-04-19 (Full Nationwide Edition)

### ✨ 새 기능 (Added)
- **🇰🇷 지도 탭 드롭다운에 "전국" 옵션 추가**
  - 17개 시도교육청 동시 선택
  - Promise.all 기반 병렬 API 호출
  - 무지개 그라데이션 펄스 애니메이션
  - 실시간 진행률 표시 (1/17 → 17/17)

- **⚡ fetchAllNationwide() 함수 신규 구현**
  - 17개 교육청 동시 호출 (순차 51초 → 병렬 3~5초)
  - 개별 실패 허용 (빈 배열 Fallback)
  - ID 자동 재부여 (중복 방지)

### 🔧 개선 (Changed)
- `EDU_OFFICES`에 `ALL` 가상 교육청 추가
- `switchEduOffice` 에 `ALL` 분기 처리
- 확인 다이얼로그로 로딩 시간 사전 안내

---

## [v8.0] - 2026-04-19 (Nationwide Edition)

### ✨ 새 기능 (Added)
- **🗺️ 전국 17개 시도교육청 확대**
  - `EDU_OFFICES` 객체 (B10~T10, 17개 정의)
  - `NATIONAL_DISTRICTS_BY_OFFICE` (250+ 시군구 좌표)
  - `switchEduOffice()` 함수로 실시간 전환

- **🎨 지도 탭 시도교육청 드롭다운**
  - 황금색 강조 스타일
  - 각 교육청별 이모지 아이콘

- **📊 3개 탭에 전국 통계 배너**
  - 차트/목록/순위 탭 상단
  - 현재 교육청, 학교 수, 시군구 수 표시

### 🔧 개선 (Changed)
- 페이지 타이틀: "서울 학교급식 지도" → "전국 학교급식 AI 지도"
- 헤더: "🍱 서울 학교급식" → "🇰🇷 전국 학교급식 AI"
- `fetchNeisSchools(officeCode)` 동적 교육청 지원
- `fetchNeisMeals(schoolCode, days, officeCode)` 동적 지원
- `extractDistrictNational()` 전국 주소 파싱

### 🐛 수정 (Fixed)
- 중복 키 문제 해결 (nested 구조로 변경)
- 잔여 "자치구" 텍스트 → "시/군/구" 일괄 변경

---

## [v7.4] - 2026-04-19 (Public Data Integration)

### ✨ 새 기능 (Added)
- **🏫 NEIS 교육정보 개방 포털 API 실연동**
  - 학교기본정보 API (`/schoolInfo`)
  - 급식식단정보 API (`/mealServiceDietInfo`)
  - API 키: `dd9c4f1cf6da4ffb92fe6101e188c14e`

- **🏫 학교알리미 Open API 연동**
  - 공시정보 API
  - API 키: `4f82fad119534e758e8c028ebdb5a49f`

- **💾 24시간 localStorage 캐싱**
  - API 호출 최소화
  - 빠른 재접속 로딩

- **🛡️ Hybrid Fallback 전략**
  - API 실패 시 Mock 데이터 자동 전환
  - 서비스 안정성 보장

- **⏱️ fetchWithTimeout() 함수**
  - 8초 AbortController
  - 타임아웃 보호

### 🎨 UI 개선 (Changed)
- 헤더 티커에 "🟢 NEIS 실시간 연동" 배지 추가
- 푸터에 공공데이터 출처 명시
- Toast 알림: "✅ NEIS 실제 급식 데이터로 분석 완료!"

---

## [v6.3] - 2026-04-18 (Phase 4 Complete)

### ✨ 새 기능 (Added)
- **💬 커뮤니티 기능**
  - 학교별 좋아요/댓글
  - 닉네임 + 역할 선택
  - localStorage 저장

- **🌱 친환경 탄소 발자국**
  - 급식 메뉴의 탄소 배출량 계산
  - 국내산 vs 수입산 비교

- **📖 데이터 스토리텔링**
  - 5챕터 서사 구조
  - AI 기반 해설 자동 생성

---

## [v5.0] - 2026-04-17 (Phase 3 Complete)

### ✨ 새 기능 (Added)
- **🤖 Claude AI 챗봇**
  - 자연어 질의 응답
  - 하이브리드 처리 (로컬 + API)
  - 학교 추천 기능

- **🏠 가정 영양 비교**
  - 학교급식 vs 가정식단
  - 영양소 레이더 차트

---

## [v4.0] - 2026-04-16 (Phase 2 Complete)

### ✨ 새 기능 (Added)
- **🏆 이달의 베스트 학교** 위젯
- **🎴 카드뉴스 공유** (SNS 최적화)
- **🍎 알레르기 필터** (7개 주요 알레르기)

---

## [v3.0] - 2026-04-15 (Phase 1 Complete)

### ✨ 새 기능 (Added)
- **🎓 3단계 온보딩** 가이드
- **🔍 내 학교 찾기** (검색 + 즐겨찾기)
- **🌟 오늘의 급식 추천** 카드
- **⭐ 즐겨찾기 시스템**
- **⚖️ 학교 비교** (최대 5개, Radar 차트)

---

## [v2.0] - 2026-04-14 (Mobile Optimization)

### 🎨 개선 (Changed)
- 21개 미디어쿼리 추가
- 모바일 반응형 최적화
- 터치 제스처 지원

---

## [v1.0] - 2026-04-13 (Initial Release)

### ✨ 초기 기능 (Added)
- 🗺️ Leaflet 기반 지도
- 📊 Chart.js 11종 차트
- 🍱 목록 & 필터 시스템
- 🏆 순위 시스템 (Rule-based)
- 7지표 평가 엔진
- 다크 테마 디자인

---

## 🔗 링크

- [📖 README](README.md)
- [🏗️ 기술 아키텍처](ARCHITECTURE.md)
- [📊 데이터 출처](DATA_SOURCES.md)
- [📝 작품 기획서](PROPOSAL.md)

---

## 📅 로드맵

### 🎯 Phase 5 (계획 중)
- [ ] PWA 전환 (오프라인 지원)
- [ ] 학교 간 심화 비교
- [ ] ML 기반 메뉴 추천
- [ ] 다국어 지원

### 🌏 Phase 6 (장기)
- [ ] 해외 학교 비교
- [ ] UN SDG 연계

---

© 2026 박정원 교수 연구팀
