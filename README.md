[🇺🇸 English Version](README-en.md)

<p align="center">
  <img src="images/acoes-ja-banner.png" alt="AçõesJá Banner">
</p>

<p align="center">
  <strong>Plataforma de Inteligência Financeira com Análise Fundamentalista Automatizada via IA.</strong>
</p>

<p align="center">
  <a href="#-sobre-o-projeto">Sobre</a> •
  <a href="#-architecture">Arquitetura</a> •
  <a href="#-data-pipeline">Data Pipeline</a> •
  <a href="#%EF%B8%8F-tecnologias">Tecnologias</a> •
  <a href="#-design-decisions">Design Decisions</a> •
  <a href="#-screenshots">Screenshots</a>
</p>

---

## 📌 Sobre o Projeto

O **AçõesJá** é um ecossistema full-stack projetado para democratizar o acesso a dados financeiros de alta qualidade. O sistema ingere, processa e analisa gigabytes de dados contábeis diretamente da **CVM** (Comissão de Valores Mobiliários) e os cruza com cotações em tempo real da **B3**.

O objetivo não é apenas exibir números, mas oferecer insights de investimento instantâneos através de um motor de regras e análise automatizada, apresentados em um dashboard interativo e de alta performance.

## 🏗️ Architecture

O sistema foi desenhado com foco em separação de responsabilidades, escalabilidade e manutenção a longo prazo, utilizando princípios de **Clean Architecture** e **Domain-Driven Design (DDD)** no backend, e uma abordagem baseada em componentes modulares no frontend.



### Fluxo Principal:
1. **Client Layer:** SPA em React consumindo dados via chamadas REST otimizadas.
2. **API Layer:** Spring Boot provendo endpoints seguros (Stateless JWT) e validando requisições.
3. **Domain & Application:** Lógica de negócio pura (análise fundamentalista, valuation) isolada de frameworks externos.
4. **Data & External:** Persistência no PostgreSQL e integrações com APIs de mercado (B3) e extração de arquivos da CVM.

## ⚙️ Data Pipeline

Um dos maiores desafios técnicos do projeto foi garantir a consistência de dados governamentais massivos e formatados de maneira irregular.



Nosso módulo `Importer` funciona como uma esteira **ETL (Extract, Transform, Load)** robusta:
* **Ingestão:** Processamento em lote (Batch Processing) de arquivos pesados da CVM.
* **Classificação:** Utilização do *Strategy Pattern* (`AccountClassifier`) para categorizar contas contábeis dinamicamente.
* **Rastreabilidade:** Auditoria completa desde a linha do CSV bruto até a consolidação do indicador calculado (P/L, ROE).

## 📐 Arquitetura de Fluxos (Sequence Diagrams)

Para garantir a separação de responsabilidades (Clean Architecture), o sistema orquestra as requisições passando por camadas bem definidas: **API**, **Domain** (onde reside a regra de negócio) e **Infrastructure**.

### Fluxo 1: Consulta de Análise de Ação
Como o sistema processa a requisição de um usuário para visualizar a análise completa de um ativo (ex: PETR4), cruzando dados do banco com APIs externas em tempo real:

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
    Repo-->>FundAnalysis: StockAnalysis (do Banco de Dados)
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
    Note over Sync: Disparado (ex: às 2am)
    
    Sync->>Local: processPendingFiles()
    
    Local->>CVM: HEAD Request (check ETag)
    CVM-->>Local: 304 Not Modified (ou 200 OK)
    
    alt Arquivo foi atualizado
        Local->>CVM: GET .zip file
        CVM-->>Local: File Stream
        
        Local->>ImportSvc: processCvmCsv(stream)
        Note over ImportSvc: - Descompacta em memória<br/>- Lê linha a linha<br/>- Valida e agrega dados
        
        ImportSvc->>Helper: saveBatchSafely(batch)
        Helper->>DB: Operação de Lote Atômico
        DB-->>Helper: OK
        Helper-->>ImportSvc: Done
        
        ImportSvc-->>Local: Success
    end
    
    Local-->>Sync: Done
```

## 🛠️ Tecnologias

A stack foi escolhida para garantir máxima tipagem, performance e segurança de ponta a ponta.

### 🖥️ Frontend (React SPA)
Construído para ser um dashboard interativo, rápido e responsivo.
* **Core:** React com TypeScript + Vite (Build ultra-rápido).
* **State Management:** Zustand (Estado global leve) + React Query (Data fetching, caching e sincronização).
* **Styling & UI:** Tailwind CSS para estilização utility-first e shadcn/ui para componentes acessíveis.
* **Data Visualization:** Recharts para renderização de gráficos financeiros interativos.
* **Routing:** React Router DOM para navegação SPA fluida.

### 🖧 Backend (API RESTful)
Construído para processamento pesado e estabilidade institucional.
* **Core:** Java 25 (LTS) + Spring Boot 3.
* **Database:** PostgreSQL (Produção) / H2 (Desenvolvimento/Testes).
* **Security:** Spring Security + JWT (Autenticação Stateless).
* **Design Patterns:** Clean Architecture, DDD, Strategy, Factory.

## 📖 API Documentation

A API foi projetada para ser consumida de forma intuitiva. Toda a documentação dos endpoints, contratos de requisição/resposta (DTOs) e esquemas de autenticação estão disponíveis de forma interativa(javadoc).

🔗 **[Acessar Swagger UI Completo](https://raphaelfeijosalles.github.io/acoes-ja-showcase/)**

## 🧠 Design Decisions

Decisões de engenharia tomadas para resolver problemas reais de domínio complexo:

### 1. Company vs. Asset Model
No mercado financeiro, uma empresa não é a mesma coisa que seu ticker de negociação.
* **Decisão:** Separação estrita no domínio entre a Entidade `Company` (ex: Petrobras, que detém o balanço patrimonial e CNPJ) e a Entidade `Asset` (ex: PETR3, PETR4, que possuem cotações, liquidez e direitos de voto diferentes).
* **Impacto:** Permite cruzar indicadores fundamentalistas de uma única empresa com múltiplas classes de ativos de forma precisa.

### 2. Self-Healing Financial Statements
Dados públicos frequentemente contêm falhas de preenchimento ou contas consolidadas de forma não-padrão.
* **Decisão:** Implementação de um algoritmo de auto-correção (*Self-Healing*). Se o balanço da CVM não fecha (Ativo ≠ Passivo + Patrimônio Líquido), o motor tenta inferir a conta faltante baseada em regras contábeis universais antes de rejeitar o lote.
* **Impacto:** Aumento drástico na disponibilidade de dados úteis sem intervenção manual.

### 3. Quarantine for Corrupted CVM Data
Ao processar gigabytes de dados, uma linha mal formatada não pode derrubar a esteira.
* **Decisão:** Implementação de um sistema de quarentena. Linhas do CSV que falham na validação estrutural ou lógica são desviadas para uma tabela de *Quarentena*, permitindo que o pipeline termine o processamento do restante do arquivo.
* **Impacto:** Tolerância a falhas elevada. Os dados em quarentena podem ser analisados e reprocessados posteriormente.

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
      <i>Gráficos dinâmicos (Recharts) e indicadores P/L, ROE.</i>
    </td>
  </tr>
  <tr>
    <td valign="top" width="50%">
      <br>
      <b>Visualização anual tabular </b>
      <img src="images/tabular-data-view.png" width="100%" alt="tabular data view">
      <br>
      <i>Visualização tabular de dados</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Motor de Busca Unificada</b>
      <img src="images/search-modal.png" width="100%" alt="Search Modal">
      <br>
      <i>Pesquisa rápida de ativos, empresas e setores.</i>
    </td>
  </tr>
</table>

---

<p align="center">
  Desenvolvido com ☕ e código limpo por <a href="https://github.com/RaphaelFeijoSalles" target="_blank">Raphael Salles</a>.
</p>
