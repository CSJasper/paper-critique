---
name: <concept-slug>
aliases: []
status: introduced   # introduced | checked | held
first_seen: YYYY-MM-DD
last_practiced:
learn_state: none    # none | in-progress | paused | skipped | done
learn_next:          # learn 재개 지점 한 줄. skip이면 비움
from_note:           # learn을 붙인 critique 노트, 없으면 비움
---

<!--
상태 규칙. 노트를 만들 때 이 주석은 복사하지 않는다.
- `introduced`: 설명을 들었으나 check를 통과하지 못했다(확인 질문 오답·무응답·skip 포함).
- `checked`: 짧은 도움의 확인 질문이나 learn 종료 check(`references/learn.md`의 종료)를 `pass`(힌트 없이)로 통과했다. 수리 뒤 재check 통과는 `pass(hint)`이고, 승급 근거가 아니다.
- `held`: `checked` 증거와 다른 날짜의 practice에서 recall 이상을 `pass`로 통과했다.
- 짧은 도움과 learn은 상태를 올리기만 한다. 내리는 것은 practice뿐이다. 같은 개념에서 2회 연속 `fail`이나 `pass(hint)`가 나오면 `held → checked`로 내리고 증거 로그에 이유를 적는다.
- 기존 노트를 갱신할 때도 이 규칙을 따른다.
- 짧은 도움은 frontmatter, `정의` 한 줄, `등장 논문`, 증거 로그 1행만 쓴다. 나머지 섹션은 learn이 채운다.
-->

# <개념 이름>

## 정의 / 무조건적 참
<!-- 단서 없이 받아들일 수 있는 형태로. 보편 진술이나 진짜 정의 -->

## 왜 필요한가 (동기)
<!-- 어떤 문제가 이 개념을 요구하나. "내가 이걸 발견할 수 있었을까?"의 출발점 -->

## 핵심 명제
- P1:
- P2:

## 추론 체인
<!-- 전제들이 함께 결론을 낳는다. 다른 체인과 섞지 않는다 -->
- [P1 + P2] ⇒ P3

## 수식 / 표준 표기
| 기호 | 의미 | shape | 비고 (논문별 표기 차이) |
|---|---|---|---|

## 내가 아는 것과의 연결
- 비유:
- **비유가 깨지는 지점**:

## 흔한 오해
<!-- 일반적인 것 + 내가 실제로 가졌던 것(증거 로그 링크) -->

## 등장 논문
- [[<노트이름>]] p.N, 식 (k) — 역할 한 줄

## 관련 개념
- requires: [[...]]
- used-by: [[...]]

## 선행 단계
<!-- learn 진행용 평문 목록. 이 개념을 가르치려고 먼저 다룰 선행 개념. 재개할 때 learn_next와 함께 읽는다 -->

## 증거 로그
<!-- 맥락: short | learn | practice. 결과: pass | pass(hint) | fail | skip -->
| 날짜 | 맥락 | 질문 | 결과 | 확신도 | 메모 |
|---|---|---|---|---|---|
