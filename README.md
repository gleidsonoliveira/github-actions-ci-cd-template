# GitHub Actions CI/CD Template

Template de pipeline CI/CD usando **GitHub Actions** com uma API ASP.NET Core 9.0 de exemplo.

---

## Sobre o Projeto

Este repositório serve como template para demonstrar um fluxo completo de **Integração Contínua (CI)** e **Entrega Contínua (CD)** com GitHub Actions. A aplicação de exemplo é uma Web API construída com ASP.NET Core 9.0 que expõe um endpoint de previsão do tempo.

---

## Tecnologias Utilizadas

- **.NET 9.0** (ASP.NET Core Web API)
- **Docker** (container Linux)
- **GitHub Actions** (CI/CD)
- **OpenAPI** (documentação da API em ambiente de desenvolvimento)

---

## Estrutura do Projeto

```
├── .github/
│   └── workflows/
│       └── cadastro-clientes.yml   # Pipeline CI/CD
├── src/
│   ├── Controllers/
│   │   └── WeatherForecastController.cs   # Controller da API
│   ├── Properties/
│   │   └── launchSettings.json            # Perfis de execução
│   ├── Program.cs                         # Ponto de entrada da aplicação
│   ├── WeatherForecast.cs                 # Modelo de dados
│   ├── Dockerfile                         # Build multi-stage para container
│   ├── WebApplication.csproj              # Configuração do projeto
│   ├── WebApplication.sln                 # Solution file
│   ├── appsettings.json                   # Configurações gerais
│   └── appsettings.Development.json       # Configurações de desenvolvimento
└── README.md
```

---

## API — Endpoints

### `GET /weatherforecast`

Retorna uma lista com 5 previsões do tempo geradas aleatoriamente.

**Resposta de exemplo:**

```json
[
  {
    "date": "2026-03-09",
    "temperatureC": 25,
    "temperatureF": 76,
    "summary": "Warm"
  }
]
```

| Campo           | Tipo     | Descrição                                  |
| --------------- | -------- | ------------------------------------------ |
| `date`          | `string` | Data da previsão (formato `yyyy-MM-dd`)    |
| `temperatureC`  | `int`    | Temperatura em Celsius (-20 a 55)          |
| `temperatureF`  | `int`    | Temperatura em Fahrenheit (calculada)      |
| `summary`       | `string` | Descrição textual (ex: Warm, Cool, Hot...) |

---

## Pipeline CI/CD

O workflow está definido em `.github/workflows/cadastro-clientes.yml` e é acionado por:

- **Push** na branch `main`
- **Pull Request** para a branch `main`
- **Execução manual** (`workflow_dispatch`)

### Jobs

```
Build ──► DeployDev        (apenas em Pull Requests)
      ──► DeployStaging    (apenas em push na main)
              ──► DeployProd   (após Staging, com aprovação)
```

| Job              | Condição                          | Ambiente       | URL                      |
| ---------------- | --------------------------------- | -------------- | ------------------------ |
| **Build**        | Sempre                            | —              | —                        |
| **DeployDev**    | Pull Request                      | Development    | `http://dev.myapp.com`   |
| **DeployStaging**| Push na `main`                    | Staging        | `http://test.myapp.com`  |
| **DeployProd**   | Após DeployStaging (com aprovação)| Production     | `http://www.myapp.com`   |

---

## Como Executar Localmente

### Pré-requisitos

- [.NET 9.0 SDK](https://dotnet.microsoft.com/download/dotnet/9.0)
- [Docker](https://www.docker.com/) (opcional, para execução em container)

### Executar com .NET CLI

```bash
cd src
dotnet run
```

A API estará disponível em `http://localhost:5047`.

### Executar com Docker

```bash
cd src
docker build -t webapplication .
docker run -p 8080:8080 -p 8081:8081 webapplication
```

A API estará disponível em `http://localhost:8080`.

### Testar o endpoint

```bash
curl http://localhost:5047/weatherforecast
```

---

## Configuração do Docker

O Dockerfile utiliza **multi-stage build** com as seguintes etapas:

1. **base** — Imagem runtime `aspnet:9.0`, expõe portas 8080 e 8081
2. **build** — Restaura dependências e compila o projeto
3. **publish** — Publica a aplicação otimizada para produção
4. **final** — Imagem final mínima com apenas os artefatos publicados

---

## Licença

Este projeto é um template de demonstração. Sinta-se livre para usar e adaptar.