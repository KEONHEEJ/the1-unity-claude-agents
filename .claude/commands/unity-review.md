# Unity Review

Unity 프로젝트 통합 코드 리뷰를 실행합니다. Unity 패턴 검토 + 일반 품질 검토를 결합합니다.

## Execution Flow

### Step 1: Unity-Specific Review
- `unity-code-reviewer`로 Unity 패턴 검토:
  - MonoBehaviour 라이프사이클 정확성
  - 이벤트 구독/해제 쌍
  - Null 참조 안전성 (파괴된 오브젝트)
  - Update 루프 내 할당
  - SerializeField 사용 패턴
  - Unity 6000.1 API 호환성

### Step 2: General Code Quality Review
- `code-reviewer`로 일반 소프트웨어 품질 검토:
  - SOLID 원칙 준수
  - DRY/KISS 패턴
  - 에러 처리
  - 네이밍 컨벤션
  - 코드 복잡도

### Step 3: Combined Report
- 두 리뷰 결과를 통합하여 단일 리포트 생성
- OMC severity 태깅 적용:
  - 🔴 Critical (즉시 수정 필요)
  - 🟡 Major (수정 권장)
  - 🔵 Minor (고려 사항)
  - 💚 Suggestion (개선 제안)
- Unity-specific과 general 이슈를 구분하여 표시

### Step 4: Verification
- `unity-verifier` STANDARD 등급으로 검증
- 자동 수정 가능한 이슈 식별

## Review Scope

### Unity Domain (unity-code-reviewer 담당)
- `.cs` 파일 내 Unity API 사용
- MonoBehaviour, ScriptableObject 패턴
- 성능 패턴 (캐싱, 풀링, 배칭)
- 플랫폼 호환성

### General Domain (code-reviewer 담당)
- 아키텍처 및 디자인 패턴
- 코드 중복 및 복잡도
- 에러 처리 및 로깅
- 테스트 가능성

## Usage

```
/unity-review 인벤토리 시스템
/unity-review Assets/Scripts/Combat/
/unity-review 최근 변경사항
```

$ARGUMENTS
