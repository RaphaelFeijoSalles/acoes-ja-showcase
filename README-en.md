[🇧🇷 Portuguese Version](README.md)

<p align="center">
  <img src="images/acoes-ja-banner.png" alt="AçõesJá Banner">
</p>

<p align="center">
  <strong>Financial Market Intelligence Platform for Stock and Cryptocurrency Analysis.</strong>
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
  <a href="#-about-the-project">About</a> •
  <a href="#-key-features">Features</a> •
  <a href="#-architecture">Architecture</a> •
  <a href="#-data-flows">Flows</a> •
  <a href="#-design-decisions-adr">ADRs</a> •
  <a href="#-api-documentation">API</a>
</p>

<a id="-screenshots"></a>
## 📸 Screenshots

<table>
  <tr>
    <td valign="top" width="50%">
      <br>
      <b>Main Dashboard</b>
      <img src="images/main-dashboard.png">
      <br>
      <i>General overview of market indexes, quotes, and portfolio.</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Asset Analysis (PETR4)</b>
      <img src="images/asset-detail-view.png" width="100%" alt="Asset Detail View">
      <br>
      <i>Dynamic charts and fundamental indicators like P/E and ROE.</i>
    </td>
  </tr>
  <tr>
    <td valign="top" width="50%">
      <br>
      <b>Annual Tabular View</b>
      <img src="images/tabular-data-view.png" width="100%" alt="tabular data view">
      <br>
      <i>Compare annual financial statements in detail.</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Unified Search Engine</b>
      <img src="images/search-modal.png" width="100%" alt="Search Modal">
      <br>
      <i>Quickly search for stocks, REITs, BDRs, and cryptocurrencies.</i>
    </td>
  </tr>
</table>

<a id="-about-the-project"></a>
## 📌 About the Project

**AçõesJá** is a full-stack ecosystem designed to democratize access to high-quality financial data. The system ingests, processes, and analyzes gigabytes of accounting data directly from Brazil's SEC (**CVM**) and cross-references it with real-time quotes from the **B3** stock exchange and crypto markets. The goal is not just to display numbers, but to offer investment insights through an automated analysis engine, presented in a high-performance, interactive dashboard.

<a id="-key-features"></a>
## ✨ Key Features

- **Complete Fundamental Analysis:** Automatically calculated Valuation (P/E, P/B), Profitability (ROE, ROIC), and Debt ratios.
- **Robust ETL Data Pipeline:** A resilient `Importer` module that processes, validates, and stores gigabytes of CVM data, featuring a quarantine system for corrupted records.
- **Real-Time Quotes:** Integration with market APIs to provide up-to-date prices for stocks and cryptocurrencies.
- **Secure Authentication:** Stateless authentication system via JWT (JSON Web Tokens).
- **Unified Search:** Find any asset from the Brazilian market or crypto space in seconds.
- **Clean Architecture:** Decoupled and testable backend with a clear separation between domain, application, and infrastructure layers.

<a id="-architecture"></a>
## 🏗️ Architecture

The system was designed with a focus on separation of concerns, scalability, and long-term maintainability, using **Clean Architecture** and **Domain-Driven Design (DDD)** principles.

- **Client Layer:** A Single Page Application (SPA) consumes data via optimized REST calls.
- **API Layer:** Spring Boot provides secure endpoints (Stateless with JWT) and validates incoming requests.
- **Domain & Application Layer:** The pure business logic (fundamental analysis calculations, valuation) resides here, completely isolated from external frameworks.
- **Infrastructure & Data Layer:** This layer is responsible for data persistence in PostgreSQL and integrations with external services, such as market APIs (B3) and CVM file extraction.

<a id="-data-flows"></a>
## 🔀 Data Flows

### Flow 1: Stock Analysis Query
How the system processes a user request to view a comprehensive asset analysis, cross-referencing database information with real-time external APIs:

```mermaid
sequenceDiagram
    autonumber
    actor User as User
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
    Repo-->>FundAnalysis: StockAnalysis (from DB)
    FundAnalysis-->>Aggregator: StockAnalysis
    Aggregator->>Market: getMarketData("PETR4")
    Market->>Brapi: HTTP GET
    Brapi-->>Market: Price JSON
    Market-->>Aggregator: MarketDataDTO
    Aggregator->>ValService: enrichWithValuation(Analysis, MarketData)
    Note over ValService: Calculates P/E, P/B in real-time
    ValService-->>Aggregator: Enriched DTO
    Aggregator-->>Controller: StockAnalysisDTO
    Controller-->>User: 200 OK (Full JSON)
```

### Flow 2: Scheduled CVM Data Import (ETL Pipeline)
How the system ensures fundamental data is always up-to-date by fetching gigabytes of government files in an optimized and fault-tolerant manner:

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
        participant DB as Database (PostgreSQL)
    end
    box External Sources
        participant CVM as dados.cvm.gov.br
    end

    Cron->>Sync: runSynchronization()
    Sync->>Local: processPendingFiles()
    Local->>CVM: HEAD Request (check ETag)
    CVM-->>Local: 304 Not Modified (or 200 OK)
    alt File was updated
        Local->>CVM: GET .zip file
        CVM-->>Local: File Stream
        Local->>ImportSvc: processCvmCsv(stream)
        Note over ImportSvc: Unzips, validates, and aggregates
        ImportSvc->>Helper: saveBatchSafely(batch)
        Helper->>DB: Atomic Batch Operation
        DB-->>Helper: OK
        Helper-->>ImportSvc: Done
        ImportSvc-->>Local: Success
    end
    Local-->>Sync: Done
```

<a id="-design-decisions-adr"></a>
## 🧠 Design Decisions (ADR)

- **1. `Company` vs. `Asset` Separation:** The domain model distinguishes the `Company` (legal entity with financials) from the `Asset` (tradable ticker with a price), allowing for the accurate cross-referencing of a single company's fundamental data against its multiple asset classes (e.g., common vs. preferred stock).
- **2. Self-Healing Financial Statements:** A *Self-Healing* algorithm attempts to infer and correct inconsistencies in CVM balance sheets (where Assets ≠ Liabilities + Equity) before discarding the data, drastically increasing the availability of useful information.
- **3. Quarantine for Corrupted Data:** Malformed CSV rows are isolated in a quarantine table, ensuring that a single bad record does not stop the entire pipeline from processing gigabytes of valid data.

<a id="-api-documentation"></a>
## 📖 API Documentation

The complete and interactive API documentation, including all endpoints, DTOs, and authentication schemes, is available via Javadoc and can be viewed on the GitHub Pages deployment of this repository.

🔗 **[Access Full Documentation](https://raphaelfeijosalles.github.io/acoes-ja-showcase/)**

---

<p align="center">
  Developed with ☕ and clean code by <a href="https://github.com/RaphaelFeijoSalles" target="_blank">Raphael Salles</a>.
</p>