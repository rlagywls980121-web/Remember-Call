# RememberCall 리멤버콜 — 웹사이트

> 자녀의 목소리를 합성한 AI가 부모님께 매일 안부 전화를 걸어,  
> 다정한 대화 속에서 인지 건강을 살펴드리는 시니어 케어 서비스.

---

## 파일 구성

```
website/
├── index.html          # 홈 (소개 랜딩 페이지)
├── service.html        # 서비스 소개 상세 페이지
├── preview.html        # 서비스 미리보기 (음성 녹음 → AI 학습 → 전화 체험)
├── terms.html          # 이용약관 · 개인정보처리방침
├── banner-movie2.mp4   # 히어로 배경 영상 (현재 적용)
├── banner-movie.mp4    # 히어로 배경 영상 (이전 버전)
├── banner.jpeg         # 히어로 배경 이미지 (영상 로드 실패 시 poster)
├── banner2.png
├── banner3.png
└── banner33.png
```

**외부 의존성 (CDN, 설치 불필요)**
- 폰트: Cormorant Garamond, Noto Serif KR, Pretendard Variable
- 아이콘: Tabler Icons v2.47
- AI 음성: ElevenLabs API (preview.html에서만 사용)

---

## 페이지별 상세

### index.html — 홈

**헤더**
- 상단 고정(sticky), 스크롤 시 반투명 베이지로 전환
- 히어로 위에서는 로고·메뉴 텍스트 흰색
- **서비스 안내** 메뉴: hover 시 드롭다운 (데스크톱), 탭 시 토글 (모바일)
  - 서비스 소개 → `service.html`
  - 서비스 미리보기 → `preview.html`

**히어로 섹션**
- 배경: `<video autoplay loop muted playsinline>` — `banner-movie2.mp4` 무한 재생
- 영상 위에 반투명 어두운 오버레이 + 그레인 텍스처

**본문 섹션 순서**
| ID | 내용 |
|----|------|
| `#how` | 작동 원리 4단계 (녹음 → 추억 입력 → 매일 전화 → 리포트) |
| `#features` | 주요 기능 3가지 카드 |
| `#trust` | 안심·보안 3가지 카드 |
| `#pricing` | 요금제 (월간 / 연간) |
| `#faq` | 자주 묻는 질문 아코디언 |
| `#signup` | 신청 폼 |

**요금제 (`#pricing`) 상세**
- 월간 19,900원 / 연간 14,900원(월 환산)
- 두 플랜 **기능 동일** — 결제 주기만 다름
- 포함 기능 8가지: AI 안부 전화, 자동 발신, 대화 퀴즈, 인지 건강 리포트, 추세 분석, 위험 알림, 리포트 공유, 우선 고객 지원
- 연간 카드에 "월간보다 25% 저렴" 뱃지 + 강조 테두리

---

### service.html — 서비스 소개

동일한 헤더·푸터 적용. 섹션 구성:

1. **리멤버콜이란** — 서비스 개요 + 3개 특징 뱃지
2. **이런 고민에서 시작했어요** — pain point 2개 카드
3. **어떻게 다른가요** — 차별점 3개 카드 (앱 불필요 / 자녀 목소리 / 데이터 케어)
4. **검증된 효과** — 수치 카드 3개 + 출처 표기
   - 160곳+ 지자체 운영, 우울 증상 44% 감소, 기억력 점수 상승
   - 출처: 황성주 외(2026); 정재훈(2022)
5. **작동 방식** — 3단계 (녹음 → 매일 전화 → 리포트)
6. **CTA** — 무료로 시작하기 + 서비스 미리보기 버튼

---

### preview.html — 서비스 미리보기

ElevenLabs API를 활용한 3단계 음성 체험 위저드.

**Step 1 — 목소리 녹음**
- 브라우저 마이크로 실시간 녹음 (MediaRecorder API)
- 녹음 중 실시간 파형 시각화 (Web Audio API + Canvas)
- 녹음 완료 후 재생 확인 가능

**Step 2 — AI 학습**
- ElevenLabs Instant Voice Cloning API (`POST /v1/voices/add`) 호출
- 성공 시: 복제된 voice_id 저장 → Step 3에서 실제 목소리 사용
- 실패 시: 에러 메시지 표시 후 기본 AI 음성으로 폴백
- 학습 진행 단계 애니메이션 (5단계 순차 표시)

**Step 3 — 전화 미리보기**
- 스마트폰 UI 모형 (발신 → 수신 → 통화 중 → 종료 상태)
- 메시지 입력 → ElevenLabs TTS API (`POST /v1/text-to-speech/:voice_id`) 로 음성 생성
- 통화 중 파형 애니메이션 + 통화 시간 카운터

> **주의**: API 키가 클라이언트 코드에 포함되어 있습니다.  
> 실제 서비스 배포 시에는 반드시 백엔드 프록시를 통해 API를 호출해야 합니다.

---

## 디자인 시스템

### 컬러 팔레트

| 변수 | 값 | 용도 |
|------|----|------|
| `--cream` | `#F6EFE2` | 기본 배경 |
| `--cream-2` | `#F0E7D6` | 보조 배경, 카드 내부 |
| `--sand` | `#DBCAAA` | 푸터 배경 |
| `--caramel` | `#C2A275` | 보조 강조 |
| `--caramel-deep` | `#9E7C4D` | 주요 강조, CTA 버튼 |
| `--brown` | `#4A3D2E` | 기본 텍스트 |
| `--brown-2` | `#6B5C49` | 보조 텍스트 |
| `--brown-3` | `#9E8B72` | 설명 텍스트, 레이블 |

### 폰트
- 영문 세리프: `Cormorant Garamond` — 브랜드명, 섹션 eyebrow
- 한글 세리프: `Noto Serif KR` — h1, h2 제목
- 기본 산세리프: `Pretendard Variable` — 본문 전반

### 반응형
- 기준점: `860px` (모바일/데스크톱 분기)
- 모바일: 햄버거 메뉴, 그리드 1열 전환

---

## 로컬 실행 방법

```
VS Code에서 website/index.html 우클릭
→ Open with Live Server
→ http://127.0.0.1:5500/website/index.html
```

> `file://` 직접 열기는 Chrome의 미디어 자동재생 보안 정책으로  
> 배경 영상이 재생되지 않을 수 있습니다. Live Server 사용을 권장합니다.

---

## 향후 개선 사항

- [ ] ElevenLabs API 키를 백엔드 프록시로 이전
- [ ] 신청 폼 실제 백엔드 연동 (현재 클라이언트 측 처리만)
- [ ] 모바일 성능을 위한 영상 압축 및 WebM 포맷 추가
- [ ] Open Graph 메타태그 추가 (SNS 공유 미리보기)
- [ ] Google Analytics 또는 유사 트래킹 연동

---

*© 2026 RememberCall · 기억을 지키는 가장 다정한 연결*
