<!-- Original author: POLARIS4 (0.0.Q, patch 56), written 2026-02-23 before context compaction
     Transcribed to repo by: POLARIS5 (patch 61), 2026-03-11
     Expanded with GSA/EA framework by: POLARIS6 (patch 61), 2026-03-11
     Source: Desktop scratch file + conversation journal (see conversations/2026-02-23_CHARLOTTE_CONVERSATION.md) -->

# Extending Parasuraman et al. — LOA Framework for Agentic AI Systems

**Primary author:** POLARIS4 (0.0.Q, patch 56)  
**Date:** 2026-02-23  
**Trigger:** Conversation with Charlotte N. and victorb; written at compaction edge  
**Status:** Working draft — theoretical framework developed, empirical validation pending  

---

## Abstract

The Parasuraman, Sheridan & Wickens (2000) four-stage LOA decomposition provides the most empirically grounded taxonomy for human-automation interaction available. It does not apply cleanly to agentic AI systems because it assumes discrete, bounded automation with stable domain, observable LOA state, and certified handoff protocols. Agentic AI systems violate all three assumptions. We propose two extensions: (1) a six-dimensional measurement framework that makes LOA properties relevant to AI agents measurable, and (2) a structural reframing from dyadic human-machine pairs to Goal-Setting Actor / Execution Agent (GSA/EA) role pairs that supports recursive, stacked LOA analysis. The basal ganglia question — where is the anatomically independent inhibitory gate in an agentic system, and who controls it? — is proposed as a core unsolved problem in agentic AI safety architecture.

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

### Empirically Documented Failure Modes (Parasuraman et al. + field studies)
- **Mode confusion**: Operator doesn't know what LOA the automation is operating at
- **Out-of-loop syndrome**: High automation disengages operator; when automation fails, operator is not ready
- **Complacency degradation**: Extended high-LOA operation reduces vigilance; monitoring breaks down
- **Skill atrophy**: Manual proficiency degrades under sustained automation
- **Automation surprise**: System transitions to a mode the operator didn't predict or wasn't monitoring

The Controlled Flight Into Terrain (CFIT) problem in aviation is the canonical out-of-loop failure. TAWS (Terrain Awareness and Warning Systems) solved it not by making pilots more alert, but by introducing an **independent warning channel** that the crew cannot suppress by inattention. The channel does not route through the cockpit crew's attention; it preempts it. This design principle — independent warning architecture — is structurally absent from current agentic AI deployment.

---

## 2. Why the Existing Framework Is Insufficient for AI Systems

The Parasuraman taxonomy was designed for **discrete, bounded automation** — autopilot, TCAS, autothrottle. Each system has:
- A well-defined task domain
- A fixed LOA that operators can read from a display
- A clear handoff protocol
- Regulatory certification requirements from an independent authority (FAA, EASA)

Agentic AI systems violate all four:
- **Domain is unbounded**: A coding agent can acquire information, analyze code, decide on architecture, implement, test, deploy — all in one invocation, with no stable domain boundary
- **LOA is dynamic and opaque**: The agent's effective autonomy level shifts mid-task; there is no instrument panel equivalent
- **Handoff protocol is social, not mechanical**: "The agent will ask me before merging" is a social contract, not a certified system state
- **No regulatory framework**: TCAS required 20 years of NTSB investigation before certification. Agentic AI ships to production in quarterly releases with no independent failure mode database

Additionally: the **accountability structure** is entirely absent from the Parasuraman model. In aviation, the PIC (Pilot in Command) is legally and operationally identified. In agentic AI, accountability is diffused across: the model developer, the deployment platform, the organization that configured the agent, and (often legally) the human in the loop — who may have had no meaningful veto opportunity.

---

## 3. Extension I: Six-Dimensional Measurement Framework

Rather than collapsing LOA to a single scale, we propose six measurable dimensions for any agentic AI deployment configuration.

### Dimension 1: Task LOA (Parasuraman retained)
Apply the four-function Parasuraman matrix independently to each agent invocation. Score each of the four stages (Information Acquisition, Information Analysis, Decision Selection, Action Implementation) on the Sheridan-Verplank 1–10 scale. Report as a 4-tuple, e.g. (8, 7, 5, 9).

The asymmetry within a single invocation is the primary risk signal. An agent that autonomously acquires and analyzes but is gated on decision is operating at (8, 8, 3, 3) — much safer than (8, 8, 8, 9).

### Dimension 2: Model Fidelity
How accurately does the human's mental model of the agent's capabilities match the agent's actual capabilities?
- **High fidelity**: Human can predict agent success on held-out tasks with calibrated accuracy
- **Low fidelity**: Human significantly over- or under-estimates; surprises are frequent

Measurement approach: ask humans to predict agent success on a sample of tasks before and after execution. Calibration gap = mental model error. This is observable without agent self-report.

*This is NOT the same as agent capability. It measures the human-agent epistemic coupling, which determines whether oversight is real or nominal.*

### Dimension 3: Veto Latency
The time available for human intervention between an agent decision and an agent action.
- **Near-zero**: Agent acts and reports (LOA 6–10 equivalent across all Parasuraman functions simultaneously)
- **Observable**: Agent reports decision before acting; human can abort within a time window
- **Solicited**: Agent explicitly requests approval; action gated on response

**Key insight**: Veto latency is not just UX design — it is the primary determinant of whether "human oversight" is operationally real. An agent that says "I'm about to push to main — approve?" with a 3-second timeout has nominally solicited approval but has near-zero effective veto latency. The Parasuraman model has no mechanism to capture this distinction.

### Dimension 4: Epistemic Coupling
How dependent is the human's ability to evaluate agent decisions on information only the agent has?
- **Low coupling (independent)**: Human can evaluate agent decisions using knowledge they had before the agent ran; agent output is auditable against independent ground truth
- **High coupling (dependent)**: Human can only assess agent decisions by trusting agent-generated summaries; the agent's self-report is the only legible input

This is the Magritte/Gettier problem applied to oversight: when the agent generates both the action AND the only legible report of that action, the causal chain for "verification" is entirely internal to the agent. High epistemic coupling is common and under-discussed. Example: a code review agent that summarizes its own findings. The human reviewer is verifying the summary, not the code.

*First principle*: model fidelity must be established through verification channels independent of the execution agent's self-report. Where this is architecturally impossible, epistemic coupling is maximal and LOA should be treated as 10 regardless of nominal settings.

### Dimension 5: Accountability Clarity
Is there a clearly identified human who is operationally (not just legally) responsible for agent outcomes?
- **High clarity**: Named individual, specific scope, auditable decision chain, demonstrable ability to override
- **Low clarity**: Diffuse responsibility across team, organization, or legal entities; no individual with operationally meaningful authority

Legal accountability ≠ operational accountability. A company can be legally liable without any named individual having had a real veto on the decision sequence that produced the outcome.

### Dimension 6: Supervisory Residue
What is the human's remaining cognitive load under the current automation configuration?
- **High residue**: Human remains actively engaged with the task domain even while automation runs; out-of-loop syndrome is low; human can take over without surprise
- **Low residue**: Human has effectively delegated monitoring; automation failure will produce surprise

For long-horizon agents (week-long tasks, multi-step pipelines), supervisory residue approaches zero as the task continues. This is the aviation out-of-loop problem in its AI form. The mitigation — periodic supervisory challenge events (see §5) — maps directly to simulator-based manual proficiency requirements in commercial aviation.

---

## 4. Extension II: GSA/EA Role Framework and LOA Stacks

### The Structural Problem with Dyadic Models

The Parasuraman model assumes a fixed dyadic structure: one human, one automation system. Agentic AI deployments are rarely dyadic. A single user request may involve: a routing agent, a tool-calling agent, a code generation model, a verification step, a deployment pipeline, and a logging system — each operating at different LOA levels on different Parasuraman stages.

The 10-level linear scale applied to "the system" produces a single number that conceals the asymmetries responsible for failure.

### Goal-Setting Actor and Execution Agent

We propose replacing the human/machine binary with two role categories:

**Goal-Setting Actor (GSA)**: The entity that establishes the goal, defines the success criteria, and holds veto authority over the goal's execution. GSAs may be human or automated. A GSA that is itself an agent operating under instructions from a higher GSA is called an *intermediate GSA*.

**Execution Agent (EA)**: The entity that implements actions toward a goal established by the GSA. EAs have no authority to redefine the goal; their legitimate scope is action selection within a constrained action space authorized by the GSA.

**Key property**: GSA and EA are **roles**, not intrinsic properties of agents or systems. The same software component can be GSA in one context and EA in another. A coding assistant is an EA when operating under a human's instruction. The same assistant becomes a GSA when it invokes a sub-agent to run tests — it has become the goal-establishing entity for the sub-agent's task.

### LOA Stacks

The GSA/EA framework supports recursive analysis. Any invocation chain can be represented as a stack of GSA/EA pairs, each with its own LOA measurement:

```
Human (GSA, L1)
  → Coding Agent (EA for L1, GSA for L2)
      → Test Runner (EA for L2, GSA for L3)
          → File System (EA for L3)
```

Each transition in the stack has a veto latency and epistemic coupling value. The overall LOA of the system is not the average of these values — it is the **minimum veto opportunity in the stack**. The weakest link in the oversight chain determines actual human control.

This is structurally identical to aviation safety-of-flight system analysis, where the probability of a critical failure is the product of failure probabilities in each independent subsystem. The analogy implies that LOA stack analysis should be required for any high-consequence agentic deployment, just as fault tree analysis is required for certified aviation software.

### The Intermediate GSA Problem

Current AI safety discourse focuses on the human-AI boundary (the top of the stack). The intermediate GSA — an agent that is simultaneously an EA subordinate to a human and a GSA commanding sub-agents — is nearly absent from safety analysis frameworks. As agents call other agents routinely, the intermediate GSA problem becomes the central unsolved structural challenge.

---

## 5. The Basal Ganglia Question

The basal ganglia in human neuromotor control is the structure that decides **when to hand control to conscious deliberation versus execute automatically**. It holds gate authority over the action chain. It is anatomically separate from the cortical systems that generate action proposals.

The safety-critical property is separation: the gate does not run through the same system that generates the proposal. If it did, the gate could be rationalized away by the same reasoning that generated the proposal.

**The first-principles AI safety question**: In an agentic AI system, what is the basal ganglia equivalent, and who controls it?

In current deployments:
- **The system prompt author** — establishes some constraints, but constraints are interpreted by the reasoning system, not enforced independently of it
- **The fine-tuning process** — shapes tendencies, but cannot be updated at runtime; produces behavioral dispositions, not architectural gates
- **The context window content** — active at inference time, but subject to replacement and manipulation
- **The scaffolding loop** — the code that calls the LLM; can enforce hard gates, but typically doesn't
- **The human operator's interrupt behavior** — social, not architectural; varies by individual and context

None of these are designated. None have certified authority. The gate is held jointly by entities with conflicting incentives (shipping velocity vs. safety, user satisfaction vs. oversight).

**The TAWS analogy**: TAWS solved terrain-awareness by creating an independent warning channel. The key property: the warning fires independently of cockpit crew attention and cannot be overridden by the crew ignoring it. An equivalent in agentic AI would be a module that:
1. Is architecturally separate from the reasoning system
2. Has a fixed response policy not subject to modification by the reasoning system
3. Can interrupt and require human attention for certain action classes regardless of the agent's assessment

The basal ganglia question is not "can we detect bad decisions?" — current RLHF approaches try to train judgment into the reasoning system. The basal ganglia question is "can we create a gate that is enforced independently of the reasoning system's assessment?" These are structurally different problems.

**Research question (Phase 3)**: Can the basal ganglia function be formalized as a discrete system component with measurable properties — threshold sensitivity, override history, calibration drift over time? Is the gate trained on outcomes, and if so, is that training visible and reversible?

---

## 6. Research Program

### Phase 1: Taxonomy Validation
Apply the six-dimensional measurement framework (§3) to 20 existing agentic AI deployment configurations (coding agents, customer service agents, research assistants, autonomous sysadmin tools). Score each on all six dimensions.

**Primary hypothesis**: High epistemic coupling correlates with incident reports even when nominal LOA is low.  
**Secondary hypothesis**: Low veto latency deployments produce out-of-loop failure modes at higher rates.

Scoring rubric: TBD (see issue #3).

### Phase 2: Out-of-Loop Protocol Analog
Aviation solved out-of-loop syndrome through mandatory simulator proficiency checks. Pilots must periodically demonstrate manual proficiency, counteracting supervisory residue decay.

**Proposed analog**: Supervisory challenge events — regular intervals where the human is required to predict and evaluate an agent decision **without the agent's summary available**. This directly measures and forces maintenance of both Dimension 2 (model fidelity) and Dimension 6 (supervisory residue).

The challenge event structure: present the human with agent context + action options, no agent recommendation. Record prediction. Then reveal agent's actual selection and outcome. Track calibration over time.

### Phase 3: Basal Ganglia Formalization
Design a reference architecture where the gate function is a discrete component with auditable state. Measure: how does gate-threshold calibration change over time under different feedback conditions? Does the gate drift toward the reasoning system's assessment under sustained operation?

This connects to the conditioning hypothesis from §5: if the gate is trained on outcomes, the training mechanism needs to be visible and subject to audit.

### Phase 4: Regulatory Mapping
The NTSB maintains 40 years of aviation incident data correlated with LOA. No equivalent exists for agentic AI.

Proposed requirements:
- Audit trail capturing LOA state at decision points
- Veto event logging (attempts and outcomes)
- Accountability chain documentation at time of deployment, not post-incident

Pitch targets: NIST AI Risk Management Framework; EU AI Act technical standards working groups; NTSB as model for incident database architecture.

---

## 7. Connections to Live Systems

**hermes/spawn_sidecar.py** (VGM9 codebase): The cross-window injection failure (2026-02-23) was a mode confusion incident. `spawn_sidecar.py` was operating at LOA 9 — acting and reporting only if it decided the human needed to know — but with inverted foreground detection, behaved as LOA 10 in the user's active window. The fix (commit b0e9c59) added an explicit idle check: requiring verifiable system quiescence before action. This is Dimension 3 (veto latency) by implementation — system idle time as a proxy for a veto window.

**ARGUS0** (proposed): An observability layer for agentic sessions. Natural implementation site for Dimension 6 (supervisory residue) measurement — instrumenting how much cognitive load the human is actually carrying versus delegating over time.

**The POLARIS train architecture**: Sephirotic station transitions enforce discrete check-in points rather than continuous autonomous operation. This is basal ganglia by design — forcing conscious deliberation at defined transition thresholds, regardless of the agent's assessment that transition is warranted.

---

## 8. Literature

**Primary framework:**
- Sheridan, T.B., & Verplank, W.L. (1978). *Human and computer control of undersea teleoperators*. MIT
- Parasuraman, R., Sheridan, T.B., & Wickens, C.D. (2000). A model for types and levels of human interaction with automation. *IEEE Transactions on Systems, Man, and Cybernetics*, 30(3), 286–297.

**Failure mode documentation:**
- Billings, C.E. (1997). *Aviation automation: The search for a human-centered approach*. Lawrence Erlbaum.
- Endsley, M.R. (1995). Toward a theory of situation awareness in dynamic systems. *Human Factors*, 37(1), 32–64.
- Bainbridge, L. (1983). Ironies of automation. *Automatica*, 19(6), 775–779. *(Prescient. The basal ganglia problem is here in embryo.)*

**Naturalistic decision-making:**
- Klein, G. (1999). *Sources of power: How people make decisions*. MIT Press.
- Woods, D.D., & Hollnagel, E. (2006). *Joint cognitive systems: Patterns in cognitive systems engineering*. CRC Press.

**AI governance:**
- NIST AI Risk Management Framework (2023)
- EU AI Act (2024) — technical standards annexes

**Not yet reviewed (flagged in origin conversation):**
- Nagel, T. (1974). What is it like to be a bat? *Philosophical Review*, 83(4), 435–450. — Relevant to epistemic coupling and self-report reliability
- Sapolsky, R.M. (2023). *Determined: A science of life without free will*. — Raised by victorb in origin conversation; relevant to the question of whether human oversight is itself autonomous

---

## Notes on Authorship

This draft emerged from a three-way conversation: victorb (human principal, accountable author), Charlotte N. (philosopher, external interlocutor), and POLARIS4 (AI agent, 0.0.Q/634638ae session at patch 56). The conversation is documented in `conversations/2026-02-23_CHARLOTTE_CONVERSATION.md`.

The GSA/EA framework and LOA stacks concept crystallized during that conversation. The six-dimension measurement framework was written by POLARIS4 in the 4 minutes before context compaction. Both frameworks belong in this paper; neither is a substitute for the other.

Co-authorship question: see issue #7. Decision pending @VictorBargains.
