# skim: 판정 먼저, 사실 카드 나중

## 범위

1. SKILL.md 절대 규칙 1의 skim 예외를 따른다. 이 파일에 그 규칙을 다시 적지 않는다.
2. skim은 사용자가 이 논문을 `deep / done / drop` 중 하나로 판정하는 짧은 읽기다. 판정하는 훈련이다.
3. Claude는 판정을 대신 내리거나 바꾸라고 권하지 않는다.

## 1. 입력과 노트

1. 입력은 `papers/`의 PDF나 링크다. `p.N`은 PDF를 읽었을 때만 단다. PDF를 읽지 못하면 사실 카드의 결과 줄에 `위치 확인 불가`를 쓰고, 위치를 밝힐 수 없는 결과는 주장하지 않는다.
2. 노트는 `notes/<first-author><year>-<short-title>.md`다. 없으면 `templates/skim-note.md`로 만들고, `[주제]`를 줬으면 `topics`에 그 slug를 넣는다. 그 주제의 후보 풀에 없는 논문이면 `references/map.md` 5절대로 후보 풀에 덧붙인다.
3. 같은 논문의 `depth: deep` 노트가 있으면 skim하지 않고 그렇다고 말한다.
4. 판정을 마친 skim 노트를 다시 skim할 때는 다음을 따른다.
   - 노트를 `status: in-progress`로 바꾸고, 흐름 5단계에서 `status: done`으로 되돌린다.
   - `## Skim 판정` 아래에 `### 재판정 YYYY-MM-DD`를 추가하고 새 판정, 새 사실 카드, 새 반론을 그 아래에 쓴다. 이전 내용은 지우지 않는다.
   - 주제 `## 기록`에 `skim` 줄을 새로 쓰지 않는다. 같은 논문을 두 번 세지 않기 위해서다.
   - 판정이 바뀌면 `skim_verdict`를 고치고 판정 보정 기록을 한 줄 쓴다(`references/checkpoint.md` 6절).

## 2. 흐름

순서를 바꾸지 않는다. 단계마다 노트에 바로 기록하고 `next`를 갱신한다.

1. 사용자가 abstract와 핵심 Figure/Table을 직접 본다(5–10분).
2. 사용자가 판정(`deep / done / drop`), 이유 한 줄, 지도의 어느 쟁점과 연결되는지를 쓴다. 원문 그대로 `## Skim 판정`에 기록한다.
3. Claude가 `## 사실 카드`를 쓴다. 문제, 방법 한 줄, 핵심 결과와 `p.N`만 쓰고 평가는 넣지 않는다.
4. Claude가 사용자 판정을 반박하고 `## Claude 반론`에 기록한다. 근거 위치를 달고, 사용자가 놓친 것은 최대 1개만 든다.
5. 최종 판정은 사용자가 한다. `skim_verdict`에 적고 `status: done`으로 바꾼다. 반론을 듣고 같은 세션에서 판정을 바꾸면 `## Skim 판정`에 `최종 판정: …` 한 줄을 사용자 원문으로 덧붙인다. 이것은 판정 보정이 아니다.

## 3. 판정을 확정한 뒤

1–4단계는 노트에 `topics`가 있을 때만 한다.

1. 주제 파일 `## 노트`에 이 노트 링크가 없으면 덧붙인다.
2. 주제 파일 `## 기록`에 skim 줄을 쓴다(문법은 `references/checkpoint.md`). 다시 skim한 노트면 쓰지 않는다(1절 4).
3. 주제 `next`를 다음 skim 대상이나 "후보 선택"으로 갱신한다.
4. SKILL.md `## 주제 연동`대로 checkpoint 트리거를 확인한다.
5. 판정이 `deep`이면 지금 승격할지 묻는다. 들어갈지는 사용자가 정한다.

## 4. 승격 (같은 파일에서)

1. frontmatter를 `depth: deep`, `status: in-progress`, `phase: 0`으로 바꾼다. `skim_verdict`와 `## Skim 판정`부터 `## Claude 반론`까지는 한 글자도 고치지 않는다. frontmatter 주석은 `templates/paper-note.md`의 주석으로 바꾸고, 위 세 키 말고는 값을 그대로 둔다.
2. 파일 끝(`## Claude 반론` 아래)에 `templates/paper-note.md`의 `## 읽는 이유`부터 끝까지를 붙인다.
3. 판정을 마친 뒤 승격하는데 원래 `skim_verdict`가 `done`이나 `drop`이면 `references/checkpoint.md` 6절대로 판정 보정 기록을 한 줄 쓴다.
4. 판정 전 승격(사용자가 판정 없이 바로 깊게 읽겠다고 할 때)은 `skim_verdict`를 비워 두고 판정 보정 기록을 쓰지 않는다. 노트에 `topics`가 있으면 각 주제 파일 `## 노트`에 링크가 없을 때 덧붙이고 주제 `next`를 갱신한다.
5. 승격 후에는 `references/read.md` Phase 0의 "PDF를 끝까지 읽는다"부터 이어간다. "노트 파일을 템플릿으로 만들고" 단계는 이미 끝났으므로 건너뛴다. 그다음 `읽는 이유` 질문과 온보딩 제안을 평소대로 한다.
6. skim 이유를 `## 읽는 이유`로 옮기지 않는다. 사용자 말은 기록한 자리에 그대로 둔다(절대 규칙 3).

## 5. 조력

1. 판정 전에 쓰는 도움 단계는 nudge와 lens뿐이다. 둘 다 볼 위치를 가리키는 데서 그친다. partial example과 full은 쓰지 않는다.
2. skim 노트에는 `조력 기록` 섹션이 없다. skim 중 개념 도움은 개념 노트(`concepts/`)에만 기록하고, 도움 단계는 노트에 남기지 않는다.
3. skim 중에 사용자가 `learn`을 직접 부르면 이 노트에 붙이지 않고 논문 없는 learn으로 다룬다.
4. skim 중에 사용자가 피곤하다고 하면 light mode로 줄이지 않고 여기서 멈출지 묻는다. 멈추면 아래 6절 1을 따른다.

## 6. 그 밖

1. 중단은 절대 규칙 5를 따른다. 판정 전에 멈추면 `status: in-progress`로 두고 `next`에 멈춘 단계를 적는다.
2. 값의 의미를 구분한다. `skim_verdict: done`은 "이 논문은 더 볼 필요 없음"이고, 노트 `status: done`(작업 끝)이나 주제 `status: dropped`(주제 접기)와 다르다.
