# schema.org Quiz / Question / Answer 예제

미쁨 퀴즈 스키마의 필드 네이밍·구조를 정렬할 때 참고하는 schema.org 어휘 예제.

- `quiz-flashcard.jsonld` — Google이 문서에 제공하는 **공식 Flashcard 예제**(verbatim).
  Google Search Q&A carousel에 노출되는, 실제로 지원되는 유일한 퀴즈 패턴.
- `quiz-multiple-choice.jsonld` — schema.org 어휘(`eduQuestionType`, `suggestedAnswer`,
  `acceptedAnswer`, `position`)로 객관식을 표현한 **대표 예제**(직접 구성). Google이 공식 지원하는
  형식은 아니며, 어휘 매핑 참고용.

## 핵심 어휘

| schema.org | 의미 | 미쁨 스키마 대응(후보) |
| --- | --- | --- |
| `Quiz.hasPart` | 문항 배열 | (폼/세트 단위 — 현재 없음) |
| `Question.eduQuestionType` | 문항 종류 | `type` |
| `Question.text` | 질문 텍스트 | `question` |
| `Question.suggestedAnswer[]` | 보기들 | `options` |
| `Answer.position` | 보기 순서(0-based) | (현재는 1-based 인덱스) |
| `Question.acceptedAnswer` | 정답 | `answer` |

## 출처

- https://schema.org/Quiz
- https://schema.org/Question
- https://schema.org/Answer
- https://schema.org/eduQuestionType
- Google Education Q&A structured data: https://developers.google.com/search/docs/appearance/structured-data/education-qa
