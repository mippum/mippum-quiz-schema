# CLAUDE.md

이 저장소에서 작업할 때 참고할 가이드입니다.

## 프로젝트 개요

미쁨(mippum) 브랜드에서 사용하는 **퀴즈(quiz) 데이터의 JSON Schema**를 정의하는 저장소입니다. 애플리케이션 코드는 없고, 스키마와 예시 데이터로만 구성됩니다.

## 구조

- `quiz.schema.json` — 단일 퀴즈 문항의 스키마 (JSON Schema draft 2020-12)
- `samples/` — type별 예시 데이터 (`Checkbox/`, `FreeAnswer/`, `MultipleChoice/`, `ShortAnswer/`, `simplest.json`)
- `README.md` — 스키마 필드 설명 (한글)
- `ref/` — 참고 자료

## 스키마 핵심 규칙

하나의 퀴즈 = 하나의 JSON 객체.

- `question`(string)은 항상 필수.
- `type`은 `MultipleChoice` | `Checkbox` | `ShortAnswer` | `FreeAnswer`. 생략하면 채점 없는 자유 질문.
- **`answer`의 선택지 인덱스는 1-based** (첫 번째 선택지 = `1`). 0-based 아님 — 샘플 교차검증으로 확정된 규칙이니 변경하지 말 것.
- `MultipleChoice`/`Checkbox`는 `options` + `answer`(정수 / 정수 배열) 사용, `answerType` 미사용.
- `ShortAnswer`/`FreeAnswer`는 `options` 미사용. `FreeAnswer`는 정답이 없으므로 `answer`도 금지.
- `help` 객체는 `summary`, `explanation`만 허용.

## 작업 원칙

- **스키마와 샘플, README는 항상 동기화**할 것. 스키마를 바꾸면 `samples/`의 예시와 `README.md` 표를 함께 갱신.
- 새 type이나 필드를 추가할 때는 `samples/`에 대응 예시를 같이 추가.
- 스키마 변경 후에는 모든 샘플이 통과하는지 검증할 것.

## 검증 방법

`ajv`로 draft 2020-12 스키마를 검증합니다. (의존성은 저장소에 커밋하지 않음 — `--no-save`로 임시 설치 후 정리)

```bash
npm i --no-save ajv
node -e '
const fs=require("fs");
const Ajv=require("ajv/dist/2020");          // 기본 export는 draft-07이므로 /dist/2020 사용
const ajv=new Ajv({allErrors:true, strict:false});
const validate=ajv.compile(JSON.parse(fs.readFileSync("quiz.schema.json","utf8")));
for(const f of ["samples/simplest.json","samples/Checkbox/simple.json","samples/FreeAnswer/simple.json","samples/MultipleChoice/simple.json","samples/ShortAnswer/help.json","samples/ShortAnswer/simple.json"]){
  const ok=validate(JSON.parse(fs.readFileSync(f,"utf8")));
  console.log((ok?"PASS":"FAIL"),f);
  if(!ok) console.log(validate.errors);
}
'
```
