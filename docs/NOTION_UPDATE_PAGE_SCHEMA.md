<!-- 목적: Notion MCP replace_content(notion-update-page) 호출 시 사용하는 파라미터 스키마·명령 정의. 개발/자동화 참고용. -->
# Notion MCP: `notion-update-page` 도구 파라미터 스키마

## 1. 요약

- **도구 이름**: `notion-update-page` (또는 `notion_update_page`)
- **필수 파라미터**: `data` (object)
- **data** 안에 반드시: `page_id` (string) + **명령 하나** (command + 해당 명령 필드)

---

## 2. 파라미터 스키마

### 최상위

| 파라미터 | 타입   | 필수 | 설명 |
|----------|--------|------|------|
| `data`   | object | ✅   | 업데이트에 쓸 payload. 아래 구조 준수. |

### `data` 객체 공통

| 필드       | 타입   | 필수 | 설명 |
|------------|--------|------|------|
| `page_id`  | string | ✅   | 수정할 Notion 페이지 ID (UUID, 대시 포함/미포함 가능). 예: `3069f1bf-3171-807f-abde-d74c9bb72f47` |
| `command`  | string | ✅   | 아래 4가지 중 하나: `update_properties` \| `replace_content` \| `replace_content_range` \| `insert_content_after` |

### 명령별 추가 필드

#### A. `update_properties` (속성만 수정)

| 필드         | 타입   | 필수 | 설명 |
|--------------|--------|------|------|
| `command`    | string | ✅   | `"update_properties"` |
| `properties` | object | ✅   | Notion 페이지 속성 키-값. DB 페이지면 스키마에 맞는 값. |

#### B. `replace_content` (본문 전체 교체)

| 필드                    | 타입    | 필수 | 설명 |
|-------------------------|---------|------|------|
| `command`               | string  | ✅   | `"replace_content"` |
| `new_str`               | string  | ✅   | Notion 마크다운 형식의 새 본문 전체. |
| `allow_deleting_content`| boolean | ❌   | 기존 블록 삭제 허용 여부. 기본값 없음. |

#### C. `replace_content_range` (일부 구간만 교체)

| 필드                    | 타입    | 필수 | 설명 |
|-------------------------|---------|------|------|
| `command`               | string  | ✅   | `"replace_content_range"` |
| `selection_with_ellipsis` | string | ✅   | 교체할 구간의 **시작 ~10자 ... 끝 ~10자**. 고유해야 함. |
| `new_str`               | string  | ✅   | 교체할 새 내용. |
| `allow_deleting_content`| boolean | ❌   | 선택 구간 내 자식 페이지/DB 삭제 허용 여부. |

#### D. `insert_content_after` (특정 위치 뒤에 삽입)

| 필드                    | 타입   | 필수 | 설명 |
|-------------------------|--------|------|------|
| `command`               | string | ✅   | `"insert_content_after"` |
| `selection_with_ellipsis` | string | ✅   | 삽입 위치 기준이 되는 구간의 **시작 ~10자 ... 끝 ~10자**. 고유해야 함. |
| `new_str`               | string | ✅   | 삽입할 내용 (Notion 마크다운). |

---

## 3. 예시 payload (JSON)

### 3.1 속성만 수정 (`update_properties`)

```json
{
  "data": {
    "page_id": "3069f1bf-3171-807f-abde-d74c9bb72f47",
    "command": "update_properties",
    "properties": {
      "title": "PRD - ai-webtoon-publishing-automation (Q&A)"
    }
  }
}
```

### 3.2 본문 전체 교체 (`replace_content`)

```json
{
  "data": {
    "page_id": "3069f1bf-3171-807f-abde-d74c9bb72f47",
    "command": "replace_content",
    "new_str": "# 0. Change Log\n\n| 날짜 | 버전 | 변경 내용 | 작성자 |\n|------|------|-----------|--------|\n| [YYYY-MM-DD] | v0.1 | 초안 작성 | [이름] |\n\n---\n\n# 1. Goal / One-liner\n\n본문 내용...",
    "allow_deleting_content": true
  }
}
```

### 3.3 일부 구간 교체 (`replace_content_range`)

```json
{
  "data": {
    "page_id": "3069f1bf-3171-807f-abde-d74c9bb72f47",
    "command": "replace_content_range",
    "selection_with_ellipsis": "# 0. Change Log...작성자 |",
    "new_str": "# 0. Change Log\n\n| 날짜 | 버전 | 변경 내용 | 작성자 |\n|------|------|-----------|--------|\n| 2026-02-13 | v0.1 | 수정 | 팀 |",
    "allow_deleting_content": false
  }
}
```

### 3.4 특정 위치 뒤에 삽입 (`insert_content_after`)

```json
{
  "data": {
    "page_id": "3069f1bf-3171-807f-abde-d74c9bb72f47",
    "command": "insert_content_after",
    "selection_with_ellipsis": "# 7. References...기타: [이름 / 날짜]",
    "new_str": "\n\n---\n\n## 추가 섹션\n\n추가 내용."
  }
}
```

---

## 4. `prd_for_notion_insert.md` 를 넣기 위한 최소 예시

대상 파일: `docs/prd_for_notion_insert.md`  
목표: 해당 파일 전체를 Notion 페이지 **본문**에 넣기 (전체 교체).

### 4.1 사용할 명령

- **command**: `replace_content`
- **page_id**: `3069f1bf-3171-807f-abde-d74c9bb72f47`
- **new_str**: `prd_for_notion_insert.md` 파일 내용 전체 (UTF-8 문자열)
- **allow_deleting_content**: `true` (기존 본문 블록을 지우고 새 내용으로 채울 때)

### 4.2 최소 payload 구조 (의사 JSON)

```json
{
  "data": {
    "page_id": "3069f1bf-3171-807f-abde-d74c9bb72f47",
    "command": "replace_content",
    "new_str": "<파일 docs/prd_for_notion_insert.md 의 전체 텍스트>",
    "allow_deleting_content": true
  }
}
```

### 4.3 스크립트로 payload 만드는 예시 (Node.js)

```javascript
const fs = require('fs');
const path = require('path');

const filePath = path.join(__dirname, 'prd_for_notion_insert.md');
const newStr = fs.readFileSync(filePath, 'utf8');

const payload = {
  data: {
    page_id: '3069f1bf-3171-807f-abde-d74c9bb72f47',
    command: 'replace_content',
    new_str: newStr,
    allow_deleting_content: true
  }
};

console.log(JSON.stringify(payload, null, 2));
```

### 4.4 스크립트로 payload 만드는 예시 (Python)

```python
import json
from pathlib import Path

file_path = Path(__file__).parent / "prd_for_notion_insert.md"
new_str = file_path.read_text(encoding="utf-8")

payload = {
    "data": {
        "page_id": "3069f1bf-3171-807f-abde-d74c9bb72f47",
        "command": "replace_content",
        "new_str": new_str,
        "allow_deleting_content": True,
    }
}

print(json.dumps(payload, ensure_ascii=False, indent=2))
```

### 4.5 MCP 도구 호출 시 주의사항

- `data`는 **반드시 객체**로 전달. 문자열로 넘기면 `Expected object, received string` 오류 발생.
- `new_str` 안에 이스케이프 필요한 문자(따옴표, 줄바꿈 등)는 사용하는 클라이언트/언어의 JSON 규칙에 맞게 처리.
- 파일이 크면 `new_str` 길이 제한이 있을 수 있으므로, Notion API/MCP 문서의 제한을 확인하는 것이 좋음.

---

## 5. 스키마 요약 (타입만)

```
data: {
  page_id: string;   // required
  command: "update_properties" | "replace_content" | "replace_content_range" | "insert_content_after";
  // 아래는 command 에 따라 하나만 사용
  properties?: object;                    // update_properties
  new_str?: string;                      // replace_content, replace_content_range, insert_content_after
  selection_with_ellipsis?: string;      // replace_content_range, insert_content_after
  allow_deleting_content?: boolean;      // replace_content, replace_content_range
}
```
