# paper-critique 스킬

논문을 비판적으로 읽고, 쌓인 노트에서 연구 주제 후보를 찾기 위한 Claude Code 스킬.

## 설치

```bash
# 전역 설치 (모든 폴더에서 사용)
git clone https://github.com/CSJasper/paper-critique.git ~/.claude/skills/paper-critique

# 또는 다른 곳에 clone한 뒤 symlink (repo에서 고친 내용이 바로 반영됨)
git clone https://github.com/CSJasper/paper-critique.git <원하는 경로>
ln -s <원하는 경로> ~/.claude/skills/paper-critique

# 또는 논문 폴더 전용
mkdir -p <논문폴더>/.claude/skills && git clone https://github.com/CSJasper/paper-critique.git <논문폴더>/.claude/skills/paper-critique
```

Claude Code를 재시작하면 인식된다.

## 사용

논문 폴더에서 Claude Code를 실행한 뒤:

```
/paper-critique read papers/xxx.pdf   # 논문 한 편 읽기
/paper-critique resume                # 읽다 멈춘 논문 이어 읽기
/paper-critique synth                 # 노트 5개 이상 쌓이면 교차 분석
/paper-critique status                # 진행 현황
```

자연어로 "이 논문 같이 비판적으로 읽어보자", "어제 읽던 거 이어서 하자"라고 해도 동작한다.

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

## 폴더 구조

```
papers/      PDF
notes/       논문당 노트
gap-log.md   연구 제약 + 갭·의문 누적
synthesis/   synth 결과
```

노트 폴더는 git으로 관리하는 걸 권장. Obsidian vault로 열면 `[[링크]]`가 그래프로 보인다.

## 커스터마이즈 포인트

- 이해도 점검이 부담되면 `skip`(현재 질문) / `skip phase`(전체)나 light mode를 쓴다. 기본값을 바꾸려면 SKILL.md Phase 1을 고친다.
- lens 목록은 SKILL.md `조력자 규칙`에 있다. 분야에 맞게 추가·삭제한다.
- `synth`의 최소 실험 기준은 `gap-log.md`의 `연구 제약`을 따른다. 기본값은 로컬 GPU 없음(노트북).
