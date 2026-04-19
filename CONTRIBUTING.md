# 🤝 기여 가이드 (CONTRIBUTING.md)

이 프로젝트에 관심을 가져 주셔서 감사합니다! 🇰🇷

본 가이드는 프로젝트에 기여하시는 분들을 위한 안내입니다.

## 📋 목차

1. [행동 강령](#행동-강령)
2. [기여 방법](#기여-방법)
3. [개발 환경 설정](#개발-환경-설정)
4. [Pull Request 절차](#pull-request-절차)
5. [코딩 컨벤션](#코딩-컨벤션)
6. [이슈 제출](#이슈-제출)

---

## 행동 강령

모든 기여자는 다음의 원칙을 준수해주세요:

- ✅ 존중과 배려: 다른 기여자의 의견을 존중합니다
- ✅ 건설적 비판: 비판은 사람이 아닌 코드에 대해서만
- ✅ 개방적 자세: 다양한 관점을 환영합니다
- ✅ 교육적 가치: 이 프로젝트는 교육 공공데이터를 위한 것입니다

---

## 기여 방법

### 🐛 버그 제보

1. [Issues 페이지](https://github.com/auraweon7-ship-it/seoul-school-meal-/issues) 확인
2. 이미 제보된 버그가 아닌지 검색
3. 새 이슈 생성 시 다음 정보 포함:
   - 문제 상황 (스크린샷 포함)
   - 재현 방법 (단계별)
   - 브라우저 및 OS 정보
   - 기대 동작 vs 실제 동작

### ✨ 새 기능 제안

기능 제안은 먼저 이슈로 등록 후 논의해주세요:
- **Feature Request** 라벨 선택
- 제안 배경 + 기대 효과
- 가능하면 구현 방안 간략히

### 📖 문서 개선

다음 문서들은 항상 개선 기회가 있습니다:
- README.md - 사용자 친화적 설명
- ARCHITECTURE.md - 기술 상세
- 주석 - 복잡한 로직 설명

---

## 개발 환경 설정

### 🔧 로컬 실행

```bash
# 1. 저장소 클론
git clone https://github.com/YOUR_USERNAME/seoul-school-meal-.git
cd seoul-school-meal-

# 2. 로컬 서버 실행 (Python)
python -m http.server 8000

# 또는 Node.js
npx http-server

# 3. 브라우저 접속
# http://localhost:8000
```

### 🔍 로컬 API 키 설정

본 프로젝트의 NEIS/학교알리미 API 키는 공개 테스트용으로 코드에 포함되어 있습니다.
실제 배포 시에는 **자체 API 키로 교체**하는 것이 권장됩니다:

1. [NEIS Open API](https://open.neis.go.kr) 회원가입
2. 인증키 신청 (무료)
3. `PUBLIC_API_CONFIG.NEIS.KEY` 수정

---

## Pull Request 절차

### 1️⃣ Fork & Clone
```bash
# GitHub에서 Fork 후
git clone https://github.com/YOUR_USERNAME/seoul-school-meal-.git
cd seoul-school-meal-
git remote add upstream https://github.com/auraweon7-ship-it/seoul-school-meal-.git
```

### 2️⃣ 브랜치 생성
```bash
# 기능 추가
git checkout -b feature/new-feature-name

# 버그 수정
git checkout -b fix/bug-description

# 문서 개선
git checkout -b docs/improve-xxx
```

### 3️⃣ 개발 및 테스트
```bash
# 코드 수정
vim index.html

# 로컬 테스트
python -m http.server 8000

# 브라우저에서 모든 기능 테스트
```

### 4️⃣ 커밋
```bash
git add .
git commit -m "feat: Add support for custom themes"

# 커밋 메시지 형식:
# feat: 새 기능
# fix: 버그 수정
# docs: 문서 개선
# style: 스타일 변경
# refactor: 리팩토링
# test: 테스트 추가
# chore: 기타 (설정, 빌드 등)
```

### 5️⃣ Push & PR
```bash
git push origin feature/new-feature-name
```

GitHub 페이지에서 Pull Request 생성:
- 제목: 명확하고 간결하게
- 본문: 변경 내용, 이유, 테스트 결과
- 관련 이슈: `Closes #123`

---

## 코딩 컨벤션

### 📝 JavaScript

```javascript
// ✅ 권장
function fetchData(officeCode) {
  return fetch(url)
    .then(res => res.json())
    .catch(err => console.error(err));
}

// ❌ 지양
function fd(x){return fetch(url).then(res=>res.json()).catch(err=>console.log(err))}
```

#### 규칙
- **들여쓰기**: 2칸 스페이스
- **세미콜론**: 항상 사용
- **따옴표**: 작은따옴표 선호 (`'text'`)
- **함수명**: camelCase
- **상수**: UPPER_SNAKE_CASE
- **주석**: 한글 권장 (교육 목적)

### 🎨 CSS

```css
/* ✅ 권장 */
.filter-group {
  display: flex;
  gap: 12px;
  padding: 14px 18px;
}

.filter-group.filter-group-office {
  flex: 1.3;
}

/* ❌ 지양 */
.fg{display:flex;gap:12px;padding:14px 18px}
```

#### 규칙
- **BEM 스타일**: `block__element--modifier`
- **들여쓰기**: 2칸 스페이스
- **한 줄에 한 속성**
- **주석**: 섹션별 구분

### 📂 파일 구조

```
seoul-school-meal-/
├── index.html              # 단일 파일 (모든 코드 포함)
├── README.md
├── ARCHITECTURE.md
├── DATA_SOURCES.md
├── PROPOSAL.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── LICENSE
└── .gitignore
```

---

## 이슈 제출

### 🏷️ 이슈 라벨

| 라벨 | 용도 |
|------|------|
| `bug` | 버그 제보 |
| `feature` | 새 기능 제안 |
| `enhancement` | 기능 개선 |
| `documentation` | 문서 관련 |
| `good first issue` | 초보자 환영 |
| `help wanted` | 도움 필요 |
| `question` | 질문 |

### 📝 이슈 템플릿

#### 버그 제보
```markdown
## 🐛 버그 설명
(버그에 대한 명확한 설명)

## 🔄 재현 방법
1. '...'로 이동
2. '...' 클릭
3. '...' 확인

## 🎯 기대 동작
(기대했던 동작)

## 💻 환경
- OS: Windows 10 / macOS 14 / Ubuntu 22
- Browser: Chrome 120 / Safari 17 / Firefox 121
- Version: v8.2

## 📸 스크린샷
(가능하면 첨부)
```

#### 기능 제안
```markdown
## ✨ 기능 제안
(제안하는 기능)

## 🎯 해결하려는 문제
(현재의 불편함 또는 부족한 점)

## 💡 제안 방안
(구체적인 구현 아이디어)

## 🔄 대안
(다른 방법들)
```

---

## 🎓 교육적 기여

본 프로젝트는 **교육 공공데이터 + AI**를 연구하는 학생과 교수님들의 협력 프로젝트입니다. 다음과 같은 기여를 환영합니다:

### 🎒 학생 기여
- 🐛 버그 제보
- 📖 문서 번역
- 🎨 디자인 개선
- ✨ 작은 기능 추가

### 👨‍🏫 교수 기여
- 📚 교육 콘텐츠 추가
- 🎯 AI 모델 개선
- 🔬 연구 데이터 제공
- 📝 논문 공동 집필

### 🏛️ 교육청 기여
- 📊 현장 피드백
- 💡 정책 연계 아이디어
- 🎪 실증 사업 협력

---

## 🙏 감사의 말

모든 기여자 분들께 진심으로 감사드립니다!

본 프로젝트는 다음과 같은 커뮤니티의 도움으로 발전하고 있습니다:
- 🎓 한국외국어대학교 박정원 교수 연구팀
- 💻 오픈소스 커뮤니티
- 🏛️ 교육부 및 KERIS
- 🏫 17개 시도교육청

---

## 📧 연락처

- 📬 Email: park9626@hanmail.net
- 🐛 Issues: [GitHub Issues](https://github.com/auraweon7-ship-it/seoul-school-meal-/issues)
- 💬 Discussions: [GitHub Discussions](https://github.com/auraweon7-ship-it/seoul-school-meal-/discussions)

---

**함께 만들어 갑니다! 🇰🇷✨**

© 2026 박정원 교수 연구팀
