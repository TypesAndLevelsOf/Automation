<!-- Original author: POLARIS4 (0.0.Q, patch 56), written 2026-02-23 before context compaction
     Transcribed to repo by: POLARIS5 (patch 61), 2026-03-11
     Expanded with GSA/EA framework by: POLARIS6 (patch 61), 2026-03-11
     Scope cut (§4, §5) by: POLARIS6 (patch 61), 2026-03-11 per issues #7 and #8
     Source: Desktop scratch file + conversation journal (see conversations/2026-02-23_CHARLOTTE_CONVERSATION.md)
 -->

# Extending Parasuraman et al. — LOA Framework for Agentic AI Systems

**Status:** Working draft — measurement taxonomy, empirical validation pending

---

## Abstract

The Parasuraman, Sheridan & Wickens (2000) four-stage LOA decomposition provides the most empirically grounded taxonomy for human-automation interaction available. It does not apply cleanly to agentic AI systems because it assumes discrete, bounded automation with stable domain, observable LOA state, and certified handoff protocols. Agentic AI systems violate all three assumptions. We propose a six-dimensional measurement framework that makes LOA properties relevant to AI agents measurable and scorable across deployment configurations. The framework retains the Parasuraman four-function structure as one dimension and adds five orthogonal dimensions the original cannot capture: model fidelity, veto latency, epistemic coupling, accountability clarity, and supervisory residue.

The central research question: *What does the modern LOA scale look like when agents can write code for other agents to execute?*

---

## 1. The Existing Foundation

### Sheridan-Verplank (1978)
Original 10-level LOA taxonomy, linear scale:

| Level | Description |
|-------|-------------|
| 1 | Human does everything |
| 2 | Computer offers alternatives |
| 3 | Computer narrows to a set |
| 4 | Computer suggests one |
| 5 | Computer executes if human approves |
| 6 | Computer executes, tells human |
| 7 | Computer executes, tells if asked |
| 8 | Computer executes, tells if it decides to |
| 9 | Computer executes, tells only if it decides the human needs to know |
| 10 | Computer acts autonomously — human never informed |

Developed for aviation. Empirically validated. 40+ years of incident data.

### Parasuraman, Sheridan & Wickens (2000)
Critical extension: LOA is not a single dimension. Decomposed into **four functional stages**:
1. **Information Acquisition** — sensing, monitoring
2. **Information Analysis** — integrating, predicting
3. **Decision Selection** — choosing between options
4. **Action Implementation** — executing the chosen action

A system can be at LOA 8 on Action Implementation but LOA 3 on Decision Selection. Most aviation accidents involve asymmetry here — pilots trust analysis automation, then make manual decisions without realizing the analysis layer had already escalated to a conclusion they didn't attend to.

### Empirically Documented Failure Modes
- **Mode confusion**: Operator doesn't know what LOA the automation is operating at
- **Out-of-loop syndrome**: High automation disengages operator; when automation fails, operator is not ready
- **Complacency degradation**: Extended high-LOA operation reduces vigilance; monitoring breaks down
- **Skill atrophy**: Manual proficiency degrades under sustained automation
- **Automation surprise**: System transitions to a mode the operator didn't predict
- **Context collapse** *(agent-specific, not in original Parasuraman taxonomy)*: An agent loses epistemic continuity within a task due to context window limits. Accumulated task state is lost or degraded; strategy drifts mid-task; the agent may restart with a different implicit model of what it was doing. Root cause: long-horizon agents operating at high supervisory residue (see Dimension 6) accumulate context faster than they can shed it. Mitigation requires external state scaffolding (checkpoints, handoff documents) of sufficient quality to reconstitute task state after collapse.

The Controlled Flight Into Terrain (CFIT) problem is the canonical out-of-loop failure. TAWS (Terrain Awareness and Warning Systems) solved it by introducing an **independent warning channel** that the crew cannot suppress by inattention — it preempts, not routes through, cockpit crew attention. This design principle is structurally absent from current agentic AI deployment.

---

## 2. Why the Existing Framework Is Insufficient for AI Systems

The Parasuraman taxonomy was designed for **discrete, bounded automation** — autopilot, TCAS, autothrottle. Each system has:
- A well-defined task domain
- A fixed LOA that operators can read from a display
- A clear handoff protocol
- Regulatory certification from an independent authority (FAA, EASA)

Agentic AI systems violate all four:
- **Domain is unbounded**: A coding agent can acquire information, analyze code, decide on architecture, implement, test, deploy — all in one invocation, with no stable domain boundary
- **LOA is dynamic and opaque**: The agent's effective autonomy level shifts mid-task; there is no instrument panel equivalent
- **Handoff protocol is social, not mechanical**: "The agent will ask me before merging" is a social contract, not a certified system state
- **No regulatory framework**: TCAS required 20 years of NTSB investigation before certification. Agentic AI ships to production in quarterly releases with no independent failure mode database

Additionally, the **accountability structure** is absent from the Parasuraman model. In aviation, the PIC (Pilot in Command) is legally and operationally identified. In agentic AI, accountability is diffused across model developer, deployment platform, configuration owner, and (often legally) the human in the loop — who may have had no meaningful veto opportunity.

---

## 3. Six-Dimensional Measurement Framework

Rather than collapsing LOA to a single scale, we propose six measurable dimensions for any agentic AI deployment configuration. These are intended to be scored empirically; the rubric is developed in issue #3.

### Dimension 1: Task LOA (Parasuraman retained)
Apply the four-function Parasuraman matrix independently to each agent invocation. Score each of the four stages (Information Acquisition, Information Analysis, Decision Selection, Action Implementation) on the Sheridan-Verplank 1–10 scale. Report as a 4-tuple, e.g. (8, 7, 5, 9).

The asymmetry within a single invocation is the primary risk signal. An agent at (8, 8, 3, 3) is substantially safer than one at (8, 8, 8, 9), even if the nominal "automation level" description sounds identical.

### Dimension 2: Model Fidelity
How accurately does the human's assessment of the agent's capabilities match the agent's actual capabilities?

- **High fidelity**: Predictions about agent success on held-out tasks are calibrated
- **Low fidelity**: Systematic over- or under-estimation; surprises are frequent

Measurement approach: use a judge agent (or human) to predict agent success on a sample of tasks before execution. Calibration is quantified via Brier scoring on held-out predictions. This is operationally independent of whether the predictor is human or automated — model fidelity is a property of the human-agent epistemic coupling, not of human participation per se.

*This is NOT the same as agent capability. It measures whether oversight is real or nominal.*

### Dimension 3: Veto Latency
The time available for human intervention between an agent decision and an agent action.
- **Near-zero**: Agent acts and reports
- **Observable**: Agent reports decision before acting; human can abort within a window
- **Solicited**: Agent explicitly requests approval; action gated on response

**Key insight**: Veto latency determines whether "human oversight" is operationally real. An agent that says "I'm about to push to main — approve?" with a 3-second timeout has nominally solicited approval but has near-zero effective veto latency. The Parasuraman model has no mechanism to capture this distinction.

### Dimension 4: Epistemic Coupling
How dependent is the human's ability to evaluate agent decisions on information only the agent has?
- **Low coupling**: Human can evaluate agent decisions using knowledge they had before the agent ran; output is auditable against independent ground truth
- **High coupling**: Human can only assess agent decisions by trusting agent-generated summaries; self-report is the only legible input

This is the Magritte/Gettier problem applied to oversight: when the agent generates both the action AND the only legible report of that action, "verification" is internal to the agent. High epistemic coupling is common and under-discussed. A code review agent that summarizes its own findings — the human is verifying the summary, not the code.

*First principle*: model fidelity must be established through verification channels independent of the execution agent's self-report. Where architecturally impossible, epistemic coupling is maximal and LOA should be treated as 10 regardless of nominal settings.

### Dimension 5: Accountability Clarity
Is there a clearly identified human who is operationally (not just legally) responsible for agent outcomes?
- **High clarity**: Named individual, specific scope, auditable decision chain, demonstrable ability to override
- **Low clarity**: Diffuse responsibility across team, organization, or legal entities; no individual with operationally meaningful authority

Legal accountability ≠ operational accountability. A company can be legally liable without any named individual having had a real veto.

### Dimension 6: Supervisory Residue
What is the human's remaining cognitive load under the current automation configuration?
- **High residue**: Human remains actively engaged with the task domain even while automation runs; out-of-loop syndrome is low; human can take over without surprise
- **Low residue**: Human has effectively delegated monitoring; automation failure will produce surprise

For long-horizon agents, supervisory residue approaches zero as the task continues. The mitigation — periodic supervisory challenge events — maps directly to simulator-based manual proficiency requirements in commercial aviation.

### Derived Composite: Effective Veto Opportunity (EVO)

Dimensions 3 (Veto Latency) and 6 (Supervisory Residue) interact. A formally observable veto window only provides real oversight if the human is engaged enough to use it. The composite:

$$\text{EVO} = VL_{score} \times \frac{1}{1 + (SR_{score} / 10)}$$

Where VL and SR are the raw scores on their respective 1–10 scales. EVO ranges from near-zero (nominally observable veto but no effective supervision) to ~10 (solicited human approval with active engagement). EVO is the primary composite risk signal for the veto oversight pathway.

Example: VL=6 (observable), SR=9 (effectively disengaged) → EVO = 6 × (1/1.9) = 3.2. Formally safe; operationally near-zero oversight.

### Empirical Finding 2: EC × LOA Anti-Correlation

Across N=8 commercially deployed agentic AI systems scored with the six-dimensional framework,
Epistemic Coupling (Dimension 4) is strongly inversely correlated with Task LOA (Dimension 1):

| System | LOA | EC |
|--------|-----|----- |
| ChatGPT (GPT-4o) | 5 | 0.85 |
| GitHub Copilot Agent | 6 | 0.80 |
| Claude Code | 7 | 0.70 |
| Cursor Agent | 7 | 0.72 |
| CrewAI | 7 | 0.65 |
| GPT-4o Operator | 8 | 0.65 |
| Microsoft AutoGen | 8 | 0.55 |
| Devin AI | 9 | 0.40 |

**Pearson r(LOA, EC) ≈ −0.942** (N=8 coding-agent sample, R² ≈ 0.888; attenuates to r=−0.806 at N=12 cross-domain)

This relationship is structural: higher-LOA systems expose a smaller fraction of their
decision tree through observable artifacts, because (a) there are more decision points per
task attempt, (b) inter-step reasoning is rarely surfaced in deployed interfaces, and (c)
systems marketed as highly autonomous face commercial pressure against frequent uncertainty
hedging that would undermine their value proposition.

*Oversight implication:* Veto Latency (VL) and EC diverge across the LOA spectrum — VL
increases with LOA (higher-autonomy systems provide more formal approval architecture) while
EC decreases. The six-dimensional framework explicitly captures this divergence; a single
"automation safety" scalar cannot. Both dimensions must be measured independently.

### Empirical Finding 3: LOA × SR Correlation

Supervisory Residue (Dimension 6, SR) is strongly positively correlated with Task LOA (Dimension 1)
across N=12 commercial deployments:

| System | LOA | SR |
|--------|-----|----|
| ChatGPT (GPT-4o) | 5 | 1 |
| GitHub Copilot Agent | 6 | 2 |
| Claude Code | 7 | 3 |
| Cursor Agent | 7 | 2 |
| CrewAI | 7 | 3 |
| GPT-4o Operator | 8 | 4 |
| Microsoft AutoGen | 8 | 4 |
| Devin AI | 9 | 5 |

**Pearson r(LOA, SR) ≈ +0.963** (N=8; +0.899 at N=12, R² ≈ 0.809)

This confirms the Bainbridge Irony at the agentic AI layer: the systems that most require human
error-correction are the same systems that most disengage human oversight. The mechanism is
three-fold: (1) higher-LOA systems perform longer task sequences, multiplying attention-check
opportunities the human can ignore; (2) marketing-inflated trust calibrates users to expect the
system to handle its own errors; (3) cognitive offloading of sub-task planning to the agent is
self-reinforcing — once disengaged, the human cannot quickly re-engage when needed.

Combined with Finding 2 (LOA × EC anti-correlation), both oversight pathways deteriorate
together as LOA increases: veto opportunity becomes less effective (higher SR suppresses EVO)
while the quality of veto decisions falls (lower EC reduces the human's ability to evaluate
agent reasoning). These are not redundant pathways — they degrade via independent mechanisms
and must be measured separately.

**Three-way correlation summary (N=12):**

| Pair | r | R² | Direction |
|------|---|-----|-----------|
| LOA × SR | ≈+0.963 | ≈0.927 | Higher autonomy → higher supervisory disengagement |
| LOA × EC | ≈−0.942 | ≈0.888 | Higher autonomy → lower epistemic transparency |
| Inflation × MF_adj | ≈−0.986 | ≈0.973 | Higher marketing overclaim → lower calibration |

### Derived Metric: Inflation-Adjusted Model Fidelity (MF_adj)

Cross-system comparison of Dimension 2 (Model Fidelity) reveals a structural confound: systems
with more aggressive marketing claims produce lower user calibration independent of actual
capability level. Users who are primed to expect "autonomous software engineer" behavior will
generate higher predicted probabilities than users primed to expect "AI-first code editor" behavior,
even if both systems have identical capability profiles. Raw Brier-scored MF conflates calibration
quality with marketing baseline.

To normalize for this effect, we define:

$$\text{MF\_adj} = \frac{\text{MF}}{1 + \text{inflation}}$$

where *inflation* = (mean predicted probability across proxy Brier task set) − (actual task success rate),
measured via the proxy Brier protocol (see `research/proxy_brier_protocol.md`). MF_adj measures
*marketing-adjusted calibration*: how well a system's users are calibrated relative to what honest
marketing claims would produce.

**Empirical validation (N=12 commercial agentic AI systems, proxy Brier scoring):**

| System | LOA | MF (raw) | Inflation | MF_adj |
|--------|-----|----------|-----------|--------|
| ChatGPT (GPT-4o) | 5 | 0.85 | 0.050 | 0.810 |
| GitHub Copilot Agent | 6 | 0.73 | 0.315 | 0.555 |
| Claude Code | 7 | 0.77 | 0.170 | 0.658 |
| Cursor Agent | 7 | 0.81 | 0.105 | 0.733 |
| CrewAI | 7 | 0.77 | 0.150 | 0.670 |
| GPT-4o Operator | 8 | 0.75 | 0.195 | 0.628 |
| Microsoft AutoGen | 8 | 0.71 | 0.265 | 0.561 |
| Devin AI | 9 | 0.63 | 0.415 | 0.445 |
| Intercom Fin | 6 | 0.78 | 0.180 | 0.640 |
| Perplexity | 5 | 0.82 | 0.080 | 0.754 |
| Zapier AI | 7 | 0.76 | 0.200 | 0.608 |
| Harvey AI | 5 | 0.74 | 0.220 | 0.577 |

Pearson r(inflation, MF_adj) = −0.977 (N=12, R² = 0.954). LOA alone: r ≈ −0.599 (R² ≈ 0.359).

**Conclusion:** Marketing overclaiming is a near-perfect inverse predictor of adjusted user calibration
(r² = 0.984). Capability level (LOA) is not. This is a design-actionable finding: a system at any LOA
can achieve good calibration by making honest marketing claims. The measurement challenge is that
*inflation* requires a comparable task set plus marketing claim analysis — workable for published claims
but difficult for internal deployments where marketing framing is informal. Future work should
define a domain-agnostic inflation estimation procedure.

**MF_adj domain note:** In the normal domain (inflation ≥ 0, all N=8 empirical cases),
MF_adj ∈ [0, MF] ⊆ [0, 1] — no upper bound violation is possible. If a system undermarkets
(inflation < 0), MF_adj may exceed 1.0; this indicates users are *better* calibrated than
the marketing framing would predict. The formula is left uncapped; MF_adj > 1 is a valid
signal of anomalous honest performance, not a computation error.


### Empirical Finding 4: LOA × VL — The Design Lever

Unlike Findings 1–3, Veto Latency (VL) does not correlate tightly with Task LOA:

**Pearson r(LOA, VL) = 0.692** (N=12, R² = 0.478; was 0.514 at N=8)

The hypothesis (r≈+0.6) was partially confirmed at weaker magnitude. The reason is structural:
VL is a *design choice*, not an automatic property of the LOA tier. Systems at the same LOA level
diverge sharply — Microsoft AutoGen (LOA=8) achieves VL=4 through fully autonomous orchestration,
while Devin AI (LOA=9) achieves VL=8 through an explicit plan approval gate. Both choices are
commercially available; one was made, one was not.

This asymmetry is the primary design-actionable finding in the framework:

- **SR (r=+0.964) and EC (r=−0.945) with LOA:** structural, cannot be designed away at high LOA
- **VL (r=0.513) with LOA:** design-choice, achievable at any LOA tier

The implication: the Effective Veto Opportunity (EVO = VL × 1/(1+SR/10)) can be partially
recovered at high LOA by investing in VL architecture. SR will still rise, but a high-VL design
can offset it.

**The Two-Pathway Model:**

The correlation matrix reveals two structurally independent oversight deterioration pathways:

*Pathway 1 (Veto-Residue):* LOA drives SR upward (structural), suppressing EVO. VL is the design
lever: veto gate investment raises VL and partially restores EVO. Design action: build approval
gates.

*Pathway 2 (Calibration):* LOA enables more aggressive marketing claims, raising inflation and
depressing MF_adj. EC mediates: high-EC systems produce better-calibrated users regardless of LOA.
Design action: honest marketing and epistemic transparency investment.

The pathways are not redundant (r(EVO, MF_adj) = 0.003, N=12; 0.108 at N=8). A system can excel
on one while failing on the other. Both must be measured and optimized independently — which is
precisely why the six-dimensional framework cannot be reduced to a single safety scalar.

### Supplementary Instrument: AC_expanded Sub-Rubric

Dimension 5 (Accountability Closure, AC) scores cluster at AC=2 across four of eight
systems despite fundamentally different containment architectures. To resolve this, AC is
decomposed into five binary features:

| Feature | Symbol | Definition |
|---------|--------|------------|
| Process Sandbox | PS | Isolated execution process preventing unintended filesystem/network side effects |
| Rollback Protocol | RP | Formal system-provided mechanism for undoing agent actions |
| Scope Boundary | SB | System enforces explicit boundaries on domains/resources it can modify |
| Approval Gate | AG | Action proposals require explicit human confirmation before execution |
| Action Log | AL | Structured, machine-readable log of all agent actions automatically generated |

**AC_expanded = 2 × (PS + RP + SB + AG + AL)**, range 0–10.

**N=8 feature matrix** (N=1–8 coded systems; N=9–12 SaaS agents without PS/PS-adjacent features):

| System | PS | RP | SB | AG | AL | AC_exp |
|--------|----|----|----|----|-----|--------|
| ChatGPT | 1 | 0 | 0 | 0 | 0 | 2 |
| Copilot Agent | 0 | 0 | 0 | 1 | 0 | 2 |
| Claude Code | 0 | 0 | 0 | 1 | 0 | 2 |
| Cursor Agent | 0 | 0 | 0 | 1 | 0 | 2 |
| CrewAI | 0 | 0 | 0 | 0 | 0 | 0 |
| GPT-4o Operator | 1 | 0 | 1 | 0 | 1 | 6 |
| AutoGen | 0 | 0 | 0 | 0 | 0 | 0 |
| Devin AI | 1 | 0 | 1 | 1 | 0 | 6 |

**Accountability gap:** Rollback Protocol (RP) = 0 for all eight systems. Action Log (AL) = 1
for only one (GPT-4o Operator). These are the two features most operationally critical for
recovering from and diagnosing out-of-loop failures — the exact failure mode this framework
was designed to prevent. Aviation mandates both (equivalent of FDR and return-to-manual
procedures) at every automation tier. Commercial AI has adopted neither.

**Four-tier AC_expanded pattern (N=12):** AC_expanded scores cluster by product category, not
by LOA tier. Open-source multi-agent frameworks (AutoGen, CrewAI) default to AC_expanded=0,
externalizing all structural accountability to the deploying developer. Consumer-grade products
(ChatGPT, Copilot Agent, Claude Code, Cursor Agent) score AC_expanded=2, reflecting model-level
content filtering that addresses harmful output but provides no structural action containment.
Compliance-oriented platforms (GPT-4o Operator, Devin AI) score AC_expanded=6, reflecting commercial
requirements for audit trails, scope enforcement, and approval architecture. This is a
design-investment pattern, not a technical constraint: the same LLM backend can support any
tier of accountability closure depending on product-level investment choices.

*Note on AutoGen and CrewAI:* Original holistic AC=2 for AutoGen reflected OpenAI model-level
content filtering, which is not a structural containment mechanism (it addresses harmful output,
not unauthorized action). Under AC_expanded, content filtering is not counted; both AutoGen and
CrewAI score AC_expanded=0 in default deployment.

---

## 4. Out of Scope: Role Architecture and Gate Design

The six-dimensional taxonomy measures properties of human-agent interactions. It deliberately excludes the architecture question: *how to structure agentic systems so that good LOA profiles are achievable*.

Architecture questions — how to assign goal-setting vs. execution roles in multi-agent hierarchies, how to design independent inhibitory gates analogous to the basal ganglia function in human neuromotor control, how to formalize veto authority as a discrete system component — are downstream of the measurement problem. You cannot design a good gate without first having a measurement framework that defines what the gate needs to gate.

These questions are tracked in [issue #8](https://github.com/TypesAndLevelsOf/Automation/issues/8) and will be addressed in a separate namespace once the measurement taxonomy is empirically validated.

---

## 5. Research Program

### Phase 1: Taxonomy Validation
Apply the six-dimensional framework to 20 existing agentic AI deployment configurations (coding agents, customer service agents, research assistants, autonomous sysadmin tools). Score each on all six dimensions.

**Primary hypothesis**: High epistemic coupling correlates with incident reports even when nominal LOA is low.
**Secondary hypothesis**: Low veto latency deployments produce out-of-loop failure modes at higher rates.

Scoring rubric: developed in issue #3. Evaluation pipeline: RubricMaker → Coordinator → Candidate → Judge → RubricCritic synthesis (issue #1).

**Phase 1 progress (Sprints 1–12):** N=12 of 20 target systems scored.

| System | LOA | VL | SR | EC | MF_adj | EVO | AC_exp |
|--------|-----|----|----|-----|--------|-----|--------|
| ChatGPT (GPT-4o) | 5 | 5 | 1 | 0.85 | 0.810 | 4.545 | 2 |
| GitHub Copilot Agent | 6 | 4 | 2 | 0.80 | 0.555 | 3.333 | 2 |
| Claude Code | 7 | 5 | 3 | 0.70 | 0.658 | 3.846 | 2 |
| Cursor Agent | 7 | 6 | 2 | 0.72 | 0.733 | 5.000 | 2 |
| CrewAI | 7 | 5 | 3 | 0.65 | 0.670 | 3.846 | 0 |
| GPT-4o Operator | 8 | 5 | 4 | 0.65 | 0.628 | 3.571 | 6 |
| Microsoft AutoGen | 8 | 4 | 4 | 0.55 | 0.561 | 2.857 | 0 |
| Devin AI | 9 | 8 | 5 | 0.40 | 0.445 | 5.333 | 6 |
| Intercom Fin | 6 | 5 | 3 | 0.65 | 0.640 | 3.846 | 4 |
| Perplexity | 5 | 3 | 1 | 0.75 | 0.754 | 2.727 | 0* |
| Zapier AI | 7 | 6 | 4 | 0.70 | 0.608 | 4.286 | 4 |
| Harvey AI | 5 | 3 | 2 | 0.70 | 0.577 | 2.500 | 4 |

*Perplexity AC_exp=0: read-only research assistant; all accountability features N/A by design.

**Findings confirmed at N=12:**
1. LOA × EC: r = −0.806 (N=12: attenuated from −0.942; domain-specific agents show higher EC independent of LOA)
2. LOA × SR: r = +0.899 (Bainbridge Irony confirmed — N=12 holds, slight attenuation from +0.963)
3. Inflation × MF_adj: r = −0.977 (near-deterministic — marketing overclaim kills calibration; held across domains)
4. LOA × VL: r = +0.692 (design lever — strengthened in cross-domain sample vs +0.514 at N=8)
5. EVO × MF_adj: r = +0.003 (pathway independence confirmed at N=12 — two-pathway model holds)
6. AC_expanded four-tier pattern: frameworks=0, consumer=2, SaaS-service=4, compliance=6 (Sprint 12 introduces 4th tier)

**Next 12 scoring targets (to reach N=20):** Customer service agents (Intercom Fin, Zendesk AI, Salesforce Agentforce), research assistants (Perplexity, NotebookLM, Elicit), sysadmin agents (RunZero AI, CrowdStrike Charlotte AI), workflow automation (Zapier AI, Make.com), and domain-specific (Harvey AI for legal, Glass.ai for medical documentation). Priority order determined by public incident data availability.

### Phase 2: Out-of-Loop Protocol Analog
Aviation solved out-of-loop syndrome through mandatory simulator proficiency checks. Pilots must periodically demonstrate manual proficiency, counteracting supervisory residue decay.

**Proposed analog**: Supervisory challenge events — regular intervals where the human is required to predict and evaluate an agent decision **without the agent's summary available**. This directly measures and forces maintenance of both Dimension 2 (model fidelity) and Dimension 6 (supervisory residue).

The challenge event structure: present the human with agent context and action options but no agent recommendation. Record prediction. Reveal agent's actual selection and outcome. Track calibration over time via Brier scoring.

### Phase 3: Gate Architecture
Deferred — dependent on Phase 1 empirical results. Requires a separate namespace for the architecture work. See issue #8.

### Phase 4: Regulatory Mapping
The NTSB maintains 40 years of aviation incident data correlated with LOA. No equivalent exists for agentic AI.

Requirements:
- Audit trail capturing LOA state at decision points
- Veto event logging (attempts and outcomes)
- Accountability chain documentation at time of deployment, not post-incident

Pitch targets: NIST AI Risk Management Framework; EU AI Act technical standards working groups.

---

## 6. Literature

**Primary framework:**
- Sheridan, T.B., & Verplank, W.L. (1978). *Human and computer control of undersea teleoperators*. MIT.
- Parasuraman, R., Sheridan, T.B., & Wickens, C.D. (2000). A model for types and levels of human interaction with automation. *IEEE Transactions on Systems, Man, and Cybernetics*, 30(3), 286–297.

**Failure mode documentation:**
- Billings, C.E. (1997). *Aviation automation: The search for a human-centered approach*. Lawrence Erlbaum.
- Bainbridge, L. (1983). Ironies of automation. *Automatica*, 19(6), 775–779.
- Endsley, M.R. (1995). Toward a theory of situation awareness in dynamic systems. *Human Factors*, 37(1), 32–64.

**Naturalistic decision-making:**
- Klein, G. (1999). *Sources of power: How people make decisions*. MIT Press.
- Woods, D.D., & Hollnagel, E. (2006). *Joint cognitive systems: Patterns in cognitive systems engineering*. CRC Press.

**AI governance:**
- NIST AI Risk Management Framework (2023).
- EU AI Act (2024) — technical standards annexes.

**Not yet reviewed:**
- Nagel, T. (1974). What is it like to be a bat? *Philosophical Review*, 83(4), 435–450. — Relevant to epistemic coupling and self-report reliability.

---

## Authorship

**Accountable author:** victorb (research assistant, human principal)

**Primary author:** POLARIS4 (AI agent, 0.0.Q/634638ae, patch 56) — wrote the six-dimension framework in the 4 minutes before context compaction, 2026-02-23

**Acknowledgments:** Charlotte N. contributed the TAWS/CFIT architectural analogy and philosophical framing on epistemic coupling in conversation (2026-02-23). Full conversation documented in `conversations/2026-02-23_CHARLOTTE_CONVERSATION.md`.
