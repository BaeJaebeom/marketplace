---
name: setup-branch
description: 여러 Git 프로젝트에 동시에 브랜치를 생성하고 push하는 워크플로우. 현재 디렉토리 하위 Git 프로젝트를 탐지하여 선택하고, production 브랜치 기반으로 새 브랜치를 생성합니다.
---

# 멀티 프로젝트 브랜치 생성 워크플로우

## Overview

현재 디렉토리 하위(1계층)의 Git 프로젝트들을 탐지하고, 사용자가 선택한 프로젝트들에 대해 production 브랜치 기반으로 새 브랜치를 생성하여 origin에 push한다.

## When to Use

- 여러 프로젝트에 걸친 작업을 시작할 때
- 모노레포 환경에서 관련 프로젝트들의 브랜치를 한 번에 만들고 싶을 때

## 동작 절차

### Step 1: 브랜치명 확인

`$ARGUMENTS`에서 브랜치명을 추출한다.

- 브랜치명이 제공된 경우: 해당 이름을 사용
- 브랜치명이 비어있는 경우: 사용자에게 브랜치명 입력을 요청하고 입력을 기다린다 (예시를 보여주지 않는다)

**브랜치명 정규화**: 브랜치명에 공백이 포함된 경우 모든 공백을 `-`(하이픈)으로 치환한다.
예: `RO-5466 회원상세 쿠폰 발급 내역 이전` → `RO-5466-회원상세-쿠폰-발급-내역-이전`

### Step 2: Git 프로젝트 탐지

현재 디렉토리의 하위 1계층 디렉토리 중 `.git` 폴더가 있는 프로젝트를 탐지한다.

```bash
# 현재 디렉토리의 하위 디렉토리 중 .git이 있는 것만 찾기
for dir in */; do [ -d "$dir/.git" ] && echo "${dir%/}"; done | sort
```

- Git 프로젝트가 하나도 없으면 "Git 프로젝트를 찾을 수 없습니다"라고 안내하고 종료

### Step 3: 프로젝트 선택

탐지된 Git 프로젝트 목록을 이름 오름차순으로 정렬한 뒤 번호와 함께 표시한다.

출력 형식:
```
## Git 프로젝트 목록

| 번호 | 프로젝트 |
|------|---------|
| 1 | rounz-cms-api |
| 2 | rounz-cms-fe |
| 3 | rounz-service-api |
| ... | ... |
```

사용자에게 선택할 프로젝트 번호를 입력하도록 요청하고 입력을 기다린다 (예시를 보여주지 않는다).
- 번호, 콤마, 범위(`-`), `all` 입력을 지원한다
- 다중 선택 가능

### Step 4: production 브랜치 확인, 체크아웃 및 업데이트

선택한 각 프로젝트에 대해 production 브랜치를 확인한 후 순차적으로 실행한다.

production 브랜치 탐지 (로컬 브랜치에서 우선순위대로 찾기):
```bash
cd {project_dir}

production_branch=""
for candidate in production prod main master; do
  if git show-ref --verify --quiet "refs/heads/$candidate"; then
    production_branch="$candidate"
    break
  fi
done

printf '%s\n' "$production_branch"
```

production 브랜치 탐지 우선순위:
1. `production`
2. `prod`
3. `main`
4. `master`

위 브랜치가 로컬에 하나도 없는 경우, origin의 default 브랜치를 사용한다:
```bash
cd {project_dir} && git remote show origin | grep 'HEAD branch' | awk '{print $NF}'
```

default 브랜치도 확인할 수 없는 경우 해당 프로젝트는 건너뛰고 오류 메시지를 표시한다.

각 프로젝트에 대해 순차적으로 실행한다:

```bash
cd {project_dir}

# 1. 현재 작업 중인 변경사항 확인
git status --porcelain

# 2. production 브랜치로 체크아웃
git checkout {production_branch}

# 3. 최신 버전으로 업데이트
git pull origin {production_branch}
```

**주의사항:**
- 커밋되지 않은 변경사항이 있는 프로젝트는 사용자에게 경고하고, 계속 진행할지 확인한다
- production 브랜치가 없는 프로젝트를 선택한 경우 해당 프로젝트는 건너뛰고 오류 메시지를 표시한다
- `git pull` 실패 시 사용자에게 알리고 해당 프로젝트는 건너뛴다

### Step 5: 새 브랜치 생성 및 push

각 프로젝트에 대해:

```bash
cd {project_dir}

# 1. 새 브랜치 생성
git checkout -b {branch_name}

# 2. origin에 push
git push -u origin {branch_name}
```

**주의사항:**
- remote는 반드시 `origin`을 사용한다. 다른 remote(upstream, fork 등)에 push하지 않는다
- 동일한 이름의 브랜치가 이미 존재하면 사용자에게 알리고 해당 프로젝트는 건너뛴다
- push 실패 시 사용자에게 알리고 해당 프로젝트는 건너뛴다

### Step 6: 결과 보고

모든 작업이 완료되면 결과를 보고한다:

```
## 브랜치 생성 결과

브랜치명: `{branch_name}`

| 프로젝트 | 상태 | production 브랜치 | 비고 |
|---------|------|------------------|------|
| rounz-cms-api | 성공 | production | push 완료 |
| rounz-cms-fe | 성공 | production | push 완료 |
| rounz-service-api | 실패 | - | production 브랜치 없음 |

### 성공: N개 / 실패: N개 / 건너뜀: N개
```
