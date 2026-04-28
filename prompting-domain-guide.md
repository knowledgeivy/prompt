# Prompting in the Enterprise: A Domain Application Guide

A practical guide for applying prompting principles to corporate domains—where utterances become structured artifacts, domain knowledge becomes routable intent, and answers must withstand operational scrutiny.

> **Companion documents:**
> - [prompting-philosophy.md](prompting-philosophy.md) — the conceptual foundation: why prompting matters, where answers come from, and the three directions of inquiry
> - [prompting-guide.md](prompting-guide.md) — the practical quick-start

This guide bridges the two: it takes the philosophy of inquiry and operationalizes it for domains where utterances must be classified, validated, and routed across real systems.

---

## Contents

1. [Why Domains Need a Different Discipline](#why-domains-need-a-different-discipline)
2. [The Three Directions, Applied](#the-three-directions-applied)
3. [Where Answers Come From in Production Systems](#where-answers-come-from-in-production-systems)
4. [Domain Abstraction: A Lingua Franca](#domain-abstraction-a-lingua-franca)
5. [The Utterance as a Structured Artifact](#the-utterance-as-a-structured-artifact)
6. [Categories of Cognitive Operations](#categories-of-cognitive-operations)
7. [Deterministic vs. Non-Deterministic Utterances](#deterministic-vs-non-deterministic-utterances)
8. [Templates for Domain-Agnostic Reuse](#templates-for-domain-agnostic-reuse)
9. [Building Utterance Sets with SMEs](#building-utterance-sets-with-smes)
10. [Coverage and Completeness](#coverage-and-completeness)
11. [Validation Checklist](#validation-checklist)
12. [Worked Example: From Question to Production](#worked-example-from-question-to-production)
13. [Conclusion: Domain Wisdom as a System Property](#conclusion-domain-wisdom-as-a-system-property)

---

## Why Domains Need a Different Discipline

The philosophy document argues that prompting is a projection of thought—how we ask shapes what we learn. That is true for individuals.

In corporate domains, prompting becomes something more: **a contract between intent and infrastructure.** A maintenance engineer asking "Should I service Chiller-04 this week?" is not just thinking aloud—she is invoking a chain of telemetry retrieval, anomaly inference, work-order lookup, and prescriptive recommendation. Each link must work, route correctly, and return a verifiable answer.

This means enterprise prompting carries additional burdens that personal prompting does not:

- **Reproducibility** — Two engineers asking the same question must get comparable answers
- **Auditability** — When a recommendation drives a $50k repair, the basis must be traceable
- **Routing** — The right question must reach the right system (telemetry DB vs. work-order system vs. forecasting model)
- **Coverage** — A domain has known operational scenarios; the utterance set must address them

The philosophy in [prompting-philosophy.md](prompting-philosophy.md) tells us *how to think well*. This guide tells us *how to encode that thinking* so it survives the journey from a question to a production response.

---

## The Three Directions, Applied

The philosophy document identifies three universal directions of inquiry: **retrospective, predictive, prescriptive**. In a domain context, these are not just modes of thought—they are routing labels and validation criteria.

### Retrospective — Establishing Ground Truth

*What happened? What is currently the case? What does the record show?*

In the enterprise, retrospective queries hit **systems of record**: telemetry stores, transaction logs, asset registries, knowledge bases.

| Domain | Retrospective Examples |
|---|---|
| Manufacturing | "What production lines are operational?" / "List all quality defects from last shift." |
| Energy | "What substations are in the network?" / "Retrieve all outage events from last month." |
| Healthcare | "What is the patient's medical history?" / "List all lab results from the past year." |
| Finance | "What were the transaction volumes last quarter?" / "Retrieve all trades for account XYZ." |

**Validation criterion:** The answer can be checked against the source of record. Right or wrong is decidable.

### Predictive — Informed Inference

*What is likely to happen next, if conditions continue?*

Predictive queries invoke **forecasting models, risk estimators, anomaly detectors**. The output is probabilistic—a distribution, a likelihood, a confidence band.

| Domain | Predictive Examples |
|---|---|
| Manufacturing | "Predict production line downtime risk." / "Forecast product quality metrics for next batch." |
| Energy | "Forecast power demand for next week." / "Predict transformer failure probability." |
| Healthcare | "What is the patient's readmission risk?" / "Predict disease progression over the next 6 months." |
| Finance | "Forecast stock price for next quarter." / "Estimate default probability for this loan." |

**Validation criterion:** Calibration over time. A model that says "20% chance" should be right 20% of the time across many such predictions.

### Prescriptive — Translating Knowledge into Action

*What should be done, given goals and constraints?*

Prescriptive queries are where models meet **values and accountability**. They invoke optimizers, recommenders, decision-support systems—but the value premises must come from the human.

| Domain | Prescriptive Examples |
|---|---|
| Manufacturing | "Recommend corrective actions for quality issue." / "Prioritize production orders for next shift." |
| Energy | "Optimize load balancing strategy." / "Recommend preventive maintenance for grid equipment." |
| Healthcare | "Recommend treatment plan for this diagnosis." / "Prioritize patients for ICU admission." |
| Finance | "Suggest portfolio rebalancing strategy." / "Recommend fraud prevention actions." |

**Validation criterion:** Multi-faceted. Did the recommendation satisfy the constraints? Did following it produce the desired outcome? Is the reasoning auditable?

### Why Separation Matters in the Enterprise

The philosophy doc warns against mixing directions in personal prompting. In the enterprise, mixing directions is worse—it **breaks routing**. A prompt that asks "What's wrong with Chiller-04 and what should I do?" combines a retrospective query, an inference, and a prescription. Each maps to a different agent or service. Untangling them is the work of prompt design, not the runtime.

---

## Where Answers Come From in Production Systems

The philosophy doc enumerates four answer sources—**parametric, contextual, retrieved, computed**—each with different hallucination risk. In a corporate domain, these map onto your system architecture:

| Source | Enterprise Equivalent | Hallucination Risk |
|---|---|---|
| Parametric | LLM's pretraining (no domain grounding) | High |
| Contextual | Document content provided in-prompt | Low |
| Retrieved (RAG) | Internal knowledge base, SOPs, manuals | Low–Medium |
| Retrieved (Web) | Open internet (rarely trusted in enterprise) | Medium |
| Computed (Tool) | Calculators, code execution, simulation | Very Low |
| Computed (System) | Telemetry DBs, ERP, work-order systems, APIs | Very Low |

**Implication for utterance design:** Every utterance should be designed with an expected answer source in mind. If the answer should come from a system call, the utterance must carry enough specificity (entity ID, time range, metric) for the system call to execute. If the answer is from RAG, the utterance must align with terminology used in the indexed corpus.

> **A well-formed enterprise utterance is one where the routing is unambiguous and the answer source is appropriate to the question type.**

This is why the `type` field (which agent, MCP server, or service handles the request) is non-negotiable in enterprise utterance schemas.

---

## Domain Abstraction: A Lingua Franca

Beneath the surface diversity of domains lies a shared conceptual vocabulary. An equipment failure mirrors a disease progression; a financial transaction parallels a supply chain event. The abstraction does not erase domain knowledge—it creates a **lingua franca** through which it can be expressed, exchanged, and reused.

### Core Placeholders

| Placeholder | Description | Cross-Domain Examples |
|---|---|---|
| `{ENTITY}` | Primary object of analysis | Patient, Account, Equipment, Vehicle, Student |
| `{ENTITY_CLASS}` | Category or type | Diagnosis, Portfolio, HVAC, Fleet, Course |
| `{LOCATION}` | Physical or logical location | Hospital, Branch, Site, Route, Campus |
| `{METRIC}` | Measurement or KPI | Heart Rate, Balance, Temperature, Speed, Grade |
| `{EVENT_TYPE}` | Type of occurrence | Admission, Transaction, Alert, Delay, Enrollment |
| `{EVENT_NAME}` | Specific event label | Readmission, Withdrawal, Failure, Accident, Dropout |
| `{TIME_RANGE}` | Temporal scope | "Last month", "2020-06-01 to 2020-06-30" |
| `{WINDOW}` | Time horizon | "Within 14 days", "Next maintenance window" |
| `{ACTION}` | Intervention or decision | Treatment, Investment, Maintenance, Reroute, Tutoring |
| `{CONDITION}` | Trigger or state | "When anomaly detected", "If balance < threshold" |
| `{OUTPUT_FORM}` | Response format | List, Table, Chart, File, Summary |

### Adapting a Generic Template

**Generic:**
> "Retrieve {METRIC} for {ENTITY} at {LOCATION} over {TIME_RANGE}"

**Domain adaptations:**
- Healthcare: "Retrieve blood pressure for Patient-123 at Boston General over last month"
- Finance: "Retrieve transaction volume for Account-456 at NYC Branch over Q1 2024"
- Manufacturing: "Retrieve cycle time for Robot-5 at Assembly Line A over last week"
- Education: "Retrieve attendance for Student-789 at Main Campus over last semester"

The template is reusable; the domain knowledge fills in the slots.

---

## The Utterance as a Structured Artifact

In personal prompting, an utterance is a string. In an enterprise system, it is a **structured object** with metadata that enables routing, validation, and lifecycle management.

### Required Schema

| Field | Type | Purpose |
|---|---|---|
| `id` | integer | Unique identifier |
| `text` | string | The natural-language utterance |
| `type` | string | The agent / MCP server / service that handles the request |
| `category` | string | Cognitive operation type (see next section) |
| `deterministic` | boolean | Whether there is a single correct answer |
| `characteristic_form` | string | Expected response shape and validation criteria |
| `group` | string or array | Direction: retrospective / predictive / prescriptive |
| `entity` | string or array | The physical subject(s) of the query |
| `note` | string | Source, owner, design rationale, dependencies |

### Example: Retrospective

```json
{
  "id": 1,
  "text": "What IoT sites are available?",
  "type": "IoT",
  "category": "Knowledge Query",
  "deterministic": true,
  "characteristic_form": "Returns the list of all sites, as text or as a file reference",
  "group": "retrospective",
  "entity": "Site",
  "note": "Source: Initial domain analysis; Owner: Domain SME team"
}
```

### Example: Prescriptive

```json
{
  "id": 416,
  "text": "When an anomaly happens for equipment CWC04009, can you recommend top three work orders?",
  "type": "Workorder",
  "category": "Decision Support",
  "deterministic": false,
  "characteristic_form": "Returns a list of work orders, each with a primary failure code",
  "group": "prescriptive",
  "entity": "Chiller",
  "note": "Source: Operations team request; supports proactive maintenance workflow"
}
```

### Example: Multi-Group

```json
{
  "id": 420,
  "text": "Review the performance of chiller 9 for June 2020 and track any anomalies or operation violations as alerts.",
  "type": "Workorder",
  "category": "Decision Support",
  "deterministic": false,
  "characteristic_form": "Reviews operational performance and detects anomalies for Chiller 9 during June 2020",
  "group": ["retrospective", "predictive"],
  "entity": "Chiller",
  "note": "Multi-category query combining historical review with anomaly detection"
}
```

The `note` field is intentionally flexible: it captures provenance, ownership, design intent, and any context that helps a future maintainer understand why this utterance exists.

---

## Categories of Cognitive Operations

The three directions (retrospective, predictive, prescriptive) describe the *purpose* of an inquiry. Categories describe the *cognitive operation* it performs. A single direction can be served by several categories.

| Category | Pattern | Typical Direction |
|---|---|---|
| **Information Retrieval** | "What is...", "List all...", "Show me..." | Retrospective |
| **Data Extraction** | "Download...", "Export...", "Get data for..." | Retrospective |
| **Analysis & Inference** | "Analyze...", "Identify patterns in..." | Retrospective / Predictive |
| **Model Customization** | "Fine-tune...", "Train model on..." | (cross-cutting) |
| **Anomaly & Exception Detection** | "Detect anomalies in...", "Find outliers..." | Predictive |
| **Future State Prediction** | "Forecast...", "Predict probability of..." | Predictive |
| **Recommendation & Optimization** | "Recommend...", "What should I do when..." | Prescriptive |
| **Multi-Step Orchestration** | "Retrieve X, analyze Y, recommend Z" | Cross-direction |

Categories are not mutually exclusive. When an utterance spans several, choose the **primary** category that captures the dominant intent—and capture the rest in `note` or the multi-group field.

---

## Deterministic vs. Non-Deterministic Utterances

This distinction reflects a foundational divide in how we validate answers.

### Deterministic (`deterministic: true`)
- Single, verifiable correct answer
- Response is checkable against ground truth
- Typical for retrieval and well-defined computation
- Validation: exact match or constrained match against source of record

### Non-Deterministic (`deterministic: false`)
- Multiple valid answers possible
- Response quality depends on reasoning, judgment, or domain heuristics
- Typical for recommendations, predictions, complex analysis
- Validation: rubric-based, expert review, outcome tracking

A retrieval question ("What was the temperature reading on 2024-06-12 at 14:00?") is deterministic. A prescriptive question ("What should we do about declining efficiency?") is not. Mark the field correctly—it determines how the response will be evaluated downstream.

---

## Templates for Domain-Agnostic Reuse

These templates are domain-neutral and can be adapted by filling in placeholders.

### Template 1: Historical Data Retrieval (Retrospective)
```
Retrieve {METRIC} for {ENTITY} at {LOCATION} from {TIME_RANGE}
```

### Template 2: Knowledge Lookup (Retrospective)
```
List all {ATTRIBUTE} of {ENTITY_CLASS}
```

### Template 3: Predictive Analysis (Predictive)
```
Forecast {METRIC} for {ENTITY} for {FUTURE_RANGE} based on data from {PAST_RANGE}
```

### Template 4: Anomaly Detection (Predictive)
```
Is there any anomaly detected in {ENTITY}'s {METRIC} in {TIME_RANGE} at {LOCATION}?
```

### Template 5: Recommendation (Prescriptive)
```
When {CONDITION} happens for {ENTITY}, recommend {ACTION_TYPE}
```

### Template 6: Multi-Factor Decision Support (Cross-direction)
```
Review {DATA_SOURCES} for {ENTITY} during {PERIOD} and {recommend/analyze/prioritize} {ACTION}
```

---

## Building Utterance Sets with SMEs

Domain expertise is distributed across people, not concentrated in any one expert. Utterance set construction should reflect this reality.

### Suggested Workflow

1. **Divide by expertise** — Different SMEs contribute utterances for their areas of depth (equipment types, operational functions, problem categories)
2. **Start small** — 5–10 critical, high-frequency queries per SME initially
3. **Iterate** — Add edge cases, test with users, refine based on feedback
4. **Vary specificity** — Mix generic templates (reusable), domain-specific utterances (tailored), and scenario-specific queries (deep expertise)

### Prioritization Heuristic

Focus first on intersections of:
- **High frequency** — common operational queries
- **High impact** — queries that drive significant decisions or spend
- **Available data** — scenarios where the underlying data exists today

Avoid the temptation to enumerate exhaustively up front. Coverage emerges through use.

---

## Coverage and Completeness

Completeness is not a count; it is **sufficient coverage of fundamental patterns** in the domain. Track it across several dimensions.

### Operational Lifecycle Coverage
- Normal operations monitoring
- Degradation detection and tracking
- Event prediction and diagnosis
- Action planning and execution
- Performance optimization
- Compliance and reporting

### Direction Balance
- Retrospective queries
- Predictive queries
- Prescriptive queries

A heavily retrospective set (typical for early-stage deployments) is fine—but recognize the imbalance and plan to extend.

### Entity / System Coverage
- All critical entity types in the domain
- Key subsystems and components
- Cross-entity dependencies and interactions

### Stakeholder Perspective Coverage
- Operations team needs
- Management team needs
- Engineering / analysis team needs
- End-user needs

A coverage matrix mapping these dimensions against your existing utterance set will surface gaps quickly.

---

## Validation Checklist

Before promoting an utterance to a production set, verify:

- [ ] **Clarity** — Is the request unambiguous?
- [ ] **Completeness** — Are all necessary parameters specified?
- [ ] **Direction** — Is the `group` field (retrospective / predictive / prescriptive) correct?
- [ ] **Category** — Is the cognitive operation type correctly assigned?
- [ ] **Deterministic Flag** — Set accurately based on answer uniqueness?
- [ ] **Characteristic Form** — Clearly describes expected output and validation criteria?
- [ ] **Routing** — Is the `type` field aligned with the agent / system that should handle it?
- [ ] **Domain Relevance** — Does it map to a real operational need?
- [ ] **Testability** — Can the response be validated or evaluated?
- [ ] **Consistency** — Follows established patterns and terminology?
- [ ] **Domain-Neutrality** — Can it be adapted via placeholders to other domains?
- [ ] **SME Validation** — Has a domain expert reviewed and approved it?
- [ ] **Coverage Contribution** — Does it fill a gap in the coverage matrix?

---

## Worked Example: From Question to Production

**Initial vague question (from a maintenance manager):**
> "Is Chiller-04 going to fail soon, and what should I do about it?"

**Problems:**
- Mixes predictive ("going to fail") and prescriptive ("what should I do") directions
- "Soon" is undefined
- "Fail" is undefined (catastrophic? performance degradation? compliance violation?)
- Routing is ambiguous (telemetry? forecasting model? work-order system?)

**Refined into a structured set:**

```json
[
  {
    "id": 501,
    "text": "Retrieve operational telemetry for Chiller-04 over the past 30 days.",
    "type": "IoT",
    "category": "Data Extraction",
    "deterministic": true,
    "characteristic_form": "Time-series data for power, flow, and temperature metrics",
    "group": "retrospective",
    "entity": "Chiller",
    "note": "First step in failure-risk workflow; grounds the prediction in recent data"
  },
  {
    "id": 502,
    "text": "Predict the probability of Chiller-04 failure within the next 14 days.",
    "type": "TSFM",
    "category": "Future State Prediction",
    "deterministic": false,
    "characteristic_form": "Probability with confidence interval; cite contributing indicators",
    "group": "predictive",
    "entity": "Chiller",
    "note": "Defines 'soon' as 14-day window; defines 'fail' as model-defined failure event"
  },
  {
    "id": 503,
    "text": "If failure probability for Chiller-04 exceeds threshold, recommend top three preventive work orders.",
    "type": "Workorder",
    "category": "Recommendation & Optimization",
    "deterministic": false,
    "characteristic_form": "Ranked list of work orders with primary failure code and estimated cost",
    "group": "prescriptive",
    "entity": "Chiller",
    "note": "Threshold defined by ops policy; translates prediction into action"
  }
]
```

The original ambiguous question becomes three structured utterances—each with a clear direction, a routable system, and a verifiable response shape. This is what enterprise prompt engineering looks like in practice.

---

## Conclusion: Domain Wisdom as a System Property

The philosophy doc argues that prompting is self-discipline—learning to ask well sharpens how you think. In the enterprise, that discipline scales: **a well-designed utterance set encodes the collective intelligence of an organization** about how its domain should be queried, reasoned about, and acted upon.

The three directions (retrospective, predictive, prescriptive) tell you what kind of question you are asking. The placeholders tell you how to express it. The schema tells you how to make it operational. The categories tell you what cognitive work is being done. The validation checklist tells you when it is ready to ship.

None of this replaces the philosophy. It encodes it.

When done well, an enterprise utterance set becomes a living artifact—a shared vocabulary that lets domain experts, engineers, and AI systems communicate without losing precision. It is the corporate analogue of clear individual prompting: not a way to make the machine smarter, but a way to make the organization more **precise, more disciplined, and more honest about what it knows and what it does not.**

---

## See Also

- [prompting-philosophy.md](prompting-philosophy.md) — the conceptual foundation
- [prompting-guide.md](prompting-guide.md) — the practical quick-start
- [temp/utterance_design_guideline.md](temp/utterance_design_guideline.md) — the source guideline this document builds on
- [temp/case_study_industrial_asset_management.md](temp/case_study_industrial_asset_management.md) — extended case study (152 utterances, real-world growth)
- [temp/case_study_wind_turbine.md](temp/case_study_wind_turbine.md) — extended case study (30 utterances, balanced design)
