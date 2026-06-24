# mippum-quiz-schema

미쁨(mippum) 브랜드에서 사용하는 퀴즈(quiz) 데이터의 스키마를 정의합니다.

- 스키마 파일: [`quiz.schema.json`](quiz.schema.json) (JSON Schema, draft 2020-12)
- 예시 데이터: [`samples/`](samples/)

하나의 퀴즈는 하나의 JSON 객체이며, 아래 필드로 구성됩니다.

## 공통 필드

| 필드 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `question` | string | ✅ | 사용자에게 보여줄 질문 |
| `type` | string | | 퀴즈 종류. 생략하면 채점이 없는 자유 질문으로 취급합니다. |
| `help` | object | | 정답 확인 후 보여줄 보충 설명 |

`type`은 다음 중 하나입니다: `MultipleChoice`, `Checkbox`, `ShortAnswer`, `FreeAnswer`.

`help` 객체는 `summary`(한 줄 요약)와 `explanation`(자세한 설명) 필드를 가질 수 있습니다.

## type별 필드

### `MultipleChoice` — 객관식 (단일 선택)

| 필드 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `options` | string[] | ✅ | 선택지 목록 |
| `answer` | integer | ✅ | 정답 선택지의 **1부터 시작하는** 인덱스 |

```json
{
  "type": "MultipleChoice",
  "question": "1+1=?",
  "options": ["0", "1", "2", "3", "4"],
  "answer": 3
}
```

### `Checkbox` — 객관식 (복수 선택)

| 필드 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `options` | string[] | ✅ | 선택지 목록 |
| `answer` | integer[] | ✅ | 정답 선택지들의 **1부터 시작하는** 인덱스 배열 |

```json
{
  "type": "Checkbox",
  "question": "Choose a numbers.",
  "options": ["A", "1", "2", "C", "D"],
  "answer": [2, 3]
}
```

### `ShortAnswer` — 단답형

| 필드 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `answerType` | `"Text"` \| `"Number"` | | 입력 형식 (기본값 `Text`) |
| `answer` | string \| number | | 정답 |

```json
{
  "type": "ShortAnswer",
  "question": "1+1=?",
  "answerType": "Number",
  "answer": 2,
  "help": {
    "summary": "1+1=2",
    "explanation": "If you add one apple to one apple, you get two apples."
  }
}
```

### `FreeAnswer` — 자유 응답 (정답 없음)

| 필드 | 타입 | 필수 | 설명 |
| --- | --- | --- | --- |
| `answerType` | `"Text"` \| `"Number"` | | 입력 형식 (기본값 `Text`) |

정답이 없는 열린 질문이므로 `options`와 `answer`를 가질 수 없습니다.

```json
{
  "type": "FreeAnswer",
  "question": "Who are you?",
  "answerType": "Text"
}
```

## 참고

- `answer`의 인덱스는 **1-based**입니다. 즉 첫 번째 선택지가 `1`입니다.
- 객관식(`MultipleChoice`, `Checkbox`)은 `answerType`을 사용하지 않습니다.
- 단답/자유형(`ShortAnswer`, `FreeAnswer`)은 `options`를 사용하지 않습니다.
