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

### Real-World Examples

#### Example 1: Database Access System

**Wrong Approach (Missing Responsibilities)**:
```
Design phase:
"Query user information" → Seems simple!

What's needed:
1. Execute query
2. Return result

Implementation:
func GetUser(id string) (User, error) {
    db := sql.Open("mysql", "connection-string")
    row := db.QueryRow("SELECT * FROM users WHERE id = ?", id)
    // ...
}
```

**Problems**:
- Create connection every time? (Inefficient)
- Master/Slave distinction? (Add later)
- Transactions? (Add later)
→ Complete refactoring needed later!

**ROD Approach (Systematic Analysis)**:
```
Design phase (System 2 activated):
Carefully analyze "Query user information":

Needed responsibilities (More):
1. Query execution - QueryExecutor
2. Result mapping - ResultMapper
3. ⚠️ Connection provision - ConnectionProvider (Easy to miss!)
4. ⚠️ DB selection - DatabaseSelector (Master/Slave) (Easy to miss!)
5. ⚠️ Transaction management - TransactionManager (Easy to miss!)
6. Error handling - ErrorHandler

Define interface for each responsibility:

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

**Result**:
→ ConnectionProvider enables pool management
→ DatabaseSelector enables Master/Slave separation
→ TransactionManager enables transaction handling
→ Easy to add features later (no architecture changes)

#### Example 2: Plugin System

**Wrong Approach (Missing Responsibilities)**:
```
Design phase:
"Process images with plugins" → Seems simple!

What's needed:
1. Load plugins
2. Process images

Implementation:
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

**Problems**:
- Code modification needed for new plugins
- Where to find plugins? (Hardcoded)
- Plugin validation? (Can we trust them?)
- Execution order? (Always fixed?)
→ Complete redesign needed later!

**ROD Approach (Systematic Analysis)**:
```
Design phase (System 2 activated):
Carefully analyze "Process images with plugins":

Needed responsibilities (More):
1. Plugin execution - PluginExecutor
2. Image pipeline - ImagePipeline
3. ⚠️ Plugin discovery - PluginDiscovery (Easy to miss!)
4. ⚠️ Plugin validation - PluginValidator (Easy to miss!)
5. ⚠️ Plugin creation - PluginFactory (Easy to miss!)
6. ⚠️ Plugin registry - PluginRegistry (Easy to miss!)
7. ⚠️ Execution order resolution - ExecutionOrderResolver (Easy to miss!)
8. Error handling - ErrorHandler

Define interface for each responsibility:

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

**Result**:
→ PluginDiscovery enables automatic plugin discovery
→ PluginValidator enables blocking malicious plugins
→ PluginFactory enables various creation methods
→ PluginRegistry enables dynamic management
→ ExecutionOrderResolver enables order changes
→ Extensibility and security ensured

#### Key Lessons

**Commonly Missed Responsibilities**:

1. **Creation**: Factory, Builder, Constructor
2. **Discovery/Selection**: Registry, Locator, Selector
3. **Validation**: Validator, Checker
4. **Resolution**: Resolver, Router, Dispatcher
5. **Lifecycle**: Manager, Initializer, Destroyer

When these responsibilities are missed:
- "Simple approach" chosen during implementation
- Responsibilities get mixed
- Changes become impossible later
- Complete refactoring needed

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
| ROD (Design) | System 2 | Systematically analyze entire chain, ensure completeness |
| TFD (Verification) | System 2 | Systematically verify one by one |
| DGTF (Implementation) | System 2 | Carefully and accurately implement |

**Key Point**: 
- Activate System 2 in all phases
- Avoid hasty judgments from System 1
- Maintain conscious and systematic thinking

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
