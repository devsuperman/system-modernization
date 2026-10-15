# 10 — The Modern .NET Stack (reference for the target platform)

Status as of **October 2026**:

- **.NET 10** — LTS (released Nov 2025, supported until Nov 2028). **The natural target today.**
- **.NET 8** — LTS, support ends Nov 2026 → don't start new projects on it.
- **.NET 11** — STS, expected Nov 2026.
- C# 14 ships with .NET 10.

"Modern .NET" (Core lineage) is cross-platform, open source, fast, container-friendly. Don't confuse with
**.NET Framework 4.8** (Windows-only, maintenance mode) — if the interviewer asks, migrating *to*
.NET Framework would be creating a new legacy.

## Building blocks

| Need | Options |
|---|---|
| Web API | ASP.NET Core (Controllers or **Minimal APIs**), OpenAPI (built-in `Microsoft.AspNetCore.OpenApi`, Scalar/Swagger UI) |
| Web UI | **Blazor** (Server / WebAssembly / Auto — C# end to end, good for teams coming from Delphi), or React/Angular SPA |
| Desktop UI | WinForms (closest to VCL), WPF, Avalonia (cross-platform), .NET MAUI |
| ORM / data | **EF Core** (writes, migrations), **Dapper** (fast SQL reads, reports) |
| Validation | FluentValidation, DataAnnotations |
| Mediator / CQRS | MediatR (commercial license since v13), Wolverine, `Mediator` (source-generated), or plain handler classes |
| Mapping | Manual mapping / extension methods, **Mapperly** (source generator); AutoMapper is now commercial |
| Messaging | MassTransit, NServiceBus, Wolverine, Rebus, raw broker SDKs |
| Background jobs | `BackgroundService` / Worker Service, Hangfire, Quartz.NET |
| Resilience | Polly v8, `Microsoft.Extensions.Http.Resilience` |
| Caching | `HybridCache` (.NET 9+), `IMemoryCache`, Redis (`IDistributedCache`) |
| Auth | ASP.NET Core Identity, OpenID Connect / JWT, Entra ID, Keycloak, Duende IdentityServer |
| Logging / telemetry | `ILogger`, Serilog, **OpenTelemetry** |
| Reverse proxy / gateway | **YARP** |
| Local orchestration | **Aspire** |
| Reports | QuestPDF, FastReport .NET, SSRS, DevExpress/Telerik |
| Testing | xUnit, NUnit, Testcontainers, Verify, NSubstitute, Playwright, NetArchTest |

## Key language/runtime features worth mentioning

- **Dependency Injection** built in (`Scoped`, `Transient`, `Singleton`) — replaces Delphi globals/DataModules.
- **async/await** end to end — replaces `Application.ProcessMessages` hacks and blocking UI.
- **Records** for DTOs and value objects; **nullable reference types** to kill null bugs.
- **Options pattern** (`IOptions<T>`) for configuration instead of INI files.
- **`decimal`** for money, **`DateOnly`/`TimeOnly`/`DateTimeOffset`** for dates.
- **Native AOT**, performance improvements each release; source generators.
- `CancellationToken` everywhere for cooperative cancellation.

## Example: a minimal endpoint in a module

```csharp
public static class PlaceOrderEndpoint
{
    public static void Map(IEndpointRouteBuilder app) =>
        app.MapPost("/orders", async (PlaceOrderCommand cmd, PlaceOrderHandler handler, CancellationToken ct) =>
        {
            var result = await handler.Handle(cmd, ct);
            return result.IsSuccess
                ? Results.Created($"/orders/{result.Value}", new { id = result.Value })
                : Results.Problem(result.Error, statusCode: 422);
        })
        .RequireAuthorization("sales:write")
        .WithTags("Orders");
}
```

## Hosting options

- **On-premises**: Windows Server/IIS or Linux + Kestrel behind Nginx; Docker.
- **Cloud**: Azure App Service, Azure Container Apps, AKS; AWS ECS/EKS/App Runner; any Kubernetes.
- For a company coming from Delphi desktop + on-prem DB, a **gradual path** is common: first API on-prem
  close to the DB, later cloud.
