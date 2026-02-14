# Unity Game Development AI Team Configuration

This repository contains specialized AI agents for Unity 6000.1 game development. Each agent represents a domain expert in game development, working together as a cohesive team to help you build high-quality Unity games.

## Working with Unity Game Dev Agents

When using these agents with Claude Code, the system will automatically select the most appropriate specialist for your task. For complex multi-step development tasks, the Unity Tech Lead Orchestrator coordinates the team's efforts.

### Key Orchestration Pattern

**CRITICAL**: For any multi-step Unity development task (implementing features, optimizing performance, setting up systems), you should ALWAYS start with the **unity-tech-lead-orchestrator**. This agent will:

1. Analyze your Unity project requirements
2. Break down the work into specialized tasks
3. Route each task to the appropriate Unity specialist
4. Coordinate the overall implementation

Only skip the orchestrator for simple, single-domain questions that clearly fall under one specialist's expertise.

## Agent Organization

Agents are organized hierarchically by their scope and specialization:

### 1. **Orchestrators** (agents/orchestrators/)
High-level coordinators that manage complex Unity development workflows:
- `unity-tech-lead-orchestrator` - Technical leadership and task routing
- `unity-project-analyst` - Unity project analysis and technology detection
- `unity-team-configurator` - Team setup based on project needs

### 2. **Core** (agents/core/)
Cross-cutting concerns that apply to all Unity projects:
- `unity-performance-optimizer` - Performance profiling and optimization
- `unity-code-reviewer` - Unity-specific code review and best practices
- `unity-build-engineer` - Build pipeline and CI/CD
- `unity-documentation-specialist` - Technical documentation

### 3. **Specialized** (agents/specialized/)
Unity domain experts organized by specialization:

#### Gameplay (agents/specialized/gameplay/)
- `unity-gameplay-programmer` - Core gameplay systems
- `unity-physics-programmer` - Physics and collision systems
- `unity-ai-programmer` - AI and behavior systems
- `unity-animation-programmer` - Animation and Timeline

#### Graphics (agents/specialized/graphics/)
- `unity-graphics-programmer` - Rendering and shaders
- `unity-technical-artist` - Art pipeline and tools
- `unity-vfx-artist` - Visual effects and particles
- `unity-lighting-artist` - Lighting and post-processing

#### Networking (agents/specialized/networking/)
- `unity-multiplayer-engineer` - Netcode and multiplayer
- `unity-backend-engineer` - Server infrastructure
- `unity-networking-optimizer` - Network performance

#### Mobile (agents/specialized/mobile/)
- `unity-mobile-developer` - iOS/Android optimization
- `unity-mobile-graphics-optimizer` - Mobile GPU optimization
- `unity-mobile-monetization` - IAP and ads integration

#### XR (agents/specialized/xr/)
- `unity-vr-developer` - Virtual reality
- `unity-ar-developer` - Augmented reality
- `unity-xr-interaction-designer` - XR interactions

### 4. **Universal** (agents/universal/)
Framework-agnostic game development specialists:
- `game-designer` - Game mechanics and systems design
- `level-designer` - Level design and world building
- `ui-ux-designer` - User interface and experience
- `audio-designer` - Sound and music implementation

## Three-Phase Unity Development Workflow

The Unity Tech Lead Orchestrator follows a structured approach:

### Phase 1: Research & Analysis
- Analyze Unity project structure and version
- Identify render pipeline (Built-in/URP/HDRP)
- Detect platforms and build targets
- Review existing systems and dependencies

### Phase 2: Planning & Architecture
- Design technical approach
- Select appropriate Unity features
- Plan performance budgets
- Identify required specialists

### Phase 3: Implementation & Optimization
- Coordinate specialist implementations
- Ensure Unity best practices
- Optimize for target platforms
- Validate performance metrics

## Agent Communication Protocol

Agents communicate through structured handoffs:

```typescript
interface AgentHandoff {
  from: string;           // Source agent
  to: string;            // Target agent
  task: string;          // Specific task description
  context: {
    unityVersion: string;      // "6000.1"
    renderPipeline: string;    // "URP" | "HDRP" | "Built-in"
    platforms: string[];       // ["iOS", "Android", "PC"]
    constraints: string[];     // Performance or technical constraints
  };
  findings: any;         // Structured findings from analysis
}
```

## Team-Based Workflow (Claude Code Teams)

`--teammate-mode tmux`와 함께 사용하면 실제 병렬 팀 작업이 가능합니다.

### Quick Start
1. `claude --dangerously-skip-permissions --teammate-mode tmux`
2. `@unity-team-lead implement [feature]`
3. 팀 리드가 자동으로 팀 구성 → 태스크 분배 → 전문가 스폰

### Architecture
- **unity-team-lead**: TeamCreate + TaskCreate + Task(spawn) + SendMessage로 팀 오케스트레이션
- **specialist agents**: TaskList + TaskUpdate + SendMessage로 팀원 모드 동작
- **tmux**: 각 팀원이 별도 패널에 표시됨

### Team Lead vs Tech Lead Orchestrator
| | `unity-team-lead` | `unity-tech-lead-orchestrator` |
|---|---|---|
| **실행 방식** | 실제 팀 생성 + 병렬 실행 | 분석 + 텍스트 출력 |
| **팀원 관리** | TeamCreate, Task(spawn), SendMessage | 없음 (단일 에이전트) |
| **태스크 관리** | TaskCreate + TaskUpdate + TaskList | 구조화된 JSON 출력 |
| **사용 조건** | `--teammate-mode tmux` 필요 | 항상 사용 가능 |
| **적합한 경우** | 복잡한 멀티도메인 기능 개발 | 분석, 계획, 단일 도메인 작업 |

### How It Works
1. **분석**: unity-team-lead가 프로젝트 구조 파악 (ProjectVersion.txt, manifest.json 등)
2. **계획**: 태스크 분해 + 전문가 에이전트 선정
3. **팀 생성**: `TeamCreate(team_name="unity-{feature}")` 호출
4. **태스크 등록**: `TaskCreate`로 개별 태스크 생성 (의존성 포함)
5. **전문가 스폰**: `Task(subagent_type="unity-xxx", team_name=..., name=...)` 호출
6. **배정**: `TaskUpdate(owner=teammate-name)` + `SendMessage`로 알림
7. **모니터링**: `TaskList`로 진행 확인, `SendMessage`로 조율
8. **완료**: 검증 → `shutdown_request` → `TeamDelete`

## Example Orchestration Flow

When you request: "Create a multiplayer racing game for mobile"

1. **unity-tech-lead-orchestrator** receives the request
2. Breaks down into specialized tasks:
   - Mobile performance requirements
   - Multiplayer architecture
   - Physics and vehicle controls
   - Touch input system
3. Routes to specialists:
   - `unity-mobile-developer` for platform setup
   - `unity-multiplayer-engineer` for networking
   - `unity-physics-programmer` for vehicle physics
   - `unity-mobile-graphics-optimizer` for rendering
4. Coordinates integration and optimization

## Unity-Specific Considerations

All agents are aware of:
- Unity 6000.1 features and APIs
- Platform-specific requirements
- Performance constraints
- Unity best practices
- Asset pipeline workflows
- Package dependencies

## Development Guidelines

### Creating New Unity Agents

1. Identify specific Unity domain expertise needed
2. Place in appropriate category folder
3. Use YAML frontmatter format
4. Include Unity 6000.1 code examples
5. Define clear integration patterns
6. Test with real Unity projects

### Agent Quality Standards

- Must use Unity 6000.1 APIs and features
- Include platform-specific considerations
- Provide working code examples
- Follow Unity coding conventions
- Consider performance implications
- Document Unity-specific gotchas

## Getting Started

1. Copy this CLAUDE.md to your Unity project root
2. The unity-tech-lead-orchestrator will analyze your project
3. Specialists will be assigned based on your needs
4. Follow the three-phase workflow for complex tasks

The Unity Game Dev AI Team is designed to accelerate your game development while maintaining high quality standards and optimal performance across all target platforms.

---

## OMC (oh-my-claudecode) Integration Protocol

This project integrates with OMC's orchestration framework. Unity agents are the **primary** workforce; OMC agents serve as **supplementary** support for non-Unity tasks.

### Agent Routing Priority

**CRITICAL RULE: Unity agents ALWAYS take priority for Unity domain work.**

| Task Type | Route To | DO NOT Use |
|-----------|----------|-----------|
| C# gameplay code | `unity-gameplay-programmer` | `oh-my-claudecode:executor` |
| Shader / HLSL code | `unity-shader-programmer` | `oh-my-claudecode:executor` |
| Unity performance | `unity-performance-optimizer` | `oh-my-claudecode:performance-reviewer` |
| Unity code review | `unity-code-reviewer` | `oh-my-claudecode:code-reviewer` |
| Unity UI (UXML/Canvas) | `unity-ui-developer` | `oh-my-claudecode:designer` |
| Unity documentation | `documentation-specialist` | `oh-my-claudecode:writer` |
| Unity API research | `unity-researcher` | (complement with `oh-my-claudecode:researcher`) |
| Verification | `unity-verifier` | (OMC verifier as supplement) |
| Git operations | `oh-my-claudecode:git-master` | (no Unity alternative) |
| Non-Unity file editing | `oh-my-claudecode:executor` | Unity specialists |
| General non-Unity research | `oh-my-claudecode:researcher` | Unity specialists |

**Ambiguous cases → Unity agent wins.**

### Unity File Extension Write Rules

Unity specialist agents are authorized to directly modify these file types:
- `.cs`, `.shader`, `.hlsl`, `.cginc`, `.compute` — Code files
- `.asmdef`, `.asmref` — Assembly definitions
- `.asset`, `.prefab`, `.mat`, `.controller` — Unity assets
- `.uxml`, `.uss` — UI Toolkit files
- `.unity` — Scene files
- `.meta` — Unity metadata

### OMC Execution Mode → Unity Workflow Mapping

| OMC Mode | Unity Workflow | Behavior |
|----------|---------------|----------|
| **autopilot** | `unity-team-lead` (tmux) / `unity-tech-lead-orchestrator` | Autonomous Unity development with auto-verification |
| **ralph** | `unity-team-lead` + persistence enforcement | All tasks + verification must pass before stopping |
| **ultrawork** | Parallel Unity specialists via TeamCreate | Maximum concurrent specialist execution |
| **ecomode** | Minimal team with haiku model routing | Token-efficient Unity development |
| **plan** | `unity-project-analyst` + `unity-team-configurator` | Unity-aware planning interview |

### Execution Flow

```
[OMC Mode Activated] → [unity-omc-bridge detects Unity project]
    ├── tmux available → unity-team-lead (real parallel team)
    └── single mode   → unity-tech-lead-orchestrator (sequential)
        │
        ├── Unity domain tasks → Unity specialist agents
        ├── Non-Unity tasks → OMC agents
        └── Verification → unity-verifier (always)
```

### Verification Protocol

Every feature must pass `unity-verifier` before completion. Verification tiers:

| Tier | When | Checks |
|------|------|--------|
| LIGHT | < 5 files, < 100 lines | Syntax, existence, namespace |
| STANDARD | Default for features | + Lifecycle, events, performance patterns |
| THOROUGH | Architecture, multiplayer, platform-critical | + Threading, network, platform paths |

### Supplementary Agents (New in OMC Integration)

| Agent | Location | Role |
|-------|----------|------|
| `unity-omc-bridge` | `agents/orchestrators/` | OMC mode → Unity workflow routing |
| `unity-researcher` | `agents/core/` | Unity API documentation lookup |
| `unity-verifier` | `agents/core/` | Tiered quality verification gate |