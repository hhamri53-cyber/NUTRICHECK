# NutriCheck — AI Food Nutrition Analyzer

## Overview

NutriCheck is a dark-themed web app that lets users upload a food photo and receive detailed nutritional analysis powered by Anthropic Claude (claude-opus-4-5). The frontend is a React/Vite SPA with custom CSS; the backend is an Express 5 API server.

## Stack

- **Monorepo tool**: pnpm workspaces
- **Node.js version**: 24
- **Package manager**: pnpm
- **TypeScript version**: 5.9
- **API framework**: Express 5
- **Database**: PostgreSQL + Drizzle ORM (not used by NutriCheck, available for future features)
- **Validation**: Zod (`zod/v4`), `drizzle-zod`
- **API codegen**: Orval (from OpenAPI spec)
- **AI**: Anthropic Claude claude-opus-4-5 via `@anthropic-ai/sdk`
- **File uploads**: Multer (memory storage, 10MB limit)

## Structure

```text
artifacts-monorepo/
├── artifacts/
│   ├── api-server/         # Express API server (analyze endpoint + static fallback)
│   │   ├── src/routes/analyze.ts  # POST /api/analyze — multer + Claude vision
│   │   └── public/index.html      # Vanilla HTML fallback (unused, React SPA takes over)
│   └── nutricheck/         # React + Vite frontend (served at /)
│       └── src/
│           ├── App.tsx     # Single-page NutriCheck UI
│           └── nutricheck.css  # Custom dark theme CSS
├── lib/                    # Shared libraries
│   ├── api-spec/           # OpenAPI spec + Orval codegen config
│   ├── api-client-react/   # Generated React Query hooks
│   ├── api-zod/            # Generated Zod schemas from OpenAPI
│   └── db/                 # Drizzle ORM schema + DB connection
└── scripts/                # Utility scripts
```

## Routing

- `/` → NutriCheck React SPA (Vite dev server, port 21361)
- `/api` → Express API server (port 8080)
  - `POST /api/analyze` — accepts multipart/form-data with an `image` field; returns JSON nutrition data
  - `GET /api/healthz` — health check

## Environment Variables

- `ANTHROPIC_API_KEY` — Required. Your Anthropic API key for Claude vision analysis.
- `PORT` — Auto-assigned per artifact by Replit.
- `DATABASE_URL` — Auto-provided by Replit PostgreSQL integration.

## Key Features

- Drag-and-drop or click-to-browse image upload
- Image preview with remove option
- Claude analyzes the image and returns:
  - Detected food items with confidence level
  - Total calories
  - Macronutrients (protein, carbs, fat, fiber, sugar) with animated progress bars
  - Micronutrients with daily value percentages
  - Health assessment notes
- Dark theme with purple accent gradient

## Development

- `pnpm --filter @workspace/api-server run dev` — Start API server
- `pnpm --filter @workspace/nutricheck run dev` — Start frontend dev server
- `pnpm run typecheck` — Full TypeScript check

## Packages Added

- `multer` + `@types/multer` — multipart file upload handling in api-server
- `@anthropic-ai/sdk` — Anthropic Claude API client in api-server
