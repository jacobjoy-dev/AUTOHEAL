# Architectural Decision Records (ADRs) and Technology Choices

This document chronicles the architectural decisions, design choices, rejected alternatives, and pending items for **AUTOHEAL**.

---

## 1. Confirmed Architectural Decisions

### ADR-001: Deterministic Monitoring vs. LLM-Based Monitoring
- **Status**: CONFIRMED
- **Decision**: The Monitor component will be implemented purely as deterministic Python code querying the Docker Engine API via the Docker SDK for Python.
- **Rationale**:
  - LLMs are non-deterministic and computationally expensive for continuous metric polling.
  - Docker status checks, CPU percentages, and memory bytes are precise numeric data that deterministic threshold logic handles reliably and instantaneously.
  - Reduces API costs and latency to near zero during routine operations.

### ADR-002: LLM Integration Role (Diagnoser & Planner Only)
- **Status**: CONFIRMED
- **Decision**: LLMs (Gemini via `google-genai`) will be strictly confined to two bounded reasoning phases:
  1. **Diagnoser**: Synthesizing evidence (logs, metric changes, health status) to identify probable root causes.
  2. **Planner**: Selecting an appropriate sequence of remediation actions strictly from a predefined, allowlisted catalog.
- **Rationale**:
  - Root cause analysis from unstructured container logs benefits strongly from semantic LLM reasoning.
  - Confining the LLM prevents hallucinatory command generation and limits cost.

### ADR-003: Mandatory Deterministic Validator Safety Gate
- **Status**: CONFIRMED
- **Decision**: Every action proposed by the Planner must pass through an independent, deterministic Python Validator before execution. An LLM can NEVER override a deterministic rejection.
- **Rationale**:
  - Safety is paramount in autonomous systems. LLMs must never have direct, unmediated write access to container runtimes.
  - The Validator strictly enforces allowlisted action types, target boundaries, parameter safety, preconditions, and risk policies.

### ADR-004: Agent Orchestration Engine (LangGraph vs. Alternatives)
- **Status**: CONFIRMED
- **Decision**: Adopt **LangGraph** (target version 1.2.24) for agent workflow orchestration.
- **Rationale**:
  - LangGraph represents agentic workflows as explicit cyclical state graphs (`StateGraph`).
  - Supports deterministic conditional branching, state persistence, error routing, and built-in human-in-the-loop pauses (crucial for administrator approvals).

### ADR-005: Persistence Engine (SQLite)
- **Status**: CONFIRMED
- **Decision**: Use **SQLite** (Python sqlite3 runtime 3.50.4) for incident persistence, audit logs, and configuration state.
- **Rationale**:
  - Lightweight, serverless, self-contained, and requires zero external container or cloud service dependencies.
  - Ideal for an MCA main project demonstration and standalone deployment.
  - Provides reliable relational integrity and straightforward querying for the dashboard.

### ADR-006: User Interface (Streamlit + Plotly)
- **Status**: CONFIRMED
- **Decision**: Use **Streamlit** (target version 1.67.0) with **Plotly** (target version 6.9.0) for the administrator dashboard and interactive approval interface.
- **Rationale**:
  - Fast, pure-Python dashboard development without requiring frontend web frameworks.
  - Built-in UI controls (buttons, forms, data tables, metrics) enable effortless human-in-the-loop approvals.
  - Plotly provides interactive charts for container telemetry and evaluation metrics.

### ADR-007: Safe Execution Mode (Mandatory Dry-Run)
- **Status**: CONFIRMED
- **Decision**: The system will default to `DRY_RUN=true`. In dry-run mode, recovery actions are validated and simulated in logs and database records without applying actual Docker mutations.
- **Rationale**:
  - Enables safe beginner development and testing without risk of terminating critical containers or crashing the developer host machine.
  - Live execution is only enabled explicitly when testing is mature.

---

## 2. Rejected Alternatives and Rationale

| Rejected Technology / Approach | Evaluated For | Reason for Rejection |
|---|---|---|
| **CrewAI** | Agent Orchestration | Higher abstraction layer, less granular control over cyclical state machines, higher difficulty for strict deterministic safety gates, and rapid breaking changes. LangGraph was chosen for explicit state graph control. |
| **FastAPI** | Backend API | Adds unnecessary architectural complexity (REST routing, serialization, separate client-server processes) when Streamlit can query the local SQLite database and Python engine directly. |
| **WebSockets** | Real-time Communication | Complex to manage, stateful socket failure modes, unnecessary overhead for periodic polling intervals (e.g. 5-10s) suited to Streamlit autorefresh. |
| **Prometheus / Grafana** | Metrics Collection & UI | Heavy operational footprint, requires managing multi-container observability stacks, redundant for monitoring a small local Docker testbed in an MCA academic project. |
| **React / Next.js** | Frontend Interface | Requires JavaScript/TypeScript toolchain, Node.js runtime, build pipelines, and REST/GraphQL APIs, drastically increasing cognitive load for a beginner focusing on Agentic AI. |
| **Kubernetes (K8s)** | Container Orchestration | Severe resource overhead on a single developer machine; complex configuration (etcd, apiserver, CRDs). Docker Engine and Docker Compose provide the exact right balance of control and simplicity. |
| **Predictive Healing (ML forecasting)** | Failure Prevention | Non-deterministic, prone to false positives, requires large historical datasets for training. Reactive self-healing upon verified failure conditions provides rigorous, measurable benchmarks. |

---

## 3. Pending Decisions and Open Architecture Questions

### PENDING-001: Traffic Flooding Detection Methodology
- **Status**: PENDING
- **Context**: Incident category 3 involves detecting traffic flooding from a single source.
- **Options Under Consideration**:
  - *Option A*: Parse reverse proxy access logs (e.g., Nginx access log rate analysis per IP).
  - *Option B*: Inspect Docker container network I/O stats (packets/bytes per second rate change).
  - *Option C*: In-application request counter middleware in the testbed container.
- **Next Step**: Design controlled test container in Phase 4 / Phase 13 and evaluate least complex signal.

### PENDING-002: Brute-Force Login Detection Methodology
- **Status**: PENDING
- **Context**: Incident category 4 involves detecting repeated unauthorized login attempts.
- **Options Under Consideration**:
  - *Option A*: Log inspection for repeated `401 Unauthorized` or failed authentication strings within a sliding time window.
  - *Option B*: Application telemetry endpoint reporting failed auth metrics to the monitor.
- **Next Step**: Standardize on structured log parsing (Option A) during Phase 4 implementation.

### PENDING-003: Metric Anomaly Threshold Policies
- **Status**: PENDING
- **Context**: Establishing thresholds for CPU and Memory anomaly triggers.
- **Tentative Proposal**:
  - Sustained CPU > 85% for 3 consecutive poll cycles (15 seconds).
  - Memory usage > 90% of container limit or rapid increase (>20MB/s).
- **Next Step**: Benchmark against sample testbed applications in Phase 4.

### PENDING-004: Recovery Verification and Rollback Strategy
- **Status**: PENDING
- **Context**: What should the system do if recovery execution succeeds, but the verifier determines the container remains unhealthy?
- **Options Under Consideration**:
  - *Option A*: Bounded retry (max 1 retry), then escalate to `Critical` administrator alert.
  - *Option B*: Roll back to previous configuration / restart with clean state.
- **Next Step**: Define per-action rollback matrices during Phase 10 design.

---

## 4. Decision Log

| Date | ID | Decision Title | Author / Role | Status |
|---|---|---|---|---|
| 2026-11-23 | ADR-001 | Deterministic Monitoring using Docker SDK | Project Team | CONFIRMED |
| 2026-11-23 | ADR-002 | Bounded Gemini LLM Role for Diagnosis & Planning | Project Team | CONFIRMED |
| 2026-11-23 | ADR-003 | Mandatory Deterministic Validator Safety Gate | Project Team | CONFIRMED |
| 2026-11-23 | ADR-004 | Adoption of LangGraph for State Orchestration | Project Team | CONFIRMED |
| 2026-11-23 | ADR-005 | SQLite for Persistence and Audit Logging | Project Team | CONFIRMED |
| 2026-11-23 | ADR-006 | Streamlit and Plotly for Administrator Console | Project Team | CONFIRMED |
| 2026-11-23 | ADR-007 | Mandatory Default Dry-Run Mode | Project Team | CONFIRMED |
| 2026-11-23 | PEND-001 | Traffic Flooding Signal Selection | Project Team | PENDING |
| 2026-11-23 | PEND-002 | Brute-force Login Detection Signal | Project Team | PENDING |
| 2026-11-23 | PEND-003 | Metric Anomaly Threshold Calibration | Project Team | PENDING |
| 2026-11-23 | PEND-004 | Post-Verification Rollback Matrix | Project Team | PENDING |
