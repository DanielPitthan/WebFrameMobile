# WebFrame Mobile

Aplicação híbrida construída com **.NET MAUI Blazor Hybrid** e **Blazor Server**, voltada para o controle de produção industrial. Roda nativamente em dispositivos móveis e desktops, além de ser acessível via navegador web, compartilhando os mesmos componentes Blazor em ambas as plataformas.

---

## Visão Geral

O WebFrame Mobile integra-se a APIs de backend para fornecer funcionalidades de acompanhamento e controle do chão de fábrica, como controle de lotes via QR Code, gestão de etiquetas, ordens de produção e um dashboard analítico de produção.

---

## Funcionalidades

| Módulo | Descrição |
|---|---|
| **Autenticação** | Login com usuário e senha; gerenciamento de token JWT e estado de sessão |
| **Controle de Lote** | Consulta e apontamento de lotes via leitura de QR Code |
| **Etiquetas** | Consulta e impressão de etiquetas vinculadas a lotes |
| **Ordens de Produção (OP)** | Listagem e gerenciamento de ordens de produção |
| **Dashboard de Produção** | Indicadores analíticos: FPY, Delta E, aprovados/reprovados, tendências e ranking de produtos |

---

## Arquitetura

A solução adota uma arquitetura **Blazor Hybrid** com inspiração em **DDD (Domain-Driven Design)**, separando responsabilidades em camadas bem definidas:

```
WebFrameMobile (solução)
├── WebFrameMobile/            # App .NET MAUI (Android, iOS, macOS, Windows)
├── WebFrameMobile.Web/        # App Blazor Server (navegador web)
├── WebFrameMobile.Shared/     # Componentes Razor compartilhados (MAUI + Web)
├── InterfacesDDD/             # Camada de serviços (DDD)
│   ├── Auth/                  # Autenticação e estado de usuário
│   ├── ControleLote/          # Controle de lotes e apontamentos
│   ├── Dashboard/             # Dados analíticos de produção
│   └── Etiquetas/             # Gestão de etiquetas
├── Models.DTO/                # Data Transfer Objects (DTOs)
│   ├── Login.DTO/
│   ├── Lote.DTO/
│   ├── OP.DTO/
│   └── Etiqueta.DTO/
├── Definitions.Commom/        # Registro de DI e configurações da API
└── Extensions/                # Extensões utilitárias (ex: DateTime)
```

O projeto `WebFrameMobile.Shared` contém todos os componentes e páginas Blazor, reutilizados tanto pelo app MAUI quanto pelo app Web, garantindo uma única base de código para a UI.

---

## Plataformas Suportadas

| Plataforma | Target Framework |
|---|---|
| Android | `net10.0-android` |
| iOS | `net10.0-ios` |
| macOS (Catalyst) | `net10.0-maccatalyst` |
| Windows | `net10.0-windows10.0.19041.0` |
| Web (Blazor Server) | `net10.0` |

---

## Stack de Tecnologias

- [.NET 10](https://dotnet.microsoft.com/)
- [.NET MAUI](https://learn.microsoft.com/dotnet/maui/) — aplicação nativa cross-platform
- [Blazor Hybrid](https://learn.microsoft.com/aspnet/core/blazor/hybrid/) — UI compartilhada via WebView
- [Blazor Server](https://learn.microsoft.com/aspnet/core/blazor/hosting-models#blazor-server) — versão web
- [Radzen Blazor Components](https://blazor.radzen.com/) — componentes de UI e gráficos
- `IHttpClientFactory` — comunicação com APIs REST de backend

---

## Pré-requisitos

- [.NET 10 SDK](https://dotnet.microsoft.com/download/dotnet/10.0)
- [Visual Studio 2022+](https://visualstudio.microsoft.com/) com as cargas de trabalho:
  - **Desenvolvimento de aplicativo móvel com .NET (MAUI)**
  - **ASP.NET e desenvolvimento web**
- Android SDK (para build/execução Android)
- Xcode (para build iOS/macOS, requer macOS)

---

## Configuração

As URLs das APIs de backend são configuradas via `appsettings.json`, localizado em `wwwroot/` de cada projeto host.

```json
{
  "ApiSettings": {
    "BaseUrlAuth": "http://<host>:<porta-auth>/",
    "BaseUrlLoteControle": "http://<host>:<porta-lote>/",
    "BaseUrlDashboard": "http://<host>:<porta-dashboard>/"
  }
}
```

> ⚠️ **Não versione endereços de produção ou credenciais.** Utilize variáveis de ambiente ou User Secrets para ambientes sensíveis.

---

## Executando o Projeto

### App MAUI (mobile/desktop)

```bash
dotnet build WebFrameMobile/WebFrameMobile/WebFrameMobile.csproj -f net10.0-android
dotnet run --project WebFrameMobile/WebFrameMobile/WebFrameMobile.csproj -f net10.0-windows10.0.19041.0
```

### App Web (Blazor Server)

```bash
dotnet run --project WebFrameMobile/WebFrameMobile.Web/WebFrameMobile.Web.csproj
```

Acesse em `https://localhost:<porta>` conforme configurado no `launchSettings.json`.

---

## Estrutura de DI (Injeção de Dependências)

O registro de serviços é centralizado em `Definitions.Commom/ServiceCollections.DI.cs` e reutilizado em ambos os hosts (MAUI e Web):

```csharp
services.AddSingleton<IAuthService, AuthService>();
services.AddSingleton<AuthStateProvider>();
services.AddScoped<ILoteServiceDDD, LoteServiceDDD>();
services.AddScoped<IEtiquetaServiceDDD, EtiquetaServiceDDD>();
services.AddScoped<IOPServiceDDD, OPServiceDDD>();
services.AddScoped<IDashboardApiService, DashboardApiService>();
services.AddRadzenComponents();
```

---

## Licença

Este projeto é de uso privado. Consulte o proprietário do repositório para informações sobre licenciamento.
