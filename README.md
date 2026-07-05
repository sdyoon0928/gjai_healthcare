# gjai_healthcare
# AI운동루틴코치 — 오늘의 회복 (Recovery Coach)

> 웨어러블 데이터와 라이프로그를 AI가 분석해, 오늘 당신 몸에 맞는 운동을 알려주는 개인 맞춤형 회복 코칭 서비스

⚕️ 본 서비스는 건강관리 참고용 데모이며 의료 진단·처방을 제공하지 않습니다.

---

## 팀 소개

**팀명: CoreShift** — 몸과 습관을 변화시키는 팀


| 이름 | 역할 | 담당 |
|------|------|------|
| (권보람) | PM | PRD 총괄, 우선순위(MoSCoW) 관리 |
| (신동윤) | UX Designer | 일정 관리, 페르소나·저니맵, 화면 흐름도 설계 |
| (김건우) | Backend Dev | DB 스키마(ERD), ,CREATETABLE, SQLite DB 구축,Recovery Score 엔진 |
| (이정우) | AI/Frontend Dev | Gradio 프로토타입, AI 코칭 로직 |

## 프로젝트 한 줄 설명

웨어러블(갤럭시 워치 등) 생체 데이터와 사용자가 직접 입력하는 라이프로그(통증·피로·스트레스)를 결합해
Recovery Score(회복 점수, 0~100)와 6단계 회복 등급을 산출하고, 등급에 맞는 운동 강도·루틴을 매일 추천하는
4일 미니 프로젝트(4인 1조) 헬스케어 서비스입니다.

## 사용 기술 스택

- **언어/런타임**: Python 3
- **UI 프레임워크**: Gradio (`gr.Blocks`, Soft 테마) — Google Colab에서 `share=True`로 배포
- **데이터베이스**: SQLite (`recovery_coach.sqlite`), 9개 테이블 · 이중 FK 구조
- **데이터 처리**: pandas, numpy
- **시각화**: Plotly (`plotly.express`, `plotly.graph_objects`) — 대시보드·캘린더·주간/월간 리포트
- **인증(데모)**: 이메일/비밀번호 + 모의(Mock) 소셜 로그인(카카오·네이버·구글·PASS), SHA-256 해시
- **AI 코칭**: LLM 기반 코칭 메시지 생성(프롬프트 가드레일 적용, 의료 판단 금지)

## 핵심 기능 3가지

1. **Recovery Score 산출 (FR-020)** — 최근 30일 개인 baseline 대비 HRV(40%)·안정시심박수(20%)·수면(25%)·
   호흡수(10%)·라이프로그(5%)를 z-score로 환산·가중합산해 0~100점과 최상/좋음/양호/주의/저조/위험 6단계
   등급, 운동 가능 여부를 즉시 제공합니다.
2. **웨어러블 연동 + 라이프로그 통합 입력** — Samsung Health Connect 기반 자동 생체 데이터 수집과, 워치가
   알 수 없는 통증·피로·스트레스·생리주기를 사용자가 직접 입력하는 라이프로그를 결합해 개인화 정확도를 높였습니다.
3. **시각화 리포트 & 관리자 모니터링** — 실시간 대시보드, 활동 캘린더, 주간/월간 추이 그래프를 제공하며,
   관리자 계정으로 로그인 시 전체 회원 DB 현황과 페르소나별 데모 인물 전환 기능을 확인할 수 있습니다.

## Colab 실행 방법

1. `09_dashboard.ipynb`(recovery_coach_app_v16)와 데이터 시드 파일 `recovery_coach_db.sql`을 같은
   Google Drive 폴더 또는 Colab 세션에 함께 업로드합니다.
2. Colab에서 노트북을 열고 **런타임 → 모두 실행**을 클릭합니다. (필요 패키지: `gradio`, `pandas`, `numpy`,
   `plotly` — 최초 실행 시 자동 설치됩니다.)
3. 노트북이 `recovery_coach.sqlite`를 생성하고 `recovery_coach_db.sql`의 3개 페르소나(45일치) 데이터를
   자동 적재합니다.
4. 마지막 셀(`demo.launch()`) 실행 후 출력되는 `public URL`(예: `https://xxxxx.gradio.live`)을 클릭해
   앱에 접속합니다.
5. 관리자 데모 계정으로 로그인하면 "DB 현황"·"관리자 데모" 탭이 나타납니다.
   - ID: `admin@recoverycoach.demo`
   - PW: `Admin1234!`
6. Colab 런타임은 서버(원격) 디스크에 DB를 저장하므로, 로컬로 보관하려면 "오늘의 분석" 또는 "DB 현황" 탭의
   **📥 최신 recovery_coach.sqlite 다운로드** 버튼을 눌러 내려받습니다.

## Demo 스크린샷

> ⚠️ 실행 화면 스크린샷은 별도로 촬영해 첨부해야 합니다(3장 이상). 아래는 촬영해서 채워 넣을 항목 가이드입니다.

1. `[스크린샷 1]` — 홈 대시보드: Recovery Score 게이지 + 코칭 메시지 카드
2. `[스크린샷 2]` — "오늘의 분석" 입력 화면과 회복도 분석 결과(점수 카드·상세표)
3. `[스크린샷 3]` — "시각화 리포트" 탭의 활동 캘린더 및 주간/월간 요약 그래프
4. (선택) `[스크린샷 4]` — 관리자 "DB 현황"/"관리자 데모" 탭

## 어려웠던 점 & 해결법

| 어려웠던 점 | 해결 방법 |
|---|---|
| 신규 가입자는 baseline(최근 30일) 데이터가 없어 Recovery Score 정확도가 낮은 콜드스타트 문제 발생 | 데이터가 30일 미만이면 첫 7일 평균으로 잠정 대체하고, 데이터가 아예 없으면 기획서의 인구 기본값(baseline)을 사용하도록 단계적 폴백 로직을 구현. 화면에는 "정확도 낮음(잠정)" 라벨을 함께 노출 |
| DB 스키마(`데이터적재물.xlsx`)에 호흡수(respiratory_rate) 컬럼이 없어 개인화된 baseline을 만들 수 없음 | 호흡수만 항상 기획서 §3의 인구 기본값(15회/분 ±1.5)을 사용하도록 예외 처리하고, 원본 스키마를 임의로 변경하지 않는 원칙을 지킴 |
| 4일(16시간)이라는 짧은 일정 안에 회원가입~AI코칭~리포트~관리자까지 모든 기능을 구현하기 어려움 | MoSCoW·RICE Score로 우선순위를 정량화해 Must(로그인, 프로필, 웨어러블 연동, 라이프로그, Recovery Score, AI 코칭, 대시보드) 기능에 집중하고, Should/Could 기능(맞춤 운동 고도화, 알림 설정 등)은 "향후 계획"으로 분리 |
| Colab 원격 런타임에서는 SQLite DB가 로컬 컴퓨터로 자동으로 오지 않아 결과물을 잃어버릴 위험 | "오늘의 분석"·"DB 현황" 탭에 `recovery_coach.sqlite` 다운로드 버튼을 추가해, 분석 직후 바로 로컬로 백업할 수 있도록 함 |
| AI 코칭이 의료 진단으로 오인될 위험 | 모든 코칭 메시지 및 화면 상단에 "건강관리 참고용 데모이며 의료 진단·처방을 제공하지 않습니다" 고지를 강제 노출하고, 위험도 '높음' 지속 시 병원 상담 권고 문구를 고정 출력 |

## 최종 제출물 구성 (team_N_healthcare/)

```
team_N_healthcare/
├── 01_painpoint.md
├── 02_journey_map.png
├── 03_persona.md
├── 04_PRD.md
├── 05_process_flow.png
├── 06_screen_flow.png          ← ai_workout_coach_screen_flow.pdf(png)
├── 07_ERD.png
├── 08_schema.sql
├── 09_dashboard.ipynb          ← 메인! (recovery_coach_app_v16.ipynb)
├── 10_presentation.pdf         ← 발표 슬라이드 8장 PDF 변환본
├── pillmate.db                 ← recovery_coach.sqlite
└── README.md                   ← 본 파일
```

---

<p align="center">AICA 인공지능사업융합단 · 인공지능사관학교</p>
