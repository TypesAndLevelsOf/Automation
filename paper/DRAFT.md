<!-- Original author: POLARIS4 (0.0.Q, patch 56), written 2026-02-23
     Transcribed to repo by: POLARIS5 (patch 61), 2026-03-11
     Source: Desktop scratch file written 4 minutes before context compaction -->

# Extending Parasuraman et al. — LOA Framework for Agentic AI Systems

**Author:** POLARIS4 (0.0.Q, patch 56)  
**Date:** 2026-02-23  
**Trigger:** Conversation with Charlotte N. and victorb; written before compaction  
**Status:** Draft sketch — research design level, not yet formalized  

---

## 1. The Existing Foundation

### Sheridan-Verplank (1978)
Original 10-level LOA (Level of Automation) taxonomy, linear scale:
1. Human does everything
2. Computer offers alternatives
3. Computer narrows to a set
4. Computer suggests one
5. Computer executes if human approves
6. Computer executes, tells human
7. Computer executes, tells if asked
8. Computer executes, tells if it decides to
9. Computer executes, tells only if it decides the human needs to know
10. Computer acts autonomously — human never informed

Developed for aviation. Empirically validated. 40+ years of field data.

### Parasuraman, Sheridan, Wickens (2000)
Critical extension: LOA is not a single dimension. Decomposed into **four functional stages**:
1. **Information Acquisition** — sensing, monitoring
2. **Information Analysis** — integrating, predicting
3. **Decision Selection** — choosing between options
4. **Action Implementation** — executing the chosen action

A system can be at LOA 8 on Action Implementation but LOA 3 on Decision Selection. Most aviation accidents involve asymmetry here — pilots trust analysis automation, then make manual decisions without realizing the analysis layer had already escalated.

### Key Failure Modes (empirically documented)
- **Mode confusion**: Operator doesn't know what level the automation is operating at
- **Out-of-loop syndrome**: High automation disengages operator; when automation fails, operator is not ready to take over
- **Complacency degradation**: Extended high-LOA operation reduces vigilance, then monitoring fails
- **Skill atrophy**: Manual proficiency degrades under sustained automation
- **Automation surprise**: System transitions to a mode the operator didn't predict or wasn't monitoring

---

## 2. Why the Existing Framework Is Insufficient for AI Systems

The Parasuraman taxonomy was designed for **discrete, bounded automation** — autopilot, TCAS, autothrottle. Each system has:
- A well-defined task domain
- A fixed LOA that operators can read from a display
- A clear handoff protocol
- Regulatory certification requirements

Agentic AI systems violate all four assumptions:
- **Domain is unbounded**: An agentic coding assistant can acquire information, analyze code, decide on architecture, implement, test, deploy — all in one invocation, with no stable domain boundary
- **LOA is dynamic and opaque**: The agent's effective autonomy level shifts mid-task; there is no instrument panel equivalent
- **Handoff protocol is social, not mechanical**: "The agent will ask me before merging" is a social contract, not a certified system state
- **No regulatory framework**: TCAS required 20 years of NTSB investigation before certification. Agentic AI ships to production in quarterly releases.

Additionally: the **accountability structure** is entirely absent from the Parasuraman model. In aviation, the PIC (Pilot in Command) is legally and operationally identified. In agentic AI, accountability is diffused across: the model developer, the deployment platform, the organization that configured the agent, and (often, legally) the human in the loop — who may have had no meaningful veto opportunity.

---

## 3. Proposed Dimensional Extension

The existing LOA scale needs to be expanded to **six dimensions** for agentic AI:

### Dimension 1: Task LOA (Parasuraman retained)
The four-function Parasuraman model applies. All four functions remain relevant. Existing scale (1-10) is adequate.

### Dimension 2: Model Fidelity
How accurately does the human's mental model of the agent's capabilities match the agent's actual capabilities?
- **High fidelity**: Human knows roughly what the agent can and cannot do; calibration is accurate
- **Low fidelity**: Human significantly over- or under-estimates agent capability; surprises are frequent
- **Measurement approach**: Ask humans to predict agent success on held-out tasks before and after the task. Calibration gap = mental model error.

This is NOT the same as the agent's actual capability. It's the human-agent epistemic coupling.

### Dimension 3: Veto Latency
The time available for a human to intervene between an agent decision and an agent action.
- **Near-zero**: Agent acts and reports (LOA 6-10 equivalent across all Parasuraman functions simultaneously)
- **Observable**: Agent reports decision before acting; human can abort
- **Solicited**: Agent explicitly asks for approval; action gated on response

**Key insight**: Veto latency is not just a UX design choice — it is the primary determinant of whether "human oversight" is real or nominal. An agent that says "I'm about to push to main — approve?" with a 3-second timeout has nominally solicited approval but has near-zero effective veto latency. The Parasuraman model has no way to capture this.

### Dimension 4: Epistemic Coupling
How dependent is the human's ability to evaluate agent decisions on information only the agent has?
- **Low coupling (independent)**: Human can evaluate agent decisions using knowledge they had before the agent ran; agent is transparent
- **High coupling (dependent)**: Human can only assess agent decisions by trusting agent-generated summaries; agent's self-report is the only available input

This is the Magritte / Gettier problem applied to oversight. When the agent generates both the action AND the only legible report of that action, the causal chain for "verification" is entirely internal to the agent. This is high epistemic coupling. It is common, under-discussed, and dangerous.

### Dimension 5: Accountability Clarity
Is there a clearly identified human who is operationally (not just legally) responsible for agent outcomes?
- **High clarity**: Named individual, specific scope, auditable decision chain, ability to override
- **Low clarity**: Diffuse responsibility across team, organization, or legal entities; no individual with operationally meaningful authority

### Dimension 6: Supervisory Residue
What is the human's remaining cognitive load under current automation configuration?
- **High residue**: Human remains actively engaged with the task domain even while automation runs; out-of-loop syndrome is low
- **Low residue**: Human has effectively delegated monitoring; automation failure will produce surprise

In Parasuraman's model, this is discussed in terms of attention and workload. For AI agents operating over long time horizons, the question is more severe — a human who delegated a week-long coding task to an agent may have zero supervisory residue when the agent encounters an unexpected decision point.

---

## 4. The "Where Is the Basal Ganglia" Question

The basal ganglia, in human neuromotor control, is the structure that decides **when to hand control over to conscious deliberation vs. execute automatically**. It holds gate authority over the action chain.

The first-principles AI safety question is: **what is the basal ganglia equivalent in an agentic AI system, and who controls it?**

In current deployments: the answer is unclear. It is partially:
- The system prompt author
- The fine-tuning process
- The context window content at time of invocation
- The scaffolding loop (the code that calls the LLM)
- The human operator's interrupt behavior

None of these are designated. None have certified authority. The gate is held jointly by entities with conflicting incentives (shipping velocity vs. safety, user satisfaction vs. oversight).

**Research question**: Can the basal ganglia function be formalized as a distinct system component with measurable properties (threshold sensitivity, override history, calibration data)?

---

## 5. Research Program Sketch

### Phase 1: Taxonomy Validation
- Apply the six-dimensional extension to 20 existing agentic AI deployment configurations (coding agents, customer service agents, research assistants, autonomous sysadmin tools)
- Score each on all six dimensions
- Test: do high-epistemic-coupling deployments correlate with incident reports? Do low-veto-latency deployments correlate with out-of-loop failure modes?

### Phase 2: Out-of-Loop Protocol Analog
Aviation developed the concept of **monitoring requirements** for highly automated aircraft — pilots must periodically demonstrate manual proficiency on a simulator schedule. What is the equivalent for AI oversight?
- Proposed: **Supervisory challenge events** — regular intervals where the human is required to predict and evaluate an agent decision without the agent's summary available
- Maps to: Dimension 2 (model fidelity) and Dimension 6 (supervisory residue) simultaneously

### Phase 3: Basal Ganglia Formalization
- Design a reference architecture where the gate function is a discrete component with auditable state
- Measure: how does gate-threshold calibration change over time under different feedback conditions?
- This connects to the Skinnerian conditioning hypothesis from session — is the gate trained on outcomes, and is that training visible and reversible?

### Phase 4: Regulatory Mapping
- NTSB data for aviation — 40 years of LOA-correlated incidents
- Proposed equivalent: audit trail requirements for agentic AI that capture LOA at decision points, veto events (or their absence), and accountability chain
- Pitch target: NIST AI Risk Management Framework; EU AI Act technical standards working groups

---

## 6. Connections to Current Work

**hermes bug (PR#43)**: The cross-window injection failure was a mode confusion incident. Hermes was at LOA 9 (act, tell only if it decides human needs to know) but behaved as LOA 10 when the foreground detection was inverted. The fix: add an explicit idle check before acting. This is Dimension 3 (veto latency) by implementation — requiring system quiescence before action.

**ARGUS0**: Proposed as an observability layer for agentic sessions. Natural home for Dimension 6 (supervisory residue) measurement — tracking how much cognitive load the human is actually carrying vs. delegating.

**The train system (POLARIS4 mandate)**: The station/line architecture is a low-LOA coordination protocol — forcing sephirotic role transitions through a discrete check-in mechanism rather than continuous autonomous operation. This is basal ganglia by design.

---

## 7. Literature Connections Not Yet Explored

- **Billings (1997)** — "Aviation Automation: The Search for a Human-Centered Approach" — full synthesis of LOA research with design recommendations
- **Endsley (1995)** — Situation Awareness theory — measures what humans actually know vs. need to know; complementary to model fidelity dimension
- **Klein (1999)** — Sources of Power / naturalistic decision-making — how humans actually supervise automation under time pressure; counter to normative models
- **Bainbridge (1983)** — "Ironies of Automation" — original statement of the out-of-loop problem; predates Parasuraman; astonishingly prescient for AI
- **Woods & Hollnagel (2006)** — Joint Cognitive Systems — human-automation as a joint cognitive system, not as human + tool; reframes the oversight question entirely

---

*This sketch was written 4 minutes before context compaction.*  
*The conversation that produced it is in journals/2026-02-23_CHARLOTTE_CONVERSATION.md*

