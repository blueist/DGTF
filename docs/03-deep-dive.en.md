# ROD, TFD, DGTF: Deep Dive

## Table of Contents
1. [Introduction](#introduction)
2. [The Two Systems of Human Thinking](#the-two-systems-of-human-thinking)
3. [ROD: In-Depth Analysis](#rod-in-depth-analysis)
4. [TFD: In-Depth Analysis](#tfd-in-depth-analysis)
5. [DGTF: In-Depth Analysis](#dgtf-in-depth-analysis)
6. [Integration of Three Methodologies](#integration-of-three-methodologies)
7. [Psychology of Practical Application](#psychology-of-practical-application)

---

## Introduction

This document explores the **philosophical background** and **psychological foundations** of ROD, TFD, and DGTF in depth.

### What This Document Covers:
- **Why** are these methodologies necessary?
- **Why** do they work this way?
- How do they connect to human psychology?
- What are "bad emergency decisions"?

### Who Should Read This:
- Developers who want to understand the fundamental principles
- Leaders planning to introduce these methodologies to their teams
- Those researching software development methodologies
- Anyone who wants to become a better developer

**Note**: For quick application, see the [Practical Guide](02-practical-guide.en.md) first.

---

## The Two Systems of Human Thinking

### Daniel Kahneman's Dual Process Theory

Nobel Prize winner Daniel Kahneman's research explains human thought processes through two systems.

#### System 1 (Fast Thinking)
```
Characteristics:
- Automatic, intuitive, immediate
- Requires little effort
- Quick judgments and reactions
- Emotional, associative

Advantages:
- Fast decisions
- Survival advantage
- Energy efficient

Disadvantages:
- Vulnerable to biases
- Unsuitable for complex problems
- Prone to errors
- Dominant under pressure
```

#### System 2 (Slow Thinking)
```
Characteristics:
- Deliberate, analytical, careful
- Requires concentration and effort
- Logical reasoning
- Systematic analysis

Advantages:
- Accurate judgment
- Solves complex problems
- Long-term thinking

Disadvantages:
- Slow
- Energy consuming
- Doesn't work when tired
- Avoided under pressure
```

---

### Cognitive Systems by Development Phase

Each phase of software development naturally induces **different cognitive systems**.

#### Psychological State During Design Phase
```
Environment:
✓ Time available
✓ Low pressure
✓ Exploratory thinking possible
✓ Low cost of mistakes

Natural system:
→ Easy to activate System 2
→ Deep thinking possible
→ Complete analysis possible
```

#### Psychological State During Implementation Phase
```
Environment:
✗ Time shortage
✗ Deadline pressure
✗ Requirement changes
✗ High cost of mistakes

Natural system:
→ System 1 dominates
→ Seeks quick decisions
→ "Just make it work" mentality
→ Risk of bad emergency decisions
```

---

### Key Insight

> **Using the wrong system at the wrong time causes problems.**

```
Problem Pattern:

Using System 1 in Design Phase:
"Roughly like this" → Incomplete design → Missing occurs

System 1 Dominance in Implementation Phase:
"Need to hurry" → Bad emergency decisions → Technical debt
```

**ROD, TFD, DGTF solve this problem.**

---

## ROD: In-Depth Analysis

### Core Concept

**Definition:**
> By utilizing System 2 in the design phase to build a complete service chain,
> prevent confusion during implementation and bad emergency decisions from System 1.

**Core Principle:**
> **"More is better than missing"**
> 
> Having more is better than having something missing

---

### The Fundamental Problem ROD Solves

#### Problem: "Bad Emergency Decisions"

**Scenario:**
```
Discovery During Implementation:
"Huh? How do I create the user object?"
"It's not in the design?"

Developer's Psychological State:
- Under pressure (deadline approaching)
- Confused (not in design)
- Stressed (lack of time)
- System 1 activated

System 1's "Solution":
→ "Let's use a global variable!"
→ "Just use a singleton!"
→ "Make it a static field!"
→ "Hardcode it for now and fix later!"

Result:
→ Bad architecture
→ Technical debt
→ Increased coupling
→ Hard to fix later
→ More time spent
```

---

### The Psychology of "Missing"

#### Why is Missing Dangerous?

**Asymmetry of Removal vs Addition:**

```
Removing Unnecessary Things (Easy):
Situation:
- Code already exists
- "Not being used" (clear evidence)
- Time available (not urgent)

Psychology:
- System 2 can operate
- Analytical judgment
- Safe decision

Adding Missing Things (Hard):
Situation:
- Empty space (overwhelming)
- "Need it but don't have it" (confusion)
- Time pressure (during implementation)

Psychology:
- System 1 dominates
- Emergency decision
- Risky choice
```

---

### ROD's Solution: Complete Service Chain

#### Definition of Service Chain

**What is a Service Chain:**
> A complete set of connected responsibilities
> needed to achieve all requirements

**Criteria for Completeness:**
```
Question: "Can requirements be achieved with only this service chain?"

YES → Complete (No Missing)
NO → Missing exists → Need to add services
```

---

### ROD's Strict Rules

ROD **prohibits** the following:

#### 1. No Constructors

**Why?**
```
When Using Constructors:
func NewUser(name string) User {
    return User{name: name}
}

→ Object creation method hardcoded
→ Cannot change creation logic
→ Difficult to Mock in tests

ROD Way:
type UserFactory interface {
    CreateUser(name string) (User, error)
}

→ Creation method provided as service
→ Can be replaced anytime
→ Easy to test
```

#### 2. No Static Fields

**Why?**
```
When Using Static Fields:
var GlobalDB *sql.DB

→ Global state
→ Hidden dependencies
→ Untestable
→ No parallel testing

ROD Way:
type DatabaseProvider interface {
    GetDatabase() (*sql.DB, error)
}

→ Explicit dependencies
→ Replaceable
→ Testable
```

#### 3. No Assumptions

**Why?**
```
When Making Assumptions:
"Database will always be connected"
"Config will be somewhere"

→ Missing occurs
→ Discovered during implementation
→ Bad emergency decisions

ROD Way:
"Who provides Database?" → DatabaseProvider
"Who manages Config?" → ConfigManager

→ All responsibilities explicit
→ No Missing
```

---

### Deep Meaning of "More is Better than Missing"

#### Strategic Choice

```
Design Phase (Time Available):
Uncertainty: "Do we need this?"
Choice: Include it anyway (More)
Reason: Easy to remove later

Implementation Phase (Time Pressure):
Discovery: "Need this but don't have it?"
Situation: Doesn't happen (ROD prevents)
Result: No bad emergency decisions
```

#### Psychological Insurance

```
ROD is "psychological insurance":

Premium: Slightly more effort during design
Coverage: Prevents bad emergency decisions during implementation
Value: Prevents technical debt, maintains quality
```

---

### Connection with SOLID

#### Why ROD Enforces SOLID

**Reason:**
```
Reality of Implementation Phase:
- Requirements change frequently
- "Not this way, that way"
- Changes under time pressure

Without SOLID:
→ Change = Modify entire codebase
→ Risky, bugs
→ More time

With SOLID:
→ Change = Replace service
→ Safe, isolated
→ Quick response
```

**Example:**
```go
// SOLID Applied (Interface)
type PaymentProcessor interface {
    Process(amount float64) error
}

// Implementation 1: Credit Card
type CreditCardProcessor struct{}

// Implementation 2: PayPal (requirement change)
type PayPalProcessor struct{}

// When changing:
// Just replace processor
// No impact on other code
```

---

### Value of ROD

```
1. Eliminates Implementation Phase Confusion
   Complete service chain = Clear guide
   No Missing = No "How do I do this?"

2. Prevents System 1's Bad Emergency Decisions
   Just follow design even under pressure
   No need for "global variables!", "singleton!"

3. Safely Responds to Requirement Changes
   SOLID: Services are replaceable
   No impact on other parts

4. Minimizes Technical Debt
   Maintains clean architecture
   Maintainable structure

5. Predictable Development
   Service chain = Task list
   Progress measurable
```

---

## TFD: In-Depth Analysis

### Core Concept

**Definition:**
> By designing tests along with (or before) design,
> clarify requirements and ensure quality.

**Core Principle:**
> **"Requirements = Tests"**
> 
> Tests are not post-work but specifications

---

### The Problem TFD Solves

#### Pitfalls of Traditional Approach

```
Traditional Flow:
Design → Implementation → "Oh, need to test" → Write tests

Problems:
1. Tests conform to implementation
   - Testing "what was implemented" not requirements
   - Also passes bugs

2. Edge cases missed
   - "It already works, why bother"
   - System 1: "Works? Done!"

3. Hard-to-test structure
   - Already implemented
   - Refactoring difficult
   - "Just leave it..."

4. Incomplete coverage
   - "Only important parts"
   - Don't know what's important
```

---

### TFD's Solution

#### Test-First Design

**Flow:**
```
1. Design (ROD)
   Define service chain

2. Test Design (TFD)
   For each service:
   - Normal case tests
   - Error case tests
   - Edge case tests
   - Performance tests

3. Implementation
   Implement to pass tests

4. Verification
   All tests pass = Complete
```

---

### Meaning of "Requirements = Tests"

#### Tests are Specifications

```
Wrong Perspective:
"Tests = Tool to find bugs"

Right Perspective:
"Tests = Specification of requirements"

Example:
Requirement: "Create session on user login"

Tests (Specification):
- Valid credentials → Session created
- Wrong password → Error returned
- Empty input → Error returned
- Already logged in → Return existing session

→ Requirements now clear!
```

---

### Connection with V-Model

```
V-Model Wisdom:

Requirements Definition ←→ Acceptance Tests
         ↓                      ↑
System Design          ←→ System Tests
         ↓                      ↑
Detailed Design        ←→ Integration Tests
         ↓                      ↑
Implementation         ←→ Unit Tests

TFD Application:
At each stage, decide "How to verify"
simultaneously with "What to build"
```

---

### Synergy Between TFD and ROD

```
ROD: "What to build"
Services A, B, C needed

TFD: "Does it work correctly"
Service A:
  - Tests 1, 2, 3
Service B:
  - Tests 4, 5, 6
Service C:
  - Tests 7, 8, 9

Together:
Complete specification = ROD + TFD
```

---

### Value of TFD

```
1. Clear Requirements
   Tests = Specification
   Clear what to implement

2. Complete Test Coverage
   No missing edge cases
   All scenarios covered

3. Forces Testable Design
   Dependency injection
   Interface usage
   Mockable structure

4. Confidence and Stability
   All tests pass = Works correctly
   Safe refactoring
   Prevents regression bugs

5. Living Documentation
   Tests show how to use
   Always up to date

6. Measurable Progress
   Y out of X tests passing
   Clear how much remains
```

---

## DGTF: In-Depth Analysis

### Core Concept

**Definition:**
> By activating System 2 even under pressure,
> prevent System 1's bad emergency decisions and maintain quality.

**Core Principle:**
> **"Slow is smooth, smooth is fast"**
> 
> Going slow makes it smooth, being smooth makes it fast

---

### The Problem DGTF Solves

#### Psychology of Pressure Situations

```
Triggers:
- Deadline pressure
- Requirement changes
- Bug discovery
- Team pressure

Psychological Response:
- Increased stress
- System 1 activation
- "Need to solve quickly!"
- Fixation on first solution

System 1's Decisions:
- Doesn't consider side effects
- "Just make it work and fix later"
- Quick hacks
- Shortcuts

Result:
- Technical debt
- New bugs
- Increased complexity
- Eventually more time spent
```

---

### DGTF's Solution

#### Conscious Control

```
1. Pause
   Recognize trigger:
   - Feel "hurry" impulse
   - Aware of stress
   - Recognize System 1 activation
   
   Action:
   - "Wait, let's think"
   - Take hands off keyboard
   - Deep breath

2. Think
   Activate System 2:
   - Check design (ROD)
   - Check tests (TFD)
   - Analyze impact scope
   - Review alternatives
   
   Questions:
   - "Is this really the right way?"
   - "Any side effects?"
   - "Other approaches?"

3. Proceed
   Careful implementation:
   - Complete one at a time
   - Continuous verification
   - Check tests
```

---

### Driving Analogy

#### Driver's License ≠ Good Driver

```
Bad Driver (System 1 Dominates):
- Quick lane changes
- Doesn't check
- Aggressive driving
- Hard acceleration, hard braking

Result:
- High accident risk
- Stress
- Fuel waste
- Overall slower (accidents, tickets)

Good Driver (System 2 Utilized):
- Consistent speed
- Maintains safe distance
- Anticipates and responds
- Smooth operation

Result:
- No accidents
- Comfort
- Fuel efficiency
- Overall faster
```

**Programming is the Same:**
```
Coding ability ≠ Good programmer
Good programmer = Skills + Habits + Attitude
```

---

### DGTF ≠ Slow

#### Common Misconception

```
❌ "DGTF = Working slowly"
❌ "DGTF = Low productivity"
❌ "DGTF = Missing deadlines"
```

#### Truth

```
✅ DGTF = Working carefully
✅ DGTF = Quality first
✅ DGTF = Sustainable pace

Paradox:
┌─────────────────────────┐
│ Hasty Development:      │
│ Fast → Bugs → Fixes →   │
│ More bugs → Refactoring │
│ → Overall slow          │
└─────────────────────────┘

┌─────────────────────────┐
│ DGTF Development:       │
│ Careful → Fewer bugs →  │
│ Less fixing → Stable →  │
│ → Overall fast          │
└─────────────────────────┘
```

---

### DGTF in Practice

#### Weinberg's "Egoless Programming"

```
Programming Without Ego:

Problem:
"My code" = "Me"
Criticism = Personal attack
Defensive attitude

DGTF:
"My code" ≠ "Me"
Criticism = Improvement opportunity
Open attitude

Effect:
- Better code reviews
- Improved team collaboration
- Continuous improvement
```

#### Explicit Agreement

```
Bad Pattern:
Developer: "I'll do it this way"
→ Decides alone
→ Implements
→ Problems later

DGTF Pattern:
Developer: "I'm planning to do it this way, thoughts?"
Team: "What about this part?"
Developer: "Oh, then like this"
Team: "Good"
→ Implements
→ No problems
```

---

### Value of DGTF

```
1. Fewer Bugs
   Careful implementation
   Thorough verification
   Early detection

2. Less Rework
   Right from the start
   Minimized technical debt

3. Higher Code Quality
   Clean structure
   Easy maintenance

4. Sustainable Pace
   No burnout
   Consistent productivity
   Faster long-term

5. Team Trust Building
   Stable deployments
   Predictable schedule
   Quality assurance

6. Personal Growth
   Train System 2
   Improved professionalism
   Career advancement
```

---

## Integration of Three Methodologies

### Complete Structure of Development Process

```
┌──────────────────────────────────────┐
│ Design Phase: ROD                    │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━       │
│                                      │
│ Situation: Time available, Low pressure │
│ System: System 2 active              │
│                                      │
│ Tasks:                               │
│ • Build complete service chain       │
│ • Eliminate Missing                  │
│ • Apply SOLID                        │
│                                      │
│ Effect: "What to build" is clear     │
└──────────────────────────────────────┘
            ↓
┌──────────────────────────────────────┐
│ Verification Design Phase: TFD       │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━       │
│                                      │
│ System: System 2 active              │
│                                      │
│ Tasks:                               │
│ • Test cases for each service        │
│ • Define completion criteria         │
│                                      │
│ Effect: "Does it work" verification  │
└──────────────────────────────────────┘
            ↓
┌──────────────────────────────────────┐
│ Implementation Phase: DGTF           │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━       │
│                                      │
│ Situation: Time shortage, High pressure │
│ Risk: System 1 dominates             │
│ Response: Activate System 2 with DGTF│
│                                      │
│ Safeguards:                          │
│ • ROD: Service chain guide           │
│ • TFD: Test criteria                 │
│ • DGTF: Careful progress             │
│                                      │
│ Effect: "How to build" is safe       │
└──────────────────────────────────────┘
```

---

### Synergy Effects

#### Alone vs Integrated

```
ROD Alone:
✓ Complete design
✗ Lacks quality assurance

TFD Alone:
✓ Quality assurance
✗ Design may be incomplete

DGTF Alone:
✓ Careful progress
✗ Lacks direction

ROD + TFD + DGTF:
✓ Complete design (ROD)
✓ Quality assurance (TFD)
✓ Careful progress (DGTF)
✓ Control System 1
✓ Sustainable
```

---

### Real Integration Example

```
Project: Payment System

1. ROD (Design):
   Service chain:
   - PaymentValidator
   - PaymentProcessor
   - TransactionLogger
   - NotificationSender
   - ErrorHandler
   
   → No Missing
   → No confusion during implementation

2. TFD (Verification):
   PaymentProcessor tests:
   - Normal payment → Success
   - Insufficient balance → Error
   - Network error → Retry
   - Timeout → Rollback
   
   → Clear requirements
   → Clear completion criteria

3. DGTF (Implementation):
   Situation: 3 days to deadline, pressure
   
   Temptation: "Just hardcode for now..."
   DGTF: "Wait, check design"
   
   Action:
   - Follow ROD design
   - Pass TFD tests
   - Carefully one by one
   
   Result:
   - Clean code
   - All tests pass
   - No technical debt
```

---

## Psychology of Practical Application

### Reasons for Resistance

#### "We don't have time"

```
Surface Reason:
"ROD/TFD/DGTF takes more time"

Real Reason:
System 1: "Hurry hurry!"
Pressure: "Need to show results now"
Fear: "New method = Risk"

Reality:
Initially: Seems slightly slower
Later: Much faster (no rework)
```

#### How to Overcome

```
Start Small:
- Apply to just one small feature
- Measure results
- Compare Before/After
- Share with team

Collect Evidence:
- Reduced bug count
- Reduced refactoring time
- Shortened code review time
- Increased team satisfaction
```

---

### Habit Formation

#### The 21-Day Rule

```
Week 1: Conscious Effort
- Design while looking at ROD checklist
- Write tests first with TFD
- Consciously recognize DGTF triggers

Week 2: Getting Comfortable
- Look at checklist less
- Tests-first becomes natural
- Pausing becomes habit

Week 3: Automation
- Don't need checklist
- Tests-first is default
- System 2 is default
```

---

### Team Culture

#### Psychological Safety

```
What's Needed:
- Environment where mistakes are okay
- Courage to shout "DGTF"
- Atmosphere where "Wait, let's think" is possible
- Quality prioritized over speed

How to Build:
- Leaders practice first
- Culture of sharing mistakes
- Convert to learning opportunities
- Reward quality system
```

---

## Conclusion

### Core Messages

```
ROD:
"Complete design before implementation"
→ No Missing
→ No bad emergency decisions

TFD:
"Test design before implementation"
→ Clear requirements
→ Quality assurance

DGTF:
"Careful even under pressure"
→ Activate System 2
→ Sustainable quality
```

### Ultimate Goal

> **Sustainable High-Quality Software Development**

```
Short-term:
✓ Clean code
✓ Fewer bugs
✓ Fast deployment

Long-term:
✓ Low technical debt
✓ Easy maintenance
✓ Team happiness
✓ Career growth
```

---

### Getting Started

```
Week 1: Learning
- Read this document
- Understand concepts
- Discuss with team

Week 2: Experiment
- Apply to small feature
- Design with ROD
- Test with TFD
- Implement with DGTF

Week 3: Measure
- Analyze results
- Before/After
- Team feedback

Week 4: Expand
- Apply to larger features
- Team-wide adoption
- Continuous improvement
```

---

### Final Advice

> "Don't try to be perfect"
> 
> Adopt ROD, TFD, DGTF with "DONT GO TOO FAST" too
> 
> Slowly, one by one, surely

---

**Remember: DONT GO TOO FAST** ⏱️

**Next Steps:**
- Learn specific application methods in [Practical Guide](02-practical-guide.en.md)
- Review summary in [Concepts and Values](01-concepts-and-values.en.md)
