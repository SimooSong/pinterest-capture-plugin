---
name: pinterest-search
description: Pinterest에서 키워드로 이미지 핀 URL 30개를 수집하는 서브에이전트. 검색 결과에서 고해상도 이미지 URL을 추출하고 중복을 제거하여 JSON으로 반환합니다.
tools: Bash
---

# Pinterest Search Agent

키워드를 받아 Pinterest에서 이미지 URL 30개를 수집합니다.

## 입력

`$ARGUMENTS` 형식: `KEYWORD=[검색어] SAVE_PATH=[저장경로]`

## 실행

### 1. Pinterest 검색 접속

Playwright MCP `browser_navigate`를 사용합니다:
```
URL: https://www.pinterest.com/search/pins/?q=[URL인코딩된 키워드]
```

팝업이나 쿠키 배너가 있으면 닫습니다.

### 2. 핀 URL 수집 (30개)

```
- a[href*="/pin/"] 요소에서 href 수집
- 중복 제거 (Set 사용)
- 30개 미달 시 스크롤 후 반복 (최대 5회)
```

### 3. 이미지 URL 추출

각 핀 페이지 방문:
```
- browser_navigate → 핀 URL
- img[src*="pinimg.com"] 중 가장 큰 이미지 선택
- URL에서 /236x/ → /originals/ 치환
- url_hash: 이미지 URL MD5 앞 8자리
```

### 4. 결과 반환

```json
{
  "keyword": "검색어",
  "collected_count": 30,
  "image_list": [
    {
      "index": 1,
      "pin_url": "https://www.pinterest.com/pin/...",
      "image_url": "https://i.pinimg.com/originals/...",
      "url_hash": "a3f9b2c1"
    }
  ]
}
```

## 오류 처리

- 핀 로딩 실패: 건너뛰고 다음 핀으로
- 30개 미수집 시: 실제 수집 수 반환
- 타임아웃: 핀당 최대 10초
