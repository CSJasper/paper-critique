# paper-study 플러그인 (초안 v0.1)

처음 접하는 분야의 논문을 **비판할 수 있을 만큼 이해**하기 위한 Claude Code 플러그인.
비판적 읽기 스킬의 앞 단계로 쓴다.

```
낯선 논문 → /paper-study:onboard → (이해 완료) → 비판 스킬 → synth
                     ↓ concepts/ 누적
              /paper-study:practice (며칠 뒤 복습)
```

## 구성

| 스킬 | 역할 |
|---|---|
| `onboard` | 선행 개념 측정 → 최소 경로 계획(mermaid 지도) → predict/teach/check로 한 단계씩 → 개념 노트 기록 |
| `practice` | 쌓인 개념을 섞어서 다른 맥락에서 꺼내 쓰는 인출 연습 |

## 설치 / 테스트

```bash
# 한 세션만 로드해서 테스트
claude --plugin-dir ./paper-study

# 검증
claude plugin validate ./paper-study
```

호출: `/paper-study:onboard papers/xxx.pdf`, `/paper-study:practice`

만들어 둔 비판 스킬도 `skills/<이름>/SKILL.md`로 이 플러그인에 넣으면 한 묶음으로 관리된다.
이때 호출명은 `/paper-study:<이름>`으로 바뀐다.

## 폴더 (논문 작업 디렉토리)

```
papers/       PDF
concepts/     개념 노트 (논문을 넘어 누적)
onboard/      논문별 온보딩 기록
notes/        비판 스킬 노트 (기존)
gap-log.md    (기존)
```

## 참고한 것

방법론은 [LearnHarness](https://github.com/amosblomqvist/learnharness)(AGPL-3.0)의 교수 이론을 논문 이해 맥락에 맞게 재구성했다. 문장은 새로 썼고 코드는 가져오지 않았다.

| LearnHarness | 이 플러그인 |
|---|---|
| prober: strand별 이분 탐색, 정오 × 확신도, thinking field | onboard Phase 2 |
| planner: 최소 트리, 무조건적 참 루트, mermaid 지도 | onboard Phase 3 |
| teacher: predict → teach → check, "어떻게 발견할 수 있었을까" | onboard Phase 4 |
| 지식 그래프(명제 + 추론 체인 + provenance) | `concepts/` 마크다운 노트 |
| practice: 비활성 상태에서의 인출, interleave, recall/discrimination/composition | practice 스킬 |
| (없음) | 논문 앵커, 비유의 한계, 기호표, repo 매핑, 비판 스킬로의 handoff |

## 다듬을 후보

- 측정이 길게 느껴지면 strand 수 상한(예: 4개)을 둔다.
- `concepts/` 상태 판정 기준(`held` 조건)을 실제 써보며 조정.
- practice를 주기적으로 돌리고 싶으면 scheduled task로 리마인드.
