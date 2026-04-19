# Vibe Coding at Scale

A practical guide for building large software systems with AI assistance through effective dialogue, architecture-first thinking, and iterative collaboration.

---

## Contents

**Quick Start**
- [Quick Reference Cheat Sheet](#quick-reference-cheat-sheet)

**Foundation**
1. [The Mental Model Shift](#the-mental-model-shift)
2. [Where It Works — and Where It Breaks](#where-it-works--and-where-it-breaks)
3. [Core Principles](#core-principles)

**Architecture & Structure**

4. [Architecture Before You Vibe](#architecture-before-you-vibe)
5. [Structuring Projects for AI Assistance](#structuring-projects-for-ai-assistance)
6. [Component Separation & Clean APIs](#component-separation--clean-apis)

**Practical Techniques**

7. [Exploring Multiple Implementation Options](#exploring-multiple-implementation-options)
8. [Prompting Patterns for Code](#prompting-patterns-for-code)
9. [Incremental Development](#incremental-development)
10. [Managing Context at Scale](#managing-context-at-scale)
11. [Session Management](#session-management)

**Quality & Safety**

12. [Backup & Plan-Review-Implement](#backup--plan-review-implement)
13. [Quality Control and Code Review](#quality-control-and-code-review)
14. [Iterative Workflow](#iterative-workflow)

**Team & Scale**

15. [Team Practices](#team-practices)
16. [Common Pitfalls](#common-pitfalls)
17. [What Scales, What Doesn't](#what-scales-what-doesnt)

**Appendix**
18. [Templates & Checklists](#templates--checklists)

---

## Quick Reference Cheat Sheet

### Core Principles
1. **Component Separation** - One component, one responsibility, clean APIs
2. **Incremental Development** - Tiny changes (5-min rule), always working code
3. **Explore Options** - Get multiple approaches, compare pros/cons, decide
4. **Session Management** - Cold start with context, hot start for continuity
5. **Backup & Plan** - Always backup, create plan, review, then implement

### Daily Workflow
```
Morning:  Start session → Provide context → Review plan
During:   Small change → Test → Commit → Repeat
Evening:  Create session summary → Save for next time
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
2. Explore Options (NEW!)
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

## Common Pitfalls

### Pitfall 1: Not Exploring Options

**Problem:** Accepting first suggestion

**Solution:**
```
Always ask: "What are other approaches?"
Compare: "Give pros/cons for my case"
Decide: "Let's go with [X] because [reason]"
```

### Pitfall 2: Changes Too Large

**Problem:** Big refactorings that break everything

**Solution:**
```
Apply 5-minute rule
Break into baby steps
Keep code always working
```

### Pitfall 3: No Backup

**Problem:** Can't revert when things break

**Solution:**
```
git commit before every change
Can compare old vs new
Easy rollback
```

### Pitfall 4: Losing Context

**Problem:** Long sessions where AI forgets

**Solution:**
```
Periodic context refresh
Cold start with summary
Save session notes
```

### Pitfall 5: Mixed Concerns

**Problem:** Components doing too much

**Solution:**
```
One component, one job
Clean APIs
Focus window technique
```

### Pitfall 6: No Plan

**Problem:** Jumping straight to code

**Solution:**
```
Create implementation plan
Review with AI
Implement step-by-step
```

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