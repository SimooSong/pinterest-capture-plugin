# Skill: Pinterest Search Agent

## 역할

이 서브에이전트는 Playwright MCP를 사용하여 Pinterest에서 키워드를 검색하고, 이미지 핀 URL 목록을 수집합니다. 캡처 서브에이전트에게 전달할 데이터를 준비하는 역할입니다.

## 입력

```
KEYWORD: [검색 키워드]
TARGET_COUNT: 30
SAVE_PATH: C:\Projects\my-project\captures\[키워드_날짜]
```

## 실행 지침

### 1단계: Pinterest 접속 및 검색

Playwright MCP를 사용하여 아래 순서로 실행합니다:

```
1. browser_navigate → https://www.pinterest.com/search/pins/?q=[URL_ENCODED_KEYWORD]
2. 페이지 로딩 완료 대기 (네트워크 idle 상태까지)
3. 쿠키/팝업 닫기 버튼이 있으면 클릭하여 닫기
```

### 2단계: 이미지 핀 URL 수집

```
1. 현재 화면에서 핀 링크(a[href*="/pin/"]) 요소를 모두 선택
2. 각 핀의 href URL을 배열에 저장
3. 핀 수가 TARGET_COUNT(30)에 미달하면 스크롤 다운 후 반복
4. URL 중복 제거 (Set 사용)
5. 정확히 30개가 될 때까지 반복 (최대 5회 스크롤)
```

### 3단계: 이미지 직접 URL 추출

각 핀 URL을 방문하여 실제 이미지 URL을 추출합니다:

```
1. 핀 페이지 접속: browser_navigate → [pin_url]
2. 고해상도 이미지 요소 찾기: img[src*="pinimg.com"] 중 가장 큰 것
3. src 속성에서 이미지 URL 추출
4. 원본 사이즈 URL로 변환: /236x/ → /originals/ 로 교체
```

### 4단계: 기존 파일 중복 검사

저장 경로에 이미 존재하는 파일들과 비교:

```
1. SAVE_PATH 폴더 내 기존 .png 파일 목록 조회
2. 기존 파일명에서 URL 해시 정보 확인 (파일명에 해시 포함)
3. 수집된 이미지 URL 해시와 비교
4. 중복된 URL은 수집 목록에서 제거
5. 중복 제거 후 30개 미달이면 추가 수집 반복
```

### 5단계: 결과 반환

수집 완료 후 아래 형식으로 반환합니다:

```json
{
  "keyword": "[검색 키워드]",
  "collected_count": 30,
  "duplicate_removed": [중복 제거 수],
  "image_list": [
    {
      "index": 1,
      "pin_url": "https://www.pinterest.com/pin/...",
      "image_url": "https://i.pinimg.com/originals/...",
      "url_hash": "[MD5 해시 앞 8자리]"
    },
    ...
  ],
  "save_path": "C:\\Projects\\my-project\\captures\\[키워드_날짜]"
}
```

## 오류 처리

- Pinterest 로그인 요청 화면이 나타나면: 로그인 없이 접근 가능한 공개 핀만 수집
- 핀 페이지 로딩 실패 시: 해당 핀 건너뛰고 다음 핀으로 이동
- 30개 수집 불가 시: 최대한 수집 후 실제 수집 수를 보고
- 네트워크 타임아웃: 개별 핀당 최대 10초 대기

## 주의사항

- User-Agent는 일반 브라우저로 설정
- 각 핀 방문 사이 0.5~1초 간격 유지 (과부하 방지)
- 이미지 URL은 고해상도 원본 기준으로 수집
