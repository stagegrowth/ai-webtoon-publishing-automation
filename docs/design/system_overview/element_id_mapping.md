# 요소 ID 맵핑 (Element ID Mapping) 정의

## 1. 목적
등록 대상 **플랫폼 웹의 요소 ID(또는 셀렉터)**가 변경되어도 **고객 운영팀**이 **변경된 요소 ID를 직접 수정**하여 시스템을 복구·유지할 수 있도록, 맵핑 요구사항·데이터 구조·사용자 인터페이스를 정의합니다.

---

## 2. 요구사항

| 구분 | 내용 |
|------|------|
| **비즈니스 요구** | 플랫폼 웹에서 각 요소의 ID(또는 CSS 셀렉터·XPath 등)가 변경되어도, **개발자 수정·배포 없이** 고객 운영팀이 **변경된 요소 ID를 등록·수정**하여 자동화가 계속 동작하도록 한다. |
| **사용자** | **고객 운영팀**(개발자가 아닌 사용자)이 맵핑 정보를 등록·수정할 수 있어야 한다. |
| **인터페이스** | 각 데이터(등록 대상 요소)별로 **등록되어야 할 ID 맵핑 정보**를 **사용자 입장에서 등록·수정할 수 있는 UI**가 있어야 한다. |
| **데이터 구조** | 맵핑 데이터는 **사용자가 이해·편집 가능한 구조**로 저장되며, Platform Integration(Adapter)은 이 맵핑을 읽어 플랫폼 웹 요소를 찾는다. |

---

## 3. 맵핑 데이터 구조

### 3.1 개념

- **플랫폼**별로, **등록 단계** 내 **요소(필드·버튼·영역 등)**마다 **어떤 셀렉터로 찾을지**를 한 건씩 저장한다.
- 요소는 **논리적 이름(역할)**로 구분하고, 그에 대응하는 **셀렉터 타입·값**을 저장한다. 플랫폼 웹이 바뀌면 **값만** 수정하면 된다.

### 3.2 맵핑 레코드 (1건)

| 필드 | 타입 | 필수 | 설명 |
|------|------|------|------|
| **platform_id** | string | Y | 플랫폼 식별자 (예: kakao, naver, ridi, kyobo). |
| **step_key** | string | Y | 등록 단계 논리명 (예: login, meta_input, file_upload, submit). UI에서 단계별로 그룹 표시용. |
| **element_key** | string | Y | 요소 논리명 (예: login_id_input, submit_button, title_field). 시스템이 참조하는 키이자 사용자에게 표시되는 이름. |
| **element_label** | string | N | 사용자 표시용 라벨 (예: "로그인 아이디 입력란", "제출 버튼"). UI에서 한글 등으로 보여줄 수 있음. |
| **selector_type** | enum | Y | 셀렉터 종류: `id` \| `css_selector` \| `xpath` \| 기타(확장). |
| **selector_value** | string | Y | 실제 셀렉터 값 (예: `#user-id`, `input[name="title"]`, `//button[@type='submit']`). **고객이 변경 시 이 값만 수정.** |
| **remarks** | string | N | 비고 (어디서 쓰이는지, 변경 이력 등). |

**제약**: (platform_id, step_key, element_key) 조합은 유일(Unique).

### 3.3 데이터 구조 예시 (JSON)

```json
{
  "mappings": [
    {
      "platform_id": "kakao",
      "step_key": "login",
      "element_key": "login_id_input",
      "element_label": "로그인 아이디 입력란",
      "selector_type": "id",
      "selector_value": "login-email",
      "remarks": ""
    },
    {
      "platform_id": "kakao",
      "step_key": "login",
      "element_key": "login_password_input",
      "element_label": "로그인 비밀번호 입력란",
      "selector_type": "id",
      "selector_value": "login-password",
      "remarks": ""
    },
    {
      "platform_id": "kakao",
      "step_key": "meta_input",
      "element_key": "title_field",
      "element_label": "제목 입력 필드",
      "selector_type": "css_selector",
      "selector_value": "input[name=\"title\"]",
      "remarks": ""
    }
  ]
}
```

### 3.4 플랫폼 Adapter와의 관계

- **Platform Integration** 내 각 플랫폼 Adapter는 **등록 단계 수행 시** 이 맵핑을 **Config & State(또는 전용 맵핑 저장소)**에서 **읽어** 사용한다.
- Adapter는 `(platform_id, step_key, element_key)`로 레코드를 조회하고, `selector_type`과 `selector_value`로 해당 플랫폼 웹에서 요소를 찾는다.
- **코드에는 요소 키만** 참조하고, **실제 셀렉터 값은 맵핑 데이터에서만** 가져오므로, 플랫폼 웹 ID 변경 시 **맵핑 데이터만 수정**하면 된다.

---

## 4. 사용자 인터페이스 요구사항

| 구분 | 내용 |
|------|------|
| **대상 사용자** | 고객 운영팀 (개발자 아님). |
| **기능** | 플랫폼별·단계별 **요소 ID(셀렉터) 맵핑**의 **등록·조회·수정·삭제**. |
| **표시** | 플랫폼 선택 → 단계(step_key)별 그룹 → 요소(element_key·element_label)별로 현재 selector_type·selector_value 표시. |
| **편집** | 요소별로 selector_type·selector_value 수정. 변경된 플랫폼 웹에서 새 ID/셀렉터를 확인한 후, 해당 값만 입력·저장하면 다음 실행부터 반영. |
| **검증** | (선택) 저장 전 또는 테스트 모드에서 셀렉터 유효성 검사(해당 플랫폼 페이지에서 요소 존재 여부 확인)를 제공할 수 있음. |

---

## 5. 요약

| 항목 | 정의 |
|------|------|
| **요구사항** | 플랫폼 웹 요소 ID 변경 시 고객 운영팀이 변경된 요소 ID를 수정하여 시스템을 수정할 수 있어야 함. |
| **맵핑 데이터** | 플랫폼·단계·요소(논리 키)별로 selector_type·selector_value를 저장; 사용자 편집 가능 구조. |
| **인터페이스** | 개발자가 아닌 사용자(고객 운영팀)가 맵핑을 등록·수정할 수 있는 UI 필요. |
| **저장·사용** | 맵핑은 Config & State(또는 전용 저장소)에 두고, Platform Integration(Adapter)이 실행 시 읽어 사용. |

---

## 6. 관련 문서
| 문서 | 설명 |
|------|------|
| [structure_view.md](../top_level_view/structure_view.md) | Config & State·Platform Integration과 맵핑의 관계 (§8 요소 ID 변경) |
| [system_feature_list.md](system_feature_list.md) | 요소 ID 맵핑 UI 기능 (F-CONFIG-05) |
| [quality_attributes.md](../architecture_driver/quality_attributes.md) | 변경용이성(요소 ID 변경 시 고객 직접 수정) |
| [use_case_list.md](../architecture_driver/use_case_list.md) | UC-07b 요소 ID(셀렉터) 맵핑 등록·수정 |
