# Validation and Limitations

## Evidence reviewed

Preparation used read-only inspection of the existing Mac application's crawler, company-field priorities, project-history identity, workbook export, Feishu planning/execution, FastAPI routes and services, configuration persistence, installer/verifier, process helpers, and configuration tests.

Existing RC1 acceptance, release-status, and later Web-configuration change records were consulted for status. Their contents were summarized into new prose; the internal documents and source files are not distributed here. Older setup prose contains earlier-stage statements, so current implementation and later acceptance records were used to avoid describing completed browser deployment as pending.

No installation, browser launch, live collection, account login, Feishu connection test, or Feishu write was performed during showcase preparation. Application tests were not rerun. Showcase checks concern documentation integrity and disclosure safety only.

## Status vocabulary

- **Implemented:** behavior is present in the inspected source; this alone does not establish successful end-to-end operation.
- **Recorded pass:** existing acceptance evidence reports a successful check. This is not a fresh independently reproduced result.
- **Pending:** the available evidence does not establish completion.
- **Proposed:** an improvement, not a current feature.

## Validation matrix

| Capability or platform | Status | Evidence boundary |
| --- | --- | --- |
| Apple Silicon private runtime, virtual environment, dependencies | Recorded pass | RC1 clean-install and verifier records on one newer macOS configuration |
| Visible Chromium and persistent context | Recorded pass | Local browser smoke tests, not authenticated site collection |
| Web pages and environment APIs | Recorded pass | Local HTTP and environment acceptance |
| Chromium diagnosis and Web repair | Recorded pass | Missing/damaged browser and repair checks in existing records |
| Repeated install and interrupted-state recovery | Recorded pass | Existing staged installation/fault checks |
| Startup, repeat startup, and project-aware Web stop | Recorded pass | Existing process-ownership and foreign-port checks |
| Repair/task conflicts | Recorded pass | Existing recorded checks include mock-based cases; not all task interleavings |
| Feishu Web configuration | Implemented; earlier offline pass recorded | Isolated configuration tests and prior release-preparation result; no live integration proof |
| Keyword search, pagination, history, extraction, Excel | Implemented; static regression recorded | Current code inspection and RC1 static checks; authenticated RC1 business flow pending |
| Feishu preview and execution | Implemented; live validation pending in RC1 evidence | Code gates inspected; no live write performed for this showcase |
| macOS 14 minimum | Declared support boundary | Every OS release at or above the minimum has not been tested |
| Intel macOS | Pending hardware validation | Runtime/dependency matrix prepared; no confirmed Intel acceptance |
| Windows | Owner-reported compatibility work in progress | No Windows code or logs inspected; no Windows support claim |
| Linux and other platforms | Not validated | Out of this showcase's verified scope |

## Limits worth discussing in an interview

1. A selected award candidate is not necessarily a final contract winner.
2. Company-name equality does not resolve aliases, subsidiaries, or consortium identities.
3. A processed-project history can skip later notice changes; refresh semantics need a deliberate policy.
4. Damaged or unavailable history can reduce deduplication reliability.
5. Workbooks, history files, and API writes are not one transaction.
6. A source signature guards against changed local input, not concurrent external table edits.
7. The UI is a local trusted-operator tool, not a hardened public or multi-tenant deployment.
8. Platform layout changes, session expiry, network access, and OS/browser compatibility remain operational dependencies.

No throughput, conversion, revenue, cost-saving, user-count, or efficiency-percentage metrics have been substantiated or included.
