# DGTF: DONT GO TOO FAST

> A Software Development Philosophy based on Cognitive Science and Decades of Experience

[한국어](#한국어) | [English](#english)

---

## 한국어

### 소개

**DGTF (DONT GO TOO FAST)**는 소프트웨어 개발에서 **성급함**이 아닌 **신중함**을 강조하는 개발 철학입니다.

이 철학은 세 가지 핵심 방법론으로 구성됩니다:

- **ROD** (Responsibility-Oriented Design) - 책임 중심 설계
- **TFD** (Test-First Development) - 테스트 우선 개발
- **DGTF** (DONT GO TOO FAST) - 성급하게 진행하지 말기

### 왜 DGTF인가?

현대 소프트웨어 개발에서는 "빠르게"가 미덕처럼 여겨집니다:
- "Move Fast and Break Things"
- "Fail Fast"
- "Ship Early, Ship Often"

하지만 **빠른 것**과 **성급한 것**은 다릅니다.

DGTF는 Daniel Kahneman의 "Thinking, Fast and Slow" 이론을 기반으로:
- **System 1** (빠른 직관적 사고)의 함정을 피하고
- **System 2** (느린 분석적 사고)를 활성화하여
- **지속 가능한 품질**을 만듭니다

### 세 가지 방법론

#### 1. ROD (Responsibility-Oriented Design)

**핵심 원칙**: "More is better than missing"

- 완전한 서비스 체인을 먼저 설계
- 나중에 제거하는 것이 추가하는 것보다 쉬움
- 언어 독립적 설계

**설계 단계에서 System 2 활성화**:
- 체계적이고 의식적인 분석으로 전체 체인 파악
- 하나씩 꼼꼼히 검토하여 빠진 부분이 없도록 함
- 성급한 판단(System 1)을 피하고 신중하게 설계

#### 2. TFD (Test-First Development)

**핵심 원칙**: "요구사항 = 테스트"

- TDD보다 덜 엄격하지만 체계적
- 설계와 테스트를 동시에 고려
- 검증 가능한 개발

**검증 단계에서 System 2 유지**:
- 테스트로 요구사항을 명확히 정의
- 애매한 사항이 없도록 체계적으로 검증

#### 3. DGTF (DONT GO TOO FAST)

**핵심 원칙**: "천천히, 하지만 확실하게"

- 성급한 결정과 구현을 피함
- 명확한 합의 후 진행
- 자아와 코드를 분리

**구현 단계에서 System 2 활성화**:
- 직관적 판단(System 1)을 멈추고
- 의식적 분석(System 2)으로 전환
- 품질과 지속가능성 확보

### 작동 방식

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

### 문서

**시작하기:**
- [왜 DGTF인가?](docs/00-why-dgtf.ko.md) - 배경과 동기 (세미나 자료 기반)

**빠른 시작:**
- [개념과 가치](docs/01-concepts-and-values.ko.md) - 핵심 개념 요약
- [실전 가이드](docs/02-practical-guide.ko.md) - 즉시 적용 가능한 가이드

**깊이 있는 학습:**
- [Deep Dive](docs/03-deep-dive.ko.md) - 철학적 배경과 심리학적 근거

### 누가 만들었나?

이 방법론은 수십 년간 소프트웨어 개발 경험을 가진 시니어 개발자 **Bakmeon**이 만들었습니다.

실전에서 겪은 수많은 실패와 성공을 통해:
- 무엇이 정말 중요한지
- 어떻게 하면 지속 가능한 품질을 만들 수 있는지
- 왜 "빠르게"가 항상 답이 아닌지

를 깨닫고 체계화한 결과입니다.

### 라이선스

이 프로젝트는 [CC BY-SA 4.0](LICENSE) 라이선스를 따릅니다.

---

## English

### Introduction

**DGTF (DONT GO TOO FAST)** is a software development philosophy that emphasizes **deliberation** over **haste**.

This philosophy consists of three core methodologies:

- **ROD** (Responsibility-Oriented Design)
- **TFD** (Test-First Development)
- **DGTF** (DONT GO TOO FAST)

### Why DGTF?

Modern software development culture glorifies speed:
- "Move Fast and Break Things"
- "Fail Fast"
- "Ship Early, Ship Often"

However, **fast** and **hasty** are different.

DGTF is based on Daniel Kahneman's "Thinking, Fast and Slow" theory:
- Avoiding the traps of **System 1** (fast intuitive thinking)
- Activating **System 2** (slow analytical thinking)
- Creating **sustainable quality**

### Three Methodologies

#### 1. ROD (Responsibility-Oriented Design)

**Core Principle**: "More is better than missing"

- Design the complete service chain first
- Removing is easier than adding later
- Language-independent design

**Activating System 2 in Design Phase**:
- Systematically and consciously analyze the entire chain
- Carefully review each part to ensure nothing is missing
- Avoid hasty judgments (System 1) and design deliberately

#### 2. TFD (Test-First Development)

**Core Principle**: "Requirements = Tests"

- Less strict than TDD but systematic
- Consider design and tests simultaneously
- Verifiable development

**Maintaining System 2 in Verification Phase**:
- Clearly define requirements through tests
- Systematically verify to eliminate ambiguity

#### 3. DGTF (DONT GO TOO FAST)

**Core Principle**: "Slowly but surely"

- Avoid hasty decisions and implementations
- Proceed after clear agreement
- Separate ego from code

**Activating System 2 in Implementation Phase**:
- Stop intuitive judgments (System 1)
- Switch to conscious analysis (System 2)
- Ensure quality and sustainability

### How It Works

```
┌─────────────────────────────────────┐
│      Development Process            │
├─────────────────────────────────────┤
│                                     │
│  1. Design (ROD)                    │
│     Activate System 2 ← Systematic  │
│     "Carefully review everything"   │
│     → Complete structure            │
│                                     │
│  2. Implement (DGTF)                │
│     Keep System 2 ← Deliberate      │
│     "Slowly, one by one"            │
│     → Accurate code                 │
│                                     │
│  3. Verify (TFD)                    │
│     Keep System 2 ← Systematic      │
│     "Prove with tests"              │
│     → Reliable system               │
│                                     │
└─────────────────────────────────────┘
```

### Documentation

**Getting Started:**
- [Why DGTF?](docs/00-why-dgtf.en.md) - Background and motivation (based on seminar material)

**Quick Start:**
- [Concepts and Values](docs/01-concepts-and-values.en.md) - Core concepts summary
- [Practical Guide](docs/02-practical-guide.en.md) - Immediately applicable guide

**In-Depth Learning:**
- [Deep Dive](docs/03-deep-dive.en.md) - Philosophical background and psychological foundation

### Who Created This?

This methodology was created by **Bakmeon**, a senior developer with decades of software development experience.

Through numerous failures and successes in the field, he realized:
- What truly matters
- How to create sustainable quality
- Why "fast" is not always the answer

And systematized these insights.

### License

This project is licensed under [CC BY-SA 4.0](LICENSE).

---

## Contributing

Contributions are welcome! Please feel free to:
- Open issues for discussions
- Submit pull requests
- Share your experiences applying these methodologies
- Translate documentation to other languages

## Contact

- GitHub: [@blueist](https://github.com/blueist)

---

**Remember: DONT GO TOO FAST** ⏱️
