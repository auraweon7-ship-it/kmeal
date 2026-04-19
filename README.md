# 🇰🇷 전국 학교급식 AI 지도 (Korea School Meal AI Map)

<div align="center">

![Version](https://img.shields.io/badge/version-v8.2-blue)
![License](https://img.shields.io/badge/license-MIT-green)
![Status](https://img.shields.io/badge/status-active-success)
![Contest](https://img.shields.io/badge/contest-2026_Education_Data_AI-orange)

### 🏆 제8회 교육 공공데이터 AI 활용대회 출품작

**NEIS 교육 공공데이터 + Claude AI 기반 전국 학교급식 통합 분석 플랫폼**

[🌐 데모 사이트](https://auraweon7-ship-it.github.io/seoul-school-meal-/) · [📖 기술 문서](ARCHITECTURE.md) · [📊 데이터 출처](DATA_SOURCES.md) · [🎬 시연 영상](#-시연-영상)

</div>

---

## 📋 프로젝트 개요

서울 866개 초·중·고 학교급식 공공데이터를 **NEIS 교육정보 개방 포털 API**와 **학교알리미 API**로 실시간 연동하고, **Claude AI**로 분석·시각화하여, 학부모에게는 자녀 학교 급식의 영양·원산지·안전성 정보를, 학생에게는 식생활 인식을, 교사·교육청에는 학교별 급식 운영 진단을 제공하는 통합 플랫폼입니다. 

**v8.2에서는 전국 17개 시도교육청으로 확대**하여 대한민국 11,000여 개 학교의 급식 데이터를 한눈에 볼 수 있게 되었습니다.

### 🎯 프로젝트 미션

> **"교육 공공데이터와 AI로 전국 학교급식의 투명성을 높이고, 학부모·학생·교사·교육청을 하나로 연결하는 통합 플랫폼을 구축한다."**

---

## ✨ 핵심 기능

### 🗺️ 1. 전국 17개 시도교육청 실시간 지원

| 구분 | 지원 범위 |
|------|-----------|
| 🏙️ 특별시/광역시 | 8개 (서울, 부산, 대구, 인천, 광주, 대전, 울산, 세종) |
| 🏘️ 도/특별자치도 | 9개 (경기, 강원, 충북, 충남, 전북, 전남, 경북, 경남, 제주) |
| 📊 학교 수 | **11,000+개** (NEIS 실시간 연동) |
| 🏫 시/군/구 | **250+개** 좌표 지원 |

### 🔌 2. 실제 공공데이터 API 연동

```
🏛️ NEIS 교육정보 개방 포털 (open.neis.go.kr)
├─ 학교기본정보 API (/schoolInfo)
└─ 급식식단정보 API (/mealServiceDietInfo)

🏫 학교알리미 (open.schoolinfo.go.kr)
└─ 공시정보 API
```

### 🤖 3. Claude AI 기반 분석

- **7지표 Rule-based 평가** (열량·다양성·신선도·균형·원산지·계절성·문화)
- **AI 챗봇** (자연어 질의 응답)
- **데이터 스토리텔링** (일간/주간/월간 변화 서술)
- **가정 영양 비교** (학교급식 vs 가정식단)
- **친환경 탄소 발자국** (급식의 환경 영향 분석)

### 📊 4. 4개 탭 완전 통합

| 탭 | 주요 기능 | 전국 대응 |
|---|---------|---------|
| 🗺️ **지도** | Leaflet 기반 시각화 | ✅ 교육청별 자동 이동 |
| 📊 **차트** | Chart.js 11종 차트 | ✅ 전국 전용 3개 차트 |
| 🍱 **목록 & 필터** | 고급 필터링 + 검색 | ✅ 시도교육청 필터 |
| 🏆 **순위** | TOP 50 랭킹 | ✅ 4가지 정렬 방식 |

### 🛡️ 5. 안정성 보장 기능

- ⚡ **Hybrid Fallback**: API 실패 시 자동 Mock 데이터 전환
- 💾 **24시간 캐싱**: localStorage 기반 성능 최적화
- ⏱️ **타임아웃 보호**: 8초 AbortController
- 🔄 **병렬 처리**: 17개 교육청 Promise.all 동시 호출

---

## 🚀 빠른 시작

### 📦 로컬 실행 (권장)

```bash
# 1. 레포 클론
git clone https://github.com/auraweon7-ship-it/seoul-school-meal-.git
cd seoul-school-meal-

# 2. 로컬 서버 실행 (CORS 이슈 방지)
python -m http.server 8000

# 3. 브라우저 접속
# http://localhost:8000
```

### 🌐 GitHub Pages 배포

이 레포지토리는 GitHub Pages로 자동 배포됩니다.

1. Settings → Pages
2. Source: `main` 브랜치 선택
3. 접속: `https://auraweon7-ship-it.github.io/seoul-school-meal-/`

### 🔑 API 키 설정

프로젝트에 **NEIS API 키와 학교알리미 API 키가 이미 포함**되어 있어 별도 설정이 불필요합니다.

Claude AI 챗봇 사용 시에만 [Anthropic Console](https://console.anthropic.com)에서 발급받은 API 키를 우측 상단 "🤖 AI 설정"에서 입력해주세요.

---

## 🏗️ 기술 스택

### 💻 Frontend
- **HTML5 / CSS3 / Vanilla JavaScript** (프레임워크 無, 단일 파일)
- **Leaflet.js 1.9.4** - 인터랙티브 지도
- **Chart.js 4.4.1** - 데이터 시각화
- **CARTO Dark** - 다크 테마 지도 타일

### 🔌 APIs
- **NEIS Open API** - 학교기본정보, 급식식단정보
- **학교알리미 Open API** - 공시정보
- **Anthropic Claude API** - AI 해설 생성

### 🎨 Design System
- **Pretendard** - 한글 폰트
- **JetBrains Mono** - 모노스페이스 폰트
- **다크 네이비 + 황금색** - 프로페셔널 대시보드

---

## 📁 프로젝트 구조

```
seoul-school-meal-/
├── index.html                     # 🎯 메인 대시보드 (445KB, v8.2)
├── README.md                      # 📖 프로젝트 설명 (이 파일)
├── ARCHITECTURE.md                # 🏗️ 기술 아키텍처 상세
├── DATA_SOURCES.md                # 📊 활용 공공데이터 목록
├── PROPOSAL.md                    # 📝 작품 기획서 (공모전 제출용)
├── CHANGELOG.md                   # 📋 버전별 변경 이력
├── LICENSE                        # ⚖️ MIT 라이선스
└── .gitignore                     # 🔒 Git 무시 목록
```

---

## 🎬 시연 영상

[🎥 YouTube 시연 영상 (3분)](https://youtu.be/XXXXXXXXXXX) _(업로드 예정)_

### 주요 시연 장면
1. **0:00~0:30** - 전국 모드 활성화 (17개 교육청 병렬 로딩)
2. **0:30~1:15** - 차트 탭 전국 전용 차트 3종
3. **1:15~2:00** - 목록 & 필터 탭 시도교육청 필터링
4. **2:00~2:45** - 순위 탭 TOP 50 랭킹 + 교육청 배지
5. **2:45~3:00** - 공공데이터 + AI 비전

---

## 🏆 공모전 정보

- **대회명**: 제8회 교육 공공데이터 AI 활용대회
- **주최**: 교육부 · 17개 시도교육청
- **주관**: 한국교육학술정보원(KERIS)
- **접수 마감**: 2026년 5월 31일 (일)
- **시상**: 총 154팀 · 총 상금 5,050만원

### 심사 기준
- **창의성** (30%)
- **기술 완성도** (40%)
- **활용 가능성** (30%)

---

## 👥 팀 정보

### 🎓 지도 교수
- **박정원 교수** (한국외국어대학교 중국언어문화학부)
- 소속: 국가교육위원회 AI특별위원회 / 교육부 AI인재양성추진단

### 💻 개발 참여
- **박준범**, **박세윤**

### 📧 문의
- Email: park9626@hanmail.net
- Office: 한국외국어대학교 교수회관 529호

---

## 🗺️ 로드맵

### ✅ Phase 1-4 (완료)
- [x] 서울 866개 학교 지도 + AI 챗봇
- [x] NEIS + 학교알리미 API 실연동
- [x] 전국 17개 시도교육청 확대 (11,000+개 학교)
- [x] 4개 탭 모두 전국화 완료

### 🔄 Phase 5 (계획)
- [ ] 모바일 앱 (PWA 전환)
- [ ] 학교 간 비교 심화 분석
- [ ] 교육청 관리자 대시보드
- [ ] 학부모 맞춤 알림 기능
- [ ] 외국어 지원 (영어, 중국어, 일본어)

---

## 📜 라이선스

이 프로젝트는 **MIT License** 하에 배포됩니다. 자세한 내용은 [LICENSE](LICENSE) 파일을 참고하세요.

공공데이터 활용 시 각 출처의 이용 조건을 준수해 주세요:
- NEIS 교육정보 개방 포털: [이용약관](https://open.neis.go.kr)
- 학교알리미: [이용약관](https://open.schoolinfo.go.kr)

---

## 🙏 감사의 말

본 프로젝트는 다음 공공기관의 데이터 개방 노력에 기반하여 제작되었습니다:

- 🏛️ **교육부** - 교육 데이터 개방 정책
- 🏫 **17개 시도교육청** - NEIS 데이터 제공
- 🏢 **한국교육학술정보원(KERIS)** - 공모전 주관
- 🤖 **Anthropic** - Claude AI API

---

## ⭐ 프로젝트가 마음에 드신다면?

이 레포지토리에 **⭐ Star**를 눌러주세요!  
피드백과 제안은 [Issues](https://github.com/auraweon7-ship-it/seoul-school-meal-/issues)로 언제든 환영합니다.

---

<div align="center">

**🇰🇷 Made with ❤️ for Korean Education**  
© 2026 박준범, 박세윤 · 지도: 박정원 교수 (한국외대)

🏆 제8회 교육 공공데이터 AI 활용대회 출품작

</div>
