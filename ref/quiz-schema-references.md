# 퀴즈 스키마 참고 표준 (외부)

미쁨 퀴즈 스키마를 설계할 때 참고할 수 있는 기존 표준/포맷 정리.
"가벼운 자체 JSON 스키마 + 채점 동작"이라는 미쁨 퀴즈의 목표를 기준으로 평가했다.

실제 정의·예제 파일:
- `schema_org/` — schema.org Quiz/Question/Answer JSON-LD 예제 + 매핑표
- `qti/` — QTI 3.0 AssessmentItem XML 예제 + 매핑표
- `google_form/` — Google Forms 모든 문항 타입 견본(HTML)

## 1. schema.org `Quiz` / `Question` / `Answer`

가장 가볍고 JSON(-LD) 기반이라 현재 스키마와 가장 가깝다.

- **구조**: `Quiz`(LearningResource 하위) → `hasPart`로 `Question` 배열 → 각 `Question`은
  `eduQuestionType`, `acceptedAnswer`(정답 `Answer`), `suggestedAnswer`(보기들) 보유.
- **`eduQuestionType`** 값 예시: `"Multiple choice"`, `"Open ended"`, `"Flashcard"`
  → 현재 스키마의 `type`과 같은 역할.
- **한계**: 본래 검색 노출(SEO)·콘텐츠 발견용 *어휘*라서, 채점/배점/오답 피드백/그리드 같은
  *작동(behavior)* 데이터는 표준화돼 있지 않음. Google이 실제 지원하는 건 사실상 Flashcard 정도.
- **활용법**: 자체 스키마(작동용)는 유지하되 필드 *이름*을 schema.org에 맞추면
  (`acceptedAnswer`, `eduQuestionType` 등) 나중에 JSON-LD export로 SEO 이점을 얻기 쉽다.

링크:
- https://schema.org/Quiz
- https://schema.org/Question
- https://schema.org/Answer
- https://schema.org/eduQuestionType
- Google Education Q&A structured data: https://developers.google.com/search/docs/appearance/structured-data/education-qa

## 2. QTI 3.0 (1EdTech, 구 IMS Global)

교육·평가 업계의 사실상 표준. 가장 포괄적.

- `AssessmentItem`(문항) / `AssessmentTest`(시험지) / 결과 리포트까지 모델링.
  `choiceInteraction`, `textEntryInteraction`, `matchInteraction`(그리드류) 등
  google form에서 발견한 12개 타입을 거의 다 커버.
- **한계**: 인증용 바인딩이 XML이라 무겁고 JSON 친화적이지 않음. 풀 도입은 과함.
- **활용법**: 직접 채택보다 개념·용어·타입 분류의 *참고서*로 사용 추천
  (interaction 종류, scoring/response processing 모델 등).

링크:
- QTI 3.0 Overview: https://www.imsglobal.org/spec/qti/v3p0/oview
- QTI standards index: https://www.1edtech.org/standards/qti
- QTI 3 Beginner's Guide: https://www.imsglobal.org/spec/qti/v3p0/guide

## 3. 그 외 실무 포맷 (가벼운 참고용)

| 포맷 | 성격 | 참고 포인트 |
| --- | --- | --- |
| **H5P** | 콘텐츠 타입별 JSON | 퀴즈 타입별 실제 JSON 구조 공개 → 모델링 참고에 좋음 |
| **Moodle GIFT / Moodle XML / Aiken** | 퀴즈 import 텍스트 포맷 | 간단한 문항 표현의 미니멀 설계 참고 |
| **Common Cartridge** | QTI 래핑 패키지 | LMS 상호운용 필요 시 |
| **xAPI / cmi5** | 학습 *기록* 추적 | 문항 정의가 아니라 응답/결과 전송용 — 목적 다름 |

## 추천 (미쁨 퀴즈 기준)

1. **기본 골격은 자체 JSON Schema로 유지** — QTI 풀 채택은 비추천.
2. **필드 네이밍을 schema.org에 정렬**(`type`→`eduQuestionType`, `answer`→`acceptedAnswer` 등)하면
   표준성과 SEO export 가능성을 확보.
3. **타입 분류·채점 모델은 QTI를 참고서로** 활용해 누락된 개념(배점/정답 처리/그리드)을 설계.
