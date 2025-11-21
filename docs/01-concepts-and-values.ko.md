# ROD, TFD, DGTF: 개념과 가치

## 목차
1. [소개](#소개)
2. [ROD (Responsibility-Oriented Design)](#rod-responsibility-oriented-design)
3. [TFD (Test-First Development)](#tfd-test-first-development)
4. [DGTF (DONT GO TOO FAST)](#dgtf-dont-go-too-fast)
5. [세 방법론의 연결](#세-방법론의-연결)
6. [인지과학적 근거](#인지과학적-근거)

---

## 소개

ROD, TFD, DGTF는 수십 년간의 소프트웨어 개발 경험에서 나온 실용적 방법론입니다.

이 방법론들의 공통점:
- **실전에서 검증됨** - 이론이 아닌 현장 경험
- **언어 독립적** - Go, Java, Python 등 모든 언어에 적용
- **상호 보완적** - 각각이 독립적이면서도 함께 작동
- **배우기 쉬움** - 복잡한 다이어그램 필요 없음

---

## ROD (Responsibility-Oriented Design)

### 핵심 개념

**"More is better than missing"**

완전한 서비스 체인을 먼저 설계하고, 불필요한 것은 나중에 제거합니다.

### 왜 ROD인가?

#### 심리학적 통찰

**추가의 어려움**:
```
빈 화면 앞에서:
- "뭘 만들어야 하지?" (분석 마비)
- "완벽하게 설계해야 해" (두려움)
- "실수하면 어떡하지?" (불안)

→ 시작조차 못 함
→ 또는 너무 오래 고민
```

**제거의 용이성**:
```
이미 있는 코드 앞에서:
- "이거 안 쓰네?" (명확한 판단)
- "테스트 돌려보니 필요 없어" (증명 가능)

→ 자신 있게 제거
→ 빠른 결정
```

### ROD의 원칙

1. **완전한 서비스 체인 설계**
   - 모든 필요한 컴포넌트를 먼저 나열
   - 입력 → 처리 → 출력의 전체 흐름
   - 빠진 부분이 없도록

2. **언어 독립적 설계**
   - 특정 언어 기능에 의존하지 않음
   - 책임과 관계에 집중
   - 다른 언어로 포팅 가능

3. **나중에 제거하기**
   - 사용하지 않는 컴포넌트는 제거
   - 중복된 책임은 병합
   - 단순화하기

### ROD vs 다른 방법론

| 방법론 | 초점 | ROD의 차이점 |
|--------|------|--------------|
| SOLID | "어떻게(How)" 설계할까 | "무엇을(What)" 먼저 정의할까 |
| DDD | 도메인 모델링 | 책임 체인 완성도 |
| YAGNI | 지금 필요한 것만 | 나중에 필요할 것도 준비 |

### 실전 예시

#### 예시 1: 데이터베이스 접근 시스템

**잘못된 접근 (책임 누락)**:
```
설계 단계:
"사용자 정보 조회" → 간단하네!

필요한 것:
1. 쿼리 실행
2. 결과 반환

구현:
func GetUser(id string) (User, error) {
    db := sql.Open("mysql", "connection-string")
    row := db.QueryRow("SELECT * FROM users WHERE id = ?", id)
    // ...
}
```

**문제 발생**:
- 연결을 매번 생성? (비효율)
- Master/Slave 구분은? (나중에 추가)
- 트랜잭션은? (나중에 추가)
→ 나중에 전체 리팩토링 필요!

**ROD 접근 (체계적 분석)**:
```
설계 단계 (System 2 활성화):
"사용자 정보 조회"를 꼼꼼히 분석:

필요한 책임 (More):
1. 쿼리 실행 - QueryExecutor
2. 결과 매핑 - ResultMapper
3. ⚠️ 연결 제공 - ConnectionProvider (빠뜨리기 쉬움!)
4. ⚠️ DB 선택 - DatabaseSelector (Master/Slave) (빠뜨리기 쉬움!)
5. ⚠️ 트랜잭션 관리 - TransactionManager (빠뜨리기 쉬움!)
6. 에러 처리 - ErrorHandler

각 책임별 인터페이스 정의:

type ConnectionProvider interface {
    GetConnection(ctx context.Context) (*sql.DB, error)
    ReleaseConnection(conn *sql.DB) error
}

type DatabaseSelector interface {
    SelectForRead(ctx context.Context) (*sql.DB, error)
    SelectForWrite(ctx context.Context) (*sql.DB, error)
}

type TransactionManager interface {
    Begin(ctx context.Context) (Transaction, error)
    Commit(tx Transaction) error
    Rollback(tx Transaction) error
}
```

**결과**:
→ ConnectionProvider가 있어서 Pool 관리 가능
→ DatabaseSelector가 있어서 Master/Slave 분리 가능
→ TransactionManager가 있어서 트랜잭션 처리 가능
→ 나중에 기능 추가가 쉬움 (아키텍처 변경 없음)

#### 예시 2: 플러그인 시스템

**잘못된 접근 (책임 누락)**:
```
설계 단계:
"플러그인으로 이미지 처리" → 간단하네!

필요한 것:
1. 플러그인 로드
2. 이미지 처리

구현:
func ProcessImage(img Image) Image {
    plugins := []ImagePlugin{
        NewResizePlugin(),
        NewFilterPlugin(),
    }
    for _, p := range plugins {
        img = p.Process(img)
    }
    return img
}
```

**문제 발생**:
- 새 플러그인 추가 시 코드 수정
- 플러그인을 어디서 찾지? (하드코딩)
- 플러그인 검증은? (신뢰할 수 있나?)
- 실행 순서는? (항상 고정?)
→ 나중에 전체 재설계 필요!

**ROD 접근 (체계적 분석)**:
```
설계 단계 (System 2 활성화):
"플러그인으로 이미지 처리"를 꼼꼼히 분석:

필요한 책임 (More):
1. 플러그인 실행 - PluginExecutor
2. 이미지 전달 - ImagePipeline
3. ⚠️ 플러그인 발견 - PluginDiscovery (빠뜨리기 쉬움!)
4. ⚠️ 플러그인 검증 - PluginValidator (빠뜨리기 쉬움!)
5. ⚠️ 플러그인 생성 - PluginFactory (빠뜨리기 쉬움!)
6. ⚠️ 플러그인 레지스트리 - PluginRegistry (빠뜨리기 쉬움!)
7. ⚠️ 실행 순서 결정 - ExecutionOrderResolver (빠뜨리기 쉬움!)
8. 에러 처리 - ErrorHandler

각 책임별 인터페이스 정의:

type PluginDiscovery interface {
    DiscoverPlugins(path string) ([]PluginMetadata, error)
    ScanDirectory(dir string) ([]PluginMetadata, error)
}

type PluginValidator interface {
    Validate(plugin Plugin) error
    CheckSignature(plugin Plugin) (bool, error)
}

type PluginFactory interface {
    CreatePlugin(metadata PluginMetadata) (Plugin, error)
}

type PluginRegistry interface {
    Register(name string, plugin Plugin) error
    Get(name string) (Plugin, error)
    List() []string
}

type ExecutionOrderResolver interface {
    ResolveOrder(plugins []Plugin) ([]Plugin, error)
}
```

**결과**:
→ PluginDiscovery가 있어서 자동으로 플러그인 발견
→ PluginValidator가 있어서 악성 플러그인 차단
→ PluginFactory가 있어서 다양한 방식으로 생성 가능
→ PluginRegistry가 있어서 동적으로 관리
→ ExecutionOrderResolver가 있어서 순서 변경 가능
→ 확장성과 보안성 확보

#### 핵심 교훈

**자주 빠뜨리는 책임들**:

1. **Creation (생성)**: Factory, Builder, Constructor
2. **Discovery/Selection (발견/선택)**: Registry, Locator, Selector
3. **Validation (검증)**: Validator, Checker
4. **Resolution (해결)**: Resolver, Router, Dispatcher
5. **Lifecycle (생명주기)**: Manager, Initializer, Destroyer

이런 책임들을 놓치면:
- 구현 단계에서 "간단한 방법" 선택
- 책임이 섞임
- 나중에 변경 불가능
- 전체 리팩토링 필요

---

## TFD (Test-First Development)

### 핵심 개념

**"요구사항 = 테스트"**

테스트를 요구사항 정의의 수단으로 사용합니다.

### 왜 TFD인가?

#### TDD와의 차이

| 특성 | TDD | TFD |
|------|-----|-----|
| 엄격성 | Red → Green → Refactor (엄격) | 설계 + 테스트 동시 고려 (유연) |
| 시작점 | 실패하는 테스트 먼저 | 요구사항 분석과 테스트 설계 |
| 초점 | 테스트 주도 | 검증 가능성 |
| 실용성 | 모든 코드에 테스트 | 중요한 부분에 테스트 |

### TFD의 원칙

1. **요구사항을 테스트로 표현**
   ```
   요구사항: "사용자 로그인 시 세션 생성"
   
   테스트:
   - 성공 케이스: 올바른 자격증명 → 세션 생성
   - 실패 케이스: 잘못된 자격증명 → 오류 반환
   - 경계 케이스: 빈 입력 → 오류 반환
   ```

2. **애매한 사항 제거**
   - "제대로 작동" → 무엇이 제대로?
   - "빠르게" → 얼마나 빠르게?
   - "안정적으로" → 어떤 조건에서?

3. **검증 가능한 개발**
   - 모든 요구사항이 테스트로 표현됨
   - 테스트 통과 = 요구사항 충족
   - 명확한 완료 기준

### V-Model과의 연결

TFD는 V-Model의 철학을 따릅니다:

```
요구사항 정의 ←→ 인수 테스트
    ↓                ↑
시스템 설계   ←→ 시스템 테스트
    ↓                ↑
상세 설계     ←→ 통합 테스트
    ↓                ↑
구현         ←→ 단위 테스트
```

### 실전 예시

**애매한 요구사항**:
```
"Worker가 올바르게 작동해야 한다"
→ 무엇이 올바른가?
```

**TFD로 명확화**:
```
테스트 케이스:
1. Worker 시작 → Running 상태
2. Task 받음 → 처리 시작
3. Task 완료 → 결과 반환
4. Stop 신호 → Graceful shutdown
5. 에러 발생 → 에러 전파
6. Timeout → 작업 중단

→ 6가지 요구사항으로 명확화됨
```

---

## DGTF (DONT GO TOO FAST)

### 핵심 개념

**"천천히, 하지만 확실하게"**

성급한 결정과 구현을 피하고, 의식적이고 신중한 사고를 유지합니다.

### 왜 DGTF인가?

#### "빠르게"의 함정

현대 개발 문화:
- "Move Fast and Break Things"
- "Fail Fast"
- "Ship Early, Ship Often"

문제점:
- 기술 부채 누적
- 잦은 리팩토링
- 버그 증가
- 팀원 번아웃

#### "빠르게" vs "성급하게"

```
빠르게 (Good):
- 명확한 목표
- 효율적인 프로세스
- 불필요한 작업 제거

성급하게 (Bad):
- 설계 생략
- 테스트 건너뛰기
- 코드 리뷰 생략
- "나중에 고치지 뭐"
```

### DGTF의 원칙

1. **System 2 활성화**
   - 직관적 판단을 멈추고
   - 의식적 분석 시작
   - 대안 검토

2. **명확한 합의**
   - "이렇게 하면 되지?" → "이렇게 하는 게 맞나요?"
   - 가정을 검증
   - 동의 후 진행

3. **자아와 코드 분리**
   - 내 코드 ≠ 나
   - 비판 = 공격이 아님
   - 더 나은 방법이 있으면 받아들임

### 운전 비유

```
운전면허 ≠ 좋은 운전자

좋은 운전자:
- 안전거리 유지
- 신호 준수
- 양보
- 예측 운전

나쁜 운전자:
- "내 실력이면 괜찮아"
- 과속
- 끼어들기
- 공격적 운전
```

마찬가지로:

```
코딩 능력 ≠ 좋은 프로그래머

좋은 프로그래머:
- 설계 먼저
- 테스트 작성
- 코드 리뷰
- 점진적 개발

나쁜 프로그래머:
- "대충 알아"
- 빠른 구현
- 테스트 생략
- 나중에 고치기
```

### 실전 적용

#### 트리거 인식

DGTF가 필요한 순간:
- "이거 쉬워 보이는데?" → 위험 신호
- "빨리 끝내야 해!" → 위험 신호
- "대충 이럴 거야" → 위험 신호
- "다 알아!" → 위험 신호

#### 대응 방법

```
1. 멈춤 (Stop)
   "잠깐, 정말 맞나?"

2. 생각 (Think)
   "다른 경우는?"
   "테스트는?"
   "영향 범위는?"

3. 합의 (Agree)
   팀원과 확인
   문서화

4. 진행 (Go)
   하나씩 천천히
```

---

## 세 방법론의 연결

### 개발 단계별 적용

```
┌─────────────────────────────────────┐
│         개발 프로세스                │
├─────────────────────────────────────┤
│                                     │
│  1. 설계 (ROD)                      │
│     System 2 활성화 ← 체계적 분석   │
│     "전체를 꼼꼼히 확인"             │
│     → 완전한 구조                   │
│                                     │
│  2. 구현 (DGTF)                     │
│     System 2 유지 ← 신중한 사고     │
│     "천천히, 하나씩"                │
│     → 정확한 코드                   │
│                                     │
│  3. 검증 (TFD)                      │
│     System 2 유지 ← 체계적 확인     │
│     "테스트로 증명"                 │
│     → 신뢰할 수 있는 시스템          │
│                                     │
└─────────────────────────────────────┘
```

### 상호 보완

| 단계 | 방법론 | 역할 | 결과 |
|------|--------|------|------|
| 설계 | ROD | 완전성 확보 | 빠진 것 없음 |
| 구현 | DGTF | 품질 확보 | 버그 최소화 |
| 검증 | TFD | 신뢰성 확보 | 증명 가능 |

---

## 인지과학적 근거

### Daniel Kahneman의 이론

"Thinking, Fast and Slow"의 핵심:

**System 1 (빠른 사고)**:
- 자동적, 직관적
- 노력 필요 없음
- 감정적
- 빠르지만 에러 발생

**System 2 (느린 사고)**:
- 의식적, 분석적
- 노력 필요
- 논리적
- 느리지만 정확

### 소프트웨어 개발에의 적용

#### System 1의 함정

```
개발자의 System 1:
- "이거 간단한데!" → 복잡도 과소평가
- "저번에 이렇게 했는데" → 맥락 무시
- "다 알아!" → 문서 안 읽음
- "빨리 고쳐야 해!" → 성급한 패치
```

#### System 2의 활성화

```
ROD, TFD, DGTF가 System 2를 켜는 방법:

ROD:
- "정말 다 있나?" (완전성 체크)
- "빠진 건 없나?" (검토)

TFD:
- "테스트로 증명할 수 있나?" (검증)
- "모든 케이스 커버하나?" (체계성)

DGTF:
- "잠깐, 이게 맞나?" (멈춤)
- "다른 방법은?" (대안 검토)
```

### 각 방법론과 System 1/2

| 방법론 | 사용하는 System | 이유 |
|--------|----------------|------|
| ROD (설계) | System 2 | 체계적 분석으로 전체 체인 파악, 빠진 것 없이 |
| TFD (검증) | System 2 | 체계적으로 하나씩 검증 |
| DGTF (구현) | System 2 | 신중하게 정확히 구현 |

**핵심**: 
- 모든 단계에서 System 2를 활성화
- System 1의 성급한 판단을 피함
- 의식적이고 체계적인 사고 유지

---

## 결론

ROD, TFD, DGTF는:

✅ **과학적 근거** - Kahneman의 인지심리학
✅ **실전 검증** - 수십 년의 개발 경험
✅ **실용적** - 배우고 적용하기 쉬움
✅ **상호 보완적** - 각각이 독립적이면서도 함께 작동
✅ **보편적** - 모든 언어와 도메인에 적용

핵심 메시지:

> "빠르게"가 아니라 "제대로"
> 
> 속도보다 품질
> 
> 성급함보다 신중함
> 
> **DONT GO TOO FAST**

---

## 다음 단계

- [실전 가이드](02-practical-guide.ko.md)에서 실제 프로젝트에 적용하는 방법을 배워보세요
