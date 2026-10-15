# 00 — One-Page Cheat Sheet (read this 10 minutes before the interview)

**Mindset**: Modernization = make change cheap and safe again. Technology is a means. Reduce risk with
small, reversible, production-validated steps. Tie everything to business value.

**Strategy**: Incremental > Big Bang. Strangler Fig + Anti-Corruption Layer + feature flags + parallel run.
Per-area strategy: Retire / Retain / Replace (buy) / Rebuild — guided by core vs supporting vs generic.

**Delphi reality**: VCL forms with rules in `OnClick`/`BeforePost`, data-aware controls, global
DataModules, logic in stored procedures/triggers, 2-tier client-server, reports, Windows-only.

**Delphi traps**: `Currency`/`Extended` → `decimal`; rounding modes; `TDateTime` = OLE date
(`DateTime.FromOADate`); 1-based strings; `Pos`=0 vs `IndexOf`=-1; ANSI/Win1252 encoding; NULL read as
`''`/`0`; regional date formats; hidden triggers; event-order side effects.

**Hybrid for desktop**: Delphi calls the new .NET API for migrated rules; new screens in web opened
from Delphi menu (WebView2/`TEdgeBrowser`); migrate module by module; new features only in .NET.

**Target architecture**: .NET 10 (LTS) **modular monolith** — modules = bounded contexts, schema per
module, public contracts, events via outbox; Clean Architecture or vertical slices inside; CQRS-light
(commands through domain, queries via Dapper/views). Extract microservices only with a real driver.

**Clean Architecture**: dependencies point inward; Domain ← Application (ports) ← Infrastructure
(adapters) / API. Opposite of Delphi smart UI. DB and UI become replaceable details.

**DDD**: Ubiquitous language, subdomains, **bounded contexts = units of migration**, context map,
**ACL to legacy**, Event Storming for discovery. Tactical: entities, value objects, small aggregates,
domain events, repositories.

**Messaging**: commands vs events; queues vs topics; at-least-once → **idempotent consumers**;
**Transactional Outbox** solves dual write; DLQ; CDC (Debezium/triggers) to get events from legacy
without changing Delphi; eventual consistency; sagas with compensation. RabbitMQ / Azure Service Bus /
Kafka; MassTransit / Wolverine / NServiceBus.

**Data**: one writer per data at any moment; shared DB behind ACL → ownership transfer → one-way sync;
expand/contract; reconciliation; profile data quality early; rehearse cut-overs.

**Safety net**: characterization tests, golden master (Verify), parallel run, Testcontainers,
architecture tests (NetArchTest), OpenTelemetry, correlation IDs, CI/CD from day 1, feature flags.

**Microservices tax**: network failures, eventual consistency, ops, observability, distributed monolith risk.

**Books**: Feathers (*Working Effectively with Legacy Code*), Newman (*Monolith to Microservices*),
Evans/Vernon (DDD), Bellotti (*Kill It with Fire*), Fowler (StranglerFig, MonolithFirst).

**Closing line**: *"I'd start by understanding the domain and protecting current behavior with tests,
then migrate incrementally into a well-bounded .NET modular monolith, delivering value at every step and
keeping each step reversible."*
