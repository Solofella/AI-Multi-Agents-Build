September 9, 2026

---

## Multi-Agent Project Checklist — v2 (Merged + Specialist Audit)

**Solofella LLC organizational asset. VRYOH Intelligence is Case Study #1 this checklist was derived from — applies to any future AI multi-agent system Solofella builds.**

---

### LAYER 1 — Systems/Solutions Architecture (pre-build)

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 1 | Write the Field Traceability Map before Node 1 exists. Every field required at any output must be declared at source before build begins. | Diagramming tool producing an actual visual map (field origin → transformation → destination per agent) — not prose. |
| 2 | Define the source-of-truth rule in writing before building. Live workflow JSON is truth. Docs are generated *from* it, never written in parallel. | Schema Registry as a Phase-0 deliverable, existing from day one. |
| 3 | Decide minimum viable agent count before ideal agent count. For each proposed agent, write one sentence justifying why the output can't be produced without it. | Decision log (markdown or NocoDB table). |
| 4 | Name your non-negotiable product principle and decide, in writing, which specific node structurally enforces it — not "the prompt says so." | Same decision log, recording confirmed *working* enforcement. |
| **25** | **Data retention and deletion policy decided before the first record is stored** — how long guest reviews, guest names, and drafted responses persist; archive/purge process defined. | **Written retention policy document, referenced in the Field Traceability Map.** |
| **26** | **Multi-tenancy / data isolation guarantee, written down and tested** — the technical mechanism that guarantees Client A's data can never appear in Client B's output. | **A documented isolation test: attempt cross-client data leakage deliberately in staging and confirm it's blocked, not assumed.** |
| **27** | **Model/vendor dependency risk assessed before build** — what happens on LLM vendor outage, deprecation, or pricing change during a scheduled run. | **Fallback/degradation policy document — even if the answer is "fail loudly and alert," it must be a decision, not a silent gap.** |

---

### LAYER 2 — Engineering / Build Discipline

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 5 | One shared validation/gate library, not per-agent reimplementation. | Version-controlled shared snippet/module repo every Code Node references. |
| 6 | Every list-fetching node gets a scalability answer at build time — no hardcoded limits without a pagination/filtering plan. | Enforced via the Layer 1 decision log. |
| 7 | Every bulk write gets a post-write verification step decided at build time. | Same decision log, plus issue-tracker entry if deferred. |
| 8 | Error-handling policy (retryOnFail/onError) decided per node type before the first instance, applied uniformly. | Same decision log. |
| 9 | One node at a time, confirm before proceeding. | Process only. |
| 10 | After every bug fix, same-session audit of every structurally similar node for the same pattern. | Node inventory/registry per agent. |
| 11 | No fabricated or placeholder metric ever ships to a client-reachable path, even "known issue, accepted." | Staging/test environment (Layer 3) to catch this pre-production. |
| 12 | Full replacement code blocks, no partial diffs. | Process only. |
| 21 | Decide the build environment per agent before starting: platform, LLM API, model, and rationale. | Documented per-agent spec: platform + model + rationale. |
| 22 | Build and test each agent in isolation before wiring it into the pipeline. | Isolated test harness — run one agent alone, feed sample input, check output. |
| 23 | Define the prompt-engineering standard before writing the first system prompt (fewer, broader principles over exhaustive rule-lists). | Written standing guideline, in place before the first prompt is drafted. |
| 24 | Decide the "unit of an agent" explicitly — one LLM call, one Code Node chain, or a multi-step sub-workflow. | Definition document, decided up front. |
| **28** | **Secrets/credential handling protocol, written and enforced — not reactive.** Credentials never typed directly into chat or code; vault/password-manager reference only. | **Documented protocol + a credential-handling tool (password manager, secrets vault) integrated into the build workflow from day one.** |
| **29** | **Prompt injection / adversarial input handling for any agent ingesting external, uncontrolled text** (e.g., public review platforms). | **Documented threat model for the ingestion agent + input sanitization/detection layer before that agent's output reaches downstream generation stages.** |
| **30** | **Cost ceiling / runaway-spend circuit breaker, per agent and per client.** | **Hard spend cap enforced in code (not just monitored), triggering a halt-and-alert rather than silent continued spend.** |

---

### LAYER 3 — QA / Verification (runs continuously, parallel to Layers 1–2)

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 13 | State source for every claim: observation / user-provided / training data / guess / unknown. | Discipline only — start day one. |
| 14 | Independent live-code audit before pilot launch, not after. | Staging/test environment separate from production. |
| 15 | Confidence markers (✅/⚠️/❌/🚫) on every architectural claim. | Discipline only. |
| — | Regression testing after any change. | Fixed sample-input set run through the full pipeline with expected-output diffing. |
| **31** | **Adversarial/edge-case test set, not just happy-path regression** — empty input, wrong-language input, profanity/threats, extreme length, missing fields, duplicates. | **Edge-case test library, built before launch, expanded every time a new edge case surfaces in production.** |
| **32** | **Ongoing output-quality sampling** — structured human review of a random % of live output post-launch, not just a one-time pre-launch audit. | **A sampling process and tracked quality score over time, distinct from raw approval/rejection rate.** |

---

### LAYER 4 — Release Management / Launch Gate

*(Confirmed structurally absent from VRYOH — no tooling or artifact existed at all.)*

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 16 | A launch-blocking checklist, separate from the general backlog, fully closed before go-live. | Standalone, versioned checklist artifact. |
| 17 | Data-integrity and governance-gap fixes explicitly ranked above new-feature work until pilot is stable. | Written ranking policy inside the release-gate artifact. |
| 18 | Confirm the node enforcing the product's core differentiator is verified *working*, not just built, before calling anything "pilot ready." | Sign-off field inside the release-gate artifact (checkbox + owner + date). |
| **33** | **Legal/compliance sign-off as an explicit gate item** — third-party platform ToS compliance for any scraped/ingested data source, privacy-notice obligations for guest-identifiable content. | **A compliance checklist item with actual sign-off, not implied by the technical build.** |
| **34** | **Written and tested rollback plan before go-live** — the actual mechanism to revert to last-known-good if a newly deployed agent version produces bad output in production. | **A documented workflow-versioning/rollback procedure, tested at least once before the first pilot client goes live.** |

---

### LAYER 5 — Operations / Maintenance (post-launch)

| # | Process Item | Tool / Material Needed |
|---|---|---|
| 19 | Doc regeneration triggered by any live-code correction, same session. | GitHub repo as mechanism — fix is timing, not infrastructure. |
| 20 | Reconcile every "unreconciled" open number before it's cited again as fact anywhere. | Structured issue/incident tracker (e.g., NocoDB table by agent/severity/status). |
| **35** | **Real-time alerting on error, not just logging.** Someone is notified when an Error Log field is written, not required to manually check. | **A basic alert integration (e.g., notification on Error Log write) — even minimal, beats requiring manual checks at growing scale.** |
| **36** | **Client-facing incident communication plan** for when a client-facing output was already wrong (e.g., a fabricated metric already sent). | **A documented correction/notification process, separate from the technical fix.** |

---

### LAYER 6 — Human & Organizational Factors *(new — did not exist in the original structure)*

| # | Process Item | Tool / Material Needed |
|---|---|---|
| **37** | **Bandwidth/single-point-of-failure risk named as an explicit go/no-go factor**, not just an acknowledged constraint — a hard trigger (e.g., "delay onboarding if headcount doesn't scale with locations"). | **A written trigger condition tied to a real metric (locations live, concurrent workstreams), checked at each onboarding decision.** |
| **38** | **Explicit escalation/decision-authority protocol with an aging limit for open items** — no item stays "pending decision" indefinitely. | **A tracked open-items list with age, forcing a decision point after N weeks.** |
| **39** | **Knowledge-continuity plan for single-operator risk** — a runbook a second person (hire, contractor, or a cold-start AI session) could use without a multi-session archaeology exercise. | **A living runbook/onboarding document, distinct from chat history and memory, that exists independent of any one person's availability.** |

---

**Full inventory: 39 process items across 6 layers, each paired with its tool/material.**
