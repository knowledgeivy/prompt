# Vibe Coding at Scale

A practical guide for experienced developers scaling up AI assistance on large software projects.

---

## Contents

1. [The Mental Model Shift](#the-mental-model-shift)
2. [Where It Works — and Where It Breaks](#where-it-works--and-where-it-breaks)
3. [Architecture Before You Vibe](#architecture-before-you-vibe)
4. [Structuring Projects for AI Assistance](#structuring-projects-for-ai-assistance)
5. [Prompting Patterns for Code](#prompting-patterns-for-code)
6. [Managing Context at Scale](#managing-context-at-scale)
7. [Quality Control and Code Review](#quality-control-and-code-review)
8. [Iterative Workflow](#iterative-workflow)
9. [Team Practices](#team-practices)
10. [Common Pitfalls](#common-pitfalls)

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

## Structuring Projects for AI Assistance

Large codebases have a structure problem: the relevant context for any given task is scattered across many files, and AI context windows are finite.

Design your codebase to be navigable by a model that can only see a few thousand tokens at a time.

### File and module design

- **Keep files small and focused** — a 1,500-line file is hard for a human to reason about and impossible for a model to hold in context completely
- **One concept per file** — don't mix unrelated abstractions in the same module
- **Explicit imports over implicit globals** — AI can see what's imported; it cannot infer hidden globals or framework magic
- **Collocate interfaces with implementations** — when types and implementations are in the same file, the model sees the full contract

### Naming for AI readability

- Function and variable names are part of the prompt, even when you don't realize it
- Prefer `parse_transaction_from_csv` over `parse_data`; the model will make better decisions with the richer name
- Consistent naming patterns across the codebase compound — the model learns your conventions and reproduces them

### Context anchors

Create documents that give AI orientation when starting a new task:

- **`ARCHITECTURE.md`** — module map, key design decisions, patterns used
- **`CLAUDE.md` or `AGENTS.md`** — project-specific conventions, what to avoid, how to run tests, any gotchas
- **Interface files** — a single file that exports all public types and signatures for a module; paste this as context before asking for implementations
- **Pattern examples** — a canonical example of how you handle errors, pagination, auth — something you can paste and say "match this pattern"

---

## Prompting Patterns for Code

### Provide constraints explicitly

AI will make choices if you don't. Tell it what it cannot do:

> "Implement this using only the existing `db.Query` abstraction — do not introduce a new ORM or query builder."

> "Match the error handling pattern in `internal/api/handlers.go` — wrap errors with context, never swallow them."

> "No new dependencies. Use the standard library only."

Constraints are more important than descriptions. A vague description with tight constraints produces better code than a detailed description with no constraints.

### Reference existing patterns

> "Implement `DeleteUser` similar to how `DeleteOrganization` is implemented in `internal/org/service.go`."

This is more reliable than describing what you want from scratch. The model sees the existing code as a template and reproduces the patterns — error handling, logging, transaction management, all of it.

### Specify the signature before the implementation

```go
// ProcessPayment attempts to charge the given amount to the card on file.
// Returns ErrInsufficientFunds if the charge fails due to balance.
// Returns ErrCardExpired if the card is past its expiry date.
// All other errors should be wrapped with context and returned as-is.
func ProcessPayment(ctx context.Context, userID string, amount Money) error
```

Paste this and ask the model to implement it. You've defined the contract; it fills in the body. This also forces you to think about failure modes before implementation.

### Ask for tests alongside implementation

> "Implement the function and write table-driven tests covering: the happy path, each error case in the docstring, and at least two edge cases you identify."

Tests generated alongside code are more likely to be coherent with the implementation than tests written after.

### Ask for the explanation

> "Implement this, then explain: what assumptions did you make? What could go wrong at scale? What would you do differently with more time?"

This surfaces hidden assumptions before they become production bugs.

---

## Managing Context at Scale

Context management is the core engineering challenge of vibe coding at scale. The model's context window is finite; your codebase is not.

### Strategies

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

### What to scrutinize in AI-generated code

**Error handling** — Does every error get handled or explicitly propagated? AI frequently generates code that ignores errors or swallows them with a comment.

**Edge cases** — Empty inputs, zero values, nil/null, concurrent access, very large inputs. AI tests the happy path; you test the edges.

**Security boundaries** — Any code that touches user input, file paths, SQL queries, authentication tokens, or privilege levels needs human review with adversarial thinking. AI doesn't reason about attackers.

**Resource management** — Are connections, files, and goroutines closed correctly? AI often misses cleanup in error paths.

**Performance characteristics** — Is there an N+1 query hiding in a loop? Does this algorithm degrade badly on the inputs you'll actually see in production?

### Review process adjustments

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

### Shared conventions

Write a **team prompting guide** the same way you write a code style guide. It should specify:

- What context to always provide (architecture summary, key patterns)
- What constraints to always include (approved dependencies, error handling conventions)
- What not to ask AI to do (security-critical code, database migrations without review)
- How to document AI-generated sections (or whether to, based on your team's policy)

### Code review culture

- Reviewers should be able to understand every line — "the AI wrote it" is not an explanation
- Flag generated code that the author clearly doesn't understand; send it back
- Reward understanding over speed

### Shared context documents

Maintain `ARCHITECTURE.md` and `CLAUDE.md` (or equivalent) as first-class artifacts in the repository. Keep them current. Stale context documents mislead both humans and AI.

### Pair AI sessions

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
