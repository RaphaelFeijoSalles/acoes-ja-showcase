[pt-BR Versão em Português](README.md)

<p align="center">
  <img src="images/acoes-ja-banner.png" alt="AçõesJá Banner">
</p>

<p align="center">
  <strong>Financial Intelligence Platform with Automated Fundamental Analysis via AI.</strong>
</p>

<p align="center">
  <a href="#-about-the-project">About</a> •
  <a href="#-architecture">Architecture</a> •
  <a href="#-data-pipeline">Data Pipeline</a> •
  <a href="#%EF%B8%8F-technologies">Technologies</a> •
  <a href="#-design-decisions">Design Decisions</a> •
  <a href="#-screenshots">Screenshots</a>
</p>

---

## 📌 About the Project

**AçõesJá** is a full-stack ecosystem designed to democratize access to high-quality financial data. The system ingests, processes, and analyzes gigabytes of accounting data directly from the **CVM** (Securities and Exchange Commission of Brazil) and cross-references it with real-time quotes from the **B3** (Brazilian Stock Exchange).

The goal isn't just to display numbers, but to offer instant investment insights through a rules engine and automated analysis, presented in a high-performance interactive dashboard.

## 🏗️ Architecture

The system was designed with a focus on separation of concerns, scalability, and long-term maintainability, using **Clean Architecture** and **Domain-Driven Design (DDD)** principles on the backend, and a modular component-based approach on the frontend.

## 📐 Architecture Flows (Sequence Diagrams)

To ensure separation of concerns, the system orchestrates requests through well-defined layers: **API**, **Domain** (where the business logic resides), and **Infrastructure**.

### Flow 1: Stock Analysis Query
How the system processes a user request to view a comprehensive asset analysis (e.g., PETR4), cross-referencing database information with real-time external APIs:

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
    Repo-->>FundAnalysis: StockAnalysis (from Database)
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
    Note over Sync: Triggered (e.g., at 2 AM)
    
    Sync->>Local: processPendingFiles()
    
    Local->>CVM: HEAD Request (check ETag)
    CVM-->>Local: 304 Not Modified (or 200 OK)
    
    alt File was updated
        Local->>CVM: GET .zip file
        CVM-->>Local: File Stream
        
        Local->>ImportSvc: processCvmCsv(stream)
        Note over ImportSvc: - Unzips in memory<br/>- Reads line by line<br/>- Validates & aggregates data
        
        ImportSvc->>Helper: saveBatchSafely(batch)
        Helper->>DB: Atomic Batch Operation
        DB-->>Helper: OK
        Helper-->>ImportSvc: Done
        
        ImportSvc-->>Local: Success
    end
    
    Local-->>Sync: Done
```

### Core Flow:
1. **Client Layer:** React SPA consuming data via optimized REST calls.
2. **API Layer:** Spring Boot providing secure endpoints (Stateless JWT) and validating requests.
3. **Domain & Application:** Pure business logic (fundamental analysis, valuation) completely isolated from external frameworks.
4. **Data & External:** Persistence in PostgreSQL and integrations with market APIs (B3) and CVM file extraction.

## ⚙️ Data Pipeline

One of the major technical challenges of the project was ensuring the consistency of massive, irregularly formatted government data.



Our `Importer` module works as a robust **ETL (Extract, Transform, Load)** pipeline:
* **Ingestion:** Batch Processing of heavy CVM files.
* **Classification:** Utilization of the *Strategy Pattern* (`AccountClassifier`) to dynamically categorize accounting items.
* **Traceability:** Full audit trail from the raw CSV line to the consolidated calculated indicator (e.g., P/E, ROE).

## 🛠️ Technologies

The stack was chosen to ensure maximum typing, performance, and end-to-end security.

### 🖥️ Frontend (React SPA)
Built to be a fast, responsive, and interactive dashboard.
* **Core:** React with TypeScript + Vite (Ultra-fast build).
* **State Management:** Zustand (Lightweight global state) + React Query (Data fetching, caching, and synchronization).
* **Styling & UI:** Tailwind CSS for utility-first styling and shadcn/ui for accessible components.
* **Data Visualization:** Recharts for rendering interactive financial charts.
* **Routing:** React Router DOM for fluid SPA navigation.

### 🖧 Backend (RESTful API)
Built for heavy processing and institutional-grade stability.
* **Core:** Java 25 (LTS) + Spring Boot 3.
* **Database:** PostgreSQL (Production) / H2 (Development/Testing).
* **Security:** Spring Security + JWT (Stateless Authentication).
* **Design Patterns:** Clean Architecture, DDD, Strategy, Factory.

## 📖 API Documentation

The API was designed to be consumed intuitively. All endpoint documentation, request/response contracts (DTOs), and authentication schemes are interactively available.

🔗 **[Access Full Swagger UI](#)** *(Temporary link for demonstration: `https://raphaelfeijosalles.github.io/acoes-ja-showcase/`)*.

## 🧠 Design Decisions

Engineering decisions made to solve real-world complex domain problems:

### 1. Company vs. Asset Model
In the financial market, a company is not the same as its trading ticker.
* **Decision:** Strict separation in the domain between the `Company` Entity (e.g., Petrobras, which holds the balance sheet and corporate ID) and the `Asset` Entity (e.g., PETR3, PETR4, which have different quotes, liquidity, and voting rights).
* **Impact:** Allows accurate cross-referencing of fundamental indicators from a single company against multiple asset classes.

### 2. Self-Healing Financial Statements
Public data often contains input errors or non-standard consolidated accounts.
* **Decision:** Implementation of a *Self-Healing* algorithm. If the CVM balance sheet doesn't balance (Assets ≠ Liabilities + Equity), the engine attempts to infer the missing account based on universal accounting rules before rejecting the batch.
* **Impact:** Drastic increase in the availability of useful data without manual intervention.

### 3. Quarantine for Corrupted CVM Data
When processing gigabytes of data, a malformed line cannot crash the pipeline.
* **Decision:** Implementation of a quarantine system. CSV lines that fail structural or logical validation are diverted to a *Quarantine* table, allowing the pipeline to finish processing the rest of the file.
* **Impact:** High fault tolerance. Quarantined data can be analyzed and reprocessed later.

## 📸 Screenshots

<table>
  <tr>
    <td valign="top" width="50%">
      <br>
      <b>Main Dashboard</b>
      <img src="images/main-dashboard.png" width="100%" alt="Main Dashboard View">
      <br>
      <i>Overview of indices, quotes, and user portfolio.</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Asset Analysis (PETR4)</b>
      <img src="images/asset-detail-view.png" width="100%" alt="Asset Detail View">
      <br>
      <i>Dynamic charts (Recharts) and P/E, ROE indicators.</i>
    </td>
  </tr>
  <tr>
    <td valign="top" width="50%">
      <br>
      <b>Annual Tabular View</b>
      <img src="images/tabular-data-view.png" width="100%" alt="Tabular Data View">
      <br>
      <i>Tabular data visualization for detailed analysis.</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Unified Search Engine</b>
      <img src="images/search-modal.png" width="100%" alt="Search Modal">
      <br>
      <i>Quick search for assets, companies, and sectors.</i>
    </td>
  </tr>
</table>

---

<p align="center">
  Developed with ☕ and clean code by <a href="https://github.com/RaphaelFeijoSalles" target="_blank">Raphael Salles</a>.
</p>