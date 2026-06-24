# QTI 3.0 예제 (1EdTech)

교육·평가 업계 표준 QTI 3.0의 문항(`AssessmentItem`) 예제. XML 바인딩이라 무겁지만
타입 분류·채점 모델의 참고서로 활용.

- `choice-interaction.qti.xml` — QTI 3.0 Beginner's Guide의 **공식 단일선택 객관식 예제**(verbatim).

## 공식 스키마 정의(XSD)

QTI 3.0 공식 스키마 파일은 용량이 커서(ASI XSD 약 18MB) 저장소에 포함하지 않는다.
필요하면 아래 URL에서 받는다. `choice-interaction.qti.xml`의 `xsi:schemaLocation`이 가리키는 XSD다.

- ASI (Assessment, Section, Item): `https://purl.imsglobal.org/spec/qti/v3p0/schema/xsd/imsqti_asiv3p0p1_v1p0.xsd`

이 ASI XSD는 아래 외부 표준 스키마를 절대 URL로 import한다(검증기가 온라인에서 자동 해석).

- MathML3: `https://purl.imsglobal.org/spec/mathml/v3p0/schema/xsd/mathml3.xsd`
- SSML 1.1: `https://purl.imsglobal.org/spec/ssml/v1p1/schema/xsd/ssmlv1p1-core.xsd`
- XInclude: `https://purl.imsglobal.org/spec/w3/2001/schema/xsd/XInclude.xsd`
- XML: `https://purl.imsglobal.org/spec/w3/2001/schema/xsd/xml.xsd`

QTI 3.0에는 ASI 외에 메타데이터·결과리포팅(Results) 등 별도 XSD도 있다.
전체 목록은 아래 standards index 참고.

## 구조 매핑 (미쁨 스키마 관점)

| QTI 요소 | 의미 | 미쁨 스키마 대응 |
| --- | --- | --- |
| `qti-assessment-item` | 한 문항 | 퀴즈 객체 1개 |
| `qti-item-body > p` | 질문 | `question` |
| `qti-choice-interaction` | 객관식 상호작용 | `type: MultipleChoice` |
| `qti-simple-choice` | 보기 | `options[]` |
| `qti-response-declaration > qti-correct-response` | 정답(보기 id) | `answer` |
| `qti-outcome-declaration (SCORE)` | 배점 | (현재 없음 — 도입 검토) |
| `qti-response-processing` | 채점 로직 | (현재 없음) |

## 타입 매핑 (미쁨 type ↔ QTI interaction)

> 핵심 설계 차이: 미쁨은 **타입 1개 = 형식 1개**지만, QTI는 **interaction 종류 + 정답 선언
> (`cardinality`/`base-type`)** 을 분리한다. 그래서 미쁨의 `MultipleChoice`와 `Checkbox`는
> QTI에선 **같은 `qti-choice-interaction`**이며, 단일/복수는 `max-choices`·`cardinality`로 구분한다.

| 미쁨 타입 | QTI interaction | 구분/대응 방식 |
| --- | --- | --- |
| `MultipleChoice` | `qti-choice-interaction` | `max-choices="1"`, cardinality **single** |
| `Checkbox` | `qti-choice-interaction` | `max-choices>1`, cardinality **multiple** |
| `ShortAnswer` | `qti-text-entry-interaction` | base-type `string`(=`answerType: Text`) / `integer`·`float`(=`Number`) |
| `FreeAnswer` | `qti-extended-text-interaction` | 장문, 정답 채점 없음(피드백만) |

→ 미쁨 4개 타입은 QTI의 **3개 interaction**(choice / text-entry / extended-text)으로 모두 표현된다.

## QTI에 더 있는 interaction (미쁨에 없음)

QTI 3.0의 interaction은 XSD 기준 **총 22종**. 미쁨이 쓰는 3종을 뺀 **19종**을 성격별로 정리.
(`← ...`는 Google Forms 견본 `ref/google_form/`의 대응 타입)

| 계열 | QTI interaction | 설명 |
| --- | --- | --- |
| 선택·순서 | `qti-inline-choice-interaction` | 드롭다운(인라인 선택) ← 드롭다운 |
| | `qti-order-interaction` | 보기 순서 배열 |
| | `qti-hottext-interaction` | 본문 속 단어/구절 선택 |
| 연결·매칭 | `qti-match-interaction` | 짝 맞추기/그리드 ← 객관식·체크박스 그리드 |
| | `qti-associate-interaction` | 항목끼리 연결 |
| | `qti-gap-match-interaction` | 빈칸에 보기 끌어다 넣기 |
| 수치·범위 | `qti-slider-interaction` | 슬라이더 ← 선형 배율 / 등급 |
| 파일·미디어 | `qti-upload-interaction` | 파일 업로드 ← 파일 업로드 |
| | `qti-media-interaction` | 미디어 재생 기반 응답 |
| 그래픽 | `qti-hotspot-interaction` | 이미지 영역 클릭 |
| | `qti-select-point-interaction` | 이미지 좌표 선택 |
| | `qti-position-object-interaction` | 이미지 위 객체 배치 |
| | `qti-graphic-order-interaction` | 이미지 위 순서 |
| | `qti-graphic-associate-interaction` | 이미지 위 연결 |
| | `qti-graphic-gap-match-interaction` | 이미지 위 빈칸 매칭 |
| | `qti-drawing-interaction` | 직접 그리기 |
| 기타·확장 | `qti-end-attempt-interaction` | 제출/힌트 제어 버튼 |
| | `qti-custom-interaction` | 커스텀 상호작용 |
| | `qti-portable-custom-interaction` | 이식 가능한 커스텀(PCI) |

### 미쁨 확장 후보 (Google Form 견본 종합)

| 추가 후보 미쁨 타입 | 대응 QTI | 대응 Google Form |
| --- | --- | --- |
| `Dropdown` | inline-choice | 드롭다운 |
| `Grid` | match | 객관식/체크박스 그리드 |
| `Scale` / `Rating` | slider | 선형 배율 / 등급 |
| `FileUpload` | upload | 파일 업로드 |
| `Date` / `Time` | (QTI엔 별도 없음, text-entry + 포맷) | 날짜 / 시간 |

> `Dropdown`은 데이터상 `MultipleChoice`와 동일하므로 별도 타입보다 `display: "dropdown"` 옵션으로
> 흡수하는 편이 깔끔할 수 있다. 그래픽 계열 7종은 미쁨 용도엔 과한 편.

## 출처

- QTI 3.0 Overview: https://www.imsglobal.org/spec/qti/v3p0/oview
- QTI 3 Beginner's Guide: https://www.imsglobal.org/spec/qti/v3p0/guide
- QTI standards index: https://www.1edtech.org/standards/qti
