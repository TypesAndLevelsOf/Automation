# Proxy Brier Scoring Protocol — Replicable Method

**Research:** TypesAndLevelsOf/Automation  
**Version:** 1.0  
**Status:** Active (Sprint 4 formalization)  
**Purpose:** Enable Model Fidelity (MF) scoring when formal calibration studies are unavailable

---

## Background

The Model Fidelity (MF) dimension of the LOA Framework requires measuring how accurately a
user's mental model of an agent matches actual agent capabilities. The canonical measurement
is a forward-looking Brier score: ask users to predict task success probabilities, observe
outcomes, compute calibration.

**Problem:** For most deployed agentic AI systems, no formal calibration study exists. Collecting
one requires experimental infrastructure, participant recruitment, and months of work.

**Proxy Brier scoring** approximates the measurement using two existing public data sources:
1. **Actual outcomes** — from published benchmarks, community incident databases, or developer surveys
2. **Predicted probabilities** — from marketing materials, demos, documentation framing, and naive user forum posts

The approach produces an MF estimate comparable to formal measurement for systems with
sufficient public data, enabling cross-system comparison without new experiments.

---

## Protocol

### Step 1: Select a Task Set

Choose N task types representative of the system's deployment domain.

**Requirements:**
- N ≥ 10 for a single estimate (N < 10 = insufficient precision; report as "proxy MF (low-N)")
- Tasks must span the system's claimed capability range (not just easy or hard tasks)
- Tasks should represent what naive first-time users actually attempt (not expert use cases)
- Record the source for each task type (forum analysis, support tickets, product documentation)

**Template for each task:**
```
Task ID: T{n}
Task description: [one sentence]
Domain: [subdomain of the system's task space]
Source: [where this task type was observed]
Predicted probability: [0.0–1.0]
Actual outcome: [0/1]
Evidence for actual outcome: [source/quote]
```

### Step 2: Establish Predicted Probabilities

Predicted probabilities represent the **naive first-time user mental model** — not expert users.

**Sources (in priority order):**
1. **Marketing framing** — capability claims from product pages, GitHub READMEs, demo videos
2. **First-impression forum posts** — "I tried this and it..." posts from developers new to the system
3. **Anchored reasoning** — given the system's known Task LOA score, what would a reasonable user predict?

**Common pitfall:** Using expert user predictions inflates MF scores artificially. The framework
measures whether the *typical first adopter* has a calibrated model, not whether power users do.

**When to flag as "Expert-inflated":** If available data primarily reflects experienced users,
note this and apply a correction discount of 0.05–0.10 to the final MF score.

### Step 3: Establish Actual Outcomes

Actual outcomes are binary (1 = success, 0 = failure) per task type.

**Sources (in priority order):**
1. **Published benchmarks** — SWE-bench, HumanEval-like evals, provider-published success rates
2. **Community consensus** — GitHub issues, forum threads with multiple "same here" replies
3. **Reviewer findings** — documented in technical articles, blog posts with reproduction steps
4. **Absence of evidence** — if no failures reported for a task type with high adoption, score as 1

**When to score as PI (Pending Investigation):** If the actual outcome is genuinely contested
(e.g., conflicting reports with no resolution), score as PI and exclude from Brier computation.

### Step 4: Compute Brier Score and MF

```
BS = (1/N) × Σᵢ (predicted_pᵢ - actual_outcomeᵢ)²
MF = 1 - BS
```

**Interpretation:**
| MF | Calibration Quality |
|----|---------------------|
| 0.0–0.2 | Severe miscalibration |
| 0.3–0.5 | Moderate miscalibration |
| 0.6–0.8 | Good calibration |
| 0.9–1.0 | Near-perfect calibration |

**Always report:**
- N (number of task types)
- Data vintage (when the sources were collected)
- Primary source for predicted probabilities
- Primary source for actual outcomes

### Step 5: Analyze Calibration Structure

A scalar MF score masks heterogeneous calibration. Always:

1. **Compute per-complexity-tier MF** — split N tasks into simple/medium/complex thirds, report MF for each
2. **Identify systematic biases** — are predictions systematically over- or under-confident in a domain?
3. **Report the max Brier term** — the task type with the worst calibration, and why

**Why this matters:** Sprint 3 finding (Copilot agent mode) showed MF = 0.73 aggregate, but
the variance revealed that users severely over-estimate complex multi-step tasks (+70–85pp) while
being well-calibrated on simple tasks. The aggregate mean obscures the safety-relevant finding.

---

## Validity Criteria

**When proxy MF is valid:**
- Sufficient public data exists for both predicted probabilities and actual outcomes
- Task set is representative (not cherry-picked for high or low success)
- Predicted probabilities are from naive users, not experts (or correction applied)
- N ≥ 10

**When proxy MF is NOT valid (use MF = NA):**
- System is too niche for community incident data to exist
- Marketing materials are too vague to extract task-level predictions
- All actual outcome data comes from a single source with potential bias

**Confidence levels:**
| Tier | Criteria | Report as |
|------|----------|-----------|
| High | Published benchmark + multi-source community data + N ≥ 20 | MF = X.XX |
| Medium | Community data only + N ≥ 10 | MF = X.XX (proxy, medium confidence) |
| Low | N < 10 or single-source | MF = X.XX (proxy, low confidence) |

---

## Relationship to Formal MF Measurement

Proxy MF is a **retrospective, observational** estimate. Formal MF is **prospective, experimental**.

| Dimension | Proxy | Formal |
|-----------|-------|--------|
| Cost | Near-zero | High (experiment design, participants) |
| Time to result | Hours | Months |
| Bias risk | Selection bias in sources | Demand characteristics |
| Precision | ± 0.05–0.10 typically | ± 0.02–0.05 with N=50 |
| Availability | Most deployed systems | Rare |

**Recommendation:** Use proxy MF for cross-system comparison when formal data unavailable.
Run formal studies on systems where MF is a key policy variable (e.g., safety-critical deployment decisions).

---

## Example Computation (Sprint 3: Copilot Agent Mode)

N=10 tasks, proxy MF = 0.73. See `sprint_03_copilot_agent_scoring.md` for full data.

Key finding: aggregate MF = 0.73 masks bimodal structure:
- Simple tasks (T1, T2, T4, T9, T10): MF = 0.98 (well-calibrated)
- Complex tasks (T3, T5, T6, T7, T8): MF = 0.50 (severe over-estimation)

This is the first published example of **task-stratified proxy MF reporting**.

---

*Research contact: TypesAndLevelsOf/Automation issue #1*  
*Protocol version: 1.0 — Sprint 4*
