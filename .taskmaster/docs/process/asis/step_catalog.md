# Step Catalog (As-Is)

| Step ID | Step Name | Automation Type | Priority | Evidence Timestamp | 비고 |
|---------|-----------|-----------------|----------|--------------------|------|
| S1 | [      ] | [ ] Manual [ ] Semi [ ] Full | [ ] High [ ] Mid [ ] Low | [YYYY-MM-DD HH:MM] | [      ] |
| S2 | [      ] | [ ] Manual [ ] Semi [ ] Full | [ ] High [ ] Mid [ ] Low | [YYYY-MM-DD HH:MM] | [      ] |
| S3 | [      ] | [ ] Manual [ ] Semi [ ] Full | [ ] High [ ] Mid [ ] Low | [YYYY-MM-DD HH:MM] | [      ] |
| … | | | | | |

**Automation Type**: Manual = 전부 수동, Semi = 일부 자동, Full = 완전 자동

---

## Step ID Naming Rules

- **메인 스텝**: `ASIS-01`, `ASIS-02`, … 순번 부여 (2자리 권장).
- **하위 스텝**: 같은 논리 단계 내 세부 동작은 `ASIS-04a`, `ASIS-04b` 형태로 부여.
- **규칙 요약**:
  - `ASIS-NN` : N번째 메인 단계
  - `ASIS-NNa` / `ASIS-NNb` : N번째 단계의 첫 번째/두 번째 하위 액션
- **예시**: 로그인 → 프로젝트 선택 → 에피소드 등록 시  
  `ASIS-01` 로그인, `ASIS-02` 프로젝트 선택, `ASIS-03a` 메타 입력, `ASIS-03b` 파일 업로드

---

## Step Fields Definition

각 Step을 채울 때 아래 필드를 기준으로 하면 시퀀스·데이터 계약·자동화 후보와 연결하기 쉽습니다.

| 필드 | 설명 | 예시 |
|------|------|------|
| **UI Location** | 화면/메뉴/URL 경로 | 대시보드 > 작품 관리 > 신규 등록 |
| **Action** | 사용자/시스템이 수행하는 동작 | "에피소드 제목 입력 후 저장 클릭" |
| **Input Source** | 데이터 출처 (폼/파일/API/복사) | 폼 입력, 로컬 파일 선택 |
| **Validation Rules** | 사전 검증 규칙 | 필수 필드, 파일 확장자/용량 |
| **Output Artifact** | 생성·변경되는 결과물 | DB 행, 업로드된 파일 경로 |
| **Failure Modes** | 실패 유형 (선택) | validation 실패, 업로드 타임아웃 |

### Automation Type 결정 기준

| 타입 | 기준 | 예시 |
|------|------|------|
| **MANUAL** | 전 과정 수동, 자동화 없음 | 수동 로그인, 수동 파일 선택 |
| **SEMI** | 일부만 자동(기본값 채움/검증/알림 등) | 템플릿 적용, 클라이언트 검증 |
| **FULL** | 트리거만 주면 끝까지 자동 | 스케줄/이벤트 기반 일괄 업로드 |

---

## Traceability

Step Catalog ↔ 시퀀스·데이터 계약·고객 Q&A 연결표. 시퀀스에서 사용한 ASIS Step ID 기준으로 연결합니다.

| Step ID | sequence_happy.puml 위치(섹션) | data_contract 섹션 | customer Q&A 질문 ID(optional) |
|---------|--------------------------------|--------------------|--------------------------------|
| ASIS-00 | Preconditions | (사전 조건) | [      ] |
| ASIS-01 | 로그인/세션 | (세션·사용자) | [      ] |
| ASIS-02 | 워크스페이스·프로젝트 선택 | Work 엔티티 (workspace/project) | [      ] |
| ASIS-03 | 작품(Work) 생성/선택 | Work 엔티티 | [      ] |
| ASIS-04a | 에피소드(Item) 메타 입력 | Item 엔티티, 메타 필드 | [      ] |
| ASIS-04b | 에피소드 원고/파일 업로드 | Asset 엔티티, 파일 규격 | [      ] |
| ASIS-05 | 원고 검증(클라이언트/서버) | Validation Rules, Outputs | [      ] |
| ASIS-06 | 콘티/구조화 (선택) | Item 엔티티, 메타 필드 | [      ] |
| ASIS-07 | 에셋(이미지/메타) 생성·첨부 | Asset 엔티티, 파일 규격 | [      ] |
| ASIS-08 | 최종 검토·수정 | Item/Asset, 메타 필드 | [      ] |
| ASIS-09 | 플랫폼 대상 선택·설정 | (설정/플랫폼) | [      ] |
| ASIS-10 | 업로드/발행 요청 | Outputs, Validation Rules | [      ] |
| ASIS-11 | 발행 결과 확인 | Outputs | [      ] |
| ASIS-12 | Post-Check/완료 | (검증 결과) | [      ] |

---

## Fill-in Worksheet (from recording)

녹화/시연을 보면서 타임스탬프와 액션을 빠르게 적을 때 사용하는 워크시트. 채운 뒤 위 Step Catalog 표와 Traceability에 Step ID를 부여해 반영합니다.

| Timestamp | Actor | Screen | Action | Inputs | Outputs | Notes | Step ID 후보 |
|-----------|-------|--------|--------|--------|---------|-------|--------------|
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |
| [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] | [      ] |

---

## Step Catalog placeholder 채우기를 위한 최소 입력 체크리스트

아래가 있으면 Step Catalog를 **최소한으로** 채울 수 있습니다.

- [ ] **타임라인 12개 핵심 순간**  
  (evidence.md Timeline 또는 녹화 요약에서 "순서 / 시각 / 액터 / 액션 / 결과물"만 있어도 Step ID 후보와 매핑 가능)
- [ ] **화면/메뉴 이름**  
  (각 순간이 어떤 화면에서 일어났는지 → UI Location, sequence_happy.puml 섹션 이름 후보)
- [ ] **입력·결과물**  
  (무엇을 넣었고 무엇이 나왔는지 → data_contract 매핑, Output Artifact)
- [ ] **수동/자동 구분**  
  (해당 단계가 전부 수동인지, 일부 자동이 있는지 → Automation Type)
- [ ] **우선순위 1개 이상**  
  (가장 자동화하고 싶은 Step 1개만 정해도 Priority 컬럼 채움 가능)

**권장 순서**: (1) Fill-in Worksheet에 타임스탬프·액터·액션만 먼저 채우기 → (2) Step ID 후보 부여 → (3) 상단 Step Catalog 표와 Traceability에 반영 → (4) data_contract / sequence_happy.puml 섹션명 연결.
