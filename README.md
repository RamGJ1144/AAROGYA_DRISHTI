# Aarogya Drishti

**AI-Assisted Oral Screening, Telemedicine, Referral, and Patient-Care
Coordination Platform**

> **Hackathon demonstration only --- NOT FOR CLINICAL USE.**\
> This project is intended for demonstration with synthetic patient data
> and licensed demo images only. It is not a medical device and must not
> be used to diagnose, rule out, stage, or treat cancer.

Aarogya Drishti is a proposed digital platform that connects
village-level oral screening with Medical Officer review, specialist
consultation, diagnostic follow-up, patient navigation, and
district-level aggregate reporting. The goal is to improve continuity of
care and reduce the chance that a patient is lost between screening and
follow-up.

## Project goals

-   Digitally register patients and record purpose-specific consent.
-   Support guided oral-image capture and image-quality checks.
-   Keep AI outputs advisory, versioned, and separate from signed
    clinical decisions.
-   Enable Medical Officer review, referrals, appointments, and
    specialist workflows.
-   Track investigations, clinician-entered results, and follow-up
    tasks.
-   Provide Telugu and English user interfaces.
-   Support low-connectivity workflows with offline drafts and safe
    synchronization.
-   Show district-level aggregate indicators without exposing
    identifiable patient records.

## User roles

  -----------------------------------------------------------------------
  Role                                Main responsibilities
  ----------------------------------- -----------------------------------
  Health Worker                       Register patients, record consent,
                                      conduct screening, capture images,
                                      and submit cases

  Medical Officer                     Review cases, record clinical
                                      findings, sign reviews, and create
                                      referrals

  Specialist                          Review referred cases, consult,
                                      request investigations, and record
                                      clinical outcomes

  Patient Navigator                   Coordinate appointments, contact
                                      patients, and track missed visits
                                      or follow-up tasks

  District Administrator              View aggregate dashboards and
                                      system-level reports
  -----------------------------------------------------------------------

Access must be checked for both the user's role and their authorization
for the specific patient or facility. Access should be denied by
default.

## Proposed technology stack

  -----------------------------------------------------------------------
  Layer                               Technology
  ----------------------------------- -----------------------------------
  Frontend                            React, TypeScript, Vite,
                                      Progressive Web App (PWA)

  Backend API                         Python, FastAPI, Pydantic

  ORM and migrations                  SQLAlchemy, Alembic

  Database                            PostgreSQL

  Image quality checks                OpenCV

  AI integration                      Pluggable mock adapter by default;
                                      optional research model only

  Private image/report storage        S3-compatible object storage; MinIO
                                      for local development

  Offline support                     Service worker and IndexedDB queue

  Local deployment                    Docker Compose

  Testing                             pytest, API integration tests,
                                      Playwright end-to-end tests
  -----------------------------------------------------------------------

These are the proposed technologies. Update this section as
implementation decisions are finalized.

## Patient workflow

1.  A health worker registers a **synthetic patient** and records
    consent.
2.  The health worker records screening history and captures permitted
    demo images.
3.  Image validation and quality checks flag invalid, blurry, overly
    dark/bright, or glare-affected images for recapture.
4.  A mock/research AI adapter may return a clearly labelled advisory
    output.
5.  A Medical Officer independently reviews the case and records a
    signed clinical decision.
6.  If required, the case is referred for specialist consultation or a
    physical appointment.
7.  Investigations and diagnostic reports are tracked separately from AI
    outputs.
8.  An authorized clinician enters the simulated diagnosis or result.
9.  A patient navigator tracks follow-up, missed appointments, and
    treatment linkage.
10. The district dashboard displays aggregate indicators only.

**Important:** The workflow must remain usable if the AI service is
unavailable. An AI result must never advance a case by itself or
overwrite a clinician's signed conclusion.

## Repository structure

The following structure is recommended for this repository:

``` text
aarogya-drishti/
├── apps/
│   └── web/                 # React + TypeScript PWA
├── services/
│   ├── api/                 # FastAPI, authentication, workflows, migrations
│   └── ai/                  # Image-quality checks and mock/research adapters
├── infra/                   # Docker Compose and deployment configuration
├── docs/                    # Requirements, architecture, data model, API, security
├── tests/                   # Unit, integration, end-to-end, and security tests
├── .env.example             # Placeholder environment variables only
├── .gitignore
└── README.md
```

This is a starter layout; directories and services should be created as
implementation begins.

## Getting started

The repository may initially be empty. The commands below create the
proposed frontend and backend foundations. Run them from the repository
root.

### Prerequisites

-   Git
-   Node.js and npm
-   Python 3.11 or a compatible project-supported version
-   PostgreSQL, or Docker with Docker Compose
-   A modern browser; test mobile workflows on a real Android device

### 1. Create the frontend

``` bash
mkdir -p apps
cd apps
npm create vite@latest web -- --template react-ts
cd web
npm install
cd ../..
```

On Windows PowerShell, create directories with
`New-Item -ItemType Directory -Force apps`, then run the Vite commands
from the `apps` directory.

### 2. Create the backend

``` bash
mkdir -p services/api
cd services/api
python -m venv .venv
```

Activate the virtual environment:

**Windows PowerShell**

``` powershell
.\.venv\Scripts\Activate.ps1
```

**macOS/Linux**

``` bash
source .venv/bin/activate
```

Install the initial API dependencies:

``` bash
pip install fastapi "uvicorn[standard]" sqlalchemy alembic "psycopg[binary]" pydantic-settings python-multipart
pip freeze > requirements.txt
```

Create an initial `main.py`:

``` python
from fastapi import FastAPI

app = FastAPI(title="Aarogya Drishti API")

@app.get("/healthz")
def health_check():
    return {"status": "ok"}
```

Start the API from `services/api` with the virtual environment
activated:

``` bash
uvicorn main:app --reload
```

The local health check should be available at
`http://127.0.0.1:8000/healthz`. This only verifies the starter API;
database integration and application features must still be implemented.

### 3. Environment variables

Create `.env.example` with placeholder values for local configuration.
Never commit real passwords, tokens, signing keys, or production
credentials.

Example placeholders:

``` dotenv
DATABASE_URL=postgresql+psycopg://user:password@localhost:5432/aarogya_drishti
APP_ENV=development
SECRET_KEY=replace-with-a-local-development-secret
```

Keep `.env` out of version control. Add `.env`, virtual environments,
`node_modules`, build outputs, test artifacts, and uploaded demo files
containing sensitive information to `.gitignore`.

### 4. Planned implementation order

1.  Repository setup, Docker Compose, database migrations, synthetic
    seed data, and `/healthz`.
2.  Authentication, roles, facility-level and patient-level
    authorization.
3.  Patient registration, consent, and screening records.
4.  Image upload, private storage, image-quality checks, and recapture
    flow.
5.  Mock AI adapter with versioned outputs and explicit warnings.
6.  Medical Officer review, referrals, and case-state transitions.
7.  Appointments, specialist consultation, investigations, and
    clinician-entered results.
8.  Patient navigation, follow-up tasks, and aggregate dashboard.
9.  Telugu/English localization, offline queue, synchronization, and
    conflict handling.
10. Security tests, end-to-end tests, deployment, backup/restore, and
    demo rehearsal.

## Safety, privacy, and responsible AI

-   Use **synthetic patient data and licensed demo images only**.
-   Display **NOT FOR CLINICAL USE**, model version, and timestamp with
    every mock/research AI output.
-   Store AI observations separately from signed clinical reviews and
    clinician-entered diagnostic results.
-   Never present AI output as a diagnosis, cancer confirmation, staging
    decision, or assurance that a patient is cancer-free.
-   The AI service must be optional to the workflow; manual clinical
    review must remain possible during an outage.
-   Authenticate every API request and authorize by role **and**
    patient/facility relationship.
-   Keep images and reports in private storage and use short-lived,
    authorized access links.
-   Do not put personal health information or secrets in application
    logs.
-   Record important access and workflow changes in an audit trail.
-   Mark offline records as synced only after server acknowledgement;
    use idempotency keys to avoid duplicates.
-   Do not use this prototype for real clinical care without the
    necessary clinical validation, security review, regulatory
    assessment, and health-authority approvals.

## Testing checklist

As features are implemented, add tests for:

-   Registration and consent requirements.
-   Upload without consent, invalid image files, and oversized uploads.
-   Image-quality failures and recapture prompts.
-   Unauthorized access to another facility's or patient's record.
-   AI-service outage while the manual review workflow remains
    available.
-   Duplicate submissions and retry-safe synchronization.
-   Invalid case-state transitions.
-   Clinician review and diagnosis permissions.
-   Missed appointments creating follow-up tasks.
-   Expired or invalid video tokens.
-   Telugu/English layout and mobile usability.
-   Dashboard totals matching synthetic seed data.
-   Audit records for sensitive actions.

Do not mark a test as passed until it has been run and evidence has been
recorded.

## Documentation roadmap

Recommended documents to add under `docs/`:

-   `requirements.md` --- functional and non-functional requirements
-   `architecture.md` --- system components and data flow
-   `data-model.md` --- tables, relationships, and case states
-   `api.md` --- implemented endpoints and request/response examples
-   `security-and-privacy.md` --- threat model and security decisions
-   `test-plan.md` --- test scenarios and recorded evidence
-   `known-issues.md` --- limitations and outstanding work

## Project status

**Initial repository / implementation in progress.** The architecture
and workflow described here are proposed design targets, not proof that
all features are implemented. Update this section with verified
functionality, test evidence, deployment URL, and known limitations as
development progresses.

## Team

-   **Team name:** To be added
-   **Team members:** To be added
-   **Participating centre:** To be added

## License

Add the project's chosen license before distributing or reusing the
code. Verify that any demo images, datasets, SDKs, and third-party
assets permit the intended use.
