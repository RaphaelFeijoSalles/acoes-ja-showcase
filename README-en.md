[🇧🇷 Versão em Português](README.md)

<p align="center">
  <img src="images/acoes-ja-banner.png" alt="AçõesJá Banner">
</p>

<p align="center">
  <strong>Financial Market Intelligence and Data Crossing Platform.</strong>
</p>

<p align="center">
    <img src="https://img.shields.io/badge/Java-17%2B-orange?logo=java&logoColor=white" alt="Java 17+">
    <img src="https://img.shields.io/badge/Spring%20Boot-3%2B-green?logo=spring&logoColor=white" alt="Spring Boot">
    <img src="https://img.shields.io/badge/React-19%2B-blue?logo=react&logoColor=white" alt="React 19+">
    <img src="https://img.shields.io/badge/PostgreSQL-18-blue?logo=postgresql&logoColor=white" alt="PostgreSQL">
</p>

<p align="center">
  <a href="#-screenshots">Screenshots</a> •
  <a href="#-about-the-project">About</a> •
  <a href="#-key-features">Features</a> •
  <a href="#-architecture">Architecture</a> •
  <a href="#-data-flow">Data Flow</a>
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
      <i>Overview of indices, market quotes, and portfolio.</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Asset Analysis (PETR4)</b>
      <img src="images/asset-detail-view.png" width="100%" alt="Asset Detail View">
      <br>
      <i>Dynamic charts and fundamental indicators.</i>
    </td>
  </tr>
  <tr>
    <td valign="top" width="50%">
      <br>
      <b>Tabular Data View</b>
      <img src="images/tabular-data-view.png" width="100%" alt="tabular data view">
      <br>
      <i>Detailed comparison of annual financial statements.</i>
    </td>
    <td valign="top" width="50%">
      <br>
      <b>Unified Search Engine</b>
      <img src="images/search-modal.png" width="100%" alt="Search Modal">
      <br>
      <i>Quick search for stocks, REITs (FIIs), BDRs, and cryptocurrencies.</i>
    </td>
  </tr>
</table>

<a id="-about-the-project"></a>
## 📌 About the Project

**AçõesJá** is a full-stack ecosystem designed to cross-reference Brazilian financial market data with high performance. The system ingests and processes gigabytes of government accounting data directly from the **CVM** (Securities and Exchange Commission of Brazil) and structures it alongside real-time quotes and indicators from the **B3** Stock Exchange.

The platform operates natively on a Freemium model, offering everything from quick views of the latest fiscal quarter to exporting dense financial spreadsheets (featuring a decade of historical data) for premium subscribers.

<a id="-key-features"></a>
## ✨ Key Features

- **CVM + B3 Crossing Engine:** Ingestion of official balance sheets crossed with real-time market prices for instant Valuation multiples calculation.
- **Optimized ETL Pipeline:** Structured data synchronizer designed to handle massive daily updates from government databases reliably.
- **Cache & High Performance:** Employment of in-memory caching strategies to store heavy financial reports, drastically reducing database I/O.
- **Integration Resilience:** Retry mechanisms and fault tolerance ensure system stability against oscillations or Rate Limits from external APIs (Brapi).
- **Stateless Authentication:** Robust protection using the Auth0 platform with short-lived JWTs and rotating Refresh Tokens.
- **Structured Export:** Dynamic `.xlsx` file generation engine formatted via *Apache POI* for offline financial modeling.

<a id="-architecture"></a>
## 🏗️ Architecture

The ecosystem adopts **Clean Architecture** and **Domain-Driven Design (DDD)** principles to ensure the isolation of pricing logic and fundamental analysis.

- **Client Layer (React):** A reactive Single Page Application (SPA) that consumes the RESTful API optimally.
- **API Layer (Spring Boot):** Security and routing layer that completely isolates the domain from the web framework.
- **Domain & Infrastructure Layer:** Centralizes financial business rules, relational persistence in PostgreSQL, and synchronous/asynchronous integrations.

<a id="-data-flow"></a>
## 🔀 Data Flow (Core)

```mermaid
sequenceDiagram
    autonumber
    actor User
    box API & Domain Layer
        participant App as AçõesJá Core
    end
    box External Data
        participant DB as PostgreSQL
        participant CVM as CVM Data (Gov)
        participant B3 as Market Data API
    end

    User->>App: Fetches fundamental analysis (e.g., WEGE3)
    App->>DB: Retrieves Balance Sheet History
    DB-->>App: Accounting Entity
    App->>B3: Fetch Real-time Quote
    B3-->>App: JSON Market Data
    Note over App: Executes multiples and profitability calculations
    App-->>User: Returns Consolidated Analysis

```
---
<p align="center">
  Developed with ☕ and clean code by <a href="https://linktr.ee/raphaelfeijosalles" target="_blank">Raphael Salles</a>.
</p>
<p align="center">
  <small>Copyright © 2026 Raphael Salles. All rights reserved. The code in this repository is proprietary and does not have an open-source license. Unauthorized use, copying, distribution, or modification is strictly prohibited.</small>
</p>