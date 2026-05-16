# PromptIQ

## Overview

PromptIQ is an AI-powered prompt optimization tool. Users submit prompts, select a complexity level (basic, medium, advanced), and the app uses OpenAI's GPT-4o to analyze, improve, and rewrite their prompts. The application stores prompt history so users can revisit past optimizations. It's built as a full-stack TypeScript application with a React frontend and Express backend, using PostgreSQL for persistence and local username/password authentication.

## User Preferences

Preferred communication style: Simple, everyday language.

## System Architecture

### Monorepo Structure
The project uses a three-folder monorepo pattern:
- `client/` — React SPA (Vite + TypeScript)
- `server/` — Express API server (TypeScript, run via tsx)
- `shared/` — Shared types, schemas, and route definitions used by both client and server

### Frontend
- **Framework**: React 18 with TypeScript
- **Bundler**: Vite with HMR in development (no Replit-specific plugins)
- **Routing**: Wouter (lightweight client-side router)
- **State Management**: TanStack React Query for server state
- **UI Components**: shadcn/ui (New York style) built on Radix UI primitives
- **Styling**: Tailwind CSS with CSS variables for theming (light/dark mode support)
- **Animations**: Framer Motion for page transitions and UI animations
- **Fonts**: Inter (body) and Outfit (display), loaded via Google Fonts
- **Path aliases**: `@/` maps to `client/src/`, `@shared/` maps to `shared/`

### Backend
- **Framework**: Express.js running on Node with TypeScript (via tsx)
- **API Pattern**: REST endpoints defined in `shared/routes.ts` with Zod schemas for input validation and response typing. This shared route contract keeps client and server in sync.
- **Build**: Custom build script (`script/build.ts`) uses Vite for client and esbuild for server, outputting to `dist/`
- **Dev vs Prod**: In development, Vite middleware serves the frontend with HMR. In production, the built static files are served from `dist/public/`.

### Database
- **Database**: PostgreSQL (required, via `DATABASE_URL` environment variable)
- **ORM**: Drizzle ORM with `drizzle-zod` for schema-to-validation integration
- **Schema location**: `shared/schema.ts` and `shared/models/` directory
- **Migrations**: Drizzle Kit with `db:push` command for schema synchronization
- **Key tables**:
  - `prompts` — stores user prompts, optimized versions, AI responses, complexity level, and favorites
  - `users` — local auth user records (id, username, password hash, email, firstName, lastName)
  - `sessions` — Express session storage in PostgreSQL (mandatory, do not drop)

### Authentication
- **Provider**: Local passport-local strategy (username + bcrypt password)
- **Implementation**: `server/localAuth.ts`
- **Session storage**: PostgreSQL-backed sessions using `connect-pg-simple`
- **Endpoints**:
  - `POST /api/register` — Register a new account
  - `POST /api/login` — Log in with username/password
  - `POST /api/logout` or `GET /api/logout` — Log out
  - `GET /api/auth/user` — Get current user
- **Password hashing**: bcryptjs with salt rounds of 12

### AI Integration
- **Provider**: OpenAI API
- **Model**: GPT-4o for prompt analysis and optimization
- **Configuration**: Uses `OPENAI_API_KEY` (local) or `AI_INTEGRATIONS_OPENAI_API_KEY` (Replit) environment variables

### Key API Endpoints
- `POST /api/analyze` — Submit a prompt for AI optimization (authenticated)
- `GET /api/prompts` — List user's prompt history (authenticated)
- `GET /api/prompts/:id` — Get a specific prompt (authenticated)
- `DELETE /api/prompts/:id` — Delete a prompt (authenticated)
- `GET /api/auth/user` — Get current authenticated user
- `POST /api/login`, `POST /api/register`, `POST /api/logout` — Auth endpoints

## Running Locally (Windows)

See `SETUP.md` for full instructions. Key steps:
1. Copy `.env.example` to `.env` and fill in values
2. In `package.json` scripts, replace `NODE_ENV=` with `cross-env NODE_ENV=` (cross-env is already installed)
3. Run `npm run db:push` to sync the schema
4. Run `npm run dev` or `npm start`

## External Dependencies

- **PostgreSQL** — Primary database (must be provisioned, connection via `DATABASE_URL`)
- **OpenAI API** — AI model access (`OPENAI_API_KEY` or `AI_INTEGRATIONS_OPENAI_API_KEY`)
- **Session Secret** — Required for Express sessions (`SESSION_SECRET`)
- **dotenv** — Loads `.env` file automatically (already imported in `server/index.ts` and `server/db.ts`)
- **cross-env** — Windows-compatible environment variable setting (installed, use in scripts)
- **bcryptjs** — Password hashing for local auth
