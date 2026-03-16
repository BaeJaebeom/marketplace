# setup-branch

여러 Git 프로젝트에 동시에 작업 브랜치를 생성하고 origin에 push하는 워크플로우입니다.

## Usage

```
/setup-branch:setup-branch [브랜치명]
```

## Features

- 현재 디렉토리 하위 1계층의 Git 프로젝트를 자동 탐지
- 프로젝트 이름 오름차순 정렬
- 번호 기반 다중 선택 지원
- 선택한 프로젝트의 production 브랜치로 체크아웃 후 최신 버전으로 업데이트
- 지정한 이름으로 새 브랜치 생성 및 origin push
- production 브랜치 탐지 우선순위: `production` > `prod` > `main` > `master`
- 로컬에 production 브랜치가 없으면 origin의 default 브랜치를 fallback으로 사용
