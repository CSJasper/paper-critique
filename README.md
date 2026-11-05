# paper-critique

논문을 비판적으로 읽고, 쌓인 노트에서 연구 주제 후보를 찾기 위한 Claude Code 플러그인(스킬).

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

설치 후 Claude Code를 재시작한다. 플러그인으로 설치하면 명령 이름 앞에 플러그인 이름이 붙어 `/paper-critique:paper-critique read ...` 형태가 된다. 자연어로 부르면 이름을 신경 쓸 필요가 없다.

업데이트와 삭제:

```
/plugin marketplace update paper-critique
/plugin uninstall paper-critique@paper-critique
```

### 스킬로 직접 설치 (수정하며 쓰고 싶을 때)

스킬 본체는 `skills/paper-critique/`에 있다. repo를 clone한 뒤 이 폴더를 스킬 경로에 연결한다.

```bash
git clone https://github.com/CSJasper/paper-critique.git <원하는 경로>

# 전역 (모든 폴더에서 사용, repo에서 고친 내용이 바로 반영됨)
ln -s <원하는 경로>/skills/paper-critique ~/.claude/skills/paper-critique

# 또는 논문 폴더 전용
mkdir -p <논문폴더>/.claude/skills
ln -s <원하는 경로>/skills/paper-critique <논문폴더>/.claude/skills/paper-critique
```

이 방식이면 `/paper-critique read ...`처럼 접두사 없이 부른다. 플러그인과 함께 설치하면 같은 스킬이 두 번 잡히므로 하나만 쓴다.

## 사용

논문 폴더에서 Claude Code를 실행한 뒤(플러그인 설치라면 `/paper-critique:paper-critique`로 바꿔 쓴다):

```
/paper-critique read papers/xxx.pdf   # 논문 한 편 읽기
/paper-critique onboard papers/xxx.pdf  # 처음 보는 분야면 배경부터 (선택)
/paper-critique resume                # 읽다 멈춘 논문 이어 읽기
/paper-critique synth                 # 노트 5개 이상 쌓이면 교차 분석
/paper-critique status                # 진행 현황
/paper-critique learn <개념>          # 모르는 개념 배우기
/paper-critique practice              # 배운 개념 복습
```

자연어로 "이 논문 같이 비판적으로 읽어보자", "어제 읽던 거 이어서 하자", "이 용어 모르겠어", "복습하자"라고 해도 동작한다.

## 흐름 (read)

1. 준비: Claude가 PDF를 읽고 빈 노트 생성 → "왜 읽나요?" 질문
2. 이해도 점검: 소크라테스식 질문 기본 3개, 사실 오류는 여기서 교정
3. **내가 먼저** 비판 작성 (주장 / 근거 / 가정 / 깨지는 조건 / reject 사유 / 아이디어)
4. Claude 공격 라운드: 비판 하나씩 판정 → 저자 rebuttal → 놓친 것 논문당 0–3개 (질문으로 먼저)
5. 정리: 주장 강도, 다른 노트와 연결, 동의한 갭만 gap-log에 추가

노트는 응답이 나올 때마다 저장되므로 언제 멈춰도 `resume`으로 이어갈 수 있다.

## 지쳤을 때

비판적 읽기는 에너지가 든다. 답이 근거 없이 흐려지거나 같은 데서 계속 막히면 Claude가 조력자로 바뀐다.

- 단계: 좁히는 질문(nudge) → 새 관점 하나(lens) → 일부만 시연(partial) → 요청하면 전체 설명(full)
- lens 예: baseline 공정성, 자원 맞춤, ablation, metric validity, data leakage, 통계, 외삽, 대안 설명
- "피곤해"라고 하면 light mode: claim 하나만 보고 나머지는 미검토로 남긴 뒤 다음에 이어간다.
- "그만"이라고 하면 바로 저장하고 멈춘다.

## 모르는 개념이 나올 때

읽다가 모르는 용어가 나오면 Claude가 논문 속 위치(쪽·식·Figure)와 이어서 짧게 설명하고, 적용해 보는 확인 질문을 하나 던진다. 답하면 하던 항목으로 돌아간다.
답이 막히면 `learn`을 한 번만 제안한다. 받아들이면 단계별로 개념을 익힌 뒤 읽던 지점에서 이어 읽고, 거절하면 비판을 계속한다.
분야 자체가 낯설면 `read`를 시작할 때 Claude가 온보딩을 한 번 제안한다. 받아들이면 논문 전체의 배경 개념을 측정하고 최소 지도를 만들어 가르친 뒤 같은 논문의 이해도 점검으로 넘어간다. `onboard <pdf>`로 바로 시작해도 된다.
`practice`는 쌓인 개념 중 복습할 것을 골라 묻는다.
개념 상태는 `introduced` → `checked` → `held` 세 단계이고, 판정 규칙은 `skills/paper-critique/templates/concept.md`에 있다.

## 논문 폴더 구조

```
papers/      PDF
notes/       논문당 노트
gap-log.md   연구 제약 + 갭·의문 누적
synthesis/   synth 결과
concepts/    개념 노트 (논문을 넘어 누적)
```

노트 폴더는 git으로 관리하는 걸 권장. Obsidian vault로 열면 `[[링크]]`가 그래프로 보인다.

## 커스터마이즈 포인트

- 이해도 점검이 부담되면 `skip`(현재 질문) / `skip phase`(전체)나 light mode를 쓴다. 기본값을 바꾸려면 `skills/paper-critique/SKILL.md` Phase 1을 고친다.
- lens 목록은 `skills/paper-critique/SKILL.md` `조력자 규칙`에 있다. 분야에 맞게 추가·삭제한다.
- `synth`의 최소 실험 기준은 `gap-log.md`의 `연구 제약`을 따른다. 기본값은 로컬 GPU 없음(노트북).
- `learn` 방법론은 `references/learn.md`, 온보딩은 `references/onboard.md`, 복습은 `references/practice.md`에 있다(모두 `skills/paper-critique/` 아래).

## 참고한 것

방법론은 [LearnHarness](https://github.com/amosblomqvist/learnharness)(AGPL-3.0)의 교수 이론을 논문 이해 맥락에 맞게 재구성했다. 문장은 새로 썼고 코드는 가져오지 않았다.

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
