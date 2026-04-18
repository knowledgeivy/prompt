# How to Ask Large Language Models: From Language and Knowledge to Responsibility

A teaching guide exploring the principles of effective prompting—and what it reveals about how we think.

---

## Contents

1. [Why We Must Relearn How to Ask](#why-we-must-relearn-how-to-ask)
2. [What a Large Language Model Is Actually Doing](#what-a-large-language-model-is-actually-doing)
3. [Questions Are Not Neutral](#questions-are-not-neutral)
4. [Why Precise Questioning Matters](#why-precise-questioning-matters)
5. [Grammar and Clarity](#grammar-and-clarity)
6. [Semantic Precision](#semantic-precision)
7. [Embeddings and Representation](#embeddings-and-representation)
8. [Hallucination: A Shared Problem](#hallucination-a-shared-problem)
9. [Responsible Use](#responsible-use)
10. [A Layered Approach to Prompting](#a-layered-approach-to-prompting)
11. [Real-World Examples](#real-world-examples)
12. [Conclusion: Prompting as Self-Discipline](#conclusion-prompting-as-self-discipline)

---

## Why We Must Relearn How to Ask

Language models now serve as research assistants, writing aids, and thinking partners. People turn to them to understand news, analyze trends, explore history, organize arguments, and generate recommendations.

A seemingly simple question emerges: **How should we ask?**

At first glance, this looks like a technical problem—does a longer, more detailed prompt simply yield better answers?

But on closer reflection, it is not only technical. **It is first a linguistic problem, then an epistemological problem, and finally one of responsibility.**

---

## What a Large Language Model Is Actually Doing

Language models appear to answer questions, but not because they directly perceive reality or inherently possess truth.

Their core mechanism: **learn statistical patterns from text, then predict what is most likely to come next.**

They are fundamentally **probabilistic language prediction systems**—and only secondarily do they appear to understand, explain, reason, or create.

This means:
- An answer from a model is often a high-probability output in language space
- It may be useful, fluent, and close to reality
- But it is not identical with reality itself

---

## Questions Are Not Neutral

Not all questions are of the same kind. The same topic can be approached at different levels:

**In Politics:**
- "What does this policy actually say?" → factual
- "How do supporters and critics interpret it?" → explanatory
- "What effects might it have?" → predictive
- "Should one support it?" → evaluative

**In Economics:**
- "What is the current inflation rate?" → fact
- "Why do people perceive inflation differently than official data?" → analysis
- "Will there be a recession?" → prediction
- "Should I adjust my investments?" → advice

**In History:**
- "When did this event occur?" → fact
- "Why did it happen?" → explanation
- "What if events had unfolded differently?" → counterfactual reasoning

**A mature questioner first knows what they're actually asking for: facts, explanation, prediction, or advice.**

---

## Why Precise Questioning Matters

Many assume good prompting means writing longer, more elaborate requests.

It does not. A good prompt is **clearer, not longer; more precise, not more ornate.**

Precise questioning includes:

1. **Identifying question type** – fact, explanation, prediction, or advice?
2. **Limiting scope** – which region, period, dataset, person, or timeframe?
3. **Stating the goal** – understanding a concept or making a decision?
4. **Specifying constraints** – which sources, standards, or perspectives?
5. **Separating layers** – don't mix facts, opinions, predictions, and value judgments in one sentence

The purpose is not to make the model obedient, but to **reduce misunderstanding, guesswork, and semantic drift.**

---

## Grammar and Clarity

Grammar is not decoration. It encodes relationships:

- Who is doing what
- Which condition limits which conclusion
- What is causal, contrastive, or hypothetical
- What is central vs. merely contextual

**When grammar is unclear, the model must infer missing information.** The more it guesses, the more likely it produces something linguistically plausible but misaligned with your intent.

### Why this matters in training

It would be oversimplified to say models were trained only on grammatically correct text. More accurately:

- High-quality pretraining data tends to include more readable, well-formed text
- But training data also contains colloquial, noisy, and imperfect language
- Still, models learn their most stable syntactic and discourse patterns from relatively well-formed text

**Grammar matters not because models refuse imperfect language, but because clearer expression aligns better with the stable patterns they've learned.**

---

## Semantic Precision

Many words appear universally understood but lack clear boundaries:

- freedom, fairness, failure, crisis
- conservative, radical, effective, reliable

These appear everywhere—politics, economics, history, daily conversation—but their meaning is **highly context-dependent.**

### Vague words expand the answer-space

If you ask whether a policy is "fair," a measure is "effective," or a figure was "bad," the model must guess which standard you mean. It fills the gap with probabilistic guesses.

### Better: unpack abstract terms

Instead of:
> Is this policy fair?

Ask:
- Does it affect different income groups equally?
- Who bears the costs and who reaps the benefits?
- Is it legally equal or outcome-equal?

Instead of:
> Is it effective?

Ask:
- Does it achieve what the policy statement claims?
- Short-term or long-term effectiveness?
- Does it create new unintended consequences?

**The clearer the terms, the easier the answer becomes to test, compare, and discuss.**

---

## Embeddings and Representation

A modern language model maps words, phrases, and contexts into high-dimensional vector space. This is not a dictionary definition but a **distributed representation learned from how expressions occur across many contexts.**

### How this relates to clarity

Semantically related expressions cluster closer in representation space—"inflation," "rising prices," and "cost of living pressure" may be nearby. But closeness is not identity. The same word shifts meaning across contexts.

**Vague terms activate a broad semantic neighborhood rather than a tightly bounded meaning.** When you use ambiguous words, the model may activate many related but inconsistent regions, causing it to follow common templates rather than target your intent precisely.

**Embeddings don't "cause" ambiguity, but they help explain why semantically vague prompts are harder to anchor.**

---

## Hallucination: A Shared Problem

"Hallucination" is often dismissed as the model "making things up."

More deeply: the model generates **what looks most like a plausible answer in language, not what is guaranteed to match reality.**

If your question is poorly defined, evidence is insufficient, or semantic boundaries are fuzzy, the model produces something coherent-sounding but unreliable.

**Hallucination is not only a machine-side problem—it is often a problem of how the question was framed.**

---

## Responsible Use

Using language models well means refusing to misuse them.

Common misuses:

1. **Justifying predetermined conclusions** – "Prove this policy must fail"
2. **Disguising rhetoric as analysis** – "The economy is clearly doomed; write this more persuasively"
3. **Outsourcing judgment** – "Just tell me who's right without explanation"
4. **Treating the model as final authority** – No longer checking sources, comparing interpretations, or distinguishing facts from speculation

---

## A Layered Approach to Prompting

Convert complex questions into a disciplined sequence:

### Layer 1: Facts
What information is relatively stable and verifiable?

### Layer 2: Explanation
How do different perspectives interpret these facts? What are they based on?

### Layer 3: Prediction
If conditions continue, what different outcomes might emerge?

### Layer 4: Advice
Given different goals and constraints, what should be done?

This separation **reduces confusion and ambiguity.**

---

## Real-World Examples

### Politics
**Weak prompt:**
> Has this policy completely failed? Is the media just manipulating the narrative?

**Better prompt:**
1. What are the main contents of the policy?
2. What are the core arguments from supporters and critics?
3. What public data or reporting supports each side?
4. How might it be judged under different standards—short-term public opinion, long-term fiscal impact, legal feasibility?

### Economics
**Weak prompt:**
> The economy is so bad, should I sell all my stocks?

**Better prompt:**
1. What indicators show economic slowdown?
2. Are these indicators consistent or contradictory?
3. What different market outcomes occurred in historically similar conditions?
4. What do different risk tolerances imply for this situation?

### History
**Weak prompt:**
> Was this historical figure good or bad?

**Better prompt:**
1. What facts about this person are relatively uncontested?
2. Where do different historical interpretations disagree?
3. What sources and value premises does each rely on?
4. Why do modern people judge past figures by today's standards?

---

## Conclusion: Prompting as Self-Discipline

Learning to use language models is not merely learning a new tool.

It is relearning:
- How to ask questions clearly
- How to distinguish facts from judgment
- How to reduce ambiguity in language
- How to avoid disguising bias as intelligent output

**A language model is not an oracle, judge, or shortcut that replaces thinking.**

Think of it as a mirror made of language and probability. The way you ask shapes how it reflects. The way you structure language shapes how it responds. The way you handle fact, bias, and reasoning amplifies these in the output.

**True prompt engineering is not making machines more human. It is making humans more precise, more honest, and more disciplined.**
