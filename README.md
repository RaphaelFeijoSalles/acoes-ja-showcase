[🇺🇸 English Version](README-en.md)

<p align="center">
  <img src="images/acoes-ja-banner.png" alt="AçõesJá Banner">
</p>

<p align="center">
  <strong>Plataforma de Inteligência de Mercado Financeiro e Cruzamento de Dados.</strong>
</p>

<p align="center">
    <img src="https://img.shields.io/badge/Java-17%2B-orange?logo=java&logoColor=white" alt="Java 17+">
    <img src="https://img.shields.io/badge/Spring%20Boot-3%2B-green?logo=spring&logoColor=white" alt="Spring Boot">
    <img src="https://img.shields.io/badge/React-19%2B-blue?logo=react&logoColor=white" alt="React 19+">
    <img src="https://img.shields.io/badge/PostgreSQL-18-blue?logo=postgresql&logoColor=white" alt="PostgreSQL">
</p>

<p align="center">
  <a href="#-screenshots">Screenshots</a> •
  <a href="#-sobre-o-projeto">Sobre</a> •
  <a href="#-principais-funcionalidades">Funcionalidades</a> •
  <a href="#-arquitetura">Arquitetura</a> •
  <a href="#-fluxo-de-dados">Fluxo de Dados</a>
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
      <i>Gráficos dinâmicos e indicadores fundamentalistas.</i>
    </td>
  </tr>
</table>

<a id="-sobre-o-projeto"></a>
## 📌 Sobre o Projeto

O **AçõesJá** é um ecossistema full-stack projetado para cruzar dados financeiros do mercado brasileiro com alta performance. O sistema ingere e processa gigabytes de dados contábeis governamentais diretamente da **CVM** (Balanços e DREs) e os cruza de forma estruturada com cotações e indicadores em tempo real da **B3**.

A plataforma opera nativamente em um modelo Freemium, oferecendo desde visualizações rápidas do último trimestre fiscal até exportações de planilhas financeiras densas (histórico de uma década) para assinantes.

<a id="-principais-funcionalidades"></a>
## ✨ Principais Funcionalidades

- **Motor de Cruzamento CVM + B3:** Ingestão de balanços oficiais e cruzamento com preços de tela para cálculo de múltiplos de *Valuation*.
- **Pipeline ETL Otimizado:** Sincronizador de dados estruturado para lidar com atualizações diárias de bases governamentais massivas.
- **Cache e Alta Performance:** Emprego de estratégias de cache em memória para armazenar relatórios financeiros pesados, reduzindo o I/O do banco de dados.
- **Resiliência de Integração:** Mecanismos de *Retry* e tolerância a falhas garantem a estabilidade do sistema frente à oscilação ou *Rate Limits* de APIs externas (Brapi).
- **Autenticação Stateless:** Proteção robusta utilizando a plataforma Auth0 com JWTs curtos e Refresh Tokens rotativos.
- **Exportação Estruturada:** Motor de geração dinâmica de arquivos `.xlsx` formatados via *Apache POI* para análises offline.

<a id="-arquitetura"></a>
## 🏗️ Arquitetura

O ecossistema adota princípios de **Clean Architecture** e **Domain-Driven Design (DDD)** para garantir o isolamento da lógica de precificação e análise fundamentalista.

- **Client Layer (React):** Single Page Application reativa que consome a API RESTful de forma otimizada.
- **API Layer (Spring Boot):** Camada de segurança e roteamento que isola o domínio do framework web.
- **Domain & Infrastructure Layer:** Concentra as regras de finanças, persistência relacional no PostgreSQL e as integrações síncronas/assíncronas.

<a id="-fluxo-de-dados"></a>
## 🔀 Fluxo de Dados (Core)

```mermaid
sequenceDiagram
    autonumber
    actor User as Usuário
    box API & Domain Layer
        participant App as AçõesJá Core
    end
    box External Data
        participant DB as PostgreSQL
        participant CVM as Dados CVM (Governo)
        participant B3 as Market Data API
    end

    User->>App: Busca análise fundamentalista (Ex: WEGE3)
    App->>DB: Recupera Histórico de Balanços
    DB-->>App: Entidade Contábil
    App->>B3: Fetch Cotação Real-time
    B3-->>App: JSON Market Data
    Note over App: Executa cálculos de múltiplos e rentabilidade
    App-->>User: Retorna Análise Consolidada

```
---
<p align="center">
  Desenvolvido com ☕ e código limpo por <a href="https://linktr.ee/raphaelfeijosalles" target="_blank">Raphael Salles</a>.
</p>
<p align="center">
  <small>Copyright © 2026 Raphael Salles. Todos os direitos reservados. O código deste repositório é proprietário e não possui licença de código aberto (Open Source). O uso, cópia, distribuição ou modificação não autorizada é estritamente proibido.</small>
</p>