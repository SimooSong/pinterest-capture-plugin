---
description: Pinterest에서 키워드 기반 레퍼런스 이미지 30장을 자동 캡처하여 저장합니다
argument-hint: <키워드>
allowed-tools: Bash, Read, Write, Glob, Agent
---

Pinterest에서 "$ARGUMENTS" 키워드로 레퍼런스 이미지 30장을 수집하고 캡처합니다.

## 실행 순서

### 1단계: 저장 폴더 생성

키워드와 현재 시각으로 저장 경로를 만듭니다:
- 경로: `C:\Projects\my-project\captures\[키워드_YYYYMMDD_HHMMSS]`
- 공백은 언더스코어로, 특수문자는 제거

```bash
$keyword = "$ARGUMENTS" -replace ' ', '_' -replace '[^a-zA-Z0-9가-힣_-]', ''
$timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$savePath = "C:\Projects\my-project\captures\${keyword}_${timestamp}"
New-Item -ItemType Directory -Path $savePath -Force
```

### 2단계: Search 서브에이전트 실행

Agent 툴로 검색 서브에이전트를 실행합니다:

**Search 서브에이전트 지시사항:**
- Playwright MCP의 `browser_navigate`로 `https://www.pinterest.com/search/pins/?q=[URL인코딩된 키워드]` 접속
- 핀 링크(`a[href*="/pin/"]`) 30개 수집 (부족하면 스크롤 후 반복)
- 중복 URL 제거
- 각 핀의 고해상도 이미지 URL 추출 (`img[src*="pinimg.com"]` → `/originals/` 치환)
- 결과를 JSON 배열로 반환: `[{"index":1, "pin_url":"...", "image_url":"...", "url_hash":"..."}]`

### 3단계: 미리보기 출력 및 확인

수집된 30개 이미지 목록을 출력합니다:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📌 Pinterest 캡처 미리보기
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
키워드: $ARGUMENTS
수집:   30장
저장:   [savePath]

 1. https://pinterest.com/pin/...
 2. https://pinterest.com/pin/...
...
30. https://pinterest.com/pin/...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
저장할까요? [Y] 저장  [N] 취소
```

사용자가 N을 선택하면 중단합니다. Y이면 4단계로 진행합니다.

### 4단계: Capture 서브에이전트 실행

Agent 툴로 캡처 서브에이전트를 실행합니다:

**Capture 서브에이전트 지시사항:**
- 각 이미지 URL에 대해:
  1. 중복 검사: 저장 폴더에 동일 해시 파일이 있으면 건너뜀
  2. Playwright MCP `browser_navigate`로 이미지 URL 접속
  3. `browser_take_screenshot`으로 전체 페이지 캡처
  4. 파일명: `[keyword]_[순번3자리]_[hash8자리].png`
  5. savePath에 저장
  6. 진행률 표시: `[5/30] 캡처 완료...`

### 5단계: 완료 보고

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ 캡처 완료
키워드: $ARGUMENTS
저장됨: [N]장
경로:   [savePath]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```
