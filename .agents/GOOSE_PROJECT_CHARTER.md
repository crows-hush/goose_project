# Goose CRUD Service — Project Charter & Control Log

**Document version:** 0.1  
**Last updated:** 2026-10-23  
**Project status:** Foundation / language-learning  
**Overall health:** Green for learning; architecture choices remain unproven  
**Project owner:** User  
**Program manager / architecture supervisor / code reviewer:** ChatGPT within this conversation

> This is the working baseline. Update the status and append a log entry when new evidence is supplied (code, test output, a decision, or deployment result). It is not a background monitoring system; progress is maintained when we work on the project together.

## 1. Mission

Build a small, robust CRUD HTTP API primarily in Goose, backed by a modest persistent database, deployable to a free or low-cost Linux host, and optimized from measured results rather than assumed benchmark performance.

## 2. Non-negotiable constraints

1. **Goose-first / pure Goose application:** all application behavior, routing decisions, input validation, data transformation, and API response policy should be written in Goose.
2. **C is an infrastructure boundary:** use existing C libraries where required (for example HTTP transport and SQLite). Custom C glue should remain small, documented, testable, and limited to interfacing with native APIs. Do not move business rules into C.
3. **Prove before committing:** validate Goose FFI, HTTP callback flow, and database binding with tiny prototypes before building the full service.
4. **Low-cost deployment:** target one small Linux VM with persistent local storage for the first production-like deployment. Free-tier eligibility and availability are not assumed.
5. **Performance claims must be measured:** define workloads and record throughput, p50/p95/p99 latency, errors, CPU, memory, and database contention.
6. **No code is approved just because it compiles:** tests, error paths, security, data lifetime, and operational behavior must be reviewed.

## 3. Current baseline (user-reported)

| Area | Status | Evidence / next action |
|---|---|---|
| Goose compiler | Working locally | User reports Goose runs and compiles code. Record compiler version/commit and platform when available. |
| Basic syntax | In progress | Continue tutorial and sample exercises; do not start with an application-sized codebase. |
| Application repository | Not yet reported | Establish a separate project repository/directory and version control. |
| HTTP proof of concept | Not started | Select a candidate C library only after confirming Goose FFI/callback shape. |
| Database proof of concept | Not started | Start with SQLite; test basic prepared-statement operations through FFI. |
| CI/tests | Not started | Add syntax/typecheck, unit tests, integration tests, and build checks incrementally. |
| Deployment/benchmarks | Not started | No throughput or cost target is verified yet. |

## 4. Architecture baseline (v0.1 — proposed, not yet proven)

```text
Clients
  │ HTTPS
  ▼
Reverse proxy (TLS, request limits, timeouts; e.g. Caddy)
  │ private local HTTP connection
  ▼
HTTP transport package (existing C library + minimal C glue)
  │ bounded request bytes / status / response bytes
  ▼
Goose application
  ├── routing and method/path handling
  ├── JSON parsing and serialization (Goose stdlib where suitable)
  ├── validation and error mapping
  ├── CRUD business rules
  └── repository interface
  │ narrow FFI
  ▼
Database adapter (SQLite C API + minimal C glue)
  │ prepared statements / transactions
  ▼
SQLite file on persistent local disk
```

### Architectural rules

- The C HTTP layer parses transport framing and exposes method, path, headers, and bounded body bytes. It must not decide application semantics.
- Goose owns route matching, validation, status-code policy, business logic, and response JSON.
- The SQLite boundary binds parameters and translates native results/errors. Never concatenate untrusted input into SQL.
- The first architecture should avoid assuming safe concurrent entry into Goose from multiple arbitrary C threads. Start with a single event loop or a documented serialization boundary; benchmark and validate before adding worker concurrency.
- Do not put the SQLite file on an ephemeral filesystem or shared network filesystem. Keep backup/restore separate from the VM.
- Reverse proxy, application, and database all run on one host initially. Horizontal scaling is not in v1 scope.
- All request sizes, result page sizes, and timeouts must have explicit limits.

### Proposed endpoint set

- `GET /health`
- `GET /items?limit=...&cursor=...`
- `GET /items/{id}`
- `POST /items`
- `PATCH /items/{id}`
- `DELETE /items/{id}`

Initial record fields: `id`, `title`, `body`, `created_at`, `updated_at`. Authentication/authorization requirements are not yet decided; public deployment must not happen until they are.

## 5. Decisions and open architecture questions

| ID | Decision / question | Current state | How we resolve it |
|---|---|---|---|
| ADR-001 | What does “pure Goose” mean operationally? | Proposed: all app logic Goose; C libraries and minimal glue allowed; no C business rules. | Confirm by continuing with this rule unless owner changes it. |
| ADR-002 | HTTP integration mechanism and C library | Open | Make a minimal end-to-end proof: one route, bounded request, call from C into exported Goose handler, response. Compare a small event-driven library against simpler alternatives. |
| ADR-003 | SQLite binding approach | Open | Prototype open/close, prepared SELECT, INSERT, UPDATE, DELETE, transaction and error mapping through Goose FFI. Prefer a maintainable package over handwritten low-level ABI code where available. |
| ADR-004 | SQLite or client/server database | SQLite proposed for v1 | Reconsider only if write contention, multi-host writes, or operational requirements justify PostgreSQL or similar. |
| ADR-005 | Deployment provider | Open | Compare current free-tier eligibility, persistent storage, region latency, quotas, and idle/sleep behavior at deployment time. |
| ADR-006 | Concurrency model | Single serialized event loop proposed initially | Validate export/reentrancy/thread restrictions; use measurement to decide if concurrency architecture needs to change. |
| ADR-007 | Authentication and data sensitivity | Open | Define before public exposure. At minimum decide whether endpoints are public or require tokens/user identity. |

## 6. Roadmap and deliverables

### Phase 0 — Developer workflow and language foundation (current)

**Deliverables**
- [ ] Record Goose version/commit, OS, architecture, build command, and test commands.
- [ ] Complete small exercises covering scalars, structs, enums, arrays, slices, references/lifetimes, errors, and JSON.
- [ ] Run upstream Goose tests and samples, recording pass/fail output.
- [ ] Create the application repository, `.gitignore`, and `README.md`.

**Exit criteria:** the developer can write and typecheck a small Goose program without copying an example blindly, and can explain its data ownership/lifetime.

### Phase 1 — FFI feasibility spikes

**Deliverables**
- [ ] `ffi_smoke`: Goose calls one C function and receives a result.
- [ ] `goose_callback_smoke`: a C-owned control flow invokes an exported Goose function through the supported generated ABI.
- [ ] `sqlite_smoke`: Goose calls a C adapter using SQLite prepared statements for one read and one write.
- [ ] A short note documenting supported FFI types, byte-slice semantics, ownership, and error handling.

**Exit criteria:** each spike builds reproducibly with `goose build`, works in a native executable, and has negative-path tests. Don't assume APIs from examples before compiling against the current checkout.

### Phase 2 — HTTP vertical slice

**Deliverables**
- [ ] HTTP adapter starts and stops cleanly.
- [ ] `GET /health` returns a bounded JSON response.
- [ ] Request body limits, timeouts, and consistent error mapping exist.
- [ ] Local integration test sends a real HTTP request to the native process.

**Exit criteria:** end-to-end HTTP works locally with Goose retaining application route and response logic.

### Phase 3 — CRUD + persistence

**Deliverables**
- [ ] Schema/migrations and prepared statements.
- [ ] All five CRUD/read endpoints work.
- [ ] Input validation, pagination, not-found behavior, and database errors tested.
- [ ] Tests for malformed JSON, oversized bodies, invalid IDs, duplicate fields, missing fields, and Unicode.
- [ ] Backup and restore instructions plus a tested restore.

**Exit criteria:** automated CRUD integration tests pass against a temporary database and data survives application restart.

### Phase 4 — Security and operational hardening

**Deliverables**
- [ ] Define public/private endpoint policy and implement authentication if needed.
- [ ] Reverse proxy/TLS, private app port, unprivileged service account, restart policy.
- [ ] Structured or consistent logs; no secrets or full sensitive request bodies in logs.
- [ ] Rate/request limits, graceful shutdown, disk-space/SQLite failure handling.
- [ ] Build and runtime versions recorded.

**Exit criteria:** documented deployment can be reproduced from a clean Linux VM, and restore/restart/error procedures are tested.

### Phase 5 — Benchmark and tune

**Deliverables**
- [ ] Published workload definition and test script.
- [ ] Baseline measurements for health, list/read, create/update/delete, and mixed load.
- [ ] CPU/memory/latency/error/busy-timeout measurements.
- [ ] One-change-at-a-time tuning log.
- [ ] Decision record stating the observed bottleneck and next action.

**Exit criteria:** workload, hardware, compiler settings, database size, and results are reproducible. No unqualified “maximum traffic” claim.

## 7. Quality gates for code review

For each code review, I will separate findings into **Blocker**, **High**, **Medium**, and **Suggestion**, and report file/line where supplied.

1. **Correctness:** control flow, boundary values, return values, status codes, null/empty inputs, partial failures.
2. **Goose memory model:** correct array type, owner/root lifetime, slices and references, no use of logically stale pool slots, bounded per-request data.
3. **FFI safety:** exact ABI signature, supported C representations, byte length versus NUL termination, buffer capacity, no retained pointers beyond their owner lifetime.
4. **Database:** prepared statements, binding, transactions, schema/index alignment, busy handling, error-code mapping, migration/backup implications.
5. **HTTP/security:** maximum body/header sizes, path parsing, method handling, error responses, timeouts, authentication/authorization, rate limits.
6. **Concurrency:** no assumptions about thread safety or callback reentrancy that have not been proven; SQLite write contention handled.
7. **Tests:** positive and negative tests, regression tests for prior defects, reproducible build/run commands.
8. **Operations/performance:** logs, shutdown, resource limits, profiling before optimization, load-test evidence.
9. **Pure Goose constraint:** business and route logic remains in Goose; any C code is justified as adapter/glue and reviewed for scope creep.

Review format: summary → blockers/high-risk issues → prioritized findings with code references → suggested patch or design → tests required → accept/revise decision.

## 8. Initial risk register

| Risk | Impact | Mitigation |
|---|---|---|
| Goose language/API evolves quickly | Examples or package APIs may change | Pin Goose commit/version; compile minimal spikes; re-run test suite after upgrades. |
| FFI or C callback ABI is misunderstood | Crashes, invalid slices, lifetime violations | Prove interface before application development; document signatures; keep buffers caller-owned and bounded. |
| HTTP support requires more C than expected | Violates project intent / maintenance cost | Keep a clear “Goose business logic” boundary; compare library/package options; cap custom C glue. |
| SQLite single-writer bottleneck | Write latency rises under concurrency | Measure contention; short transactions and WAL; move to client/server DB only if evidence requires it. |
| “High traffic” remains unquantified | Cannot know whether architecture meets the goal | Agree a workload and latency/error targets before performance sign-off. |
| Free-tier compute/storage unavailable or ephemeral | Downtime or data loss | Verify provider constraints live; persist DB; off-host backup; deploy on a small paid VM if needed. |
| Unauthenticated CRUD exposed publicly | Data exposure or abuse | Determine access model before deployment; keep private until security gate passes. |

## 9. Progress log

| Date | Entry | Status / evidence |
|---|---|---|
| 2026-10-23 | Project charter opened. User reports Goose works and compiles locally; syntax learning is ongoing. | Baseline is user-reported; compiler version and local OS not yet recorded. |
| 2026-10-23 | Reviewed current upstream README, tutorial, and package docs. They document native executable builds (`goose build`), C interop, package-local C sources, and an early-stage language with some OS/library access intentionally out of scope. | Architecture stays proposed until FFI/HTTP/SQLite spikes pass. |

## 10. Atomized task backlog

The dependency-ordered, task-by-task execution plan is maintained separately in [GOOSE_ATOMIZED_TASKS.md](GOOSE_ATOMIZED_TASKS.md). Each task defines a goal, steps, acceptance criteria, dependencies, and status. The current task is **G-001 — Record a reproducible local build baseline**.

## 11. Working agreement

- User brings code, terminal output, questions, or decisions; I review and update this baseline in the conversation and the project log when requested or when a milestone changes.
- I will not silently assume code compiles or tests pass. Findings will distinguish verified facts, proposals, and unknowns.
- I will challenge complexity that does not serve v1, especially premature concurrency, custom allocators, custom HTTP, and speculative optimization.
- A roadmap phase advances only when its exit criteria have evidence.
- Before a risky architecture choice, build a minimal proof rather than committing the whole project to it.

## 12. Upstream references checked

- https://github.com/aardappel/goose
- https://github.com/aardappel/goose/blob/master/docs/tutorial.md
- https://github.com/aardappel/goose/blob/master/docs/packages.md
- https://github.com/aardappel/goose/blob/master/docs/index.md


## 13. Progress log update

| Date | Entry | Status / evidence |
|---|---|---|
| 2026-10-23 | User requested atomized tasks with sequential completion and review. | Added `GOOSE_ATOMIZED_TASKS.md`; current task G-001. Cross-chat memory is unavailable while user Memory is disabled. |
