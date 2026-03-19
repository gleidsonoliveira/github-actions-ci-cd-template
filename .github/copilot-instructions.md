# Copilot Instructions

## Repository Summary

This is a **CI/CD template** for an ASP.NET Core 9.0 Web API, demonstrating a full GitHub Actions pipeline (Build → Dev → Staging → Production). The sample API is a `WeatherForecast` endpoint that returns 5 days of randomly generated forecast data.

**Language/Framework:** C# / .NET 9.0 (ASP.NET Core, minimal hosting model)  
**Size:** ~15 source files  
**Runtime:** Linux containers, Docker multi-stage builds

---

## Project Layout

```
.github/
  workflows/
    cadastro-clientes.yml        # Main CI/CD pipeline (Build + Deploy Dev/Staging/Prod)
    feature-branch-validation.yaml  # Ensures feature/* branches are synced with main
src/
  WebApplication.sln             # Solution file
  WebApplication.csproj          # Project file (net9.0, ImplicitUsings, Nullable)
  Program.cs                     # App entry point (minimal hosting model)
  WeatherForecast.cs             # Data model — namespace: CadastroClientes
  Controllers/
    WeatherForecastController.cs # GET /weatherforecast — namespace: CadastroClientes.Controllers
  appsettings.json               # Logging config, AllowedHosts
  Properties/launchSettings.json # Run profiles: http (5047), https (7112), Docker (8080/8081)
  Dockerfile                     # 4-stage multi-stage build (base/build/publish/final)
  .dockerignore
  WebApplication.http            # REST client test file
```

**Key dependencies** (`src/WebApplication.csproj`):
- `Microsoft.AspNetCore.OpenApi` 9.0.13
- `Microsoft.VisualStudio.Azure.Containers.Tools.Targets` 1.22.1

---

## Build, Run & Test

### Prerequisites
- .NET 9.0 SDK (`dotnet --list-sdks` to verify)
- Docker (optional, for container builds)

### Build
```bash
cd src
dotnet build       # Debug build — succeeds in ~2s
dotnet build -c Release
```

### Run
```bash
cd src
dotnet run         # http://localhost:5047
curl http://localhost:5047/weatherforecast
```

### Docker
```bash
cd src
docker build -t webapplication .
docker run -p 8080:8080 webapplication
curl http://localhost:8080/weatherforecast
```

### Tests & Linting
There are **no test projects** and **no linting configuration** in this repository. If adding tests, use `dotnet test` from `src/`.

---

## Coding Conventions

- **Namespaces:** Use `CadastroClientes` (not `WebApplication` — that conflicts with `Microsoft.AspNetCore.Builder.WebApplication`). Controllers use `CadastroClientes.Controllers`.
- **Naming:** PascalCase for classes/methods/properties; `_camelCase` for private fields.
- **Modern C#:** `ImplicitUsings`, `Nullable` enabled; use `DateOnly`, `Random.Shared`, LINQ, top-level statements.
- **DI:** Constructor injection; register services in `Program.cs`.
- **Attribute routing:** `[ApiController]` + `[Route("[controller]")]`.

---

## CI/CD Workflows

### `cadastro-clientes.yml` — Main Pipeline
Triggered by: push/PR to `main`, or manual dispatch.

| Job | Condition | Environment |
|-----|-----------|-------------|
| Build | always | — |
| DeployDev | `pull_request` only | Development (`http://dev.myapp.com`) |
| DeployStaging | push to `main` only | Staging (`http://test.myapp.com`) |
| DeployProd | after Staging (requires approval) | Production (`http://www.myapp.com`) |

### `feature-branch-validation.yaml` — Feature Branch Guard
Triggered by: push/PR to `feature/*` branches. Runs on **self-hosted** runner. Fails if the feature branch is behind `main`.

---

## Known Issues & Workarounds

- **Namespace conflict:** Never name C# namespaces `WebApplication` in this project — it conflicts with `Microsoft.AspNetCore.Builder.WebApplication` (imported via `ImplicitUsings`). Use `CadastroClientes` instead.
- **Build tool:** Always build from `src/` directory. The `dotnet` CLI uses the solution file (`WebApplication.sln`) by default when run from `src/`.
- **No tests:** The project currently has no test infrastructure. The CI workflow's `Build` job uses a placeholder `echo Hello, world!` step.
- **Self-hosted runner:** `feature-branch-validation.yaml` requires a self-hosted runner (`runs-on: self-hosted`). It will not run on `ubuntu-latest`.

---

**Trust these instructions.** Only search the codebase if the information above is incomplete or appears to be in error.
