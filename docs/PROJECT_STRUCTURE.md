# Project Structure Documentation

This document describes the directory organization, module responsibilities, and structural conventions of the **AUTOHEAL** repository.

---

## 1. Directory Tree Overview

```text
AUTOHEAL/
├── .env.example                  # Environment configuration template (tracked)
├── .gitignore                    # Git exclusion rules for secrets, builds, and artifacts
├── IMPLEMENTATION_PLAN.md        # Comprehensive phase-by-phase implementation roadmap
├── progress.txt                  # Real-time task progress tracker and session log
├── PROJECT_CONTEXT.md            # Master architectural specification and system context
├── README.md                     # Project overview, architecture, and developer onboarding
├── TASK_TRACKER.md               # Master granular task checklist across all 15 phases
├── docs/                         # Architecture, decision, environment, and testing specs
│   ├── DECISIONS.md              # Architectural Decision Records (ADRs) and rationale
│   ├── ENVIRONMENT.md            # Environment status, version verification, and compatibility
│   ├── PROJECT_STRUCTURE.md      # This document: directory and module responsibilities
│   └── TESTING_PLAN.md           # Test cases, verification criteria, and simulation plans
├── src/                          # Primary application source code
│   ├── __init__.py               # Package marker
│   ├── config/                   # Configuration management and settings loaders
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── models/                   # Shared Pydantic data schemas and incident definitions
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── monitor/                  # Deterministic Docker container metrics & state collector
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── detection/                # Incident detection engine and heuristic rule evaluators
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── agents/                   # Agentic AI reasoning components
│   │   ├── __init__.py
│   │   ├── diagnoser/            # Gemini-based Root Cause Analysis (RCA) agent
│   │   │   ├── __init__.py
│   │   │   └── .gitkeep
│   │   └── planner/              # Gemini-based Recovery Action Planning agent
│   │       ├── __init__.py
│   │       └── .gitkeep
│   ├── validator/                # Deterministic safety policy and allowlist validator
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── orchestration/            # LangGraph state machine and workflow controller
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── executor/                 # Controlled Docker remediation executor (Dry-run & Live)
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── verifier/                 # Post-remediation verification and health confirmation
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── database/                 # SQLite repository, migrations, and incident persistence
│   │   ├── __init__.py
│   │   └── .gitkeep
│   ├── dashboard/                # Streamlit administrator UI and Plotly visualizations
│   │   ├── __init__.py
│   │   └── .gitkeep
│   └── utils/                    # Common logging, date-time, and helper utilities
│       ├── __init__.py
│       └── .gitkeep
├── test_environment/             # Controlled Docker sandbox for fault injection testing
│   ├── sample_apps/              # Mock microservices (flaky web app, memory hog, etc.)
│   │   └── .gitkeep
│   ├── docker-compose.test.yml   # Multi-container testbed definition
│   └── .gitkeep
└── tests/                        # Automated test suites
    ├── __init__.py
    ├── unit/                     # Unit tests for individual deterministic modules
    │   ├── __init__.py
    │   └── .gitkeep
    ├── integration/              # Integration tests between sub-components
    │   ├── __init__.py
    │   └── .gitkeep
    └── e2e/                      # End-to-end incident injection and recovery workflows
        ├── __init__.py
        └── .gitkeep
```

---

## 2. Module Responsibilities and Boundaries

### `src/config/`
- **Purpose**: Centralized application configuration using environment variables.
- **Responsibilities**:
  - Load environment variables securely from `.env` using Pydantic Settings or python-dotenv.
  - Define system-wide constants (polling intervals, safety timeouts, threshold defaults).
  - Enforce `DRY_RUN` defaults to prevent accidental container state changes.

### `src/models/`
- **Purpose**: Shared, strongly typed data contracts used across the entire system.
- **Responsibilities**:
  - Define `ContainerState`, `MetricSnapshot`, `IncidentEvidence` data structures.
  - Define `DiagnosisResult` (root cause, confidence score, affected components).
  - Define `RemediationPlan` and `PermittedAction` enum/schemas.
  - Define `ValidationResult`, `ExecutionResult`, and `VerificationResult`.

### `src/monitor/`
- **Purpose**: Purely deterministic polling agent for Docker containers.
- **Responsibilities**:
  - Connect to Docker daemon via Docker SDK for Python.
  - Query container status (`running`, `exited`, `unhealthy`).
  - Stream container CPU and memory usage statistics.
  - Extract the latest tail of container logs (stdout/stderr).
  - **Boundary**: Purely read-only; does not perform AI reasoning or state mutation.

### `src/detection/`
- **Purpose**: Deterministic rule-based evaluation of collected telemetry.
- **Responsibilities**:
  - Compare metrics against established threshold policies.
  - Detect stopped or crashed containers.
  - Detect memory threshold breaches and CPU spikes.
  - Package detected anomalies into an `Incident` object with supporting evidence.
  - Assign preliminary incident severity levels (`Low`, `Medium`, `High`, `Critical`).

### `src/agents/diagnoser/`
- **Purpose**: LLM-driven root cause analysis component using Gemini (`google-genai`).
- **Responsibilities**:
  - Receive structured incident evidence and logs from detection module.
  - Format a constrained prompt instructing Gemini to analyze symptoms.
  - Enforce structured output via response schema (Pydantic).
  - Output probable cause, confidence level, and severity recommendation.

### `src/agents/planner/`
- **Purpose**: LLM-driven recovery action recommendation component.
- **Responsibilities**:
  - Ingest diagnosis findings and permitted recovery actions catalogue.
  - Select appropriate remediation steps (e.g., `restart_container`, `scale_service`, `clear_cache`).
  - Output a structured `RemediationPlan` with target identifiers and execution order.
  - **Boundary**: The planner can ONLY pick actions from the pre-approved allowlist.

### `src/validator/`
- **Purpose**: Strict deterministic safety gate.
- **Responsibilities**:
  - Intercept the `RemediationPlan` before any execution occurs.
  - Verify that proposed actions exist in the hardcoded allowlist.
  - Verify that target containers are within the allowed monitored scope.
  - Verify container preconditions (e.g., container exists, dependencies satisfied).
  - Check risk rating: flag `Critical` actions as requiring explicit administrator approval.
  - **Rule**: An LLM cannot override a rejection by the Validator.

### `src/orchestration/`
- **Purpose**: LangGraph-based state machine workflow.
- **Responsibilities**:
  - Maintain the lifecycle state (`AutohealState`) through the healing loop.
  - Define nodes: `MonitorNode`, `DiagnoseNode`, `PlanNode`, `ValidateNode`, `ApprovalNode`, `ExecuteNode`, `VerifyNode`.
  - Handle conditional branching (e.g., if invalid plan -> re-plan or escalate; if critical -> pause for approval).
  - Provide deterministic transition logic and fault resilience.

### `src/executor/`
- **Purpose**: Safe, controlled executor of approved remediation actions.
- **Responsibilities**:
  - Evaluate the `DRY_RUN` flag. If `true`, log the simulated action and exit cleanly.
  - Execute Docker SDK commands (e.g., `container.restart()`, `container.start()`).
  - Enforce execution timeouts and bounded retry limits.
  - Capture execution status, duration, and error details.

### `src/verifier/`
- **Purpose**: Post-remediation verification and confirmation.
- **Responsibilities**:
  - Wait for cooldown / stabilization period.
  - Query container status, healthcheck endpoints, and recent logs.
  - Compare post-remediation state against expected baseline.
  - Mark recovery as `SUCCESS`, `PARTIAL`, or `FAILED`.
  - Trigger rollback or administrator escalation if verification fails.

### `src/database/`
- **Purpose**: Persistent incident and audit record store using SQLite.
- **Responsibilities**:
  - Initialize tables for incidents, telemetry snapshots, diagnoses, plans, validation logs, approvals, and recovery results.
  - Provide CRUD operations for recording and historical querying.
  - Serve data to the Streamlit dashboard.

### `src/dashboard/`
- **Purpose**: Human-in-the-loop administrator control room built with Streamlit and Plotly.
- **Responsibilities**:
  - Real-time display of monitored container health and resource utilization.
  - Incident timeline and detailed drill-down (evidence, diagnosis, proposed plan).
  - Interactive approval UI: "Approve" or "Reject" buttons for High/Critical incidents.
  - Historical recovery audit logs and evaluation metrics charts.

### `test_environment/`
- **Purpose**: Isolated Docker sandbox for controlled fault injection.
- **Responsibilities**:
  - Contain reproducible sample microservices (e.g., Python Flask/FastAPI dummy apps).
  - Provide scenarios for crash loops, memory exhaustion, CPU stress, and simulated network floods.
  - Allow safe end-to-end testing without affecting the host or development machine.

---

## 3. Structural Rules & Invariants

1. **Deterministic Priority**: The Monitor and Validator are 100% deterministic code. No LLM decision can override deterministic safety checks.
2. **Layer Separation**: Agents (`src/agents/`) do not execute Docker commands directly. Execution is strictly delegated to `src/executor/` only after passing `src/validator/`.
3. **No Unapproved Dependencies**: Every external library must be justified, documented in `docs/DECISIONS.md`, and recorded in `docs/ENVIRONMENT.md`.
4. **Secret Isolation**: No credentials, tokens, or local `.env` files may be committed or stored in repository source code.
