# Halo Kids!

아이가 읽을 책과 볼 영상을 표지를 보며 메뉴판처럼 고르고, 계정별로 읽은 책·본 영상을 기록하는 가족용 웹앱.

**서비스**: https://withcolours.github.io/halo-kids/ · 아이패드 Safari에서 "홈 화면에 추가"하면 전체화면 앱으로 동작 (PWA)

## 페이지 구성

| 페이지 | 역할 |
|---|---|
| `login.html` | 로그인/가입 (계정: 영문·숫자·`._-` 3자+, 비번 5자+, Enter 제출) |
| `index.html` | 대문 — 책/영상/프로필/설정 카드, 테마 토글, 로그아웃. admin 역할이면 '관리' 카드 표시 |
| `category.html?type=books\|ott` | 카테고리별 하위 메뉴 선택 |
| `at-series.html` | 앗! 시리즈 150권 메뉴판 — 검색·분야 필터 + "읽었어요" 기록 + 읽음 필터 |
| `ott.html` | OTT 영어 애니 59선 — "봤어요" 기록 + 필터 |
| `ott-list.html` | OTT 전체 목록 107편 (부모용, 나이/AR 필터) |
| `profile.html` | 닉네임·생년 설정 |
| `settings.html` | 테마, 로그아웃 |
| `admin.html` | 관리자 전용 — 사용자 목록(읽음 수), 최근 활동 30건 |

## 백엔드 (Firebase `halo-kids`)

- **Auth**: 이메일/비밀번호 (`{계정명}@halo-kids.local` 형식)
- **Realtime DB**:
  - `users/{uid}` — username, nickname, birthYear, role(`child`/`admin`), createdAt
  - `userReadings/{uid}/{itemId}` — 읽음/봤음 기록 (`at-series-{n}`, `ott59-{n}`)
  - `userActivity/{uid}` — login/logout/signup/read_book/watch_ott/update_profile 로그
- **관리자 지정**: Firebase 콘솔 → Realtime Database → `users/{uid}/role`을 `admin`으로 수정

## 디자인

- 대표색 도저 블루 `#1E90FF`, 라이트 배경 `#F2F8FD`, 다크 `#16202B`
- 폰트: Jua(제목) + Noto Sans KR(본문), 아이콘: Feather Icons
- 라이트/다크 토글 — `data-theme` 속성 + localStorage, 모든 페이지 `<body>` 첫 스크립트에서 조기 복원

## 개발 메모

- 모든 CSS/JS/이미지(base64)는 각 HTML에 인라인 — 빌드 과정 없음
- Firebase SDK는 반드시 `-compat` 빌드 사용 (일반 빌드는 ES module이라 `<script src>` 로드 불가)
- 배포: `main` 브랜치 push → GitHub Pages 자동 재배포 (1~2분 + CDN 캐시 수 분)

## TODO

- [ ] **DB 보안 규칙** — 테스트 모드 만료(생성 후 30일) 전에 본인 데이터만 r/w 규칙 적용 (최우선)
- [ ] ott-list.html에 "봤어요" 기록 열 추가
- [ ] 59선(`ott59-n`)과 107편 목록 id 통합 (제목 기준)
- [ ] 앗시리즈 70↔150 매칭표에 표지 썸네일
- [ ] 새 전집 추가
- [ ] Phase 2: 전집 데이터 JSON 분리 + 공용 뷰어, 나이/AR/이력 기반 추천, 프로필 통계
