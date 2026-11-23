# AUTOHEAL
### An Agentic AI-Based Self-Healing System for Containerized Environments

[![Development Status](https://img.shields.io/badge/Status-Planning%20%26%20Design-blue.svg)](#development-status)
[![Python Version](https://img.shields.io/badge/Python-3.14.2%20(Verified)-brightgreen.svg)](#technology-stack)
[![Docker Engine](https://img.shields.io/badge/Docker%20Engine-29.9.2%20(Verified)-blue.svg)](#technology-stack)
[![License](https://img.shields.io/badge/License-MIT-lightgrey.svg)](#license)

---

## 1. Introduction

**AUTOHEAL** is an Agentic AI-powered self-healing system designed for microservice environments running on Docker. It automates the detection, diagnosis, remediation planning, safety validation, execution, and verification of container failures. 

AUTOHEAL blends deterministic reliability with modern Agentic AI reasoning:
- **Deterministic telemetry collection and safety validation** ensure zero hallucinated commands, zero unchecked mutations, and strict adherence to predefined safety boundaries.
- **Agentic AI (Gemini via `google-genai`)** performs nuanced root-cause analysis on unstructured logs and synthesizes remediation plans from a permitted allowlist.
- **LangGraph orchestration** structures the operational lifecycle as a state machine with built-in human-in-the-loop checkpoints for critical incidents.

---

## 2. Problem Statement

Microservices in containerized environments frequently encounter transient faults, resource exhaustion, configuration errors, and network anomalies. Conventional recovery relies either on manual human intervention—introducing delayed response times and high Mean Time To Recovery (MTTR)—or naive restart policies (`restart: always`), which blindly recycle failing containers and create infinite crash loops.

AUTOHEAL addresses this challenge by introducing an intelligent, closed-loop autonomic feedback architecture:
$$\text{Detect} \longrightarrow \text{Diagnose} \longrightarrow \text{Plan} \longrightarrow \text{Validate} \longrightarrow \text{Execute} \longrightarrow \text{Verify}$$

---

## 3. Project Objectives

1. **Non-Intrusive Monitoring**: Continuously collect container states, resource metrics (CPU/RAM), and logs using lightweight, deterministic Python polling without requiring heavy third-party agent daemons.
2. **Intelligent Root Cause Diagnosis**: Analyze incident logs and operational telemetry using Gemini to identify underlying failure causes, generating structured diagnosis reports with confidence scores.
3. **Constrained Remediation Planning**: Formulate recovery plans strictly selecting actions from a predefined, safe allowlist.
4. **Deterministic Safety Validation**: Enforce a strict Python validation gate. An LLM cannot override deterministic safety checks.
5. **Human-in-the-Loop Governance**: Provide an interactive administrator dashboard with Streamlit and Plotly, requiring manual sign-off for critical or high-risk recovery actions.
6. **Controlled Execution & Verification**: Support dry-run simulation by default, safe execution timeouts, and post-recovery health verification to confirm true recovery.
7. **Empirical Evaluation**: Benchmark self-healing performance against traditional static restart policies, measuring MTTD, MTTR, diagnosis accuracy, and recovery success rate.

---

## 4. Architecture Overview

```text
┌────────────────────────────────────────────────────────────────────────┐
│                        Controlled Docker Workload                       │
│                       (Microservices / Containers)                     │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Telemetry & Logs
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                    Deterministic Monitoring Layer                      │
│            (Docker SDK for Python: Status, Health, CPU, Memory)        │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Anomaly Threshold Evaluation
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                    Incident Detection & Packaging                      │
│             (Structured Incident Evidence, Initial Severity)           │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│               LangGraph State Machine Orchestration Engine             │
│                                                                        │
│   ┌────────────────────┐            ┌────────────────────┐             │
│   │  Diagnoser Agent   │ ─────────► │   Planner Agent    │             │
│   │ (Gemini 2.5 Flash) │            │ (Gemini 2.5 Flash) │             │
│   └────────────────────┘            └─────────┬──────────┘             │
│                                               │ Proposed Plan          │
│                                               ▼                        │
│                                     ┌────────────────────┐             │
│                                     │ Deterministic Gate │             │
│                                     │ (Python Validator) │             │
│                                     └─────────┬──────────┘             │
│                                               │                        │
│                  ┌────────────────────────────┴─────────────┐          │
│                  ▼                                          ▼          │
│         [ Low / Medium Risk ]                     [ Critical Severity ]│
│                  │                                          │          │
│                  │                                 ┌────────┴────────┐ │
│                  │                                 │ Admin Approval  │ │
│                  │                                 │ (Streamlit UI)  │ │
│                  │                                 └────────┬────────┘ │
│                  ▼                                          │ (Approved│
│          ┌───────────────┐ ◄────────────────────────────────┘          │
│          │   Recovery    │ (Dry-Run by Default; Controlled Execution)  │
│          │   Executor    │                                             │
│          └───────┬───────┘                                             │
│                  │                                                     │
│                  ▼                                                     │
│          ┌───────────────┐                                             │
│          │   Recovery    │ (Post-Recovery Healthcheck & Cooldown)      │
│          │   Verifier    │                                             │
│          └───────┬───────┘                                             │
└──────────────────┼─────────────────────────────────────────────────────┘
                   │ State & Audit Logs
                   ▼
┌────────────────────────────────────────────────────────────────────────┐
│                        SQLite Relational Store                         │
│     (Incidents, Evidence, Diagnoses, Plans, Approvals, Recovery Logs)  │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │
                                    ▼
┌────────────────────────────────────────────────────────────────────────┐
│                 Streamlit Administrator Console & Plotly                │
│    (Container Health, Telemetry Charts, Incident Timeline, Approvals)  │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 5. Technology Stack

| Layer | Component | Version | Environment Status |
|---|---|---|---|
| **Language** | Python | `3.14.2` | **VERIFIED INSTALLED** on Host |
| **Container Engine** | Docker Engine | `29.9.2` | **VERIFIED INSTALLED** on Host |
| **Orchestration Tool** | Docker Compose | `v5.6.1` | **VERIFIED INSTALLED** on Host |
| **Database** | SQLite (Python runtime) | `3.50.4` | **VERIFIED INSTALLED** on Host |
| **Docker SDK** | `docker` | `7.2.0` | TARGET / UNVERIFIED (Pending `.venv` install) |
| **Agent Orchestration** | `langgraph` | `1.2.24` | TARGET / UNVERIFIED (Pending `.venv` install) |
| **LLM Reasoning** | `google-genai` | `3.6.1` | TARGET / UNVERIFIED (Pending `.venv` install) |
| **Admin UI** | `streamlit` | `1.67.0` | TARGET / UNVERIFIED (Pending `.venv` install) |
| **Visualization** | `plotly` | `6.9.0` | TARGET / UNVERIFIED (Pending `.venv` install) |
| **Validation** | `pydantic` | `>=2.10.0` | TARGET / UNVERIFIED (Pending `.venv` install) |

---

## 6. Planned Features and Incident Scope

AUTOHEAL is designed to handle four primary incident scenarios:
1. **Container Crashes / Exits**: Detect sudden process termination, abnormal exit codes, and execute controlled restarts.
2. **Resource Exhaustion**: Detect runaway CPU loops (>85%) or memory leaks (>90% threshold) and remediate before system thrashing or kernel OOM kills.
3. **Traffic Flooding**: Detect volumetric request surges from single sources and apply rate-limiting policies *(Design in progress)*.
4. **Brute-Force Login Attempts**: Detect repeated failed authentication attempts in logs and apply security defensive actions *(Design in progress)*.

---

## 7. Project Structure

```text
AUTOHEAL/
├── .env.example              # Configuration template (tracked)
├── .gitignore                # Exclusion rules for secrets, DBs, and venv
├── IMPLEMENTATION_PLAN.md    # Master step-by-step implementation roadmap
├── progress.txt              # Live progress status log
├── PROJECT_CONTEXT.md        # Definitive architectural reference
├── README.md                 # Project documentation (this file)
├── TASK_TRACKER.md           # Detailed 15-phase task checklist
├── docs/                     # Architectural records, environment & test plans
│   ├── DECISIONS.md          # Architectural Decision Records (ADRs)
│   ├── ENVIRONMENT.md        # Environment specs and verification commands
│   ├── PROJECT_STRUCTURE.md  # Detailed module breakdown and contracts
│   └── TESTING_PLAN.md       # 14 test scenario specifications
├── src/                      # Source code
│   ├── config/               # Settings & environment variable loader
│   ├── models/               # Pydantic schemas (incidents, plans, states)
│   ├── monitor/              # Deterministic Docker metric poller
│   ├── detection/            # Rule-based anomaly evaluation
│   ├── agents/               # Gemini Diagnoser and Planner agents
│   ├── validator/            # Deterministic safety gate and allowlist check
│   ├── orchestration/        # LangGraph workflow state machine
│   ├── executor/             # Controlled Docker action executor (Dry-run/Live)
│   ├── verifier/             # Post-recovery health verifier
│   ├── database/             # SQLite database management
│   ├── dashboard/            # Streamlit dashboard & Plotly charts
│   └── utils/                # Helper utilities and logging
├── test_environment/         # Sandbox containers for fault injection
└── tests/                    # Unit, integration, and E2E test suites
```

---

## 8. Development Status

> [!NOTE]
> **Current Project Status: PHASE 0 – PLANNING AND INITIALIZATION**  
> The application code has **not** been implemented yet. The project management foundation, architectural records, environment verification criteria, and granular task tracker have been established.

---

## 9. Setup Instructions (Pending Phase 1 Execution)

*Detailed step-by-step setup commands will be finalized and tested during Phase 1. A general outline is provided below:*

1. **Clone the repository**:
   ```bash
   git clone <repository-url>
   cd AUTOHEAL
   ```

2. **Create and activate a Python virtual environment**:
   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```

3. **Configure Environment Secrets**:
   ```powershell
   Copy-Item .env.example .env
   ```
   *Edit `.env` to supply your `GEMINI_API_KEY`. Keep `DRY_RUN=true` during development.*

4. **Verify Host Services**:
   Ensure Docker Desktop is running with WSL2 integration enabled.

---

## 10. Future Enhancements

- Multi-host monitoring support via Docker Swarm or lightweight agent nodes.
- Advanced predictive resource scaling based on historical usage curves.
- Automated rollback configuration generation using git-backed infrastructure definitions.
- Webhook notifications for Slack, Discord, and PagerDuty.

---

## 11. License

This project is developed as an academic MCA main project. Distributed under the MIT License.
