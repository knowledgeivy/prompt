# Vibe Coding at Scale

A practical guide for building large software systems with AI assistance through effective dialogue, architecture-first thinking, and iterative collaboration.

---

## Contents

**Quick Start**
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)

**Foundation**

1. [What is Vibe Coding?](#what-is-vibe-coding)
2. [The Mental Model Shift](#the-mental-model-shift)
3. [Where It Works — and Where It Breaks](#where-it-works-and-where-it-breaks)
4. [Core Principles](#core-principles)
5. [The Dialogue Mindset](#the-dialogue-mindset)
6. [Stage-Aware Vibe Coding](#stage-aware-vibe-coding)

**Architecture & Structure**

7. [Architecture Before You Vibe](#architecture-before-you-vibe)
8. [Structuring Projects for AI Assistance](#structuring-projects-for-ai-assistance)
9. [Component Separation & Clean APIs](#component-separation-clean-apis)

**Practical Techniques**

10. [Exploring Multiple Implementation Options](#exploring-multiple-implementation-options)
11. [Prompting Patterns for Code](#prompting-patterns-for-code)
12. [Incremental Development](#incremental-development)
13. [Managing Context at Scale](#managing-context-at-scale)
14. [Documentation & Memory Management](#documentation-memory-management)
15. [Session Management](#session-management)

**Quality & Safety**

16. [Backup & Plan-Review-Implement](#backup-plan-review-implement)
17. [Quality Control and Code Review](#quality-control-and-code-review)
18. [Security-First Vibe Coding](#security-first-vibe-coding)
19. [Iterative Workflow](#iterative-workflow)

**Team & Scale**

20. [Team Practices](#team-practices)
21. [Common Pitfalls](#common-pitfalls)
22. [What Scales, What Doesn't](#what-scales-what-doesnt)

**Appendix**

23. [Templates & Checklists](#templates-checklists)
---

## Quick Reference Cheat Sheet

### Core Principles
1. **Component Separation** - One component, one responsibility, clean APIs
2. **Incremental Development** - Tiny changes (5-min rule), always working code
3. **Explore Options** - Get multiple approaches, compare pros/cons, decide
4. **Session Management** - Cold start with context, hot start for continuity
5. **Backup & Plan** - Always backup, create plan, review, then implement

### Development Cycle
```
Session Start:  Provide context → Review plan → Set scope
Each Change:    Small change → Test → Commit → Repeat
Session End:    Document decisions → Save context
```

### Essential Commands
```bash
# Backup
git commit -m "Before [change]"

# Small change
[Make one tiny change]
[Test immediately]
git commit -m "Step X: [what changed]"

# Compare
git diff HEAD~1 [file]
```

### Key Questions to Ask AI
- "What are the options for [problem]?"
- "Compare approach A vs B for my use case"
- "What's the SMALLEST change we can make?"
- "What could go wrong with this?"
- "Break this into smaller steps"

---

## What is Vibe Coding?

**Vibe Coding** is collaborative development where you work with an AI assistant as a technical partner, maintaining continuous dialogue to build software iteratively.

### The AI is Your Technical Partner

**Not a magic wand** - It's a conversation partner who:
- Suggests approaches
- Explains tradeoffs
- Implements your decisions
- Reviews your plans
- Helps you think through problems

### The Vibe Coding Loop

```
1. Define Objective
   ↓
2. Explore Options
   ↓
3. Provide Context
   ↓
4. AI Suggests/Implements
   ↓
5. You Review & Test
   ↓
6. Dialogue: Refine, Question, Iterate
   ↓
7. Next Small Step
```

### Finding the "Vibe"

Balance between:
- Enough context ↔ Not overwhelming
- Specific ↔ Creative freedom
- Fast ↔ Quality
- Accept suggestions ↔ Your vision

---


## The Mental Model Shift

Vibe coding is not about generating code faster. It is about shifting what you spend your cognitive budget on.

Traditional development: you own the architecture *and* the implementation details.

Vibe coding at scale: you own the architecture, the interfaces, and the constraints. The AI handles implementation details within those bounds. Your job becomes **directing, reviewing, and integrating** rather than writing.

This shift sounds simple. It is not. Most developers fail at vibe coding not because they can't prompt, but because they haven't internalized where the human judgment is irreplaceable:

- **You define what the system should do** — AI cannot infer your business requirements
- **You own the architecture** — AI will invent one if you don't provide it, and you won't like the result at 100k lines
- **You validate correctness** — AI generates plausible code, not necessarily correct code
- **You carry the context** — the model forgets everything between sessions; you don't

The developers who scale best with AI are the ones who become better at specification, not the ones who trust the output most.

---


## Where It Works — and Where It Breaks

### High-leverage uses

- **Boilerplate and scaffolding** — CRUD handlers, serializers, CLI flags, config parsers
- **Test generation** — unit tests for well-defined functions, table-driven test cases, mock scaffolding
- **Isolated, well-typed functions** — anything with clear inputs, outputs, and no hidden dependencies
- **Refactoring with a clear goal** — "extract this logic into a function matching this signature"
- **Format and translation** — converting between data formats, writing SQL from a description, generating OpenAPI specs from types
- **Documentation** — generating docstrings, README sections, changelog entries from code

### Where it degrades

- **Cross-cutting concerns** — authentication, logging, error handling strategies that must be consistent across the whole codebase
- **Complex state management** — anything where the correctness depends on the history of mutations
- **Novel algorithms** — AI will produce something that compiles and looks reasonable but may be subtly wrong
- **Security-critical paths** — input validation, auth, crypto, privilege escalation boundaries
- **Code that requires understanding your domain** — business rules, data invariants, regulatory constraints that don't exist in training data
- **Large-scale refactors** — AI can help execute the steps, but cannot design a safe migration path through a live system

**Rule of thumb:** if you'd struggle to write a specification for it, AI will struggle to implement it correctly.

---


## Core Principles

### 1. Component Separation
**One component, one responsibility, clean APIs**
- Keeps AI focused
- Easier to test
- Simpler to understand

### 2. Incremental Development
**Tiny changes, always working code**
- 5-minute rule: If change takes >5 min to verify, too big
- Test immediately
- Commit frequently

### 3. Explore Options
**Get multiple approaches before deciding**
- Ask for alternatives
- Compare pros/cons
- Make informed decisions

### 4. Session Management
**Cold start with context, hot start for flow**
- New session: Provide full context
- Active session: Maintain continuity
- Save summaries for next time

### 5. Backup & Plan
**Safety first, think before code**
- Always backup before changes
- Create implementation plan
- Review with AI
- Implement step-by-step

---


## The Dialogue Mindset

### Conversation Over Commands

❌ **Poor:** "Create a database class"

✅ **Better:**
```
You: "I need to persist data. What are the options?"
AI: [Explains SQLite, PostgreSQL, etc.]
You: "What are pros/cons for my use case?"
AI: [Compares options]
You: "Let's go with SQLite. What should the API look like?"
```

### Key Mindset Shifts

**From:** "Tell AI what to build"
**To:** "Discuss with AI how to build"

**From:** "Get code quickly"
**To:** "Understand the approach first"

**From:** "Big changes"
**To:** "Tiny, verifiable steps"

**From:** "Accept first answer"
**To:** "Explore alternatives"


## Stage-Aware Vibe Coding

Different development stages require different interaction strategies with AI. Adapt your approach based on where you are in the development lifecycle.

### Planning Stage: Exploratory & Strategic

**Mindset:** Maximize exploration, understand tradeoffs, think long-term

**Key Practices:**
- **Ask open-ended questions**: "What are the options for [problem]?"
- **Request multiple approaches**: "Give me 3 different ways to solve this"
- **Evaluate tradeoffs**: "What are the pros and cons of each approach?"
- **Consider long-term impact**: "How will this decision affect future development?"
- **Challenge assumptions**: "What could go wrong with this approach?"

**Example Dialogue:**
```
You: "I need to implement a caching layer. What are my options?"
AI: [Presents Redis, in-memory, file-based, etc.]
You: "Compare these for a system that needs to scale to 1M users"
AI: [Analyzes scalability, cost, complexity]
You: "What's the long-term maintenance burden of each?"
AI: [Discusses operational complexity]
You: "Let's plan Redis. What's the migration path if we start simple?"
```

### Implementation Stage: Controlled & Incremental

**Mindset:** Minimal changes, always working, test everything

**Key Practices:**
- **Minimal changes only**: Break work into smallest possible steps
- **One change at a time**: Never combine multiple modifications
- **Test after each change**: Run tests immediately, verify behavior
- **Use version control**: Save before/after for comparison
- **Question the change**: "Why this approach? What could break?"

**Example Workflow:**
```bash
# 1. Save current state
git commit -m "Before: Adding cache layer"

# 2. Make ONE minimal change
You: "Add just the cache interface, no implementation yet"
AI: [Creates interface only]

# 3. Test immediately
pytest tests/test_cache_interface.py

# 4. Compare versions
git diff HEAD~1 src/cache.py

# 5. Commit if good
git commit -m "Step 1: Cache interface defined"

# 6. Next minimal step
You: "Now add Redis implementation, just connect/disconnect"

### Verification & Questioning: Critical for Large Projects

**For large software packages, always verify AI responses multiple times. Slow and thorough beats fast and wrong.**

#### The Verification Principle

**AI can be confident and wrong.** In large projects, a single error compounds across the codebase. Always verify:

**Question Everything:**
```
You: "Implement user authentication"
AI: [Provides implementation]

You: "Explain how this handles password hashing"
AI: [Explains]

You: "Is bcrypt the right choice here? What are alternatives?"
AI: [Compares bcrypt, argon2, scrypt]

You: "Why did you choose bcrypt over argon2?"
AI: [Explains reasoning]

You: "What's the security risk if we use default cost factor?"
AI: [Explains cost factor importance]

You: "Show me how to test this with malicious inputs"
AI: [Provides security tests]
```

**Repeat Questions to Confirm:**
```
First time:
You: "How does this handle concurrent requests?"
AI: "It uses a mutex lock"

Verify:
You: "Walk me through what happens when two requests arrive simultaneously"
AI: [Explains step-by-step]

Confirm:
You: "What happens if the lock is held for too long?"
AI: [Explains timeout/deadlock scenarios]

Final check:
You: "Review your implementation. Any race conditions?"
AI: [Self-reviews, may find issues]
```

#### Verification Checklist for Each Implementation

**Before accepting ANY AI-generated code:**

```markdown
## Accuracy Verification
- [ ] Asked AI to explain the approach
- [ ] Questioned why this approach vs alternatives
- [ ] Asked about edge cases
- [ ] Asked about failure modes
- [ ] Asked AI to review its own code
- [ ] Asked same question in different ways (got consistent answers)

## Correctness Verification
- [ ] Tested with normal inputs
- [ ] Tested with edge cases (empty, null, max values)
- [ ] Tested with malicious inputs
- [ ] Verified error handling
- [ ] Checked resource cleanup
- [ ] Reviewed for security issues

## Integration Verification
- [ ] Fits with existing architecture
- [ ] Follows project conventions
- [ ] Compatible with other components
- [ ] Doesn't break existing functionality
- [ ] Performance acceptable
```

#### Slow is Fast for Large Projects

**Don't rush. Repeating questions is good practice:**

**❌ Fast but Risky:**
```
You: "Add caching"
AI: [Implements caching]
You: "Looks good, commit"
[Later: Cache invalidation bugs, memory leaks, race conditions]
```

**✅ Slow but Safe:**
```
You: "Add caching"
AI: [Implements caching]

You: "Explain the caching strategy"
AI: [Explains]

You: "What happens when cache is full?"
AI: [Explains eviction]

You: "How do we invalidate cache?"
AI: [Explains invalidation]

You: "What if two threads invalidate simultaneously?"
AI: [Explains thread safety]

You: "Show me tests for these scenarios"
AI: [Provides tests]

You: "Review your implementation for race conditions"
AI: [Reviews, finds potential issue]

You: "Fix the race condition"
AI: [Fixes]

You: "Explain the fix"
AI: [Explains]

[Now commit with confidence]
```

**Time spent: 10 minutes vs 2 minutes**
**Bugs prevented: Many vs None**
**Debugging time saved: Hours vs Minutes**

#### Repetition Patterns for Verification

**Pattern 1: Explain-Verify-Confirm**
```
1. You: "Implement [feature]"
   AI: [Implements]

2. You: "Explain how this works"
   AI: [Explains]

3. You: "What could go wrong?"
   AI: [Lists risks]

4. You: "How did you handle [specific risk]?"
   AI: [Explains handling]

5. You: "Show me in the code where that happens"
   AI: [Points to code]

6. You: "Is there a better way?"
   AI: [May suggest improvements]
```

**Pattern 2: Challenge-Defend-Improve**
```
1. You: "Why did you use [approach]?"
   AI: [Justifies]

2. You: "What about [alternative]?"
   AI: [Compares]

3. You: "Your approach has [potential issue]. How do you handle it?"
   AI: [Explains or admits oversight]

4. You: "Fix that issue"
   AI: [Improves implementation]

5. You: "Explain the fix"
   AI: [Explains]
```

**Pattern 3: Scenario Testing**
```
1. You: "What happens if [scenario 1]?"
   AI: [Explains]

2. You: "What happens if [scenario 2]?"
   AI: [Explains]

3. You: "What happens if [scenario 1] AND [scenario 2] simultaneously?"
   AI: [Explains or identifies issue]

4. You: "Show me tests for these scenarios"
   AI: [Provides tests]
```

#### When to Repeat Questions

**Repeat the same question when:**

1. **Answer seems incomplete**
   ```
   You: "How does this handle errors?"
   AI: "It catches exceptions"
   You: "What specific exceptions? Show me the code"
   ```

2. **Answer is vague**
   ```
   You: "Is this thread-safe?"
   AI: "Yes, it uses locks"
   You: "Which locks? Where? Show me"
   ```

3. **Answer contradicts earlier statements**
   ```
   Earlier: "This is stateless"
   Later: "It caches results"
   You: "Wait, you said stateless. Explain the caching"
   ```

4. **You don't fully understand**
   ```
   You: "Explain this algorithm"
   AI: [Technical explanation]
   You: "Explain it again with a concrete example"
   AI: [Example-based explanation]
   You: "Walk through step-by-step with this input: [specific input]"
   ```

5. **Stakes are high (security, data integrity, money)**
   ```
   You: "How does this prevent SQL injection?"
   AI: [Explains]
   You: "Show me the code that does that"
   AI: [Shows code]
   You: "Test it with this malicious input: [injection attempt]"
   AI: [Tests]
   You: "Explain why it's safe"
   AI: [Explains]
   ```

#### Verification Dialogue Examples

**Example 1: Payment Processing (High Stakes)**
```
You: "Implement payment processing with Stripe"
AI: [Implements]

You: "How do you prevent duplicate charges?"
AI: "Using idempotency keys"

You: "Show me where idempotency keys are generated"
AI: [Shows code]

You: "What if the same key is used twice?"
AI: "Stripe returns the original result"

You: "What if our database fails after Stripe succeeds?"
AI: "That's a problem, we should use transactions"

You: "Fix that"
AI: [Adds transaction handling]

You: "Walk through the failure scenarios"
AI: [Explains each scenario]

You: "Add tests for each failure scenario"
AI: [Adds tests]

You: "Review the implementation for any other issues"
AI: [Reviews, may find more issues]
```

**Example 2: Database Migration (Data Integrity)**
```
You: "Create migration to add user_role column"
AI: [Creates migration]

You: "What's the default value?"
AI: "NULL"

You: "What happens to existing users?"
AI: "They'll have NULL role"

You: "Is that safe? Can the app handle NULL roles?"
AI: "Probably not, should have a default"

You: "What should the default be?"
AI: "Depends on your business logic"

You: "Set default to 'user', add NOT NULL constraint"
AI: [Updates migration]

You: "What happens if migration fails halfway?"
AI: "Database will rollback"

You: "Test the rollback"
AI: [Adds rollback test]

You: "What if there are millions of rows?"
AI: "Migration might timeout"

You: "How do we handle that?"
AI: [Suggests batched migration]
```

#### Key Principles for Large Projects

1. **Slow down to speed up** - 10 minutes of verification saves hours of debugging
2. **Repeat questions** - Same question, different phrasing reveals inconsistencies
3. **Challenge assumptions** - "Are you sure?" is a valid question
4. **Demand evidence** - "Show me in the code" not "trust me"
5. **Test edge cases** - AI optimizes for happy path, you test the edges
6. **Question confidence** - AI sounds confident even when wrong
7. **Verify integration** - Does it fit with the rest of the system?
8. **Think adversarially** - What could an attacker do? What could go wrong?

**Remember: In large projects, one unverified assumption can cascade into dozens of bugs. Take the time to verify thoroughly.**

```

### Debugging Stage: Skeptical & Methodical

**Mindset:** Question everything, verify assumptions, avoid massive changes

**Key Practices:**
- **Question the diagnosis**: "Are you sure this is the root cause?"
- **Verify assumptions**: "Show me evidence this is the problem"
- **Request minimal fixes**: "What's the SMALLEST change to test this theory?"
- **Avoid shotgun debugging**: Never accept multiple simultaneous changes
- **Compare before/after**: Save both versions to understand the fix
- **Test the fix**: Verify it solves the problem without side effects

**Example Dialogue:**
```
You: "Tests are failing. What's wrong?"
AI: "The issue is in the database connection"
You: "How do you know? Show me the evidence"
AI: [Points to specific error]
You: "What's the MINIMAL change to test if that's really the cause?"
AI: [Suggests adding logging]
You: "Just add logging, don't fix anything yet"
[Test with logging]
You: "Confirmed. Now what's the smallest fix?"
AI: [Suggests targeted change]
You: "Make ONLY that change, nothing else"
```

### Refactoring Stage: Planned & Reversible

**Mindset:** Plan the path, maintain functionality, easy rollback

**Key Practices:**
- **Plan the sequence**: Map out all steps before starting
- **Maintain tests**: Keep all tests passing at each step
- **Save checkpoints**: Commit after each successful step
- **Verify equivalence**: Ensure behavior unchanged
- **Easy rollback**: Each step should be independently revertable

**Example Plan:**
```markdown
# Refactoring Plan: Extract Cache Layer

## Current State
- Cache logic mixed in business logic
- Hard to test, hard to swap implementations

## Goal
- Clean cache interface
- Pluggable implementations
- 100% test coverage maintained

## Steps (each is one commit)
1. [ ] Extract cache interface (no implementation change)
2. [ ] Add tests for interface contract
3. [ ] Create adapter for existing code
4. [ ] Migrate first module to use adapter
5. [ ] Verify tests still pass
6. [ ] Migrate remaining modules one-by-one
7. [ ] Remove old cache code

## Rollback Strategy
- Each step is independently revertable
- Tests must pass after each step
- If any step fails, revert and reassess
```

### Using Stored Memory & Plans

**Control the scope with documentation:**

1. **Session Context Document**: What we're working on, what's off-limits
2. **Implementation Plan**: Step-by-step sequence, current progress
3. **Decision Log**: Why we chose this approach, what we rejected
4. **Known Issues**: What's broken, what's being ignored for now

**Example Session Context:**
```markdown
# Session: Cache Layer Implementation

## In Scope
- Cache interface definition
- Redis implementation
- Unit tests for cache

## Out of Scope (DO NOT TOUCH)
- Database layer
- API endpoints
- Authentication logic

## Current Step
Step 3 of 7: Creating Redis adapter

## Next Steps
1. Finish Redis adapter
2. Add integration tests
3. Update documentation
```

### Version Comparison Workflow

**Always compare before/after to understand changes:**

```bash
# Save before state
cp src/module.py src/module.py.before

# Make change with AI
[AI makes modification]

# Compare
diff -u src/module.py.before src/module.py

# Or use git
git diff src/module.py

# Review changes yourself
# Understand WHAT changed and WHY
# Verify it matches your expectations
```

### Key Principles Across All Stages

1. **You control the scope** - Use plans and context docs to limit AI's changes
2. **Minimal changes always** - Never accept large modifications
3. **Test immediately** - Verify after every single change
4. **Save versions** - Compare before/after to understand impact
5. **Question suggestions** - Especially in debugging, verify the diagnosis
6. **Plan before implement** - Think through the sequence of steps
7. **Easy rollback** - Each change should be independently revertable

---

---


## Architecture Before You Vibe

The single biggest mistake in vibe coding large systems is starting with the implementation.

AI is very good at filling space. If you don't give it an architecture, it will give you one — derived from whatever open-source projects were most common in its training data. That may not match your requirements, your existing patterns, or your operational constraints.

**Do this before any AI-assisted implementation:**

1. **Define module boundaries** — what is in and out of scope for each component; write it down
2. **Write interface definitions first** — types, function signatures, API contracts; these become the constraints the AI must satisfy
3. **Document your invariants** — what must always be true? What are the failure modes you refuse to tolerate?
4. **Establish error handling conventions** — how errors propagate, what gets logged, what gets surfaced to callers; if this isn't specified, AI will invent it inconsistently
5. **Name things intentionally** — AI uses names as semantic hints; vague names produce vague implementations

A useful exercise: before generating any code, write the docstring or spec comment for the module you're about to build. If you can't write it clearly, you're not ready to implement it — with or without AI.

---


## Exploring Multiple Implementation Options

**The Golden Rule:** When there are multiple ways to implement something, always explore options before deciding.

### Why This Matters

- ❌ First suggestion might not be best for your case
- ❌ You might miss simpler alternatives
- ❌ Tradeoffs aren't always obvious
- ❌ Context matters for the right choice

✅ **Solution:** Systematic option exploration

### The Options Exploration Process

**Step 1: Ask for Options**

```
You: "I need to [solve problem]. What are the different approaches?"

AI: "Here are the main approaches:

     Option 1: [Approach A]
     - Description: [What it is]
     - Best for: [Scenarios]
     
     Option 2: [Approach B]
     - Description: [What it is]
     - Best for: [Scenarios]
     
     Option 3: [Approach C]
     - Description: [What it is]
     - Best for: [Scenarios]
     
     Which direction interests you?"
```

**Step 2: Request Pros/Cons Comparison**

```
You: "Compare these options for my specific use case:
     - [Your constraint 1]
     - [Your constraint 2]
     - [Your priority]
     
     Give me pros and cons of each."

AI: "For your use case:

     Option 1: [Approach A]
     Pros:
     ✅ [Advantage 1]
     ✅ [Advantage 2]
     Cons:
     ❌ [Disadvantage 1]
     ❌ [Disadvantage 2]
     
     Option 2: [Approach B]
     Pros:
     ✅ [Advantage 1]
     ✅ [Advantage 2]
     Cons:
     ❌ [Disadvantage 1]
     ❌ [Disadvantage 2]
     
     Option 3: [Approach C]
     Pros:
     ✅ [Advantage 1]
     ✅ [Advantage 2]
     Cons:
     ❌ [Disadvantage 1]
     ❌ [Disadvantage 2]
     
     Recommendation: [Option X] because [reason]"
```

**Step 3: Dig Deeper if Unclear**

```
You: "I'm torn between Option A and Option B. 
     Can you elaborate on [specific aspect]?"

AI: [Provides detailed comparison of that aspect]

You: "What if [my situation changes]? Which would be better?"

AI: [Explains how each handles that scenario]

You: "Show me a simple example of each approach."

AI: [Provides code examples for comparison]
```

**Step 4: Make Informed Decision**

```
You: "Based on [reasoning], let's go with Option B.
     Start with the simplest implementation."

AI: [Implements chosen option]
```

### Real Example: Caching Strategy

**Bad Approach:**
```
You: "Add caching to this function."
AI: [Implements one caching approach]
You: [Might not be the best fit]
```

**Good Approach:**
```
You: "I need to add caching to reduce repeated calculations. 
     What are my options?"

AI: "Main caching strategies:

     1. In-Memory Cache (dict)
        - Fastest
        - Lost on restart
        - Memory limited
     
     2. LRU Cache (functools)
        - Built-in Python
        - Automatic eviction
        - Size limited
     
     3. Redis Cache
        - Persistent
        - Shared across processes
        - Requires Redis server
     
     4. File-Based Cache
        - Persistent
        - Simple
        - Slower than memory
     
     What's your use case?"

You: "Single process, need to cache expensive calculations, 
     okay to lose on restart, want automatic size management."

AI: "Then LRU Cache (Option 2) is perfect:
     
     Pros:
     ✅ Built-in, no dependencies
     ✅ Automatic size management
     ✅ Simple decorator syntax
     ✅ Thread-safe
     
     Cons:
     ❌ Lost on restart (but you said that's okay)
     ❌ Not shared across processes (single process, so fine)
     
     Want to see the implementation?"

You: "Yes, show me."

AI: [Implements LRU cache]
```

### When to Explore Options

**Always explore when:**
- 🔍 Multiple valid approaches exist
- 🔍 Performance matters
- 🔍 Scalability is a concern
- 🔍 You're unfamiliar with the domain
- 🔍 Decision has long-term impact

**Can skip when:**
- ✓ Trivial implementation
- ✓ Standard pattern applies
- ✓ You already know the best approach
- ✓ Easy to change later

### The Options Template

**Save this pattern:**

```
You: "I need to [goal]. What are my options?"

AI: [Lists 3-5 options with brief descriptions]

You: "Compare these for my case: [constraints]. 
     Give pros/cons."

AI: [Detailed comparison]

You: [If still unclear] "Elaborate on [specific aspect]"
     or "Show examples of Option X and Y"

AI: [Provides clarification]

You: "Let's go with [choice] because [reasoning]."

AI: [Implements]
```

### Benefits of This Approach

✅ **Better Decisions**
- Informed by tradeoffs
- Fits your specific needs
- Avoids regret later

✅ **Learning**
- Understand why, not just what
- Build intuition for future
- Know when to use each approach

✅ **Confidence**
- Know you chose well
- Can explain to others
- Easier to maintain

---


## Component Separation & Clean APIs

**The Golden Rule:** Keep components separate with clean APIs to avoid confusing the AI.

### Why This Matters

Tight coupling confuses both you and AI:
- ❌ Unclear responsibilities
- ❌ Mixed concerns
- ❌ Hard to test
- ❌ AI suggestions less accurate

✅ **Solution:** One component, one job, clean interface

### The Separation Strategy

**1. One Component, One Responsibility**

❌ **Bad:**
```python
class DataProcessor:
    def process(self, data):
        # Validates, transforms, saves, notifies, logs...
        # Too many jobs!
```

✅ **Good:**
```python
class DataValidator:
    def validate(self, data) -> ValidationResult:
        """Only validates"""

class DataTransformer:
    def transform(self, data) -> TransformedData:
        """Only transforms"""

class DataRepository:
    def save(self, data) -> SaveResult:
        """Only saves"""
```

**2. Clean API Boundaries**

❌ **Bad:**
```python
def get_data(self):
    return self.db_connection  # Leaks implementation
```

✅ **Good:**
```python
def get_data(self) -> List[DataRecord]:
    """Returns clean data, hides how it's fetched"""
    return [DataRecord(...) for row in results]
```

**3. Minimal Dependencies**

❌ **Bad:**
```python
class ComponentA:
    def __init__(self):
        self.b = ComponentB()  # Tight coupling
        self.c = ComponentC()
```

✅ **Good:**
```python
class ComponentA:
    def __init__(self, data_provider: DataProvider):
        self.provider = data_provider  # Interface, not concrete
```

### The Focus Window Technique

**Work on ONE component at a time:**

```
Session 1: Design & implement DataSource
Session 2: Test DataSource
Session 3: Design & implement Validator
Session 4: Test Validator
... and so on
```

**In each session:**
```
You: "Today we're focusing on [Component X].
     Here's its interface: [paste]
     Don't worry about other components."

AI: [Stays focused on Component X]
```

### Component Design Checklist

✅ **Clear Input/Output**
```python
def process(self, input: InputType) -> OutputType:
    """Clear what goes in and out"""
```

✅ **No Side Effects** (when possible)
```python
def transform(self, data: Data) -> TransformedData:
    """Returns new data, doesn't modify input"""
```

✅ **Self-Contained**
```python
class Component:
    """Has everything it needs internally"""
    def __init__(self, dependencies: Dependencies):
        self.deps = dependencies
```

✅ **Documented Purpose**
```python
class DataValidator:
    """
    Validates data records.
    
    Does: Check fields, types, constraints
    Does NOT: Transform, save, or notify
    """
```

### Red Flags

🚩 "This class does too many things" → Split it
🚩 "Can't test without setting up everything" → Add interfaces
🚩 "Changing X breaks Y and Z" → Decouple
🚩 "AI keeps mixing concerns" → Clarify boundaries

---

## Incremental Development

**The Golden Rule:** Make tiny, incremental changes. Avoid large refactorings.

### Why This Matters

Large changes create problems:
- ❌ AI loses context
- ❌ Hard to verify
- ❌ Difficult to debug
- ❌ Risk multiple bugs
- ❌ Can't easily roll back

✅ **Solution:** Changes so small they're obviously correct

### The 5-Minute Rule

**If a change takes >5 minutes to verify, it's too big.**

❌ **Too Big:**
- "Refactor the entire authentication system"
- "Rewrite the database layer"

✅ **Right Size:**
- "Add a helper function for password hashing"
- "Add one new database query method"

### The Incremental Strategy

**1. One Thing at a Time**

❌ **Bad:**
```
You: "Add caching, improve errors, optimize algorithm"
AI: [Makes massive changes, breaks things]
```

✅ **Good:**
```
Session 1: "Add simple cache for last result"
[Test - works!]

Session 2: "Make cache size configurable"
[Test - works!]

Session 3: "Add cache expiration"
[Test - works!]
```

**2. Always Keep It Working**

❌ **Bad:**
```
You: "Rewrite this component"
AI: [Rewrites everything]
[Nothing works, hours debugging]
```

✅ **Good:**
```
You: "Add new method with new pattern, keep old method"
[Test - both work!]

You: "Migrate one caller to new method"
[Test - still works!]

You: "Migrate another caller"
[Test - still works!]

... continue until done, then remove old method
```

**3. Baby Steps**

Instead of: "Rewrite validation system"

Break into:
1. Add email validation rule
2. Test email validation
3. Add phone validation rule
4. Test phone validation
5. Extract common logic
6. Test existing validations still work

### Dialogue Pattern

```
You: "This change seems big. Break it into smaller steps?"

AI: "Yes:
     1. [Small step 1] - 5 min
     2. [Small step 2] - 5 min
     3. [Small step 3] - 5 min"

You: "Perfect. Let's do step 1 only."

AI: [Implements step 1]

You: [Tests - works!]

You: "Good. Now step 2."
```

### The Incremental Checklist

Before making a change:

✅ Is this the smallest possible change?
✅ Can I test in under 5 minutes?
✅ Does existing code keep working?
✅ Can I explain in one sentence?
✅ If it breaks, will I know why?

### The Mantra

```
Make the smallest change that could possibly work
Test it immediately
If it works, commit it
Then make the next small change

Never:
- Rewrite large sections
- Change multiple things at once
- Break working code
```

---

## Session Management

**The Golden Rule:** Use cold starts for new sessions, hot starts for continuity.

### Understanding Sessions

**Hot Start (Active Session):**
- AI has full recent context
- Fast, maintains flow
- Best for: Iterative work, debugging
- Limit: ~10-20 exchanges

**Cold Start (New Session):**
- AI has no memory
- Requires context re-establishment
- Best for: New features, fresh perspective
- Challenge: Need to provide "memory"

### When to Cold Start

Indicators you need a new session:
1. ✋ AI forgets earlier decisions
2. ✋ Responses become generic
3. ✋ 15+ exchanges in current session
4. ✋ Moving to different phase
5. ✋ Need fresh perspective

### The Cold Start Protocol

**Step 1: Create Session Summary (Before Ending)**

```
You: "Create a summary for the next session:
     1. What we built
     2. Key decisions and why
     3. Open issues
     4. Next steps"

AI: [Creates summary]

You: [Save to docs/session_notes/YYYY-MM-DD_topic.md]
```

**Step 2: Start New Session with Context**

```
You: "Continuing work on [project], now in [phase].

Context from previous session:
[Paste summary]

Current structure:
- src/core/ (core logic)
- src/api/ (REST API)
- tests/ (tests)

Today's goal: [Specific objective]

Ready?"

AI: [Responds with full context]
```

**Step 3: Verify Context Transfer**

```
You: "Quick check - what pattern did we use for [X]?"

AI: "You used [pattern] because [reason]"

You: "Perfect. Let's proceed."
```

### Session Context Template

```markdown
# Session Context: [Topic]

## Previous Sessions
- Session 1: [What was done]
- Session 2: [What was done]

## Current State
- Component A: [Status, file: path]
- Component B: [Status, file: path]

## Key Decisions
1. [Decision]: We chose [X] because [reason]
2. [Decision]: We use [pattern] for [purpose]

## Status
- ✅ Completed: [List]
- 🚧 In Progress: [Current]
- 📋 TODO: [Next]

## Today's Goal
[Specific objective]
```

---

## Backup & Plan-Review-Implement

**The Golden Rule:** Always backup, create plan, review with AI, then implement step-by-step.

### Why This Matters

AI can make mistakes. Protect yourself:
- ❌ Might misunderstand
- ❌ Might introduce bugs
- ❌ Changes might break code
- ✅ Need to compare old vs new
- ✅ Need to roll back if needed

### The Safety Protocol

**Step 1: Backup**

```bash
# Git (recommended)
git add .
git commit -m "Before [change]"

# Or file copy
cp important_file.py important_file.py.backup
```

**Step 2: Create Implementation Plan**

```markdown
# Implementation Plan: [Feature]

## Goal
[What we want to achieve]

## Steps
1. [ ] Step 1: [Small change]
   - File: [filename]
   - Action: [What to do]
   - Test: [How to verify]

2. [ ] Step 2: [Next change]
   - File: [filename]
   - Action: [What to do]
   - Test: [How to verify]

## Risks
- [What could go wrong]

## Rollback
git checkout [filename]
```

**Step 3: Review Plan with AI**

```
You: "Review this plan:
[Paste plan]

What am I missing? What could go wrong?"

AI: [Reviews, suggests improvements]

You: "Update the plan with your suggestions"

AI: [Updates plan]
```

**Step 4: Implement Step-by-Step**

```
For each step:
1. Backup: git commit -m "Before Step X"
2. Implement: "Do Step X only"
3. Test: Verify it works
4. Commit: git commit -m "Step X: [description]"
5. Next step
```

### Implementation Plan Template

```markdown
# Implementation Plan: [Feature]

## Current State
- Files: [List]
- Behavior: [Current]

## Goal
[One sentence]

## Steps
1. [ ] [Step 1]
   - File: [path]
   - Action: [what]
   - Test: [how to verify]
   - Status: [ ] Not started / [x] Done

2. [ ] [Step 2]
   [Same structure]

## Risks
- [Risk 1]: [Mitigation]
- [Risk 2]: [Mitigation]

## Rollback
git checkout [files]
```

### The Complete Flow

```
1. BACKUP
   git commit -m "Before [change]"

2. PLAN
   Create implementation plan
   Break into small steps

3. REVIEW
   Ask AI to review plan
   Incorporate suggestions

4. IMPLEMENT
   One step at a time
   Test after each
   Commit when working

5. VERIFY
   All tests pass
   Behavior correct
```

---

# Part 3: Practical Application

## Dialogue Techniques

### Technique 1: The Socratic Method

**Engage in dialogue, don't just command:**

```
You: "I need to persist data. What are the tradeoffs 
     between SQLite and PostgreSQL?"

AI: [Explains tradeoffs]

You: "For my use case [describe], which is better?"

AI: [Recommends with reasoning]

You: "Show me the schema design"

AI: [Proposes schema]
```

### Technique 2: Iterative Refinement

**Start broad, narrow through conversation:**

```
Round 1: "I'm building [system]"
Round 2: "Let's focus on [component]"
Round 3: "Show me implementation of [method]"
Round 4: "Add error handling for [case]"
```

### Technique 3: The "Why" Question

**Always ask for reasoning:**

```
You: "Why did you choose [X]?"
AI: [Explains reasoning]

You: "What are the downsides?"
AI: [Explains tradeoffs]

You: "When would [alternative] be better?"
AI: [Explains scenarios]
```

### Technique 4: Constraint Negotiation

**Discuss tradeoffs openly:**

```
You: "I need [performance target]"
AI: "That's challenging. Options:
     1. [Approach A] - [tradeoff]
     2. [Approach B] - [tradeoff]"

You: "What if I relax [constraint]?"
AI: "Then [approach] becomes feasible"
```

### Technique 5: The Context Refresh

**Periodically summarize:**

```
Every 10-15 exchanges:

You: "Quick recap:
     - Built: [A, B, C]
     - Current: [D]
     - Next: [E]
     Agree?"

AI: [Confirms or corrects]
```

---

## Complete Example

### Scenario: Add Caching to Data Pipeline

**Full dialogue showing all principles in action:**

#### Session Start: Explore Options

```
You: "I need to add caching to reduce repeated data processing.
     What are my options?"

AI: "Main caching strategies:
     1. In-Memory (dict) - Fast, lost on restart
     2. LRU Cache (functools) - Built-in, auto-eviction
     3. Redis - Persistent, shared
     4. File-based - Simple, slower
     
     What's your use case?"

You: "Single process, expensive calculations, okay to lose on restart,
     want automatic management."

AI: "LRU Cache is perfect:
     Pros: ✅ Built-in ✅ Auto-management ✅ Simple
     Cons: ❌ Lost on restart (okay for you)
     
     Want to see implementation?"

You: "Yes, but let's plan it first."
```

#### Create Plan

```
You: "Create an implementation plan for adding LRU cache."

AI: "Implementation Plan:

1. Add cache key generation method
2. Add @lru_cache decorator to process method
3. Add cache size configuration
4. Add cache clear method for testing
5. Update tests

Each step is small and testable."

You: "Good. Save to temp/implementation_plan_caching.md"
```

#### Backup

```
You: "Before we start, I'll backup."

[You run: git commit -m "Before adding cache"]

You: "Backup done. Let's implement Step 1 only."
```

#### Implement Step-by-Step

```
You: "Implement Step 1: cache key generation method"

AI:
```python
def _generate_cache_key(self, data: Dict) -> str:
    """Generate unique cache key"""
    import hashlib, json
    sorted_data = json.dumps(data, sort_keys=True)
    return hashlib.md5(sorted_data.encode()).hexdigest()
```

You: [Applies change, tests]
You: "Works! Commit."

[git commit -m "Step 1: Add cache key generation"]

You: "Now Step 2: Add LRU cache decorator"

AI:
```python
from functools import lru_cache

@lru_cache(maxsize=100)
def process(self, data: Dict) -> Result:
    """Process data with caching"""
    key = self._generate_cache_key(data)
    # ... existing logic
```

You: [Applies, tests]
You: "Works! Commit."

[git commit -m "Step 2: Add LRU cache"]

... continue for remaining steps
```

#### Verify

```
You: "All steps complete. Run full test suite."

[Tests pass]

You: "Perfect! Update plan status to Complete."
```

**Key Points Demonstrated:**
- ✅ Explored options first
- ✅ Created plan before coding
- ✅ Backed up before changes
- ✅ Implemented incrementally
- ✅ Tested after each step
- ✅ Committed frequently
- ✅ Maintained working code throughout

---

---

## Structuring Projects for AI Assistance

Large codebases have a structure problem: the relevant context for any given task is scattered across many files, and AI context windows are finite.

Design your codebase to be navigable by a model that can only see a few thousand tokens at a time.

## File and module design

- **Keep files small and focused** — a 1,500-line file is hard for a human to reason about and impossible for a model to hold in context completely
- **One concept per file** — don't mix unrelated abstractions in the same module
- **Explicit imports over implicit globals** — AI can see what's imported; it cannot infer hidden globals or framework magic
- **Collocate interfaces with implementations** — when types and implementations are in the same file, the model sees the full contract

## Naming for AI readability

- Function and variable names are part of the prompt, even when you don't realize it
- Prefer `parse_transaction_from_csv` over `parse_data`; the model will make better decisions with the richer name
- Consistent naming patterns across the codebase compound — the model learns your conventions and reproduces them

## Context anchors

Create documents that give AI orientation when starting a new task:

- **`ARCHITECTURE.md`** — module map, key design decisions, patterns used
- **`CLAUDE.md` or `AGENTS.md`** — project-specific conventions, what to avoid, how to run tests, any gotchas
- **Interface files** — a single file that exports all public types and signatures for a module; paste this as context before asking for implementations
- **Pattern examples** — a canonical example of how you handle errors, pagination, auth — something you can paste and say "match this pattern"

---


## Prompting Patterns for Code

## Provide constraints explicitly

AI will make choices if you don't. Tell it what it cannot do:

> "Implement this using only the existing `db.Query` abstraction — do not introduce a new ORM or query builder."

> "Match the error handling pattern in `internal/api/handlers.go` — wrap errors with context, never swallow them."

> "No new dependencies. Use the standard library only."

Constraints are more important than descriptions. A vague description with tight constraints produces better code than a detailed description with no constraints.

## Reference existing patterns

> "Implement `DeleteUser` similar to how `DeleteOrganization` is implemented in `internal/org/service.go`."

This is more reliable than describing what you want from scratch. The model sees the existing code as a template and reproduces the patterns — error handling, logging, transaction management, all of it.

## Specify the signature before the implementation

```go
// ProcessPayment attempts to charge the given amount to the card on file.
// Returns ErrInsufficientFunds if the charge fails due to balance.
// Returns ErrCardExpired if the card is past its expiry date.
// All other errors should be wrapped with context and returned as-is.
func ProcessPayment(ctx context.Context, userID string, amount Money) error
```

Paste this and ask the model to implement it. You've defined the contract; it fills in the body. This also forces you to think about failure modes before implementation.

## Ask for tests alongside implementation

> "Implement the function and write table-driven tests covering: the happy path, each error case in the docstring, and at least two edge cases you identify."

Tests generated alongside code are more likely to be coherent with the implementation than tests written after.

## Ask for the explanation

> "Implement this, then explain: what assumptions did you make? What could go wrong at scale? What would you do differently with more time?"

This surfaces hidden assumptions before they become production bugs.

---


## Managing Context at Scale

Context management is the core engineering challenge of vibe coding at scale. The model's context window is finite; your codebase is not.

## Strategies

**Work file by file, not feature by feature.**
A feature touches many files. Trying to implement it all in one prompt produces incoherent output. Break it into: types first, then interface, then implementation, then tests — each in a focused prompt.

**Summarize context explicitly.**
Don't assume the model knows what it's working in. Open each session with a brief orientation:

> "This is a Go service that handles payment processing. The `internal/payment` package owns all payment logic. We use PostgreSQL via `db.Query`, errors are wrapped with `fmt.Errorf`, and all handlers follow the pattern in `handlers.go`. I'm about to ask you to implement X."

**Maintain a living context document.**
Keep a `CLAUDE.md` or `AI_CONTEXT.md` that you paste at the start of complex sessions. Include: what the project does, key patterns, what's currently in flight, and what to avoid. Update it as the project evolves.

**Use the model's output as input to the next prompt.**
After generating an interface, paste that interface into the next prompt that generates the implementation. After generating an implementation, paste it into the test-generation prompt. Each step gives the next step accurate context.

**Split long tasks into checkpoints.**
For a multi-file feature: implement one component, review it, commit it, then continue. Don't let AI generate ten files at once — the errors in the first file propagate into everything built on top of it.


## Documentation & Memory Management

Large software projects require managing two types of memory: **short-term** (within a session) and **long-term** (across sessions and team members). AI can help with both, but you must architect the memory system.

### The Memory Problem in Large Projects

**AI has no memory between sessions.** Every conversation starts fresh. For large projects:
- You can't re-explain the entire architecture every time
- Team members need shared understanding
- Decisions made months ago must be remembered
- Context grows faster than you can communicate it

**Solution:** Build a documentation system that serves as external memory for both humans and AI.

### Long-Term Memory: Living Documentation

**Create persistent context documents that evolve with your project:**

#### 1. Architecture Memory (`ARCHITECTURE.md`)

**Purpose:** High-level system design that rarely changes

```markdown
# System Architecture

## Overview
[2-3 sentence description of what the system does]

## Core Components
- **API Layer** (`src/api/`) - REST endpoints, authentication
- **Business Logic** (`src/core/`) - Domain models, business rules
- **Data Layer** (`src/data/`) - Database access, caching
- **Workers** (`src/workers/`) - Background jobs, async processing

## Key Design Decisions
1. **Monolithic architecture** - Chosen for team size (3 devs)
2. **PostgreSQL** - ACID compliance required for transactions
3. **Redis for caching** - Session data and computed results
4. **Event-driven workers** - Async processing via message queue

## Data Flow
[Diagram or description of how data moves through system]

## External Dependencies
- Payment API: Stripe
- Email: SendGrid
- Storage: AWS S3

## What NOT to Change
- Database schema without migration
- API contracts without versioning
- Authentication mechanism
```

**Using with AI:**
```
You: "Read ARCHITECTURE.md. I need to add a new feature for [X]"
AI: [Suggests implementation that fits architecture]
You: "Which component should own this?"
AI: [Recommends based on architecture doc]
```

#### 2. AI Context Memory (`AI_CONTEXT.md` or `CLAUDE.md`)

**Purpose:** Patterns, conventions, and current state for AI sessions

```markdown
# AI Context for [Project Name]

## Quick Facts
- Language: Python 3.11
- Framework: FastAPI
- Database: PostgreSQL 14
- Testing: pytest
- Style: Black formatter, type hints required

## Code Patterns We Use

### Error Handling
```python
# Always use this pattern
try:
    result = operation()
except SpecificError as e:
    logger.error(f"Operation failed: {e}")
    raise ServiceError(f"Failed to {action}") from e
```

### Database Queries
```python
# Always use parameterized queries
query = "SELECT * FROM users WHERE email = ?"
cursor.execute(query, (email,))
```

### API Responses
```python
# Standard response format
return {
    "success": True,
    "data": result,
    "error": None
}
```

## Current Work In Progress
- [ ] Adding payment processing (branch: feature/payments)
- [ ] Refactoring auth system (branch: refactor/auth)
- [ ] DO NOT TOUCH: Email system (being rewritten)

## Known Issues
- Cache invalidation needs improvement (tech debt)
- Test coverage low in workers/ (< 50%)
- Performance issue with large reports (> 10k rows)

## What AI Should NOT Do
- Never use eval() or exec()
- Never hardcode secrets
- Never modify database schema directly
- Never change authentication logic without review
- Never use deprecated libraries (see DEPENDENCIES.md)

## Recent Decisions
- 2024-04-15: Switched to Redis for sessions (was in-memory)
- 2024-04-10: Added rate limiting to all public APIs
- 2024-04-01: Migrated from SQLAlchemy 1.x to 2.x

## Team Conventions
- PR requires 2 approvals
- All features need tests (min 80% coverage)
- Security changes require security review
- Breaking changes need migration guide
```

**Using with AI:**
```
Session Start:
You: "Read AI_CONTEXT.md. I'm adding a new API endpoint for user profiles."
AI: [Follows patterns, avoids known issues, respects conventions]
```

#### 3. Decision Log (`DECISIONS.md` or ADRs)

**Purpose:** Record why decisions were made

```markdown
# Architecture Decision Records

## ADR-001: Use Monolithic Architecture
**Date:** 2024-01-15
**Status:** Accepted

### Context
Starting new project with 3 developers, need to ship in 3 months.

### Decision
Build as monolith with clear module boundaries.

### Consequences
✅ Faster development
✅ Simpler deployment
✅ Easier debugging
❌ Harder to scale team later
❌ All components share same deployment cycle

### Alternatives Considered
- Microservices: Too complex for team size
- Serverless: Vendor lock-in concerns

---

## ADR-002: PostgreSQL for Primary Database
**Date:** 2024-01-20
**Status:** Accepted

### Context
Need ACID transactions for payment processing.

### Decision
Use PostgreSQL 14 with connection pooling.

### Consequences
✅ ACID compliance
✅ Rich query capabilities
✅ Team has experience
❌ Vertical scaling limits
❌ Backup/restore complexity

### Alternatives Considered
- MongoDB: No ACID guarantees
- MySQL: Less feature-rich
```

**Using with AI:**
```
You: "Why did we choose PostgreSQL? Check DECISIONS.md"
AI: [Reads ADR-002, explains rationale]
You: "Should we add MongoDB for analytics?"
AI: [Considers existing decision, suggests approach]
```

### Short-Term Memory: Session Management

**Within a single coding session, manage context actively:**

#### Session Context Document (Temporary)

**Create at session start, delete when done:**

```markdown
# Session: Add Payment Processing - 2024-04-18

## Goal
Implement Stripe payment integration for subscription billing

## Scope (What we're doing)
- [ ] Add Stripe SDK dependency
- [ ] Create payment service interface
- [ ] Implement Stripe adapter
- [ ] Add webhook handler
- [ ] Write integration tests

## Out of Scope (What we're NOT doing)
- ❌ Refund processing (future)
- ❌ Multiple payment providers (future)
- ❌ Invoice generation (separate feature)

## Current Progress
✅ Added Stripe SDK
✅ Created payment interface
🔄 Implementing Stripe adapter (current)
⏳ Webhook handler (next)
⏳ Tests (after webhook)

## Key Decisions This Session
1. Using Stripe Checkout (not Payment Intents) - simpler for MVP
2. Webhooks for async confirmation - more reliable
3. Idempotency keys for all operations - prevent duplicates

## Files Modified
- `requirements.txt` - Added stripe==5.4.0
- `src/payments/interface.py` - Payment service interface
- `src/payments/stripe_adapter.py` - In progress

## Next Session
- Complete webhook handler
- Add comprehensive tests
- Update API documentation
```

**Using with AI:**
```
Every 10-15 exchanges:
You: "Quick recap - read session context doc. Are we on track?"
AI: [Reviews progress, confirms or suggests adjustments]

When resuming:
You: "Read session context. Where did we leave off?"
AI: [Picks up from current progress]
```

#### Context Refresh Pattern

**Periodically re-anchor the AI:**

```
Every 15-20 minutes or 10-15 exchanges:

You: "Context refresh:
     - Working on: [current task]
     - Just completed: [last step]
     - Next: [next step]
     - Constraints: [key constraints]
     Confirm understanding?"

AI: [Confirms or asks clarifying questions]
```

### Documentation Generation with AI

**Use AI to maintain documentation, not just code:**

#### 1. Generate API Documentation

```
You: "Generate OpenAPI spec for the payment endpoints we just created"
AI: [Creates OpenAPI YAML]

You: "Add examples for success and error cases"
AI: [Adds example requests/responses]

You: "Generate markdown documentation from this spec"
AI: [Creates human-readable docs]
```

#### 2. Update Architecture Docs

```
You: "We just added payment processing. Update ARCHITECTURE.md to include:
     - New payment component
     - Stripe as external dependency
     - Data flow for payment processing"
AI: [Updates architecture doc]

You: "Review the update. Does it fit the existing structure?"
AI: [Self-reviews, suggests improvements]
```

#### 3. Generate Decision Records

```
You: "Create an ADR for our decision to use Stripe Checkout instead of Payment Intents.
     Context: We chose it for simplicity in MVP.
     Tradeoffs: Less customization but faster implementation.
     Alternatives: Payment Intents (more complex), PayPal (different provider)"
AI: [Generates ADR in standard format]
```

#### 4. Create Runbooks

```
You: "Generate a runbook for handling failed payment webhooks.
     Include: How to detect, how to investigate, how to retry, when to escalate"
AI: [Creates operational runbook]

You: "Add monitoring queries and alert thresholds"
AI: [Adds operational details]
```

### Memory Hierarchy for Large Projects

**Organize documentation by access frequency:**

```
📁 docs/
├── 📄 README.md                    # First stop for everyone
├── 📄 ARCHITECTURE.md              # Read once, reference often
├── 📄 AI_CONTEXT.md                # Read every AI session
├── 📄 DECISIONS.md                 # Reference when needed
├── 📁 api/                         # API documentation
│   ├── openapi.yaml
│   └── endpoints.md
├── 📁 runbooks/                    # Operational guides
│   ├── deployment.md
│   ├── troubleshooting.md
│   └── incident-response.md
├── 📁 adrs/                        # Architecture decisions
│   ├── 001-monolithic-architecture.md
│   ├── 002-postgresql-database.md
│   └── 003-stripe-payments.md
└── 📁 sessions/                    # Temporary session notes
    └── 2024-04-18-payment-integration.md
```

### Team Memory: Shared Context

**For teams, documentation is shared memory:**

#### Onboarding New Developers

```markdown
# Onboarding Checklist

## Day 1: Read Core Docs
- [ ] README.md - Project overview
- [ ] ARCHITECTURE.md - System design
- [ ] AI_CONTEXT.md - Coding patterns
- [ ] DECISIONS.md - Why we built it this way

## Day 2: Setup & First Task
- [ ] Setup development environment
- [ ] Run tests (should all pass)
- [ ] Read runbooks/deployment.md
- [ ] Pick a "good first issue"

## Using AI for Onboarding
- Start every AI session with: "Read AI_CONTEXT.md"
- Ask AI to explain architecture: "Explain the data flow in ARCHITECTURE.md"
- Use AI to understand decisions: "Why did we choose PostgreSQL? Check DECISIONS.md"
```

#### Knowledge Transfer

```
When developer leaves or changes projects:

1. Create handoff document:
   - Current state of their work
   - Known issues and workarounds
   - Pending decisions
   - Key contacts

2. Update AI_CONTEXT.md:
   - Remove "in progress" items
   - Add "needs owner" items
   - Update team conventions

3. Record tribal knowledge:
   - Undocumented patterns
   - Gotchas and edge cases
   - Performance tips
```

### Vibe Coding Workflow with Documentation

**Complete workflow integrating documentation:**

```markdown
## Starting a New Feature

1. **Read Long-Term Memory**
   - ARCHITECTURE.md - Where does this fit?
   - AI_CONTEXT.md - What patterns to follow?
   - DECISIONS.md - Any relevant past decisions?

2. **Create Session Context**
   - Goal and scope
   - Out of scope
   - Key constraints

3. **Vibe Code with AI**
   - Start session: "Read AI_CONTEXT.md and session context"
   - Implement incrementally
   - Refresh context every 15 minutes

4. **Update Documentation**
   - Code comments for complex logic
   - API docs for new endpoints
   - Update AI_CONTEXT.md if patterns changed
   - Create ADR if architectural decision made

5. **Session Summary**
   - What was completed
   - What's pending
   - Key decisions made
   - Issues discovered

6. **Commit Everything**
   - Code changes
   - Documentation updates
   - Session summary (if valuable for team)
```

### Documentation Anti-Patterns

**Avoid these common mistakes:**

❌ **Stale Documentation**
- Problem: Docs don't match code
- Fix: Update docs in same PR as code changes

❌ **Over-Documentation**
- Problem: Too much detail, nobody reads it
- Fix: Keep high-level docs concise, details in code comments

❌ **No Documentation**
- Problem: Everything in people's heads
- Fix: Start with AI_CONTEXT.md and ARCHITECTURE.md

❌ **Documentation Debt**
- Problem: "We'll document it later" (never happens)
- Fix: Make documentation part of definition of done

❌ **AI-Generated Docs Without Review**
- Problem: AI hallucinates or misunderstands
- Fix: Always review and edit AI-generated documentation

### Memory Management Checklist

```markdown
## Long-Term Memory (Update Monthly)
- [ ] ARCHITECTURE.md reflects current system
- [ ] AI_CONTEXT.md has current patterns
- [ ] DECISIONS.md has recent ADRs
- [ ] Runbooks are tested and accurate
- [ ] API docs match implementation

## Short-Term Memory (Every Session)
- [ ] Create session context document
- [ ] Read relevant long-term docs
- [ ] Refresh context every 15 minutes
- [ ] Update docs if patterns change
- [ ] Create session summary

## Team Memory (Continuous)
- [ ] Onboarding docs up to date
- [ ] Knowledge transfer documented
- [ ] Tribal knowledge captured
- [ ] Team conventions documented
- [ ] Shared context maintained
```

### Using AI to Maintain Documentation

**AI is excellent at documentation maintenance:**

```
Weekly Documentation Review:

You: "Review ARCHITECTURE.md against current codebase.
     Check if:
     - All components are documented
     - External dependencies are current
     - Data flows are accurate
     Flag any discrepancies."
AI: [Reviews and reports issues]

You: "Update ARCHITECTURE.md with your findings"
AI: [Updates documentation]

You: "Generate a changelog of what changed"
AI: [Creates changelog for team review]
```

**Key Principle:** Documentation is code. Version it, review it, test it (by using it), and maintain it with the same rigor as your codebase.

---

---


## Quality Control and Code Review

**Never merge AI-generated code you don't understand.**

This sounds obvious. It is routinely violated. The output is fluent; it compiles; the tests pass; the reviewer skims it. Six months later there's a production incident rooted in a subtle error that nobody caught because nobody fully read the code.

## What to scrutinize in AI-generated code

**Error handling** — Does every error get handled or explicitly propagated? AI frequently generates code that ignores errors or swallows them with a comment.

**Edge cases** — Empty inputs, zero values, nil/null, concurrent access, very large inputs. AI tests the happy path; you test the edges.

**Security boundaries** — Any code that touches user input, file paths, SQL queries, authentication tokens, or privilege levels needs human review with adversarial thinking. AI doesn't reason about attackers.

**Resource management** — Are connections, files, and goroutines closed correctly? AI often misses cleanup in error paths.

**Performance characteristics** — Is there an N+1 query hiding in a loop? Does this algorithm degrade badly on the inputs you'll actually see in production?

## Review process adjustments

Shift code review focus from "is this syntactically correct" (the model handles that) to "is this architecturally correct and safe."

Ask the model to review its own output:

> "What are the weakest parts of this implementation? What would you change if you had more time?"

Ask it to attack its own output:

> "If you were trying to make this code fail in production, what would you exploit?"

These prompts surface things the standard review often misses.

---


## Iterative Workflow

Good vibe coding is not a single prompt. It is a structured iteration.

```
1. Specify       → Write the interface, docstring, or spec before touching implementation
2. Generate      → Prompt with full context and explicit constraints
3. Read          → Actually read the output; don't just run it
4. Question      → Ask the model to explain assumptions and weak points
5. Test          → Run existing tests; generate new ones for the new code
6. Refine        → Follow-up prompts to fix what the review found
7. Integrate     → Verify the new code fits the surrounding codebase patterns
8. Commit        → Commit with a message that describes the change, not the AI session
```

The most important step is step 3. Developers who skip reading the output are accumulating a debt they will pay with production incidents.

**Treat follow-up prompts as first-class work**, not corrections. "Narrow," "challenge," and "reframe" apply to code just as they do to any other AI interaction:

- *Narrow* — "You handled the database error case but not the network timeout case. Add timeout handling."
- *Challenge* — "You said this is O(n) — walk me through why."
- *Reframe* — "That implementation couples the cache to the database. Refactor to take the cache as an interface parameter."


## Security-First Vibe Coding

**Critical Principle:** AI generates code that works, not code that's secure. Security requires adversarial thinking that AI lacks.

### The Security Problem with AI-Generated Code

AI is trained on code that:
- Works in the happy path
- Passes functional tests
- Looks reasonable to reviewers

AI is NOT trained to:
- Think like an attacker
- Consider malicious inputs
- Understand security implications
- Recognize subtle vulnerabilities

**Result:** AI-generated code often has security holes that won't be caught by functional testing.

### Never Trust AI For Security-Critical Code

**These require mandatory human security review:**

1. **Authentication & Authorization**
   - Login/logout logic
   - Password handling
   - Session management
   - Permission checks
   - Token validation

2. **Input Validation & Sanitization**
   - User input processing
   - File uploads
   - API parameters
   - Database queries
   - Command execution

3. **Data Protection**
   - Encryption/decryption
   - Secret management
   - PII handling
   - Data access controls
   - Audit logging

4. **External Interfaces**
   - API endpoints
   - Database queries
   - File system access
   - Network calls
   - Third-party integrations

### Common Security Vulnerabilities in AI-Generated Code

#### 1. SQL Injection

**❌ AI Often Generates:**
```python
# VULNERABLE - String concatenation
query = f"SELECT * FROM users WHERE email = '{user_email}'"
cursor.execute(query)
```

**✅ You Must Enforce:**
```python
# SECURE - Parameterized query
query = "SELECT * FROM users WHERE email = ?"
cursor.execute(query, (user_email,))
```

**Vibe Coding Approach:**
```
You: "Create a user lookup function"
AI: [Generates code with string concatenation]
You: "STOP. Use parameterized queries to prevent SQL injection"
AI: [Fixes to use parameters]
You: "Add a test with malicious input: email = \"'; DROP TABLE users; --\""
AI: [Adds security test]
```

#### 2. Missing Input Validation

**❌ AI Often Generates:**
```python
def process_file(filename):
    with open(filename, 'r') as f:  # Path traversal vulnerability
        return f.read()
```

**✅ You Must Enforce:**
```python
def process_file(filename):
    # Validate filename
    if '..' in filename or filename.startswith('/'):
        raise ValueError("Invalid filename")
    
    # Use safe path joining
    safe_path = os.path.join(UPLOAD_DIR, os.path.basename(filename))
    
    with open(safe_path, 'r') as f:
        return f.read()
```

**Vibe Coding Approach:**
```
You: "This file handling code - what security issues do you see?"
AI: [May or may not identify path traversal]
You: "Check for path traversal attacks. Test with '../../../etc/passwd'"
AI: [Adds validation]
You: "Also validate file extension and size limits"
AI: [Adds additional checks]
```

#### 3. Hardcoded Secrets

**❌ AI Often Generates:**
```python
API_KEY = "sk-1234567890abcdef"  # NEVER DO THIS
db_password = "admin123"
```

**✅ You Must Enforce:**
```python
import os
API_KEY = os.environ.get('API_KEY')
if not API_KEY:
    raise ValueError("API_KEY environment variable not set")
```

**Vibe Coding Approach:**
```
You: "Add API integration"
AI: [May include example API key]
You: "NEVER hardcode secrets. Use environment variables"
AI: [Fixes to use env vars]
You: "Add validation that raises error if env var missing"
AI: [Adds validation]
```

#### 4. Missing Authentication/Authorization

**❌ AI Often Generates:**
```python
@app.route('/api/user/<user_id>')
def get_user(user_id):
    return User.query.get(user_id)  # No auth check!
```

**✅ You Must Enforce:**
```python
@app.route('/api/user/<user_id>')
@require_authentication
def get_user(user_id):
    # Check authorization
    if not current_user.can_access_user(user_id):
        abort(403)
    return User.query.get(user_id)
```

**Vibe Coding Approach:**
```
You: "Review this endpoint for security issues"
AI: [May miss auth check]
You: "This needs authentication. Add @require_authentication decorator"
AI: [Adds authentication]
You: "Also check authorization - users should only access their own data"
AI: [Adds authorization check]
```

#### 5. Information Leakage in Errors

**❌ AI Often Generates:**
```python
except Exception as e:
    return {"error": str(e)}  # Leaks internal details
```

**✅ You Must Enforce:**
```python
except Exception as e:
    logger.error(f"Database error: {e}")  # Log details
    return {"error": "Internal server error"}  # Generic message
```

#### 6. Missing Rate Limiting

**❌ AI Often Generates:**
```python
@app.route('/api/login', methods=['POST'])
def login():
    # No rate limiting - vulnerable to brute force
    username = request.json['username']
    password = request.json['password']
    return authenticate(username, password)
```

**✅ You Must Enforce:**
```python
from flask_limiter import Limiter

limiter = Limiter(app, key_func=get_remote_address)

@app.route('/api/login', methods=['POST'])
@limiter.limit("5 per minute")  # Rate limit
def login():
    username = request.json['username']
    password = request.json['password']
    return authenticate(username, password)
```

#### 7. Insecure Logging

**❌ AI Often Generates:**
```python
logger.info(f"User login: {username}, password: {password}")  # NEVER LOG PASSWORDS
logger.info(f"Processing payment: {credit_card_number}")  # NEVER LOG PII
```

**✅ You Must Enforce:**
```python
logger.info(f"User login attempt: {username}")  # No password
logger.info(f"Processing payment: card ending {credit_card_number[-4:]}")  # Masked
```

### Security Review Checklist for AI-Generated Code

**Before accepting ANY AI-generated code, verify:**

```markdown
## Input Validation
[ ] All user inputs validated (type, format, range)
[ ] File paths sanitized (no path traversal)
[ ] File uploads validated (type, size, content)
[ ] API parameters validated against schema
[ ] Reject invalid input, don't try to fix it

## SQL & Database
[ ] All queries use parameterized statements
[ ] No string concatenation in queries
[ ] Database errors don't leak schema info
[ ] Proper connection pooling and cleanup

## Authentication & Authorization
[ ] Authentication required for protected endpoints
[ ] Authorization checked before data access
[ ] Session management secure (timeout, regeneration)
[ ] Password handling uses proper hashing (bcrypt, argon2)
[ ] No authentication logic in client-side code

## Secrets & Credentials
[ ] No hardcoded secrets, passwords, or API keys
[ ] Secrets loaded from environment or vault
[ ] Secrets never logged or returned in responses
[ ] API keys rotated regularly

## Data Protection
[ ] Sensitive data encrypted at rest
[ ] TLS/HTTPS for data in transit
[ ] PII handled according to regulations (GDPR, etc.)
[ ] Proper data sanitization before logging

## Error Handling
[ ] Errors logged with context (for debugging)
[ ] Generic error messages to users (no details)
[ ] No stack traces exposed to users
[ ] Failed operations don't leak information

## API Security
[ ] Rate limiting on public endpoints
[ ] CORS configured correctly
[ ] Security headers set (CSP, X-Frame-Options, etc.)
[ ] Input size limits enforced
[ ] Request validation before processing

## Logging & Monitoring
[ ] Security events logged (login, access, changes)
[ ] No PII or secrets in logs
[ ] Failed authentication attempts logged
[ ] Anomaly detection configured
[ ] Logs protected from tampering

## Dependencies
[ ] Dependencies scanned for vulnerabilities
[ ] No deprecated or unmaintained libraries
[ ] Dependency versions pinned
[ ] Regular security updates applied
```

### Security-First Workflow

**1. Design Phase - Define Security Requirements**
```
You: "I need a user registration endpoint"
AI: [Suggests implementation]
You: "Before we implement, what are the security requirements?"
AI: [Lists password hashing, validation, rate limiting, etc.]
You: "Add: email verification, CAPTCHA, and account lockout after 5 failed attempts"
AI: [Updates requirements]
```

**2. Implementation Phase - Security by Default**
```
You: "Implement user registration with these security requirements"
AI: [Implements with security features]
You: "Review this for security vulnerabilities"
AI: [Self-reviews]
You: "Test with malicious inputs: SQL injection, XSS, path traversal"
AI: [Adds security tests]
```

**3. Review Phase - Adversarial Testing**
```
You: "What are the attack vectors for this code?"
AI: [Lists potential vulnerabilities]
You: "Create tests for each attack vector"
AI: [Generates security tests]
You: "What happens if an attacker sends 1000 requests per second?"
AI: [Identifies need for rate limiting]
```

**4. Documentation Phase - Security Notes**
```
You: "Document the security assumptions and requirements"
AI: [Creates security documentation]
You: "Add runbook for security incidents"
AI: [Creates incident response guide]
```

### Testing for Security

**Functional tests are NOT enough. Add security tests:**

```python
# Functional test (AI generates easily)
def test_user_login():
    response = client.post('/login', json={'username': 'alice', 'password': 'pass123'})
    assert response.status_code == 200

# Security tests (YOU must specify)
def test_sql_injection_in_login():
    response = client.post('/login', json={
        'username': "admin' OR '1'='1",
        'password': 'anything'
    })
    assert response.status_code == 401  # Should fail, not succeed

def test_rate_limiting():
    for i in range(10):
        response = client.post('/login', json={'username': 'alice', 'password': 'wrong'})
    assert response.status_code == 429  # Too many requests

def test_no_password_in_logs(caplog):
    client.post('/login', json={'username': 'alice', 'password': 'secret123'})
    assert 'secret123' not in caplog.text  # Password not logged
```

### Security Dialogue Patterns

**Pattern 1: Challenge Security Assumptions**
```
You: "This authentication code - what could go wrong?"
AI: [Lists potential issues]
You: "What if an attacker intercepts the token?"
AI: [Suggests token expiration, HTTPS]
You: "What if they replay an old valid token?"
AI: [Suggests nonce or timestamp validation]
```

**Pattern 2: Request Attack Scenarios**
```
You: "Generate 5 attack scenarios for this API endpoint"
AI: [Lists: SQL injection, XSS, CSRF, rate limit bypass, auth bypass]
You: "Create tests for each scenario"
AI: [Generates security test suite]
```

**Pattern 3: Security Code Review**
```
You: "Review this code as if you were a security auditor"
AI: [Identifies potential vulnerabilities]
You: "What's the worst-case scenario if this is exploited?"
AI: [Explains impact]
You: "Fix the top 3 vulnerabilities"
AI: [Implements fixes]
```

### When to Reject AI Suggestions

**Immediately reject if AI suggests:**

1. String concatenation in SQL queries
2. Hardcoded passwords or API keys
3. Disabling security features ("for testing")
4. Storing passwords in plain text
5. Using deprecated crypto algorithms (MD5, SHA1 for passwords)
6. Executing user input as code (eval, exec)
7. Trusting client-side validation only
8. Exposing internal errors to users

### Security is Human Responsibility

**Remember:**
- AI can implement security features you specify
- AI cannot identify security requirements
- AI cannot think adversarially
- AI cannot assess risk
- **YOU own the security of AI-generated code**


### Dangerous Functions & Safe Alternatives

**AI often suggests dangerous functions without understanding security implications. Always replace with safe alternatives.**

#### Python

**❌ NEVER USE:**
```python
# eval() - Executes arbitrary code
result = eval(user_input)  # DANGEROUS!

# exec() - Executes arbitrary code
exec(user_code)  # DANGEROUS!

# pickle.loads() - Can execute arbitrary code
data = pickle.loads(untrusted_data)  # DANGEROUS!

# os.system() / subprocess with shell=True
os.system(f"ls {user_path}")  # Shell injection!
subprocess.run(f"cat {filename}", shell=True)  # DANGEROUS!

# input() in Python 2 - Evaluates input
value = input("Enter value: ")  # Python 2 only, DANGEROUS!
```

**✅ USE INSTEAD:**
```python
# For eval() - Use ast.literal_eval() for safe evaluation
import ast
result = ast.literal_eval(user_input)  # Only evaluates literals

# Or use simpleeval for safe expression evaluation
from simpleeval import simple_eval
result = simple_eval(user_input, names={"x": 10})  # Controlled evaluation

# For exec() - Redesign to avoid dynamic code execution
# Use configuration, plugins, or DSL instead

# For pickle - Use json for untrusted data
import json
data = json.loads(untrusted_data)  # Safe for data

# For os.system - Use subprocess with list arguments
subprocess.run(["ls", user_path])  # No shell injection
subprocess.run(["cat", filename], shell=False)  # Safe

# For input() - Use raw_input() in Python 2, input() in Python 3
value = raw_input("Enter value: ")  # Python 2, safe
value = input("Enter value: ")  # Python 3, safe
```

**Vibe Coding Dialogue:**
```
AI: "Here's the code using eval()..."
You: "STOP. Never use eval(). Use ast.literal_eval() or simpleeval instead"
AI: [Rewrites with safe alternative]
You: "Add a comment explaining why we don't use eval()"
AI: [Adds security comment]
```

#### JavaScript/Node.js

**❌ NEVER USE:**
```javascript
// eval() - Executes arbitrary code
eval(userInput);  // DANGEROUS!

// Function constructor - Similar to eval
new Function(userCode)();  // DANGEROUS!

// setTimeout/setInterval with string
setTimeout(userCode, 1000);  // DANGEROUS!

// innerHTML with user content
element.innerHTML = userInput;  // XSS vulnerability!

// document.write with user content
document.write(userInput);  // XSS vulnerability!
```

**✅ USE INSTEAD:**
```javascript
// For eval() - Use JSON.parse for data
const data = JSON.parse(userInput);  // Safe for JSON

// Or use a safe expression evaluator
const { evaluate } = require('safe-eval-expression');
const result = evaluate(userInput, context);

// For setTimeout - Use function reference
setTimeout(() => safeFunction(), 1000);  // Safe

// For innerHTML - Use textContent or sanitize
element.textContent = userInput;  // Safe, no HTML
// Or use DOMPurify for HTML
element.innerHTML = DOMPurify.sanitize(userInput);  // Sanitized

// For document.write - Use DOM methods
const node = document.createTextNode(userInput);
element.appendChild(node);  // Safe
```

#### SQL

**❌ NEVER USE:**
```python
# String concatenation or f-strings
query = f"SELECT * FROM users WHERE id = {user_id}"  # SQL injection!
query = "SELECT * FROM users WHERE name = '" + username + "'"  # DANGEROUS!
```

**✅ USE INSTEAD:**
```python
# Parameterized queries (Python)
query = "SELECT * FROM users WHERE id = ?"
cursor.execute(query, (user_id,))

# Named parameters
query = "SELECT * FROM users WHERE name = :name"
cursor.execute(query, {"name": username})

# ORM (SQLAlchemy, Django ORM)
User.objects.filter(name=username)  # Safe, parameterized
```

#### Shell Commands

**❌ NEVER USE:**
```python
# String concatenation in shell commands
os.system(f"rm {filename}")  # Shell injection!
subprocess.run(f"tar -xzf {archive}", shell=True)  # DANGEROUS!
```

**✅ USE INSTEAD:**
```python
# List of arguments (no shell)
subprocess.run(["rm", filename])  # Safe
subprocess.run(["tar", "-xzf", archive])  # Safe

# Use shlex.quote() if shell is unavoidable
import shlex
safe_filename = shlex.quote(filename)
os.system(f"rm {safe_filename}")  # Safer, but still avoid
```

#### Cryptography

**❌ NEVER USE:**
```python
# Weak hash algorithms for passwords
import hashlib
password_hash = hashlib.md5(password.encode()).hexdigest()  # WEAK!
password_hash = hashlib.sha1(password.encode()).hexdigest()  # WEAK!

# Custom crypto implementations
def my_encrypt(data, key):  # DON'T ROLL YOUR OWN CRYPTO!
    return data ^ key
```

**✅ USE INSTEAD:**
```python
# Use bcrypt, argon2, or scrypt for passwords
import bcrypt
password_hash = bcrypt.hashpw(password.encode(), bcrypt.gensalt())

# Or argon2 (recommended)
from argon2 import PasswordHasher
ph = PasswordHasher()
password_hash = ph.hash(password)

# Use established crypto libraries
from cryptography.fernet import Fernet
cipher = Fernet(key)
encrypted = cipher.encrypt(data)
```

#### Regular Expressions

**❌ DANGEROUS PATTERNS:**
```python
# ReDoS (Regular Expression Denial of Service)
import re
# Catastrophic backtracking patterns
pattern = r"(a+)+"  # DANGEROUS!
pattern = r"(a|a)*"  # DANGEROUS!
pattern = r"(a|ab)*"  # DANGEROUS!

if re.match(pattern, user_input):  # Can hang on malicious input
    pass
```

**✅ USE INSTEAD:**
```python
# Use timeout (Python 3.11+)
import re
try:
    re.match(pattern, user_input, timeout=1.0)  # 1 second timeout
except TimeoutError:
    # Handle timeout

# Or use simpler patterns
pattern = r"a+"  # Safe, no nested quantifiers

# Or use regex libraries with protection
import regex  # Has built-in protection
regex.match(pattern, user_input)
```

### Package Security: Deprecated & Vulnerable Libraries

**Check before using AI-suggested packages:**

#### Python Packages to Avoid

**❌ AVOID:**
```python
# pycrypto - Deprecated, use pycryptodome
from Crypto.Cipher import AES  # Old, vulnerable

# requests with verify=False
requests.get(url, verify=False)  # Disables SSL verification!

# PyYAML with unsafe loading
yaml.load(data)  # Can execute arbitrary code!

# pickle for untrusted data
pickle.loads(untrusted_data)  # Can execute code
```

**✅ USE INSTEAD:**
```python
# pycryptodome (maintained fork)
from Cryptodome.Cipher import AES

# requests with proper SSL
requests.get(url, verify=True)  # Default, keep it!

# PyYAML with safe loading
yaml.safe_load(data)  # Safe, no code execution

# json for untrusted data
json.loads(untrusted_data)  # Safe for data
```

#### JavaScript Packages to Avoid

**❌ AVOID:**
```javascript
// Packages with known vulnerabilities (check npm audit)
// lodash < 4.17.21 - Prototype pollution
// moment < 2.29.4 - ReDoS
// axios < 0.21.1 - SSRF

// eval-based template engines
const template = require('lodash.template');
template(userInput)();  // Can execute code!
```

**✅ USE INSTEAD:**
```javascript
// Keep packages updated
npm audit fix

// Use safe template engines
const Handlebars = require('handlebars');
const template = Handlebars.compile(templateString);
template(data);  // Safe, no code execution
```

### Vibe Coding Security Workflow

**When AI suggests a function or package:**

```
1. AI: "Here's the implementation using eval()..."

2. You: "What security risks does eval() have?"
   AI: [Explains arbitrary code execution risk]

3. You: "What's the safe alternative?"
   AI: [Suggests ast.literal_eval or simpleeval]

4. You: "Rewrite using the safe alternative"
   AI: [Implements with safe function]

5. You: "Add a comment explaining why we avoid eval()"
   AI: [Adds security documentation]

6. You: "Add a test with malicious input"
   AI: [Adds security test]
```

### Security Review Questions for AI

**Always ask when reviewing AI-generated code:**

```
You: "Does this code use any of these dangerous functions?"
- eval(), exec(), pickle.loads()
- os.system(), subprocess with shell=True
- SQL string concatenation
- innerHTML with user input
- MD5/SHA1 for passwords

You: "Are there safer alternatives to the libraries used?"

You: "What happens if an attacker provides malicious input?"

You: "Show me the security tests for this code"
```

### Team Convention: Banned Functions List

**Create a team-wide banned functions list:**

```markdown
# Banned Functions & Packages

## Never Use (Auto-reject in code review)
- Python: eval(), exec(), pickle.loads(), os.system()
- JavaScript: eval(), Function(), innerHTML (without sanitization)
- SQL: String concatenation in queries
- Crypto: MD5, SHA1 for passwords; custom crypto

## Require Justification & Security Review
- subprocess with shell=True (use list args instead)
- Regular expressions with nested quantifiers
- File operations with user-provided paths
- Network requests without timeout

## Deprecated Packages (Update or Replace)
- Python: pycrypto → pycryptodome
- JavaScript: Check npm audit regularly
```

**Add to your prompting guide:**
```
"Never use eval(), exec(), or pickle.loads(). 
Always use parameterized SQL queries.
Always sanitize user input before rendering HTML.
Use bcrypt or argon2 for password hashing."
```

**Golden Rule:** If it touches user input, authentication, authorization, or sensitive data - assume it's vulnerable until proven otherwise through human security review.

---

---


## Team Practices

Vibe coding without team conventions produces inconsistent codebases where different sections look like they were written by different people — because they were, through different AI sessions with different prompts.

## Shared conventions

Write a **team prompting guide** the same way you write a code style guide. It should specify:

- What context to always provide (architecture summary, key patterns)
- What constraints to always include (approved dependencies, error handling conventions)
- What not to ask AI to do (security-critical code, database migrations without review)
- How to document AI-generated sections (or whether to, based on your team's policy)

## Code review culture

- Reviewers should be able to understand every line — "the AI wrote it" is not an explanation
- Flag generated code that the author clearly doesn't understand; send it back
- Reward understanding over speed

## Shared context documents

Maintain `ARCHITECTURE.md` and `CLAUDE.md` (or equivalent) as first-class artifacts in the repository. Keep them current. Stale context documents mislead both humans and AI.

## Pair AI sessions

Two humans plus one AI works well for complex problems. One person prompts; the other reviews and challenges. The AI doesn't replace the pair — it joins the pair.

---


## Common Pitfalls

### Beginner Pitfalls

**Not exploring options** — Accepting the first AI suggestion without asking for alternatives. Fix: Always ask "What are other approaches?" and compare pros/cons before deciding.

**Changes too large** — Attempting big refactorings that break everything. Fix: Apply the 5-minute rule, break into baby steps, keep code always working.

**No backup** — Can't revert when things break. Fix: `git commit` before every change, compare old vs new, easy rollback.

**Losing context** — Long sessions where AI forgets earlier decisions. Fix: Periodic context refresh, cold start with summary, save session notes.

**Mixed concerns** — Components doing too much. Fix: One component one job, clean APIs, use focus window technique.

**No plan** — Jumping straight to code without thinking. Fix: Create implementation plan, review with AI, implement step-by-step.

### Advanced Pitfalls

**Vibe drift** — The codebase gradually loses coherence as AI fills in details inconsistently across sessions. Different error handling styles, inconsistent naming, duplicated abstractions with slightly different behaviors. Fix: enforce patterns explicitly in every prompt; review for consistency at the module level, not just the function level.

**Specification debt** — Moving fast by skipping specs means the AI invented the design. This feels productive for weeks and becomes a maintenance crisis at month six. Fix: write the spec before generating the implementation, every time.

**Hallucinated dependencies** — AI confidently uses library functions that don't exist, or APIs that were deprecated two versions ago. Fix: always check imports and API calls against actual documentation, especially for libraries that have changed frequently.

**Security blind spots** — AI is not trained to think like an attacker. It generates code that works in the happy path and fails badly under adversarial input. Fix: all security-sensitive code requires adversarial human review, not just functional review.

**Test theater** — AI generates tests that pass but don't test much. Tests that only cover the happy path, that mock everything, or that duplicate the implementation logic instead of verifying behavior. Fix: write test specifications before generating tests; review tests with the same scrutiny as implementation.

**Ownership diffusion** — When AI writes code, developers sometimes feel less accountable for it. "The AI did it" becomes an implicit excuse. Fix: establish clearly that the human who merges the code owns it, fully, regardless of how it was generated.

**Context contamination** — A long AI session where early mistakes propagate into later generations. The model builds on its own flawed output. Fix: commit working checkpoints frequently; start fresh sessions for distinct components.

---


## What Scales, What Doesn't

| Scales well with AI | Requires human ownership |
|---|---|
| Implementation within defined interfaces | Interface and architecture design |
| Test case generation | Test strategy and coverage decisions |
| Boilerplate and scaffolding | Cross-cutting conventions |
| Documentation of existing code | Domain knowledge and business rules |
| Isolated refactoring tasks | System-wide refactors |
| Format conversion and translation | Security review |
| Exploring solution options | Final design decisions |

The pattern: AI handles **breadth** well — covering all the cases of a well-defined thing. Humans handle **depth** well — understanding what the right thing is to build in the first place.

Scale your AI usage by getting better at defining the problem. The developers who get the most out of vibe coding are the ones who invest more in specification, architecture, and review — not the ones who prompt faster.

## Templates & Checklists

### Before Coding Checklist

```
[ ] Explored implementation options?
[ ] Compared pros/cons?
[ ] Made informed decision?
[ ] Created backup?
[ ] Created implementation plan?
[ ] Reviewed plan with AI?
[ ] Broke into small steps?
```

### During Coding Checklist

```
[ ] Working on ONE component?
[ ] Making ONE small change?
[ ] Can test in <5 minutes?
[ ] Tested immediately?
[ ] Committed if working?
[ ] Existing code still works?
```

### Before Committing Checklist

```
[ ] All tests pass?
[ ] Manually tested?
[ ] No debug code left?
[ ] Clear commit message?
[ ] Can explain what changed?
```

### Session Context Template

```markdown
# Session: [Topic] - [Date]

## Context
Previous: [What was done before]
Today: [What we're doing]

## Files
- [file1]: [purpose]
- [file2]: [purpose]

## Key Decisions
- [Decision 1]: [Reasoning]
- [Decision 2]: [Reasoning]

## Status
✅ Done: [List]
🚧 Current: [What]
📋 Next: [What]
```

### Implementation Plan Template

```markdown
# Plan: [Feature]

## Goal
[One sentence]

## Steps
1. [ ] [Step]: File [X], Action [Y], Test [Z]
2. [ ] [Step]: File [X], Action [Y], Test [Z]

## Risks
- [Risk]: [Mitigation]

## Rollback
git checkout [files]
```

### Commit Message Format

```
[Step X]: [What changed]

- [Detail 1]
- [Detail 2]

Test: [How verified]
```

---

## Summary: The Vibe Coding Way

### Core Principles

1. **Explore Options** - Get alternatives, compare, decide
2. **Component Separation** - One job, clean API, focused
3. **Incremental Development** - Tiny changes, always working
4. **Session Management** - Context for cold start, flow for hot
5. **Backup & Plan** - Safety first, think then code

### Daily Workflow

```
Morning:
- Start session with context
- Review plan for today

During:
- Explore options for decisions
- Make one small change
- Test immediately
- Commit if working
- Repeat

Evening:
- Create session summary
- Save for next time
```

### The Mantra

```
Explore before deciding
Separate before mixing
Small before large
Backup before changing
Plan before coding
Test before committing
```

### Remember

- AI is your partner, not magic
- Dialogue reveals better solutions
- Small steps compound into big results
- Working code beats perfect code
- You can always refine later

**Master the dialogue, master the code.**

---

*This guide is based on real-world experience building complex software with AI assistance. Adapt these practices to your specific needs and context.*