# MachineLearningHub

A full-stack ML education platform inspired by GeeksforGeeks, focused exclusively on Machine Learning, AI, and Data Science. Features tutorials, MCQ quizzes, interview Q&A, ML project guides, career roadmaps, and MLGuru AI tutor powered by OpenAI.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000/8080)
- `pnpm --filter @workspace/ml-hub run dev` — run the frontend (Vite, port auto-assigned)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- `pnpm --filter @workspace/scripts run seed` — seed the database with ML content
- Required env: `DATABASE_URL` — Postgres connection string
- Optional env: `OPENAI_API_KEY` — enables MLGuru AI chat (gracefully disabled if missing)

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite, Wouter routing, TailwindCSS, shadcn/ui, react-markdown, react-syntax-highlighter
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- Validation: Zod (`zod/v4`), `drizzle-zod`
- AI: OpenAI gpt-4o-mini with SSE streaming
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/db/src/schema/` — Drizzle ORM schema: topics, tutorials, quizzes, quiz_questions, interview_questions, projects, roadmaps, conversations, messages
- `lib/api-spec/openapi.yaml` — OpenAPI contract (source of truth for API)
- `lib/api-client-react/` — Generated React Query hooks (from Orval codegen)
- `lib/api-zod/` — Generated Zod schemas (from Orval codegen)
- `artifacts/api-server/src/routes/` — Express route handlers (topics, tutorials, quizzes, interview, projects, roadmaps, stats, chat)
- `artifacts/ml-hub/src/pages/` — All frontend pages
- `artifacts/ml-hub/src/components/` — Layout, MarkdownRenderer, UI components
- `scripts/src/seed.ts` — Database seeding script

## Architecture decisions

- **Contract-first API:** OpenAPI spec in `lib/api-spec/openapi.yaml` drives codegen for both React hooks and Zod schemas. Never write these by hand.
- **MLGuru AI:** Uses OpenAI gpt-4o-mini with SSE streaming. Conversation history is persisted to DB per conversation. Returns 503 gracefully if `OPENAI_API_KEY` is missing.
- **Markdown-first content:** Tutorial/project content is stored as markdown in the DB and rendered client-side with `react-markdown` + Prism syntax highlighting.
- **Seed script idempotent:** Uses `onConflictDoNothing()` so it can be re-run safely.
- **Python f-strings in TS template literals:** When writing seed content in TypeScript template literals, escape Python f-string `${...}` patterns as `\${...}` to avoid esbuild parse errors.

## Product

MachineLearningHub provides:
- **12 ML/AI topics** covering the full spectrum from fundamentals to MLOps and Generative AI
- **9 in-depth tutorials** with code examples and markdown rendering
- **5 quizzes** with 18 MCQ questions covering ML theory
- **9 interview questions** with detailed answers for job prep
- **4 project guides** (house prices, sentiment analysis, CNN, churn prediction)
- **3 career roadmaps** (Data Scientist, ML Engineer, AI Engineer)
- **MLGuru AI tutor** — GPT-4o-mini powered chat assistant (requires OPENAI_API_KEY)

## User preferences

- Dark navy/teal theme — serious, engineer-focused aesthetic
- Dense, technically accurate content — no fluff

## Gotchas

- Python f-strings like `f"value: ${var:.2f}"` inside TypeScript template literals cause esbuild parse errors. Escape as `\${var:.2f}`.
- The seed script inserts topics with `onConflictDoNothing` — safe to re-run.
- Chat route gracefully returns 503 (not 500) when `OPENAI_API_KEY` is missing, with a helpful message.

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
