# xquik-x-data

Xquik REST API 및 원격 MCP를 사용해 X 데이터 작업을 준비하는 Claude Code 플러그인입니다.

## 사용법

```
/x-twitter-scraper:x-twitter-scraper [Xquik 작업]
```

## 지원 작업

- REST API 및 SDK 설정
- 원격 MCP 설정
- tweet search, user lookup, timeline read
- export, monitor, webhook 계획
- 승인 기반 X action 계획

## 설정

`XQUIK_API_KEY` 환경 변수를 설정한 뒤 원격 MCP 설정을 사용하세요. API 키를 채팅, 로그, 예시, commit에 넣지 마세요.

## Source Checks

- Docs: https://docs.xquik.com
- OpenAPI: https://xquik.com/openapi.json
- MCP manifest: https://xquik.com/.well-known/mcp.json
- MCP endpoint: https://xquik.com/mcp

Xquik is an independent third-party service. Not affiliated with X Corp.
"Twitter" and "X" are trademarks of X Corp.
