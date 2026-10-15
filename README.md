# system-modernization

This a study project about system modernization — focused on migrating a **legacy Delphi system to
modern .NET**, and how architecture concepts (Clean Architecture, DDD, Modular Monolith,
Microservices, Messaging) support that process.

## Docs

| # | Document | What's inside |
|---|---|---|
| 00 | [Cheat Sheet](docs/00-cheat-sheet.md) | One-page summary for last-minute review |
| 01 | [Modernization Fundamentals](docs/01-modernization-fundamentals.md) | Drivers, the "R's", big bang vs incremental, Strangler Fig, ACL, Branch by Abstraction, parallel run, roadmap, risks |
| 02 | [Delphi → .NET](docs/02-delphi-to-dotnet.md) | Anatomy of a Delphi system, concept mapping, migration traps, hybrid strategies, assessment checklist |
| 03 | [Clean Architecture](docs/03-clean-architecture.md) | Layers, dependency rule, .NET layout, vertical slices, CQRS, how it helps migration |
| 04 | [Domain-Driven Design](docs/04-ddd.md) | Strategic (bounded contexts, context map, ACL, Event Storming) & tactical DDD |
| 05 | [Modular Monolith](docs/05-modular-monolith.md) | Why it's the recommended target, implementation in .NET, data isolation, boundary tests |
| 06 | [Microservices](docs/06-microservices.md) | Benefits, costs, when (not) to use, sagas, gateways, comparison table |
| 07 | [Messaging](docs/07-messaging.md) | Commands/events, delivery guarantees, outbox, idempotency, CDC, legacy integration |
| 08 | [Data Migration](docs/08-data-migration.md) | Coexistence strategies, ownership, sync, expand/contract, cut-over checklist |
| 09 | [Testing, Quality & DevOps](docs/09-testing-quality-devops.md) | Characterization tests, golden master, observability, CI/CD, security |
| 10 | [Modern .NET Stack](docs/10-modern-dotnet-stack.md) | .NET 10 and the libraries/tools for the target platform |
| 11 | [Interview Q&A](docs/11-interview-questions.md) | Likely questions with model answers + questions to ask |

## Suggested reading order (short on time)

1. `00-cheat-sheet.md`
2. `02-delphi-to-dotnet.md` (most job-specific)
3. `11-interview-questions.md`
4. `01` → `05` → `07` for depth, the rest as reference.
