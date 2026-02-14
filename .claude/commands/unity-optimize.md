# Unity Optimize

Unity 프로젝트 최적화 워크플로우를 실행합니다. 프로파일링 → 분석 → 최적화 → 검증 파이프라인.

## Execution Flow

### Step 1: Profiling Analysis
- `unity-performance-optimizer`로 프로파일링 분석
- CPU/GPU 병목 식별
- 메모리 사용 패턴 분석
- 드로우콜, 배칭, LOD 상태 확인

### Step 2: Optimization Plan
- 병목별 우선순위 매기기 (Critical > Major > Minor)
- 최적화 대상 전문가 매핑:
  - 렌더링 → `unity-graphics-programmer`
  - 물리 → `unity-physics-programmer`
  - 스크립트 → `unity-gameplay-programmer`
  - 메모리 → `unity-performance-optimizer`
  - 모바일 → `unity-mobile-developer`

### Step 3: Parallel Optimization
- 독립적인 최적화 작업은 병렬 실행 (ultrawork 방식)
- 각 전문가가 자신의 도메인 최적화 수행
- 파일 충돌이 없는 범위에서 동시 작업

### Step 4: Before/After Verification
- `unity-verifier` THOROUGH 등급으로 검증
- 최적화 전후 비교 메트릭 제시:
  - FPS 변화
  - 드로우콜 변화
  - 메모리 사용량 변화
  - 빌드 크기 변화
- 성능 회귀 없음 확인

### Step 5: Report
- 최적화 결과 종합 리포트
- 추가 권장 사항

## Target Metrics

| Platform | Target FPS | Max Draw Calls | Memory Budget |
|----------|-----------|----------------|---------------|
| Mobile | 30-60 | < 300 | < 2GB |
| Console | 60 | < 2000 | < 4GB |
| Desktop | 60-144 | < 5000 | < 8GB |
| VR | 72-90 | < 100 | < 2GB |

## Usage

```
/unity-optimize 모바일 성능 개선
/unity-optimize 렌더링 최적화
/unity-optimize 메모리 사용량 줄이기
```

$ARGUMENTS
