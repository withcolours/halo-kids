# 작업 현황 (2026-09-01 기준)

## ✅ 완료
### Phase 1 (2026-08-29, 배포됨)
- 로그인/가입, 대문(아바타 드롭다운), 카테고리, 읽음 추적(앗시리즈·OTT), 프로필(닉네임·생년), 설정(테마·비번변경), admin 대시보드
- DB 보안 규칙 게시(본인만 r/w, admin 열람, role 셀프승격 차단 — `database.rules.json`)
- 전집 5종 메뉴판(토론왕93·수학기본40·수학심화50·과학40·사회40[표지 플레이스홀더]) + videos.html 통합 181편
- 안드로이드 PWA(manifest + 아이콘), iOS 홈 화면 추가 지원

### 주간 플래너 설계 (2026-08-29, 구현 전)
- 설계서 아티팩트 승인 반복 완료: https://claude.ai/code/artifact/555d984a-186e-485c-9191-227a000adbc3
- 확정 스펙: [planner-spec.md](planner-spec.md). **"⑥ 결정 필요" 6건 사용자 답변 대기 → 답변 후 구현 착수**

### 2026-09-01 작업 (배포됨)
- **planner-print.html**: 인쇄용 주간 플래너(사용자 제공 도구 통합). A4 가로 지면을 PNG/PDF로 출력. localStorage 키 `halokids_weekly_grid_v1`. "자주 쓰는 일정" 라이브러리 추가(삭제해도 서랍에 보관, [이번 주에]로 재등록, 전체 초기화에도 유지). 홈스쿨 계획용 — **영우가 직접 입력하는 것이 목적이라 시드 없음**
- settings.html에 플래너 바로가기 카드(로그아웃 아래 분리, 웃는 달력 아이콘)
- 아바타 이니셜 깜빡임 수정: localStorage `nickname` 캐시로 첫 페인트부터 표시. 로그인 시 DB에서 미리 캐시, 로그아웃·타계정 로그인 시 정리 (index/settings/profile/login)

### 브랜딩 (2026-09-01, 확정·적용)
- **이름 "Halo Kids" 확정** — Haylo/Hailo 등 변형 검토 후 유지 (halo+hello 이중 의미가 원안에서만 성립)
- 로고 시안 3종: https://claude.ai/code/artifact/3cac98e3-bb0a-419d-8ff9-04e35fdf145d — **B 마스코트(심볼) + A 워드마크(로고타입) 조합으로 확정**
- **후광 고리 모티프 금지 확정** (고인 연상) — 빛 표현은 스파클·빛살로
- 적용·배포 완료(df0e8a9): icon-192/512·icon-home(마스코트, 배경 #F2F8FD), index/login 헤더에 마스코트 SVG+워드마크. icon-books/ott는 유지. 이 맥에 GitHub SSH 인증 구축됨(~/.ssh/id_ed25519, remote는 SSH로 전환)
- BI 확정안(토큰·조합 규칙·적용 현황)은 플래너 설계서 ⑦절에 문서화

## ⚠️ 미결
0. **planner-print 가로 넘침 수정** — `.landscape-grid` 열을 `minmax(0,1fr)`로 패치(로컬, **미커밋**). 원인: 1fr의 min-width:auto가 긴 nowrap 요약 텍스트에 밀려 열이 시트 밖으로 확장. **다른 세션에서 검증·커밋 예정**. 참고: planner-print 데이터는 localStorage라 계정별 분리·기기 간 동기화 안 됨(같은 브라우저면 thdduddn/elyse가 같은 데이터를 봄) — Firebase 저장으로 전환 검토
1. **플래너 구현 보류 중** — ⑥ 결정 6건 답변 대기 (스펙: planner-spec.md, 설계서 ⑦절에 BI 확정안 추가됨)
3. ott-list.html(107편 표) "봤어요" 열 미완 / ott59-N vs ott-N id 통합
4. 뒤집기 과학 심화 50권·국어 40권 수집 미해결(세트 전용 유통) → 이후 6분야 통합 메뉴(기존 읽음 id 보존)
5. 사회 뒤집기 표지 0/40 플레이스홀더, videos.html 분량 null 25편(동요 모음집)
6. 앗시리즈 70↔150 매칭표 썸네일, 테스트 계정 test.claude 정리
7. Phase 2 구상: 전집 JSON 분리+공용 뷰어, 추천, 프로필 통계

## 인프라 메모 (2026-09-01)
- 리포가 `~/dev/halo-kids` → `~/Library/Mobile Documents/com~apple~CloudDocs/dev/halo-kids`로 이동 (iCloud로 두 맥 동기화)
- 두 맥 운용 규칙: 한 번에 한 맥에서만, 자리 이동 전 동기화 완료 확인. "저장 공간 최적화"는 dev 폴더 "지금 다운로드 유지"로 대응(방출 감지: `find dev -name "*.icloud"`)
- LifeLog(기록·원본 자료)는 형제 리포 `CloudDocs/dev/LifeLog` — 교육 원천 자료는 그 안 `교육/참고 자료/`
