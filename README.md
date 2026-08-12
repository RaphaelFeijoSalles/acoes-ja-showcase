[🇺🇸 Read in English](README-en.md)

<p align="center">
  <img src="images/acoes-ja-banner.png" alt="AçõesJá — análise de ativos brasileiros">
</p>

<h1 align="center">Dados financeiros brasileiros, organizados para análise</h1>

<p align="center">
  Pesquise ativos, explore fundamentos e históricos e mantenha uma carteira de acompanhamento em uma experiência full-stack.
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Java-25-orange?logo=openjdk&logoColor=white" alt="Java 25">
  <img src="https://img.shields.io/badge/Spring_Boot-4.0.5-6DB33F?logo=springboot&logoColor=white" alt="Spring Boot 4.0.5">
  <img src="https://img.shields.io/badge/Next.js-16-black?logo=nextdotjs&logoColor=white" alt="Next.js 16">
  <img src="https://img.shields.io/badge/React-19-149ECA?logo=react&logoColor=white" alt="React 19">
  <img src="https://img.shields.io/badge/PostgreSQL-persistência-4169E1?logo=postgresql&logoColor=white" alt="PostgreSQL">
</p>

<p align="center">
  <a href="#experiência">Experiência</a> ·
  <a href="#funcionalidades">Funcionalidades</a> ·
  <a href="#como-funciona">Como funciona</a> ·
  <a href="#para-recrutadores">Para recrutadores</a> ·
  <a href="https://acoesja.com.br">Demonstração</a> ·
  <a href="https://linktr.ee/raphaelfeijosalles">Contato</a>
</p>

> Plataforma educacional de análise financeira. O conteúdo não constitui recomendação de investimento.

## A proposta

Informações contábeis e dados de mercado costumam estar espalhados por fontes e formatos diferentes. O AçõesJá transforma esse material em uma jornada única:

1. pesquise uma empresa por ticker, nome ou CNPJ;
2. consulte indicadores e demonstrativos anuais ou trimestrais;
3. acompanhe o histórico de preços junto aos dados contábeis;
4. salve tickers em uma carteira de acompanhamento;
5. exporte análises autorizadas para `.xlsx`.

O projeto também automatiza a ingestão de DFP, ITR, cadastro de companhias e FRE da CVM, mantendo histórico, controles e auditoria do processo de importação.

<a id="experiência"></a>
## Experiência

<table>
  <tr>
    <td valign="top" width="50%">
      <strong>Dashboard e acompanhamento</strong><br>
      <img src="images/main-dashboard.png" alt="Dashboard do AçõesJá com pesquisa e acompanhamento de ativos">
      <small>Entrada para pesquisa, informações de mercado e carteira de tickers.</small>
    </td>
    <td valign="top" width="50%">
      <strong>Análise de ativo</strong><br>
      <img src="images/asset-detail-view.png" alt="Página de detalhes de um ativo no AçõesJá">
      <small>Fundamentos, dados de mercado e histórico anual ou trimestral.</small>
    </td>
  </tr>
  <tr>
    <td valign="top" width="50%">
      <strong>Histórico financeiro</strong><br>
      <img src="images/tabular-data-view.png" alt="Visualização tabular de dados financeiros históricos">
      <small>Leitura estruturada de períodos e indicadores financeiros.</small>
    </td>
    <td valign="top" width="50%">
      <strong>Pesquisa unificada</strong><br>
      <img src="images/search-modal.png" alt="Pesquisa por ticker, nome ou CNPJ">
      <small>Descoberta pública de ativos por ticker, nome ou CNPJ.</small>
    </td>
  </tr>
</table>

### Acesso público

<p align="center">
  <img src="images/detalhes-sem-login.png" alt="Detalhes públicos de VALE3 com histórico financeiro e estados de acesso">
  <small>Consulta de fundamentos e histórico sem exigir login, com recursos autenticados claramente identificados.</small>
</p>

### Autenticação e consentimento

<table>
  <tr>
    <td valign="top" width="50%">
      <strong>Login por senha ou Google</strong><br>
      <img src="images/fluxo-login/modal-login.png" alt="Modal de login por senha ou Google">
      <small>Acesso à carteira, exportação e Professor IA.</small>
    </td>
    <td valign="top" width="50%">
      <strong>Integração Google</strong><br>
      <img src="images/fluxo-login/login-google.png" alt="Continuação do login com Google no domínio acoesja.com.br">
      <small>Fluxo social conectado ao domínio público.</small>
    </td>
  </tr>
  <tr>
    <td valign="top" width="50%">
      <strong>Termos de Uso versionados</strong><br>
      <img src="images/fluxo-login/termos-de-uso.png" alt="Leitura e aceite dos Termos de Uso">
    </td>
    <td valign="top" width="50%">
      <strong>Política de Privacidade</strong><br>
      <img src="images/fluxo-login/politica-priv.png" alt="Leitura e aceite da Política de Privacidade">
    </td>
  </tr>
</table>

### Professor IA em ação

<p align="center">
  <img src="images/professorIA-em-acao.png" alt="Professor IA comparando métricas selecionadas de BBAS3 e PETR4">
  <small>Contexto selecionável, resposta educacional e aviso explícito para conferência das fontes.</small>
</p>

### Operação administrativa

<table>
  <tr>
    <td valign="top" width="50%">
      <strong>Bootstrap guiado de dados</strong><br>
      <img src="images/painel-admin/bootstrap-guiado.png" alt="Pipeline administrativo de importação CVM, FRE e balanços">
    </td>
    <td valign="top" width="50%">
      <strong>Publicação de documentos legais</strong><br>
      <img src="images/painel-admin/publicação-de-termos.png" alt="Editor administrativo de documentos legais com preview">
    </td>
  </tr>
  <tr>
    <td valign="top" colspan="2">
      <strong>Gestão de usuários e papéis</strong><br>
      <img src="images/painel-admin/manejo-de-usuarios.png" alt="Área administrativa de gestão de usuários e papéis">
    </td>
  </tr>
</table>

<a id="funcionalidades"></a>
## Funcionalidades comprovadas

### Para quem analisa ativos

- Pesquisa e detalhes públicos.
- Indicadores fundamentalistas e históricos DFP/ITR.
- Histórico de cotações associado aos períodos financeiros.
- Carteira de acompanhamento após autenticação.
- Exportação XLSX para perfis autorizados.
- Professor IA contextual para explicações e comparação de métricas selecionadas.

### Identidade e experiência

- Cadastro e login por senha.
- Login social com Google.
- JWT em cookies HttpOnly e renovação silenciosa.
- Papéis de acesso e rotas administrativas.
- Termos de uso e política de privacidade versionados.
- Estados de carregamento, erro, conteúdo vazio e acesso restrito.

### Operação de dados

- Importação de DFP/ITR, cadastro e FRE da CVM.
- Separação entre demonstrativos consolidados e individuais.
- Persistência de cotações históricas.
- Brapi como integração de mercado e AlphaVantage como fallback de histórico.
- Histórico, erros, auditoria e reimportação dos pipelines.
- Geração de planilhas com Apache POI.

<a id="como-funciona"></a>
## Como funciona

```mermaid
flowchart LR
    Pessoa[Pessoa usuária] -->|pesquisa e análise| Web[Next.js 16 + React 19]
    Web -->|API REST + cookies| API[Java 25 + Spring Boot 4]
    API --> DB[(PostgreSQL)]
    CVM[CVM: DFP / ITR / cadastro / FRE] --> PIPE[Pipeline de importação]
    PIPE --> DB
    MARKET[Brapi / AlphaVantage] --> API
    API --> XLSX[Exportação XLSX]
    API --> AI[Professor IA configurável]
```

```mermaid
sequenceDiagram
    actor Pessoa
    participant Web as AçõesJá Web
    participant API as AçõesJá API
    participant DB as PostgreSQL

    Pessoa->>Web: Pesquisa um ticker
    Web->>API: Busca e detalhes
    API->>DB: Companhia, ativo e históricos
    DB-->>API: Dados persistidos
    API-->>Web: Indicadores e séries
    Web-->>Pessoa: Análise histórica
    opt Usuário autenticado
        Pessoa->>Web: Salva o ticker
        Web->>API: Atualiza carteira
    end
```

## Stack

| Backend | Frontend | Dados e entrega |
|---|---|---|
| Java 25 | Next.js 16 | PostgreSQL |
| Spring Boot 4.0.5 | React 19 | Flyway |
| Spring Security | TypeScript 5.9 | Docker |
| Spring Data JPA | TanStack Query | GitHub Actions |
| Spring AI/Caffeine | Axios/Zustand | Configuração Fly.io |
| Apache POI | Recharts/Tailwind | Dependabot |

## Decisões técnicas de alto nível

### Companhia não é ticker

`Company` representa o emissor; `Asset` representa o instrumento negociado. Uma companhia pode, assim, compartilhar demonstrações entre diferentes classes de ação.

### Schema evolui por migrations

Flyway registra mudanças de banco, enquanto Hibernate valida a estrutura em execução normal.

### Tokens permanecem fora do JavaScript

JWTs são transportados por cookies HttpOnly; o frontend coordena renovação e sessão sem ler diretamente os tokens.

### Consolidado e individual não se misturam

O pipeline identifica o escopo contábil e prioriza demonstrativos consolidados para preservar a coerência das análises.

<a id="para-recrutadores"></a>
## Para recrutadores

Este projeto demonstra trabalho full-stack sobre um domínio com regras, precisão numérica e integrações:

- API REST e processamento financeiro com Java 25 e Spring Boot 4.
- Interface orientada a dados com Next.js 16, React 19 e TypeScript.
- Modelagem relacional, JPA, PostgreSQL e migrations Flyway.
- Autenticação por senha/Google, JWT em cookies HttpOnly e autorização por papéis.
- Pipelines auditáveis de dados públicos da CVM e integrações de mercado.
- Testes automatizados e integração contínua nos repositórios de backend e frontend.

## Vamos conversar?

Se você quer discutir o produto, as decisões técnicas ou uma oportunidade Full-Stack Java + React/Next.js, entre em contato:

<p align="center">
  <a href="https://linktr.ee/raphaelfeijosalles"><strong>Contato e perfil profissional</strong></a>
</p>

---

<p align="center">
  Desenvolvido por Raphael Salles · Projeto proprietário, sem licença open source.
</p>
