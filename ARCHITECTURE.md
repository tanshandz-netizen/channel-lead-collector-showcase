# Architecture

This is a conceptual description of the existing local application. Boxes represent responsibilities visible in its implementation; they do not imply independently deployed services. No source code or internal document is reproduced here.

## Operator workflow

```mermaid
flowchart TD
    K[Operator enters keywords and collection targets] --> L[Manual authorized login if required]
    L --> P[Playwright persistent browser context]
    P --> S[Search results in platform frame]
    S --> H{Project ID or URL already processed?}
    H -->|Yes| N[Skip detail and inspect next result]
    H -->|No| D[Open and parse notice details]
    D --> X[Extract prioritized company field and provenance]
    X --> R[Record completed project in local history]
    R --> V{Usable company field found?}
    V -->|No| N
    V -->|Yes| C{Duplicate company, link, or baseline pair?}
    C -->|Yes| N
    C -->|No| E[Retain result and export Excel]
    E --> T{Useful-result target or scan limit reached?}
    N --> T
    T -->|No, more results| Q[Next result or next page]
    Q --> S
    T -->|Yes, or no more pages| F[Operator requests Feishu dry-run]
    F --> B[Read existing Bitable records and calculate plan]
    B --> U[Review add, fill-empty, and skip actions]
    U --> A{Operator confirms write?}
    A -->|No| O[Retain local export]
    A -->|Yes| G{Connection and source signature still valid?}
    G -->|No| F
    G -->|Yes| W[Execute Feishu write]
```

The diagram compresses per-keyword checkpoints and final export into one logical export step. It is not a claim of a distributed transaction across history, workbooks, and Feishu. Recording a project as processed prevents repeat detail inspection; it does not guarantee that its information is final or that a later synchronization succeeded.

## Technical layers

```mermaid
flowchart TD
    subgraph LocalApp[Local macOS application]
        UI[Web UI - templates and static assets]
        API[FastAPI - routers and services]
        CR[Crawler - Playwright child process]
        DP[Data processing - extraction, identity, filtering]
        HI[(Local JSON project history)]
        EX[Excel - workbook export and input]
        FI[Feishu integration - preview and execute]
        EN[Environment diagnosis and task coordination]
        IN[Installer and environment verifier]
        RT[Project-private Python, virtual environment, Chromium]
        UI --> API
        API --> CR
        CR --> DP
        DP <--> HI
        DP --> EX
        API --> FI
        EX --> FI
        API --> EN
        EN -->|Chromium repair only| IN
        IN --> RT
        RT -. supports .-> API
        RT -. supports .-> CR
    end
    CR <-->|Authorized browser session| TP[External tender platform]
    FI <-->|Authenticated REST API| FB[External Feishu Bitable]
    OP[Operator] --> UI
    OP -->|External first-install workflow| IN
```

## Responsibilities and boundaries

| Layer | Responsibility | Important limit |
| --- | --- | --- |
| Web UI | Search inputs, progress, history, configuration, preview and confirmation | A local operator surface, not a public customer portal |
| FastAPI | Route requests, coordinate task state, launch child processes, present results | In-process state and filesystem persistence; no distributed queue is claimed |
| Crawler | Navigate dynamic frames, handle result/detail transitions, paginate | Depends on platform layout and operator-authenticated access |
| Data processing | Prioritized field extraction, canonical identity, baseline and company filters | Deterministic matching; no ML-based entity resolution |
| History and Excel | Retain processed-project identities and reviewable exports | History JSON is distinct from result workbooks; no SQL database is claimed |
| Feishu integration | Read table state, calculate plan, add records and fill empty fields | Preview and write are separate calls, not an atomic transaction |
| Environment management | Verify private runtime/dependencies/browser; isolate repair stages | Web repair is Chromium-only; foundational repairs use the external installer |

## Data and trust boundaries

Browser session data belongs to the operator's local profile. Integration settings belong to local configuration. Result workbooks and history belong to the local workflow. None of these artifacts are included in this repository.

The Feishu execution gate checks that the source still matches the previewed signature. It does not freeze external table contents between preview and execution. Company review and an authorized test target remain important before live writes.

Repair coordination rejects conflicts with collection, synchronization, and login activity. This describes the implemented repair boundary, not a claim that every possible task combination has transactional isolation.
