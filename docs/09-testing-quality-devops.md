# 09 — Testing, Quality, Observability & DevOps in Modernization

Without a safety net, modernization is gambling. The new system must be **provably** doing what the
old one did (or intentionally different).

## Testing legacy behavior

### Characterization tests (Michael Feathers)

Tests that capture **what the system currently does**, right or wrong:
1. Pick a piece of behavior (e.g. "calculate order total with discounts and taxes").
2. Feed real inputs, record the legacy outputs.
3. The new implementation must produce the same outputs. Any difference is either a bug in the new
   code or a **bug discovered in the legacy** → business decides.

### Golden Master / Approval testing

Run many inputs (e.g. 10,000 real orders from production) through the legacy, save the outputs as
the "golden master", compare the new system's outputs. In .NET: **Verify** (snapshot/approval testing),
ApprovalTests.

### Parallel run

Both implementations run in production on the same input; differences are logged; only legacy output is
used until the difference rate is ~0. (Scientist pattern.)

### Test pyramid for the new system

| Level | Tools (.NET) | Focus |
|---|---|---|
| Unit | xUnit / NUnit / MSTest, FluentAssertions/Shouldly, NSubstitute/Moq | Domain rules, value objects, handlers |
| Integration | `WebApplicationFactory`, **Testcontainers** (real SQL Server/Postgres/RabbitMQ in Docker) | API + DB + messaging |
| Architecture | NetArchTest, ArchUnitNET | Module boundaries, layer dependencies |
| Contract | Pact, schema checks | Contracts between modules/services and with legacy |
| E2E / UI | Playwright | Critical user journeys |
| Performance | k6, NBomber, JMeter | Compare with legacy baseline |

## Observability

You can't safely run a hybrid system without seeing it.

- **Structured logging**: `ILogger` + Serilog → Seq / Elastic / Grafana Loki / Application Insights.
- **Metrics & traces**: **OpenTelemetry** (the standard in .NET), exported to Prometheus/Grafana, Jaeger/Tempo, Azure Monitor.
- **Correlation IDs** across Delphi → API → broker → consumers (Delphi can send a header too).
- **Health checks** and alerts (including DLQ size, outbox backlog, sync lag).
- **Business metrics**: % of traffic on new vs legacy, difference rate in parallel runs, errors per module.

## DevOps / Delivery

- **Version control** for everything (including Delphi code and DB scripts — sometimes the legacy isn't even in git!).
- **CI/CD** from day 1 for the new system: build, tests, static analysis (analyzers, SonarQube), security scans, deploy (GitHub Actions / Azure DevOps / GitLab CI).
- If possible, also an **automated build of the Delphi app** (MSBuild works with `.dproj`), so legacy changes needed for integration are safe.
- **Containers** (Docker) for the new system; **Infrastructure as Code** (Bicep/Terraform) if on cloud.
- **.NET Aspire** (now branded just *Aspire*) for local orchestration of API + DB + broker + dashboards in development.
- **Feature flags** for gradual rollout (`Microsoft.FeatureManagement`, LaunchDarkly, Unleash…).
- Deployment strategies: blue-green, canary (per customer/branch), rollback plan.

## Security improvements usually included

- Centralized **authentication/authorization** (OpenID Connect, Entra ID / Keycloak / Duende IdentityServer), replacing user tables with plaintext/weak passwords.
- No DB credentials on client machines (a big win of moving from 2-tier desktop to API).
- Secrets in a vault, HTTPS everywhere, audit logs, LGPD/GDPR concerns (personal data mapping, anonymization in test DBs).

## Definition of done for a migrated module

- [ ] Behavior covered by characterization/approval tests and validated by key users
- [ ] Data migrated and reconciled
- [ ] Legacy screens/code for the module disabled or removed
- [ ] Observability: logs, metrics, traces, alerts in place
- [ ] Documentation of discovered business rules + ADRs
- [ ] Performance at least as good as legacy baseline
