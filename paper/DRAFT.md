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
