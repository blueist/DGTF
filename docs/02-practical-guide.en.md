# Practical Guide: Applying ROD, TFD, DGTF

## Table of Contents
1. [Before You Start](#before-you-start)
2. [Applying ROD](#applying-rod)
3. [Applying TFD](#applying-tfd)
4. [Applying DGTF](#applying-dgtf)
5. [Introducing to Your Team](#introducing-to-your-team)
6. [Common Mistakes and Solutions](#common-mistakes-and-solutions)
7. [Measuring Success](#measuring-success)

---

## Before You Start

### Mindset

What you need to apply ROD, TFD, DGTF:

✅ **Humility**: "I might not know"   
✅ **Patience**: "It's okay to go slowly"  
✅ **Openness**: "There might be better ways"  
✅ **Discipline**: "Follow principles"  

❌ What you don't need:
- Genius-level intelligence
- Perfect memory
- Latest tech knowledge
- Fast coding speed

### Gradual Introduction

```
Week 1: Practice ROD (design only)
Week 2: Practice TFD (writing tests only)
Week 3: Practice DGTF (strengthen code reviews)
Week 4: Integration (all together)
```

**DONT GO TOO FAST**: Don't try everything at once!

---

## Applying ROD

### Step 1: Requirements Analysis

**Bad Example**:
```
"Query users from database"
→ Start coding immediately
```

**ROD Approach**:
```
1. List entire chain:
   - Input: User ID
   - Processing:
     * Acquire DB connection
     * Execute query
     * Map results
     * Release connection
   - Output: User information or error
   
2. Define each step's responsibility:
   - ConnectionProvider: Provide/release connections
   - DatabaseSelector: Select Master/Slave
   - QueryExecutor: Execute queries
   - ResultMapper: Convert results
   - TransactionManager: Manage transactions
   - ErrorHandler: Handle errors
```

### Step 2: Define Interfaces First

```go
// 1. Overall chain interface
type UserRepository interface {
    GetUser(ctx context.Context, id string) (User, error)
    SaveUser(ctx context.Context, user User) error
}

// 2. Each responsibility interface

// ⚠️ Easy to miss: Connection provision
type ConnectionProvider interface {
    GetConnection(ctx context.Context) (*sql.DB, error)
    ReleaseConnection(conn *sql.DB) error
}

// ⚠️ Easy to miss: DB selection
type DatabaseSelector interface {
    SelectForRead(ctx context.Context) (*sql.DB, error)
    SelectForWrite(ctx context.Context) (*sql.DB, error)
}

type QueryExecutor interface {
    Execute(query string, args ...interface{}) (Result, error)
}

type ResultMapper interface {
    MapToUser(result Result) (User, error)
}

// ⚠️ Easy to miss: Transaction
type TransactionManager interface {
    Begin(ctx context.Context) (Transaction, error)
    Commit(tx Transaction) error
    Rollback(tx Transaction) error
}
```

### Step 3: Create Implementation Chain

```go
type DefaultUserRepository struct {
    connProvider ConnectionProvider  // ⚠️ Without this, create connection every time
    dbSelector   DatabaseSelector    // ⚠️ Without this, only use Master
    executor     QueryExecutor
    mapper       ResultMapper
    txManager    TransactionManager  // ⚠️ Without this, no transactions
}

func (r *DefaultUserRepository) GetUser(ctx context.Context, id string) (User, error) {
    // 1. Select DB (read from Slave)
    db, err := r.dbSelector.SelectForRead(ctx)
    if err != nil {
        return User{}, err
    }
    
    // 2. Execute query
    result, err := r.executor.Execute(
        "SELECT * FROM users WHERE id = ?", id,
    )
    if err != nil {
        return User{}, err
    }
    
    // 3. Map result
    user, err := r.mapper.MapToUser(result)
    if err != nil {
        return User{}, err
    }
    
    return user, nil
}
```

### Step 4: Fill Implementations (Later)

```go
// Initially simple implementation
type SimpleConnectionProvider struct {
    db *sql.DB
}

func (p *SimpleConnectionProvider) GetConnection(ctx context.Context) (*sql.DB, error) {
    return p.db, nil
}

// Add pool later when needed
type PooledConnectionProvider struct {
    pool *sql.DB
}

func (p *PooledConnectionProvider) GetConnection(ctx context.Context) (*sql.DB, error) {
    // Acquire from pool
    return p.pool, nil
}

// Add Master/Slave later
type MasterSlaveSelector struct {
    master *sql.DB
    slaves []*sql.DB
}

func (s *MasterSlaveSelector) SelectForRead(ctx context.Context) (*sql.DB, error) {
    // Select one of slaves
    return s.slaves[rand.Intn(len(s.slaves))], nil
}

func (s *MasterSlaveSelector) SelectForWrite(ctx context.Context) (*sql.DB, error) {
    // Always use Master for writes
    return s.master, nil
}
```

### ROD Checklist

Complete service chain created?
- [ ] Is input clear?
- [ ] Is each processing step defined?
- [ ] Is output clear?
- [ ] Is error handling path present?

Each responsibility clear?
- [ ] Does one component have only one responsibility?
- [ ] Do responsibilities not overlap?
- [ ] Are there no missing responsibilities?

Language-independent?
- [ ] Not dependent on specific language features?
- [ ] Portable to other languages?

---

## Applying TFD

### Step 1: Convert Requirements to Tests

**Vague Requirement**:
```
"Authentication system must be secure"
```

**Clarified with TFD**:
```go
func TestAuthentication_Security(t *testing.T) {
    t.Run("Reject wrong password", func(t *testing.T) {
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
    
    t.Run("Reject empty password", func(t *testing.T) {
        // ...
    })
    
    t.Run("Reject SQL injection attempts", func(t *testing.T) {
        // ...
    })
    
    t.Run("Block brute force", func(t *testing.T) {
        // ...
    })
}
```

### Step 2: Classify Test Cases

```
1. Happy Path (normal operation)
   - Correct input → Expected output

2. Error Cases (error handling)
   - Invalid input → Appropriate error
   - System error → Recovery or propagation

3. Edge Cases (boundary conditions)
   - Empty input
   - Maximum/minimum values
   - nil/null

4. Concurrent Cases (concurrency)
   - Multiple simultaneous requests
   - Race conditions
   - Deadlock
```

### Step 3: Write Tests First (Before Implementation)

```go
func TestAuthenticator_Authenticate(t *testing.T) {
    // When writing this test, implementation doesn't exist
    // But this test defines requirements
    
    t.Run("Success: valid credentials", func(t *testing.T) {
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
    
    t.Run("Failure: invalid credentials", func(t *testing.T) {
        // ...
    })
}
```

### Step 4: Write Implementation

```go
func (a *DefaultAuthenticator) Authenticate(creds Credentials) (Token, error) {
    // Implement as tests require
    user, err := a.validator.Validate(creds)
    if err != nil {
        return Token{}, fmt.Errorf("validation failed: %w", err)
    }
    
    // ...
}
```

### TFD Checklist

Requirements expressed as tests?
- [ ] All functions defined as tests?
- [ ] No ambiguity?
- [ ] Clear completion criteria?

All cases covered?
- [ ] Happy path
- [ ] Error cases
- [ ] Edge cases
- [ ] Concurrent cases (if needed)

Tests independent?
- [ ] Independent of test order?
- [ ] No impact on other tests?
- [ ] Independent of execution environment?

---

## Applying DGTF

### Step 1: Recognize Triggers

**Detect Warning Signals**:

```
Ask yourself:
- "This looks easy?" → 🚨 Danger
- "Must finish quickly!" → 🚨 Danger
- "I roughly know" → 🚨 Danger
- "Tests can wait" → 🚨 Danger
- "No review needed" → 🚨 Danger
```

### Step 2: Stop

```
When trigger detected, immediately:

1. Stop coding
2. Take hands off keyboard
3. Breathe deeply
4. Ask "Is this really right?"
```

### Step 3: Think

```
Checklist:

[ ] Do I understand requirements accurately?
[ ] Do I know the full impact scope?
[ ] Are there other cases?
[ ] How will I test this?
[ ] Is there a better approach?
[ ] Who should I consult?
```

### Step 4: Agree

```
Don't decide alone:

1. Share design
   - "I'm planning to do this, what do you think?"
   
2. Get feedback
   - Listen
   - Don't defend
   - Review alternatives

3. Proceed after agreement
   - Confirm clear consent
   - Document (comments, wiki, etc.)
```

### Step 5: Go

```
After agreement:

1. One at a time
   - Break into small units
   - One complete → Test → Next

2. Commit frequently
   - Commit in small units
   - Clear messages
   - Reviewable size

3. Continuous verification
   - Run tests
   - Code review
   - Integration testing
```

### DGTF Real Example

**Bad (Hasty)**:
```
Manager: "This bug is urgent, fix it quickly!"
Developer: "Got it!"
→ Patched in 5 minutes
→ Other features broken
→ 3 days of bug fixing
```

**Good (DGTF)**:
```
Manager: "This bug is urgent, fix it quickly!"
Developer: "Wait, let me analyze it properly"

1. Stop:
   - Don't code immediately despite urgency

2. Think:
   - Analyze bug cause (10 minutes)
   - Check impact scope
   - Review 2 fix approaches

3. Agree:
   - Report to manager
   - "Method A is fast but risky, Method B is safe but takes 1 hour"
   - Decide together

4. Go:
   - Carefully fix with chosen method
   - Write tests
   - Get reviewed
   
Result: 
- 30 minutes spent
- No side effects
- No other bugs
```

### DGTF Checklist

Did you stop?
- [ ] Did you recognize the trigger?
- [ ] Did you stop immediately?
- [ ] Did you question yourself?

Did you think?
- [ ] Did you verify requirements?
- [ ] Did you identify impact scope?
- [ ] Did you review alternatives?

Did you agree?
- [ ] Did you share with team?
- [ ] Did you get feedback?
- [ ] Did you obtain clear consent?

---

## Introducing to Your Team

### Phased Introduction

#### Phase 1: Awareness (1-2 weeks)

```
Goal: Help team understand ROD, TFD, DGTF

Activities:
1. Share documentation
2. Brief presentation (30 min)
3. Q&A
4. Share examples

Output:
- None (no enforcement)
```

#### Phase 2: Experiment (2-4 weeks)

```
Goal: Try on small project

Activities:
1. Volunteers only
2. Apply to one new feature
3. Weekly retrospectives

Output:
- Before/After comparison
- Share learnings
```

#### Phase 3: Expansion (1-3 months)

```
Goal: Make it team standard

Activities:
1. Share success stories
2. Write guidelines
3. Reflect in code reviews

Output:
- Team development guide
- Checklists
```

### Handling Resistance

#### "We don't have time"

```
Counterargument: "Slower initially, faster long-term"

Evidence:
- Fewer bugs → Less fixing time
- Less refactoring → Less maintenance time
- Faster onboarding → Less training time
```

#### "Our project is special"

```
Counterargument: "ROD, TFD, DGTF are domain-independent"

Examples:
- Web backend ✅
- Mobile apps ✅
- Embedded ✅
- ML/AI ✅
- Games ✅
```

#### "Too complicated"

```
Counterargument: "Just remember the core"

ROD: More is better than missing
TFD: Requirements = Tests
DGTF: Slowly but surely

→ Three sentences
```

---

## Common Mistakes and Solutions

### Mistake 1: ROD Overengineering

**Symptoms**:
```
- Dozens of interfaces
- Unused abstractions
- Excessive complexity
```

**Solution**:
```
"More is better" but be "reasonable"
- Is it really needed?
- Are there 3+ use cases?
- If not, add later
```

### Mistake 2: TFD Perfectionism

**Symptoms**:
```
- Tests for every line
- Days spent writing tests
- No actual features
```

**Solution**:
```
Focus on what matters:
- Business logic ✅
- Complex algorithms ✅
- Public APIs ✅
- Getters/Setters ❌
- Simple conversions ❌
```

### Mistake 3: DGTF Excessive Caution

**Symptoms**:
```
- Can't make decisions
- Endless reviews
- Analysis paralysis
```

**Solution**:
```
Set time boxes:
- Design review: 30 min
- Alternative review: 15 min
- Agreement: 15 min
- If over 1 hour, proceed anyway
```

---

## Measuring Success

### Quantitative Metrics

#### Bug Rate Reduction
```
Measure:
- Bugs within 1 week after deployment
- Hotfix count
- Rollback count

Goal:
- 50% reduction (6 months)
```

#### Refactoring Time Reduction
```
Measure:
- Feature addition vs refactoring time ratio
- "Fix later" issue count

Goal:
- Ratio 80:20 → 90:10
```

#### Code Review Time
```
Measure:
- Comments per PR
- Rounds until approval
- Review time

Goal:
- 50% fewer comments
- Approved in 1-2 rounds
```

### Qualitative Metrics

#### Team Satisfaction
```
Survey:
- "Satisfied with code quality?"
- "Is development process efficient?"
- "Confident in your code?"

Goal:
- Score increase
```

#### Onboarding Speed
```
Measure:
- Time for new hire to first PR
- Time to first feature completion

Goal:
- 50% reduction
```

---

## Real Workflow Example

### New Feature Development

```
Day 1: ROD Design
1. Requirements analysis (30 min)
2. Define service chain (1 hour)
3. Interface design (1 hour)
4. Team review (30 min)

Day 2: TFD Test Writing
1. List test cases (30 min)
2. Happy path tests (1 hour)
3. Error case tests (1 hour)
4. Edge case tests (30 min)

Day 3-4: DGTF Implementation
1. First component (2 hours)
   - Implement
   - Verify tests
   - Commit
2. Second component (2 hours)
   - Implement
   - Verify tests
   - Commit
3. Integration (2 hours)
   - Full tests
   - Refactoring
   - Commit

Day 5: Review and Deploy
1. Request code review
2. Reflect feedback
3. Final testing
4. Deploy
```

---

## Conclusion

### Summary of Core Principles

1. **ROD**: More is better than missing
   - Create complete chain first
   - Remove unnecessary later

2. **TFD**: Requirements = Tests
   - Clarify ambiguity with tests
   - Develop verifiably

3. **DGTF**: Slowly but surely
   - Avoid hasty decisions
   - Activate System 2

### Getting Started

```
Week 1:
[ ] Design one feature with ROD
[ ] Review with team
[ ] Document learnings

Week 2:
[ ] Write tests first with TFD
[ ] Implement
[ ] Measure effectiveness

Week 3:
[ ] Practice recognizing DGTF triggers
[ ] "Stop → Think → Agree → Go"
[ ] Retrospective

Week 4:
[ ] Apply all three integrated
[ ] Share success stories
[ ] Apply to next feature
```

### Final Advice

> "Don't try to be perfect"
> 
> Introduce ROD, TFD, DGTF with "DONT GO TOO FAST" too
> 
> Slowly, one by one, surely

---

**Remember: DONT GO TOO FAST** ⏱️

Next: Return to [Concepts and Values](01-concepts-and-values.en.md)
