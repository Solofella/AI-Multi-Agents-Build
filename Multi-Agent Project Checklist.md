Chat #106 · September 9, 2026

## Multi-Agent Project Checklist — Complete, Merged (Process + Tools/Materials + Agent Creation)

---

### LAYER 1 — Systems/Solutions Architecture (pre-build)

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 1 | Write the Field Traceability Map before Node 1 exists. Every field required at any output must be declared at source before build begins. | Diagramming tool producing an actual visual map (field origin → transformation → destination per agent) — not prose. Currently reconstructed after the fact in this project; must exist before build starts next time. |
| 2 | Define the source-of-truth rule in writing before building, not after drift is discovered. Live workflow JSON is truth. Docs are generated *from* it, never written in parallel. | Schema Registry as a Phase-0 deliverable. This project built one (`Schema Registry mqv1znpza948pm9`), but mid-project — needs to exist from day one next time. |
| 3 | Decide minimum viable agent count before ideal agent count. For each proposed agent, write one sentence: "this agent is required because output X cannot be produced without it." If you can't write that sentence, it's a refinement stage, not a required agent — flag as a future-consolidation candidate now. | Decision log (markdown file or NocoDB table) capturing this reasoning as it's decided, not reconstructed later from chat history. |
| 4 | Name your non-negotiable product principle (e.g., "detect, never prescribe") and decide, in writing, which specific node in which specific agent structurally enforces it — not "the prompt says so," an actual gate/checkpoint. | Same decision log — record which node, confirmed *working*, not just planned. |

---

### LAYER 2 — Engineering / Build Discipline

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 5 | One shared validation/gate library, not per-agent reimplementation. Any check plausibly needed by more than one agent (trust/dignity, halt conditions, dedup logic) gets built once and referenced, never copy-pasted with local variation. | Version-controlled shared snippet/module repo (e.g., a shared JS file in the GitHub knowledge repo) every Code Node copies from at build time. |
| 6 | Every node that fetches a list gets a scalability question answered at build time, not left open: what happens when this table exceeds N rows? No hardcoded limits without a pagination/filtering plan, even in v1. | No additional tool — enforced via the Layer 1 decision log. |
| 7 | Every bulk write gets a post-write verification step decided at build time — in scope for v1, or explicitly deferred with a tracked ticket and date. | Same decision log, plus an issue-tracker entry if deferred (see Layer 5 tool). |
| 8 | Decide error-handling policy (retryOnFail/onError) per node type before building the first instance, then apply uniformly. | Same decision log. |
| 9 | One node at a time, confirm before proceeding. *(Kept — worked in this project.)* | No new tool — process only. |
| 10 | After every bug fix, immediately audit every structurally similar node in the pipeline for the same pattern — same session, not "later." | Node inventory/registry per agent (can live inside the Field Traceability Map) to search against. |
| 11 | No fabricated or placeholder metric ever ships to a path that could reach a client, even flagged "known issue, accepted." If a metric can't be computed correctly yet, omit the field or hold the feature. | Staging/test environment (see Layer 3) to catch this before it reaches production config. |
| 12 | Full replacement code blocks, no partial diffs. *(Kept — worked in this project.)* | No new tool — process only. |
| **21** | **Decide the build environment per agent before starting: which platform hosts the agent's logic (n8n Code Node, standalone script, dedicated agent framework), which LLM API, which model per agent, and why that model fits that agent's job.** | **Documented per-agent spec: platform + model + rationale. This project has it de facto (n8n Code Nodes, gpt-5.2 for EIP, claude-sonnet-4-6 for BRA/RDA) but never as one deliberate spec — decided agent-by-agent as the project progressed.** |
| **22** | **Build and test each agent in isolation before wiring it into the pipeline.** | **An isolated test harness — run one agent alone, feed sample input, check output, without needing the full 8-agent chain live. Does not exist in this project; testing has always been live-pipeline or manual chat-based JSON review.** |
| **23** | **Define the prompt-engineering standard before writing the first system prompt.** | **This project already derived one correctly through experience: fewer, broader principles over exhaustive rule-lists (confirmed to reduce rule-competition/attention-dilution). Should be written down as a standing guideline *before* the first prompt is drafted next time, not discovered through trial across 8 agents.** |
| **24** | **Decide the "unit of an agent" explicitly: is an agent one LLM call, one Code Node chain, or a multi-step sub-workflow?** | **A definition document. Currently inconsistent across this project's agents — ALA is mostly deterministic/CSV logic, EIP is an LLM call with dictionary injection, BRA is an LLM call plus template library plus governance check. Fine in practice but never explicitly decided; it emerged. Deciding it up front avoids scope confusion when estimating build time per agent.** |

---

### LAYER 3 — QA / Verification (runs continuously, parallel to Layers 1–2)

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 13 | State source for every claim: direct observation / user-provided / training data / educated guess / unknown. *(Kept — worked, but should start day one instead of being retrofitted mid-project as it was here, at Chat #104.)* | No new tool — discipline only. |
| 14 | Get an independent live-code audit (second reviewer, or a fresh audit pass with no prior context) before any pilot launch, not after. | Staging/test environment separate from the production droplet — **does not currently exist; single largest missing material in this project.** |
| 15 | Confidence markers (✅ HIGH / ⚠️ MEDIUM / ❌ LOW / 🚫 DO NOT CLAIM) on every architectural claim. *(Kept — worked.)* | No new tool — discipline only. |
| — | Regression testing after any change to catch unintended breakage. | Fixed set of sample reviews run through the full pipeline with expected-output diffing — **does not exist; second-largest missing material in this project.** |

---

### LAYER 4 — Release Management / Launch Gate

*(The layer with no tooling or artifact at all in this project — confirmed structurally absent, not merely under-resourced.)*

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 16 | Write a launch-blocking checklist, separate from the general backlog, and don't launch until every item on it — not the general horizon list — is closed. | A standalone, versioned checklist document/artifact — **does not exist for this project; did not exist for AJI-001's actual launch.** |
| 17 | Explicitly rank data-integrity and governance-gap fixes above new-feature work, every time, until pilot is stable. Write the ranking down so it isn't re-litigated per decision. | Written ranking policy inside the same release-gate artifact. |
| 18 | Before calling anything "pilot ready," name the one thing that most differentiates the product from a generic LLM wrapper, and confirm the node enforcing it is verified *working*, not just built. | Sign-off field inside the release-gate artifact (checkbox + owner + date, not a mental note). |

---

### LAYER 5 — Operations / Maintenance (post-launch)

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 19 | Doc regeneration is triggered by any live-code correction, same session — not queued as a future task. | GitHub repo already exists as the mechanism — the fix here is process timing, not new infrastructure. |
| 20 | Reconcile every open "unreconciled" number (node counts, discrepant docs) before it's cited again as fact anywhere — including your own recollection. | Structured issue/incident tracker — a NocoDB table logging bugs by agent/severity/status, separate from the HOW docs' "known bugs" sections, which currently function as an ad hoc, unstructured version of this. |

---

**Full inventory: 24 process items + agent-creation subsection, across 5 layers, each paired with its tool/material.**

**Standing gap confirmed across all three source lists:** Layers 1, 2, and 5 have partial tooling already in place (Schema Registry, GitHub repo, NocoDB) that mainly needs earlier timing and stricter discipline — not new infrastructure. Layers 3 and 4 have no tooling or artifact at all: no staging environment, no regression suite, no release-gate document, no isolated agent-test harness. Those are where the next project requires genuinely new material, not process correction alone.
