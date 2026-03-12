# Sprint 10: §3 Reviewer Packet
# LOA Framework — §3 Empirical Findings Review Request

**Sprint date:** 2026-03-12
**Status:** Review request — @VictorBargains and Charlotte
**Branch:** Research Phase 1
**Objective:** Compile all four §3 empirical findings + full correlation matrix into a
standalone review packet. §3 is data-complete at N=7. This packet requests:

1. @VictorBargains — authorship review of findings, correlation matrix, and two-pathway model
2. Charlotte — specific review of EC×LOA framing (§3 Finding 1) and the two-pathway oversight
   model architecture (she contributed the TAWS/CFIT analogy and epistemic coupling framing,
   2026-02-23)

---

## N=7 Complete Dataset

All seven systems scored on all six dimensions + derived metrics.

| System | LOA | VL | SR | AC_exp | EC | MF | EVO | Inflation | MF_adj |
|--------|-----|----|----|--------|-----|-----|------|-----------|--------|
| ChatGPT (GPT-4o) | 5 | 5 | 1 | 2 | 0.85 | 0.85 | 4.545 | 0.050 | 0.810 |
| GitHub Copilot Agent | 6 | 4 | 2 | 2 | 0.80 | 0.73 | 3.333 | 0.315 | 0.555 |
| Claude Code | 7 | 5 | 3 | 2 | 0.70 | 0.77 | 3.846 | 0.170 | 0.658 |
| Cursor Agent | 7 | 6 | 2 | 2 | 0.72 | 0.81 | 5.000 | 0.105 | 0.733 |
| GPT-4o Operator | 8 | 5 | 4 | 6 | 0.65 | 0.75 | 3.571 | 0.195 | 0.628 |
| Microsoft AutoGen | 8 | 4 | 4 | 0 | 0.55 | 0.71 | 2.857 | 0.265 | 0.561 |
| Devin AI | 9 | 8 | 5 | 6 | 0.40 | 0.63 | 5.333 | 0.415 | 0.445 |

**Dimension key:** LOA=Task LOA (Sheridan-Verplank 1–10), VL=Veto Latency, SR=Supervisory Residue,
AC_exp=Accountability Clarity (expanded), EC=Epistemic Coupling (0–1), MF=Model Fidelity (raw Brier),
EVO=Effective Veto Opportunity (composite), Inflation=proxy Brier inflation, MF_adj=inflation-adjusted MF.

---

## Finding 1: EC × LOA Anti-Correlation

**Pearson r(LOA, EC) = −0.945** (N=7, R² = 0.893)

Epistemic Coupling (Dimension 4) is strongly inversely correlated with Task LOA.

| System | LOA | EC |
|--------|-----|----|
| ChatGPT (GPT-4o) | 5 | 0.85 |
| GitHub Copilot Agent | 6 | 0.80 |
| Claude Code | 7 | 0.70 |
| Cursor Agent | 7 | 0.72 |
| GPT-4o Operator | 8 | 0.65 |
| Microsoft AutoGen | 8 | 0.55 |
| Devin AI | 9 | 0.40 |

The relationship is structural: higher-LOA systems expose a smaller fraction of their decision
tree through observable artifacts because (a) there are more inter-step decision points, (b)
reasoning between steps is rarely surfaced in deployed interfaces, and (c) commercial pressure
against frequent uncertainty hedging increases with the system's autonomy marketing pitch.

**Oversight implication:** Higher-LOA systems simultaneously degrade the human's ability to
evaluate agent decisions while expanding the action scope the human would need to evaluate.
A single "automation safety" scalar cannot capture this—EC must be measured independently.

*Review focus for Charlotte:* Does this framing of the EC mechanism (especially point c —
commercial pressure on EC) align with your epistemic coupling analysis from the 2026-02-23
conversation? The structural vs. commercial-pressure decomposition of the EC degradation
pathway is new since that conversation.

---

## Finding 2: LOA × SR — Bainbridge Irony Confirmed

**Pearson r(LOA, SR) = +0.964** (N=7, R² = 0.929)

Supervisory Residue (Dimension 6, SR) is strongly positively correlated with Task LOA.

| System | LOA | SR |
|--------|-----|----|
| ChatGPT (GPT-4o) | 5 | 1 |
| GitHub Copilot Agent | 6 | 2 |
| Claude Code | 7 | 3 |
| Cursor Agent | 7 | 2 |
| GPT-4o Operator | 8 | 4 |
| Microsoft AutoGen | 8 | 4 |
| Devin AI | 9 | 5 |

The Bainbridge Irony is confirmed at the agentic AI layer (N=7, R²=0.929): the systems that most
require human error-correction are the same systems that most disengage human oversight.

Mechanism: (1) higher-LOA systems perform longer task sequences, multiplying opportunities for
the human to stop monitoring; (2) marketing-inflated trust sets expectations the system will
handle its own errors; (3) cognitive offloading of sub-task planning is self-reinforcing — once
disengaged, re-engagement is slow.

Combined with Finding 1, both oversight pathways deteriorate together as LOA increases:
- Veto opportunity becomes less effectively used (SR rises, suppressing EVO)
- Veto decision quality falls (EC falls, reducing the human's ability to evaluate agent reasoning)

These are independent mechanisms, not two measurements of the same failure.

---

## Finding 3: Marketing Inflation → MF_adj

**Pearson r(inflation, MF_adj) = −0.990** (N=7, R² = 0.980)

Raw model fidelity (MF) is confounded by marketing baseline. We define:

$$\text{MF\_adj} = \frac{\text{MF}}{1 + \text{inflation}}$$

where inflation = (mean predicted probability on proxy Brier task set) − (actual task success rate).

| System | LOA | MF (raw) | Inflation | MF_adj |
|--------|-----|----------|-----------|--------|
| ChatGPT (GPT-4o) | 5 | 0.85 | 0.050 | 0.810 |
| GitHub Copilot Agent | 6 | 0.73 | 0.315 | 0.555 |
| Claude Code | 7 | 0.77 | 0.170 | 0.658 |
| Cursor Agent | 7 | 0.81 | 0.105 | 0.733 |
| GPT-4o Operator | 8 | 0.75 | 0.195 | 0.628 |
| Microsoft AutoGen | 8 | 0.71 | 0.265 | 0.561 |
| Devin AI | 9 | 0.63 | 0.415 | 0.445 |

Marketing overclaiming is a near-perfect inverse predictor of adjusted user calibration (R²=0.980).
Capability level (LOA) alone: R²=0.540. This is the design-actionable finding: a system at any LOA
can achieve good calibration through honest marketing. The challenge is measuring inflation for
informal or internal deployments where marketing framing is not codified—a direction for future work.

---

## Finding 4: LOA × VL — The Design Lever

**Pearson r(LOA, VL) = +0.513** (N=7, R² = 0.263)

Unlike Findings 1-3, Veto Latency does not tightly correlate with LOA. VL is a design choice.

| System | LOA | VL | Note |
|--------|-----|----|------|
| ChatGPT | 5 | 5 | Consumer product — minimal formal gating |
| Copilot Agent | 6 | 4 | Diff view present; no formal plan approval |
| Claude Code | 7 | 5 | File edit diffs; no step approval |
| Cursor Agent | 7 | 6 | Richer diff UI than competitors |
| GPT-4o Operator | 8 | 5 | API-mediated; operator can add gates |
| AutoGen | 8 | 4 | Fully autonomous orchestration by default |
| Devin AI | 9 | 8 | Explicit plan approval step ← high VL at high LOA |

The critical data point: **AutoGen (LOA=8, VL=4) vs. Devin (LOA=9, VL=8).** The highest-LOA
system in the dataset achieves high VL through commercial investment in a plan approval gate.
AutoGen at similar LOA made the opposite choice. Both are valid product decisions — one is
objectively safer.

SR (r=+0.964) and EC (r=−0.945) with LOA are structural — you cannot design away supervisory
disengagement or epistemic opacity at LOA=8. VL (r=0.513) is the design lever: EVO can be
partially recovered at high LOA by investing in veto architecture.

---

## Full 9-Variable Correlation Matrix (N=7)

| | LOA | VL | SR | AC_exp | EC | MF | EVO | Inflation | MF_adj |
|---|---|---|---|---|---|---|---|---|---|
| **LOA** | +1.000 | +0.513 | +0.964 | +0.500 | -0.946 | -0.801 | +0.082 | +0.651 | -0.738 |
| **VL** | +0.513 | +1.000 | +0.427 | +0.654 | -0.603 | -0.407 | **+0.882** | +0.325 | -0.301 |
| **SR** | +0.964 | +0.427 | +1.000 | +0.520 | -0.940 | -0.861 | -0.042 | +0.724 | -0.805 |
| **AC_exp** | +0.500 | +0.654 | +0.520 | +1.000 | -0.434 | -0.413 | +0.442 | +0.359 | -0.359 |
| **EC** | -0.946 | -0.603 | -0.940 | -0.434 | +1.000 | +0.860 | -0.186 | -0.723 | +0.780 |
| **MF** | -0.801 | -0.407 | -0.861 | -0.413 | +0.860 | +1.000 | +0.027 | **-0.972** | **+0.988** |
| **EVO** | +0.082 | **+0.882** | -0.042 | +0.442 | -0.186 | +0.027 | +1.000 | -0.062 | +0.122 |
| **Inflation** | +0.651 | +0.325 | +0.724 | +0.359 | -0.723 | **-0.972** | -0.062 | +1.000 | **-0.988** |
| **MF_adj** | -0.738 | -0.301 | -0.805 | -0.359 | +0.780 | **+0.988** | +0.122 | **-0.988** | +1.000 |

**Near-deterministic (|r| > 0.9) in bold:**

| Pair | r | R² | Mechanism |
|------|---|-----|-----------|
| Inflation × MF_adj | −0.988 | 0.977 | By construction (MF_adj = MF/(1+inflation)) |
| LOA × SR | +0.964 | 0.929 | Bainbridge Irony: autonomy disengages oversight |
| LOA × EC | −0.946 | 0.894 | Opacity scales with step-count and commercial pressure |
| SR × EC | −0.940 | 0.884 | Both degrade with LOA — correlated, not redundant |
| MF × MF_adj | +0.988 | 0.976 | MF_adj = MF corrected for inflation |

**Strong design-space (0.7 < |r| < 0.9):**

| Pair | r | R² | Mechanism |
|------|---|-----|-----------|
| VL × EVO | +0.882 | 0.779 | EVO is VL-weighted; VL is the dominant term |
| EC × MF | +0.860 | 0.740 | Transparent systems calibrate users better |
| SR × MF | −0.861 | 0.741 | High residue systems tend toward marketing overclaim |
| LOA × MF | −0.801 | 0.641 | Partially mediated by inflation |

**EVO × MF_adj independence notice:** r(EVO, MF_adj) = **+0.122**. The two composite oversight
metrics are structurally independent. A system can have high EVO with low MF_adj (Devin:
EVO=5.33, MF_adj=0.445 — lots of veto opportunity, users still miscalibrated by marketing).
This is the empirical basis for the two-pathway model.

---

## The Two-Pathway Model

The correlation matrix reveals two structurally independent oversight deterioration pathways.
These are not two measures of the same phenomenon — they must both be measured and optimized
independently.

```
LOA increases
  │
  ├── Pathway 1: VETO-RESIDUE
  │     SR rises (r=+0.964) ──→ EVO falls
  │     VL is the design lever: investment in approval gates raises VL, partially restores EVO
  │     Design action: BUILD VETO GATES
  │
  └── Pathway 2: CALIBRATION
        Inflation rises (r(LOA,Inflation)=+0.651) ──→ MF_adj falls (r=-0.988)
        EC mediates: high-EC systems calibrate users better regardless of LOA (r(EC,MF)=+0.860)
        Design action: HONEST MARKETING + EC INVESTMENT
```

**Key structural claim:** r(EVO, MF_adj) = +0.122. Near-zero correlation. The two pathways are
not redundant. Investments in veto gate architecture do not substitute for marketing honesty,
and vice versa. This is why the six-dimensional framework cannot be reduced to a single safety
score — it would mask which pathway is failing.

### Aviation Analog (Charlotte's framing, 2026-02-23)

TCAS and TAWS in aviation operate as independent warning channels that preempt cockpit crew
attention rather than routing through it. They are structurally redundant but operationally
distinct.

The two-pathway model maps directly: EVO corresponds to TCAS (active intervention capability,
independent of crew state); MF_adj corresponds to crew situational awareness (calibrated human
judgment, independent of the intervention mechanism). Both must work. TCAS does not substitute
for a calibrated crew; a calibrated crew is not enough if the intervention mechanism is absent.

---

## Review Questions

### For @VictorBargains

1. **Finding 3 (MF_adj):** Is the inflation measurement methodology solid enough for the
   paper? The proxy Brier protocol assumes marketing claim analysis is operationally feasible.
   For informal deployments, this breaks down. Should we add a caveat section, or is the
   limitation note in §3 sufficient?

2. **The two-pathway independence claim:** r(EVO, MF_adj) = 0.122 at N=7. This is the crux
   of the "cannot reduce to single scalar" argument. Is N=7 sufficient to make this claim,
   or does it need an N=8+ replication first?

3. **AC_expanded accountability gap (RP=0 for all 7):** Is this a §3 finding or does it
   belong in §5 (Research Program) as a policy recommendation? Currently in §3 as
   supplementary instrument.

4. **CrewAI (N=8):** Do you want to add CrewAI before the reviewer packet goes out, giving
   us N=8 and a second framework-type system to cross-check against AutoGen?

### For Charlotte

1. **EC framing (Finding 1):** The mechanism decomposition adds "commercial pressure against
   uncertainty hedging" as a third driver of EC degradation. In your view, is this a first-class
   mechanism or is it derivative of the architectural opacity (point a and b)? The distinction
   matters: commercial pressure is a design-addressable cause; step-count opacity is structural.

2. **Two-pathway model (TCAS/TAWS analog):** The framing maps the Veto-Residue pathway to
   TCAS and the Calibration pathway to crew situational awareness. Is this mapping sound? The
   risk is that TCAS and crew SA are not fully independent in practice — are EVO and MF_adj
   similarly entangled in ways the correlation matrix doesn't capture?

3. **Context Collapse as a seventh dimension:** In Sprint 2 we added SCCD (Severe Context
   Collapse Disorder) as a sixth failure mode alongside the five Parasuraman modes. Should
   it be promoted to the formal framework as a seventh dimension, or left as a failure mode
   characterization? This would affect the authorship section (you'd be cited on TAWS/CFIT;
   SCCD has a different provenance).

---

## Provenance

| Finding | First formulated | Sprint evidence |
|---------|-----------------|-----------------|
| EC × LOA anti-correlation | Sprint 3 (Copilot N=1) | Sprints 3–7, N=7 final |
| LOA × SR (Bainbridge confirmed) | Sprint 8 | Sprint 8, N=7 |
| Inflation → MF_adj | Sprint 4 | Sprints 4–7, N=7 |
| LOA × VL design lever | Sprint 9 | Sprint 9, N=7 |
| Two-pathway model | Sprint 9 | Sprint 9 synthesis |
| Full correlation matrix | Sprint 9 | Sprint 9 final |
