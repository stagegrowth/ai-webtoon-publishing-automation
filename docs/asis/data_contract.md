<!-- 목적: As-Is 자동화의 입출력·스키마·Step 매핑 등 데이터 계약 정의. 문서를 열면 용도를 바로 확인할 수 있습니다. -->
# Data Contract (As-Is)

## Work / Item / Asset 엔티티

| 엔티티 | 설명 | 주요 식별자 |
|--------|------|-------------|
| Work | [작업/프로젝트 단위] | [id / key] |
| Item | [에피소드/단위 항목] | [id / key] |
| Asset | [파일/리소스] | [id / path] |

## 메타 필드

| 필드명 | 타입 | 필수 | 설명 |
|--------|------|------|------|
| [      ] | [string/number/date] | [ ] Y [ ] N | [      ] |
| [      ] | [      ] | [ ] Y [ ] N | [      ] |

## 파일 규격

| 항목 | 규격 |
|------|------|
| 허용 확장자 | [      ] |
| 최대 크기 | [      ] |
| 인코딩 | [      ] |

## Validation Rules

- [                          ]
- [                          ]

## Outputs

| 출력 | 형식 | 용도 |
|------|------|------|
| [      ] | [      ] | [      ] |

---

## Step-to-Data Mapping

Step ID와 입·출력 데이터의 대응 관계. 시퀀스(sequence_happy.puml)의 ASIS Step과 연계한다.

**현행 플로우**: Excel(웹툰 목록·메타) + 별도 수령 웹툰 파일 → 업체별 로그인 → 업로드 화면 → (웹툰별) 메타 복사·입력 → 웹툰 파일 등록.

| Step ID | Data inputs | Data outputs | Notes |
|---------|--------------|--------------|-------|
| ASIS-00 | (사전 준비) | — | Excel(웹툰 목록·메타), 웹툰 파일(별도 전달), 대상 업체 목록 |
| ASIS-01 | credentials (id, password) | session_id, 쿠키/세션 | 업체 웹페이지 로그인; 자동화 시 계정·세션 정책에 따름 |
| ASIS-02 | (메뉴 이동) | 업로드 화면 URL/상태 | 업로드(등록) 화면 진입; 업체마다 UX 상이 |
| ASIS-03 | Excel 행(메타: 제목, 요약 등) | 폼 입력 완료 상태 | Excel에서 복사한 메타를 업체 폼에 입력; 업체별 필수 필드 |
| ASIS-04 | 웹툰 파일(경로/바이너리), 해당 웹툰 식별 | 업로드 완료, 플랫폼 내 URL/상태 | 해당 웹툰에 맞는 파일 1건 등록; 확장자·용량 검증 |
| ASIS-05 | (반복 제어) | 다음 웹툰/다음 업체 | Excel 다음 행 또는 다음 업체; 결과 확인(선택) |

---

## Automation Input Package v0

자동화 실행 시 제공되는 입력 패키지의 디렉토리 구조와 최소 스키마 초안. 고객 확정 전이므로 enum 등은 placeholder로 둔다.

### 디렉토리 구조 (예시)

```
input/
├── work.yaml              # 작품(Work) 메타 1건
├── episodes/
│   ├── ep01/
│   │   ├── episode.yaml   # 에피소드(Item) 메타
│   │   └── assets/        # 해당 에피소드 소속 에셋
│   │       ├── *.png
│   │       └── (기타 허용 확장자)
│   ├── ep02/
│   │   ├── episode.yaml
│   │   └── assets/
│   └── ...
└── (선택) shared/         # 작품 공통 에셋
    └── *.png
```

### work.yaml 스키마 (최소)

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| title | string | Y | 작품 제목 |
| description | string | N | 작품 설명 |
| tags | string[] | N | 태그 목록 (placeholder) |
| category | string | N | 장르/카테고리 (enum placeholder) |
| language | string | N | 언어 코드 (예: ko, en) |
| publish_targets | string[] | N | 발행 대상 플랫폼 ID 목록 (placeholder) |
| (기타) | — | N | 확장 필드는 추후 정의 |

### episode.yaml 스키마 (최소)

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| item_no | number | Y | 에피소드 번호 (순서, 중복 불가) |
| title | string | Y | 에피소드 제목 |
| summary | string | N | 요약/설명 |
| visibility | string | N | 공개 여부 (enum placeholder) |
| asset_list | string[] | N | 해당 폴더 내 파일명 또는 상대 경로 매핑 (없으면 assets/ 내 전체) |
| (기타) | — | N | 확장 필드 추후 정의 |

### 검증 규칙 (5개 이상)

| # | 규칙 | 설명 |
|---|------|------|
| 1 | **파일명 규칙** | 에피소드 폴더명은 `epNN` 형태 권장 (NN: 2자리 이상). 특수문자·공백 미사용. |
| 2 | **중복 금지** | 동일 work 내 `item_no` 중복 불가. 동일 episode 폴더 내 동일 파일명 중복 불가. |
| 3 | **포맷** | `work.yaml`, `episode.yaml`은 UTF-8, YAML 1.2 유효 문법. JSON 허용 시 별도 명시. |
| 4 | **확장자·용량** | 에셋 허용 확장자: placeholder (예: png, jpg, webp). 파일당 최대 크기: placeholder (예: 50MB). |
| 5 | **필수 파일** | `input/work.yaml` 필수. 에피소드 사용 시 `episodes/<epNN>/episode.yaml` 필수. |
| 6 | **참조 무결성** | `asset_list`에 기재된 파일은 해당 에피소드의 `assets/` 하위에 존재해야 함. |

---

## Error Codes & Retry Policy (Draft)

자동화 시 예상 오류와 재시도·사용자 액션 가이드. sequence_exceptions.puml(EX-01~05)과 대응.

| 오류 유형 | 대표 코드/상황 | 재시도 가능 | 사용자 액션 가이드 |
|-----------|----------------|-------------|---------------------|
| validation | 4xx, validation_report.errors | 보통 불가 (입력 수정 필요) | 입력 포맷·필수 필드·파일 규격 수정 후 **재실행** |
| upload | 5xx, timeout | 가능 (일시적 장애 가정) | 재시도 횟수 초과 시 파일 분할·용량 축소 후 **재실행** |
| conflict | 409 Duplicate | 정책에 따라 (덮어쓰기 옵션 등) | 중복 스킵/덮어쓰기 **승인** 또는 식별자 수정 후 **재실행** |
| permission | 403 Forbidden | 불가 (권한 변경 필요) | 계정/역할·리소스 권한 확인 후 **승인·재설정** 및 **재실행** |
| downstream | 5xx, partial failure | 제한적 (외부 복구 후) | 실패 항목 확인 → 외부 서비스 복구 또는 **수동 개입** 후 **재실행** |

- **재시도 가능**: 자동 재시도 시 백오프(예: exponential backoff) 적용 권장.
- **사용자 액션**: 수정 = 입력 데이터/설정 변경, 승인 = 정책 선택·권한 부여, 재실행 = 동일/수정된 입력으로 파이프라인 다시 실행.
