# pinterest-capture-plugin

Pinterest에서 키워드 기반 레퍼런스 이미지를 30장 자동 캡처하는 Claude Code 플러그인입니다.

## 주요 기능

- Playwright MCP를 이용한 브라우저 자동화
- 검색/캡처 서브에이전트 분리 구조
- 중복 방지: 파일명 + URL 해시 이중 검사
- 저장 전 미리보기 확인 단계
- 자동 폴더 생성 및 순번 파일명 관리

## 설치 방법

### 1. Playwright MCP 설치

```bash
npm install -g @playwright/mcp
npx playwright install chromium
```

### 2. 플러그인 등록

Claude Code 설정에 MCP 서버 추가:

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
    }
  }
}
```

### 3. 플러그인 설치 (팀 마켓플레이스)

```
/plugin install [github-username]/pinterest-capture-plugin
```

## 사용법

```
/pinterest-capture [키워드]
```

### 예시

```
/pinterest-capture minimal interior
/pinterest-capture 카페 브랜딩
/pinterest-capture brutalist poster design
```

## 실행 흐름

```
사용자 입력 키워드
        ↓
[Search 서브에이전트]
  Pinterest 검색 → 핀 URL 30개 수집 → 이미지 URL 추출 → 중복 URL 제거
        ↓
[Capture 서브에이전트]
  미리보기 목록 출력 → 사용자 확인 → 순서대로 스크린샷 → 중복 파일 검사 → 저장
        ↓
완료 보고 (저장 수, 경로, 파일 목록)
```

## 저장 구조

```
C:\Projects\my-project\captures\
└── [keyword_YYYYMMDD_HHMMSS]\
    ├── [keyword]_001_[hash].png
    ├── [keyword]_002_[hash].png
    ├── ...
    └── [keyword]_030_[hash].png
```

## 안전장치

| 기능 | 설명 |
|------|------|
| 미리보기 확인 | 저장 전 30개 URL 목록 표시, 사용자 승인 필수 |
| 중복 방지 (파일명) | 동일 파일명 존재 시 자동 건너뜀 |
| 중복 방지 (해시) | URL 해시 비교로 다른 이름의 동일 이미지 탐지 |
| 오류 복구 | 개별 캡처 실패 시 건너뛰고 계속 진행 |
| 취소 지원 | 미리보기 단계에서 N 선택으로 전체 취소 가능 |

## 파일 구조

```
pinterest-capture-plugin/
├── .claude-plugin/
│   ├── plugin.json        # 플러그인 매니페스트
│   └── .mcp.json          # Playwright MCP 서버 설정
├── commands/
│   └── pinterest-capture.md  # /pinterest-capture 커맨드 정의
├── skills/
│   ├── search/
│   │   └── SKILL.md       # 검색 서브에이전트
│   └── capture/
│       └── SKILL.md       # 캡처 서브에이전트
└── README.md
```

## 마켓플레이스 등록

```bash
# marketplace.json 업데이트 후 push
git add .
git commit -m "add pinterest-capture-plugin"
git push origin main
```

## 요구사항

- Claude Code
- Node.js 18+
- Playwright MCP (`@playwright/mcp`)
- Chromium (자동 설치됨)

## 라이선스

MIT
