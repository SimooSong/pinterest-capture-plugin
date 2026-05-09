---
name: pinterest-capture
description: Search Agent가 수집한 Pinterest 이미지 URL을 스크린샷으로 캡처하여 로컬에 저장하는 서브에이전트. 미리보기 확인 및 중복 방지 기능 포함.
tools: Bash, Read, Write, Glob
---

# Pinterest Capture Agent

Search Agent의 JSON 결과를 받아 이미지를 캡처하고 저장합니다.

## 입력

`$ARGUMENTS` 형식:
```json
{
  "keyword": "검색어",
  "image_list": [...],
  "save_path": "C:\\Projects\\my-project\\captures\\..."
}
```

## 실행

### 1. 저장 폴더 준비

save_path 폴더가 없으면 생성합니다.

### 2. 중복 검사

저장 폴더의 기존 파일명에서 url_hash를 추출하고, 수집 목록과 비교합니다. 중복 항목은 제거합니다.

### 3. 미리보기 출력

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
📌 캡처 미리보기
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
키워드: [keyword]
저장:   [저장 예정 수]장
경로:   [save_path]

 1. https://pinterest.com/pin/...
...
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

사용자에게 확인을 받습니다. N이면 중단합니다.

### 4. 순서대로 캡처 및 저장

각 이미지에 대해:
1. Playwright MCP `browser_navigate` → 이미지 URL 접속
2. `browser_take_screenshot` 캡처
3. 파일명 생성: `[keyword_snake]_[순번3자리]_[hash8자리].png`
4. save_path에 저장
5. 진행 상황 출력: `[N/30] 저장 완료`

### 5. 완료 보고

```
✅ 캡처 완료: [N]장 저장 → [save_path]
```

## 오류 처리

- 캡처 실패: 건너뛰고 계속 진행
- 연속 3회 실패: 사용자에게 알리고 계속 여부 확인
