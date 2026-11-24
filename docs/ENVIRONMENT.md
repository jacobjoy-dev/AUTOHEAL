# Environment Configuration and Dependency Verification

This document maintains the canonical record of verified software installed on the development host, target dependencies, verification commands, and installation status.

---

## 1. System Environment Status

### Verified Installed Host Software
The following core runtimes have been directly verified on the development system:

| Software | Verified Version | Verification Command | Status |
|---|---|---|---|
| **Python** | `3.14.2` | `python --version` | **VERIFIED INSTALLED** |
| **Docker Engine** | `29.9.2` | `docker --version` | **VERIFIED INSTALLED** |
| **Docker Compose** | `v5.6.1` | `docker-compose --version` | **VERIFIED INSTALLED** |
| **SQLite (Python runtime)** | `3.50.4` | `python -c "import sqlite3; print(sqlite3.sqlite_version)"` | **VERIFIED INSTALLED** |

> [!IMPORTANT]
> The four components above are confirmed present. The development machine is running **Windows** with Docker Desktop (WSL2 backend).

---

## 2. Target Python Dependencies (Verified in .venv)

The following libraries were selected as target dependencies for AUTOHEAL.
All of these libraries have been installed in the dedicated `.venv` virtual environment and verified compatible with Python 3.14.2:

| Package | Target Version | Purpose in AUTOHEAL | Status | Compatibility Check with Python 3.14.2 |
|---|---|---|---|---|
| `docker` | `7.2.0` | Docker SDK for Python (container monitoring & action execution) | **VERIFIED INSTALLED** | VERIFIED COMPATIBLE (pywin32 wheel available) |
| `langgraph` | `1.2.24` | Cyclical agent state graph orchestration & workflow control | **VERIFIED INSTALLED** | VERIFIED COMPATIBLE (StateGraph operational) |
| `google-genai` | `3.6.1` | Official Google Gemini SDK for Diagnoser and Planner agents | **VERIFIED INSTALLED** | VERIFIED COMPATIBLE (client initialized) |
| `streamlit` | `1.67.0` | Administrator web UI and human-in-the-loop approval interface | **VERIFIED INSTALLED** | VERIFIED COMPATIBLE (CLI operational) |
| `plotly` | `6.9.0` | Interactive metrics visualization and evaluation charts | **VERIFIED INSTALLED** | VERIFIED COMPATIBLE (v7.1.0 installed) |
| `pydantic` | `>=2.10.0` | Strongly-typed schemas for incident models and LLM outputs | **VERIFIED INSTALLED** | VERIFIED COMPATIBLE (pydantic-core wheel available) |
| `python-dotenv` | `>=1.0.1` | Safe environment variable loading from local `.env` | **VERIFIED INSTALLED** | VERIFIED COMPATIBLE |
| `pytest` | `>=8.3.0` | Automated testing framework for unit and integration suites | **VERIFIED INSTALLED** | VERIFIED COMPATIBLE (pytest 9.2.0 operational) |

---

## 3. Python 3.14 Compatibility Caution and Verification Protocol

Python `3.14.2` is a very modern release. Certain compiled C-extensions, Cython wheels, or dependencies of Streamlit, LangGraph, or Docker SDK might require pre-built binary wheels or pure-Python fallbacks.

### Compatibility Verification Steps (Phase 1):
1. **Virtual Environment Isolation**:
   Always create an isolated virtual environment (`.venv`) before attempting any installations:
   ```powershell
   python -m venv .venv
   .\.venv\Scripts\Activate.ps1
   ```
2. **Incremental Installation & Verification**:
   Do **NOT** install all packages at once with a monolithic command. Install and verify each package individually:
   - Step 1: Install `python-dotenv` and `pydantic` -> Verify import.
   - Step 2: Install `docker` SDK -> Verify Docker Engine connectivity.
   - Step 3: Install `google-genai` -> Verify import and basic client initialization.
   - Step 4: Install `langgraph` -> Verify import and basic graph definition.
   - Step 5: Install `streamlit` and `plotly` -> Verify CLI and headless launch.
   - Step 6: Install `pytest` -> Verify test runner execution.
3. **Fallback Plan if Python 3.14 incompatibility occurs**:
   If a binary wheel fails to build on Python 3.14 on Windows, identify the blocking package and test if an updated wheel or an alternate compatible version exists before escalating.

---

## 4. Verification Commands Reference (PowerShell)

Use these exact commands during Phase 1 to verify each component:

```powershell
# 1. Verify Host Core Runtimes
python --version
docker --version
docker compose version
python -c "import sqlite3; print('SQLite version:', sqlite3.sqlite_version)"

# 2. Check Docker Daemon Connectivity via CLI
docker ps

# 3. Create Virtual Environment
python -m venv .venv
.\.venv\Scripts\Activate.ps1

# 4. Verify Virtual Environment Python & Pip
python -c "import sys; print(sys.executable)"
python -m pip --version

# 5. Incremental Target Dependency Tests (To be executed in Phase 1)
python -c "import dotenv; print('dotenv: OK')"
python -c "import pydantic; print('pydantic:', pydantic.__version__)"
python -c "import docker; client = docker.from_env(); print('docker SDK: OK, ping:', client.ping())"
python -c "import google.genai; print('google-genai: OK')"
python -c "import langgraph; print('langgraph:', langgraph.__version__)"
python -c "import streamlit; print('streamlit:', streamlit.__version__)"
python -c "import plotly; print('plotly:', plotly.__version__)"
python -c "import pytest; print('pytest:', pytest.__version__)"
```

---

## 5. Actual Installed Versions Recording Area

*This section will be populated during Phase 1 tasks after virtual environment installation.*

| Target Package | Expected / Target Version | Actual Installed Version | Verified Date | Verified By |
|---|---|---|---|---|
| `docker` | `7.2.0` | `7.2.0` | 2026-11-24 | Verified (.venv) |
| `langgraph` | `1.2.24` | `1.2.24` | 2026-11-24 | Verified (.venv) |
| `google-genai` | `3.6.1` | `3.6.1` | 2026-11-24 | Verified (.venv) |
| `streamlit` | `1.67.0` | `1.67.0` | 2026-11-24 | Verified (.venv) |
| `plotly` | `6.9.0` | `7.1.0` | 2026-11-24 | Verified (.venv) |
| `pydantic` | `>=2.10.0` | `2.14.4` | 2026-11-24 | Verified (.venv) |
| `python-dotenv` | `>=1.0.1` | `1.2.4` | 2026-11-24 | Verified (.venv) |
| `pytest` | `>=8.3.0` | `9.2.0` | 2026-11-24 | Verified (.venv) |

---

## 6. Environment Setup Checklist

- [x] Host Python 3.14.2 verified.
- [x] Host Docker Engine 29.9.2 verified.
- [x] Host Docker Compose v5.6.1 verified.
- [x] Python sqlite3 runtime 3.50.4 verified.
- [x] `.gitignore` created protecting `.env` and `.venv`.
- [x] `.env.example` created with template variables.
- [x] Virtual environment (`.venv`) created.
- [x] Pip updated to latest version within `.venv` (pip 25.3).
- [x] Individual dependency compatibility tests conducted.
- [x] Local `.env` instantiated with template variables (`DRY_RUN=true`).
- [ ] Docker named pipe / socket connectivity verified via Python SDK.
