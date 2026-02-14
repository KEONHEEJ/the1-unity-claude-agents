# Unity Autopilot

Unity 프로젝트에서 OMC autopilot을 실행합니다. 전체 개발 파이프라인을 자율적으로 수행합니다.

## Execution Flow

### Step 1: Project Analysis
- `unity-project-analyst`로 프로젝트 구조 분석
- Unity 버전, 렌더 파이프라인, 타겟 플랫폼 감지
- 기존 코드 아키텍처 파악

### Step 2: Team Formation
- tmux teammate 모드 사용 가능 시 → `unity-team-lead`로 실제 팀 구성
- 단일 모드 시 → `unity-tech-lead-orchestrator`로 순차 분석 + 위임

### Step 3: Implementation
- Unity 도메인 작업은 반드시 Unity 전문가 에이전트에게 위임
- 비-Unity 작업 (git, 리서치)은 OMC 에이전트 활용
- 모든 에이전트에게 model 파라미터 명시 (haiku/sonnet/opus)

### Step 4: Verification
- `unity-verifier`가 최종 검증 수행
- 검증 tier: 자동 선택 (LIGHT/STANDARD/THOROUGH)
- 검증 실패 시 자동 재작업

### Step 5: Completion
- 모든 태스크 completed + 검증 통과
- 결과 요약 리포트 사용자에게 전달

## Key Principles

1. **Unity 에이전트 우선**: Unity 코드는 항상 Unity 전문가가 작성
2. **Documentation-first**: API 사용 전 `unity-researcher`로 문서 확인
3. **자율 실행**: 사용자 개입 없이 전체 파이프라인 수행
4. **검증 필수**: `unity-verifier` 없이 완료 선언 금지

## Usage

```
/unity-autopilot 플레이어 인벤토리 시스템 구현
/unity-autopilot 모바일 성능 최적화
/unity-autopilot 멀티플레이어 로비 시스템 구현
```

$ARGUMENTS
