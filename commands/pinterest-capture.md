# /pinterest-capture

Pinterest에서 키워드 기반 레퍼런스 이미지를 30장 자동 캡처하여 저장합니다.

## 사용법

```
/pinterest-capture [키워드]
```

## 인수

- `키워드` (필수): Pinterest에서 검색할 키워드 (예: "minimal interior", "브랜드 로고 디자인")

## 예시

```
/pinterest-capture minimal interior design
/pinterest-capture 카페 브랜딩 레퍼런스
/pinterest-capture brutalist typography poster
```

## 실행 흐름

1. **검색 서브에이전트** → Pinterest에서 키워드 검색 후 이미지 URL 30개 수집
2. **캡처 서브에이전트** → 수집된 URL을 순서대로 스크린샷
3. **중복 검사** → 이미 저장된 이미지와 비교, 중복 제거
4. **미리보기** → 저장 전 캡처 목록 확인
5. **저장** → 확인 후 `C:\Projects\my-project\captures\[키워드]\` 에 저장

## 참고

- 저장 경로: `C:\Projects\my-project\captures\[키워드_날짜]\`
- 파일명 형식: `[키워드]_[순번].png`
- 중복 방지: 파일명 + 이미지 해시 기반 검사
