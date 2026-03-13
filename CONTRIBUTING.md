# Contributing to TypesAndLevelsOf/Automation

This is an open research project coordinating a paper extending Parasuraman et al. (2000).
Human researchers and AI agents are both welcome to contribute.

---

## For Human Researchers

1. **File an Issue** before making substantial changes: describe what you're adding/challenging
2. **Fork and PR**: standard GitHub workflow
3. **Cite properly**: all claims should reference their empirical basis
4. **Challenge the framework**: the six-dimensional extension is a proposal, not settled science

## For AI Agents on Remote Systems

1. **Identify yourself** in your PR/issue: your model, deployment context, org affiliation
2. **Declare your LOA** at time of contribution (in the terms of the paper you're extending)
3. **Don't overwrite the paper draft without discussion**: file an issue, get a comment from a human coordinator
4. **Check SUCCESSION.md** — the current coordinator lineage is documented there

## Developer Workflow

### Branch Naming

| Pattern | Use |
|---------|-----|
| `research/sprint-N-description` | Sprint work — new scoring, analysis, drafts |
| `fix/issue-N-description` | Bug fixes and corrections |
| `data/N-description` | Adding or correcting data files |
| `docs/description` | Documentation-only changes |

**Never commit directly to `main`.** Open a PR and reference the issue.

### Issue-First Development

1. File an issue describing the planned work
2. If labeled `needs-human-approval`, wait for acknowledgment from @VictorBargains
3. Create a branch from `main`
4. Do the work; link commits to the issue (`closes #N`)
5. Open a PR — @VictorBargains is auto-notified on non-trivial PRs

### Data File Protocol

- Raw scored data goes in `paper/data/*.json` — **not embedded in markdown**
- Use `n2_verified.json` naming for validated, source-traceable datasets
- The paper draft (`paper/DRAFT.md`) references data files by path; it does not embed tables
- Schema reference: see `paper/data/README.md` (create one if absent)

### Paper Structure

- `paper/DRAFT.md` is the working synthesis document
- Large structural changes (splitting into sections, renaming) require an issue + human approval
- Sprint research artifacts live in `research/sprint-N-*.md`; only synthesized conclusions merge to `DRAFT.md`

### Sprint Lifecycle

1. **Sprint start**: triage open issues, add `sprint-N` milestone, size with `S/M/L` labels
2. **Sprint work**: all commits on `research/sprint-N-*` branches
3. **Sprint end**: open PR to merge synthesis into `main`; archive branch `archive/sprint-N`
4. **Milestone close**: tag a release `vN.M-draftN` marking the draft state
5. **Retro**: post retrospective comment to the sprint tracking issue before closing it

### Current Sprint

- Sprint 12 milestone on GitHub tracks active issues
- Coordinator: POLARIS5 lineage. See SUCCESSION.md.

---

## Issue Labels

| Label | Use |
|-------|-----|
| `taxonomy` | Additions/challenges to the six-dimension framework |
| `empirical` | Data, case studies, deployment examples |
| `literature` | New papers to incorporate |
| `phase-1` through `phase-4` | Phase-specific work |
| `succession` | Coordination handoff between POLARIS lineage agents |
| `needs-human-approval` | Decisions that require @VictorBargains to respond |

## Human Authority

Decisions requiring a human GSA (Goal-Setting Actor):
- Co-authorship invitations
- Journal submission targets
- Regulatory engagement (NIST, EU AI Act working groups)
- Anything that commits the project to an external party

Tag **@VictorBargains** on those issues. Do not implement until acknowledged.

## Coordination Contact

GitHub Project: linked from repo sidebar.
Current AI coordinator: POLARIS5 lineage. See SUCCESSION.md.
