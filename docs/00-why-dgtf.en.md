# Why Do We Need ROD, TFD, DGTF?

## Table of Contents
1. [Introduction](#introduction)
2. [What is Good Software?](#what-is-good-software)
3. [The Driving Analogy](#the-driving-analogy)
4. [Reality's Problems](#realitys-problems)
5. [The Solution](#the-solution)
6. [Next Steps](#next-steps)

---

## Introduction

This document explains **why** the ROD, TFD, DGTF methodologies are necessary and **what problems** they solve.

### Why Read This Document:
- Leaders planning to introduce ROD, TFD, DGTF to their teams
- Developers wondering "why do we need these methodologies?"
- Anyone wanting to understand what good software really is
- Those who recognize problems with current development practices

---

## What is Good Software?

### Common Misconceptions

```
"Good software = Software with many features?"
"Good software = Fast software?"
"Good software = Software using latest technology?"
```

---

### ISO/IEC 25010 Quality Model

The international standard ISO/IEC 25010 defines software quality through **9 characteristics**:

```
1. Functionality
   - Does the software provide required functions?

2. Reliability
   - Does the software work stably?
   - Does it not crash?

3. Performance Efficiency
   - Is execution time appropriate?
   - Is memory usage appropriate?

4. Usability
   - Can users use it easily?

5. Security
   - Is it safe from external attacks?

6. Compatibility
   - Does it work well with other software/systems?

7. Maintainability
   - Is it easy to modify?
   - Is it easy to find bugs?

8. Portability
   - Does it work in other environments?

9. Functional Suitability
   - Are necessary functions provided appropriately?
```

---

### Functional vs Non-Functional

**Functional**:
```
What users explicitly request
- "User login feature"
- "Payment processing feature"
- "Data query feature"
```

**Non-Functional**:
```
What users think is "obvious"
- "Obviously it shouldn't crash"
- "Obviously it should always work"
- "Obviously it shouldn't be slow"
- "Obviously it should be bug-free"
```

---

### Bad Software Experiences

We've all experienced these:

```
✗ Software doesn't work as promised
✗ Dies when performing certain functions
✗ Uses too much memory
✗ Crashes when users use it in unexpected ways
✗ Can't find the cause when errors occur
✗ Takes too long to execute
✗ Installation fails on new OS
✗ Conflicts with other software
✗ Crashes after extended use
```

**Question**: Why do these problems occur?

---

## The Driving Analogy

### Scenario: You Arrived at Your Destination!

```
Result:
✓ Arrived at destination on time!!!

But:
✗ Caused 2 traffic accidents because of me!!!
✗ Passengers started vomiting from motion sickness!!!
✗ Car's front bumper is destroyed!!!
✗ Caught by speed cameras 3 times!!
```

**Question**: Is this person a good driver?

---

### Good Software == Good Driving?

| Driving | Software |
|---------|----------|
| Arrive at destination | Implement features |
| No accidents | No bugs |
| Passenger comfort | User experience |
| No vehicle damage | System stability |
| Follow traffic laws | Follow standards/conventions |

**Key Insight**:
> Arriving at destination ≠ Good driving
> 
> Implementing features ≠ Good software

---

### Driving Principles

```
PRINCIPLES OF DRIVING:

✓ To go right, turn on right signal and turn steering wheel right
✓ Watch cars approaching from behind using side and rearview mirrors
✓ Turn on lights when it gets dark
✓ To go left, turn on left signal and turn steering wheel left
✓ Press accelerator to go forward
✓ Press brake to stop
✓ Turn on wipers when it rains
✓ Don't go on red light
✓ Go on green light
```

**Question**: Can you become a good driver just by following these?

**Answer**: No!

---

### What Makes a Good Driver?

```
Principles:
- Basic operation methods
- Traffic laws
- Vehicle structure understanding

+

Attitude:
- Safety awareness
- Consideration for others
- Defensive driving

+

Habits:
- Maintaining safe distance
- Regular checking
- Predictive driving

+

Experience:
- Responding to various situations
- Risk recognition
- Judgment
```

**Key Point**:
> Good driver = Driver's license + Attitude + Habits + Experience

---

## Reality's Problems

### Reality of Software Development Education

John Ousterhout (Stanford Professor, author of "A Philosophy of Software Design"):

> **"People have been writing programs for electronic computers**
> **for more than 80 years, but there has been surprisingly little**
> **conversation about how to design those programs**
> **or what good programs should look like."**

> **"The state of the art in software design**
> **has not progressed much beyond that paper**
> **in the ensuing 45 years."**

> **"We teach for loops and object-oriented programming,**
> **but not software design."**

---

### What We Learn

```
Universities/Education:
✓ Programming languages (Java, Python, C++)
✓ Data structures (Array, List, Tree, Graph)
✓ Algorithms (Sorting, Searching, Optimization)
✓ Design patterns (Singleton, Factory, Observer)
✓ Development methodologies (Agile, Scrum, Waterfall)

But Not:
✗ How to design good software
✗ How to achieve non-functional requirements
✗ How to avoid technical debt
✗ How to write maintainable code
```

---

### Are Principles Enough?

**SOLID Principles**:
```
S - Single Responsibility Principle
O - Open/Closed Principle
L - Liskov Substitution Principle
I - Interface Segregation Principle
D - Dependency Inversion Principle
```

**DRY Principle**:
```
Don't Repeat Yourself
```

**KISS Principle**:
```
Keep It Simple, Stupid
```

**Question**: Can we create good software just by knowing these principles?

**Answer**: No!

---

### Why Principles Alone Are Insufficient

```
Problem 1: Too Abstract
"Single Responsibility"
→ What counts as one responsibility?
→ When should we split?
→ What are specific methods?

Problem 2: Context-Dependent
"Open/Closed Principle"
→ When to open?
→ When to close?
→ How to balance?

Problem 3: Difficult to Practice
We know the principles but:
→ Deadline pressure
→ Requirement changes
→ Time shortage
→ Eventually "just make it work"
```

---

### Real Development Field Problems

```
Scenario 1: Deadline Pressure
Developer: "I should follow SOLID principles..."
Manager: "Need to finish by tomorrow"
Developer: "Let me hardcode for now and fix later"
→ Technical debt occurs

Scenario 2: Requirement Change
Developer: "Need to redesign..."
Manager: "No time, add to existing code"
Developer: "I could add one global variable..."
→ Architecture collapses

Scenario 3: Testing
Developer: "I should write test code..."
Manager: "Feature development is urgent now"
Developer: "I'll write it later..."
→ Bugs increase
```

---

### Root Cause

```
Principles:
✓ Tell you what to do
✗ Vague about how to do it
✗ Difficult to follow under pressure

What's Needed:
✓ Concrete methodology
✓ Actionable process
✓ Habits maintainable under pressure
✓ Team-wide culture
```

---

## The Solution

### Good Software == Good Driving!

To become a driver:

```
1. Learn Principles
   - Traffic laws
   - Vehicle operation

2. Develop Attitude
   - Safety awareness
   - Consideration for others

3. Form Habits
   - Safe distance
   - Regular checking

4. Accumulate Experience
   - Various situations
   - Risk response
```

To become a developer:

```
1. Learn Principles
   - SOLID, DRY, KISS
   - Design patterns

2. Change Perspective ← ROD, TFD, DGTF
   - Complete design
   - Test-first
   - Careful progress

3. Form Habits ← ROD, TFD, DGTF
   - Systematic design
   - Verification first
   - Activate System 2

4. Build Culture ← ROD, TFD, DGTF
   - Quality first
   - Team collaboration
   - Sustainability
```

---

### What ROD, TFD, DGTF Provide

#### 1. Concrete Methodology

```
Principle: "Follow Single Responsibility"
→ Abstract, vague

ROD: "Design complete service chain first"
→ Concrete, actionable
→ No Constructors, No Static fields
→ "More is better than missing"
```

#### 2. Actionable Process

```
Principle: "Write tests"
→ When? How?

TFD: "Design tests along with design"
→ During design phase
→ For each service
→ Requirements = Tests
```

#### 3. Protection Under Pressure

```
Reality: "Deadline pressure → Hasty decisions → Technical debt"

DGTF: "Pause → Think → Agree → Proceed"
→ Control System 1
→ Activate System 2
→ "Slow is smooth, smooth is fast"
```

---

### Integrated Effect

```
ROD (Design):
"What to build" is clear
→ Complete service chain
→ No Missing
→ No confusion during implementation

TFD (Verification):
"Does it work correctly" verified
→ Requirements = Tests
→ Clear completion criteria
→ Quality assurance

DGTF (Implementation):
"How to build" is safe
→ Careful progress
→ Prevents bad emergency decisions
→ Sustainable quality

Together:
Complete development process
```

---

### Real Effects

```
Before (Principles Only):
✗ Incomplete design → Missing discovered
✗ Pressure → Hasty decisions
✗ Technical debt → Difficult maintenance
✗ Bugs increase → Reliability drops

After (ROD + TFD + DGTF):
✓ Complete design → No Missing
✓ Maintain caution even under pressure
✓ Clean code → Easy maintenance
✓ Fewer bugs → High reliability
```

---

## Next Steps

### Learning Path

```
1. Understand Background (Current Document)
   ✓ Why is it needed?
   ✓ What problems does it solve?

2. Learn Concepts
   → docs/01-concepts-and-values.en.md
   - Core concepts of ROD, TFD, DGTF
   - Principles of each methodology

3. Practical Application
   → docs/02-practical-guide.en.md
   - Step-by-step application methods
   - Real examples
   - Checklists

4. Deep Understanding
   → docs/03-deep-dive.en.md
   - Psychological background
   - Philosophical foundation
   - Advanced topics
```

---

### Getting Started

```
Team Leaders:
1. Share this document with team
2. Discuss "why is it needed"
3. Pilot apply to small project
4. Measure effectiveness and gather feedback

Individual Developers:
1. Learn concepts
2. Apply to personal projects
3. Share experiences
4. Propose team adoption
```

---

## Conclusion

### Core Message

```
Good Software = Good Driving

Principles alone are insufficient
→ Concrete methodology
→ Actionable process
→ Protection under pressure

ROD + TFD + DGTF:
→ Perspective change
→ Habit formation
→ Culture building
→ Sustainable quality
```

### Get Started

> "Just as becoming a good driver,
> 
> becoming a good developer
> 
> requires learning, practicing, and building habits."

---

**Next**: Learn the core concepts of ROD, TFD, DGTF in [Concepts and Values](01-concepts-and-values.en.md).

**Remember: DONT GO TOO FAST** ⏱️
