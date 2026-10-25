# Technical Highlights

These descriptions were written from a read-only inspection of the existing implementation and its acceptance records. They are explanatory summaries, not published source or proof of live end-to-end operation. See [Validation](VALIDATION.md) for evidence limits.

## 1. Dynamic pages, iframes, and micro-frontend navigation

**Problem:** A search page inside a workbench may exist in an embedded frame rather than the top-level page. A loaded outer page does not establish that the search application is ready.

**Solution:** Identify the expected search-frame URL, wait for frame attachment and visible controls, and operate within the resolved page/frame scope. Detail parsing inspects relevant detail scopes; returning from a detail attempts to recover a live search scope.

**Engineering value:** Readiness is based on relevant UI state instead of a single fixed sleep. The abstraction addresses an embedded application boundary without claiming control over the site's micro-frontend internals. Selectors and URLs still need maintenance when the site changes.

## 2. Persistent browser context

**Problem:** Repeating login interrupts a desktop workflow, but bypassing authentication or human verification is inappropriate.

**Solution:** Launch visible Chromium with a project-local persistent context. The operator completes legitimate login and required verification; subsequent sessions can reuse local state when still valid.

**Engineering value:** Keeps authentication under operator control and separates the tool's browser from a personal browser installation. Expired sessions still require manual action. Profiles are sensitive and must never enter a public repository.

## 3. Historical project identity

**Problem:** The same notice can reappear across searches or through workbench-wrapped URLs.

**Solution:** Prefer a project data ID when present, use canonical detail URLs, and fall back to title/time identity when stronger identifiers are unavailable. Load processed keys before collection and skip known projects before opening details. Local history writes use a temporary file, replacement, and backup behavior.

**Engineering value:** Reduces repeat inspection and preserves incremental workflow state. Fallback identities can collide, and previously processed notices may change; this is not a complete change-detection or recovery guarantee.

## 4. Pagination and bounded work

**Problem:** A desired number of useful companies is different from the number of notices inspected, particularly when history and invalid records are present.

**Solution:** Track cumulative scan work separately from valid results. Navigate the next result page, verify page progress using result identity/page state, and stop at the useful-result target, scan limit, or end of results. Pagination retries are bounded.

**Engineering value:** Makes collection limits explicit and keeps a run from expanding indefinitely. The implementation does not guarantee that every requested target can be met.

## 5. Prioritized company-field extraction

**Problem:** Purchasing organizations and suppliers appear near each other. A candidate name can also be mistaken for a confirmed winner.

**Solution:** Prefer structured label/value relationships, then fall back to nearby text matching. The inspected priority is award candidate, winning entity, winning/deal supplier, deal supplier, then supplier name. Reject missing or access-placeholder values and retain the chosen source field and notice link.

**Engineering value:** Makes extraction auditable and avoids presenting an unexplained company string as established fact. Candidate status requires human interpretation; there is no claim of perfect extraction accuracy.

## 6. Deduplication across results, Excel, and Feishu

**Problem:** Different notices may produce the same company, and a collaboration table may already contain a manually curated entry.

**Solution:** Compare new results with baseline company names, canonical links, and company/project pairs. Read structured workbook candidates for synchronization. Within the integration, use trimmed company-name equality, keep the first repeated input company, skip duplicate additions, and plan only empty-field completion for existing entries.

**Engineering value:** Protects curated fields and reduces duplicate records. Name equality is not legal-entity resolution; spelling variants, subsidiaries, or joint bidders need review. Excel is an exchange artifact, not an independent entity-matching service.

## 7. Dry-run and explicit confirmation

**Problem:** A preview can become stale after an export changes, and external writes are harder to undo than local inspection.

**Solution:** Build an add/fill/skip plan before execution. The Web flow requires a completed preview, a successful connection check, an unchanged source signature, and a distinct operator write action. The CLI also separates default preview from explicit execution.

**Engineering value:** Gives the operator an inspectable decision point. Dry-run can read external records; it is not an offline mode. Separate field-initialization operations can also write and should not be confused with preview. External table changes between preview and write remain a concurrency limitation.

## 8. Project-private Python deployment

**Problem:** A new colleague's Mac may lack Python, pip, and a development toolchain.

**Solution:** Bootstrap a version-pinned python-build-standalone runtime, selecting an artifact for the detected architecture and checking its checksum. Create a project-root virtual environment and install per-architecture, hash-locked dependencies. Start the application with the project interpreter.

**Engineering value:** Reduces dependence on workstation configuration and makes environment selection explicit. The Mac workflow is designed without Homebrew, global Python changes, or administrator-level package installation. Intel hardware validation is still pending.

## 9. Private Playwright Chromium

**Problem:** Browser automation can fail when an unrelated system browser changes version or when only a headless executable is available for a manual-login workflow.

**Solution:** Set a project-local Playwright browser path, install the Chromium build paired with the pinned Playwright package, and verify visible and persistent-context launch behavior.

**Engineering value:** Keeps the browser dependency under project control and supports manual login. The browser is a substantial network download; private placement does not remove OS-version and CPU compatibility requirements.

## 10. Environment diagnosis and targeted repair

**Problem:** A saved success flag can outlive a deleted browser, a broken dependency, or a moved project directory.

**Solution:** Verify actual runtime, virtual-environment linkage, package versions, and browser files instead of trusting state alone. Show local environment health separately from login/configuration status. Permit Web-based Chromium repair only when foundational layers are healthy; route foundational repair to the external installer.

**Engineering value:** Produces actionable recovery guidance without replacing the interpreter underneath a running Web process. The Web repair feature is not a general-purpose package installer.

## 11. Task coordination and safe stopping

**Problem:** Repairing shared browser files during a collection or login can corrupt a run. Stopping a service by port alone can terminate an unrelated program.

**Solution:** Coordinate repair against active collection, synchronization, and login tasks; reject repeated conflicting repairs. The Web stop script checks recorded PID, command, working directory, project ownership, and port before signalling a process, and rechecks ownership before escalation. Collection stopping separately terminates its tracked child process, with a profile-specific browser cleanup fallback.

**Engineering value:** Ties operational actions to project ownership rather than assuming a port identifies the right process. These are local coordination measures, not distributed scheduling or a universal concurrency guarantee.

## 12. Idempotent installation and recovery

**Problem:** Network failures and interrupted installation can leave a mixture of healthy and incomplete components. Reinstalling every layer makes recovery expensive and confusing.

**Solution:** Verify components before reuse, retain staged installation state, write state atomically, use installation/repair locks, validate downloads, and bound retries. Stage replacement and quarantine/restore behavior support recovery. A dependency failure does not imply a runtime redownload; a browser failure does not imply rebuilding every layer.

**Engineering value:** Makes repeat execution useful as diagnosis and recovery. Some recovery paths replace files or remove invalid temporary artifacts, so failure reporting and stage boundaries matter. Recorded Mac acceptance is not proof against every power-loss or filesystem scenario.
