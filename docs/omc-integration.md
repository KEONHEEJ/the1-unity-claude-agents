# OMC + Unity Claude Agents Integration Guide

이 문서는 oh-my-claudecode(OMC)와 Unity Claude Agents 간의 통합 아키텍처를 설명합니다.

## Overview

Unity Claude Agents는 36+ 전문 에이전트로 구성된 Unity 게임 개발 AI 팀입니다. OMC는 autopilot, ralph, ultrawork 등의 실행 모드를 제공하는 오케스트레이션 프레임워크입니다.

이 통합은 두 시스템의 장점을 결합합니다:
- **Unity 에이전트**: 게임 개발 도메인 전문성 (항상 우선)
- **OMC 프레임워크**: 실행 모드, 지속성, 검증 프로토콜

## Architecture

```
┌─────────────────────────────────────────────────────┐
│ User Request (e.g., "autopilot: 인벤토리 시스템 구현") │
└───────────────────────────┬─────────────────────────┘
                            │
                            v
┌───────────────────────────────────────────────────────┐
│ Main Session (Conductor)                               │
│ - OMC CLAUDE.md 행동 규칙 적용                          │
│ - OMC 모드 감지 (autopilot/ralph/ultrawork/ecomode)     │
│ - Unity 프로젝트 감지                                    │
└───────────────────────────┬───────────────────────────┘
                            │
                            v
┌───────────────────────────────────────────────────────┐
│ unity-omc-bridge                                       │
│ - OMC 모드 → Unity 워크플로우 변환                       │
│ - tmux 사용 가능 여부 판단                               │
│ - 최적 라우팅 결정                                       │
└─────────┬─────────────────────────────┬───────────────┘
          │                             │
    tmux available              single agent mode
          │                             │
          v                             v
┌─────────────────────┐   ┌──────────────────────────┐
│ unity-team-lead      │   │ unity-tech-lead-          │
│ (실제 병렬 팀)       │   │ orchestrator (순차 분석)   │
│                      │   │                          │
│ TeamCreate           │   │ Structured findings      │
│ TaskCreate           │   │ Task breakdown           │
│ Task(spawn)          │   │ Agent routing            │
│ SendMessage          │   │                          │
└─────────┬────────────┘   └──────────────────────────┘
          │
          v
┌───────────────────────────────────────────────────────┐
│ Parallel Specialist Execution (tmux panels)            │
│                                                        │
│ Panel #1: gameplay-dev   (unity-gameplay-programmer)    │
│ Panel #2: ui-dev         (unity-ui-developer)          │
│ Panel #3: network-dev    (unity-multiplayer-engineer)   │
│ Panel #4: verifier       (unity-verifier)              │
│                                                        │
│ Each teammate: TaskList → TaskGet → Work → TaskUpdate  │
└───────────────────────────────────────────────────────┘
```

## Routing Rules

### Unity Domain → Unity Agents (Always)

| Task | Agent | Why Not OMC? |
|------|-------|-------------|
| C# 게임플레이 코드 | `unity-gameplay-programmer` | OMC executor는 Unity API 모름 |
| 셰이더 코드 | `unity-shader-programmer` | HLSL + Unity 렌더 파이프라인 전문지식 필요 |
| Unity 성능 | `unity-performance-optimizer` | Unity Profiler 전문지식 필요 |
| Unity 코드 리뷰 | `unity-code-reviewer` | MonoBehaviour 라이프사이클 이해 필요 |
| Unity UI | `unity-ui-developer` | UI Toolkit/Canvas 전문지식 필요 |

### Non-Unity → OMC Agents

| Task | Agent | Why Not Unity? |
|------|-------|---------------|
| Git 작업 | `oh-my-claudecode:git-master` | Unity 대안 없음 |
| 비-Unity 파일 편집 | `oh-my-claudecode:executor` | Unity 에이전트 범위 밖 |
| 일반 리서치 | `oh-my-claudecode:researcher` | 보편적 웹 리서치 |

### Supplementary (Both)

| Task | Primary | Supplement |
|------|---------|-----------|
| API 리서치 | `unity-researcher` | `oh-my-claudecode:researcher` |
| 검증 | `unity-verifier` | OMC verification tiers |

## OMC Execution Modes in Unity

### autopilot
**"autopilot: 멀티플레이어 인벤토리 시스템 구현"**

1. unity-omc-bridge가 Unity 프로젝트 감지
2. unity-team-lead가 팀 자동 구성
3. 전문가 3-4명 + verifier 1명 스폰
4. 사용자 개입 없이 전체 파이프라인 수행
5. unity-verifier 검증 후 완료

### ralph
**"ralph: 모바일 최적화"**

autopilot과 동일 + 지속 실행:
- 모든 태스크 completed + 검증 통과까지 중단 금지
- 실패 시 자동 재시도 (최대 3회)
- 남은 태스크가 있으면 새 팀원 스폰

### ultrawork
**"ulw 게임 전체 최적화"**

- 가능한 모든 독립 태스크 동시 시작
- 최대 5명 동시 실행 (Claude Code 제한)
- 태스크 완료 즉시 다음 태스크 할당

### ecomode
**"eco 간단한 버그 수정"**

- 팀원 수 최소화 (2-3명)
- 간단한 작업 → haiku 모델
- 핵심 작업만 sonnet 모델
- 간결한 프롬프트 사용

## Smart Model Routing

| 태스크 복잡도 | 모델 | 예시 |
|--------------|------|------|
| simple | haiku | SerializeField 추가, 간단한 버그 수정 |
| standard | sonnet | 기능 구현, 시스템 개발 (기본값) |
| complex | opus | 아키텍처 설계, 멀티플레이어, 성능 최적화 |

## Verification Tiers

| Tier | 조건 | 검사 항목 |
|------|------|----------|
| LIGHT | < 5 파일, < 100 라인 | 문법, 존재 확인, 네임스페이스 |
| STANDARD | 일반 기능 (기본값) | + 라이프사이클, 이벤트, 성능 패턴 |
| THOROUGH | 아키텍처, 멀티플레이어, 플랫폼 | + 스레딩, 네트워크, 플랫폼 경로 |

## New Agents

| Agent | Location | Role |
|-------|----------|------|
| `unity-omc-bridge` | `agents/orchestrators/` | OMC↔Unity 모드 변환 |
| `unity-researcher` | `agents/core/` | Context7 기반 Unity 문서 조회 |
| `unity-verifier` | `agents/core/` | 3단계 품질 검증 게이트 |

## Project Commands

| Command | Description |
|---------|-------------|
| `/unity-autopilot` | Unity autopilot 모드 실행 |
| `/unity-optimize` | Unity 최적화 워크플로우 |
| `/unity-review` | Unity + 일반 통합 코드 리뷰 |

## Troubleshooting

### Unity 에이전트가 아닌 OMC 에이전트가 선택될 때
- CLAUDE.md의 라우팅 우선순위 테이블 확인
- Unity 프로젝트 파일 (ProjectVersion.txt) 존재 여부 확인
- 에이전트 심링크가 정상인지 확인: `ls -la ~/.claude/agents`

### tmux 팀 모드가 작동하지 않을 때
- `claude --teammate-mode tmux` 옵션으로 시작했는지 확인
- `tmux -V`로 tmux 설치 확인
- `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` 환경변수 확인

### OMC 스킬이 인식되지 않을 때
- `/oh-my-claudecode:help`로 OMC 스킬 목록 확인
- `~/.claude/plugins/installed_plugins.json`에서 OMC 항목 확인
- OMC 클린 재설치 필요 여부 판단

### 에이전트 충돌 발생 시
- `code-reviewer` vs `unity-code-reviewer`: Unity 코드는 `unity-code-reviewer`
- `performance-optimizer` vs `unity-performance-optimizer`: Unity 런타임은 `unity-performance-optimizer`
- `documentation-specialist`: Unity 문서 전용, 비-Unity는 OMC `writer`
