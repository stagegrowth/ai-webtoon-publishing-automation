<!-- 목적: Notion과 로컬 문서 동기화 방법. 어떤 파일을 어디에 복사·업데이트할지, 수동 복사 절차를 안내합니다. -->
# Notion 복사·업데이트 가이드

Notion에 **어떤 파일을**, **어디에** 복사·업데이트하면 되는지 정리한 문서입니다.

---

## 1. 복사·업데이트 대상 요약

| 용도 | 로컬 파일 | Notion 대상 | 방법 |
|------|------------|-------------|------|
| **PRD (질문형)** | `prd_for_notion_insert.md` | PRD 페이지 본문 | 본문 전체 교체 또는 수동 복사 |
| **고객 확인 체크리스트 (회사별)** | `asis/customer_checklist_by_company.md` | Notion "고객 확인 체크리스트 (회사별)" + COMPANY-A/B/C As-Is Flow 페이지 | 수동 복사 |
| **02_Exceptions & Rework** | `asis/exceptions_and_rework.md` | Notion "02_Exceptions & Rework" 페이지 (예외·재작업 회사별 현황) | 수동 복사 |
| **03_Automation Boundary & Decisions** | `asis/automation_boundary_and_decisions.md` | Notion "03_Automation Boundary & Decisions" 페이지 (자동화 경계·Open Questions·참고 링크) | 수동 복사 |

---

## 2. 파일별 상세

### 2.1 PRD 페이지 업데이트

- **로컬 파일**: `docs/prd_for_notion_insert.md`
- **Notion 페이지**: PRD - ai-webtoon-publishing-automation (Q&A)  
  - **page_id**: `3069f1bf-3171-807f-abde-d74c9bb72f47`  
  - **URL**: `sources.md` 참고 (또는 Notion에서 해당 페이지 링크 복사)
- **업데이트 방법**
  - **수동**: `prd_for_notion_insert.md` 내용을 열어서 Notion PRD 페이지 본문에 붙여넣기(기존 본문 덮어쓰기).
  - **MCP/API**: Notion MCP `replace_content` 사용 시, `new_str`에 `prd_for_notion_insert.md` **전체 텍스트**를 넣고, 해당 `page_id`로 요청. (자세한 스키마는 `NOTION_UPDATE_PAGE_SCHEMA.md` 참고.)

### 2.2 고객 확인 체크리스트 (회사별) — Notion 동기화 원본

- **로컬 파일**: `docs/asis/customer_checklist_by_company.md`
- **Notion 대상**: "고객 확인 체크리스트 (회사별)" 제목으로 관리하던 페이지. 상단 체크리스트 + **COMPANY-A/B/C As-Is Flow** 표.
- **설명**: 회사별 As-Is Flow(로그인 → 워크스페이스 선택 → 작품 생성/선택 → 에피소드 메타 → 원고 업로드 → 원고 검증 → 콘티(선택) → 에셋 첨부 → 최종 검토 → 플랫폼 선택 → 발행 요청 → 결과 확인). 체크리스트에서 참조하는 **예외표**는 `asis/exceptions_and_rework.md`(Notion 02_Exceptions & Rework), **자동화 경계**는 `asis/automation_boundary_and_decisions.md`(Notion 03_Automation Boundary & Decisions)에서 관리합니다.
- **업데이트 방법**: 이 파일을 수정한 뒤 Notion 해당 페이지에 수동 복사·붙여넣기.

### 2.3 02_Exceptions & Rework — Notion 동기화 원본

- **로컬 파일**: `docs/asis/exceptions_and_rework.md`
- **Notion 대상**: "02_Exceptions & Rework" 제목의 페이지. EX-01~05별 COMPANY-A/B/C 발생 Step·빈도·심각도·현재 처리·자동화 희망·결정 옵션 표.
- **설명**: 예외(Validation, Upload, Duplicate, Permission, Downstream) 회사별 현황. 고객 확인 체크리스트에서 "Page B 예외표"로 링크되는 내용의 원본입니다.
- **업데이트 방법**: 이 파일을 수정한 뒤 Notion 해당 페이지에 수동 복사·붙여넣기.

### 2.4 03_Automation Boundary & Decisions — Notion 동기화 원본

- **로컬 파일**: `docs/asis/automation_boundary_and_decisions.md`
- **Notion 대상**: "03_Automation Boundary & Decisions" 제목의 페이지. COMPANY-A/B/C 자동화 경계(완전 자동/반자동/제외 후보) 표 + Open Questions(10개) + Links/Artifacts.
- **설명**: 회사별 Step ID별 자동화 경계 후보·결정 옵션, 트리거·알림·중복 정책 등 Open Questions, 참고 문서(시퀀스·Step Catalog·Data Contract·As-Is 요약) 링크. 고객 확인 체크리스트에서 "자동화 경계"로 참조하는 내용의 원본입니다.
- **업데이트 방법**: 이 파일을 수정한 뒤 Notion 해당 페이지에 수동 복사·붙여넣기.

### 2.5 As-Is 요약 (업체별) — 참고용

- **로컬 파일**: `docs/asis_summary_for_customer_multi_company.md`
- **용도**: **참고용 요약본**. 업로드 중심 5단계(ASIS-01~05) + **예외 비교 표(EX-01~05)** + **자동화 경계 표**를 한 문서에 담은 축약 버전입니다. Notion 동기화 대상이 아니며, 상세 회사별 Flow는 `asis/customer_checklist_by_company.md`를 사용합니다.
- **유지 이유**: 고객에게 "한 페이지 요약"으로 보낼 때, 또는 예외·자동화 경계를 한눈에 볼 때 활용 가능. 상세 예외표는 `asis/exceptions_and_rework.md`, 상세 자동화 경계는 `asis/automation_boundary_and_decisions.md`(Notion 03_Automation Boundary & Decisions) 참고.

---

## 3. 그 외 참고용 파일 (복사 필수 아님)

| 파일 | 용도 |
|------|------|
| `sources.md` | Notion 페이지 ID, URL, sync_mode 등 소스 등록 정보 |
| `NOTION_UPDATE_PAGE_SCHEMA.md` | Notion MCP/API로 본문 교체할 때 파라미터 스키마 |
| `payload_for_update.json` | replace_content 호출 시 payload 예시 |
| `prd_for_notion_insert.md` | PRD 페이지 본문 원본 (위 2.1에서 사용) |

---

## 4. 체크리스트 (Notion 반영 시)

- [ ] **PRD 페이지**: `prd_for_notion_insert.md` 내용으로 본문 갱신했는지 확인
- [ ] **고객 확인 체크리스트 (회사별)**: `asis/customer_checklist_by_company.md` 내용을 Notion 해당 페이지에 반영했는지 확인
- [ ] **02_Exceptions & Rework**: `asis/exceptions_and_rework.md` 내용을 Notion 해당 페이지에 반영했는지 확인
- [ ] **03_Automation Boundary & Decisions**: `asis/automation_boundary_and_decisions.md` 내용을 Notion 해당 페이지에 반영했는지 확인
- [ ] (선택) `sources.md`에 As-Is·체크리스트·예외표·자동화 경계 페이지 URL/ID 기록
