# Job AutoApply — Phases 0, 1, 2, and 3

A job application platform built with Next.js and Supabase for data and authentication.
Designed for job seekers who want to organize opportunities and prepare tailored applications.
Stack: Next.js, Supabase, and hybrid AI with Ollama, Claude, and Gemini.

[Leer este documento en español](README.es.md).

## What it includes

- **Next.js 16** (App Router, Turbopack), TypeScript, and Tailwind CSS.
- **Authentication and sessions:** Registration, sign-in, and callback flows with Supabase Auth, plus middleware protecting private routes (`/dashboard`, `/dashboard/jobs`, `/dashboard/applications`, `/dashboard/resumes`, `/dashboard/profile`).
- **Candidate profile (`/dashboard/profile`):** Persistent candidate details (name, phone, LinkedIn, GitHub, portfolio, and location) for form autofill.
- **Resume management:** Upload `.txt` and `.md` files, preview and edit them, and set a primary resume (`es_actual`) with RLS policies.
- **Job aggregator (`/dashboard/jobs`):** Idempotent ingestion from public Remotive and Arbeitnow feeds without API keys; ATS detection for Greenhouse, Lever, Ashby, Workday, and SmartRecruiters; keyword search and filters by work arrangement or ATS; manual job entry; and an action to preload a selected listing into the AI generator.
- **Application tracking (`/dashboard/applications`):** Save tailored applications with generated resumes and letters, track their lifecycle from draft through offer, and inspect or copy generated documents in a modal.
- **ATS-friendly PDF resume export (`components/CvPdfModal.tsx`):** Three typography styles, formatted bullets and semantic sections, and native Letter-size PDF export in a single-column, table-free layout.
- **ATS match score and keyword analyzer (`lib/ats/matcher.ts` and `components/AtsMatchCard.tsx`):** Deterministic lexical and semantic matching between a job listing and tailored resume; an interactive 0–100 score with levels and skill-category breakdowns; matching and missing keyword tabs with one-click copying; and optimization suggestions.
- **ATS autofill assistant (`lib/worker/ats/filler.ts` and `/api/worker/autofill`):** Deterministic field mapping for Greenhouse, Lever, Ashby, and generic forms; a JavaScript injector that triggers DOM `input` and `change` events and highlights completed fields; and a Playwright runner (`worker/autoapply-playwright.mjs`) for automated headless or headed execution.
- **Chrome Manifest V3 extension (`extension/`):** Loadable in Chrome or Edge, detects supported ATS forms, autofills with native React/HTML5 events, highlights filled fields and shows an on-page confirmation. Stores data locally in `chrome.storage.local` and syncs directly from the web app through `/api/profile`. See the [extension installation and usage guide](extension/README.md).
- **n8n webhook and automation bridge (`app/api/webhooks/n8n/route.ts` and `n8n/`):** Webhook-token-protected endpoint supporting `sync_jobs`, `get_pending`, `update_status`, and `telegram_digest`. The exportable `n8n/job_autoapply_pipeline.json` workflow can be imported into n8n and includes scheduled job ingestion, a morning digest, and Telegram alerts. See the [n8n import guide](n8n/README.md).
- **Reactive navbar:** Session status, user email, available credits in real time, and navigation.
- **Hybrid AI architecture:** Local Ollama with `qwen2.5:7b`; Claude (`claude-sonnet-5-5`) as the primary cloud provider; and Google Gemini (`gemini-2.5-flash`) as a fallback.
- `supabase/schema.sql` and migrations in `supabase/migrations/` define `subscriptions`, `profiles`, `resumes`, `job_postings`, `applications`, and `ai_generations`, with RLS, non-negative balance constraints, multi-provider audit support, and performance indexes.
- `app/api/generate/route.ts` provides resilient generation with atomic credit reservation and automatic refunds on failure.
- `app/dashboard/page.tsx` provides a dashboard with catalog job preloading, automatic primary resume selection, AI engine selection, loading feedback, and direct saving to *My Applications*.

## Getting started

1. Create a project at [supabase.com](https://supabase.com) and run `supabase/schema.sql` in the SQL Editor. If you already have tables from earlier phases, apply the delta migrations in `supabase/migrations/`.
2. To use local Ollama, make sure it is running and download the model:
   ```bash
   ollama pull qwen2.5:7b
   ```
3. Copy `.env.example` to `.env.local` and fill in the Supabase variables. API keys can be left blank when using only local Ollama.
4. Install dependencies and start the development server:
   ```bash
   npm install
   npm run dev
   ```
5. Open `http://localhost:3000`. To try local generation, add a test row to `resumes` in Supabase with a valid `user_id` and resume text. Enter its `id` and a job listing on `/dashboard`, choose an AI engine, and select **Generate**.

## Deployment

The project is ready to deploy on Vercel: connect the repository, add the Supabase and cloud provider (Anthropic / Gemini) environment variables in the project settings, and deploy.

## License: MIT
