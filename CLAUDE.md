# CLAUDE.md

이 저장소에서 작업할 때 참고할 가이드입니다.

## 프로젝트 개요

미쁨(mippum) 브랜드에서 사용하는 **퀴즈(quiz) 데이터의 JSON Schema**를 정의하는 저장소입니다. 애플리케이션 코드는 없고, 스키마와 예시 데이터로만 구성됩니다.

## 구조

- `quiz.schema.json` — **단일 문항** 스키마 (JSON Schema draft 2020-12)
- `quiz-set.schema.json` — **묶음(여러 문항)** 스키마. `quizzes[]`가 `quiz.schema.json`을 `$ref`로 참조
- `samples/quiz/` — 단일 문항 예시 (`Checkbox/`, `FreeAnswer/`, `MultipleChoice/`, `ShortAnswer/`, `simplest.json`)
- `samples/quiz-set/` — 묶음 예시 (`simple.json`, `mixed.json`)
- `README.md` — 스키마 필드 설명 (한글)
- `ref/` — 참고 자료 (schema.org, QTI, Google Forms 견본)

> 샘플 폴더는 스키마 파일과 1:1로 대응한다: `quiz.schema.json` ↔ `samples/quiz/`, `quiz-set.schema.json` ↔ `samples/quiz-set/`.

## 스키마 핵심 규칙

하나의 퀴즈(문항) = 하나의 JSON 객체. 묶음은 그 문항들의 배열.

- `question`(string)은 항상 필수.
- `type`은 `MultipleChoice` | `Checkbox` | `ShortAnswer` | `FreeAnswer`. 생략하면 채점 없는 자유 질문.
- **`answer`의 선택지 인덱스는 1-based** (첫 번째 선택지 = `1`). 0-based 아님 — 샘플 교차검증으로 확정된 규칙이니 변경하지 말 것.
- `MultipleChoice`/`Checkbox`는 `options` + `answer`(정수 / 정수 배열) 사용, `answerType` 미사용.
- `ShortAnswer`/`FreeAnswer`는 `options` 미사용. `FreeAnswer`는 정답이 없으므로 `answer`도 금지.
- `help` 객체는 `summary`, `explanation`만 허용.
- 묶음(`quiz-set.schema.json`): `quizzes`(최소 1개) 필수, `title`/`description` 선택.

## 작업 원칙

- **스키마와 샘플, README는 항상 동기화**할 것. 스키마를 바꾸면 `samples/`의 예시와 `README.md` 표를 함께 갱신.
- 새 type이나 필드를 추가할 때는 `samples/`에 대응 예시를 같이 추가.
- 스키마 변경 후에는 모든 샘플이 통과하는지 검증할 것.

## 검증 방법

`ajv`로 draft 2020-12 스키마를 검증합니다. (의존성은 저장소에 커밋하지 않음 — `--no-save`로 임시 설치 후 정리)

`quiz-set.schema.json`이 `quiz.schema.json`을 상대 `$ref`로 참조하므로, 두 스키마를 모두 ajv에 등록해야 한다.

```bash
npm i --no-save ajv
node -e '
const fs=require("fs");
const Ajv=require("ajv/dist/2020");          // 기본 export는 draft-07이므로 /dist/2020 사용
const ajv=new Ajv({allErrors:true, strict:false});
const quiz=JSON.parse(fs.readFileSync("quiz.schema.json","utf8"));
ajv.addSchema(quiz);                          // $id 로 등록 → "quiz.schema.json" 상대 ref 해석됨
const vQuiz=ajv.getSchema(quiz["$id"]);
const vSet=ajv.compile(JSON.parse(fs.readFileSync("quiz-set.schema.json","utf8")));
const check=(v,files)=>files.forEach(f=>{
  const ok=v(JSON.parse(fs.readFileSync(f,"utf8")));
  console.log((ok?"PASS":"FAIL"),f); if(!ok) console.log(v.errors);
});
check(vQuiz,["samples/quiz/simplest.json","samples/quiz/Checkbox/simple.json","samples/quiz/FreeAnswer/simple.json","samples/quiz/MultipleChoice/simple.json","samples/quiz/ShortAnswer/help.json","samples/quiz/ShortAnswer/simple.json"]);
check(vSet,["samples/quiz-set/simple.json","samples/quiz-set/mixed.json"]);
'
```
