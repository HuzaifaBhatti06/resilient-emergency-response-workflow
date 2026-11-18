# Resilient Emergency Response Workflow (Sentinel)

A web console that turns scattered, manual emergency-response work into one dependable, resumable procedure. Staff launch a **playbook** for an incident (flood, fire, severe weather), and the app runs each step through a workflow engine. Consequential actions such as evacuation orders and mass SMS **wait for human approval**. If a tool fails mid-run, the workflow **pauses and can be recovered** instead of starting over.

> **Note:** All external integrations (SMS gateway, pager, shelters, utilities, etc.) are **simulated**. The app demonstrates the workflow engine end to end without contacting real systems.

---

## Features

- **Incident management:** create incidents with location, zone, severity and population at risk.
- **Response playbooks:** ready-made step sequences for Flash Flood, Wildfire / Structure Fire and Severe Weather.
- **Human approval gates:** critical, high and medium impact steps need explicit approval before they run.
- **Failure recovery:** each step can be approved, rejected, retried or skipped. A failed tool pauses the run rather than breaking it.
- **Evidence and audit trail:** every action produces a receipt, and every change is written to an audit log with the actor.
- **Impact metrics:** compares a documented manual baseline against automated runs (time saved, errors prevented).
- **Built for poor connectivity:**
  - Service worker caches the app shell for offline use.
  - Offline action queue stores actions in the browser and replays them when the connection returns.
  - Connectivity bar and Lite mode toggle for low-bandwidth links.

## Simulated tool integrations

| Tool | Purpose |
|------|---------|
| `weather_feed` | Live river and weather data |
| `responder_pager` | Page fire, EMS and other response teams |
| `shelter_activate` | Open evacuation shelters |
| `evac_order` | Publish a mandatory evacuation order |
| `sms_broadcast` | Mass SMS to residents |
| `utility_notify` | Notify gas and electric utilities |
| `status_page` | Update the public status page |
| `social_media` | Post a public advisory |

A tool marked unavailable in the `tool_status` table fails on purpose, which lets you test the recovery path.

## Tech stack

- [Next.js](https://nextjs.org/) 16 (App Router) and React 19
- TypeScript
- Tailwind CSS 4
- PostgreSQL with [Drizzle ORM](https://orm.drizzle.team/) and `pg`

## Getting started

### Prerequisites

- [Node.js](https://nodejs.org/) 20.9 or newer
- [PostgreSQL](https://www.postgresql.org/download/) running locally
- Git

### 1. Clone and install

```bash
git clone https://github.com/HuzaifaBhatti06/resilient-emergency-response-workflow.git
cd resilient-emergency-response-workflow
npm install
```

### 2. Create the database

Create an empty PostgreSQL database named `app_db`:

```bash
psql -U postgres -c "CREATE DATABASE app_db;"
```

### 3. Configure the environment

Create a file named `.env.local` in the project root:

```
DATABASE_URL=postgresql://postgres:postgres@127.0.0.1:5432/app_db
```

Change the username, password or port if your PostgreSQL setup is different. If you do, also update the `url` in `drizzle.config.json`.

### 4. Create the tables

```bash
npx drizzle-kit push
```

### 5. Load sample data (optional but recommended)

```bash
node scripts/seed.mjs
```

This adds the manual baseline and a few sample runs, including one paused for recovery, so the dashboard and metrics page show data immediately.

### 6. Run the app

```bash
npm run dev
```

Open [http://localhost:3000](http://localhost:3000).

## Available scripts

| Command | What it does |
|---------|--------------|
| `npm run dev` | Start the development server |
| `npm run build` | Build for production |
| `npm start` | Run the production build |
| `npm run lint` | Check code with ESLint |
| `npm run typecheck` | Check TypeScript types |

## Project structure

```
├── public/sw.js            # Service worker (offline app shell)
├── scripts/seed.mjs        # Sample data seeder
└── src/
    ├── app/
    │   ├── api/            # REST routes (incidents, workflows, tools, steps)
    │   ├── incidents/      # Incident list, create form, detail page
    │   ├── approvals/      # Pending approvals queue
    │   ├── evidence/       # Action receipts and audit evidence
    │   ├── tools/          # Tool status page
    │   └── metrics/        # Manual vs automated impact report
    ├── components/         # UI components (Nav, StepRow, ConnectivityBar, ...)
    ├── db/                 # Drizzle schema and connection
    └── lib/
        ├── workflow.ts     # Workflow engine
        ├── playbooks.ts    # Response playbook definitions
        ├── tools.ts        # Simulated tool integrations
        ├── offline.ts      # Offline action queue
        └── queries.ts      # Database queries
```

## How a workflow runs

1. A responder creates an incident and launches a playbook.
2. Steps run in order. Safe steps run automatically.
3. Consequential steps pause at **awaiting approval** until a person approves or rejects them.
4. If a tool fails, the run pauses, and the step can be **retried** or **skipped**.
5. Every action is logged with a receipt and the actor who did it.

## Author

Huzaifa Bhatti ([@HuzaifaBhatti06](https://github.com/HuzaifaBhatti06))
