# img_gen

[advisor-mcp](https://github.com/ifpksec/advisor-mcp) 로 Gemini · Qwen · Copilot · ChatGPT 에서 생성한 이미지 갤러리입니다.

**갤러리: https://ifpksec.github.io/img_gen/**

- 날짜별로 묶어 최신 날짜부터 한 페이지로 쭉 보여줍니다.
- 카드마다 이미지와 프롬프트를 함께 표시합니다.
- 카드의 **복사** 버튼은 이미지를 클립보드에 넣고(PNG), **다운로드** 버튼은 원본 파일을 받습니다.

페이지와 빌드 워크플로는 `E:\__NODE\img_gen`(junghh21/img_gen) 과 같은 형식입니다.

## 이미지가 올라오는 방식

advisor-mcp 가 이미지를 생성할 때마다 GitHub Contents API 로 이 저장소에 직접 올립니다.

```http
PUT https://api.github.com/repos/ifpksec/img_gen/contents/images/2026-09-15T11-02-56-123Z-copilot.png
Authorization: Bearer <token>
Accept: application/vnd.github+json

{ "message": "image: copilot — …", "content": "<base64>", "branch": "main" }
```

이미지 한 장당 파일 두 개가 생깁니다.

```
images/2026-09-15T11-02-56-123Z-copilot.png    이미지
images/2026-09-15T11-02-56-123Z-copilot.json   메타데이터
```

```json
{ "file": "2026-09-15T11-02-56-123Z-copilot.png", "provider": "copilot",
  "prompt": "부산 바다 위로 떠오르는 일출, 유화 스타일",
  "date": "2026-09-15T11:02:56.123Z", "width": 1024, "height": 1024 }
```

메타데이터에는 파일명 · 자문사 · 프롬프트 · 시각 · 크기만 담습니다. 공개 저장소이므로 개인 대화 주소는 넣지 않습니다.

## 빌드

`images.json` 은 저장소에 커밋하지 않습니다. 푸시마다 GitHub Actions(`.github/workflows/pages.yml`)가
`images/*.json` 메타데이터를 모아 빌드 시점에 `images.json` 을 만들고 Pages 로 배포합니다.
페이지는 이 정적 JSON 만 읽으므로 API 호출이나 서버가 필요 없습니다.
이미지 한 장은 커밋 두 개(이미지 + 메타데이터)로 들어오므로, 앞선 빌드는 취소하고 마지막 빌드만 배포합니다.

## 구조

```
index.html                    갤러리 (날짜별 묶음 · 이미지+프롬프트 카드 · 복사/다운로드)
.github/workflows/pages.yml   images/*.json → images.json → Pages 배포
images/                       이미지와 메타데이터
```
