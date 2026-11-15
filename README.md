# paper-critique

논문을 비판적으로 읽고 쌓인 노트에서 연구 주제 후보를 찾는 Claude Code 플러그인(스킬).

## 설치

### 플러그인으로 설치 (권장)

Claude Code 안에서:

```
/plugin marketplace add CSJasper/paper-critique
/plugin install paper-critique@paper-critique
```

Claude Code 2.1.275 이상이면 한 줄로도 된다.

```
/plugin install paper-critique --marketplace CSJasper/paper-critique
```

터미널에서 하려면:

```bash
claude plugin marketplace add CSJasper/paper-critique
claude plugin install paper-critique@paper-critique
```

설치한 뒤 Claude Code를 재시작한다. 플러그인으로 설치하면 `/paper-critique:paper-critique read ...`처럼 명령 앞에 플러그인 이름이 붙는다. 자연어로 부를 때는 이름을 신경 쓰지 않아도 된다.

업데이트와 삭제:

```
/plugin marketplace update paper-critique
/plugin uninstall paper-critique@paper-critique
```

### 스킬로 직접 설치 (수정하며 쓰고 싶을 때)

스킬 본체는 `skills/paper-critique/`에 있다. repo를 clone한 뒤 스킬 경로에 이 폴더를 연결한다.

```bash
git clone https://github.com/CSJasper/paper-critique.git <원하는 경로>

# 전역 (모든 폴더에서 사용, repo에서 고친 내용이 바로 반영됨)
ln -s <원하는 경로>/skills/paper-critique ~/.claude/skills/paper-critique

# 또는 논문 폴더 전용
mkdir -p <논문폴더>/.claude/skills
ln -s <원하는 경로>/skills/paper-critique <논문폴더>/.claude/skills/paper-critique
```

이렇게 설치하면 `/paper-critique read ...`처럼 접두사 없이 부른다. 플러그인과 함께 설치하면 같은 스킬이 두 번 잡히니 하나만 쓴다.

### Codex에서 쓰기

같은 스킬 폴더를 Codex CLI에서도 쓴다. 둘 중 하나로 설치한다.

플러그인으로 설치:

```bash
codex plugin marketplace add https://github.com/CSJasper/paper-critique.git
codex plugin add paper-critique@paper-critique
```

스킬로 직접 설치 (clone한 repo에서 고친 내용이 바로 반영됨):

```bash
mkdir -p ~/.agents/skills
ln -s <원하는 경로>/skills/paper-critique ~/.agents/skills/paper-critique
```

Codex에서는 `$paper-critique read papers/xxx.pdf`처럼 `$`로 부르거나 `/skills`에서 고른다. 자연어로 불러도 된다. 후보 논문을 찾고 synth에서 선행 연구를 확인할 때 웹 검색을 쓴다. 노트를 저장하려면 쓰기 권한도 필요하므로 논문 폴더에서 이렇게 실행한다.

```bash
codex --search --sandbox workspace-write
```

PDF를 바로 읽을 수 없는 환경에서는 `pdftotext`, `pdftoppm`(poppler)을 쓴다. 설치해 두면 좋다(`brew install poppler`).

## 사용

논문 폴더에서 Claude Code를 실행하고 아래처럼 부른다. 플러그인으로 설치했다면 `/paper-critique:paper-critique`로, Codex라면 `$paper-critique`로 바꿔 쓴다.

```
/paper-critique read papers/xxx.pdf [주제]  # 논문 한 편 깊게 읽기 (주제를 주면 그 주제에 기록)
/paper-critique map <주제>            # 주제 지도: 내 예측 → 후보 논문 풀 → 계보·쟁점 초안
/paper-critique map papers/a.pdf, 2501.01234, 논문 제목  # 논문 리스트로 지도 만들기 (리스트를 seed로)
/paper-critique skim papers/xxx.pdf [주제]  # 판정 먼저(deep / done / drop), 사실 카드 나중
/paper-critique checkpoint [주제]     # 주제를 계속할지·바꿀지·접을지 판단
/paper-critique onboard papers/xxx.pdf  # 처음 보는 분야면 배경부터 (선택)
/paper-critique resume                # 읽다 멈춘 논문 이어 읽기
/paper-critique synth                 # 완료한 deep 노트 5개 이상 쌓이면 교차 분석
/paper-critique status                # 진행 현황
/paper-critique learn <개념>          # 모르는 개념 배우기
/paper-critique practice              # 배운 개념 복습
```

"이 논문 같이 비판적으로 읽어보자", "어제 읽던 거 이어서 하자", "이 용어 모르겠어", "복습하자"처럼 자연어로 말해도 된다.

## 흐름 (read)

1. 준비: Claude가 PDF를 읽고 빈 노트 생성 → "왜 읽나요?" 질문
2. 이해도 점검: 소크라테스식 질문 기본 3개, 사실 오류는 여기서 교정
3. **내가 먼저** 비판 작성 (주장 / 근거 / 가정 / 깨지는 조건 / reject 사유 / 아이디어)
4. Claude 공격 라운드: 비판 하나씩 판정 → 저자 rebuttal → 놓친 것 논문당 0–3개 (질문으로 먼저)
5. 정리: 주장 강도, 다른 노트와 연결, 동의한 갭만 gap-log에 추가

응답이 나올 때마다 노트가 저장되므로 언제 멈춰도 `resume`으로 이어갈 수 있다.

## 흐름 (주제)

논문 한 편이 아니라 주제 하나를 단위로 읽고 싶을 때 쓴다.

1. `map`: 먼저 주제 이름(slug)을 정한다. Claude가 하나를 제안하고 내가 확인하면, 이후 `skim`·`read`·`checkpoint`의 `[주제]`에 이 slug를 쓴다. 그다음 이 주제에 대해 내가 아는 것과 예상 쟁점을 쓴다. Claude는 그 뒤에 후보 논문 15–30편(제목·연도·링크·찾은 출처만, 순서 매기기·추천 없음)을 모으고 주제 수준의 계보·쟁점 초안을 만든다. 초안에는 "이번에 찾은 자료 범위 안에서"라는 단서를 달고, 검색으로 확인하지 않은 주장은 `[추측]`으로 표시한다. skim할 논문은 내가 고른다.
   - 주제 대신 논문 리스트를 줄 수도 있다. PDF 경로, arXiv 링크나 ID, 제목을 섞어 쉼표나 줄바꿈으로 나눈다. Claude는 먼저 목록(제목·연도·링크)만 정리해 보여 준다. 내용 요약이나 공통 주제는 말하지 않는다. slug를 정하고 논문을 묶은 이유와 예상 쟁점을 쓰면, Claude는 내가 준 논문을 후보 풀 맨 위(출처 `사용자 지정`)에 두고 주변 논문을 검색으로 보탠다. 이미 있는 주제의 slug를 고르면 준 논문만 그 주제의 후보 풀 끝에 덧붙는다. 예측과 초안은 그대로다.
2. `skim`: abstract와 핵심 Figure/Table을 직접 보고 `deep / done / drop`과 이유 한 줄을 먼저 쓴다. 판정 전에는 "설명해줘"라고 해도 Claude가 이 논문의 방법을 설명하지 않는다. 볼 위치만 알려 준다. 판정을 쓰면 Claude가 사실 카드(문제, 방법 한 줄, 핵심 결과와 쪽 번호, 평가 없음)를 주고 내 판정을 반박한다. 최종 판정은 내가 한다. 후보 풀에 없는 논문도 skim할 수 있다. 그 논문은 후보 풀에 덧붙는다.
3. `deep` 판정이면 같은 노트 파일에서 `read`로 승격한다. skim 기록은 그대로 남는다. 판정 없이 바로 깊게 읽겠다고 해도 된다.
4. 이미 판정한 논문을 다시 skim하면 새 판정은 아래에 덧붙는다. 같은 논문을 두 번 세지는 않는다.
5. `checkpoint`: 한 주제에서 skim 8편이나 deep 1편이 쌓이면 Claude가 한 번 제안한다. 거절하면 다음 트리거까지 다시 묻지 않는다. 수락하면 Claude가 판단 재료(처음 쓴 내 예측, 이 주제의 gap 항목, 판정 보정 기록, skim·deep 수)를 평가 없이 먼저 보여 준다. 내가 계속 / 전환 / 접기와 근거를 쓰면 Claude가 공격한다. 결정은 내가 한다.
6. 접을 때는 이유와 다시 열 조건을 내 말로 남긴다. 방향을 바꾸면 새 주제 파일이 생긴다. 이어 쓸 노트는 내가 고른다.
7. 주제를 접거나 바꿔도 노트와 gap-log는 그대로 남고 `synth`가 함께 읽는다. 나중에 skim 판정을 바꾸면(예: drop이었던 논문을 다시 깊게 읽음) 주제 파일에 한 줄씩 남기고 checkpoint에서 돌아본다.
8. `status`는 주제별 skim·deep 수와 checkpoint까지 남은 skim 수를 보여 준다. 파일은 고치지 않는다.

## 예시 workflow

시작할 때의 상황 세 가지와 노트가 쌓인 뒤 할 일을 적었다. 아래 주제와 파일 이름은 예시다.

### 관심 주제만 있고 읽을 논문은 아직 모를 때

```
/paper-critique map 비디오 생성 모델의 물리 일관성
```

1. Claude가 slug를 제안한다(예: `video-physics`). 내가 확인하면 이후 `[주제]` 자리에 이 slug를 쓴다.
2. 이 주제에 대해 아는 것과 예상 쟁점을 먼저 쓴다. 틀려도 괜찮다. 나중에 checkpoint에서 이 예측과 실제로 읽은 결과를 나란히 본다.
3. Claude가 후보 논문 15–30편과 계보·쟁점 초안을 준다. 목록을 보고 skim할 논문을 몇 편 고른다.
4. 고른 논문을 skim한다.

   ```
   /paper-critique skim papers/foo.pdf video-physics
   ```

   abstract와 핵심 Figure를 보고 `drop — 평가가 합성 장면뿐`처럼 판정과 이유 한 줄을 먼저 쓴다. Claude가 사실 카드를 주고 판정을 반박하면, 최종 판정은 내가 정한다.
5. `deep`으로 판정한 논문은 같은 노트에서 `read`로 이어 깊게 읽는다. 지금 읽을지는 내가 정한다.
6. skim 8편이나 deep 1편이 쌓이면 Claude가 checkpoint를 한 번 제안한다. 아직 이르다 싶으면 거절하고 계속 읽는다. 받아들이면 처음 쓴 예측, gap 항목, 판정 수를 보고 계속 / 전환 / 접기를 정한다.

### 이미 모아 둔 논문이 있을 때

```
/paper-critique map papers/a.pdf, 2501.01234, 논문 제목
```

Claude는 목록(제목·연도·링크)만 정리해 보여 준다. slug를 정하고 이 논문들을 묶은 이유와 예상 쟁점을 쓰면, Claude는 내가 준 논문을 후보 풀 맨 위에 두고 주변 논문을 검색으로 보탠다. 그다음은 위와 같다. 나중에 논문을 더 모으면 같은 방식으로 `map`을 부르고 기존 주제의 slug를 고른다. 그러면 새 논문만 그 주제의 후보 풀에 덧붙는다.

### 논문 한 편을 깊게 읽을 때

```
/paper-critique read papers/xxx.pdf video-physics
```

`[주제]`는 빼도 된다. 다만 주제 없이 읽은 노트는 checkpoint 횟수에 들어가지 않는다. 분야가 낯설면 시작할 때 Claude가 제안하는 온보딩을 받는다. 중간에 멈추면 나중에 `resume`으로 이어 읽는다.

### 몇 주 뒤

- `status`로 주제별 skim·deep 수, 진행 중인 노트, 각 노트의 다음 할 일을 확인한다.
- 끝까지 읽은 deep 노트가 5편 이상이면 `synth`로 노트를 교차 분석해 갭과 연구 주제 후보를 찾는다. 접거나 바꾼 주제의 노트도 함께 읽는다.
- 읽으며 배운 개념이 쌓였으면 `practice`로 복습한다.

## 지쳤을 때

비판적으로 읽기는 힘이 든다. 답이 근거 없이 흐려지거나 같은 데서 계속 막히면 Claude가 조력자로 바뀐다.

- 단계: 좁히는 질문(nudge) → 새 관점 하나(lens) → 일부만 시연(partial) → 요청하면 전체 설명(full)
- lens 예: baseline 공정성, 자원 맞춤, ablation, metric validity, data leakage, 통계, 외삽, 대안 설명
- "피곤해"라고 하면 light mode로 간다. claim 하나만 보고 나머지는 미검토로 남긴 뒤 다음에 이어간다.
- "그만"이라고 하면 바로 저장하고 멈춘다.
- skim 중에는 판정 전에 nudge와 lens(볼 위치 안내)까지만 돕는다. 피곤하다고 하면 light mode 대신 여기서 멈출지 묻고, 멈추면 다음에 같은 단계부터 이어간다.

## 모르는 개념이 나올 때

읽다가 모르는 용어가 나오면 Claude가 논문의 어느 쪽·식·Figure에 나오는지 짚어 짧게 설명한다. 이어 적용해 보는 확인 질문을 하나 한다. 답하면 하던 항목으로 돌아간다.
답이 막히면 `learn`을 한 번만 제안한다. 받아들이면 단계별로 개념을 익히고 읽던 지점부터 이어 읽는다. 거절하면 비판을 계속한다.
분야 자체가 낯설면 `read`를 시작할 때 Claude가 온보딩을 한 번 제안한다. 받아들이면 논문 전체에 걸친 배경 개념을 얼마나 아는지 확인한다. 최소 지도를 만들어 가르친 뒤 같은 논문의 이해도 점검으로 넘어간다. `onboard <pdf>`로 바로 시작해도 된다.
`practice`는 쌓인 개념 중 복습할 것을 골라 묻는다.
개념 상태는 `introduced` → `checked` → `held` 세 단계다. 판정 규칙은 `skills/paper-critique/templates/concept.md`에 있다.

## 논문 폴더 구조

Claude Code를 실행하는 논문 폴더는 아래와 같다. 직접 넣는 것은 `papers/`의 PDF뿐이다. 나머지 파일과 폴더는 각 모드를 처음 쓸 때 Claude가 만든다. 파일 이름은 예시다.

```
my-papers/                          # 논문 폴더 (여기서 Claude Code 실행)
├── papers/                         # PDF 원본, 직접 넣는다
│   ├── kim2024-physbench.pdf
│   └── lee2025-physgen.pdf
├── notes/                          # 논문당 노트 1개: <first-author><year>-<short-title>.md
│   ├── kim2024-physbench.md        #   read로 깊게 읽은 노트 (depth 없음 = deep)
│   └── lee2025-physgen.md          #   skim 노트 (depth: skim), deep이면 같은 파일에서 read로 승격
├── topics/                         # 주제당 파일 1개: <slug>.md (map·skim·checkpoint)
│   └── video-physics.md            #   내 예측, 후보 풀, 노트 링크, 기록, 판정 보정, 접기·전환
├── concepts/                       # 개념당 노트 1개: <concept-slug>.md (개념 도움·learn·practice)
│   └── optical-flow.md
├── synthesis/                      # synth 결과: YYYY-MM-DD.md
│   └── 2026-11-15.md
└── gap-log.md                      # 연구 제약 + 갭·의문·아이디어 누적 (주제 열 포함)
```

- 주제 소속과 skim 판정은 노트 frontmatter(`topics`, `skim_verdict`)에만 저장한다. 주제 파일의 `## 노트`는 링크 목록이다.
- 템플릿은 스킬 폴더의 `skills/paper-critique/templates/`에 있다.
  - 노트: `paper-note.md`, `skim-note.md`
  - 주제 파일: `topic.md`
  - 개념 노트: `concept.md`
  - 갭 로그: `gap-log.md`

노트 폴더는 git으로 관리하길 권장한다. Obsidian vault로 열면 `[[링크]]`가 그래프로 보인다.

## 커스터마이즈 포인트

- 이해도 점검이 부담되면 `skip`(현재 질문) / `skip phase`(전체)나 light mode를 쓴다. 기본값을 바꾸려면 `skills/paper-critique/references/read.md` Phase 1을 고친다.
- lens 목록은 `skills/paper-critique/SKILL.md` `조력자 규칙`에 있다. 분야에 맞게 추가·삭제한다.
- `synth`의 최소 실험 기준은 `gap-log.md`의 `연구 제약`을 따른다. 기본값은 로컬 GPU 없음(노트북).
- `read` Phase 0–4는 `references/read.md`, `synth`는 `references/synth.md`, 주제 지도는 `references/map.md`, skim은 `references/skim.md`, checkpoint는 `references/checkpoint.md`에 있다. `learn` 방법론은 `references/learn.md`, 온보딩은 `references/onboard.md`, 복습은 `references/practice.md`에 있다. 모두 `skills/paper-critique/` 아래에 있다.

## 변경 기록

### 0.4.0

- Codex CLI에서도 쓸 수 있다. 스킬 폴더 하나를 함께 쓴다. Codex용 `.codex-plugin/plugin.json`, `.agents/plugins/marketplace.json`, `skills/paper-critique/agents/openai.yaml`을 더했다.
- 스킬 본문에서 Claude Code 전용 표현을 뺐다. `WebSearch` 대신 "웹 검색"이라 쓰고, 검색을 쓸 수 없으면 `확인 불가`로 남긴다. PDF를 바로 읽을 수 없으면 `pdftotext`·`pdftoppm`으로 읽는다.
- `## Claude 피드백`, `## Claude 반론`, `Claude 판단`은 기존 노트와 맞추기 위해 이름을 그대로 둔다. 스킬 문서의 "Claude"는 스킬을 실행하는 AI를 뜻한다.
- 옛 `paper-study` 초안(`archive/`)을 지웠다.

### 0.3.0

논문을 한 편씩 같은 깊이로 읽으면 연구 주제를 찾는 데 너무 오래 걸린다. 그래서 주제 전체를 먼저 살피는 흐름을 더했다. 깊게 읽을 때의 원칙(내가 먼저 생각하고 Claude가 공격, 근거는 논문 위치로, 아첨 금지)은 그대로다.

- 새 모드: `map`(주제 지도), `skim`(판정 먼저 빠르게 읽기), `checkpoint`(계속 / 전환 / 접기).
- `map`에는 주제 이름 대신 논문 리스트를 줘도 된다. 리스트는 seed다. 이미 있는 주제를 고르면 후보 풀에 논문만 덧붙인다.
- `read <pdf> [주제]`로 깊게 읽는 논문도 주제에 넣을 수 있다. 주제 없이 읽은 논문은 checkpoint 횟수에 들어가지 않는다.
- 새 폴더 `topics/`와 새 템플릿 `templates/skim-note.md`, `templates/topic.md`를 더했다. 노트에는 `depth`, `topics`, `skim_verdict` 필드가 생겼다. `depth`가 없는 기존 노트는 깊게 읽은 노트로 본다.
- 주제 소속과 판정은 노트 frontmatter에만 둔다. 주제 파일 `## 노트`는 링크 목록이다.
- `synth`는 끝까지 읽은 deep 노트 5편 이상을 권장 기준으로 삼는다. skim 노트는 보조 근거로만 쓴다. 접거나 바꾼 주제의 노트도 읽는다.
- `gap-log`에 `주제` 열이 생겼다. 기존 gap-log는 처음 항목을 쓸 때 열이 추가된다.
- 구조: `read`와 `synth`의 세부 규칙을 문장 그대로 `references/read.md`, `references/synth.md`로 옮겼다. `SKILL.md`에는 절대 규칙, 조력자 규칙, 노트 상태, 모드 라우팅 표, 짧은 모드 규칙이 남는다.

## 참고한 것

[LearnHarness](https://github.com/amosblomqvist/learnharness)(AGPL-3.0)의 교수 이론을 논문 읽기에 맞게 재구성했다. 문장은 새로 썼고 코드는 가져오지 않았다.

| LearnHarness | 이 스킬 |
|---|---|
| prober: strand별 이분 탐색, 정오 × 확신도, thinking field | `references/onboard.md` 측정, `learn`의 선행지식 확인(축소) |
| planner: 최소 트리, 무조건적 참 루트, mermaid 지도 | `references/onboard.md` 계획(평문 또는 mermaid 지도), `references/learn.md` 원칙 i |
| teacher: predict → teach → check, "어떻게 발견할 수 있었을까" | `learn` |
| 지식 그래프(명제 + 추론 체인 + provenance) | `concepts/` 마크다운 노트 |
| practice: 비활성 상태에서의 인출, interleave, recall/discrimination/composition | `practice` 모드 |
| (없음) | 논문 앵커, 비유의 한계, 기호표, 코드 앵커, critique 복귀 |

## 라이선스

[MIT](LICENSE)
