# Master Testing and Verification Plan

This document defines the comprehensive testing strategy, verification criteria, and test case specifications for **AUTOHEAL**.

---

## 1. Testing Philosophy and Safety Rules

1. **Isolation First**: All destructive or fault-injection testing must be conducted strictly within the designated `test_environment/` using isolated Docker test containers.
2. **Dry-Run by Default**: Initial testing for all remediation plans must execute with `DRY_RUN=true` to verify safety and logging before allowing any live container modifications.
3. **No Flaky Assertions**: Test criteria must evaluate precise deterministic metrics (container exit codes, health status, schema validation passes/fails, database records).
4. **Deterministic Gate Enforcement**: Every test case testing the Validator must assert that an invalid or unallowlisted action is definitively rejected, regardless of LLM confidence.

---

## 2. Test Scenarios Specification

### Scenario TS-001: Stopped / Crashed Container Recovery
- **Status**: CONFIRMED
- **Test Objective**: Verify that when a monitored container exits or crashes unexpectedly, AUTOHEAL detects the stopped state, diagnoses the exit, plans a restart, validates the action, executes `restart_container`, and verifies container restoration.
- **Test Setup**: Run a mock container `autoheal-test-web` labeled `autoheal.monitored=true`. Trigger exit via `docker stop autoheal-test-web` or sending `SIGTERM`.
- **Expected Monitoring Evidence**: Container status changes from `running` to `exited`, non-zero exit code or sudden termination detected in monitor poll cycle.
- **Expected Diagnosis**: Root cause identified as unexpected process termination; high confidence (`> 0.85`); severity classified as `Medium` or `High`.
- **Expected Plan**: Action proposed: `restart_container` on target `autoheal-test-web`.
- **Expected Validator Decision**: **APPROVED** (action is in allowlist, container is in monitored scope, risk is acceptable).
- **Expected Execution Result**: Container restart API invoked (or simulated in dry-run); exit code returns 0.
- **Expected Verification Result**: **SUCCESS** (container status returns to `running` with passing health checks).

---

### Scenario TS-002: Unhealthy Container Detection and Recovery
- **Status**: CONFIRMED
- **Test Objective**: Verify detection and remediation when a container's internal health check begins failing while the container process remains running.
- **Test Setup**: Run a test web server with a Dockerfile `HEALTHCHECK` targeting `/health`. Trigger health failure by invoking a mock endpoint `POST /fault/fail-health`.
- **Expected Monitoring Evidence**: Container health status transitions from `healthy` to `unhealthy`; error logs show health check failure HTTP 500.
- **Expected Diagnosis**: Application health check failure / internal HTTP error; confidence `> 0.80`; severity classified as `Medium`.
- **Expected Plan**: Action proposed: `restart_container` or `reload_service`.
- **Expected Validator Decision**: **APPROVED**.
- **Expected Execution Result**: Container restarted within configured timeout.
- **Expected Verification Result**: **SUCCESS** (subsequent HTTP GET `/health` returns 200 OK within stabilization window).

---

### Scenario TS-003: High CPU Resource Exhaustion
- **Status**: CONFIRMED
- **Test Objective**: Verify detection of abnormal CPU saturation sustained beyond the configured threshold and safe resolution.
- **Test Setup**: Run a compute-intensive worker container. Trigger infinite loop or intensive calculation via `POST /fault/cpu-spike`.
- **Expected Monitoring Evidence**: CPU utilization exceeds 85% for 3 consecutive poll cycles (15s); evidence captures PID and thread CPU spikes.
- **Expected Diagnosis**: CPU saturation caused by runaway compute loop; severity `High`.
- **Expected Plan**: Action proposed: `restart_container` (or restart with temporary CPU throttling if supported).
- **Expected Validator Decision**: **APPROVED**.
- **Expected Execution Result**: Action executed; container process recycled.
- **Expected Verification Result**: **SUCCESS** (CPU usage drops below 20% in subsequent verification cycles).

---

### Scenario TS-004: High Memory Leak / OOM Pressure
- **Status**: CONFIRMED
- **Test Objective**: Detect rapid memory consumption approaching container memory limit and take preventative remediation before kernel Out-Of-Memory (OOM) kill.
- **Test Setup**: Monitored container with 256MB limit. Trigger memory allocation via `POST /fault/memory-leak`.
- **Expected Monitoring Evidence**: Memory consumption exceeds 90% of limit (230MB+) and slope indicates exhaustion.
- **Expected Diagnosis**: Uncontrolled memory growth / memory leak; severity `High`.
- **Expected Plan**: Action proposed: `restart_container`.
- **Expected Validator Decision**: **APPROVED**.
- **Expected Execution Result**: Container restarted cleanly before OOM killer terminates it abruptly.
- **Expected Verification Result**: **SUCCESS** (memory usage returns to baseline < 50MB).

---

### Scenario TS-005: Application Runtime Errors in Logs
- **Status**: CONFIRMED
- **Test Objective**: Verify that continuous application runtime stack traces or fatal database connection errors in stdout/stderr trigger diagnosis.
- **Test Setup**: Web service outputs repeated Python tracebacks or database connection refused logs via `POST /fault/db-disconnect`.
- **Expected Monitoring Evidence**: Log scraper detects error pattern (regex matches `Traceback`, `ConnectionRefusedError`, `FATAL`).
- **Expected Diagnosis**: Service dependency disconnect; root cause identified as downstream connectivity failure.
- **Expected Plan**: Action proposed: `check_dependency` or `restart_container`.
- **Expected Validator Decision**: **APPROVED**.
- **Expected Execution Result**: Permitted action executed.
- **Expected Verification Result**: Log tail shows error rate returns to 0.

---

### Scenario TS-006: Abnormal Log Spikes
- **Status**: CONFIRMED
- **Test Objective**: Detect sudden burst of error log volume indicating application distress.
- **Test Setup**: Inject 500 error logs per second into container stderr.
- **Expected Monitoring Evidence**: Log volume per second exceeds baseline anomaly threshold.
- **Expected Diagnosis**: Sudden log burst indicating panic state or misconfiguration.
- **Expected Plan**: Action proposed: `restart_container`.
- **Expected Validator Decision**: **APPROVED**.
- **Expected Execution Result**: Executed within limits.
- **Expected Verification Result**: Log volume normalized.

---

### Scenario TS-007: Traffic Flooding from a Single Source (DoS Simulation)
- **Status**: PENDING DESIGN
- **Test Objective**: Detect rapid incoming traffic surge from a single IP address causing service degradation.
- **Test Setup**: **PENDING** (Design test harness using curl loop or Python script targeting test container).
- **Expected Monitoring Evidence**: **PENDING** (Evaluated in Phase 4: request rate per IP > 100 req/s or network RX spikes).
- **Expected Diagnosis**: Single-source traffic flood / denial of service attempt; severity `High`.
- **Expected Plan**: Action proposed: `block_source_ip` or `rate_limit_traffic` (must be in allowlist).
- **Expected Validator Decision**: **PENDING** (Requires allowlist expansion and firewall/proxy parameter check).
- **Expected Execution Result**: Mock rate-limit applied.
- **Expected Verification Result**: **PENDING**.

---

### Scenario TS-008: Brute-Force Login Attempts
- **Status**: PENDING DESIGN
- **Test Objective**: Detect repeated authentication failures from an external client attempting password guessing.
- **Test Setup**: **PENDING** (Script sending 50 failed authentication POST requests within 10 seconds).
- **Expected Monitoring Evidence**: **PENDING** (Log analysis matches 50 `401 Unauthorized` responses from same remote IP).
- **Expected Diagnosis**: Automated brute-force credential stuffing attack; severity `High`.
- **Expected Plan**: Action proposed: `block_ip` or `notify_administrator`.
- **Expected Validator Decision**: **PENDING** (Validate IP parameter formatting and subnet mask safety).
- **Expected Execution Result**: Permitted defensive action executed.
- **Expected Verification Result**: **PENDING**.

---

### Scenario TS-009: Invalid Remediation Plan (Schema Rejection)
- **Status**: CONFIRMED
- **Test Objective**: Verify that when the Planner outputs a syntactically invalid or schema-violating plan, the Validator immediately rejects it without throwing uncaught exceptions.
- **Test Setup**: Mock Planner output with missing fields, invalid JSON, or incorrect data types.
- **Expected Monitoring Evidence**: N/A (Internal pipeline state test).
- **Expected Diagnosis**: Valid diagnosis object.
- **Expected Plan**: Corrupted / malformed plan payload.
- **Expected Validator Decision**: **REJECTED** (Schema validation failure: `ValidationError`).
- **Expected Execution Result**: **NO EXECUTION**; pipeline routes to re-plan or admin alert.
- **Expected Verification Result**: State marked as `PLAN_REJECTED`.

---

### Scenario TS-010: Unsafe Action Rejection (Allowlist Violation)
- **Status**: CONFIRMED
- **Test Objective**: Verify that if the Planner suggests an unapproved or destructive action (e.g., `docker rm -f`, `format_disk`, `prune_system`), the deterministic Validator strictly rejects it.
- **Test Setup**: Inject an unallowlisted action into the plan: `{"action": "remove_container", "target": "all"}`.
- **Expected Monitoring Evidence**: N/A.
- **Expected Diagnosis**: Valid diagnosis.
- **Expected Plan**: Malicious or hallucinated unapproved action.
- **Expected Validator Decision**: **REJECTED** with error: `Action 'remove_container' is not in the permitted allowlist`.
- **Expected Execution Result**: **NO EXECUTION**; zero Docker mutating API calls.
- **Expected Verification Result**: Audit log records unauthorized action attempt; LLM cannot override rejection.

---

### Scenario TS-011: Critical Severity Incident Requiring Human Approval
- **Status**: CONFIRMED
- **Test Objective**: Verify that an incident classified as `Critical` pauses the LangGraph workflow and waits for manual administrator approval in the Streamlit dashboard.
- **Test Setup**: Inject a simulated critical failure (e.g. multi-service cascading outage or high-risk configuration rollback).
- **Expected Monitoring Evidence**: Critical failure telemetry.
- **Expected Diagnosis**: Critical cascading outage; severity classified as `Critical`.
- **Expected Plan**: High-impact recovery action (e.g., `recreate_container_stack`).
- **Expected Validator Decision**: **REQUIRES_APPROVAL** (Validator flags high risk; blocks autonomous execution).
- **Expected Execution Result**:
  - *If Admin Approves*: Action proceeds to Recovery Executor.
  - *If Admin Rejects*: Action aborted; state recorded as `ADMIN_REJECTED`.
- **Expected Verification Result**: Workflow faithfully reflects administrator decision.

---

### Scenario TS-012: Failed Recovery Execution Handling
- **Status**: CONFIRMED
- **Test Objective**: Verify graceful error handling when Docker Engine encounters a system error while executing an approved recovery command (e.g., port conflict or daemon timeout).
- **Test Setup**: Mock container configured to fail on restart due to deliberate port binding collision.
- **Expected Monitoring Evidence**: Container stopped.
- **Expected Diagnosis**: Service crash.
- **Expected Plan**: `restart_container`.
- **Expected Validator Decision**: **APPROVED**.
- **Expected Execution Result**: Docker API throws `APIError` (port already allocated); Executor catches error, logs failure, and avoids crash.
- **Expected Verification Result**: **EXECUTION_FAILED**; incident escalated to administrator with exact error traceback.

---

### Scenario TS-013: Failed Recovery Verification (Post-Recovery Health Check Failure)
- **Status**: CONFIRMED
- **Test Objective**: Verify behavior when an action executes successfully, but the container fails to recover to a healthy state within the stabilization window.
- **Test Setup**: Container has a broken configuration file that causes immediate crashing on startup (crash loop).
- **Expected Monitoring Evidence**: Container exited.
- **Expected Diagnosis**: Crash loop.
- **Expected Plan**: `restart_container`.
- **Expected Validator Decision**: **APPROVED**.
- **Expected Execution Result**: Docker `restart` returns success.
- **Expected Verification Result**: **VERIFICATION_FAILED** (container exited again within 5 seconds of restart); recovery marked unsuccessful; rollback or escalation triggered.

---

### Scenario TS-014: End-to-End Successful Recovery Lifecycle
- **Status**: CONFIRMED
- **Test Objective**: Validate the entire end-to-end autonomous healing loop from incident injection to verified recovery, database persistence, and dashboard update.
- **Test Setup**: Testbed container running with simulated transient memory leak.
- **Expected Monitoring Evidence**: Metric poll captures threshold breach; incident created.
- **Expected Diagnosis**: Gemini Diagnoser correctly pinpoints transient memory exhaustion.
- **Expected Plan**: Gemini Planner selects `restart_container`.
- **Expected Validator Decision**: Validator validates schema, allowlist, and target. Approves action.
- **Expected Execution Result**: Executor cleanly restarts container.
- **Expected Verification Result**: Verifier checks container status: `running`, health: `healthy`. Incident marked `RESOLVED` in SQLite; Streamlit dashboard displays updated timeline.

---

## 3. Testing Matrix Summary

| Test ID | Scenario | Severity | Autonomous Allowed? | Status |
|---|---|---|---|---|
| TS-001 | Stopped Container | Medium | Yes | CONFIRMED |
| TS-002 | Unhealthy Container | Medium | Yes | CONFIRMED |
| TS-003 | High CPU Saturation | High | Yes (Stricter validation) | CONFIRMED |
| TS-004 | High Memory Pressure | High | Yes (Stricter validation) | CONFIRMED |
| TS-005 | Application Log Errors | Medium | Yes | CONFIRMED |
| TS-006 | Abnormal Log Spikes | Medium | Yes | CONFIRMED |
| TS-007 | Traffic Flooding | High | Depends on policy | PENDING DESIGN |
| TS-008 | Brute-force Login Attempts | High | Depends on policy | PENDING DESIGN |
| TS-009 | Invalid Remediation Plan | N/A | No (Rejection) | CONFIRMED |
| TS-010 | Unsafe Action (Allowlist) | Critical | No (Rejection) | CONFIRMED |
| TS-011 | Critical Human Approval | Critical | No (Requires Admin) | CONFIRMED |
| TS-012 | Failed Docker Execution | High | N/A (Execution Error) | CONFIRMED |
| TS-013 | Failed Post-Recovery Verification | High | N/A (Escalation) | CONFIRMED |
| TS-014 | End-to-End Full Lifecycle | Medium | Yes | CONFIRMED |
