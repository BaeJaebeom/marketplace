# Claude Code Plugin Marketplace

이 프로젝트는 **Claude Code 플러그인 마켓플레이스**입니다. Claude Code의 기능을 확장하는 플러그인을 개발하고 배포합니다.

## 프로젝트 구조

```
marketplace/
├── .claude-plugin/
│   └── marketplace.json      # 마켓플레이스 정의 파일
├── plugins/
│   ├── dev-review-loop/      # 병렬 개발 + 코드리뷰 반복 워크플로우
│   ├── save-session/         # 세션 작업 내용 저장
│   └── load-session/         # 이전 세션 자동 불러오기
├── CLAUDE.md
├── README.md
└── LICENSE
```

## 플러그인 목록

### 1. dev-review-loop
- **설명**: 병렬 개발 에이전트 3개로 분석/설계 후 구현하고, 코드리뷰→수정→재리뷰 반복 워크플로우
- **타입**: Skill
- **스킬**: `/dev-review-loop:dev-review-loop [작업 내용]`
- **특징**:
  - Phase 1: 3개 에이전트 병렬 분석 (Code Explorer, Code Architect, Code Reviewer)
  - Phase 2: 종합 및 구현
  - Phase 3-5: 코드리뷰 → 수정 → 재리뷰 (최대 2회)
  - Phase 6: Playwright 브라우저 테스트
  - Phase 7: 완료 보고

### 2. save-session (v2.0.0)
- **설명**: 현재 세션의 작업 내용을 구조화된 MD 파일로 저장
- **타입**: Skill
- **스킬**: `/save-session:save-session`
- **특징**:
  - git 변경사항 기반 정보 수집
  - 서브프로젝트 단위 그룹핑
  - 토픽명 자동 생성
  - 다음 세션에서 복원 가능한 핸드오프 문서 생성

### 3. load-session
- **설명**: 이전 세션에서 저장한 작업 내용을 자동으로 불러와 작업 재개
- **타입**: Skill
- **스킬**: `/load-session:load-session`
- **특징**:
  - `sessions/` 폴더에서 수정시간 기준 최근 파일 자동 선택
  - 세션 파일 읽기 및 맥락 복원
  - 남은 작업 목록과 참고사항 요약 보고

## 플러그인 개발 가이드

### 플러그인 구조

```
plugin-name/
├── .claude-plugin/
│   └── plugin.json           # 플러그인 메타데이터 (필수)
├── skills/                   # 스킬 정의
│   └── skill-name/
│       └── SKILL.md
├── hooks/                    # 훅 스크립트
│   └── hook-script.sh
└── scripts/                  # 유틸리티 스크립트
```

### plugin.json 필수 필드

```json
{
  "name": "plugin-name",
  "description": "플러그인 설명",
  "version": "1.0.0",
  "author": {
    "name": "작성자 이름",
    "email": "email@example.com"
  }
}
```

## 로컬 테스트

```bash
# 단일 플러그인 테스트
claude --plugin-dir ./plugins/dev-review-loop

# 여러 플러그인 테스트
claude --plugin-dir ./plugins/dev-review-loop --plugin-dir ./plugins/save-session
```

## 라이선스

MIT
