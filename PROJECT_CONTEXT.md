# Project Context Specification: AUTOHEAL

This document provides the authoritative, self-contained architectural context, system boundaries, technology decisions, and operational specifications for **AUTOHEAL**.

---

## 1. Project Identity

- **Project Title**: AUTOHEAL – An Agentic AI-Based Self-Healing System for Containerized Environments
- **Academic Context**: Master of Computer Applications (MCA) Main Project
- **Developer Level**: Beginner in Agentic AI and Backend Development
- **Engineering Philosophy**: Small, verifiable, incremental steps; deterministic safety gates; strict separation of reasoning from execution; zero unvetted dependencies.

---

## 2. Problem Statement

Modern microservice applications deployed in containerized environments (such as Docker) frequently suffer from unexpected downtime, runtime crashes, resource exhaustion (CPU/memory spikes), and malicious traffic anomalies. 

Traditional recovery approaches rely on:
1. **Manual intervention**: Site Reliability Engineers (SREs) inspect logs, diagnose root causes, and execute fixes. This introduces high Mean Time To Recovery (MTTR) and human error under stress.
2. **Naive rule-based restart policies**: Docker restart policies (`restart: always` or `on-failure`) repeatedly restart failing containers without addressing root causes (e.g. bad configs or database drops), leading to infinite crash-loops.

**AUTOHEAL** bridges this gap by combining **deterministic monitoring and safety validation** with **Agentic AI reasoning (Gemini via `google-genai`)** to perform automated, intelligent root-cause diagnosis, structured remediation planning, and verified recovery.

---

## 3. Project Objectives

1. **Autonomous Incident Detection**: Continuously monitor local Docker containers for crashes, health degradation, and resource saturation using deterministic, non-LLM telemetry collection.
2. **Intelligent Root Cause Analysis**: Leverage Gemini to analyze unstructured container logs and metric evidence, returning structured diagnoses with confidence metrics.
3. **Constrained Remediation Planning**: Formulate recovery action plans strictly selected from a predefined, safe allowlist.
4. **Deterministic Safety Validation**: Guarantee that no action is executed without passing rigorous allowlist, target scope, precondition, and risk checks. LLMs cannot override deterministic safety gates.
5. **Human-in-the-Loop Governance**: Require explicit administrator confirmation via an interactive dashboard before executing actions on `Critical` severity incidents.
6. **Controlled Execution & Verification**: Execute recovery actions through a controlled interface supporting dry-run simulation, followed by post-recovery health verification.
7. **Comprehensive Auditability**: Maintain a complete chronological record of incidents, telemetry, diagnoses, plans, validator verdicts, and execution outcomes in SQLite.

---

## 4. System Scope and Boundaries

### In Scope
- Local single-host Docker environments managed via Docker Engine daemon.
- Multi-container workloads orchestrated via Docker Compose for testing.
- Four distinct incident categories: container crashes, resource exhaustion, traffic flooding, and brute-force login attempts.
- LangGraph cyclical state machine workflow.
- Streamlit administrator dashboard with Plotly visual analytics.
- Dry-run simulation mode.

### Out of Scope (Explicitly Excluded)
- Kubernetes (K8s) cluster orchestration.
- Cloud providers (AWS, GCP, Azure) production infrastructure.
- Distributed tracing (OpenTelemetry/Jaeger).
- Prometheus/Grafana server deployments.
- FastAPI / REST API microservice architecture.
- Real-time WebSockets.
- Predictive machine learning forecasting (healing is strictly reactive).

---

## 5. Architectural Components and Responsibilities

| Component | Nature | Primary Responsibility | LLM Required? |
|---|---|---|---|
| **Monitor** | Deterministic Python | Polls Docker daemon for container status, health, CPU%, memory, and recent log tail. | **NO** |
| **Detection Engine** | Deterministic Python | Evaluates telemetry against threshold rules; constructs structured `Incident` evidence. | **NO** |
| **Diagnoser Agent** | Agentic AI (Gemini) | Analyzes logs and evidence; identifies probable root causes; assigns confidence and severity. | **YES** |
| **Planner Agent** | Agentic AI (Gemini) | Selects remediation steps strictly from a predefined permitted-action allowlist. | **YES** |
| **Validator** | Deterministic Python | Strict safety gate; enforces allowlists, targets, preconditions, risk rating, and approval rules. | **NO (Deterministic)** |
| **Admin Approval** | Human-in-the-Loop | Streamlit interactive UI where administrators approve or reject High/Critical actions. | **NO** |
| **Recovery Executor** | Deterministic Python | Safely executes approved Docker actions; strictly enforces dry-run mode and execution timeouts. | **NO** |
| **Recovery Verifier** | Deterministic Python | Assesses container health post-remediation; verifies restoration; triggers escalation on failure. | **NO** |
| **Orchestrator** | LangGraph StateGraph | Manages workflow state transitions, cyclical retry loops, error routing, and human pauses. | **NO** |
| **Database** | SQLite Relational Store | Persists incidents, telemetry snapshots, agent reasoning, audit logs, and metrics. | **NO** |
| **Dashboard** | Streamlit + Plotly | Visualizes container health, telemetry charts, incident history, and provides approval controls. | **NO** |

---

## 6. End-to-End Workflow

```text
[ Docker Containers ]
         │
         ▼
[ Monitor Component ] (Deterministic: Status, Health, CPU, Memory, Logs)
         │
         ▼
[ Incident Detection ] (Rule Evaluation: Crash, Threshold Exceeded, Anomalies)
         │  (Incident Detected)
         ▼
[ Diagnoser Agent ] (Gemini: Root Cause Analysis, Severity, Confidence)
         │
         ▼
[ Planner Agent ] (Gemini: Selects Actions from Permitted Allowlist)
         │
         ▼
[ Validator Component ] (Deterministic Safety Checks: Allowlist, Scope, Preconditions)
         │
      ┌──┴────────────────────────┐
   Approved                   Rejected ──► [ Re-plan or Escalate to Admin ]
      │
   Requires Approval? (e.g. Critical / High Risk)
      ├── Yes ──► [ Streamlit Dashboard Approval ] ──► (Rejected ──► Abort)
      │                     │ (Approved)
      └── No ───────────────┘
         │
         ▼
[ Recovery Executor ] (Dry-Run / Live Docker Mutation with Timeouts)
         │
         ▼
[ Recovery Verifier ] (Post-remediation Health Check & Cooldown Evaluation)
         │
      ┌──┴────────────────────────┐
   Verified (Success)         Unverified (Fail) ──► [ Rollback / Admin Alert ]
         │
         ▼
[ SQLite Database ] ◄── (All state, evidence, decisions & audit logs recorded)
         │
         ▼
[ Streamlit Admin Dashboard ] (Telemetry, Timeline, Visualizations, Evaluation)
```

---

## 7. Incident Categories

1. **Category 1: Container Crashes / Stopped Containers**
   - *Status*: CONFIRMED
   - *Detection*: Docker status equals `exited` or non-zero exit code detected.
   - *Remediation*: Container restart, log analysis, configuration validation.
2. **Category 2: Resource Exhaustion (High CPU / High Memory)**
   - *Status*: CONFIRMED
   - *Detection*: CPU > 85% sustained for 3 cycles (15s); Memory > 90% of container limit.
   - *Remediation*: Container restart, clearing cache, temporary throttling.
3. **Category 3: Traffic Flooding from a Single Source**
   - *Status*: PROPOSED / PENDING DESIGN
   - *Detection*: Rate threshold breach (e.g., >100 req/s from single IP in proxy logs or network RX spike).
   - *Remediation*: Mock IP block, rate limit application.
4. **Category 4: Brute-Force Login Attempts**
   - *Status*: PROPOSED / PENDING DESIGN
   - *Detection*: Sliding window log analysis matching consecutive `401 Unauthorized` responses.
   - *Remediation*: Rate limit, IP block, notification.

---

## 8. Severity Policy and Permitted Action Guidelines

| Severity Level | Definition | Autonomous Execution Permitted? | Validator Safety Standard |
|---|---|---|---|
| **Low** | Minor anomaly; non-critical service degradation; single transient error. | **YES** | Standard allowlist and parameter check. |
| **Medium** | Service unhealthy or crashed; single microservice affected. | **YES** | Standard allowlist, target scope, and precondition verification. |
| **High** | Resource exhaustion, rapid memory leak, security anomaly. | **CONDITIONAL** | Stricter checks; dry-run verification; bounded retry limit. |
| **Critical** | Core infrastructure failure, cascading outage, multi-container impact. | **NO (Requires Human Approval)** | Mandatory administrator sign-off via Streamlit dashboard before execution. |

### Predefined Permitted Actions Allowlist (CONFIRMED)
- `restart_container`: Gracefully restart a specific monitored container.
- `start_container`: Start an exited monitored container.
- `stop_container`: Safely stop a runaway container exceeding safety limits.
- `reload_service`: Trigger application-level reload signal (`SIGHUP`).
- `clear_cache`: Execute approved internal cache clearance command inside container.
- `scale_service`: Scale service replicas via Docker Compose (Phase 9+).
- `block_ip`: Block abusive source IP (Pending final network policy design).

> [!CAUTION]
> Actions such as `docker rm -f`, `docker system prune`, modifying host files, or running arbitrary shell scripts are strictly **FORBIDDEN** and hard-blocked by the Validator.

---

## 9. Technology Stack and Verification Status

| Layer | Technology | Version | Status |
|---|---|---|---|
| **Programming Language** | Python | `3.14.2` | **VERIFIED INSTALLED** |
| **Container Engine** | Docker Engine | `29.9.2` | **VERIFIED INSTALLED** |
| **Compose Tool** | Docker Compose | `v5.6.1` | **VERIFIED INSTALLED** |
| **Relational Database** | Python sqlite3 runtime | `3.50.4` | **VERIFIED INSTALLED** |
| **Container SDK** | Docker SDK for Python (`docker`) | `7.2.0` | **TARGET / UNVERIFIED** |
| **Orchestration** | LangGraph | `1.2.24` | **TARGET / UNVERIFIED** |
| **LLM Provider SDK** | Google GenAI SDK (`google-genai`) | `3.6.1` | **TARGET / UNVERIFIED** |
| **Dashboard UI** | Streamlit | `1.67.0` | **TARGET / UNVERIFIED** |
| **Plotting Library** | Plotly | `6.9.0` | **TARGET / UNVERIFIED** |
| **Data Validation** | Pydantic | `>=2.10.0` | **TARGET / UNVERIFIED** |
| **Config Loader** | python-dotenv | `>=1.0.1` | **TARGET / UNVERIFIED** |
| **Test Runner** | pytest | `>=8.3.0` | **TARGET / UNVERIFIED** |

---

## 10. Evaluation Metrics

To evaluate AUTOHEAL academically and empirically for the MCA main project thesis:

1. **Mean Time To Detect (MTTD)**: Elapsed seconds from fault injection to incident detection.
2. **Mean Time To Remediate (MTTR)**: Elapsed seconds from incident detection to verified healthy recovery.
3. **Diagnosis Accuracy**: Percentage of test scenarios where Gemini Diagnoser correctly identifies the true injected fault.
4. **Recovery Success Rate**: Percentage of recovery actions that successfully restore container health.
5. **False Positive Rate**: Frequency of benign container fluctuations erroneously flagged as incidents.
6. **Safety Violation Rejection Rate**: Must be **100%** (every unallowlisted or dangerous action must be rejected by the Validator).
7. **Baseline Comparison**: Comparative performance against standard Docker restart policies (`restart: always` / `on-failure`).

---

## 11. Known Unknowns and Pending Decisions

1. **Traffic Flooding Signal Calibration**: Finalizing the exact mechanism (reverse proxy access logs vs. Docker network I/O counters) to detect single-source traffic flooding.
2. **Brute-Force Detection Parser**: Defining the exact log signature and sliding window duration for brute-force login detection.
3. **Python 3.14.2 Library Wheel Availability**: Validating that all target dependencies install and run smoothly without C-extension compilation hurdles on Windows.
4. **Rollback Strategy**: Establishing standardized multi-step rollback actions for complex composite failures.

---

## 12. Current Project Implementation Status

- **Status**: **NOT STARTED (PLANNING PHASE)**
- **Application Code Implemented**: 0%
- **Current Phase**: Phase 0 (Planning, Documentation, Skeleton, Task Tracker)
- **Next Milestone**: Phase 1 (Virtual Environment Setup & Compatibility Verification)
