# PromptToHire

> An AI-native campus placement workspace where students, recruiters, and administrators collaborate — and where you can literally *ask your data questions in plain English*.

PromptToHire brings together applicant tracking, resume intelligence, and a natural-language analytics layer into a single Next.js application. Type a question like *"show me the top 10 candidates for backend roles with a CGPA above 8"* and the platform turns it into a safe, role-scoped SQL query and renders the answer as a chart or table.

---

## Why PromptToHire

Most placement tools stop at storing applications. PromptToHire adds an intelligence layer on top:

- **Ask, don't query.** A guarded natural-language-to-SQL engine lets non-technical users explore data without writing SQL.
- **Resume signals that matter.** Uploaded resumes are scored against role requirements with actionable, AI-generated feedback.
- **Bring your own model.** Gemini, OpenAI, and Anthropic are all first-class; switch providers with a single environment variable.
- **Safety by design.** Every generated query is validated, sandboxed, and automatically filtered by the caller's role before it ever touches the database.

---

## Who it's for

| Role | What they get |
| --- | --- |
| **Candidates** | Profile builder, job feed, application kanban, ATS resume scoring, peer benchmarking, interview calendar |
| **Recruiters** | Job postings with eligibility rules, applicant pipelines, multi-round interview scheduling, hiring analytics |
| **Administrators** | Account approvals, bulk moderation, platform-wide dashboards, registration verification |

---

## Feature tour

**Talent & applications**
- Rich candidate profiles (skills, projects, experience, certifications)
- Filterable job discovery and one-click applications
- Drag-and-drop kanban to track application stages

**AI toolbox**
- Natural-language questions → validated, role-aware SQL → charts and tables
- Resume ATS analysis with scoring and improvement tips
- Profile gap detection and personalized suggestions
- Pluggable LLM providers (Gemini / OpenAI / Anthropic / custom endpoint)

**Recruiting workflow**
- Job posting lifecycle with eligibility criteria
- Applicant status transitions (applied → OA → interview → offer/reject)
- Interview scheduling with multiple rounds and calendar sync

**Administration**
- Approve, reject, or suspend accounts
- Batch operations across many users at once
- Aggregate analytics for the whole platform

---

## Tech stack

| Layer | Choices |
| --- | --- |
| App framework | Next.js 15 (App Router), React 19, TypeScript |
| UI | Tailwind CSS 4, shadcn/ui (Radix), Recharts, TipTap, Motion |
| Data | PostgreSQL (Neon serverless) via Drizzle ORM + Drizzle Kit |
| Auth | Better Auth (email/password + Google OAuth) |
| Storage & mail | AWS S3 (presigned uploads), Resend + React Email |
| AI | Gemini 2.0 Flash by default, OpenAI / Anthropic / custom optional |
| Quality | Jest, Playwright, ESLint, Artillery (load testing) |

---

## Quick start

```bash
# 1. Install dependencies
npm install

# 2. Create your environment file (see below)
cp .env.example .env   # or create .env manually

# 3. Push the schema to your database
npx drizzle-kit push

# 4. Run it
npm run dev
```

The app boots at [http://localhost:3000](http://localhost:3000). API routes live under `/api/*`.

> Running end-to-end tests for the first time? Install the browsers with `npm run playwright:install`.

---

## Configuration

Create a `.env` file in the project root. At minimum you need a database, a mail sender, storage, and one LLM provider.

```bash
# --- Core ---
DATABASE_URL=postgresql://user:password@host:port/database
NEXT_PUBLIC_BASE_URL=http://localhost:3000

# --- Email (Resend) ---
RESEND_API_KEY=re_xxxxxxxx
EMAIL_FROM=PromptToHire <onboarding@resend.dev>

# --- File storage (AWS S3) ---
AWS_ACCESS_KEY_ID=your_access_key
AWS_SECRET_ACCESS_KEY=your_secret_key
AWS_S3_BUCKET=your_bucket
AWS_REGION=us-east-1
# Optional CDN in front of S3
AWS_CLOUDFRONT_URL=https://your-distribution.cloudfront.net

# --- AI provider (pick one) ---
LLM_PROVIDER=gemini            # gemini | openai | anthropic | custom
GEMINI_API_KEY=your_gemini_key
GEMINI_MODEL=gemini-2.0-flash-exp

# OPENAI_API_KEY=sk-...
# OPENAI_MODEL=gpt-4o-mini
# ANTHROPIC_API_KEY=sk-ant-...
# ANTHROPIC_MODEL=claude-3-5-sonnet-20241022

# --- Optional: social login ---
GOOGLE_CLIENT_ID=your_client_id
GOOGLE_CLIENT_SECRET=your_client_secret

# --- Optional: custom/self-hosted model ---
# CUSTOM_LLM_API_URL=https://your-endpoint/v1
# CUSTOM_LLM_API_KEY=your_key
# CUSTOM_LLM_MODEL=your-model
```

---

## Project layout

```
.
├── app/            # App Router: pages, layouts, and /api route handlers
├── components/     # UI components, forms, charts, and email templates
├── db/             # Drizzle client and schema
├── lib/            # AI clients, analytics, auth, storage, helpers
├── server/         # Server-side business logic (jobs, applications, users…)
├── migrations/     # Drizzle SQL migrations + metadata
├── query-engine/   # NL-to-SQL dataset, notebooks, and model training assets
├── tests/          # unit, integration, e2e, security, performance suites
├── scripts/        # one-off developer/maintenance scripts
├── hooks/          # shared React hooks
├── public/         # static assets
├── docs/           # testing and implementation documentation
├── auth-schema.ts  # Better Auth schema
├── middleware.ts   # auth + role-based routing
└── drizzle.config.ts
```

---

## Scripts

| Command | What it does |
| --- | --- |
| `npm run dev` | Start the dev server (Turbopack) |
| `npm run build` | Production build |
| `npm start` | Serve the production build |
| `npm run lint` | Lint with ESLint |
| `npm test` | Jest unit tests |
| `npm run test:integration` | Integration tests |
| `npm run test:e2e` | Playwright end-to-end tests |
| `npm run test:security` | Security-focused tests |
| `npm run test:performance` | Artillery load test |
| `npm run test:all` | Everything (Jest + Playwright) |

---

## How the natural-language query engine works

1. A user types a question in the AI assistant.
2. The query generator assembles context: the caller's role, the relevant schema, and permission rules.
3. The chosen LLM drafts a SQL statement.
4. A validation layer rejects anything unsafe (writes, multi-statements, disallowed tables, etc.).
5. Role-based filters are injected automatically so users only ever see rows they're allowed to.
6. The query runs with a timeout, results are summarized into insights, and the UI renders a chart or table.

The training data and experiments behind this engine live in [`query-engine/`](./query-engine).

---

## Testing

The suite is split by concern under `tests/` — unit, integration, `e2e` (Playwright), `security`, `performance` (Artillery), and LLM accuracy checks. See [`docs/testing`](./docs/testing) for the strategy and guide.

```bash
npm run test:all
```

---

## License

Released for educational and demonstration purposes. Review and adapt the configuration before deploying to production.
