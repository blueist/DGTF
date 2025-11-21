# ROD, TFD, DGTF: Concepts and Values

## Table of Contents
1. [Introduction](#introduction)
2. [ROD (Responsibility-Oriented Design)](#rod-responsibility-oriented-design)
3. [TFD (Test-First Development)](#tfd-test-first-development)
4. [DGTF (DONT GO TOO FAST)](#dgtf-dont-go-too-fast)
5. [Connection Between Three Methodologies](#connection-between-three-methodologies)
6. [Cognitive Science Foundation](#cognitive-science-foundation)

---

## Introduction

ROD, TFD, and DGTF are practical methodologies born from decades of software development experience.

Common characteristics:
- **Field-tested** - Born from real-world experience, not theory
- **Language-independent** - Applicable to Go, Java, Python, and all languages
- **Complementary** - Each works independently yet together
- **Easy to learn** - No complex diagrams needed

---

## ROD (Responsibility-Oriented Design)

### Core Concept

**"More is better than missing"**

Design the complete service chain first, then remove what's unnecessary later.

### Why ROD?

#### Psychological Insight

**The Difficulty of Addition**:
```
Facing a blank screen:
- "What should I build?" (Analysis paralysis)
- "Must design it perfectly" (Fear)
- "What if I make mistakes?" (Anxiety)

→ Can't even start
→ Or overthink too long
```

**The Ease of Removal**:
```
Facing existing code:
- "This isn't used?" (Clear judgment)
- "Tests show it's unnecessary" (Provable)

→ Confident removal
→ Quick decision
```

### ROD Principles

1. **Design complete service chain**
   - List all necessary components first
   - Full flow: Input → Processing → Output
   - Ensure nothing is missing

2. **Language-independent design**
   - Don't depend on specific language features
   - Focus on responsibilities and relationships
   - Portable to other languages

3. **Remove later**
   - Delete unused components
   - Merge duplicate responsibilities
   - Simplify

### ROD vs Other Methodologies

| Methodology | Focus | ROD's Difference |
|------------|-------|------------------|
| SOLID | "How" to design | "What" to define first |
| DDD | Domain modeling | Completeness of responsibility chain |
| YAGNI | Only what's needed now | Prepare for future needs too |

### Real-World Example

**Wrong Approach (YAGNI Overconfidence)**:
```
"Only need HTTP now, so let's just build HTTP Listener"
→ Later need WebSocket
→ Complete architecture overhaul
→ Existing code breaks
```

**ROD Approach**:
```
"Need communication methods":
1. NetworkListener (interface)
2. HTTPListener (implementation)
3. WebSocketListener (implementation - empty is OK)
4. TCPListener (implementation - empty is OK)

→ When WebSocket needed, just fill implementation
→ No architecture changes
```

---

## TFD (Test-First Development)

### Core Concept

**"Requirements = Tests"**

Use tests as a means to define requirements.

### Why TFD?

#### Differences from TDD

| Characteristic | TDD | TFD |
|---------------|-----|-----|
| Strictness | Red → Green → Refactor (strict) | Design + Tests together (flexible) |
| Starting point | Failing test first | Requirements analysis and test design |
| Focus | Test-driven | Verifiability |
| Practicality | Tests for all code | Tests for critical parts |

### TFD Principles

1. **Express requirements as tests**
   ```
   Requirement: "Create session on user login"
   
   Tests:
   - Success case: Valid credentials → Session created
   - Failure case: Invalid credentials → Error returned
   - Edge case: Empty input → Error returned
   ```

2. **Eliminate ambiguity**
   - "Works properly" → What is proper?
   - "Fast" → How fast?
   - "Stable" → Under what conditions?

3. **Verifiable development**
   - All requirements expressed as tests
   - Test passing = Requirements met
   - Clear completion criteria

### Connection with V-Model

TFD follows the V-Model philosophy:

```
Requirements Definition ←→ Acceptance Tests
         ↓                      ↑
System Design          ←→ System Tests
         ↓                      ↑
Detailed Design        ←→ Integration Tests
         ↓                      ↑
Implementation         ←→ Unit Tests
```

### Real-World Example

**Vague Requirement**:
```
"Worker must work correctly"
→ What is correct?
```

**Clarified with TFD**:
```
Test cases:
1. Worker starts → Running state
2. Receives task → Processing starts
3. Task completes → Returns result
4. Stop signal → Graceful shutdown
5. Error occurs → Error propagated
6. Timeout → Task aborted

→ Clarified into 6 requirements
```

---

## DGTF (DONT GO TOO FAST)

### Core Concept

**"Slowly but surely"**

Avoid hasty decisions and implementations, maintain conscious and deliberate thinking.

### Why DGTF?

#### The Trap of "Fast"

Modern development culture:
- "Move Fast and Break Things"
- "Fail Fast"
- "Ship Early, Ship Often"

Problems:
- Technical debt accumulation
- Frequent refactoring
- Bug increase
- Team burnout

#### "Fast" vs "Hasty"

```
Fast (Good):
- Clear goals
- Efficient processes
- Remove unnecessary work

Hasty (Bad):
- Skip design
- Skip tests
- Skip code reviews
- "Fix it later"
```

### DGTF Principles

1. **Activate System 2**
   - Stop intuitive judgment
   - Start conscious analysis
   - Review alternatives

2. **Clear agreement**
   - "This should work?" → "Is this the right approach?"
   - Verify assumptions
   - Proceed after agreement

3. **Separate ego from code**
   - My code ≠ Me
   - Criticism ≠ Attack
   - Accept better approaches

### Driving Analogy

```
Driver's license ≠ Good driver

Good driver:
- Maintain safe distance
- Follow signals
- Yield
- Predictive driving

Bad driver:
- "I'm skilled enough"
- Speeding
- Cutting in
- Aggressive driving
```

Similarly:

```
Coding ability ≠ Good programmer

Good programmer:
- Design first
- Write tests
- Code reviews
- Incremental development

Bad programmer:
- "I roughly know"
- Quick implementation
- Skip tests
- Fix later
```

### Practical Application

#### Trigger Recognition

Moments when DGTF is needed:
- "This looks easy?" → Danger signal
- "Must finish quickly!" → Danger signal
- "I roughly know" → Danger signal
- "Know it all!" → Danger signal

#### Response Method

```
1. Stop
   "Wait, is this right?"

2. Think
   "Other cases?"
   "Tests?"
   "Impact scope?"

3. Agree
   Confirm with team
   Document

4. Go
   Slowly, one by one
```

---

## Connection Between Three Methodologies

### Application by Development Phase

```
┌─────────────────────────────────────┐
│      Development Process            │
├─────────────────────────────────────┤
│                                     │
│  1. Design (ROD)                    │
│     Use System 1 ← Experience       │
│     "Need this and that"            │
│     → Complete structure            │
│                                     │
│  2. Implement (DGTF)                │
│     Activate System 2 ← Deliberate  │
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

### Mutual Complementarity

| Phase | Methodology | Role | Result |
|-------|------------|------|---------|
| Design | ROD | Ensure completeness | Nothing missing |
| Implementation | DGTF | Ensure quality | Minimize bugs |
| Verification | TFD | Ensure reliability | Provable |

---

## Cognitive Science Foundation

### Daniel Kahneman's Theory

Core of "Thinking, Fast and Slow":

**System 1 (Fast Thinking)**:
- Automatic, intuitive
- No effort required
- Emotional
- Fast but error-prone

**System 2 (Slow Thinking)**:
- Conscious, analytical
- Requires effort
- Logical
- Slow but accurate

### Application to Software Development

#### System 1 Traps

```
Developer's System 1:
- "This is simple!" → Underestimate complexity
- "Did this before" → Ignore context
- "Know it all!" → Don't read docs
- "Must fix quickly!" → Hasty patches
```

#### System 2 Activation

```
How ROD, TFD, DGTF activate System 2:

ROD:
- "Is everything here?" (Completeness check)
- "Nothing missing?" (Review)

TFD:
- "Can prove with tests?" (Verification)
- "Cover all cases?" (Systematicness)

DGTF:
- "Wait, is this right?" (Stop)
- "Other approaches?" (Alternative review)
```

### Each Methodology and System 1/2

| Methodology | System Used | Reason |
|------------|-------------|---------|
| ROD (Design) | System 1 | Quick overview with experience and intuition |
| TFD (Verification) | System 2 | Systematically verify one by one |
| DGTF (Implementation) | System 2 | Carefully and accurately implement |

**Key Point**: 
- Utilize System 1 where it's powerful
- Activate System 2 where System 1 is dangerous

---

## Conclusion

ROD, TFD, DGTF are:

✅ **Scientific foundation** - Kahneman's cognitive psychology
✅ **Field-tested** - Decades of development experience
✅ **Practical** - Easy to learn and apply
✅ **Complementary** - Each independent yet work together
✅ **Universal** - Applicable to all languages and domains

Core Message:

> Not "fast" but "right"
> 
> Quality over speed
> 
> Deliberation over haste
> 
> **DONT GO TOO FAST**

---

## Next Steps

- Learn how to apply in real projects in the [Practical Guide](02-practical-guide.en.md)
