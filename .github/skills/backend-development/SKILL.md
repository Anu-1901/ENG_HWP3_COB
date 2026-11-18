---
name: fastapi-backend-development
description: "Handles professional-grade development, refactoring, and test generation of a FastAPI backend using strict modular design patterns, Pydantic settings, standardized session management, and pytest."
version: "2.0.0"
category: "Backend Architecture & Development"
tools: ["read", "edit", "search", "execute shell"]
---

# FastAPI Production-Grade Backend Runbook

Use this skill to develop, refactor, or test endpoints and business modules for our FastAPI application. This runbook enforces professional enterprise-grade patterns, clean modular boundaries, strict state/database lifecycle management, and a mandatory test-driven design.

---

## ⚡ Global System & Runtime Constraints

1. **Port Constraints**: The FastAPI application must always run on port `5000` (e.g., initialized using `uvicorn app.main:app --port 5000 --reload`).
2. **Environment & Runtime Context**: All executions, imports, and terminal tests must run relative to the project root directory. Do not run nested terminal commands.
3. **Settings & Config Management**: 
   - Never hardcode secrets, credentials, database URLs, or API keys.
   - Use Pydantic's `BaseSettings` (from `pydantic-settings`) defined inside `app/core/config.py` to manage configuration variables loaded strictly from environment variables or a `.env` file.

---

## 📂 Feature-Driven Directory Layout

For every major feature (e.g., `billing`, `analytics`), you must strictly adhere to the following directory layout:

```text
your-project-root/
├── app/
│   ├── main.py                       # Application bootstrap, routing mounts, and middleware only
│   ├── core/                         # Global configurations, logging, settings, global dependencies
│   │   ├── config.py                 # Pydantic Settings class
│   │   ├── database.py               # Database engine & SessionLocal configurations
│   │   └── logging.py                # Standardized system logger
│   └── features/                     # Feature-specific domains
│       └── <feature_name>/
│           ├── __init__.py
│           ├── router.py             # Route definitions, dependencies, and HTTP mappings
│           ├── handler.py            # Main business logic orchestrator
│           ├── services/             # Extracted business logic (if handler > 500 lines)
│           │   └── service_helper.py
│           ├── models.py             # Database/ORM models (e.g., SQLAlchemy/SQLModel)
│           └── schemas.py            # Pydantic schemas (Request, Response, Internal)
└── tests/
    └── features/
        └── test_<feature_name>.py    # Feature-specific unit and integration tests
