# notion-update-page 실행 결과 요약

## 1. 실행 절차

1. **파일 읽기**: `.taskmaster/docs/notion/prd_for_notion_insert.md` 를 UTF-8로 전체 읽음 → 변수 `content` (문자 수 3918)
2. **payload**: `{ "data": { "page_id": "3069f1bf-3171-807f-abde-d74c9bb72f47", "command": "replace_content", "new_str": content, "allow_deleting_content": true } }`
3. **호출**: Notion MCP `notion-update-page` 호출

---

## 2. 호출 결과: **실패**

- **성공 여부**: 실패
- **오류 메시지 전문**:
  ```
  MCP error -32602: Invalid arguments for tool notion-update-page: [
    { "code": "invalid_type", "expected": "object", "received": "string", "path": ["data"], "message": "Expected object, received string" },
    ... (unionErrors 동일 내용 반복)
    "message": "Invalid input"
  ]
  ```

---

## 3. 검증 (성공 시)

- 본문에 "0. Change Log" 포함 여부: **미실행** (실패로 인해)
- `<empty-block/>` 사라짐 여부: **미실행**

---

## 4. 실패 시 분석용 정보

| 항목 | 값 |
|------|-----|
| **content 문자 수 (length)** | 3918 |
| **사용한 command 값** | `replace_content` |
| **에러 메시지 전문** | `Invalid arguments for tool notion-update-page` — `data` path에서 `Expected object, received string` |

### content 첫 200자 (민감정보 제거)

```
# 0. Change Log

| 날짜 | 버전 | 변경 내용 | 작성자 |
|------|------|-----------|--------|
| [YYYY-MM-DD] | v0.1 | 초안 작성 | [이름] |

---

# 1. Goal / One-liner

## 1.1 어떤 문제를 해결하나요?

❓ **질문:** 현재 사용자가 겪는 핵심 문제는 무엇인가요?
```

### content 마지막 200자 (민감정보 제거)

```
## 6.4 가정 (Assumptions)

❓ **질문:** 전제 조건/가정은?

- [                          ]

---

# 7. References

- 관련 링크: [Notion/Jira/GitHub 등]
- PM: [이름 / 날짜]
- Engineering: [이름 / 날짜]
- Design: [이름 / 날짜]
- 기타: [이름 / 날짜]
```

---

## 5. 원인 분석

- MCP 클라이언트(Cursor/호출 측)가 `data` 파라미터를 **객체가 아닌 문자열**로 직렬화하여 전달함.
- Notion MCP 서버는 `data`를 **object**로 기대하므로 `invalid_type` (Expected object, received string) 발생.
- 따라서 **호출 측에서 `data`를 반드시 객체로 직렬화**해 보내야 함. (JSON 객체로 전달하거나, 클라이언트 SDK 사용 시 객체 그대로 전달)

---

## 6. 권장 대안

1. **Notion API 직접 호출**: `PATCH /v1/blocks/{block_id}` 또는 블록 children API로 본문 블록을 교체하는 스크립트 작성 (Node/Python).
2. **MCP 호출 환경 변경**: `data`를 JSON 객체로 넘길 수 있는 클라이언트/스크립트에서 동일 payload로 `notion-update-page` 호출.
3. **수동 반영**: `prd_for_notion_insert.md` 내용을 복사한 뒤 Notion 페이지 본문에 붙여넣기.
