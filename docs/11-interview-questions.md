# 11 — Interview Questions & Model Answers

Use these as **talking points**, not scripts. Adapt with your own experience (real examples beat theory).

---

### 1. "How would you modernize our Delphi system?"

Structure the answer in phases (shows method):

1. **Understand before changing**: assess code, DB, integrations, reports; talk to users; find which
   features are used and what hurts most. Map business capabilities / bounded contexts (Event Storming).
2. **Define target & principles**: .NET 10 modular monolith, Clean Architecture/vertical slices inside
   modules, API-first, web UI (or desktop if justified), CI/CD and observability from day 1. Record ADRs.
3. **Safety net**: characterization tests and golden masters for critical rules, production data samples.
4. **Incremental migration (Strangler Fig)**: first a thin, low-risk but valuable slice to prove the
   architecture end to end. New features only in .NET. Delphi calls the new API for migrated rules;
   new screens open from the Delphi menu (WebView2) or a separate portal.
5. **Data**: shared DB behind an anti-corruption layer at first, then ownership transfer per module,
   one-direction sync via outbox/CDC, reconciliation.
6. **Decommission** legacy parts module by module; measure progress.

Close with: *"The key is reducing risk: small steps, each in production, each reversible."*

### 2. "Why not rewrite everything from scratch?"

Long time without value, moving target, hidden rules discovered late, risky big cut-over. Incremental
migration delivers value continuously and validates with real users. A full rewrite only makes sense
for small systems or when coexistence is impossible — and even then, release in phases by user group/module.

### 3. "Microservices or monolith?"

Modular monolith first. Boundaries in a legacy are unknown and will be wrong at first; fixing them is
cheap in a monolith, expensive across services. Microservices bring distributed system costs (network,
eventual consistency, ops). Design modules to be extractable (own schema, events, contracts) and extract
when there is a real driver: independent scale, team autonomy, different deploy cadence.

### 4. "What is a Strangler Fig?"

Incrementally replacing a legacy system by routing functionality piece by piece to the new system
through a façade, until the legacy can be removed. In a desktop Delphi app, the façade is the Delphi app
itself calling the new API / opening new web screens, or migrating whole user groups/modules.

### 5. "What is an Anti-Corruption Layer and why use it?"

A translation layer between the new model and the legacy model (or any external system) so the legacy
naming, structure and assumptions don't leak into the new domain. Example: map `TB_CLI.FLG_ST='B'` to
`CustomerStatus.Blocked`. When the legacy dies, you delete the ACL and the domain doesn't change.

### 6. "How do you deal with business rules nobody documented?"

- Read code **and** the database (triggers/procedures).
- Event Storming / interviews with key users.
- Characterization tests with real data, golden master, parallel run.
- Document each discovered rule (as tests + short docs). Differences go to the business to decide
  (bug in legacy vs. rule to keep).

### 7. "Explain Clean Architecture."

Business rules in the center (Domain, Application), frameworks/UI/DB at the edges, dependencies point
inward, ports (interfaces) in the application, adapters in infrastructure. It's the opposite of Delphi
smart-UI: rules become testable and independent from UI/DB — so during migration the same use case can
serve the Delphi app and the new UI, and the DB adapter can point to legacy tables then to new ones.

### 8. "What is DDD and how does it help?"

Strategic: ubiquitous language, subdomains (core/supporting/generic), bounded contexts, context maps
→ decide what to rebuild, buy or keep, where to cut the legacy and in which order. Tactical: entities,
value objects, aggregates, domain events → rich, testable model for the complex core. Not everything
needs tactical DDD — CRUD modules stay simple.

### 9. "What is an aggregate?"

A consistency boundary: a cluster of entities/value objects changed together through a root that
enforces invariants. Keep them small, reference other aggregates by ID, one aggregate per transaction
ideally, eventual consistency between aggregates via domain events.

### 10. "How do you guarantee an event is published when data is saved?"

Transactional Outbox: save the business data and the event in the same DB transaction; a background
relay publishes it to the broker. Since it's at-least-once, consumers must be idempotent (inbox table /
natural idempotency).

### 11. "Commands vs Events?"

Command: intent, imperative, one handler, can be rejected (`ReserveStock`). Event: fact in the past,
zero or many subscribers, can't be rejected (`StockReserved`).

### 12. "How do you keep data consistent between legacy and new system?"

Single writer per piece of data at any moment; sync in one direction (CDC/outbox/triggers + ACL);
reconciliation jobs; avoid bidirectional sync. Start with the shared DB if it's simplest, with a plan to
transfer ownership.

### 13. "What's a saga?"

A long-running business transaction across modules/services as a sequence of local transactions, with
compensating actions on failure. Choreography (react to events) or orchestration (a coordinator/state
machine).

### 14. "Biggest risks in a Delphi → .NET migration?"

Hidden rules; numeric/date/encoding differences (Currency/Extended vs decimal, TDateTime, 1-based
strings, ANSI); reports underestimated; data quality; permanent hybrid phase; team knowledge gaps;
scope creep; user resistance (keyboard-heavy Delphi workflows vs new UI).

### 15. "How do you convince the business / prioritize?"

Tie each step to business outcomes (time to market, risk reduction, cost, new channels). Prioritize by
value × pain ÷ coupling/risk. Show progress continuously (each slice in production). Measure: lead time,
incidents, % migrated, legacy usage decreasing.

### 16. "How do you test the new system matches the old?"

Characterization tests, golden master/approval tests (Verify) with production samples, parallel run
comparing outputs, reconciliation of data, user acceptance with key users, feature flags for canary
rollout.

### 17. "What about the UI? Users love the Delphi screens."

Involve key users early; keep productivity features (keyboard shortcuts, fast grids, F-keys, Enter to
next field); migrate gradually; train; measure task time. Blazor or a SPA with a good grid component;
WinForms/WPF if desktop is truly needed.

### 18. "Which .NET version and why?"

.NET 10 (LTS until Nov 2028). New projects shouldn't start on .NET 8 (support ends Nov 2026) or .NET
Framework (Windows-only, maintenance mode).

### 19. "What about AI tools in the migration?"

Useful to accelerate: explaining legacy Pascal code, drafting translations of isolated units,
generating characterization test cases, documenting rules. But every output is verified by tests and
review — AI doesn't remove the need for the safety net; it makes building it faster.

### 20. "How would you structure the team?"

Mixed teams: people who know the legacy/business + .NET devs, pairing to transfer knowledge. A small
"platform/enabling" effort for CI/CD, observability, shared building blocks. Teams aligned to bounded
contexts (Conway's Law) as it grows.

---

## Questions YOU should ask the interviewer

- What's the size of the system (forms, LOC, tables) and the Delphi/DB versions?
- Is it a single installation or one per customer (on-prem product)? Is SaaS the goal?
- What's the main driver: hiring, web/mobile, cloud, performance, cost?
- Is there a target UI decided (web, desktop)? Cloud provider?
- Are there automated tests or CI for the legacy today?
- Is the legacy still receiving new features in parallel? (moving target)
- Team size and composition? Do the original developers still work there?
- Has a previous modernization attempt failed? Why? (very revealing!)
- How is success going to be measured?

## Your own story (prepare it — STAR format)

Prepare 1–2 real examples: **S**ituation, **T**ask, **A**ction, **R**esult. E.g. a refactoring of a
legacy area, a migration, a performance issue, a production incident caused by a hidden rule. Numbers
help: "reduced deploy time from X to Y", "migrated N screens", "zero incidents in cut-over".
