# AISD Lab 5 - TalentHub Campus Placement Management System

TalentHub is a full-stack web application that manages the college placement process end-to-end:
students apply for jobs, companies shortlist eligible students, interviews get scheduled and
recorded, offers are issued and accepted, and the placement coordinator tracks placement statistics.

Built with a clean layered architecture:

```
backend/
  app/
    api/          # Router layer (FastAPI routers)
    core/         # Database config, JWT auth / security
    models/       # SQLAlchemy models (Student, Company, JobListing, Application, Interview, Offer, User)
    repositories/ # Data-access layer
    services/     # Business logic (eligibility rules, placement workflow, auth)
    schemas/      # Pydantic request/response schemas
  main.py         # FastAPI entry point (seeds demo data on first run)
  requirements.txt
frontend/
  src/
    pages/        # Login, StudentDashboard, CompanyDashboard, CoordinatorDashboard
    components/   # Header, StatusBadge
    api.js        # Axios client with JWT auth
  package.json
```

---

## Step-by-step: how to run it locally

### 1. Prerequisites

- **Python 3.9+** installed and added to PATH (tested on Python 3.11 / 3.12 / 3.13).
- **Node.js v18+** and npm installed.
- A terminal — PowerShell / Command Prompt on Windows, or any shell on macOS/Linux.

### 2. Get the code

```bash
git clone https://github.com/sunayanakamat04/AISDLAB5_TALENTHUB.git
cd AISDLAB5_TALENTHUB
```

### 3. Start the backend (Terminal window 1)

```bash
cd backend

# Create a virtual environment
python -m venv venv

# Activate it
# Windows PowerShell:
.\venv\Scripts\activate
# macOS / Linux:
source venv/bin/activate

# Install dependencies
pip install -r requirements.txt

# Set the JWT signing secret (kept outside the source code)
# Windows PowerShell:
$env:TALENTHUB_SECRET_KEY = "any-long-random-string"
# macOS / Linux:
export TALENTHUB_SECRET_KEY="any-long-random-string"

# Start the FastAPI server
uvicorn main:app --reload --port 8000
```

If `TALENTHUB_SECRET_KEY` is not set, a development fallback key is used.
- On first run the server automatically creates `talenthub.db` (SQLite) and seeds it with demo
  students, companies, job listings and login accounts — no manual setup needed.
- **Backend verification:** open http://127.0.0.1:8000/docs to see the interactive Swagger API docs,
  or http://127.0.0.1:8000/ for a welcome message.

### 4. Start the frontend (Terminal window 2 — leave the backend running)

```bash
cd frontend

# Install npm packages
npm install

# Start the Vite dev server
npm run dev
```

- **Frontend access:** open http://localhost:5173 in your browser.

### 5. Log in and try it out

On the login screen, click any of the demo-account buttons to auto-fill the email (password is
always `password123`), then click **Sign In**.

---

## Running the tests (TDD)

```bash
cd backend
pip install -r requirements.txt -r requirements-dev.txt
python -m pytest -q
```

Each test uses a fresh in-memory SQLite database with synthetic data. The suite covers:

| Folder | What it checks |
|---|---|
| `tests/unit` | Eligibility rules R1–R4 with boundary values; the full status state machine |
| `tests/service` | Database constraints, applying, shortlisting runs (atomic, idempotent), interviews, offers, coordinator functions, privacy |
| `tests/api` | Login, 401/403 handling, access control between students and companies, input validation, the full workflow |
| `tests/architecture` | Layer rules: no queries in routers, no HTTP errors in services, eligibility in one function |

---

## Demo accounts (seeded automatically on first backend start)

All demo accounts use the password `password123`.

| Role        | Email                          |
|-------------|---------------------------------|
| Student     | rahul@student.talenthub.edu     |
| Student     | priya@student.talenthub.edu     |
| Student     | aman@student.talenthub.edu      |
| Company (HR)| hr@abctech.com                  |
| Company (HR)| hr@xyzcorp.com                  |
| Coordinator | coordinator@talenthub.edu       |

Demo students:
- **Rahul**: CSE, CGPA 8.2, 1 active backlog
- **Priya**: ISE, CGPA 9.1, 0 backlogs
- **Aman**: ECE, CGPA 6.4, 3 backlogs

---

## Workflow summary

1. **Student** logs in, views eligible job listings, and applies.
2. **Company** logs in, posts job listings, views applicants, shortlists/rejects, schedules
   interviews, records pass/fail results, and issues offers to students who passed.
3. **Student** accepts or declines the offer.
4. **Coordinator** logs in to view placement statistics and a full table of all applications
   across the system.
