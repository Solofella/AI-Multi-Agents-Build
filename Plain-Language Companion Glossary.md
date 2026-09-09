SOLOFELLA LLC
Plain-Language Companion Glossary
for the Multi-Agent Project Checklist v2
Chat #109 · September 9, 2026
Built for: Miguel — solo developer, VRYOH Intelligence / Solofella LLC
How to use this document
This is not a dictionary. It's a learning companion, ordered the same way the checklist is — by layer, in the sequence you'd actually meet each concept while building. Every term includes three things: a plain-language explanation with no jargon, a note on where that exact concept already showed up somewhere in VRYOH (even if you didn't have a name for it at the time), and why it matters enough to be on the checklist at all.
Read it once straight through to build vocabulary. After that, use it as a lookup: when the checklist says a term you don't fully own yet, find it here before moving on.

LAYER 1 — Systems / Solutions Architecture
The thinking you do before any workflow node exists

Field Traceability Map
Plain-language: A diagram showing, for every piece of data you need in a final output, exactly where it first entered the system and every place it got changed along the way — like tracing a single ingredient back through every step of a recipe to know exactly which stage introduced it.
In VRYOH: You never built this as a formal diagram — you have it scattered across memory as prose (e.g., "ALA Record ID travels through all 6 agent NocoDB tables as common traceability field"). That sentence IS a fragment of a Field Traceability Map. The checklist wants that made visual and complete before building starts.
Why it matters: Without it, you discover missing fields the hard way — mid-build, when an agent needs data that was never captured upstream. This happened repeatedly in VRYOH (Phase 3 EIP→RDA signal enrichment had to retrofit fields that should have been planned from the start).

Source of Truth
Plain-language: The one place everyone agrees is "what's actually real" when two records disagree. If your notes say one thing and the live system does another, the source of truth wins, automatically, no debate.
In VRYOH: Your Instruction 6 ("Source Hierarchy for Verification") IS a source-of-truth rule — it says live n8n JSON beats any HOW document. You already invented this concept before knowing its name.
Why it matters: Every documented doc/live mismatch in VRYOH (BRA 19 vs 24 nodes, SIA 17 vs 18) happened because there wasn't a firm source-of-truth rule from day one — docs and reality drifted apart independently.

Schema
Plain-language: The blueprint of a database table: what columns exist, what type of data each holds (text, number, date), and what each column's internal ID is.
In VRYOH: Every NocoDB table ID and field ID in your memory (e.g., EIP's "Enriched Emotion Tag c1aphddit5x0ylc") is a schema entry. You've been managing schema correctly by instinct — the checklist just wants it centralized as a Schema Registry from day one instead of assembled gradually.
Why it matters: A schema that lives only in scattered chat history is fragile — you found this out when node counts and field lists needed reconstruction from memory rather than being readable in one place.

Agent (in this context)
Plain-language: One discrete step or worker in your pipeline that does one job — takes some input, does something to it (often using an LLM), and passes output to the next step. Not a robot; think of it as one employee on an assembly line who only does one task.
In VRYOH: Your 8 agents (ALA through MRA) are exactly this. But your own memory admits the definition of "agent" was inconsistent across them — ALA is mostly deterministic code with no LLM call at all, while BRA is an LLM call plus a template library plus a governance check. Both got called "an agent."
Why it matters: If "agent" means different things in different places, it becomes hard to estimate build time or compare complexity across agents — which is exactly the confusion Checklist item 24 is trying to prevent next time.

Minimum Viable Agent Count
Plain-language: The smallest number of pipeline steps you actually need to produce your two real outputs — as opposed to the number of steps that feels thorough or feels like "doing it properly."
In VRYOH: This is the exact question raised earlier in this conversation: only 2 of your 8 agents (BRA/RDA for drafting, MRA for reporting) are strictly required to produce VRYOH's two outputs. The other 5 exist for signal-quality refinement — a deliberate choice, not a mistake, but one that should be named explicitly next time, not discovered in hindsight.
Why it matters: Knowing which agents are load-bearing versus refinement lets you make honest tradeoffs between speed-to-market and signal quality, instead of building all of it by default.

Governance Gate / Checkpoint
Plain-language: A hard stop built into the code itself — not just an instruction you hope the AI follows — that blocks bad output from moving forward unless a specific condition is met.
In VRYOH: Your "detect, never prescribe" principle needed exactly this. BRA's Step 9b (the dignity/trust check before Step 10) was meant to be this gate — but you've documented that its condition checks the wrong field, so it rarely fires correctly. The checklist wants you to name the enforcing node AND verify it actually works, not just that it exists.
Why it matters: A governance rule that lives only in a prompt ("please don't do X") can be argued around by the LLM under enough context pressure. A governance rule enforced by code logic cannot — this is the core reason multi-agent pipelines are used for compliance-sensitive products at all.

Data Retention Policy
Plain-language: A written rule for how long you keep data, and what happens to it after — delete it, archive it, anonymize it. Not a default the software gives you; a decision you make.
In VRYOH: VRYOH stores guest names and review text indefinitely with no documented retention rule anywhere in memory. This is a genuine open gap, not something already solved and just unnamed.
Why it matters: Hospitality guests didn't opt into indefinite storage of their complaints. Beyond ethics, most US states and the EU have real legal requirements here — this is one of the few checklist items that's a legal risk, not just a technical one.

Multi-Tenancy / Data Isolation
Plain-language: The guarantee that Client A's data can never leak into Client B's report, dashboard, or email — even by accident, even under a bug.
In VRYOH: Every one of your queries currently filters by Client ID correctly, as far as documented — but there's no test that deliberately TRIES to break this and confirms it can't. It's assumed correct because it's coded correctly, not proven correct under adversarial testing.
Why it matters: As you scale past AJI-001 to 10 real client locations, a single missed Client ID filter in any of 8 agents becomes a real data-privacy incident, not just a bug.

Vendor Dependency Risk
Plain-language: What breaks in your system if a company you rely on (OpenAI, Anthropic) changes prices, has an outage, or discontinues a model you're using — and whether you have a backup plan.
In VRYOH: VRYOH has zero documented fallback if gpt-5.2 or claude-sonnet-4-6 becomes unavailable mid-scheduled-run. A scheduled SIA or MRA run failing silently during a vendor outage is a real, currently-unprotected scenario.
Why it matters: You're on a subscription-model business now, with a paying client depending on daily/weekly reports arriving. A vendor outage isn't hypothetical risk anymore — it has a customer on the other end.

LAYER 2 — Engineering / Build Discipline
How you actually build each node, and the shared rules that keep every agent consistent

Shared Validation / Gate Library
Plain-language: Instead of writing the same check five separate times in five separate agents, you write it once in one shared file, and every agent points to that one copy.
In VRYOH: This is the single most costly gap in VRYOH's build history. The trust/dignity check bug appears independently in both HSI Step 16 and BRA Step 9b — same wrong-field mistake, made twice, because the logic was written twice instead of shared once.
Why it matters: Every time shared logic gets rewritten instead of reused, you get a new chance to make the same mistake differently. One shared, well-tested copy means fixing a bug once fixes it everywhere it's used.
Pagination / Scalability Limit
Plain-language: When you fetch a list of records from a database, most systems cap how many they return in one request (e.g., "give me up to 1,000 rows"). Pagination means asking for the next batch after that, in a loop, until you've truly got everything — instead of silently only ever seeing the first batch.
In VRYOH: SIA's Steps 5 and 7 fetch with a hardcoded limit=1000 and no pagination — meaning once either table passes 1,000 rows, SIA will silently process incomplete or wrong data with zero error message. This is documented in memory as an unresolved "ticking time bomb."
Why it matters: This is the clearest example in the whole project of a problem that's invisible right up until the moment it silently breaks production — no warning, no crash, just quietly wrong numbers.
Post-Write Verification
Plain-language: After you tell a database "save this," checking that it actually got saved successfully — instead of assuming it worked because no error appeared on screen.
In VRYOH: Most of your agents do this correctly (a "Capture Record ID" step that throws an error if the save didn't return an expected ID). SIA's Step 14 bulk write is the one confirmed exception — it has no such check, so a silent partial failure there would currently go completely unnoticed.
Why it matters: Silent failures are the most dangerous kind, because nothing alerts you — the system looks fine right up until you go looking for data that was never actually written.
Error Handling Policy (retryOnFail / onError)
Plain-language: A decision, applied consistently, for what a node does when it fails: try again automatically a few times, or stop and report the failure immediately, or something else.
In VRYOH: Documented as missing on multiple nodes across the pipeline (BRA Step 19, at least 3-4 other occurrences). Present on some nodes, absent on others — meaning the current behavior is accidental, not decided.
Why it matters: A node that fails silently and doesn't retry can quietly stop a whole client's daily report from generating, with no signal that anything went wrong until someone notices the report never arrived.
Credentials / Secrets Handling
Plain-language: The rule for how passwords, API keys, and tokens are ever entered or stored — the safe answer is: never typed directly anywhere they could be seen or logged, only referenced from a secure vault.
In VRYOH: Your own memory documents real OAuth credentials being pasted into this chat multiple times during the Google Business Profile integration. Each time, it was caught and flagged after the fact — reactive, not preventive.
Why it matters: A leaked credential can grant an attacker access to your Google Cloud project, your email sending service, or your database — the damage from one leaked key can be far larger than any single bug in your pipeline logic.
Prompt Injection
Plain-language: A trick where someone hides instructions inside normal-looking text (like a guest review) hoping an AI reading it will follow those hidden instructions instead of doing its actual job.
In VRYOH: ALA ingests guest review text directly from public review platforms — completely open, unmoderated text — and that text eventually flows into EIP, BRA, and RDA's LLM calls. VRYOH currently has no documented defense against a review containing a hidden instruction aimed at those downstream LLM calls.
Why it matters: This is not theoretical — public review fields are one of the most common real-world attack surfaces for any pipeline that reads user-submitted text into an LLM. It costs little to add basic detection; it costs a lot if a malicious review manipulates a client-facing response.
Cost Ceiling / Circuit Breaker
Plain-language: A hard limit coded into the system that stops spending money once a threshold is hit, rather than a limit you only find out you exceeded when the bill arrives.
In VRYOH: You track per-record cost (EIP ~$0.0072/record) but there's no coded cap on total daily or per-client spend. A stuck loop or an unexpected review-volume spike currently has no financial backstop.
Why it matters: At 10 locations and growing, an unnoticed retry loop or malformed batch could run up real cost before anyone catches it manually.
Isolated Test Harness
Plain-language: A way to run just ONE agent by itself, feed it a sample input, and see what it produces — without needing the entire 8-agent chain live and connected.
In VRYOH: This does not exist in VRYOH. Every test so far has either been the full live pipeline or manual review of JSON in chat. There's no quick way to check "does BRA alone still work correctly" without running the whole thing.
Why it matters: Testing one small piece at a time is dramatically faster and safer than only ever testing the whole machine — problems get caught closer to where they're introduced.

LAYER 3 — QA / Verification
Proving the system works — not just that it was built

Regression Testing
Plain-language: Running the same fixed set of test inputs through your system every time you make a change, to confirm the change didn't accidentally break something that used to work.
In VRYOH: Does not exist in VRYOH. There is no fixed set of sample reviews you run after every change to confirm nothing broke — each verification has been a manual, one-off audit.
Why it matters: Without this, every fix carries hidden risk of silently breaking something else — and you'd have no fast way to find out until a real client review surfaces the new problem.
Edge Case
Plain-language: An unusual or extreme input that's technically valid but easy to forget to test for — an empty review, a review with no star rating, a review in the wrong language, a duplicate submission.
In VRYOH: Nearly every confirmed bug in VRYOH's history came from an edge case that wasn't tested up front: the Spanish language-mixing failure, the same-day same-location star-only review dedup collision, the missing Signal Type values in tier classification.
Why it matters: Happy-path testing (does it work when everything is normal) is necessary but not sufficient — production data is messier than test data, always.
Staging Environment
Plain-language: A separate, private copy of your entire system where you can test changes safely, completely isolated from the live system your real client depends on.
In VRYOH: Does not exist for VRYOH. All node changes appear to happen directly against the production n8n instance serving AJI-001's live data — meaning every edit carries live-system risk.
Why it matters: This is flagged as the single largest missing piece of infrastructure in the whole project. Without it, testing and production are the same environment, which means every experiment has real consequences.
Output Quality Sampling
Plain-language: Regularly and deliberately reviewing a random slice of what your system produces in live use — not just checking it once before launch, but on an ongoing schedule.
In VRYOH: RDA's quality target is ~90%, but there's no ongoing, structured process to measure that number over time — only the informal signal of approval/rejection rates in the Google Sheet, which isn't the same as a deliberate quality audit.
Why it matters: A system can silently drift in quality over weeks without anyone noticing if nobody is sampling and grading real output on a schedule.

LAYER 4 — Release Management / Launch Gate
The formal stop-and-check before anything goes live for a real client

Launch-Blocking Checklist
Plain-language: A separate, short list of must-fix items that specifically block a launch — distinct from your general to-do list, where items can sit indefinitely without urgency.
In VRYOH: Never existed for AJI-001's launch. Known risks (SIA's scalability bomb, RDA's governance gap, MRA's fabricated metric) sat mixed into the same general backlog as feature requests like the dashboard evaluation — with no item having launch-blocking authority.
Why it matters: Mixing must-fix-before-launch items with nice-to-have feature ideas means nothing forces a stop. This was the single structurally missing piece across the entire project.
Release Gate
Plain-language: The formal decision point where someone explicitly says "yes, this is ready to go live" — with a checklist that has to be fully closed first, not just mostly done.
In VRYOH: This role didn't exist as a distinct step in VRYOH's process. Build discipline, verification protocol, and QA all existed in some form — but nothing sat between "built" and "live" with authority to say no.
Why it matters: Every other discipline in the project (build, QA, architecture) can be done reasonably well and a project can still launch with real risk if nothing has the job of saying "not yet."
Compliance Sign-off
Plain-language: Confirming, explicitly and in writing, that you're not violating the terms of service of a platform you're pulling data from, or the privacy expectations of the people whose data you're processing.
In VRYOH: Nothing in VRYOH's documentation addresses Google/Yelp/OpenTable terms-of-service compliance for review ingestion, or a privacy notice for guests whose review data and names are being processed.
Why it matters: This is a legal exposure category, not a technical one — the kind of gap that doesn't show up as a bug, it shows up as a letter from a platform or a regulator.
Rollback Plan
Plain-language: A tested, working way to undo a recent change and go back to the last version that was known to work correctly, if a new change breaks something in production.
In VRYOH: No documented workflow-versioning or rollback strategy exists for n8n. Since workflows are edited live in the same instance serving AJI-001, there's currently no confirmed way to quickly undo a bad change.
Why it matters: Without this, every deployment is one-way — if something goes wrong, fixing it forward under pressure is riskier than reverting to what already worked.

LAYER 5 — Operations / Maintenance
Keeping the system healthy after it's live

Doc/Live Drift
Plain-language: When your written documentation and the actual running system slowly stop matching each other, because one gets updated and the other doesn't.
In VRYOH: Confirmed multiple times: BRA's doc said 19 nodes, the live system had 24. SIA's doc said 17, live had 18, with two of three trigger times also wrong in the doc.
Why it matters: Documentation you can't trust is worse than no documentation, because it actively misleads you into confident wrong conclusions instead of prompting you to go check.
Alerting vs. Logging
Plain-language: Logging means the system writes down that an error happened somewhere you could look. Alerting means someone gets actively notified the moment it happens — a message, an email, a ping — without needing to go looking.
In VRYOH: VRYOH has Error Log fields on every agent's NocoDB table — that's logging. It has no alerting: nobody gets notified when one of those fields gets written to. Someone has to manually go check.
Why it matters: At your current one-person scale with 10 locations coming, a failure that requires manual checking to discover could run for days unnoticed. A basic alert (even just an email) closes this gap cheaply.
Issue / Incident Tracker
Plain-language: A single, structured place where every known bug is logged with its severity and status — separate from your architecture documentation, so bugs don't get buried inside doc prose.
In VRYOH: Currently, known bugs live inside each agent's HOW document under "known bugs" sections — functional, but unstructured, and not filterable by severity or status across all 8 agents at once.
Why it matters: As bug count grows across more agents and more locations, an unstructured list becomes hard to prioritize from. A real tracker lets you sort by severity and see what's actually most urgent.

LAYER 6 — Human & Organizational Factors
The risks that live in how the project is run, not in the code

Single Point of Failure (Bandwidth Risk)
Plain-language: When one person is the only one who can do a critical job — meaning if that person is unavailable, overloaded, or leaves, the whole system's progress stops.
In VRYOH: Explicitly documented in your own memory: 5 concurrent workstreams (production support, new onboardings, gap-closure, RDA quality build, new architecture) on one person. Named as a real constraint, but with no hard trigger attached to it.
Why it matters: Naming a risk isn't the same as managing it. A trigger like "pause new client onboarding if concurrent workstreams exceed X" turns an acknowledged risk into an actual decision rule.
Decision Aging / Escalation
Plain-language: A rule that says any open decision that's been sitting unresolved for too long automatically gets forced to a conclusion, instead of being allowed to sit indefinitely.
In VRYOH: SIA alone has 8 open items pending your decision, some tracked across multiple sessions without resolution. The Diagnose-Only Rule correctly stops Claude from pushing you toward a fix — but nothing currently forces the decision itself to happen by a deadline.
Why it matters: Open items that never age out tend to just stay open forever, quietly accumulating as unaddressed risk in a live production system.
Knowledge Continuity / Runbook
Plain-language: A document that lets someone else (a hire, a contractor, or even a fresh AI session with no prior context) understand and work on your system without you personally explaining everything from scratch.
In VRYOH: Right now, VRYOH's full architecture understanding exists across many chat sessions, Claude's memory, and your own head. There's no single runbook a new person could pick up and actually use — this whole multi-session conversation has effectively been an archaeology exercise to reconstruct that knowledge.
Why it matters: At single-operator scale, this is a real business-continuity risk — if you're unavailable for any reason, there's currently no fast way for anyone else to pick up where you left off.

Quick-Reference Index — All Terms, Alphabetical
Use this to jump straight to a term without hunting through layers.

●	Agent (in this context) — Layer 1
●	Alerting vs. Logging — Layer 5
●	Compliance Sign-off — Layer 4
●	Cost Ceiling / Circuit Breaker — Layer 2
●	Credentials / Secrets Handling — Layer 2
●	Data Retention Policy — Layer 1
●	Decision Aging / Escalation — Layer 6
●	Doc/Live Drift — Layer 5
●	Edge Case — Layer 3
●	Error Handling Policy — Layer 2
●	Field Traceability Map — Layer 1
●	Governance Gate / Checkpoint — Layer 1
●	Isolated Test Harness — Layer 2
●	Issue / Incident Tracker — Layer 5
●	Knowledge Continuity / Runbook — Layer 6
●	Launch-Blocking Checklist — Layer 4
●	Minimum Viable Agent Count — Layer 1
●	Multi-Tenancy / Data Isolation — Layer 1
●	Output Quality Sampling — Layer 3
●	Pagination / Scalability Limit — Layer 2
●	Post-Write Verification — Layer 2
●	Prompt Injection — Layer 2
●	Regression Testing — Layer 3
●	Release Gate — Layer 4
●	Rollback Plan — Layer 4
●	Schema — Layer 1
●	Shared Validation / Gate Library — Layer 2
●	Single Point of Failure (Bandwidth Risk) — Layer 6
●	Source of Truth — Layer 1
●	Staging Environment — Layer 3
●	Vendor Dependency Risk — Layer 1

Solofella LLC — internal reference document. Companion to the Multi-Agent Project Checklist v2. Update this file whenever a new term earns a place on the checklist.
