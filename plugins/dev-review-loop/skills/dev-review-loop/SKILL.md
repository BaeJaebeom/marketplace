---
name: dev-review-loop
description: 병렬 개발 에이전트 3개로 분석/설계 후 구현하고, 코드리뷰→수정→재리뷰 반복 워크플로우를 실행합니다.
---

> **Playwright 테스트 포함**: 코드리뷰 완료 후 Playwright MCP를 활용한 브라우저 동작 테스트를 수행합니다.

# 반복 개발 + 코드리뷰 워크플로우

## 작업 내용

$ARGUMENTS

## 필수 규칙

- 반드시 아래 Phase 순서를 따를 것
- Phase 1의 3개 에이전트는 **반드시 Task tool을 사용하여 동시에 병렬 실행**할 것 (하나의 메시지에서 3개의 Task tool call)
- 각 에이전트의 결과를 종합하여 구현할 것
- 코드리뷰에서 Critical Issue가 있으면 반드시 수정 후 재리뷰할 것
- 프로젝트 루트의 CLAUDE.md와 각 서브프로젝트의 CLAUDE.md를 반드시 참조할 것
- 리뷰 반복은 최대 2회까지만 수행할 것
- Playwright 테스트 반복은 최대 2회까지만 수행할 것
- Playwright 테스트 전후로 관련 서비스를 반드시 재시작/종료할 것 (CLAUDE.md 규칙 준수)

---

## Phase 1: 병렬 분석 (3개 에이전트 동시 실행)

Task tool을 사용하여 아래 3개 에이전트를 **하나의 메시지에서 동시에** 실행한다.

### Agent 1: Code Explorer (subagent_type: feature-dev:code-explorer)

프롬프트에 반드시 포함할 내용:
- 작업 내용: `$ARGUMENTS`
- 분석 요청 사항:
  - 작업과 관련된 기존 코드의 실행 경로 추적
  - 아키텍처 계층 구조 매핑 (Controller → Service → Repository)
  - 사용 중인 패턴과 컨벤션 파악
  - 의존성 관계 문서화
  - 재사용 가능한 기존 컴포넌트/추상화 식별
- 출력 형식: 파일 경로와 라인 번호를 포함한 구조화된 분석 결과

### Agent 2: Code Architect (subagent_type: feature-dev:code-architect)

프롬프트에 반드시 포함할 내용:
- 작업 내용: `$ARGUMENTS`
- 설계 요청 사항:
  - 기존 코드베이스 패턴 분석 기반 구현 설계
  - 생성/수정할 파일 목록과 각 파일의 역할
  - 컴포넌트 간 데이터 플로우
  - DB 스키마 변경 사항 (필요시)
  - 구현 순서 (빌드 시퀀스)
- 출력 형식: 구현 블루프린트 문서

### Agent 3: Code Reviewer (사전 검증) (subagent_type: feature-dev:code-reviewer)

프롬프트에 반드시 포함할 내용:
- 작업 내용: `$ARGUMENTS`
- 사전 검증 요청 사항:
  - 작업 대상 영역의 기존 코드 품질 이슈 파악
  - 보안 취약점이 될 수 있는 패턴 식별
  - 테스트 커버리지 현황 확인
  - 기존 코드에서 주의해야 할 함정(gotcha) 파악
- 출력 형식: 사전 검증 리포트

---

## Phase 2: 종합 및 구현

Phase 1에서 받은 3개 에이전트의 결과를 종합한다:

1. **Code Explorer 결과**: 기존 패턴과 컨벤션을 파악하여 코드 스타일 맞춤
2. **Code Architect 결과**: 설계 블루프린트에 따라 구현 순서 결정
3. **Code Reviewer 결과**: 사전 검증에서 발견된 주의사항 반영

종합한 내용을 바탕으로 실제 코드를 구현한다:
- 설계 블루프린트의 구현 순서를 따를 것
- 기존 코드베이스의 패턴과 컨벤션을 준수할 것
- 사전 검증에서 지적된 위험 요소를 회피할 것

---

## Phase 3: 1차 코드리뷰

구현이 완료되면 Task tool로 code-reviewer 에이전트를 실행한다.

### Code Review Agent (subagent_type: feature-dev:code-reviewer)

프롬프트에 반드시 포함할 내용:
- 원래 작업 요청: `$ARGUMENTS`
- 변경된 파일 목록과 각 파일의 변경 내용 요약
- 리뷰 기준:
  - **Critical Issues** (반드시 수정): 보안 취약점, 로직 오류, 리소스 누수, NPE
  - **Warnings** (수정 권장): 성능 이슈, 에러 처리 누락, 네이밍 불일치
  - **Suggestions** (개선 제안): 코드 가독성, 테스트 커버리지, 리팩토링 기회
- 프로젝트 CLAUDE.md의 코딩 규칙 준수 여부 확인 요청
- 출력 형식: Critical / Warning / Suggestion 분류된 리뷰 리포트

### 리뷰 결과 판단

- **Critical Issues가 0건**: Phase 6(Playwright 테스트)으로 이동
- **Critical Issues가 1건 이상**: Phase 4(수정)로 이동

---

## Phase 4: 수정 개발

Phase 3의 코드리뷰 결과를 바탕으로 코드를 수정한다:

1. Critical Issues를 우선 수정
2. Warnings 중 타당한 것 수정
3. Suggestions는 간단히 반영 가능한 것만 수정

---

## Phase 5: 2차 코드리뷰

수정된 코드에 대해 다시 Task tool로 code-reviewer 에이전트를 실행한다.

### Code Review Agent (subagent_type: feature-dev:code-reviewer)

Phase 3과 동일한 형식으로 리뷰를 요청하되, 추가로:
- 1차 리뷰에서 지적된 Critical Issues가 해결되었는지 확인 요청
- 수정 과정에서 새로운 이슈가 발생하지 않았는지 확인 요청

### 리뷰 결과 판단

- **Critical Issues가 0건**: Phase 6(Playwright 테스트)으로 이동
- **Critical Issues가 1건 이상**: 사용자에게 남은 이슈를 보고하고 수동 판단 요청

---

## Phase 6: Playwright 브라우저 테스트

구현 및 코드리뷰가 완료된 코드를 실제 브라우저에서 동작 테스트한다.

### Step 1: 대상 프로젝트 판별

변경된 파일 경로를 기반으로 테스트 대상 프로젝트를 판별한다.
- 프론트엔드 또는 UI 관련 변경이 포함된 경우에만 Playwright 테스트를 수행한다.
- **판별이 불가능한 경우** (백엔드 전용 변경 등): Playwright 테스트를 건너뛰고 Phase 7(완료 보고)으로 이동하며, 완료 보고에 "브라우저 테스트 해당 없음" 기록.
- 테스트 URL, 로그인 정보, 서비스 재시작 절차 등은 **프로젝트의 CLAUDE.md**를 참조하여 결정한다.

### Step 2: 서비스 재시작

대상 프로젝트에 따라 관련 서비스를 재시작한다. **프로젝트 CLAUDE.md의 playwright mcp 사용 규칙을 반드시 준수할 것.**

- 프로젝트 CLAUDE.md에 서비스 재시작 절차가 명시되어 있으면 그에 따른다.
- 명시되어 있지 않으면, 해당 프로젝트의 빌드/실행 명령어를 참고하여 서비스를 재시작한다.
- 서비스가 준비될 때까지 대기 (health check 또는 적절한 대기 시간)

### Step 3: Playwright MCP 테스트 실행

Playwright MCP 도구들을 사용하여 브라우저 테스트를 수행한다.

#### 테스트 절차:
1. **browser_navigate**: 대상 URL로 이동
2. **로그인이 필요한 경우**: 프로젝트 CLAUDE.md에 명시된 테스트 계정 정보를 사용
3. **browser_snapshot**: 페이지 상태를 캡처하여 정상 로드 확인
4. **변경된 기능으로 이동**: 구현한 기능이 있는 페이지/화면으로 네비게이션
5. **기능 동작 테스트**: 구현한 기능을 실제로 조작하여 테스트
   - 입력 필드 채우기 (browser_fill_form / browser_type)
   - 버튼 클릭 (browser_click)
   - 결과 확인 (browser_snapshot)
6. **에러 확인**: browser_console_messages로 콘솔 에러 확인
7. **browser_take_screenshot**: 테스트 결과 스크린샷 저장 (파일명: `playwright-test-{timestamp}.png`)

#### 테스트 판단 기준:
- **PASS**: 기능이 의도대로 동작하고 콘솔에 critical 에러가 없음
- **FAIL**: 기능이 동작하지 않거나, 페이지 로드 실패, 콘솔에 critical 에러 존재

### Step 4: 테스트 실패 시 수정 (최대 2회 반복)

테스트가 FAIL인 경우:
1. 실패 원인 분석 (에러 메시지, 콘솔 로그, 스크린샷 분석)
2. 코드 수정
3. 서비스 재시작 (Step 2 반복)
4. 테스트 재실행 (Step 3 반복)

- **2회 반복 후에도 FAIL**: 사용자에게 실패 내용을 보고하고 수동 판단 요청

### Step 5: 서비스 종료

테스트 완료 후 (PASS든 FAIL이든) Step 2에서 시작한 서비스를 종료한다.
- 프로젝트 CLAUDE.md의 서비스 종료 규칙을 따른다.
- 브라우저: browser_close로 닫기

---

## Phase 7: 완료 보고

최종 결과를 아래 형식으로 보고한다:

```
## 구현 완료 보고

### 작업 요약
- 요청 사항: [원래 요청]
- 변경 파일 수: [N]개

### 변경 파일 목록
| 파일 | 변경 유형 | 설명 |
|------|----------|------|
| path/to/file | 생성/수정 | 설명 |

### 아키텍처 결정 사항
- [주요 설계 결정과 근거]

### 코드리뷰 결과
- 1차 리뷰: Critical [N]건, Warning [N]건, Suggestion [N]건
- 2차 리뷰: Critical [N]건, Warning [N]건, Suggestion [N]건 (수행한 경우)

### Playwright 브라우저 테스트 결과
- 대상 프로젝트: [프로젝트명 / 해당 없음]
- 테스트 결과: [PASS / FAIL / 미수행]
- 테스트 횟수: [N]회
- 수정 후 재테스트: [있음(N회) / 없음]
- 스크린샷: [파일 경로 또는 "없음"]
- 발견된 이슈: [이슈 내용 또는 "없음"]

### 남은 사항
- [수동으로 확인이 필요한 사항]
- [추후 개선 가능한 사항]
```
