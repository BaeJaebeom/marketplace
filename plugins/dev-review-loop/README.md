# dev-review-loop

병렬 개발 에이전트 3개로 분석/설계 후 구현하고, 코드리뷰→수정→재리뷰를 반복하는 워크플로우입니다.

## 사용법

```
/dev-review-loop:dev-review-loop [작업 내용]
```

## 워크플로우

```
Phase 1: 병렬 분석 (3개 에이전트 동시 실행)
  ├── Code Explorer    → 기존 코드 실행 경로 추적, 패턴 파악
  ├── Code Architect   → 구현 설계, 파일 목록, 데이터 플로우
  └── Code Reviewer    → 사전 검증, 보안 취약점, 코드 품질
         ↓
Phase 2: 종합 및 구현
         ↓
Phase 3: 1차 코드리뷰
  ├── Critical 0건 → Phase 6
  └── Critical 1건+ → Phase 4
         ↓
Phase 4: 수정 개발
         ↓
Phase 5: 2차 코드리뷰 (최대 2회)
         ↓
Phase 6: Playwright 브라우저 테스트 (프론트엔드 변경 시)
         ↓
Phase 7: 완료 보고
```

## 특징

- Phase 1에서 Task tool로 3개 에이전트를 병렬 실행하여 분석 시간 단축
- 코드리뷰를 Critical / Warning / Suggestion으로 분류
- Playwright MCP를 활용한 실제 브라우저 동작 테스트
- 구조화된 완료 보고서 자동 생성
