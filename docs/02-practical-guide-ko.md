# 실전 가이드: ROD, TFD, DGTF 적용하기

## 목차
1. [시작하기 전에](#시작하기-전에)
2. [ROD 적용하기](#rod-적용하기)
3. [TFD 적용하기](#tfd-적용하기)
4. [DGTF 적용하기](#dgtf-적용하기)
5. [팀에 도입하기](#팀에-도입하기)
6. [흔한 실수와 해결책](#흔한-실수와-해결책)
7. [성공 측정하기](#성공-측정하기)

---

## 시작하기 전에

### 마인드셋

ROD, TFD, DGTF를 적용하기 위해 필요한 것:

✅ **겸손함**: "내가 모를 수 있다"  
✅ **인내심**: "천천히 해도 괜찮다"  
✅ **개방성**: "더 나은 방법이 있을 수 있다"  
✅ **규율**: "원칙을 지킨다" 

❌ 필요하지 않은 것:
- 천재적 지능
- 완벽한 기억력
- 최신 기술 지식
- 빠른 코딩 속도

### 단계적 도입

```
1주차: ROD 연습 (설계만)
2주차: TFD 연습 (테스트 작성만)
3주차: DGTF 연습 (코드 리뷰 강화)
4주차: 통합 (모두 함께)
```

**DONT GO TOO FAST**: 한 번에 다 하지 마세요!

---

## ROD 적용하기

### Step 1: 요구사항 분석

**나쁜 예**:
```
"사용자 인증 시스템 만들기"
→ 바로 코딩 시작
```

**ROD 접근**:
```
1. 전체 체인 나열:
   - 입력: 사용자 자격증명 (아이디, 비밀번호)
   - 처리:
     * 자격증명 검증
     * 세션 생성
     * 권한 확인
     * 토큰 발급
   - 출력: 인증 토큰 또는 에러
   
2. 각 단계의 책임 정의:
   - AuthenticationService: 자격증명 검증
   - SessionManager: 세션 관리
   - AuthorizationService: 권한 확인
   - TokenGenerator: 토큰 생성
```

### Step 2: 인터페이스 먼저 정의

```go
// 1. 전체 체인 인터페이스
type Authenticator interface {
    Authenticate(credentials Credentials) (Token, error)
}

// 2. 각 책임 인터페이스
type CredentialValidator interface {
    Validate(credentials Credentials) (User, error)
}

type SessionManager interface {
    CreateSession(user User) (Session, error)
    GetSession(sessionID string) (Session, error)
    DestroySession(sessionID string) error
}

type AuthorizationChecker interface {
    CheckPermission(user User, resource string) (bool, error)
}

type TokenGenerator interface {
    GenerateToken(session Session) (Token, error)
    ValidateToken(token Token) (Session, error)
}
```

### Step 3: 구현 체인 만들기

```go
type DefaultAuthenticator struct {
    validator  CredentialValidator
    sessions   SessionManager
    authorizer AuthorizationChecker
    tokenGen   TokenGenerator
}

func (a *DefaultAuthenticator) Authenticate(creds Credentials) (Token, error) {
    // 1. 검증
    user, err := a.validator.Validate(creds)
    if err != nil {
        return Token{}, err
    }
    
    // 2. 세션 생성
    session, err := a.sessions.CreateSession(user)
    if err != nil {
        return Token{}, err
    }
    
    // 3. 토큰 발급
    token, err := a.tokenGen.GenerateToken(session)
    if err != nil {
        return Token{}, err
    }
    
    return token, nil
}
```

### Step 4: 구현 채우기 (나중에)

```go
// 처음에는 빈 구현도 OK
type SimpleValidator struct{}

func (v *SimpleValidator) Validate(creds Credentials) (User, error) {
    // TODO: 실제 DB 검증
    return User{}, nil
}

// 나중에 필요하면 구현 추가
type DatabaseValidator struct {
    db *sql.DB
}

func (v *DatabaseValidator) Validate(creds Credentials) (User, error) {
    // 실제 구현
    // ...
}
```

### ROD 체크리스트

완전한 서비스 체인을 만들었는가?
- [ ] 입력이 명확한가?
- [ ] 각 처리 단계가 정의되었는가?
- [ ] 출력이 명확한가?
- [ ] 에러 처리 경로가 있는가?

각 책임이 명확한가?
- [ ] 하나의 컴포넌트가 하나의 책임만 가지는가?
- [ ] 책임이 겹치지 않는가?
- [ ] 책임이 빠진 것은 없는가?

언어 독립적인가?
- [ ] 특정 언어 기능에 의존하지 않는가?
- [ ] 다른 언어로 포팅 가능한가?

---

## TFD 적용하기

### Step 1: 요구사항을 테스트로 변환

**애매한 요구사항**:
```
"인증 시스템이 안전해야 한다"
```

**TFD로 명확화**:
```go
func TestAuthentication_Security(t *testing.T) {
    t.Run("잘못된 비밀번호는 거부", func(t *testing.T) {
        // Given
        auth := NewAuthenticator()
        creds := Credentials{
            Username: "user@example.com",
            Password: "wrong-password",
        }
        
        // When
        _, err := auth.Authenticate(creds)
        
        // Then
        assert.Error(t, err)
        assert.Equal(t, ErrInvalidCredentials, err)
    })
    
    t.Run("빈 비밀번호는 거부", func(t *testing.T) {
        // ...
    })
    
    t.Run("SQL Injection 시도는 거부", func(t *testing.T) {
        // ...
    })
    
    t.Run("브루트 포스는 차단", func(t *testing.T) {
        // ...
    })
}
```

### Step 2: 테스트 케이스 분류

```
1. Happy Path (정상 동작)
   - 올바른 입력 → 기대한 출력

2. Error Cases (에러 처리)
   - 잘못된 입력 → 적절한 에러
   - 시스템 에러 → 복구 또는 전파

3. Edge Cases (경계 조건)
   - 빈 입력
   - 최대/최소 값
   - nil/null

4. Concurrent Cases (동시성)
   - 여러 요청 동시 처리
   - Race condition
   - Deadlock
```

### Step 3: 테스트 먼저 작성 (구현 전)

```go
func TestAuthenticator_Authenticate(t *testing.T) {
    // 이 테스트를 작성할 때 구현은 없음
    // 하지만 이 테스트가 요구사항을 정의함
    
    t.Run("성공: 올바른 자격증명", func(t *testing.T) {
        // Given
        auth := NewAuthenticator(
            NewMockValidator(validUser),
            NewMockSessionManager(),
            NewMockTokenGenerator(),
        )
        
        // When
        token, err := auth.Authenticate(validCreds)
        
        // Then
        assert.NoError(t, err)
        assert.NotEmpty(t, token)
    })
    
    t.Run("실패: 잘못된 자격증명", func(t *testing.T) {
        // ...
    })
}
```

### Step 4: 구현 작성

```go
func (a *DefaultAuthenticator) Authenticate(creds Credentials) (Token, error) {
    // 테스트가 요구하는 대로 구현
    user, err := a.validator.Validate(creds)
    if err != nil {
        return Token{}, fmt.Errorf("validation failed: %w", err)
    }
    
    // ...
}
```

### TFD 체크리스트

요구사항이 테스트로 표현되었는가?
- [ ] 모든 기능이 테스트로 정의되었는가?
- [ ] 애매한 사항이 없는가?
- [ ] 완료 기준이 명확한가?

모든 케이스를 커버하는가?
- [ ] Happy path
- [ ] Error cases
- [ ] Edge cases
- [ ] Concurrent cases (필요시)

테스트가 독립적인가?
- [ ] 테스트 순서에 무관한가?
- [ ] 다른 테스트에 영향 없는가?
- [ ] 실행 환경에 무관한가?

---

## DGTF 적용하기

### Step 1: 트리거 인식하기

**위험 신호 감지**:

```
자신에게 물어보기:
- "이거 쉬워 보이는데?" → 🚨 위험
- "빨리 끝내야 해!" → 🚨 위험
- "대충 알아" → 🚨 위험
- "테스트는 나중에" → 🚨 위험
- "리뷰 안 받아도 돼" → 🚨 위험
```

### Step 2: 멈추기 (Stop)

```
트리거 감지 시 즉시:

1. 코딩 멈춤
2. 키보드에서 손 떼기
3. 깊게 숨쉬기
4. "정말 맞나?" 자문
```

### Step 3: 생각하기 (Think)

```
체크 리스트:

[ ] 요구사항을 정확히 이해했나?
[ ] 전체 영향 범위를 아는가?
[ ] 다른 경우는 없나?
[ ] 테스트는 어떻게 할까?
[ ] 더 나은 방법은?
[ ] 누구와 상의해야 하나?
```

### Step 4: 합의하기 (Agree)

```
혼자 결정하지 말고:

1. 설계 공유
   - "이렇게 하려고 하는데 어떤가요?"
   
2. 피드백 받기
   - 경청
   - 방어하지 않기
   - 대안 검토

3. 합의 후 진행
   - 명확한 동의 확인
   - 문서화 (코멘트, 위키 등)
```

### Step 5: 진행하기 (Go)

```
합의 후:

1. 한 번에 하나씩
   - 작은 단위로 나누기
   - 하나 완료 → 테스트 → 다음

2. 자주 커밋
   - 작은 단위로 커밋
   - 명확한 메시지
   - 리뷰 가능한 크기

3. 지속적 검증
   - 테스트 실행
   - 코드 리뷰
   - 통합 테스트
```

### DGTF 실전 예시

**Bad (성급함)**:
```
Manager: "이 버그 급해요, 빨리 고쳐주세요!"
Developer: "알겠습니다!"
→ 5분 만에 패치
→ 다른 기능 깨짐
→ 3일간 버그 수정
```

**Good (DGTF)**:
```
Manager: "이 버그 급해요, 빨리 고쳐주세요!"
Developer: "잠깐만요, 정확히 파악하겠습니다"

1. 멈춤:
   - 급하다고 바로 코딩하지 않음

2. 생각:
   - 버그 원인 분석 (10분)
   - 영향 범위 확인
   - 수정 방법 2가지 검토

3. 합의:
   - Manager에게 보고
   - "방법 A는 빠르지만 위험, 방법 B는 안전하지만 1시간 소요"
   - 함께 결정

4. 진행:
   - 선택한 방법으로 신중히 수정
   - 테스트 작성
   - 리뷰 받음
   
결과: 
- 30분 소요
- 부작용 없음
- 다른 버그 없음
```

### DGTF 체크리스트

멈췄는가?
- [ ] 트리거를 인식했는가?
- [ ] 즉시 멈췄는가?
- [ ] 자문했는가?

생각했는가?
- [ ] 요구사항 확인했는가?
- [ ] 영향 범위 파악했는가?
- [ ] 대안 검토했는가?

합의했는가?
- [ ] 팀원과 공유했는가?
- [ ] 피드백 받았는가?
- [ ] 명확한 동의를 얻었는가?

---

## 팀에 도입하기

### 단계별 도입

#### Phase 1: 인식 (1-2주)

```
목표: 팀원들이 ROD, TFD, DGTF를 이해하게 하기

활동:
1. 문서 공유
2. 간단한 프레젠테이션 (30분)
3. 질의응답
4. 사례 공유

산출물:
- 없음 (강제하지 않음)
```

#### Phase 2: 실험 (2-4주)

```
목표: 작은 프로젝트에 적용해보기

활동:
1. 자원하는 사람만
2. 새로운 기능 하나에 적용
3. 주간 회고

산출물:
- Before/After 비교
- 배운 점 공유
```

#### Phase 3: 확산 (1-3개월)

```
목표: 팀 표준으로 만들기

활동:
1. 성공 사례 공유
2. 가이드라인 작성
3. 코드 리뷰에 반영

산출물:
- 팀 개발 가이드
- 체크리스트
```

### 저항 다루기

#### "시간이 없어요"

```
반론: "처음에는 느려 보이지만 장기적으로 빠릅니다"

증거:
- 버그 감소 → 수정 시간 감소
- 리팩토링 감소 → 유지보수 시간 감소
- 온보딩 빨라짐 → 신입 교육 시간 감소
```

#### "우리 프로젝트는 특별해요"

```
반론: "ROD, TFD, DGTF는 도메인 독립적입니다"

예시:
- 웹 백엔드 ✅
- 모바일 앱 ✅
- 임베디드 ✅
- ML/AI ✅
- 게임 ✅
```

#### "너무 복잡해요"

```
반론: "핵심만 기억하세요"

ROD: More is better than missing
TFD: 요구사항 = 테스트
DGTF: 천천히, 하지만 확실하게

→ 세 문장
```

---

## 흔한 실수와 해결책

### 실수 1: ROD 과잉

**증상**:
```
- 수십 개의 인터페이스
- 사용하지 않는 추상화
- 과도한 복잡성
```

**해결**:
```
"More is better"이지만 "reasonable"하게
- 정말 필요한가?
- 3가지 이상 사용 예시가 있나?
- 없으면 나중에 추가
```

### 실수 2: TFD 완벽주의

**증상**:
```
- 모든 줄에 테스트
- 테스트 작성에만 며칠
- 실제 기능은 없음
```

**해결**:
```
중요한 것에 집중:
- 비즈니스 로직 ✅
- 복잡한 알고리즘 ✅
- 공개 API ✅
- Getter/Setter ❌
- 단순 변환 ❌
```

### 실수 3: DGTF 과도한 신중함

**증상**:
```
- 결정을 못 내림
- 무한 검토
- 분석 마비
```

**해결**:
```
시간 박스 설정:
- 설계 검토: 30분
- 대안 검토: 15분
- 합의: 15분
- 총 1시간 넘으면 일단 진행
```

---

## 성공 측정하기

### 정량적 지표

#### 버그율 감소
```
측정:
- 배포 후 1주일 내 버그 수
- 핫픽스 횟수
- 롤백 횟수

목표:
- 50% 감소 (6개월)
```

#### 리팩토링 시간 감소
```
측정:
- 기능 추가 vs 리팩토링 시간 비율
- "나중에 고치기" 이슈 수

목표:
- 비율 80:20 → 90:10
```

#### 코드 리뷰 시간
```
측정:
- PR당 코멘트 수
- Approve까지 라운드 수
- 리뷰 시간

목표:
- 코멘트 50% 감소
- 1-2 라운드에 승인
```

### 정성적 지표

#### 팀 만족도
```
설문:
- "코드 품질에 만족하나요?"
- "개발 프로세스가 효율적인가요?"
- "자신의 코드에 자신 있나요?"

목표:
- 점수 상승
```

#### 온보딩 속도
```
측정:
- 신입이 첫 PR까지 시간
- 첫 기능 완성까지 시간

목표:
- 50% 단축
```

---

## 실전 워크플로우 예시

### 새로운 기능 개발

```
Day 1: ROD 설계
1. 요구사항 분석 (30분)
2. 서비스 체인 정의 (1시간)
3. 인터페이스 설계 (1시간)
4. 팀 리뷰 (30분)

Day 2: TFD 테스트 작성
1. 테스트 케이스 목록 (30분)
2. Happy path 테스트 (1시간)
3. Error case 테스트 (1시간)
4. Edge case 테스트 (30분)

Day 3-4: DGTF 구현
1. 첫 번째 컴포넌트 (2시간)
   - 구현
   - 테스트 확인
   - 커밋
2. 두 번째 컴포넌트 (2시간)
   - 구현
   - 테스트 확인
   - 커밋
3. 통합 (2시간)
   - 전체 테스트
   - 리팩토링
   - 커밋

Day 5: 리뷰 및 배포
1. 코드 리뷰 요청
2. 피드백 반영
3. 최종 테스트
4. 배포
```

---

## 마무리

### 핵심 원칙 요약

1. **ROD**: More is better than missing
   - 완전한 체인을 먼저 만들고
   - 불필요한 것은 나중에 제거

2. **TFD**: 요구사항 = 테스트
   - 애매한 사항을 테스트로 명확히
   - 검증 가능하게 개발

3. **DGTF**: 천천히, 하지만 확실하게
   - 성급한 결정을 피하고
   - System 2를 활성화

### 시작하기

```
1주차:
[ ] ROD로 하나의 기능 설계
[ ] 팀과 리뷰
[ ] 배운 점 기록

2주차:
[ ] TFD로 테스트 먼저 작성
[ ] 구현
[ ] 효과 측정

3주차:
[ ] DGTF 트리거 인식 연습
[ ] "멈춤 → 생각 → 합의 → 진행"
[ ] 회고

4주차:
[ ] 세 가지 통합 적용
[ ] 성공 사례 공유
[ ] 다음 기능에도 적용
```

### 마지막 조언

> "완벽하게 하려고 하지 마세요"
> 
> ROD, TFD, DGTF도 "DONT GO TOO FAST"로 도입하세요
> 
> 천천히, 하나씩, 확실하게

---

**Remember: DONT GO TOO FAST** ⏱️

다음: [개념과 가치](01-concepts-and-values-ko.md)로 돌아가기
