# Team-Based Workflow Guide

Claude Code Teams를 활용한 Unity 개발 팀 워크플로우 가이드.

## Prerequisites

### Required Setup
```bash
# teammate-mode tmux로 Claude Code 실행
claude --dangerously-skip-permissions --teammate-mode tmux
```

- **tmux 설치 필요**: `brew install tmux` (macOS)
- **Claude Code**: `--teammate-mode tmux` 플래그 지원 버전
- **에이전트 설치**: `~/.claude/agents/` 경로에 이 레포지토리 클론 또는 심링크

### Agent Installation
```bash
# 옵션 1: 클론
git clone https://github.com/the1/unity-claude-agents.git ~/.claude/agents

# 옵션 2: 심링크 (이미 다른 곳에 클론한 경우)
ln -s /path/to/unity-claude-agents ~/.claude/agents
```

## Usage

### Basic Usage
```
@unity-team-lead implement player health system with UI
```

팀 리드가 자동으로:
1. 프로젝트 분석 (Unity 버전, 렌더 파이프라인, 아키텍처)
2. 태스크 분해 (health component, UI widget, integration)
3. 팀 생성 (`TeamCreate`)
4. 전문가 스폰 (`unity-gameplay-programmer`, `unity-ui-developer`)
5. 병렬 작업 조율
6. 완료 후 정리

### Complex Feature Example
```
@unity-team-lead create a complete multiplayer inventory system
```

이 경우 팀 리드가 다음과 같은 팀을 구성할 수 있습니다:
- `gameplay-dev` (unity-gameplay-programmer): 인벤토리 로직
- `ui-dev` (unity-ui-developer): 인벤토리 UI
- `network-dev` (unity-multiplayer-engineer): 네트워크 동기화
- `optimizer` (unity-performance-optimizer): 성능 최적화

### Optimization Sprint Example
```
@unity-team-lead optimize my mobile game for release
```

팀 구성:
- `perf-analyzer` (unity-performance-optimizer): 프로파일링 + 병목점 분석
- `mobile-dev` (unity-mobile-developer): 모바일 최적화
- `build-eng` (unity-build-engineer): 빌드 크기 최적화

## Team Lead Decision Logic

### When Team Mode Activates
| 조건 | 팀 모드 여부 |
|------|-------------|
| 2+ 도메인에 걸친 기능 | Yes |
| 최적화 스프린트 | Yes |
| 대규모 리팩토링 | Yes |
| 풀스택 기능 개발 | Yes |
| 단일 도메인 버그 수정 | No — 단일 전문가 사용 |
| 간단한 질문 | No — 직접 답변 |
| 코드 리뷰 | No — 단일 리뷰어 사용 |

### Agent Routing
팀 리드는 태스크의 도메인을 분석하여 최적의 전문가를 선택합니다:

| 도메인 | 에이전트 |
|--------|---------|
| 게임플레이 로직 | `unity-gameplay-programmer` |
| 물리 시뮬레이션 | `unity-physics-programmer` |
| AI/NPC 행동 | `unity-ai-programmer` |
| 애니메이션 | `unity-animation-programmer` |
| 렌더링/셰이더 | `unity-graphics-programmer`, `unity-shader-programmer` |
| 아트 파이프라인 | `unity-technical-artist` |
| UI/UX | `unity-ui-developer` |
| 네트워킹 | `unity-multiplayer-engineer` |
| 모바일 최적화 | `unity-mobile-developer` |
| 성능 분석 | `unity-performance-optimizer` |
| 빌드/CI | `unity-build-engineer` |
| QA/테스트 | `unity-qa-engineer` |
| 오디오 | `unity-audio-engineer` |
| VR | `unity-vr-developer` |
| AR | `unity-ar-developer` |
| 데이터 관리 | `unity-data-engineer` |
| 클라우드 서비스 | `unity-cloud-engineer` |
| 보안 | `unity-security-engineer` |
| 수익화 | `unity-monetization-specialist` |
| 현지화 | `unity-localization-specialist` |
| 접근성 | `unity-accessibility-specialist` |
| 게임 디자인 | `game-designer` |

## Lifecycle

### 1. Team Creation
```
TeamCreate(team_name="unity-combat-system", description="Implementing combat system")
```

### 2. Task Registration
```
TaskCreate(subject="Implement damage calculator", description="...", activeForm="Implementing damage calculator")
TaskCreate(subject="Create health bar UI", description="...", activeForm="Creating health bar UI")
TaskUpdate(taskId="2", addBlockedBy=["1"])  // UI depends on health component
```

### 3. Agent Spawning
```
Task(
  subagent_type="unity-gameplay-programmer",
  team_name="unity-combat-system",
  name="gameplay-dev",
  mode="bypassPermissions",
  prompt="You are on team unity-combat-system. Check TaskList for assigned tasks..."
)
```

### 4. Task Assignment
```
TaskUpdate(taskId="1", owner="gameplay-dev")
SendMessage(type="message", recipient="gameplay-dev",
  content="Task #1 assigned: Implement damage calculator",
  summary="Task #1 assigned")
```

### 5. Monitoring
```
TaskList()  // 전체 태스크 상태 확인
SendMessage(type="message", recipient="gameplay-dev",
  content="How's the damage calculator going?",
  summary="Progress check")
```

### 6. Completion
```
// 모든 태스크 완료 확인 후
SendMessage(type="shutdown_request", recipient="gameplay-dev",
  content="All tasks complete")
SendMessage(type="shutdown_request", recipient="ui-dev",
  content="All tasks complete")
TeamDelete()
```

## Teammate Protocol (For Specialists)

모든 전문가 에이전트가 팀원으로 스폰되면 따르는 프로토콜:

### Startup
1. `TaskList` → 할당된 태스크 확인
2. `TaskGet` → 태스크 상세 요구사항 읽기
3. `SendMessage` → 팀 리드에게 상태 보고

### Working
1. `TaskUpdate(status="in_progress")` → 작업 시작 표시
2. 전문 지식을 활용하여 작업 수행
3. 막히면 팀 리드에게 메시지 전송
4. 다른 팀원의 결과물이 필요하면 직접 메시지

### Completion
1. `TaskUpdate(status="completed")` → 완료 표시
2. `SendMessage` → 팀 리드에게 완료 보고
3. `TaskList` → 다음 미배정/미차단 태스크 확인
4. 없으면 대기 (idle 상태)

### Communication
- 항상 `SendMessage(type="message")` 사용
- `summary` 필드 포함 (5-10 단어)
- `shutdown_request` 수신 시 `shutdown_response`로 응답

## Token Usage Guidelines

### Cost Factors
- 각 팀원은 별도의 API 세션 (토큰 소비)
- `SendMessage`마다 수신 에이전트에 컨텍스트 추가
- `broadcast`는 모든 팀원에게 전송 — 비용 N배

### Optimization Tips
1. **최소 팀 구성**: 필요한 전문가만 스폰 (3-4명 최적)
2. **명확한 프롬프트**: 스폰 시 충분한 컨텍스트 제공 → 추가 질의 감소
3. **의존성 관리**: 블로킹 태스크 완료 후 후속 팀원 스폰
4. **broadcast 자제**: 1:1 메시지 선호
5. **조기 종료**: 태스크 완료된 팀원은 즉시 shutdown

## Troubleshooting

### "Team already exists" 오류
이전 세션에서 TeamDelete가 호출되지 않은 경우:
```
TeamDelete()  // 기존 팀 정리 후 재생성
```

### 팀원이 응답하지 않음
1. `TaskList`로 해당 팀원의 태스크 상태 확인
2. `SendMessage`로 메시지 전송 (idle 팀원을 깨움)
3. 계속 응답 없으면 새 팀원 스폰하여 태스크 재배정

### 팀원 간 충돌 (같은 파일 수정)
팀 리드가 관리:
1. 한 팀원에게 먼저 작업 완료 요청
2. 완료 후 다른 팀원에게 최신 코드 기반으로 작업 시작 안내
3. 의존성으로 관리: `TaskUpdate(addBlockedBy=...)`

### tmux 패널 관리
- 각 팀원은 별도 tmux 패널에 표시
- `Ctrl+b` + 화살표키로 패널 이동
- `Ctrl+b` + `z`로 패널 줌/언줌
