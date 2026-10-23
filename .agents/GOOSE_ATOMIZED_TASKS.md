# Goose CRUD Service — Atomized Task Backlog

**Version:** 1.0  
**Last updated:** 2026-10-23  
**Project owner:** User  
**Program manager / architecture supervisor / reviewer:** ChatGPT in this conversation  
**Current state:** Goose runs locally and compiles code; syntax learning is ongoing (user-reported).

## How to use this backlog

1. Work on tasks in ID order. Do not skip a task just because later work seems more exciting.
2. Complete only the current task unless we explicitly agree to run a small, independent spike in parallel.
3. Send me the task ID, relevant source files, the exact command you ran, and its complete output (or the smallest useful excerpt). Never send credentials or secrets.
4. I will review the evidence, classify findings as Blocker / High / Medium / Suggestion, update the status, and identify the next task.
5. A task is **Done** only when its acceptance criteria have evidence. “It compiles” is necessary but not always sufficient.

### Status legend

- **DONE** — evidence received and acceptance criteria met.
- **IN PROGRESS** — currently being worked on.
- **READY** — dependencies met; can be started.
- **BLOCKED** — waiting on an issue, decision, or dependency.
- **NOT STARTED** — not yet active.

## Current control state

| Item | Status | Evidence |
|---|---|---|
| Goose runs and compiles locally | DONE (reported) | User-reported; exact version/build details not captured yet. |
| Basic syntax | IN PROGRESS | User-reported. |
| HTTP, C FFI, SQLite, deployment, performance | NOT STARTED | No test evidence yet. |

**Current task:** `G-001 — Record a reproducible local build baseline`.

---

## Phase 0 — Reproducible setup and language fluency

### G-001 — Record a reproducible local build baseline
**Status:** READY  
**Goal:** Capture enough detail to reproduce the current working setup.

Steps:
1. From the Goose repository, record the current commit (`git rev-parse HEAD`) if it is a Git checkout.
2. Record OS, CPU architecture, C compiler name/version, and how Goose is invoked (binary path or build method).
3. Run the smallest Goose program that you already know works; save the exact command and output.
4. Put the details in `PROJECT_LOG.md` or `README.md` in the project workspace.

Acceptance:
- The Goose revision/version (or “unknown/not exposed”), OS/architecture, compiler, command, and output are recorded.
- No secrets, tokens, or personal paths that should stay private are included.

### G-002 — Run one upstream tutorial sample
**Status:** NOT STARTED  
**Depends on:** G-001  
**Goal:** Confirm that the local setup can run a known example.

Steps:
1. Read the current `samples/README.md` and `docs/tutorial.md` in the checkout.
2. Run `samples/01_tour.goose` using the invocation documented by that checkout.
3. Save the command and output. If it fails, capture the complete compiler error and stop here.

Acceptance:
- The sample runs successfully, or its failure has a reproducible error that we can diagnose.
- The actual command is recorded rather than assumed.

### G-003 — Create the project workspace and Git history
**Status:** NOT STARTED  
**Depends on:** G-002  
**Goal:** Keep application work separate from the Goose compiler checkout.

Steps:
1. Create a new `items-api` directory/repository.
2. Add `README.md`, `PROJECT_LOG.md`, `.gitignore`, `src/`, `tests/`, and `docs/`.
3. Add only source/configuration files; ignore generated binaries, object files, databases, and local secrets.
4. Make the initial Git commit.

Acceptance:
- A clean `git status` after the commit.
- The README explains the goal and current stage.

### G-004 — Write a basic-syntax exercise
**Status:** NOT STARTED  
**Depends on:** G-003  
**Goal:** Demonstrate that basic syntax is understood rather than copied.

Steps:
1. In `src/exercises/01_basics.goose`, use `let`, `var`, scalar types, a function, a struct, and a loop.
2. Compute the sum of a small fixed set of integers and print the result.
3. Add a comment explaining one design choice in your own words.

Acceptance:
- It compiles and runs.
- The result matches a manually calculated expected value.
- You can explain the function signature and the difference between `let` and `var`.

### G-005 — Practice the array family
**Status:** NOT STARTED  
**Depends on:** G-004  
**Goal:** Learn array types and growth behavior.

Steps:
1. Create a small example using one fixed array, one frozen/sized array, and one grow-only array.
2. Push values into the grow-only array and iterate through it.
3. In comments, state which values can change size and which can’t.
4. Consult the current tutorial/reference for the exact syntax; don't guess based on another language.

Acceptance:
- The example compiles and prints the expected values.
- The comments correctly describe the arrays’ size/growth properties.

### G-006 — Practice strings and byte slices
**Status:** NOT STARTED  
**Depends on:** G-005  
**Goal:** Treat strings as bytes and lengths explicitly.

Steps:
1. Write a function that accepts a byte slice and reports its length.
2. Exercise an empty string, an ASCII string, and a string containing non-ASCII UTF-8.
3. Record the difference between byte count and displayed character count where relevant.

Acceptance:
- All examples run and the observed lengths are recorded.
- No code assumes that a byte slice is NUL-terminated.

### G-007 — Practice references and scopes
**Status:** NOT STARTED  
**Depends on:** G-006  
**Goal:** Build correct intuition for references and lifetimes.

Steps:
1. Read the tutorial sections on where values live, slices, references, and nested data.
2. Adapt a small reference/link example from an upstream sample.
3. Introduce one intentionally invalid lifetime/reference case in a throwaway file and capture the compiler diagnostic.
4. Explain why the invalid case is rejected.

Acceptance:
- The valid example runs.
- The invalid example is rejected by the compiler for the expected reason, or the actual diagnostic is reviewed.
- The explanation is in your own words.

### G-008 — Practice enums and error propagation
**Status:** NOT STARTED  
**Depends on:** G-007  
**Goal:** Represent success/failure without turning every failure into a panic.

Steps:
1. Read the current tutorial/reference sections on enums and error handling.
2. Write a tiny function that validates a value and returns an explicit success/failure outcome using documented Goose patterns.
3. Test success, invalid input, and a boundary value.

Acceptance:
- Three test cases behave as expected.
- Failure is distinguishable from a valid result.

### G-009 — Complete a JSON learning exercise
**Status:** NOT STARTED  
**Depends on:** G-008  
**Goal:** Read and write JSON using Goose's own library.

Steps:
1. Read the current `stdlib/json` reference and upstream JSON sample (currently listed among the numbered samples).
2. Parse a small object with a string and integer.
3. Serialize a response object using the documented builder/writer API.
4. Test malformed JSON, a missing required field, and escaped characters.

Acceptance:
- Valid input is parsed and output is valid JSON.
- Invalid input fails in a controlled way.
- The exact API used compiles on the pinned Goose revision.

### G-010 — Verify upstream tests and samples
**Status:** NOT STARTED  
**Depends on:** G-009  
**Goal:** Establish a local regression baseline before integrating C.

Steps:
1. Find the test/sample commands documented by the current Goose checkout.
2. Run the documented suite(s) that are feasible locally.
3. Save pass/fail output, skipped tests, and any environment-specific blockers.

Acceptance:
- The project log clearly distinguishes passing tests, failing tests, and commands not run.
- No test command is copied from this backlog without verifying it exists in the current checkout.

**Phase 0 exit gate:** G-001 through G-010 are accepted; basic exercises and test evidence are in the project log.

---

## Phase 1 — Prove C interoperability (without HTTP or database complexity)

### G-011 — Call one C scalar function from Goose
**Status:** NOT STARTED  
**Depends on:** Phase 0 exit gate  
**Goal:** Prove the smallest `extern fn` path.

Steps:
1. Add `src/ffi_smoke.goose` declaring an `extern fn` for a C function that adds two small integers.
2. Add `src/ffi_smoke.c` implementing the function with the exact declared ABI.
3. Add the program-specific `.build` file using the current package-guide syntax for the C source.
4. Build and run the program.

Acceptance:
- Native build succeeds.
- Several inputs produce correct results.
- The C function signature and Goose declaration match exactly.

### G-012 — Test byte-slice interop and lengths
**Status:** NOT STARTED  
**Depends on:** G-011  
**Goal:** Prove that byte data can cross the language boundary without guessing about string layout.

Steps:
1. Add a C helper receiving a byte slice and its length through Goose's supported slice ABI.
2. Test empty bytes, embedded zero byte, and ordinary UTF-8 bytes.
3. Do not keep the pointer after the call and do not treat it as NUL-terminated unless the code creates such a copy.

Acceptance:
- Returned lengths and bytes match the inputs.
- Slice ABI details are documented in `docs/ffi-contract.md`.

### G-013 — Call exported Goose code from C
**Status:** NOT STARTED  
**Depends on:** G-012  
**Goal:** Prove the host-to-Goose direction needed by an HTTP library callback.

Steps:
1. Export a simple Goose function with an integer argument and integer return value.
2. Generate the header using the current `--standalone`/`--header` workflow documented by the checked-out compiler.
3. Write a tiny C `main` that calls `goose_init()` and then invokes the exported function according to the generated header.
4. Build, run, and inspect the generated header rather than manually inventing ABI declarations.

Acceptance:
- C can call the Goose function and receive the expected result.
- Initialization order and ownership rules are recorded.

### G-014 — Document the FFI contract
**Status:** NOT STARTED  
**Depends on:** G-013  
**Goal:** Prevent unsafe or accidental complexity at the native boundary.

Steps:
1. Document supported argument/return types, slice representation, builder behavior, initialization, and error returns.
2. Decide which side owns request/response buffers and how long each remains valid.
3. State that C glue cannot implement application/business rules.

Acceptance:
- `docs/ffi-contract.md` contains working examples and explicit lifetime rules.
- The examples compile against the pinned Goose version.

**Phase 1 exit gate:** both call directions work in native builds, and the interface/ownership contract is documented.

---

## Phase 2 — Prove SQLite persistence

### G-015 — Open SQLite and initialize schema through a C adapter
**Status:** NOT STARTED  
**Depends on:** Phase 1 exit gate  
**Goal:** Open a temporary database and initialize the schema.

Steps:
1. Check that SQLite development headers and library are available locally.
2. Implement only `db_open`, `db_init_schema`, and `db_close` in C.
3. Call them through Goose and propagate errors as explicit return codes.
4. Use a temporary test path; do not use a production or personal database file.

Acceptance:
- The test creates the database and table, closes cleanly, and can reopen it.
- Open/schema failures are reported rather than ignored.

### G-016 — Add parameterized INSERT and SELECT
**Status:** NOT STARTED  
**Depends on:** G-015  
**Goal:** Prove safe write and read through the adapter.

Steps:
1. Implement create-item and read-item operations using prepared statements and bound parameters.
2. Test ordinary text, quotes, Unicode, nonexistent IDs, and database errors.
3. Return structured status codes and record the mapping.

Acceptance:
- Values round-trip correctly.
- Values containing quotes are treated as data, not SQL syntax.
- Not found and database error are distinguishable.

### G-017 — Add UPDATE, DELETE, and persistence test
**Status:** NOT STARTED  
**Depends on:** G-016  
**Goal:** Complete database-level CRUD.

Steps:
1. Add parameterized update and delete operations.
2. Test updating/deleting existing and missing IDs.
3. Close and reopen the database; verify committed rows persist.
4. Add a transaction test for a failure that should roll back.

Acceptance:
- CRUD behaves correctly for existing/missing rows.
- Persistence across process restart is demonstrated.
- Transaction rollback test passes.

### G-018 — Decide initial SQLite concurrency settings
**Status:** NOT STARTED  
**Depends on:** G-017  
**Goal:** Choose safe, modest initial database settings.

Steps:
1. Review current SQLite documentation for WAL, synchronous mode, and busy timeout.
2. Apply the initial settings in code/startup migration rather than relying on manual shell state.
3. Document why WAL is appropriate for a single-host deployment and that it still has one writer at a time.

Acceptance:
- Settings and limitations are recorded in an architecture decision note.
- Basic database tests pass with the selected settings.

**Phase 2 exit gate:** database CRUD and persistence tests pass without network or HTTP code.

---

## Phase 3 — HTTP integration and the first vertical slice

### G-019 — Select the HTTP adapter with evidence
**Status:** NOT STARTED  
**Depends on:** Phase 1 exit gate  
**Goal:** Choose a maintained C HTTP library without committing the whole application prematurely.

Steps:
1. Compare at least two realistic options for maintenance, HTTP correctness, licensing, Linux packaging, body limits, timeouts, event-loop support, and how callbacks reach Goose.
2. Confirm whether a C callback into Goose can be executed safely under the selected runtime and build model.
3. Record the chosen library and why custom HTTP parsing is out of scope.

Acceptance:
- A short ADR records options, license/build considerations, callback model, and decision.
- The choice is not based on benchmark headlines alone.

### G-020 — Start and stop a minimal HTTP server
**Status:** NOT STARTED  
**Depends on:** G-019  
**Goal:** Verify the native HTTP library works before routing application logic.

Steps:
1. Start a local-only HTTP server in C with one fixed test response.
2. Add clean startup failure reporting and graceful shutdown.
3. Verify it binds only to the intended local interface/port.

Acceptance:
- A local HTTP client receives the test response.
- Ctrl-C/service shutdown exits cleanly.
- No external network exposure is introduced.

### G-021 — Invoke Goose for `GET /health`
**Status:** NOT STARTED  
**Depends on:** G-013, G-020  
**Goal:** Complete the first Goose-backed HTTP request.

Steps:
1. Pass method, path, and bounded request-body bytes into an exported Goose handler using the proven FFI contract.
2. Let Goose choose the status code and construct the response body.
3. Have C send those response bytes and status over HTTP.
4. Keep the application handler synchronous on the initial event-loop path until reentrancy/thread safety is proven.

Acceptance:
- `GET /health` returns the expected status and JSON.
- An integration test reaches the real native process.
- C handles transport; Goose owns route/response policy.

### G-022 — Implement `POST /items`
**Status:** NOT STARTED  
**Depends on:** G-009, G-017, G-021  
**Goal:** Create an item end to end.

Steps:
1. Parse JSON in Goose.
2. Validate required fields and length limits.
3. Call the SQLite adapter to insert with bound values.
4. Return `201` and the created identifier, or an appropriate error response.

Acceptance:
- Integration test creates and reads the new item.
- Invalid JSON/fields are rejected without corrupting the process.
- No SQL string concatenation with untrusted values occurs.

### G-023 — Implement `GET /items/{id}`
**Status:** NOT STARTED  
**Depends on:** G-022  
**Goal:** Read one persistent item via HTTP.

Steps:
1. Parse and validate the path ID in Goose.
2. Call the adapter and serialize the record.
3. Return a defined not-found response.

Acceptance:
- Existing, malformed, and missing IDs are covered by integration tests.

### G-024 — Implement `GET /items` with bounded pagination
**Status:** NOT STARTED  
**Depends on:** G-023  
**Goal:** List records without loading an unbounded table.

Steps:
1. Define a maximum page size (initial proposal: 100).
2. Implement stable ordering and cursor or keyset pagination; don't rely on unbounded `SELECT *` results.
3. Validate missing, negative, malformed, and oversized pagination parameters.

Acceptance:
- Tests show stable pages with no accidental duplicate/skipped rows under the stated consistency model.
- Response size is bounded by the page-size limit.

### G-025 — Implement `PATCH /items/{id}`
**Status:** NOT STARTED  
**Depends on:** G-024  
**Goal:** Update an existing item safely.

Steps:
1. Decide and document whether `PATCH` allows partial updates or requires the full representation.
2. Validate only allowed fields and use prepared statements.
3. Update `updated_at` consistently.

Acceptance:
- Existing and missing IDs, invalid JSON, and invalid field types are tested.
- Unspecified fields behave according to the documented policy.

### G-026 — Implement `DELETE /items/{id}`
**Status:** NOT STARTED  
**Depends on:** G-025  
**Goal:** Delete a record and report the outcome consistently.

Steps:
1. Parse ID and call the adapter.
2. Return the documented success status and missing-record behavior.
3. Test repeated deletion.

Acceptance:
- The item is no longer readable after successful deletion.
- Repeat/missing deletion behavior is stable and tested.

### G-027 — Build the API integration test suite
**Status:** NOT STARTED  
**Depends on:** G-022 through G-026  
**Goal:** Lock in observable API behavior.

Steps:
1. Test each endpoint with valid and invalid input.
2. Cover malformed JSON, missing fields, oversized body, unknown route/method, invalid IDs, Unicode, quotes, and DB failures.
3. Ensure tests use an isolated temporary database and clean it up.

Acceptance:
- Tests run with one documented command and pass on a clean build.
- Failures include enough context to reproduce.

**Phase 3 exit gate:** all CRUD routes work end to end against persistent SQLite, with automated integration tests.

---

## Phase 4 — Security, operations, and deployment

### G-028 — Define the access-control model
**Status:** NOT STARTED  
**Depends on:** G-027  
**Goal:** Decide whether API data is public or private before exposure.

Steps:
1. Document whether the first release is public read-only, authenticated CRUD, or single-user/private.
2. If private, implement and test authentication/authorization before public deployment.
3. Keep secrets out of source control and logs.

Acceptance:
- Unauthenticated/unauthorized behavior is explicit and tested.
- No public deployment until this task passes.

### G-029 — Add request and operational limits
**Status:** NOT STARTED  
**Depends on:** G-027  
**Goal:** Prevent unbounded input and graceful-failure gaps.

Steps:
1. Set maximum request body and header limits in the HTTP layer.
2. Configure connection/read/write timeouts and bounded response pages.
3. Add consistent logs, a request ID if feasible, graceful shutdown, and useful error reporting.
4. Ensure logs do not expose credentials or complete sensitive payloads.

Acceptance:
- Oversized/slow requests are rejected within defined limits.
- Shutdown and error cases do not corrupt the database.

### G-030 — Verify backup and restore
**Status:** NOT STARTED  
**Depends on:** G-017, G-029  
**Goal:** Prove the database can be recovered.

Steps:
1. Choose a backup method supported by SQLite and the actual deployment layout.
2. Back up to a location independent of the VM's primary disk where practical.
3. Restore into a fresh test location and run integrity/application checks.

Acceptance:
- A documented restore test passes; a backup file alone is not considered proof.

### G-031 — Deploy to a low-cost Linux VM
**Status:** NOT STARTED  
**Depends on:** G-028, G-029, G-030  
**Goal:** Reproduce the service on a persistent host.

Steps:
1. Select provider/region based on current price, capacity, persistent disk, region latency, and free-tier limits.
2. Build for the target architecture and record compiler/library versions.
3. Run under an unprivileged service account; keep the app port private behind a reverse proxy with HTTPS.
4. Configure restart policy, database location, logs, and backup schedule.

Acceptance:
- Fresh deployment procedure works from documented steps.
- Reboot/restart preserves data and the health check returns successfully.
- Security and restore gates are met before public exposure.

**Phase 4 exit gate:** deployment, access policy, operational limits, and tested recovery are documented.

---

## Phase 5 — Measure before optimizing

### G-032 — Define workload and SLO targets
**Status:** NOT STARTED  
**Depends on:** G-031  
**Goal:** Turn “maximum traffic” into a testable goal.

Steps:
1. Specify the read/write mix, payload size, database size, connection count, test duration, and client location.
2. Choose initial acceptable latency/error targets; mark them as project targets rather than universal facts.
3. Record the exact VM size and network/database conditions.

Acceptance:
- Another person can reproduce the test workload from the recorded specification.

### G-033 — Capture a baseline load test
**Status:** NOT STARTED  
**Depends on:** G-032  
**Goal:** Establish baseline capacity.

Steps:
1. Benchmark health, point read, list/pagination, create, update, delete, and mixed traffic separately.
2. Record throughput, p50/p95/p99 latency, error rate, CPU, memory, disk usage, and SQLite busy/timeout behavior.
3. Include test command, duration, tool version, server details, and database size.

Acceptance:
- Results are reproducible and include failures/limits, not only the best throughput number.

### G-034 — Optimize one measured bottleneck
**Status:** NOT STARTED  
**Depends on:** G-033  
**Goal:** Improve measured performance without losing correctness.

Steps:
1. Identify the dominant constraint from the baseline.
2. Propose one change with a prediction.
3. Run the same tests before and after.
4. Keep the change only if it improves the agreed metric without breaking acceptance tests or safety.

Acceptance:
- A short before/after report records the change and evidence.
- No unverified concurrency or caching strategy is introduced merely to chase requests/second.

### G-035 — Decide whether SQLite and the process model still fit
**Status:** NOT STARTED  
**Depends on:** G-034  
**Goal:** Make the next architecture decision from evidence.

Steps:
1. Evaluate observed write contention, latency, reliability, data size, and hosting costs.
2. Keep SQLite if it meets the targets; otherwise record a migration plan to a client/server DB.
3. Revisit worker/concurrency model only after confirming Goose callback/runtime constraints and measuring CPU saturation.

Acceptance:
- An architecture decision record explains whether to keep or change the database/process model and why.

**Phase 5 exit gate:** reproducible performance results and a documented bottleneck-driven next step.

---

## Review policy

When code is submitted, review in this order:

1. Does it compile with the pinned Goose/compiler/library versions?
2. Is the behavior correct for ordinary and boundary inputs?
3. Are Goose ownership, slices, references, and scopes used correctly?
4. Is the FFI signature exactly compatible, with valid buffer lengths and ownership?
5. Are SQL statements parameterized, transactions safe, and DB errors mapped correctly?
6. Are HTTP parsing, limits, status codes, and authorization safe?
7. Are tests present and reproducible?
8. Does C remain a small infrastructure adapter rather than becoming the application?

Findings will be classified as **Blocker**, **High**, **Medium**, or **Suggestion**. A task will not be marked done until the evidence meets its acceptance criteria.

## Progress log

| Date | Task / event | Outcome |
|---|---|---|
| 2026-10-23 | Baseline recorded | Goose runs and compiles locally; basic syntax is in progress (user-reported). |
| 2026-10-23 | Atomized backlog created | Tasks ordered by dependency; no implementation tasks marked done without code/test evidence. |

## Upstream references

- Tutorial: https://github.com/aardappel/goose/blob/master/docs/tutorial.md
- Language reference: https://github.com/aardappel/goose/blob/master/docs/reference.md
- Standard library: https://github.com/aardappel/goose/blob/master/docs/stdlib.md
- Package/build guide: https://github.com/aardappel/goose/blob/master/docs/packages.md
- Samples guide: https://github.com/aardappel/goose/blob/master/samples/README.md
