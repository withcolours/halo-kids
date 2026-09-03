# Halo Kids — 프로젝트 가이드

아이(영우, 계정 thdduddn) 학습 콘텐츠 웹앱. 책/영상 메뉴판 + 읽음 추적, 주간 플래너로 확장 중.
**현재 상태와 미결 작업은 [docs/STATUS.md](docs/STATUS.md), 플래너 확정 스펙은 [docs/planner-spec.md](docs/planner-spec.md)를 먼저 읽을 것.**

## 배포·인프라
- 서비스: https://withcolours.github.io/halo-kids/ (GitHub Pages, 공개)
- 리포: github.com/withcolours/halo-kids · 로컬 `~/Library/Mobile Documents/com~apple~CloudDocs/dev/halo-kids/`
- push → 1~2분 내 자동 재배포. CDN 캐시 몇 분 지연 가능 (배포 확인: `curl`로 변경 문자열 grep)
- **push는 사용자 확인 후** (공개 사이트이므로)
- 백엔드: Firebase 프로젝트 `halo-kids` — Realtime DB + Auth(이메일/비번, `{계정명}@halo-kids.local` 형식)
- DB 보안 규칙: 본인만 r/w, admin 전체 열람, role 셀프승격 차단. 원본은 `database.rules.json`, 변경 시 Firebase 콘솔에 재게시 필요
- 카카오 책검색 API 키: `.kakao_key` (gitignored). **응답·코드에 키 값 노출 금지**

## 디자인 시스템
- 브랜드: **"Halo Kids!"** (이름 확정 — halo(후광)+hello(인사) 이중 의미. 변형 철자 검토 후 Halo 유지 결정)
- 색: 도저 블루 `#1E90FF`(--acc), 라이트 배경 `#F2F8FD`, 다크 `#16202B`(네이비 틴트), 노란 포인트 `#F4C531`
- 폰트: Jua(제목) + Noto Sans KR(본문), Google Fonts
- 아이콘: Feather Icons(CDN) 또는 인라인 SVG. **이모지 남용 금지** (사용자 피드백: "올드하고 AI스럽다")
- **후광 고리(링) 모티프 금지** — 고인 연상(사용자 피드백). "빛" 표현은 스파클(✦)·빛살로
- 테마: `data-theme` 3상태. 라이트는 `data-theme="light"` 명시 설정 필수(attr 제거만 하면 OS 다크에서 되돌아감). 모든 페이지 `<body>` 첫 줄에 조기 복원 스크립트
- 아바타 이니셜: `localStorage.nickname` 캐시로 첫 페인트부터 그림 (로그인 시 DB에서 미리 캐시, 로그아웃·재로그인 시 캐시 정리) — 깜빡임 금지

## 기술 규약·함정
- 정적 HTML만. 모든 CSS/JS/이미지(base64)는 각 HTML에 인라인 (외부 파일 없음)
- **Firebase SDK는 -compat 빌드 필수** (`firebase-app-compat.js` 등) — non-compat은 `<script src>` 로드 시 "Unexpected token 'export'" 오류
- DB 접근은 반드시 `onAuthStateChanged` 이후 (인증 복원 전 읽으면 규칙에 막힘)
- 대용량 페이지(at-series 2.9MB 등)는 Read 불가 — python/grep으로 다룰 것. base64 안 우연 매치 주의
- re.sub 치환문자열에 r'\1' 쓰면 리터럴 사고남 (겪음)
- file://로 열면 CDN 차단으로 아이콘·폰트 안 보임 — 로컬 확인은 `python3 -m http.server`로
- 카드가 분야별 탭에 cloneNode 복제됨 → 읽음 토글은 같은 data-book-id 전체 동기화. 필터 함수는 `window.__apply`로 노출

## 페이지 구성
login / index(대문: 책·영상 카드 + 아바타 드롭다운) / category / at-series(앗시리즈 150권) / discussking(토론왕 93) / math-basic·math-adv·science-basic·social-basic(뒤집기류) / videos(통합 181편) / ott·ott-list(구버전) / profile / settings(테마·비번변경·로그아웃 + 인쇄용 플래너 바로가기 카드) / admin(role=admin 전용) / **planner-print**(주간 플래너 체크뷰 — Firebase 계정별 저장: `userPlanWeeks`/`userPlanChecks`/`userPlanLibrary`, 주별 스냅샷, 시트 위 라이브 체크(페인트 스트로크), 주 이동+빈 주 3택, localStorage는 오프라인 캐시·레거시 `halokids_weekly_grid_v1`은 최초 로그인 시 1회 이관) / planner(구모델 초안 — 그리드 모델로 재작업 예정)

## DB 구조
- `users/{uid}`: username, nickname, birthYear, role(child|admin), createdAt, lastLogin
- `userReadings/{uid}/{itemId}`: timestamp (`at-series-{no}`, `ott59-{no}` 등)
- `userActivity/{uid}/{push}`: action, detail, timestamp
- 플래너 체크뷰(라이브, 규칙 게시됨): `userPlanWeeks/{uid}/{주시작일}`(주별 스냅샷), `userPlanChecks/{uid}/{날짜}/{taskId}`, `userPlanLibrary/{uid}`
- 플래너 Phase B 확장 예정: userTasks, userBooks, userStars, rewards, redemptions, usernames, parents ([docs/planner-spec.md](docs/planner-spec.md) — 확장 시 보안 규칙 재게시)

## 계정
- `thdduddn` — **실사용 아이 계정** (영우, 2017년생) ← 2026-09-01 사용자 확정
- `elyse` (닉네임 nari) — **관리자(부모) 계정**
- `test.claude` — 검증용, 삭제 가능
- 관리자 지정: 콘솔에서 uid의 role을 "admin"으로 수동 변경
- 원천 자료: `~/Library/Mobile Documents/com~apple~CloudDocs/dev/LifeLog/교육/참고 자료/` (시간표 원본, OTT/도서 자료)

## 작업 원칙 (사용자 지시)
- **설계 먼저, 목업으로 합의 후 구현** — "구현은 나중 문제"
- 큰 기능은 UI 계획 → 목업 → 승인 → 구현 순서를 지킬 것
