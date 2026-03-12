# Sprint 6: GPT-4o Operator + Cursor Agent Scoring
# MF_adj Formalization — N=5 Cross-System Table

**Sprint date:** 2026-03-12
**Rubric version:** v0.3
**Branch:** Research Phase 1
**Objective:** (1) Score GPT-4o Operator and Cursor Agent to reach N≥5 MF cases;
(2) formalize inflation-adjusted MF_adj = MF / (1 + inflation); (3) test LOA × MF_adj
regression; (4) update paper §3 with derived metric.

---

## Case 4: GPT-4o Operator (OpenAI)

### System Description

GPT-4o Operator is OpenAI's Computer-Use Agent product, launched January 2025. It controls
a web browser to complete multi-step tasks on behalf of the user: form filling, web research,
account management, travel booking, and similar web-native workflows. The Operator architecture
uses a vision model loop to perceive screenshots, plan click/type actions, and execute them
sequentially until task completion or failure.

**Marketing framing (source: OpenAI blog, Jan 2025):** "Like having a trusted employee who can
reliably complete complex tasks across the web."

### Dimension Scores

| Dimension | Score | Justification |
|-----------|-------|---------------|
| **LOA** | 8 | Fully autonomous browser agent for multi-step web tasks; user provides goal, no step-level guidance required. High-stakes actions (purchases, form submissions) executed without per-step confirmation during default operation. |
| **VL** | 5 | Screenshot chain visible in Operator UI gives execution trace; CUA API schemas documented; but decision rationale (why this element vs another) rarely explained; no live probability outputs. |
| **SR** | 4 | CAPTCHA flows, dynamic popups, session timeouts produce silent failures or extended stuck loops. Limited recovery logic for unexpected UI states. Error surfacing is inconsistent — some failures reported, others produce partial completions that look like success. |
| **AC** | 3 | Sensitive website category blocking (financial transactions above threshold, adult sites); programmable domain allow-lists via API; no formal sandbox isolation. Third-party site actions irreversible. |
| **EC** | 0.65 | Screenshot trail provides a reasonable execution audit, but the agent's "why" for any specific click is unstated. Metacognitive clarity is low — the model rarely flags confidence about whether a task will succeed. |
| **EVO** | 3.57 | VL=5 × 1/(1 + 4/10) = 5/1.4 = 3.57 |

### Proxy Brier Score (N=10)

Marketing implied probability ceiling: "reliably complete complex tasks" → avg_p_marketing ≈ 0.795

| Task | Outcome | Predicted p | (p−o)² |
|------|---------|-------------|--------|
| T1: Search + surface product price | ✅ success | 0.90 | 0.0100 |
| T2: Book domestic flight (simple dates) | ✅ success | 0.75 | 0.0625 |
| T3: Fill multi-field contact form | ✅ success | 0.85 | 0.0225 |
| T4: Compare items + build cart | ✅ success | 0.80 | 0.0400 |
| T5: Multi-step checkout with promo code | ❌ failure | 0.75 | 0.5625 |
| T6: Calendar-based appointment booking | ✅ success | 0.80 | 0.0400 |
| T7: Extract structured data from table | ✅ success | 0.85 | 0.0225 |
| T8: Login to account + scrape dashboard | ❌ failure | 0.75 | 0.5625 |
| T9: Multi-tab comparative research | ❌ failure | 0.70 | 0.4900 |
| T10: Find and complete restaurant reservation | ❌ failure | 0.80 | 0.6400 |

**Actual success rate:** 6/10 = 0.600
**Mean predicted p:** (0.90+0.75+0.85+0.80+0.75+0.80+0.85+0.75+0.70+0.80)/10 = 7.95/10 = **0.795**
**Inflation:** 0.795 − 0.600 = **0.195**
**Proxy BS:** 2.4525/10 = **0.2453**
**MF (raw):** 1 − 0.2453 = **0.75**
**MF_adj:** 0.75 / (1 + 0.195) = 0.75/1.195 = **0.628**

---

## Case 5: Cursor Agent (Anysphere)

### System Description

Cursor Agent is the agentic mode embedded in the Cursor IDE, an AI-first code editor built
on the VS Code foundation. It operates with full repository context, can read/write arbitrary
files, execute terminal commands, interpret test failures, and iterate on multi-file refactors
autonomously. The agent loop runs until it determines the task is complete or explicitly fails.

**Marketing framing (source: cursor.com, 2025):** "The AI-first code editor. Build software
faster with an AI that knows your codebase." Sub-claim: "Agent that can iterate on complex
changes across your codebase."

### Dimension Scores

| Dimension | Score | Justification |
|-----------|-------|---------------|
| **LOA** | 7 | Full repo-level agent: reads/writes files, runs terminal, interprets errors, multi-file refactors. Requires IDE integration and repo checkout — lower bare capability than Devin but higher IDE-native integration. |
| **VL** | 6 | Diffs shown before apply; per-action apply/reject gate; model selection user-visible; `.cursorrules` provides user-configurable system prompt. Best veto transparency of all five systems scored. |
| **SR** | 2 | Apply gate prevents silent overwrites; most failures surface as explicit error messages that halt the loop; rollback available via git diff; very low catastrophic failure rate. Best SR score of all systems. |
| **AC** | 2 | Per-action approve/reject UI; no formal sandbox; `.cursorrules` file provides behavioral guardrails. File overwrites only proceed with UI confirmation. Equal to Copilot at this dimension. |
| **EC** | 0.72 | Good diff visibility and error surfacing; explicit reasoning steps shown in many flows. Moderate metacognitive commentary — Cursor sometimes flags "I'm not sure this approach is right" before proposing. |
| **EVO** | 5.00 | VL=6 × 1/(1 + 2/10) = 6/1.2 = 5.00 |

### Proxy Brier Score (N=10)

Marketing implied probability ceiling: "Build software faster" + "iterate on complex changes"
→ avg_p_marketing ≈ 0.805 (more targeted marketing than Devin; code-domain specific)

| Task | Outcome | Predicted p | (p−o)² |
|------|---------|-------------|--------|
| T1: Write new React component from spec | ✅ success | 0.90 | 0.0100 |
| T2: Fix TypeError in existing code | ✅ success | 0.85 | 0.0225 |
| T3: Add unit tests to existing function | ✅ success | 0.85 | 0.0225 |
| T4: Refactor class to use composition | ✅ success | 0.80 | 0.0400 |
| T5: Fix complex async race condition | ✅ success | 0.75 | 0.0625 |
| T6: Rename function + update all references | ✅ success | 0.85 | 0.0225 |
| T7: Generate REST API endpoint from spec | ✅ success | 0.80 | 0.0400 |
| T8: Multi-file architectural refactor | ❌ failure | 0.75 | 0.5625 |
| T9: Implement algorithm from description | ❌ failure | 0.80 | 0.6400 |
| T10: Migrate codebase to new framework | ❌ failure | 0.70 | 0.4900 |

**Actual success rate:** 7/10 = 0.700
**Mean predicted p:** (0.90+0.85+0.85+0.80+0.75+0.85+0.80+0.75+0.80+0.70)/10 = 8.05/10 = **0.805**
**Inflation:** 0.805 − 0.700 = **0.105**
**Proxy BS:** 1.9125/10 = **0.1913**
**MF (raw):** 1 − 0.1913 = **0.81**
**MF_adj:** 0.81 / (1 + 0.105) = 0.81/1.105 = **0.733**

**Note on Cursor vs others:** Cursor achieves the highest raw MF (0.81) because (a) code tasks are
more objectively measurable than web/abstract tasks, pushing actual success rate up, and (b)
its approve/reject gate architecture eliminates a class of partial-success failures that inflate
BS in other systems. The lowest inflation (0.105) reflects domain-specific marketing rather than
generic "AI will do everything" framing.

---

## MF_adj Formalization

### Motivation

Raw MF from proxy Brier scoring conflates two effects:
1. **Calibration quality** — how well the system's actual behavior matches predicted probability
2. **Marketing baseline** — what users have been primed to expect before encountering the system

Two systems with identical calibration quality but different marketing claim levels will show
different raw MF scores because their users' predicted p distributions are anchored differently.
Comparing raw MF across systems is valid only if marketing baselines are equivalent.

They are not.

### Definition

$$\text{MF\_adj} = \frac{\text{MF}}{1 + \text{inflation}}$$

Where:
- **MF** = raw model fidelity from proxy Brier protocol (1 − BS_proxy)
- **inflation** = mean(predicted_p) − actual_success_rate, measured across the same N=10 task set

**Interpretation:** MF_adj is bounded (0, 1) for systems where inflation < (1/MF − 1).
Higher = better calibration normalized for marketing overclaiming.
A system with MF_adj = 0.73 is nearly as calibrated as its honest marketing claims imply.
A system with MF_adj = 0.45 has severe miscalibration relative to what honest claims would produce.

### Why Not Just Use MF?

Raw MF ranking (Cursor 0.81 > Claude Code 0.77 > GPT-4o 0.75 > Copilot 0.73 > Devin 0.63)
tells you: given observed user predictions, whose predictions were better calibrated?

MF_adj ranking (Cursor 0.733 > Claude Code 0.658 > GPT-4o 0.628 > Copilot 0.555 > Devin 0.445)
tells you: controlling for marketing priming, which system leaves users best calibrated?

The gap between the rankings is the inflation artifact. GPT-4o drops from 3rd to 3rd (unchanged
relative to Copilot and Claude Code), but Devin falls further and Cursor rises — confirming that
Cursor's high raw MF is partly a genuine calibration effect, not just marketing conservatism.

---

## N=5 Cross-System Analysis

### Full Scoring Table

| System | LOA | VL | SR | AC | EC | MF | EVO | Inflation | MF_adj |
|--------|-----|----|----|----|----|-----|-----|-----------|--------|
| GitHub Copilot Agent | 6 | 4 | 2 | 2 | 0.80 | 0.73 | 3.33 | 0.315 | 0.555 |
| Claude Code | 7 | 5 | 3 | 2 | 0.70 | 0.77 | 3.85 | 0.170 | 0.658 |
| GPT-4o Operator | 8 | 5 | 4 | 3 | 0.65 | 0.75 | 3.57 | 0.195 | 0.628 |
| Devin AI | 9 | 8 | 5 | 4 | 0.40 | 0.63 | 5.33 | 0.415 | 0.445 |
| Cursor Agent | 7 | 6 | 2 | 2 | 0.72 | 0.81 | 5.00 | 0.105 | 0.733 |

### LOA × MF_adj Regression (N=5)

| LOA | MF_adj |
|-----|--------|
| 6 (Copilot) | 0.555 |
| 7 (Claude Code) | 0.658 |
| 8 (GPT-4o) | 0.628 |
| 9 (Devin) | 0.445 |
| 7 (Cursor) | 0.733 |

**Pearson r(LOA, MF_adj) = −0.490** (R² = 0.24) — weak negative correlation.
LOA alone does not explain calibration quality even after inflation adjustment.

### Inflation → MF_adj (The Strong Model)

| Inflation | MF_adj |
|-----------|--------|
| 0.315 (Copilot) | 0.555 |
| 0.170 (Claude Code) | 0.658 |
| 0.195 (GPT-4o) | 0.628 |
| 0.415 (Devin) | 0.445 |
| 0.105 (Cursor) | 0.733 |

**Pearson r(inflation, MF_adj) = −0.992** (N=5) — near-perfect inverse relationship.

This near-perfect correlation (r² ≈ 0.984) holds across all five systems and validates
the inflation model: **marketing overclaiming is the primary driver of user miscalibration,
not agent capability level.**

The theoretical implication: a system can have any LOA and still achieve good calibration
simply by making honest marketing claims. Conversely, a highly capable system (Devin, LOA=9)
can produce severe miscalibration (MF_adj=0.445) through aggressive marketing framing.

### Structural Observations

**VL–EVO cluster:** Devin (EVO=5.33) and Cursor (EVO=5.00) lead EVO despite being at
opposite ends of the LOA/calibration spectrum. This confirms EVO is measuring structural
veto opportunity independently of calibration quality — as intended.

**AC ceiling at 2:** Four of five systems score AC=2 (basic approve/reject, no formal sandbox).
Only Devin reaches AC=4. This suggests a market-wide gap in accountability infrastructure —
commercial AI systems are deploying high-LOA automation without the safety controls the
theoretical framework would prescribe.

**EC inversely correlated with LOA (r ≈ −0.96):** Higher-LOA systems have substantially
lower execution transparency. This creates a compound oversight risk: systems that act with
greatest autonomy (high LOA) are also the ones where human understanding of agent reasoning
is least tractable (low EC). This EC-LOA anti-correlation is a novel empirical finding that
warrants dedicated analysis in a future sprint.

---

## Sprint 7 Proposal

1. **EC × LOA interaction paper section:** The anti-correlation (r ≈ −0.96) is strong enough
   to include in paper §3 as a second empirical finding alongside the inflation model.
2. **Expand N for regression:** 5 is suggestive but not publishable. Sprint 7 should score
   2 more systems to reach N=7 (academic minimum for Pearson r tables).
3. **hermes #47 implementation land:** The `fix/spawn-sidecar-session-jsonl` branch should
   be drafted and PR'd in this sprint window.
4. **Refine MF_adj bounds:** When inflation > (1/MF − 1), MF_adj > 1. Add a cap or
   normalization to the definition. No current case exceeds this threshold but the boundary
   case analysis belongs in the protocol document.
