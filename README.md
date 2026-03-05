# recrime-market

개발 워크플로우 자동화를 위한 Claude Code 플러그인 마켓플레이스입니다.

## 플러그인 목록

| 플러그인 | 설명 |
|----------|------|
| [dev-review-loop](./plugins/dev-review-loop/) | 병렬 에이전트 분석 → 구현 → 코드리뷰 → Playwright 테스트 워크플로우 |
| [save-session](./plugins/save-session/) | 세션 작업 내용을 MD로 저장하여 다음 세션에서 이어서 작업 |
| [load-session](./plugins/load-session/) | 이전 세션에서 저장한 작업 내용을 자동으로 불러와 작업 재개 |

각 플러그인의 상세 설명은 해당 폴더의 README.md를 참고하세요.

---

## 설치 방법

### 1. 마켓플레이스 등록

```bash
/plugin marketplace add BaeJaebeom/marketplace
```

### 2. 플러그인 설치

```bash
# /plugin install {플러그인명}@recrime-market
/plugin install dev-review-loop@recrime-market
```

## 라이선스

MIT
