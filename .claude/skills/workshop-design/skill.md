---
name: workshop-design
description: "비전공자 대상 Claude Code/AI 도구 워크숍의 진행안을 5명 에이전트 팀으로 설계. '워크숍 설계해줘', '강의안 짜줘', '커리큘럼' 트리거."
---

# Workshop Design Orchestrator

비전공자 학습자 페르소나에 맞춘 Claude Code 워크숍 진행안을 생성하는 통합 스킬.

## 실행 모드: 에이전트 팀

## 에이전트 구성

| 팀원 | 타입 | 역할 | 출력 |
|------|------|------|------|
| learner-profiler | 커스텀 | 학습자 페르소나 분석 | `_workspace/01_learner_profiles.md` |
| curriculum-architect | 커스텀 | 시간 배분·세션 설계 | `_workspace/02_curriculum.md` |
| project-ideator | 커스텀 | 페르소나별 첫 프로젝트 | `_workspace/03_projects.md` |
| engagement-designer | 커스텀 | 참여·재미·백업플랜 | `_workspace/04_engagement.md` |
| facilitator-guide-writer | 커스텀 | 통합 진행안 작성 | `WORKSHOP_PLAN.md`, `HANDOUT.md`, `PREP_CHECKLIST.md` |

## 워크플로우

### Phase 1: 입력 분석
1. 학습자 정보, 워크숍 길이, 학습 목표 파악
2. `_workspace/00_input.md`에 정리하여 저장

### Phase 2: 팀 구성
```
TeamCreate(team_name: "workshop-design-team", members: [
  { name: "learner-profiler", agent_type: "learner-profiler", prompt: "..." },
  { name: "curriculum-architect", agent_type: "curriculum-architect", prompt: "..." },
  { name: "project-ideator", agent_type: "project-ideator", prompt: "..." },
  { name: "engagement-designer", agent_type: "engagement-designer", prompt: "..." },
  { name: "facilitator-guide-writer", agent_type: "facilitator-guide-writer", prompt: "..." },
])
```

### Phase 3: 분석·설계 (병렬+의존)
- learner-profiler 먼저 실행 (다른 팀원이 의존)
- curriculum-architect, project-ideator, engagement-designer 병렬 실행
- 팀원 간 SendMessage로 wow 모먼트 / 시간 배분 동기화

### Phase 4: 통합
- facilitator-guide-writer가 4개 산출물을 통합하여 최종 3개 파일 생성

### Phase 5: 정리
- `_workspace/` 보존
- 사용자에게 `WORKSHOP_PLAN.md` 위치 안내

## 데이터 흐름
```
입력 → learner-profiler → profiles.md ─┐
                                        ├→ curriculum.md ┐
                                        ├→ projects.md   ├→ facilitator → WORKSHOP_PLAN.md
                                        └→ engagement.md ┘
```

## 에러 핸들링
| 상황 | 전략 |
|------|------|
| 페르소나 정보 부족 | 사용자에게 추가 질문 |
| 팀원 1명 실패 | 재시도 1회 → 리더가 해당 영역을 직접 채움 |
| 시간 배분과 프로젝트 길이 불일치 | curriculum-architect가 조정 |

## 테스트 시나리오
- 정상: 2명 학습자 + 3시간 입력 → 5단계 거쳐 WORKSHOP_PLAN.md 생성
- 에러: project-ideator가 wow 모먼트 도출 실패 → engagement-designer 결과로 보완
