# LUNAR — Autonomous Software Maintenance

LUNAR is an AI-powered technical-debt and code-maintenance platform for public GitHub repositories.

## Core loop

SCAN → UNDERSTAND → PRIORITIZE → REPAIR → APPROVE → VERIFY → MEASURE → REMEMBER

LUNAR analyzes a repository with parallel specialist agents, consolidates findings into an evidence-based technical-debt model, prepares fixes in a temporary workspace, requires approval for medium/high-risk changes, verifies approved changes, and compares before/after metrics.

## MVP stack

- Frontend: React + Vite
- Backend: Python + FastAPI
- Database: MongoDB
- Repository source: public GitHub URL
- Analysis engines: deterministic scanners + pluggable AI/Bob prompts
- Verification: Python/Node build and test commands where available

## Six agents

1. Code Intelligence Agent
2. Security & Dependency Agent
3. Test & Documentation Agent
4. Technical Debt Analyst
5. Repair Agent
6. Verification Agent

The first three analysis agents are designed to run in parallel. The analyst consolidates their structured findings. Repair occurs in a temporary copy. Verification runs after approval.

## Run

### Backend

```bash
cd backend
python -m venv .venv
# Windows: .venv\Scripts\activate
# macOS/Linux: source .venv/bin/activate
pip install -r requirements.txt
uvicorn app.main:app --reload
```

Optional MongoDB:
```text
MONGO_URL=mongodb://localhost:27017
MONGO_DB=lunar
```

### Frontend

```bash
cd frontend
npm install
npm run dev
```

Set `VITE_API_URL=http://localhost:8000`.

## Demo repository

Use a small public sample hospital-management repository. For the controlled hackathon demo, seed 5–10 known maintenance problems:
- hard-coded credential
- duplicated validation
- high-complexity function
- outdated dependency
- missing tests
- TODO/FIXME accumulation
- weak error handling
- missing documentation

Do not use real patient data.

## Product safety

LUNAR never edits the user's public repository directly. Analysis is read-only. Proposed fixes are applied only to an isolated temporary workspace. Medium/high-risk fixes require explicit approval.

## IBM Bob 2.0

IBM Bob 2.0 is used as the agentic development partner during implementation and as the model/orchestration layer for the maintenance workflow where available. The `bob-prompts/` directory contains the structured prompts and contracts used to reproduce the six-agent workflow in Bob.

## Metrics

Measure the actual demo:
- analysis time
- manual investigation steps
- findings detected
- true positives / false positives
- time to understand the highest-priority debt
- time to prepare a fix
- verification time
- health score before/after
- technical-debt hours before/after
- test coverage before/after
- regression count
- manual interventions

Never claim benchmark numbers that were not measured.


## Built-in hospital demo

LUNAR includes a synthetic hospital-management repository under `demo-repo/`. It contains only fictional data.

You can analyze it without publishing anything to GitHub by entering `demo://hospital` in the dashboard.
The frontend also includes a **LOAD HOSPITAL DEMO** button.

### Local run

Backend:

```bash
cd backend
python -m pip install -r requirements.txt
python -m uvicorn app.main:app --reload --port 8000
```

Frontend (second terminal):

```bash
cd frontend
npm install
npm run dev
```

Open the Vite URL shown in the terminal, normally `http://localhost:5173`.

### Demo flow

1. Click **LOAD HOSPITAL DEMO**.
2. Click **START ANALYSIS**.
3. Review the prioritized findings.
4. Select the hard-coded credential finding.
5. Choose **PREPARE FIX**.
6. Review the generated diff.
7. Approve the repair.
8. Run verification.
9. Show the before/after health, findings, and debt metrics.

The public GitHub workflow remains available: paste a public repository URL instead of `demo://hospital`.

## Repository maintenance layer

The repository intentionally separates **LUNAR itself** from the **demo codebase it maintains**:

```text
LUNAR/                 # product
├── backend/            # scanners, scoring, repair, verification
├── frontend/           # maintenance dashboard
├── bob-prompts/        # Bob agent contracts
└── docs/               # architecture and demo documentation

demo-repo/              # controlled maintenance target
├── application code
├── tests/
└── requirements.txt
```

`demo-repo/` is intentionally imperfect. It contains synthetic maintenance debt so the product can demonstrate detection, prioritization, repair, and verification without using real customer or patient data.

### Maintenance categories

LUNAR currently demonstrates detection of complexity, duplication/maintainability issues, security patterns, dependency concerns, testing gaps, documentation gaps, TODO/FIXME markers, and weak error handling.

### Engineering quality

The project includes backend tests under `backend/tests/` plus a CI workflow under `.github/workflows/ci.yml`. The bundled demo repository is also exercised as an integration-style scanner fixture.

See [`TECHNICAL_DEBT.md`](TECHNICAL_DEBT.md) and [`docs/MAINTENANCE_WORKFLOW.md`](docs/MAINTENANCE_WORKFLOW.md) for the scoring and maintenance methodology.
