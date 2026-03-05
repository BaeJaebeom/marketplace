# save-session

현재 세션의 작업 내용을 구조화된 MD 파일로 저장합니다. 다음 세션에서 `load-session`으로 작업 맥락을 복원하고 이어서 작업할 수 있습니다.

## 사용법

```
/save-session:save-session
```

## 동작 절차

1. `git status`, `git diff --stat`, `git branch` 로 변경 사항 수집
2. 작업 내용 기반으로 토픽명 자동 생성 (영문 kebab-case)
3. `{프로젝트루트}/sessions/YYYY-MM-DD-HHmm-{토픽}.md` 파일 생성
4. 저장 경로 안내

## 저장 내용

- 메타 정보 (날짜, 브랜치, 프로젝트 경로)
- 변경된 서브프로젝트 목록
- 작업 내용 (배경 / 내용 / 영향 범위)
- 주요 결정사항과 이유
- 미완성 작업 / 다음 단계
- 재개 시 참고사항
