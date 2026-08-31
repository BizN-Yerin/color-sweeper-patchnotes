# Color Sweeper — 패치노트

컬러스위퍼(Color Sweeper) 공식 패치노트 사이트입니다.

**🔗 https://bizn-yerin.github.io/color-sweeper-patchnotes/**

차수별 업데이트 내역을 누적해서 볼 수 있고, 12개 언어를 지원합니다.

---

## 저장소 구조

| 경로 | 내용 |
|---|---|
| `index.html` | 사이트 본체 (단일 파일, 외부 의존성 없음) |
| `data/index.json` | 차수 목록 · 언어 설정 |
| `data/v/*.json` | 차수별 본문 |
| `images/<차수>/` | 차수별 이미지 |

`data/` 와 `images/` 는 **내부 운영 도구가 자동으로 생성·갱신**합니다.
직접 수정하면 다음 배포 때 덮어써집니다.

---

## 지원 언어

한국어 · English · 日本語 · 简体中文 · 繁體中文 · Deutsch · Français · Español · Русский · ไทย · Tiếng Việt · Bahasa Indonesia

번역이 준비되지 않은 언어로 접속하면 영어 본문과 함께 해당 언어로 된 안내가 표시됩니다.

---

## 기술 메모

- 정적 사이트 (GitHub Pages). 빌드 과정 없음
- 언어는 `?lang=` 파라미터 또는 브라우저 설정으로 결정되며, 선택값은 브라우저에 기억됩니다
- 라이트/다크 테마 자동 + 수동 전환
- 차수 딥링크: `?v=1-24-0&lang=ja`

문의는 저장소 소유자에게 부탁드립니다.
