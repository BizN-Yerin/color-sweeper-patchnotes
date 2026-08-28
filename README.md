# 컬러스위퍼 패치노트 — 구축 가이드 (관리자용)

> 최초 1회 세팅. 반나절. 이후 팀원은 시트만 쓰면 됩니다.

---

## 왜 파일이 3개인가

한 곳에 다 넣으면 **시트를 공유하는 순간 GitHub 토큰도 같이 공유**됩니다. 시트 Editor는 확장 프로그램 → Apps Script를 열어 스크립트 안의 모든 값을 볼 수 있기 때문에, 스크립트에 비밀을 숨기는 것도 소용없습니다.

그래서 비밀과 권한을 팀원이 닿을 수 없는 곳으로 분리했습니다.

| 파일 | 공유 대상 | 들어있는 것 |
|---|---|---|
| **① 패치노트 마스터 시트** | 팀원 Editor | 원문·번역·검증 메뉴. **비밀정보 없음** |
| **② 배포 콘솔 시트** | 🔒 나만 | GitHub 토큰, 배포 권한자 목록, 배포 로그 |
| **③ Drive 이미지 폴더** | 팀원 Editor | 차수별 이미지 |
| ④ GitHub 저장소 | 나만 (팀원은 존재도 몰라도 됨) | 사이트 |

팀원이 배포 버튼을 누르면 → 마스터 시트가 ②의 웹앱을 호출 → ②가 **누가 눌렀는지 구글 계정으로 확인** → 허가된 사람이면 대신 GitHub에 커밋. 토큰은 ② 밖으로 절대 나가지 않습니다.

---

## 1. GitHub 저장소

1. `color-sweeper-patchnotes` 저장소 생성 (Public — Pages 무료 조건)
2. 받으신 `site/` 폴더 내용을 저장소 **루트**에 push
3. Settings → Pages → Source `main` / `/ (root)` → Save
4. 5분 뒤 `https://<계정>.github.io/color-sweeper-patchnotes/` 접속 확인

### 도메인 연결

| 방식 | DNS 설정 |
|---|---|
| 서브도메인 (권장) | `CNAME  patchnote → <계정>.github.io` |
| 루트 도메인 | `A` 4개: `185.199.108.153` / `.109.153` / `.110.153` / `.111.153` |

저장소 루트에 `CNAME` 파일 생성(내용은 도메인 한 줄) → Settings → Pages → Custom domain 입력 → **Enforce HTTPS 체크**.

> 리다이렉트가 아니라 CNAME 연결을 쓰세요. 리다이렉트는 주소창에 github.io가 남고, 인게임 웹뷰의 도메인 화이트리스트에 걸립니다.

### 토큰 발급

GitHub → Settings → Developer settings → **Fine-grained personal access token**

- Repository access: `color-sweeper-patchnotes` **만**
- Permissions → **Contents: Read and write** (이것 하나면 충분)
- 만료 **1년** → 캘린더에 갱신 알림 등록

---

## 2. Drive 이미지 폴더

내 드라이브에 `컬러스위퍼_패치노트_이미지` 폴더 생성 → 팀원에게 **편집자** 공유.

그 안에 차수 이름으로 하위 폴더를 만듭니다. 폴더명은 **Versions 탭의 version 값과 정확히 같아야** 합니다.

```
컬러스위퍼_패치노트_이미지/
├── 1.24.0/
│   ├── hero.png              → 대표 이미지 (전 언어 공용)
│   ├── hero@ja.png           → 일본어일 때만 이걸로 교체
│   ├── book.png
│   └── booster.png
└── 1.23.0/
```

**파일명 규칙: `이미지키[@언어코드].확장자`**

- `@` 없으면 전 언어 공용
- `@ja`, `@zh-Hans` 처럼 붙이면 그 언어에서만 교체
- 파일명 앞부분(= 이미지키)을 시트 `image_key` 칸에 적습니다
- png / jpg / gif / webp, **개당 2MB 이하**

폴더 URL에서 `folders/` 뒤 문자열이 **폴더 ID**입니다. 복사해 두세요.

---

## 3. 배포 콘솔 시트 (🔒 절대 공유 금지)

새 스프레드시트 이름: `[비공개] 패치노트 배포 콘솔`

### Config 탭

| key | value |
|---|---|
| github_owner | 깃허브 계정/조직명 |
| github_repo | color-sweeper-patchnotes |
| github_branch | main |
| github_token | github_pat_... |
| drive_root_id | 2번에서 복사한 폴더 ID |

### Access 탭 ← **여기가 권한 관리의 전부입니다**

| email | role | name | memo |
|---|---|---|---|
| rosiekim@com2us.com | admin | 옌 | |
| teammate1@com2us.com | publisher | ○○○ | 라이브 운영 |
| teammate2@com2us.com | editor | △△△ | 원문 작성 |

| role | 할 수 있는 것 |
|---|---|
| `admin` / `publisher` | 원문 작성 · 번역 시트 생성 · 검증 · **배포** |
| `editor` | 원문 작성 · 번역 시트 생성 · 검증 (배포 불가) |
| 목록에 없음 | 아무것도 못 함 (마스터 시트는 볼 수 있어도 배포는 차단) |

**권한 주기 = 한 줄 추가 / 회수 = 한 줄 삭제.** 즉시 반영됩니다.

### Log 탭

헤더만 만들어두면 됩니다: `시각 | 이메일 | 액션 | 버전 | 결과`
누가 언제 무엇을 배포했는지, 권한 없는 사람이 시도한 것까지 자동 기록됩니다.

### 스크립트 설치

확장 프로그램 → Apps Script → `console/Deploy.gs` 내용 붙여넣기 → 저장

**웹앱 배포**: 배포 → 새 배포 → 유형 **웹 앱**

| 항목 | 값 |
|---|---|
| 실행 계정 | **나** |
| 액세스 권한 | **Com2uS 내 모든 사용자** ← "모든 사용자" 절대 금지 |

생성된 **웹 앱 URL을 복사**해 두세요.

설치 확인: 시트 새로고침 → 🔐 배포 콘솔 메뉴 → GitHub 연결 테스트 / Drive 폴더 점검

---

## 4. 패치노트 마스터 시트 (팀 공유용)

새 스프레드시트 이름: `컬러스위퍼 패치노트`

### Config 탭

| key | value |
|---|---|
| deploy_url | 3번에서 복사한 웹 앱 URL |
| drive_folder_url | 2번 Drive 폴더 링크 (팀원이 이미지 넣을 곳) |

### Versions 탭

| version | date | tags | hero_image_key | title_ko | summary_ko | period_start | period_end |
|---|---|---|---|---|---|---|---|
| 1.24.0 | 2026-08-27 | event,content,fix | hero | 해변 스티커 컬렉션 | 여름이 가기 전… | 2026-08-27 04:00 | 2026-09-10 03:59 |

`tags`: `event` / `content` / `balance` / `fix` / `system` 중 콤마 구분

### Content 탭 — 한 행 = 한 블록

| version | order | section_key | section_title_ko | tag | type | key | ko | image_key | max_len | note |
|---|---|---|---|---|---|---|---|---|---|---|
| 1.24.0 | 10 | evt | 해변 스티커 컬렉션 | event | text | evt.intro | 시원한 파도와… | | 250 | |
| 1.24.0 | 20 | evt | | event | image | evt.cap1 | 스티커북은 메인 화면… | book | 60 | |
| 1.24.0 | 30 | new | 신규 콘텐츠 | content | list | new.items.1 | 스테이지 2,401~2,460… | | 120 | |
| 1.24.0 | 40 | new | | content | list | new.items.2 | 파도타기 부스터 등장 | | 120 | |

- `type`: `text` / `list` / `image` / `notice`
- 연속된 `list` 행은 자동으로 하나의 목록으로 묶입니다
- `image` 행에서 `key`·`ko`를 비우면 캡션 없는 이미지가 됩니다
- `key`는 한번 정하면 바꾸지 마세요 (번역 보존의 기준입니다)

### 스크립트 설치

확장 프로그램 → Apps Script → `master/Code.gs` 붙여넣기 → 저장 → 시트 새로고침
상단에 **📋 패치노트** 메뉴가 뜨면 성공.

### 시트 공유

팀원에게 **편집자**로 공유. 비밀정보가 없으므로 안전합니다.

**보호 권장**: `Config` 탭 → 우클릭 → 시트 보호 → 나만 편집. 팀원이 `deploy_url`을 실수로 지우는 사고를 막습니다.

---

## 5. 설치 확인

1. 마스터 시트에서 📋 패치노트 → **내 권한 확인** → `admin` 으로 나오면 정상
2. 팀원에게도 눌러보게 하세요. Access 탭에 넣은 role 그대로 나와야 합니다
3. 📋 패치노트 → **3. 이미지 확인** → Drive 폴더의 이미지 목록이 나오면 연결 정상

---

## 6. 잘못 배포했을 때 (롤백)

사이트 데이터가 전부 git 히스토리에 남습니다. GitHub 저장소 → Commits → 되돌릴 커밋 → **Revert** → 1~2분 뒤 사이트 원복.

이건 팀원이 못 하고 관리자만 할 수 있습니다. 사실상 이 구조의 안전망이에요 — 팀원이 뭘 잘못 올려도 한 번의 클릭으로 되돌아갑니다.

---

## 7. 남아있는 한계 (솔직하게)

| 한계 | 실제 위험도 | 완화책 |
|---|---|---|
| 마스터 시트 Editor는 Content·Versions를 망가뜨릴 수 있음 | 중 | 배포 전 검증 리포트에서 드러남 + git revert로 원복. 완전 차단은 불가 |
| 배포 권한자는 검증 실패해도 배포 강행 가능 | 낮 | 경고는 뜨지만 막지는 않음. 팀 규칙으로 관리 |
| GitHub 토큰 만료 시 조용히 실패 | 중 | 만료 시 "GitHub 인증 실패 (401)" 메시지가 명확히 뜨도록 처리됨. 캘린더 알림 필수 |
| Drive 이미지 2MB 초과 시 제외 | 낮 | 배포 결과에 제외된 파일명이 표시됨 |
| 사내 계정 전용 | — | 외부 번역사에게는 T_ 시트만 별도 파일로 분리해 전달하세요 |

**가장 현실적인 사고**: 번역 발주 후 한국어 원문을 수정 → 번역 시트 재생성 → 번역가가 작업 중이던 행이 밀림.
번역은 `key` 기준으로 보존되지만 순서는 바뀝니다. **"번역 발주 후 원문 수정 금지, 급하면 새 key 추가"** 를 팀 규칙으로 정하세요.
