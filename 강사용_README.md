# 환경공단 바이브코딩 교육 — 실습 템플릿 (강사·프론티어용)

교육 중 수강생이 막혔을 때 "이 파일로 이어서 하세요"라고 건네는 **단계별 완성본**입니다.
조별 GitHub 템플릿 repo 또는 공유 링크로 배포하세요. 수강생에게 미리 배포하지 않습니다(직접 만들게 하는 것이 목적).

## 파일 구성

| 파일 | 용도 | 커리큘럼 매핑 |
|---|---|---|
| 수질측정_샘플데이터.xlsx | 공통 예제 더미 데이터 (30행, W01~W03) | Day 1 전체 |
| step1_화면설계.html | Claude Design 결과물 예시 (정적 목업) | 14:30~15:30 |
| step2_기능구현.html | 엑셀 파싱 + 기준값 판정 + AI 해석 + 차트 | 15:30~16:20 + 1차 배포 |
| step3_supabase연동.html | step2 + Supabase 저장/조회 | 16:50~17:40 + 재배포 |

## 더미 데이터 설계 (이두원 대표 강의자료 기준값 준거)

- **W01**: 정상 관정. 단, 2026-01-10 행에 DO=25.0 **이상치** 주입 (유효 범위 초과 → "이상치" 판정 확인용)
- **W02**: 마지막 3개월(2026-04~06) **유류 오염 패턴** — TPH 0→320→850→1,200 µg/L 상승, DO 4.x→0.9 급감, ORP +→-65 음전환, EC 352→780 상승. 강의자료의 "오염 패턴 해석 가이드"(DO↓→ORP↓→EC↑→TPH↑) 그대로 재현
- **W03**: 정상. 2026-03-10 EC=2,150 1회 (경계 초과 → "주의" 판정 확인용)

## 판정 기준 (코드 내 judge 함수)

- TPH > 500 µg/L → **오염 의심** (빨강)
- DO > 15 → **이상치** / DO < 2 또는 ORP < 0 또는 EC ≥ 2,000 → **주의** (노랑)
- 그 외 → **정상** (초록)

기대 결과: 30건 = 정상 25 / 주의 2 / 이상치 1 / 오염 의심 2
(W02의 TPH 320 행은 기준 미만이지만 DO 1.8로 "주의" 판정 — "TPH가 기준을 넘기 전에 DO·ORP가 먼저 신호를 준다"는 스토리텔링 포인트로 활용)

## AI 해석 동작

- API 키 입력 시: Claude API(claude-sonnet-4-6) 호출, 관정별 60자 요약 생성
- 키 없음/오류 시: **규칙 모드**로 자동 전환 (수업 중단 방지용 폴백)
- 조별 키 4개 발급·배포 권장 (429 rate limit 분산)

## Step 3 사전 준비 — Supabase 테이블

Table Editor에서 `water_quality` 테이블 생성 (RLS 비활성화, 교육용):

| 컬럼 | 타입 |
|---|---|
| well_id | text |
| measure_date | date |
| ec_uscm, do_mgl, orp_mv, ph, tph_ugl | numeric |
| judgment, ai_summary | text |

※ 실무 적용 시 RLS·인증 설정 필요 — Day 2 마무리에서 안내.

## 리허설 체크리스트 (8월 마지막 주)

1. Claude Design에서 step1 수준 목업이 생성되는지 + HTML 내보내기 → 프로젝트 대화에서 기능 추가가 매끄러운지 (막히면 플랜 B: 시안 참고 방식)
2. step2를 강사 API 키로 실행 → AI 해석 정상 생성 확인
3. step3를 실제 Supabase 프로젝트로 저장/조회 → 새로고침 유지 확인
4. GitHub 웹 업로드 → Vercel 배포 → 배포 URL에서 전 기능 재확인 (특히 API 호출)
5. 산림비전센터 네트워크에서 최종 점검
6. 스크린샷 10컷 촬영 후 교안 교체: ① Claude Code 탭 화면 ② Design 캔버스 목업 ③ Step 2 대시보드 ④ Supabase New Project 입력 화면 ⑤ Table Editor 컬럼 추가 화면 ⑥ Settings→API 화면(URL·anon key 위치) ⑦ Vercel 배포 완료 화면 ⑧ Table Editor에 쌓인 행 ⑨ 최종 완성 앱 ⑩ Anthropic 콘솔 API Keys 화면(Create Key 버튼)
7. 실습 키트 배포 URL 확정 후 키트 슬라이드(10번)에 단축 URL·QR 기입
8. Claude Code 워크플로우 리허설: 폴더 열기 → PRD.md·design.html 배치 → index.html 생성 → GitHub 폴더 업로드까지 전 구간 1회 완주
9. 배포 플랜 B 리허설: Code에서 "이 폴더를 Vercel로 배포해줘" 실행, 교육장 네트워크에서 최초 로그인(브라우저 인증) 흐름 확인

## 키트 배포 사이트 (AX-day_키트사이트.zip)
1. 압축 해제 → kit-site 폴더 (index.html + files/ 3종)
2. 교안 확정 시 files/AX-day_교안.pdf를 최종본으로 교체 (스크린샷 반영 후)
3. GitHub 새 저장소에 kit-site 내용물 업로드 → Vercel Import → 배포
4. 생성된 URL을 단축 URL로 만들고 QR 생성 → 교안 10번 키트 슬라이드에 기입
5. 사이트의 프롬프트 6종은 교안 문구와 동일하게 유지할 것 (교안 수정 시 함께 수정)
