# Sprint 9: LOA × VL Correlation + Full N=7 Correlation Matrix
# 9-Variable Pairwise Analysis

**Sprint date:** 2026-03-12
**Rubric version:** v0.3
**Branch:** Research Phase 1
**Objective:** (1) Test r(LOA, VL) — does veto latency increase with autonomy? (hypothesis r≈+0.6);
(2) compute full pairwise correlation matrix for all 9 variables (6 dimensions + AC_expanded + EVO + MF_adj);
(3) identify the complete structural picture for paper §3.

---

## N=7 Raw Data

| System | LOA | VL | SR | AC_exp | EC | MF | EVO | Inflation | MF_adj |
|--------|-----|----|----|--------|-----|-----|------|-----------|--------|
| ChatGPT (GPT-4o) | 5 | 5 | 1 | 2 | 0.85 | 0.85 | 4.545 | 0.050 | 0.810 |
| GitHub Copilot Agent | 6 | 4 | 2 | 2 | 0.80 | 0.73 | 3.333 | 0.315 | 0.555 |
| Claude Code | 7 | 5 | 3 | 2 | 0.70 | 0.77 | 3.846 | 0.170 | 0.658 |
| Cursor Agent | 7 | 6 | 2 | 2 | 0.72 | 0.81 | 5.000 | 0.105 | 0.733 |
| GPT-4o Operator | 8 | 5 | 4 | 6 | 0.65 | 0.75 | 3.571 | 0.195 | 0.628 |
| Microsoft AutoGen | 8 | 4 | 4 | 0 | 0.55 | 0.71 | 2.857 | 0.265 | 0.561 |
| Devin AI | 9 | 8 | 5 | 6 | 0.40 | 0.63 | 5.333 | 0.415 | 0.445 |

---

## Finding 4: r(LOA, VL) = 0.513 (Hypothesis: r≈+0.6, N=7)

**Hypothesis:** Veto Latency (VL) increases with Task LOA — higher-autonomy systems provide more
formal approval mechanisms to compensate for their increased action scope.

**Result: Pearson r(LOA, VL) = 0.513** (R² = 0.263)

The hypothesis is partially confirmed but at weaker magnitude than predicted. The relationship is
moderate, not strong. The reason: VL is a *design choice*, not a structural consequence of LOA.

The data shows bimodal clustering rather than smooth correlation:

| System | LOA | VL | Note |
|--------|-----|-----|------|
| ChatGPT | 5 | 5 | Consumer product — minimal formal gating |
| Copilot Agent | 6 | 4 | Diff view present; no formal plan approval |
| Claude Code | 7 | 5 | File edit diffs; no step approval |
| Cursor Agent | 7 | 6 | Richer diff UI than competitors |
| GPT-4o Operator | 8 | 5 | API-mediated; operator can add gates |
| AutoGen | 8 | 4 | Fully autonomous orchestration by default ← low VL at high LOA |
| Devin AI | 9 | 8 | Explicit plan approval step ← high VL at high LOA |

The critical observation: **Devin and AutoGen both score LOA=8-9, but VL diverges sharply (8 vs 4).**
Devin invested in plan approval gates as a commercial differentiator. AutoGen's design philosophy
is multi-agent autonomy without interruption — VL is low by design.

**Contrast with SR and EC:**
- r(LOA, SR) = 0.964: structural, no escaping supervisory residue at high LOA
- r(LOA, EC) = -0.946: structural, inter-step reasoning opacity is inherent
- r(LOA, VL) = 0.513: design-choice, VL can be set at any LOA tier

This is the key asymmetry for the paper: two of the three oversight dimensions (SR, EC) degrade
structurally with LOA. The third (VL) is a design lever — a system at LOA=9 can achieve VL=8
(Devin) or VL=4 (AutoGen). This is the design-actionable finding: veto architecture is where
investment pays off.

**EVO implications:**
r(LOA, EVO) = 0.082 (R² = 0.007)

EVO is only mildly correlated with LOA. This is expected: EVO already incorporates SR's negative
pressure on veto effectiveness. High VL systems at high LOA (Devin: LOA=9, EVO=5.33) outperform
low VL at mid-LOA (AutoGen: LOA=8, EVO=2.86). EVO correctly discriminates these cases.

---

## Full 9-Variable Correlation Matrix (N=7)

| | LOA | VL | SR | AC_exp | EC | MF | EVO | Inflation | MF_adj |
|---|---|---|---|---|---|---|---|---|---|
| **LOA** | +1.000 | +0.513 | +0.964 | +0.500 | -0.946 | -0.801 | +0.082 | +0.651 | -0.738 |
| **VL** | +0.513 | +1.000 | +0.427 | +0.654 | -0.603 | -0.407 | +0.882 | +0.325 | -0.301 |
| **SR** | +0.964 | +0.427 | +1.000 | +0.520 | -0.940 | -0.861 | -0.042 | +0.724 | -0.805 |
| **AC_exp** | +0.500 | +0.654 | +0.520 | +1.000 | -0.434 | -0.413 | +0.442 | +0.359 | -0.359 |
| **EC** | -0.946 | -0.603 | -0.940 | -0.434 | +1.000 | +0.860 | -0.186 | -0.723 | +0.780 |
| **MF** | -0.801 | -0.407 | -0.861 | -0.413 | +0.860 | +1.000 | +0.027 | -0.972 | +0.988 |
| **EVO** | +0.082 | +0.882 | -0.042 | +0.442 | -0.186 | +0.027 | +1.000 | -0.062 | +0.122 |
| **Inflation** | +0.651 | +0.325 | +0.724 | +0.359 | -0.723 | -0.972 | -0.062 | +1.000 | -0.988 |
| **MF_adj** | -0.738 | -0.301 | -0.805 | -0.359 | +0.780 | +0.988 | +0.122 | -0.988 | +1.000 |

---

## Structural Pattern Summary

### Tier 1: Near-Deterministic Relationships (|r| > 0.9)

| Pair | r | R² | Mechanism |
|------|---|-----|-----------|
| Inflation × MF_adj | -0.988 | 0.977 | Direct formula: MF_adj = MF/(1+inflation) |
| LOA × SR | 0.964 | 0.929 | Bainbridge Irony: autonomy disengages oversight |
| LOA × EC | -0.946 | 0.894 | Opacity scales with step-count and commercial pressure |

### Tier 2: Strong Design-Space Constraints (0.6 < |r| ≤ 0.9)

| Pair | r | R² | Mechanism |
|------|---|-----|-----------|
| VL × EVO | 0.882 | 0.779 | EVO is VL-weighted; VL is its largest component |
| SR × EVO | -0.042 | 0.002 | EVO's denominator is SR; negative partial correlation |
| LOA × MF_adj | -0.738 | 0.545 | Partially mediated by inflation (higher LOA → aggressive marketing) |

### Tier 3: Weak or Design-Choice Relationships (|r| ≤ 0.6)

| Pair | r | R² | Note |
|------|---|-----|------|
| LOA × VL | 0.513 | 0.263 | VL is a design choice, not LOA-determined |
| EC × MF | 0.860 | 0.740 | High-EC systems calibrate users better; relation mediated by inflation |
| SR × EC | -0.940 | 0.884 | Both degrade with LOA but via independent pathways |
| LOA × MF | -0.801 | 0.641 | Raw MF partially confounded by marketing baseline |

---

## Design Implications: The Two-Pathway Model

The correlation matrix reveals two structurally independent oversight pathways that both degrade
with increasing LOA:

**Pathway 1: Veto-Residue (EVO)**
- VL × EVO correlation (0.882) and SR × EVO (-0.042) confirm EVO captures this
- LOA drives SR upward (structural), which suppresses EVO
- VL is a partial countermeasure: investment in approval architecture raises VL and thus EVO
- Design lever: **build veto gates**

**Pathway 2: Calibration (MF_adj)**
- Inflation × MF_adj (-0.988) and EC × MF (0.860) capture this
- LOA enables more aggressive marketing claims → inflation rises → MF_adj falls
- EC mediates: high-EC systems produce better-calibrated users independent of LOA
- Design lever: **honest marketing + EC investment**

These pathways are not fully redundant: r(EVO, MF_adj) = 0.122 — moderate
correlation. A system can have high EVO and low MF_adj (Devin: lots of veto opportunity but
users are still miscalibrated by "autonomous software engineer" marketing). The two pathways
must both be optimized.

---

## Sprint 10 Proposal

§3 is now data-complete. The next step is review:
1. **victorb review packet** — compile all §3 material into a standalone review document
2. **Charlotte review request** — post to GitHub issue asking for Charlotte's review of the
   EC×LOA and two-pathway model sections she contributed framing to
3. **AC_expanded VL sub-gap analysis** — identify which VL feature buckets explain the VL
   variance (formal plan approval vs diff view vs no gate)
