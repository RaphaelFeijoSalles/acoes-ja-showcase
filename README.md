[🇺🇸 English Version](README-en.md)

<p align="center">
  <img src="images/acoes-ja-banner.png" alt="AçõesJá Banner">
</p>

<p align="center">
  <strong>Plataforma de Inteligência de Mercado Financeiro para Análise de Ações e Criptomoedas.</strong>
</p>

<p align="center">
    <img src="https://img.shields.io/badge/Java-25-orange?logo=java&logoColor=white" alt="Java 25">
    <img src="https://img.shields.io/badge/Spring%20Boot-3+-green?logo=spring&logoColor=white" alt="Spring Boot">
    <img src="https://img.shields.io/badge/React-19+-blue?logo=react&logoColor=white" alt="React 19+">
    <img src="https://img.shields.io/badge/PostgreSQL-18-blue?logo=postgresql&logoColor=white" alt="PostgreSQL 18">
    <img src="https://img.shields.io/badge/License-MIT-green" alt="License MIT">
</p>

<p align="center">
  <a href="#-screenshots">Screenshots</a> •
  <a href="#-sobre-o-projeto">Sobre</a> •
  <a href="#-principais-funcionalidades">Funcionalidades</a> •
  <a href="#-arquitetura">Arquitetura</a> •
  <a href="#-fluxos-de-dados">Fluxos</a> •
  <a href="#-decisões-de-design-adr">ADRs</a> •
  <a href="#-documentação-da-api">API</a>
</p>

<a id="-screenshots"></a>
## 📸 Screenshots

<table>
  <tr>
    <td valign="top" width="50%">
      <br>
      <b>Dashboard Principal</b>
      <img src="images/main-dashboard.png">
      <br>
      <i>Visualização geral de índices, cotações e portfólio.</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Análise de Ativo (PETR4)</b>
      <img src="images/asset-detail-view.png" width="100%" alt="Asset Detail View">
      <br>
      <i>Gráficos dinâmicos e indicadores fundamentalistas como P/L e ROE.</i>
    </td>
  </tr>
  <tr>
    <td valign="top" width="50%">
      <br>
      <b>Visualização Anual Tabular</b>
      <img src="images/tabular-data-view.png" width="100%" alt="tabular data view">
      <br>
      <i>Compare detalhadamente as demonstrações financeiras anuais.</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Motor de Busca Unificada</b>
      <img src="images/search-modal.png" width="100%" alt="Search Modal">
      <br>
      <i>Pesquisa rápida por ações, FIIs, BDRs e criptomoedas.</i>
    </td>
  </tr>
</table>

<a id="-sobre-o-projeto"></a>
## 📌 Sobre o Projeto

O **AçõesJá** é um ecossistema full-stack projetado para democratizar o acesso a dados financeiros de alta qualidade. O sistema ingere, processa e analisa gigabytes de dados contábeis diretamente da **CVM** e os cruza com cotações em tempo real da **B3** e de mercados de criptoativos. O objetivo não é apenas exibir números, mas oferecer insights de investimento através de um motor de análise automatizada, apresentados em um dashboard interativo e de alta performance.

<a id="-principais-funcionalidades"></a>
## ✨ Principais Funcionalidades

- **Análise Fundamentalista Completa:** Indicadores de Valuation (P/L, P/VP), Rentabilidade (ROE, ROIC) e Endividamento calculados automaticamente.
- **Pipeline de Dados ETL Robusto:** Módulo de ingestão (`Importer`) que processa, valida e armazena de forma resiliente gigabytes de dados da CVM, com sistema de quarentena para dados corrompidos.
- **Cotações em Tempo Real:** Integração com APIs de mercado para fornecer preços atualizados de ações e criptomoedas.
- **Autenticação Segura:** Sistema de autenticação stateless via JWT (JSON Web Tokens).
- **Busca Unificada:** Encontre qualquer ativo do mercado brasileiro ou cripto em segundos.
- **Arquitetura Limpa (Clean Architecture):** Backend desacoplado e testável, com clara separação entre domínio, aplicação e infraestrutura.

<a id="-arquitetura"></a>
## 🏗️ Arquitetura

O sistema foi desenhado com foco em separação de responsabilidades, escalabilidade e manutenção a longo prazo, utilizando princípios de **Clean Architecture** e **Domain-Driven Design (DDD)**.

- **Client Layer:** Um SPA (Single Page Application) consome os dados via chamadas REST otimizadas.
- **API Layer:** O Spring Boot provê endpoints seguros (Stateless com JWT) e valida as requisições de entrada.
- **Domain & Application Layer:** A lógica de negócio pura (cálculos de análise fundamentalista, valuation) reside aqui, completamente isolada de frameworks externos.
- **Infrastructure & Data Layer:** Camada responsável pela persistência de dados no PostgreSQL e pelas integrações com serviços externos, como APIs de mercado (B3) e a extração de arquivos da CVM.

<a id="-fluxos-de-dados"></a>
## 🔀 Fluxos de Dados

### Fluxo 1: Consulta de Análise de Ação
Como o sistema processa a requisição de um usuário para visualizar a análise completa de um ativo, cruzando dados do banco com APIs externas em tempo real:

```mermaid
sequenceDiagram
    autonumber
    actor User as Usuário
    box API Layer
        participant Controller as StockController
    end
    box Domain Layer
        participant Aggregator as StockAggregator
        participant FundAnalysis as FundamentalAnalysisService
        participant ValService as ValuationService
    end
    box Infrastructure & External
        participant Repo as AssetRepository
        participant Market as MarketDataService
        participant Brapi as brapi.dev
    end

    User->>Controller: GET /api/stocks/PETR4
    Controller->>Aggregator: getStockDetails("PETR4")
    Aggregator->>FundAnalysis: getAnalysis("PETR4")
    FundAnalysis->>Repo: findByTicker("PETR4")
    Repo-->>FundAnalysis: StockAnalysis (do BD)
    FundAnalysis-->>Aggregator: StockAnalysis
    Aggregator->>Market: getMarketData("PETR4")
    Market->>Brapi: HTTP GET
    Brapi-->>Market: Price JSON
    Market-->>Aggregator: MarketDataDTO
    Aggregator->>ValService: enrichWithValuation(Analysis, MarketData)
    Note over ValService: Calcula P/L, P/VP em tempo real
    ValService-->>Aggregator: Enriched DTO
    Aggregator-->>Controller: StockAnalysisDTO
    Controller-->>User: 200 OK (JSON Completo)
```

### Fluxo 2: Importação Agendada de Dados CVM (Pipeline ETL)
Como o sistema garante que os dados fundamentalistas estejam sempre atualizados, buscando gigabytes de arquivos governamentais de forma otimizada e tolerante a falhas:

```mermaid
sequenceDiagram
    autonumber
    actor Cron as Scheduler (Cron)
    box Domain Layer
        participant Sync as DataSyncService
        participant Local as CvmLocalImporter
        participant ImportSvc as CvmImportService
    end
    box Infrastructure Layer
        participant Helper as CvmPersistenceHelper
        participant DB as Banco de Dados (PostgreSQL)
    end
    box Fontes Externas
        participant CVM as dados.cvm.gov.br
    end

    Cron->>Sync: runSynchronization()
    Sync->>Local: processPendingFiles()
    Local->>CVM: HEAD Request (check ETag)
    CVM-->>Local: 304 Not Modified (ou 200 OK)
    alt Arquivo foi atualizado
        Local->>CVM: GET .zip file
        CVM-->>Local: File Stream
        Local->>ImportSvc: processCvmCsv(stream)
        Note over ImportSvc: Descompacta, valida e agrega
        ImportSvc->>Helper: saveBatchSafely(batch)
        Helper->>DB: Operação de Lote Atômico
        DB-->>Helper: OK
        Helper-->>ImportSvc: Done
        ImportSvc-->>Local: Success
    end
    Local-->>Sync: Done
```

<a id="-decisões-de-design-adr"></a>
## 🧠 Decisões de Design (ADR)

- **1. Separação `Company` vs. `Asset`:** O modelo de domínio distingue a `Empresa` (CNPJ, balanços) do `Ativo` (ticker, cotação), permitindo cruzar dados fundamentalistas de uma empresa com suas múltiplas classes de ativos (ON, PN) de forma precisa.
- **2. Auto-Correção de Balanços:** Um algoritmo de *Self-Healing* tenta inferir e corrigir inconsistências nos balanços da CVM (Ativo ≠ Passivo + PL) antes de descartar os dados, aumentando drasticamente a disponibilidade de informações.
- **3. Quarentena de Dados Corrompidos:** Linhas de CSV mal formatadas são isoladas em uma tabela de quarentena, garantindo que a falha em um registro não interrompa o processamento de gigabytes de dados válidos.

<a id="-documentação-da-api"></a>
## 📖 Documentação da API

A documentação completa e interativa da API, incluindo todos os endpoints, DTOs e esquemas de autenticação, está disponível através do Javadoc e pode ser visualizada no deploy do GitHub Pages deste repositório.

🔗 **[Acessar Documentação Completa](https://raphaelfeijosalles.github.io/acoes-ja-showcase/)**

---

<p align="center">
  Desenvolvido com ☕ e código limpo por <a href="https://github.com/RaphaelFeijoSalles" target="_blank">Raphael Salles</a>.
</p>
