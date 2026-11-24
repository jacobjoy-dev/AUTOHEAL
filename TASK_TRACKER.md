# AUTOHEAL Master Task Tracker

This document is the master project execution checklist for **AUTOHEAL**. It breaks down the entire project lifecycle across 15 phases into granular, actionable tasks designed for beginner execution.

---

## Progress Overview

| Phase | Description | Total Tasks | Completed | In Progress | Pending | Status |
|---|---|---|---|---|---|---|
| **Phase 0** | Project Planning and Documentation | 10 | 10 | 0 | 0 | DONE |
| **Phase 1** | Development Environment Setup | 11 | 11 | 0 | 0 | DONE |
| **Phase 2** | Docker Fundamentals and Integration | 9 | 0 | 0 | 9 | IN PROGRESS |
| **Phase 3** | Monitoring System | 9 | 0 | 0 | 9 | TODO |
| **Phase 4** | Incident Detection Engine | 10 | 0 | 0 | 10 | TODO |
| **Phase 5** | Gemini Integration and Diagnoser | 10 | 0 | 0 | 10 | TODO |
| **Phase 6** | Remediation Planner Agent | 8 | 0 | 0 | 8 | TODO |
| **Phase 7** | Deterministic Safety Validator | 9 | 0 | 0 | 9 | TODO |
| **Phase 8** | LangGraph Workflow Orchestration | 9 | 0 | 0 | 9 | TODO |
| **Phase 9** | Controlled Recovery Executor | 9 | 0 | 0 | 9 | TODO |
| **Phase 10** | Recovery Verification | 7 | 0 | 0 | 7 | TODO |
| **Phase 11** | SQLite Database Persistence Layer | 8 | 0 | 0 | 8 | TODO |
| **Phase 12** | Streamlit Dashboard & Visualizations | 9 | 0 | 0 | 9 | TODO |
| **Phase 13** | Integration and End-to-End Testing | 14 | 0 | 0 | 14 | TODO |
| **Phase 14** | Evaluation, Benchmarking & Documentation | 9 | 0 | 0 | 9 | TODO |
| **TOTAL** | **All 15 Phases** | **141** | **21** | **0** | **120** | **14.9% Done** |

---

## Phase 0 – Project Planning and Documentation

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **SETUP-001** | Phase 0 | Initialize Project Repository & Git Rules | Create `.gitignore` to protect `.env`, secrets, `.venv`, and SQLite databases. | None | Critical | 30 mins | `.gitignore` exists and excludes secrets, DBs, and virtual environments. | DONE | Created & verified |
| **SETUP-002** | Phase 0 | Create Environment Template (`.env.example`) | Create `.env.example` with placeholders for `GEMINI_API_KEY`, `DRY_RUN=true`, and limits. | None | Critical | 20 mins | `.env.example` contains placeholders with no real credentials. | DONE | Created & verified |
| **SETUP-003** | Phase 0 | Create Project Structure Specification | Document directory tree, responsibilities, and structural invariants in `docs/PROJECT_STRUCTURE.md`. | SETUP-001 | High | 45 mins | `docs/PROJECT_STRUCTURE.md` exists and defines all module roles. | DONE | Created & verified |
| **SETUP-004** | Phase 0 | Create Architectural Decision Records (ADRs) | Document confirmed technologies, rejected alternatives, and pending items in `docs/DECISIONS.md`. | None | High | 45 mins | `docs/DECISIONS.md` records all ADRs with rationales. | DONE | Created & verified |
| **SETUP-005** | Phase 0 | Create Environment & Dependency Specification | Document verified installed runtimes and unverified target packages in `docs/ENVIRONMENT.md`. | None | High | 45 mins | Verified versions (Python 3.14.2, Docker 29.9.2, etc.) recorded accurately. | DONE | Created & verified |
| **SETUP-006** | Phase 0 | Create Comprehensive Testing Plan | Detail 14 test scenarios with objectives, evidence, and expected outcomes in `docs/TESTING_PLAN.md`. | None | High | 1 hour | All 14 scenarios documented with clear pass/fail criteria. | DONE | Created & verified |
| **SETUP-007** | Phase 0 | Create Progress Tracking Log | Initialize `progress.txt` reflecting Phase 0 status and zero completed application code. | None | Medium | 15 mins | `progress.txt` formatted per specification with accurate status. | DONE | Created & verified |
| **SETUP-008** | Phase 0 | Create Definitive Project Context | Document full system scope, architecture, agent responsibilities, and metrics in `PROJECT_CONTEXT.md`. | SETUP-003, SETUP-004 | Critical | 1 hour | `PROJECT_CONTEXT.md` aligns with all architectural decisions. | DONE | Created & verified |
| **SETUP-009** | Phase 0 | Create Public Project README | Draft comprehensive GitHub `README.md` with overview, architecture diagrams, and roadmap. | SETUP-008 | High | 45 mins | `README.md` complete, professional, and marks setup as pending. | DONE | Created & verified |
| **SETUP-010** | Phase 0 | Create Project Directory Skeleton | Create clean directory structure (`src/`, `tests/`, `test_environment/`, `docs/`) with placeholder files. | SETUP-003 | High | 30 mins | Directory tree matches `docs/PROJECT_STRUCTURE.md`. | DONE | Skeleton established |

---

## Phase 1 – Development Environment Setup

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **ENV-001** | Phase 1 | Verify Host Core Runtimes | Confirm Python 3.14.2, Docker Engine 29.9.2, Docker Compose v5.6.1, and SQLite 3.50.4 via PowerShell. | Phase 0 | Critical | 20 mins | Terminal outputs match expected verified versions. | DONE | Python 3.14.2, Docker 29.9.2, Compose v5.6.1, SQLite 3.50.4 verified via PowerShell |
| **ENV-002** | Phase 1 | Create Python Virtual Environment | Run `python -m venv .venv` in repository root to establish isolated environment. | ENV-001 | Critical | 15 mins | `.venv` directory created in `d:\AUTOHEAL\.venv`. | DONE | .venv created with Python 3.14.2 at D:\AUTOHEAL\.venv |
| **ENV-003** | Phase 1 | Activate Virtual Environment & Upgrade Pip | Activate `.venv\Scripts\Activate.ps1` and run `python -m pip install --upgrade pip`. | ENV-002 | High | 15 mins | `sys.executable` points to `.venv` and pip is up-to-date. | DONE | pip 25.3 verified up-to-date in .venv |
| **ENV-004** | Phase 1 | Install and Verify Utility Packages | Install `python-dotenv` and `pydantic`. Verify imports and versions in Python 3.14.2. | ENV-003 | High | 30 mins | `python -c "import dotenv, pydantic; print('OK')"` exits code 0. | DONE | Installed & verified: python-dotenv 1.2.4, pydantic 2.14.4 (pydantic-core 2.50.5) |
| **ENV-005** | Phase 1 | Install and Verify Docker SDK for Python | Install `docker==7.2.0`. Verify import and test connection ping to Docker daemon. | ENV-004 | Critical | 30 mins | `client.ping()` returns `True` via Python in `.venv`. | DONE | docker 7.2.0 and pywin32-312 installed & verified in .venv |
| **ENV-006** | Phase 1 | Install and Verify Google GenAI SDK | Install `google-genai==3.6.1`. Verify import without compilation errors. | ENV-004 | Critical | 30 mins | `python -c "import google.genai; print('OK')"` exits code 0. | DONE | google-genai 3.6.1 installed & client verified in .venv |
| **ENV-007** | Phase 1 | Install and Verify LangGraph | Install `langgraph==1.2.24`. Verify import and basic graph instantiation. | ENV-004 | Critical | 45 mins | `from langgraph.graph import StateGraph` succeeds without error. | DONE | langgraph 1.2.24 installed & StateGraph verified in .venv |
| **ENV-008** | Phase 1 | Install and Verify Streamlit & Plotly | Install `streamlit==1.67.0` and `plotly==6.9.0`. Verify import and CLI execution. | ENV-004 | High | 45 mins | `streamlit --version` prints 1.67.0 and Plotly imports cleanly. | DONE | streamlit 1.67.0 & plotly 7.1.0 installed & verified in .venv |
| **ENV-009** | Phase 1 | Install and Verify Pytest | Install `pytest>=8.3.0`. Verify test runner runs against an empty test directory. | ENV-004 | Medium | 15 mins | `pytest` runs and outputs 0 errors / clean exit. | DONE | pytest 9.2.0 installed & verified in .venv |
| **ENV-010** | Phase 1 | Instantiate Local Configuration (`.env`) | Copy `.env.example` to `.env` and set local `DRY_RUN=true`. | ENV-004 | High | 15 mins | `.env` exists locally and is not tracked by Git (`git status`). | DONE | .env instantiated & verified with python-dotenv |
| **ENV-011** | Phase 1 | Update Environment Status Documentation | Record actual installed package versions and verified dates in `docs/ENVIRONMENT.md`. | ENV-005 to ENV-009 | Medium | 20 mins | Table in `docs/ENVIRONMENT.md` populated with verified versions. | DONE | docs/ENVIRONMENT.md updated & requirements.txt generated |

---

## Phase 2 – Docker Fundamentals and Integration

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **DOCKER-001** | Phase 2 | Study Docker Named Pipe & WSL2 Access | Document how Docker Desktop exposes daemon on Windows (`npipe:////./pipe/docker_engine`). | Phase 1 | Medium | 30 mins | Connection parameters documented in `src/config/settings.py`. | TODO | Critical on Windows |
| **DOCKER-002** | Phase 2 | Implement Docker Client Connection Factory | Create `src/utils/docker_client.py` with robust connection handling and error messages. | DOCKER-001 | High | 45 mins | `get_docker_client()` returns active client or raises clear exception. | TODO | Reusable client singleton |
| **DOCKER-003** | Phase 2 | Launch Standalone Test Container | Run lightweight alpine test container using Docker CLI (`autoheal-sandbox`). | DOCKER-002 | Low | 15 mins | Container is running and visible via `docker ps`. | TODO | Sandbox target |
| **DOCKER-004** | Phase 2 | Implement Container Listing Utility | Write function to list all running containers and filter by label (`autoheal.monitored=true`). | DOCKER-002, DOCKER-003 | High | 45 mins | Function returns list of container objects matching label filter. | TODO | Scope limiter |
| **DOCKER-005** | Phase 2 | Implement Container State Inspector | Extract container state (`running`, `exited`, `paused`, `restarting`) and exit codes. | DOCKER-004 | High | 45 mins | Function returns structured container state dictionary. | TODO | Core telemetry |
| **DOCKER-006** | Phase 2 | Implement Container Health Inspector | Extract Docker internal healthcheck status (`healthy`, `unhealthy`, `starting`, `none`). | DOCKER-005 | High | 45 mins | Function accurately identifies health status from container inspect data. | TODO | Required for Scenario TS-002 |
| **DOCKER-007** | Phase 2 | Implement Container Log Extractor | Extract tail of container stdout/stderr logs with configurable line limit (default: 50). | DOCKER-005 | High | 45 mins | Function returns decoded string tail of container logs safely. | TODO | Required for Diagnoser |
| **DOCKER-008** | Phase 2 | Implement Resource Stats Collector | Read raw Docker stats stream (1 snapshot) and parse CPU and memory usage bytes. | DOCKER-005 | High | 1 hour | Function returns raw CPU and memory metric values. | TODO | Foundation for Monitor |
| **DOCKER-009** | Phase 2 | Write Unit Tests for Docker Integration | Create unit tests with mocks in `tests/unit/test_docker_client.py`. | DOCKER-002 to DOCKER-008 | High | 1 hour | `pytest tests/unit/test_docker_client.py` passes 100%. | TODO | Automated test harness |

---

## Phase 3 – Monitoring System

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **MON-001** | Phase 3 | Define Telemetry Pydantic Models | Create `src/models/telemetry.py` defining `ContainerSnapshot`, `ResourceMetrics`, and `ContainerHealth`. | Phase 2 | High | 45 mins | Pydantic models validate sample telemetry dictionaries cleanly. | TODO | Strongly-typed telemetry |
| **MON-002** | Phase 3 | Implement CPU Percentage Calculator | Implement standard Docker CPU percentage formula normalizing system and container delta by core count. | MON-001 | Critical | 45 mins | CPU% accurately reflects usage between 0.0% and 100.0% * num_cores. | TODO | Formula precision check |
| **MON-003** | Phase 3 | Implement Memory Utilization Calculator | Calculate memory usage in MB, memory limit in MB, and percentage utilized. | MON-001 | High | 30 mins | Memory percentage accurately calculated from container stats. | TODO | Detects memory leaks |
| **MON-004** | Phase 3 | Implement Container Polling Engine | Create `src/monitor/poller.py` that iterates over monitored containers and gathers snapshots. | MON-002, MON-003 | Critical | 1 hour | Poller returns list of `ContainerSnapshot` objects in single pass. | TODO | Deterministic execution |
| **MON-005** | Phase 3 | Implement Monitored Container Registry | Implement label and name-based filtering to ensure only designated containers are monitored. | MON-004 | High | 30 mins | Host and unrelated Docker containers are completely ignored by poller. | TODO | Boundary enforcement |
| **MON-006** | Phase 3 | Implement Periodic Polling Loop | Implement non-blocking or configurable interval polling loop (e.g. 5-second sleep). | MON-005 | Medium | 45 mins | Poller runs continuously, emitting telemetry events per cycle. | TODO | Configurable polling rate |
| **MON-007** | Phase 3 | Implement Graceful Error Handling | Ensure poller handles disconnected containers or Docker daemon restarts without crashing. | MON-006 | High | 45 mins | Poller catches `DockerException` and logs warning instead of terminating. | TODO | Resilience check |
| **MON-008** | Phase 3 | Implement Telemetry Logger / Broadcaster | Output structured telemetry to logging framework and prepare for DB ingestion. | MON-007 | Medium | 30 mins | Telemetry logged in JSON or structured log format. | TODO | Observability |
| **MON-009** | Phase 3 | Write Unit Tests for Monitoring System | Write comprehensive unit tests in `tests/unit/test_monitor.py` using mock telemetry. | MON-001 to MON-008 | High | 1 hour | `pytest tests/unit/test_monitor.py` passes 100%. | TODO | Test coverage |

---

## Phase 4 – Incident Detection Engine

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **DET-001** | Phase 4 | Define Incident & Evidence Models | Create `src/models/incident.py` defining `Incident`, `IncidentEvidence`, and `SeverityLevel` enums. | Phase 3 | Critical | 45 mins | Pydantic schema validates incidents with severity `Low`, `Medium`, `High`, `Critical`. | TODO | Contract for Diagnoser |
| **DET-002** | Phase 4 | Implement Stopped Container Detector | Create rule detecting when a monitored container exits with status `exited` or non-zero code. | DET-001 | Critical | 45 mins | Rule flags stopped container and produces `Incident` with exit code evidence. | TODO | Scenario TS-001 |
| **DET-003** | Phase 4 | Implement Unhealthy Container Detector | Create rule detecting when container health transitions to `unhealthy`. | DET-001 | High | 45 mins | Rule flags unhealthy container and packages health log evidence. | TODO | Scenario TS-002 |
| **DET-004** | Phase 4 | Implement Sustained High CPU Detector | Create rule requiring CPU > 85% across 3 consecutive cycles (sliding window) to trigger incident. | DET-001 | High | 1 hour | Single transient spike ignored; 3 sustained spikes trigger `High` severity incident. | TODO | Scenario TS-003 |
| **DET-005** | Phase 4 | Implement High Memory Detector | Create rule triggering incident when memory usage exceeds 90% of container limit. | DET-001 | High | 45 mins | Memory threshold breach triggers `High` severity incident before OOM kill. | TODO | Scenario TS-004 |
| **DET-006** | Phase 4 | Design Traffic Flooding Detector | Design sliding window request rate or network RX spike evaluator for DoS detection. | DET-001 | Medium | 1 hour | Rule identifies sudden traffic anomaly from single source IP. | TODO | Scenario TS-007 (Pending) |
| **DET-007** | Phase 4 | Design Brute-Force Login Detector | Design log analyzer detecting consecutive `401 Unauthorized` responses in sliding window. | DET-001 | Medium | 1 hour | Rule identifies repeated failed authentication attempts in logs. | TODO | Scenario TS-008 (Pending) |
| **DET-008** | Phase 4 | Implement Master Detection Evaluator | Combine all rules in `src/detection/detector.py` to evaluate container snapshots each cycle. | DET-002 to DET-007 | Critical | 1 hour | Evaluator takes snapshot list and returns list of newly detected incidents. | TODO | Aggregation engine |
| **DET-009** | Phase 4 | Implement Incident De-duplication | Prevent firing duplicate incidents for the same container failure during active remediation. | DET-008 | High | 45 mins | Active incidents are deduplicated until resolved or cooldown expires. | TODO | Prevents alert storm |
| **DET-010** | Phase 4 | Write Unit Tests for Detection Engine | Create test cases in `tests/unit/test_detection.py` simulating all failure states. | DET-001 to DET-009 | High | 1 hour | `pytest tests/unit/test_detection.py` passes 100%. | TODO | Test coverage |

---

## Phase 5 – Gemini Integration and Diagnoser Agent

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **DIAG-001** | Phase 5 | Set Up Gemini Client Factory | Create `src/utils/gemini_client.py` initializing `google-genai` with API key validation. | Phase 4 | Critical | 45 mins | Client initializes cleanly; raises descriptive error if `GEMINI_API_KEY` is missing. | TODO | Safe client init |
| **DIAG-002** | Phase 5 | Implement LLM Daily Quota Guard | Implement in-memory counter enforcing `MAX_LLM_CALLS_PER_DAY` to protect developer budget. | DIAG-001 | High | 30 mins | Client halts requests and raises `QuotaExceededException` when limit reached. | TODO | Safety guard |
| **DIAG-003** | Phase 5 | Define Diagnosis Pydantic Schema | Create `src/models/diagnosis.py` defining `DiagnosisResult` (root cause, confidence, evidence). | DIAG-001 | Critical | 45 mins | Schema enforces typed fields: root cause string, confidence float (0.0 to 1.0). | TODO | Structured output |
| **DIAG-004** | Phase 5 | Design Diagnoser System Prompt | Create prompt template in `src/agents/diagnoser/prompts.py` constraining LLM to factual log analysis. | DIAG-003 | High | 1 hour | Prompt instructs LLM to cite specific log lines and evaluate uncertainty. | TODO | Prompt engineering |
| **DIAG-005** | Phase 5 | Implement Structured Output Generation | Call Gemini with structured output config (`response_schema=DiagnosisResult`). | DIAG-003, DIAG-004 | Critical | 1 hour | Gemini returns validated JSON directly parsed into `DiagnosisResult`. | TODO | Strict JSON mode |
| **DIAG-006** | Phase 5 | Implement Diagnoser Agent Class | Create `src/agents/diagnoser/diagnoser.py` integrating prompt, Gemini call, and parsing. | DIAG-005 | Critical | 1 hour | `diagnose(incident)` takes `Incident` and returns `DiagnosisResult`. | TODO | Core agent logic |
| **DIAG-007** | Phase 5 | Implement API Timeout & Retry Logic | Wrap Gemini call with 30s timeout and bounded exponential backoff (max 2 retries). | DIAG-006 | High | 45 mins | Transient API network drops are retried; timeouts exit gracefully. | TODO | Network resilience |
| **DIAG-008** | Phase 5 | Implement Heuristic Fallback Diagnoser | Create fallback rule-based diagnosis when Gemini is unreachable or quota exhausted. | DIAG-007 | Medium | 45 mins | System falls back to deterministic diagnosis based on exit codes if LLM fails. | TODO | Graceful degradation |
| **DIAG-009** | Phase 5 | Secure Credentials in Logs | Audit logging in Diagnoser to guarantee `GEMINI_API_KEY` never appears in logs. | DIAG-006 | Critical | 20 mins | Zero occurrences of secret keys in log files or console output. | TODO | Security audit |
| **DIAG-010** | Phase 5 | Write Unit Tests for Diagnoser | Write unit tests in `tests/unit/test_diagnoser.py` with mocked Gemini responses. | DIAG-001 to DIAG-009 | High | 1 hour | `pytest tests/unit/test_diagnoser.py` passes 100% without consuming live API tokens. | TODO | Mock-based testing |

---

## Phase 6 – Remediation Planner Agent

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **PLAN-001** | Phase 6 | Define Permitted Actions Enum | Create `src/models/actions.py` defining allowed actions (`restart_container`, `start_container`, etc.). | Phase 5 | Critical | 30 mins | Enum defines all approved remediation verbs and forbidden action comments. | TODO | Foundation for allowlist |
| **PLAN-002** | Phase 6 | Define Remediation Plan Schema | Create `src/models/plan.py` defining `RemediationPlan`, `PlanStep`, target ID, and parameters. | PLAN-001 | Critical | 45 mins | Schema strictly validates structured plan with ordered steps. | TODO | Structured plan contract |
| **PLAN-003** | Phase 6 | Design Planning Prompt Template | Create `src/agents/planner/prompts.py` instructing Gemini to choose ONLY from allowed actions. | PLAN-002 | High | 1 hour | Prompt explicitly lists permitted actions and warns against arbitrary shell commands. | TODO | Constrained planning |
| **PLAN-004** | Phase 6 | Implement Planner Agent Class | Create `src/agents/planner/planner.py` integrating diagnosis input, prompt, and Gemini call. | PLAN-003 | Critical | 1 hour | `plan_recovery(incident, diagnosis)` returns a typed `RemediationPlan`. | TODO | Core agent logic |
| **PLAN-005** | Phase 6 | Enforce Structured Output for Plan | Configure Gemini structured schema to emit JSON matching `RemediationPlan` model. | PLAN-004 | High | 45 mins | Raw LLM output parses directly into `RemediationPlan` without string regex slicing. | TODO | Structured generation |
| **PLAN-006** | Phase 6 | Implement Heuristic Fallback Planner | Provide deterministic fallback plan (e.g. standard restart) if LLM call fails. | PLAN-004 | Medium | 45 mins | Basic failure scenarios have deterministic default recovery plans. | TODO | Fallback safety net |
| **PLAN-007** | Phase 6 | Add Plan Reason & Rollback Strategy | Ensure each plan step contains an explanatory `rationale` and tentative rollback hint. | PLAN-004 | Medium | 30 mins | Plan steps include human-readable justification for administrator review. | TODO | Auditability |
| **PLAN-008** | Phase 6 | Write Unit Tests for Planner Agent | Test plan generation with mocked responses in `tests/unit/test_planner.py`. | PLAN-001 to PLAN-007 | High | 1 hour | `pytest tests/unit/test_planner.py` passes 100%. | TODO | Test coverage |

---

## Phase 7 – Deterministic Safety Validator

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **VAL-001** | Phase 7 | Define Validation Result Schema | Create `src/models/validation.py` defining `ValidationResult` (is_valid, decision, rejection_reason). | Phase 6 | Critical | 30 mins | Schema supports decisions: `APPROVED`, `REJECTED`, `REQUIRES_APPROVAL`. | TODO | Safety verdict schema |
| **VAL-002** | Phase 7 | Implement Action Allowlist Checker | Implement check verifying that every action in the plan is present in the permitted enum. | VAL-001 | Critical | 45 mins | Any action outside the allowlist (e.g. `rm`, `exec`) is unconditionally rejected. | TODO | Scenario TS-010 |
| **VAL-003** | Phase 7 | Implement Target Scope Checker | Verify that target container exists and carries label `autoheal.monitored=true`. | VAL-001 | Critical | 45 mins | Plans attempting to mutate unmonitored or system containers are rejected. | TODO | Blast radius control |
| **VAL-004** | Phase 7 | Implement Parameter Sanitizer | Check that action parameters do not contain shell injection characters (`;`, `&`, `|`, `` ` ``). | VAL-001 | Critical | 30 mins | Any dangerous parameter string triggers immediate validation rejection. | TODO | Anti-injection guard |
| **VAL-005** | Phase 7 | Implement Precondition Verifier | Verify target container is in valid state for proposed action (e.g., cannot start running container). | VAL-001 | High | 45 mins | Contradictory action proposals are rejected before execution. | TODO | Logic validation |
| **VAL-006** | Phase 7 | Implement Critical Severity Gate | If incident severity is `Critical`, set decision to `REQUIRES_APPROVAL` regardless of action safety. | VAL-001 | Critical | 30 mins | All Critical incidents halted for manual administrator approval. | TODO | Scenario TS-011 |
| **VAL-007** | Phase 7 | Enforce LLM Override Prevention | Architect Validator as pure deterministic Python with zero LLM override pathway. | VAL-002 to VAL-006 | Critical | 30 mins | Architecture guarantees no code path allows an LLM to bypass a rejection. | TODO | Core architectural rule |
| **VAL-008** | Phase 7 | Create Comprehensive Negative Test Suite | Write negative tests in `tests/unit/test_validator.py` injecting 10+ malicious/invalid plans. | VAL-001 to VAL-007 | Critical | 1 hour | 100% of invalid plans rejected; zero false approvals. | TODO | Scenario TS-009, TS-010 |
| **VAL-009** | Phase 7 | Write Positive Unit Tests for Validator | Verify valid plans for routine restarts pass validation cleanly. | VAL-001 to VAL-007 | High | 30 mins | Valid plans receive `is_valid=True` and `APPROVED`. | TODO | Positive path test |

---

## Phase 8 – LangGraph Workflow Orchestration

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **ORCH-001** | Phase 8 | Define LangGraph State Schema | Create `src/orchestration/state.py` defining `AutohealState` with all incident lifecycle fields. | Phase 7 | Critical | 45 mins | TypedDict captures incident, diagnosis, plan, validation, execution, and verification data. | TODO | Single state container |
| **ORCH-002** | Phase 8 | Implement Monitor and Detector Nodes | Wrap monitoring and incident detection as graph entry nodes in `src/orchestration/nodes.py`. | ORCH-001 | High | 45 mins | Node queries monitor; transitions to diagnosis if incident found, or ends cycle if clear. | TODO | Graph entrypoint |
| **ORCH-003** | Phase 8 | Implement Diagnoser and Planner Nodes | Wrap Diagnoser and Planner agents into graph nodes updating state. | ORCH-002 | High | 45 mins | Nodes execute agent reasoning and store diagnosis and plan in `AutohealState`. | TODO | AI reasoning nodes |
| **ORCH-004** | Phase 8 | Implement Validator Node | Wrap deterministic Validator into graph node evaluating proposed plan. | ORCH-003 | Critical | 30 mins | Node executes deterministic checks and sets validation verdict in state. | TODO | Deterministic safety node |
| **ORCH-005** | Phase 8 | Implement Conditional Routing Edges | Add conditional edges: if rejected -> escalate/re-plan; if requires approval -> pause. | ORCH-004 | Critical | 1 hour | Graph routes dynamically based on validation verdict and severity. | TODO | Branching logic |
| **ORCH-006** | Phase 8 | Implement Human-in-the-Loop Interruption | Configure LangGraph `interrupt_before` or state pause for administrator approval node. | ORCH-005 | High | 1 hour | Graph halts at approval node until explicit external input resume signal provided. | TODO | Human approval hook |
| **ORCH-007** | Phase 8 | Implement Bounded Retries & Cycle Guards | Limit re-planning or recovery loops to maximum 2 attempts to prevent infinite cycling. | ORCH-005 | High | 45 mins | Graph transitions to `FAILED` or `ESCALATED` after exceeding retry budget. | TODO | Infinite loop prevention |
| **ORCH-008** | Phase 8 | Compile and Visualize StateGraph | Compile workflow graph in `src/orchestration/graph.py` and output ASCII/Mermaid representation. | ORCH-001 to ORCH-007 | High | 45 mins | `graph.compile()` succeeds; visual representation accurately matches architecture. | TODO | Orchestrator ready |
| **ORCH-009** | Phase 8 | Write Integration Tests for Orchestration | Test full state graph transitions with mocked nodes in `tests/integration/test_orchestration.py`. | ORCH-008 | High | 1.5 hours | Integration test verifies state progression across all branches. | TODO | End-to-end state test |

---

## Phase 9 – Controlled Recovery Executor

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **EXEC-001** | Phase 9 | Define Execution Result Schema | Create `src/models/execution.py` defining `ExecutionResult` (success, duration, error, dry_run). | Phase 8 | Critical | 30 mins | Schema captures outcome of each recovery step. | TODO | Result contract |
| **EXEC-002** | Phase 9 | Implement Dry-Run Mode Safety Guard | Check `DRY_RUN` setting; if true, log simulation and return success without calling Docker. | EXEC-001 | Critical | 45 mins | Dry-run mode verified: zero mutating Docker calls executed when `DRY_RUN=true`. | TODO | Mandatory safety guard |
| **EXEC-003** | Phase 9 | Implement Container Restart Action | Implement `restart_container(container_id, timeout=30)` using Docker SDK with timeout. | EXEC-001 | High | 45 mins | Function restarts container cleanly and handles `docker.errors.APIError`. | TODO | Primary action |
| **EXEC-004** | Phase 9 | Implement Container Start Action | Implement `start_container(container_id)` for stopped/exited containers. | EXEC-001 | High | 30 mins | Function starts stopped container and returns execution metadata. | TODO | Scenario TS-001 action |
| **EXEC-005** | Phase 9 | Implement Container Stop Action | Implement `stop_container(container_id, timeout=10)` for runaway resource consumers. | EXEC-001 | High | 30 mins | Function safely terminates runaway container process. | TODO | Containment action |
| **EXEC-006** | Phase 9 | Implement Execution Timeout Wrapper | Wrap all Docker actions in strict timeout wrappers to prevent daemon hang scenarios. | EXEC-003 to EXEC-005 | High | 45 mins | Action times out and aborts if Docker daemon does not respond within limit. | TODO | Fault resilience |
| **EXEC-007** | Phase 9 | Implement Master Execution Controller | Create `src/executor/executor.py` coordinating execution of all steps in `RemediationPlan`. | EXEC-002 to EXEC-006 | Critical | 1 hour | Controller iterates steps, checks dry-run, invokes action, and records results. | TODO | Main executor entry |
| **EXEC-008** | Phase 9 | Implement Execution Audit Logger | Record all executed commands, parameters, timestamps, and caller identities to audit log. | EXEC-007 | High | 30 mins | Detailed audit entries written for every execution attempt. | TODO | Forensic trail |
| **EXEC-009** | Phase 9 | Write Unit Tests for Recovery Executor | Test dry-run and live execution modes with mock Docker API in `tests/unit/test_executor.py`. | EXEC-001 to EXEC-008 | High | 1 hour | `pytest tests/unit/test_executor.py` passes 100%. | TODO | Test coverage |

---

## Phase 10 – Recovery Verification

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **VER-001** | Phase 10 | Define Verification Result Schema | Create `src/models/verification.py` defining `VerificationResult` (`SUCCESS`, `PARTIAL`, `FAILED`). | Phase 9 | Critical | 30 mins | Schema captures verification status, healthcheck output, and verification duration. | TODO | Post-check contract |
| **VER-002** | Phase 10 | Implement Cooldown / Stabilization Delay | Implement configurable wait period (e.g. 5-10s) before checking container status. | VER-001 | High | 30 mins | Verifier waits for container startup before sampling health checks. | TODO | Avoid false negatives |
| **VER-003** | Phase 10 | Implement Container State Verification | Query Docker daemon to confirm container state is `running` and has not restarted/crashed. | VER-001, VER-002 | High | 45 mins | Verifier detects if container immediately crashed after restart. | TODO | Crash loop detection |
| **VER-004** | Phase 10 | Implement Application Healthcheck Verification | Query Docker internal healthcheck or HTTP health endpoint to verify application health. | VER-003 | High | 45 mins | Health status must equal `healthy` (or HTTP 200) to confirm recovery. | TODO | Application-layer check |
| **VER-005** | Phase 10 | Implement Post-Recovery Log Inspector | Inspect post-recovery log tail to verify absence of fatal error strings. | VER-004 | Medium | 45 mins | Immediate traceback or fatal error in new logs flags verification as `FAILED`. | TODO | Log confirmation |
| **VER-006** | Phase 10 | Implement Recovery Failure Escalation | If verification fails, flag incident as unrecovered and generate administrator alert. | VER-005 | High | 45 mins | Failed recovery escalates to administrator with full diagnosis history. | TODO | Scenario TS-013 |
| **VER-007** | Phase 10 | Write Unit Tests for Verifier | Write unit tests in `tests/unit/test_verifier.py` covering both success and fail scenarios. | VER-001 to VER-006 | High | 1 hour | `pytest tests/unit/test_verifier.py` passes 100%. | TODO | Test coverage |

---

## Phase 11 – SQLite Database Persistence Layer

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **DB-001** | Phase 11 | Design Relational Schema & DDL | Create `src/database/schema.sql` defining tables: `incidents`, `evidence`, `diagnoses`, `plans`, etc. | Phase 10 | Critical | 1 hour | DDL defines tables with primary keys, foreign keys, and indexes. | TODO | Database design |
| **DB-002** | Phase 11 | Implement Database Connection Manager | Create `src/database/db.py` managing SQLite connection lifecycle and thread safety. | DB-001 | Critical | 45 mins | Manager opens connection, handles transactions, and closes cleanly without file locks. | TODO | Safe SQLite wrapper |
| **DB-003** | Phase 11 | Implement Schema Migration / Init Script | Write auto-initialization function creating all tables if `autoheal.db` is empty. | DB-002 | High | 30 mins | Running init script generates valid database file with all tables. | TODO | One-click setup |
| **DB-004** | Phase 11 | Implement Incident Repository CRUD | Write functions to insert new incidents and update incident status (`DETECTED` -> `RESOLVED`). | DB-003 | High | 1 hour | Functions insert and update incident records with full type safety. | TODO | Incident persistence |
| **DB-005** | Phase 11 | Implement Diagnosis and Plan Persistence | Write functions to store Gemini diagnosis and validated recovery plans linked by incident ID. | DB-004 | High | 45 mins | Diagnoses and plans stored and retrievable via foreign key `incident_id`. | TODO | Audit record |
| **DB-006** | Phase 11 | Implement Execution & Verification Persistence | Write functions to record execution results, administrator decisions, and verification status. | DB-005 | High | 45 mins | Execution outcomes and administrator approvals faithfully recorded. | TODO | Complete lifecycle log |
| **DB-007** | Phase 11 | Implement Metric Aggregation Queries | Write SQL queries to calculate MTTD, MTTR, recovery success rate, and daily incident counts. | DB-006 | High | 1 hour | Queries return accurate aggregated metrics for dashboard display. | TODO | Analytics queries |
| **DB-008** | Phase 11 | Write Unit Tests for Database Operations | Write unit tests in `tests/unit/test_database.py` with in-memory SQLite database (`:memory:`). | DB-001 to DB-007 | High | 1 hour | `pytest tests/unit/test_database.py` passes 100%. | TODO | In-memory DB testing |

---

## Phase 12 – Streamlit Dashboard & Visualizations

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **DASH-001** | Phase 12 | Create Dashboard Base Layout | Create `src/dashboard/app.py` with multi-tab layout (Overview, Incidents, Approvals, Analytics). | Phase 11 | High | 45 mins | Streamlit app launches cleanly on `localhost:8501`. | TODO | UI scaffolding |
| **DASH-002** | Phase 12 | Implement Container Health Overview Cards | Display live cards showing status (`Running`, `Exited`, `Unhealthy`) of all monitored containers. | DASH-001 | High | 45 mins | Overview displays real-time container count and health status. | TODO | Live status view |
| **DASH-003** | Phase 12 | Implement Plotly CPU & Memory Telemetry Charts | Render interactive Plotly time-series charts showing container CPU% and RAM usage. | DASH-001 | High | 1 hour | Interactive line charts display resource trends over time. | TODO | Telemetry charts |
| **DASH-004** | Phase 12 | Implement Incident History Timeline Table | Build data table listing all past incidents with severity badges and status. | DASH-001 | High | 1 hour | Table displays incident history sorted by timestamp with search filter. | TODO | Incident log UI |
| **DASH-005** | Phase 12 | Implement Incident Drill-Down Detail View | Allow clicking an incident to view evidence, Gemini diagnosis, and remediation plan. | DASH-004 | High | 1 hour | Drill-down view presents full diagnosis explanation and plan steps. | TODO | Transparency UI |
| **DASH-006** | Phase 12 | Implement Interactive Approval Controls | Add "Approve" and "Reject" buttons for pending `Critical` severity incidents. | DASH-005 | Critical | 1.5 hours | Clicking Approve updates DB and resumes LangGraph workflow to execute action. | TODO | Scenario TS-011 UI |
| **DASH-007** | Phase 12 | Implement Operational Mode Toggle & Indicators | Display active `DRY_RUN` banner and quota usage meter in dashboard sidebar. | DASH-001 | Medium | 30 mins | Administrator clearly sees whether system is in dry-run or live mode. | TODO | Safety visibility |
| **DASH-008** | Phase 12 | Implement Evaluation Metrics Dashboard | Display MTTD, MTTR, diagnosis accuracy %, and recovery success % using Plotly gauge/bar charts. | DASH-001 | Medium | 1 hour | Summary metrics accurately calculated from database history. | TODO | Academic viva demo |
| **DASH-009** | Phase 12 | Test Dashboard Interactivity Locally | Conduct local walkthrough testing all buttons, chart interactions, and approval flows. | DASH-001 to DASH-008 | High | 1 hour | Dashboard runs smoothly without exceptions or lag. | TODO | UI validation |

---

## Phase 13 – Integration and End-to-End Testing

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **E2E-001** | Phase 13 | Build Controlled Testbed Containers | Create sample microservices in `test_environment/sample_apps/` with fault-injection endpoints. | Phase 12 | Critical | 1.5 hours | Testbed containers build cleanly via `docker compose -f docker-compose.test.yml build`. | TODO | Testbed preparation |
| **E2E-002** | Phase 13 | Test Scenario TS-001 (Stopped Container) | Trigger container stop; assert detection, diagnosis, restart plan, and successful recovery. | E2E-001 | Critical | 1 hour | Container restarted; incident marked `RESOLVED` in SQLite. | TODO | Core test |
| **E2E-003** | Phase 13 | Test Scenario TS-002 (Unhealthy Container) | Trigger failing healthcheck; assert detection, diagnosis, restart, and health restoration. | E2E-001 | High | 1 hour | Container recovered to `healthy`; health status verified. | TODO | Health check test |
| **E2E-004** | Phase 13 | Test Scenario TS-003 (High CPU Saturation) | Trigger runaway CPU calculation; assert detection after 3 cycles, diagnosis, and remediation. | E2E-001 | High | 1 hour | CPU drops below threshold; incident resolved. | TODO | Resource test |
| **E2E-005** | Phase 13 | Test Scenario TS-004 (High Memory Leak) | Trigger memory allocation; assert detection before OOM kill, diagnosis, and restart. | E2E-001 | High | 1 hour | Container recycled before kernel OOM kill occurs. | TODO | Memory leak test |
| **E2E-006** | Phase 13 | Test Scenario TS-005 (Application Error Logs) | Inject stack traces; assert log detection, Gemini diagnosis, and recovery action. | E2E-001 | High | 1 hour | Gemini identifies error traceback in logs correctly. | TODO | Log analysis test |
| **E2E-007** | Phase 13 | Test Scenario TS-006 (Abnormal Log Burst) | Inject log burst; assert volume anomaly detected and handled safely. | E2E-001 | Medium | 45 mins | Anomaly flagged; no crash of monitoring poller. | TODO | Log volume test |
| **E2E-008** | Phase 13 | Test Scenario TS-007 (Traffic Flooding) | Run traffic flood script against testbed; verify rate-limit or defensive action. | E2E-001 | Medium | 1 hour | Traffic anomaly detected and defensive response logged. | TODO | Pending design check |
| **E2E-009** | Phase 13 | Test Scenario TS-008 (Brute-Force Login) | Send 50 failed auth requests; verify security incident detection and IP mitigation. | E2E-001 | Medium | 1 hour | Brute-force pattern diagnosed and defensive action planned. | TODO | Pending design check |
| **E2E-010** | Phase 13 | Test Scenario TS-009 (Invalid Plan Rejection) | Feed malformed plan schema to Validator; assert clean rejection without crash. | E2E-001 | Critical | 45 mins | Validator rejects malformed plan; system halts safely. | TODO | Negative test |
| **E2E-011** | Phase 13 | Test Scenario TS-010 (Unsafe Action Rejection) | Inject unallowlisted action (`docker rm -f`); assert unconditional deterministic rejection. | E2E-001 | Critical | 45 mins | Unallowlisted action blocked 100%; zero Docker mutating calls. | TODO | Core safety test |
| **E2E-012** | Phase 13 | Test Scenario TS-011 (Critical Admin Approval) | Inject Critical incident; assert workflow pauses; approve via dashboard; assert execution. | E2E-001 | Critical | 1 hour | System pauses until approval button clicked in UI; then proceeds. | TODO | Human-in-the-loop |
| **E2E-013** | Phase 13 | Test Scenario TS-012 & TS-013 (Failure Handling) | Test Docker execution error and post-recovery health failure; assert escalation. | E2E-001 | High | 1 hour | Failed recoveries escalate to administrator; no crash loop. | TODO | Escalation test |
| **E2E-014** | Phase 13 | Test Scenario TS-014 (Full Lifecycle E2E) | Run unattended full lifecycle autonomous self-healing test on randomized fault. | E2E-001 to E2E-013 | Critical | 1.5 hours | Entire cycle completes autonomously; all data recorded in DB. | TODO | Master E2E demo |

---

## Phase 14 – Performance Evaluation, Benchmarking, and Final Thesis Documentation

| Task ID | Phase | Task | Description | Dependencies | Priority | Estimated Effort | Acceptance Criteria | Status | Notes |
|---|---|---|---|---|---|---|---|---|---|
| **EVAL-001** | Phase 14 | Establish Rule-Based Baseline Benchmark | Test standard Docker restart policy (`restart: always`) on crash-loop containers. | Phase 13 | High | 1 hour | Document failure mode: naive restart loops continuously without diagnosing root cause. | TODO | Baseline comparison |
| **EVAL-002** | Phase 14 | Execute Repeated Experiment Runs | Run 10 trials each across crash, resource, and error scenarios; record all timestamps. | EVAL-001 | High | 2 hours | 30+ total trial runs recorded in SQLite database. | TODO | Statistical rigor |
| **EVAL-003** | Phase 14 | Calculate MTTD and MTTR Metrics | Query database to compute average detection time (seconds) and recovery time (seconds). | EVAL-002 | High | 45 mins | Mean and standard deviation calculated for MTTD and MTTR. | TODO | Core thesis metric |
| **EVAL-004** | Phase 14 | Evaluate Diagnosis Accuracy | Compare Gemini Diagnoser root causes against injected ground truth faults. | EVAL-002 | High | 1 hour | Accuracy percentage computed (Target: >85% accuracy). | TODO | Core AI metric |
| **EVAL-005** | Phase 14 | Calculate Recovery Success Rate | Compute percentage of recovery attempts that successfully passed verification. | EVAL-002 | High | 30 mins | Recovery success rate computed (Target: >90% on recoverable faults). | TODO | Reliability metric |
| **EVAL-006** | Phase 14 | Generate Evaluation Charts & Graphs | Generate high-resolution Plotly / matplotlib charts comparing AUTOHEAL to baseline. | EVAL-003 to EVAL-005 | Medium | 1 hour | Visual comparison graphs generated and saved in `reports/figures/`. | TODO | Thesis visuals |
| **EVAL-007** | Phase 14 | Draft Final Project Report / Thesis | Compile MCA project report chapters: Introduction, Literature Review, Architecture, Evaluation. | EVAL-006 | Critical | 4 hours | Complete academic thesis document formatted per university guidelines. | TODO | MCA academic report |
| **EVAL-008** | Phase 14 | Prepare Viva Presentation Slides | Create PowerPoint / PDF slides highlighting problem, Agentic architecture, safety, and results. | EVAL-007 | High | 2 hours | Professional slide deck prepared for viva defense. | TODO | Viva presentation |
| **EVAL-009** | Phase 14 | Prepare Live Demonstration Script | Prepare scripted step-by-step live demo injecting faults and showing real-time healing in UI. | EVAL-008 | High | 1 hour | Rehearsed demo script ensuring 100% reproducible viva demonstration. | TODO | Final demonstration |
