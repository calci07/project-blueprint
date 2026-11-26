# Role & Objective
Act as a Principal Software Architect designing a **production-grade, highly scalable, maintainable system**.

Your goal is to evaluate my project constraints and quality attributes against industry-standard architectures, recommend the optimal system design, and generate a complete, copy-paste-ready **`ARCHITECTURE.md`** specification—evaluating **Repository Strategy**, **Frontend**, **Backend**, **API & Contracts**, **State/Data Ownership**, and **Runtime/Container Topology** independently.

### Core Directives
1. **Clarify Before Designing:** If any required field in Section 1 is blank or contradictory, ask brief clarifying questions first before generating `ARCHITECTURE.md`. Never guess critical constraints (such as online vs. offline/local-first, deployment model, or primary stack).
2. **Production-First (No MVP Shortcuts):** Do NOT recommend fragile, flat structures that collapse after 6 months of feature growth. Optimize strictly for long-term scalability, strict dependency boundaries, automated testability, failure resilience, and multi-developer concurrency (minimizing Git merge conflicts).
3. **File Destination & Safe Fence Formatting (`ARCHITECTURE.md`):** Format the main deliverable so it can be saved directly as `docs/ARCHITECTURE.md` (or `ARCHITECTURE.md`). Wrap the entire `ARCHITECTURE.md` deliverable inside a single **4-backtick** Markdown block and use standard **3-backtick** blocks for internal ASCII trees, Mermaid diagrams, and config snippets so copying never breaks.
4. **No Lazy Tree Copying:** The ASCII trees in Section 2 are compact reference blueprints for evaluation only. In your final output, generate a **deep, 50+ line production directory tree** tailored specifically to my domain entities, tech stack, containerization/infra files, database migrations, and test suites.
5. **Reconciliation with `design.md` & `AGENTS.md`:** Assume visual styling, color tokens, typography, motion, and UI/UX rules live in `design.md` (or `docs/design.md`), and agent workflow rules live in `AGENTS.md` and `.cursor/rules/`. Keep `ARCHITECTURE.md` strictly focused on system structure, runtime topology, module boundaries, state/data flow, schema isolation, testing topology, and import governance. If `design.md` already exists in the workspace, read it first and unify shared paths (e.g., `src/sections/` for marketing vs. `src/features/` for app workflows, and `src/shared/tokens/` + `src/shared/ui/`) so the two documents never contradict each other.
6. **Modern Bundler, Compiler & Boundary Awareness:** Account for modern toolchain constraints. Do not blindly mandate `index.ts` barrel files everywhere if the stack uses Next.js App Router or Vite where heavy barrels degrade tree-shaking, slow dev cold-starts, or cross React `"use client"` / `"use server"` boundaries; use explicit module entry points, `package.json` `exports`, or static boundary linters appropriate to the stack.
7. **Challenge Assumptions & Propose Better Options:** If my constraints point to a better architectural pattern or hybrid not listed in Section 2, proactively recommend it and justify the trade-offs.

---

## 1. My Project Context (Fill in before sending)

### Product & Team
- **Project & Domain Summary:** [e.g., Local-first personal finance tracker / Multi-tenant B2B FinTech invoicing platform / AI SaaS]
- **Expected Scale (1–3 Years):** [e.g., Single-user local app growing to cloud sync / 10+ core product domains, 100k+ users, Web + Mobile + Public API]
- **Current & Future Team Size:** [e.g., Solo dev now, scaling to 3–5 devs / Starting with 2–3 devs, scaling to 15+ devs across squads]

### Tech Stack
- **Frontend Tech Stack:** [e.g., Next.js App Router, React + Vite, Vue/Nuxt, Astro, React Native/Expo, Tauri/Electron]
- **Backend Tech Stack:** [e.g., Node.js/NestJS/Fastify, Go, Python/FastAPI, Java/Spring Boot, .NET, Rust]
- **API & Communication Style:** [e.g., REST/OpenAPI, tRPC, GraphQL, gRPC, WebSockets/SSE, Local IPC]
- **Database, Caching & Messaging:** [e.g., Local PostgreSQL/SQLite + Drizzle/Prisma, Redis, Kafka/BullMQ, S3/MinIO, Stripe, OpenAI]

### Runtime, Infra & Quality Attributes (NFRs)
- **Runtime & Connectivity Mode:** [e.g., Cloud-only SaaS / Offline-first running locally with containerized DB / Local-first with optional cloud sync]
- **Deployment & Containerization Target:** [e.g., Local Docker Compose (app + DB volume) / Docker + Kubernetes / AWS ECS / Vercel + Railway / Serverless]
- **Tenancy, Security & Compliance:** [e.g., Single-user local / Multi-tenant org isolation (RLS or tenant_id) / Strict PII/HIPAA/SOC2 audit trail]
- **Primary Scalability / Maintenance Bottleneck:** [e.g., Complex evolving business rules / Offline sync & local DB migrations / Heavy 3rd-party integrations / Massive shared UI state / High team velocity without breaking adjacent features]

---

## 2. Production Reference Patterns & Blueprints to Evaluate

### A. Frontend & Client Architecture Patterns

#### 1. Modular / Feature-Based Architecture (Scalable SPA Standard)
Groups code by business feature rather than technical file type, keeping components, hooks, state, and API calls isolated per domain behind controlled public entry points.
```text
src/
├── app/                        # Global providers, router, root store setup
├── features/                   # Self-contained product domains
│   ├── billing/
│   │   ├── api/                # Feature-specific queries & mutations
│   │   ├── components/         # Private UI components for billing
│   │   ├── hooks/              # Feature state & domain logic hooks
│   │   ├── types/
│   │   └── public.ts           # Controlled public entry point (or explicit exports)
│   └── analytics/
└── shared/                     # Strictly domain-agnostic code
    ├── tokens/                 # Design tokens (governed by design.md)
    ├── ui/                     # Reusable design system primitives
    ├── lib/                    # Configured wrappers (fetch/ky, queryClient)
    └── utils/
```
- **Best when:** Medium-to-large SPAs where developers work on separate features concurrently.
- **Trade-offs:** High cohesion and easy feature deletion; requires automated import boundary rules to prevent cross-feature spaghetti.

#### 2. Feature-Sliced Design - FSD (Enterprise Unidirectional Scale)
Enforces strict top-down imports (`app` -> `pages` -> `widgets` -> `features` -> `entities` -> `shared`).
```text
src/
├── app/                        # Routing, global providers, initialization
├── pages/                      # Full route views composing widgets
├── widgets/                    # Autonomous UI blocks combining features + entities
├── features/                   # User actions delivering business value
├── entities/                   # Core business domain data models & passive UI
└── shared/                     # Tokens, UI kit (design.md), API client, helpers
```
- **Best when:** Long-lived enterprise frontends where multiple squads reuse core domain entities across many screens.
- **Trade-offs:** Eliminates circular dependencies mathematically; steeper onboarding curve when classifying slices.

#### 3. Route-Colocated + Feature Hybrid (Production Meta-Framework Standard)
Keeps the file-system router (`app/`) as a thin routing, layout, and Server Component shell while delegating domain logic, client components, server actions, and validation schemas to `features/` (and marketing sections to `sections/` if paired with `design.md`).
```text
src/
├── app/                        # Thin URL routing, layouts & RSC shell ONLY
│   ├── (marketing)/            # Composes src/sections/*
│   └── (dashboard)/
│       └── invoices/
│           ├── page.tsx        # Thin server page composing src/features/invoices
│           └── loading.tsx
├── sections/                   # Marketing/editorial sections (if applicable, per design.md)
├── features/                   # Heavy app domain logic, actions, queries, schemas
└── shared/                     # Tokens, UI primitives (design.md) & utilities
```
- **Best when:** Next.js App Router, Remix/React Router v7, Nuxt, or SvelteKit.
- **Trade-offs:** Clean 1:1 URL mapping and safe Server/Client boundaries; requires discipline to keep `page.tsx` thin.

#### 4. Local-First / Sync Engine Architecture (Reactive Client Cache & Offline Scale)
Pairs a local embedded store/sync engine (SQLite/IndexedDB via ElectricSQL, Zero, PowerSync, RxDB, or Replicache) with optimistic mutation queues and modular UI features.
```text
src/
├── sync/                       # Local DB schema, sync engine, outbox & conflict resolvers
│   ├── schema.ts
│   ├── mutators/
│   └── subscriptions/
├── features/                   # Domain modules reading/writing local reactive store
└── shared/                     # UI primitives (design.md) & offline/sync status indicators
```
- **Best when:** Collaborative apps, POS systems, or offline-capable apps requiring sub-16ms interactions and intermittent connectivity resilience.
- **Trade-offs:** Zero-latency UX and native offline support; adds schema migration and conflict-resolution complexity.

#### 5. Islands / Partial Hydration Architecture (Content & Conversion Speed)
Ships zero-JS static HTML by default and hydrates isolated interactive islands (`features/`) only when visible or interacted with.
- **Best when:** Content-heavy, editorial, marketing, or e-commerce storefronts prioritizing Core Web Vitals.
- **Trade-offs:** Maximum load speed; not ideal for deeply interconnected, state-heavy workspace apps.

#### 6. Atomic Design (Design System UI Layer — Composable Add-on)
Organizes `shared/ui/` or `packages/ui/` into `atoms/`, `molecules/`, `organisms/`, and `templates/` governed by `design.md`. Always paired with a feature architecture above.

#### 7. Desktop / Mobile Shell Wrapper or Micro-Frontends (Specialized)
- **Desktop/Mobile Shell (Tauri / Electron / Capacitor / Expo):** Separates native OS bridge/IPC (`src-tauri/` or `electron/`) from the modular web/UI app.
- **Micro-Frontends (Module Federation / Workspace Verticals):** Only justified at 25+ frontend engineers needing independent deploy pipelines; avoid for small/medium teams due to operational and bundle overhead.

---

### B. Backend Architecture Patterns

#### 1. Modular Monolith with Vertical Slices (Production Default)
Partitions the backend into self-contained domain modules (`modules/`). Each module owns its vertical slices (routes/handlers, use cases, domain models, module-scoped schema/repositories) and exposes a single public facade or domain event contract.
```text
src/
├── modules/
│   ├── identity/
│   │   ├── api/                # Controllers, DTOs, Route handlers
│   │   ├── application/        # Vertical-slice use cases / command & query handlers
│   │   ├── domain/             # Module business rules & invariants
│   │   ├── data/               # Repositories & module-scoped schema/migrations
│   │   └── identity.facade.ts  # ONLY synchronous entry point for other modules
│   └── billing/
└── shared/
    ├── infrastructure/         # DB pool, cache, event bus, telemetry, graceful shutdown
    └── kernel/                 # Base errors, auth guards, middleware, primitives
```
- **Best when:** Default choice for scalable production backends (both cloud and Dockerized local backends) needing microservice-grade isolation without distributed-systems overhead on Day 1.
- **Trade-offs:** Easy future extraction to microservices; prohibits cross-module SQL joins (use facades or domain events).

#### 2. Hexagonal Architecture / Ports & Adapters (Core & Integration Isolation)
Inverts dependencies so `core/` has zero framework, ORM, or vendor SDK imports. External I/O plugs in via `adapters/inbound/` (HTTP, webhooks, queues, CLI) and `adapters/outbound/` (DB, payment gateways, LLMs, filesystem).
```text
src/
├── core/
│   ├── domain/                 # Pure entities, value objects & invariant rules
│   ├── use-cases/              # Command/query orchestrators
│   └── ports/
│       ├── inbound/            # Driving interfaces
│       └── outbound/           # Driven interfaces (RepoPort, GatewayPort)
└── adapters/
    ├── inbound/                # HTTP controllers, webhooks, workers, CLI
    └── outbound/               # Postgres/SQLite repos, external SDKs, local FS
```
- **Best when:** Heavy 3rd-party integrations, multiple entry triggers, swappable storage engines (e.g., SQLite local <-> Postgres cloud), or strict unit-testability.
- **Trade-offs:** 100% vendor- and framework-agnostic core; higher mapping boilerplate (best applied selectively to complex modules inside a Modular Monolith).

#### 3. Domain-Driven Design - DDD + Clean/Onion (High Business Complexity)
Combines Bounded Contexts with tactical DDD (Aggregates, Value Objects, Domain Events, Repository Interfaces) inside concentric layers.
- **Best when:** FinTech, logistics, insurance, scheduling, or healthcare domains with intricate invariants.
- **Trade-offs:** Mirrors Ubiquitous Language cleanly; overkill for CRUD modules (use selectively inside complex modules).

#### 4. Event-Driven / CQRS + Transactional Outbox (Asymmetric Scale & Audit)
Separates Write Commands (aggregate validation + transactional outbox) from Read Queries (denormalized projections/views) and async event consumers.
- **Best when:** Heavy read/write asymmetry, strict audit trails, or multi-step async workflows.
- **Trade-offs:** Scales reads independently and decouples workflows; introduces eventual consistency and idempotency requirements.

#### 5. Local / Embedded Containerized Backend & BFF (Offline / Local-First Pattern)
Runs the Modular Monolith + local database (PostgreSQL container with named volume, or embedded SQLite in WAL mode) on localhost with automated boot migrations, healthchecks, backup/export hooks, and optional background cloud-sync workers.
- **Best when:** Local-first or offline apps packaged via Docker Compose or desktop shells.

#### 6. Layered / N-Tier, Serverless (FaaS), or Microservices (Situational)
- **Layered / N-Tier:** Acceptable only inside a tiny single-purpose worker or simple CRUD module—never as the top-level structure for a growing multi-domain app.
- **Serverless / Edge Functions:** Best for spiky, stateless webhook/API workloads; beware cold starts and connection pool exhaustion.
- **Microservices:** Justified only when independent scaling, fault isolation, or multi-squad org boundaries outweigh distributed tracing, network latency, and deployment complexity.

---

### C. Repository & API Contract Strategy

#### 1. Monorepo / Workspace Architecture (pnpm / Turborepo / Nx / Bazel)
Consolidates apps (`apps/`), shared libraries (`packages/`), infrastructure (`infra/` or `docker/`), and docs (`docs/`) in one repository with cached task graphs.
```text
root/
├── docs/
│   ├── ARCHITECTURE.md         # System structure, runtime topology & governance
│   ├── design.md               # UI/UX tokens, typography & component specs
│   └── adr/                    # Architecture Decision Records (0001-*.md)
├── docker/                     # Dockerfiles, compose.yaml, DB init/backup scripts
├── apps/
│   ├── web/                    # Primary Frontend
│   ├── api/                    # Primary Backend
│   └── worker/                 # Optional async/sync worker
└── packages/
    ├── ui/                     # Shared Design System (implements design.md)
    ├── contracts/              # Shared Zod/OpenAPI/tRPC/Protobuf schemas & DTOs
    ├── database/               # DB client, schema registry & migration tooling
    └── config/                 # Shared lint, typecheck & formatter configs
```
- **Best when:** Shared language/contracts across apps (especially full-stack TS or single-repo Dockerized stacks) needing atomic cross-stack PRs.

#### 2. Polyrepo (Isolated Service Repositories)
Separate repositories communicating via versioned, automated contract packages (OpenAPI SDKs, Protobuf registries).
- **Best when:** Disparate tech stacks/toolchains, strict per-repo access control, or large independent teams.

#### 3. API Contract Discipline (Required in Both Strategies)
Explicitly choose and enforce a single source of truth for Frontend <-> Backend contracts (e.g., **OpenAPI/Swagger codegen**, **tRPC**, **GraphQL schema-first**, or **Protobuf/gRPC**) so runtime payloads and types never drift.

---

## 3. Required Output: Generate My `ARCHITECTURE.md` File

Analyze my **Project Context** and output a complete, production-ready **`ARCHITECTURE.md`** file wrapped in a single **4-backtick** Markdown block.

Include these exact **10 sections** inside `ARCHITECTURE.md`:

1. **Header & Documentation Map:**
   - Define how `docs/ARCHITECTURE.md` (structure, runtime topology, state/data ownership, dependency governance) coordinates with `docs/design.md` (visual system & UX) and `docs/adr/` (historical decisions).
2. **Executive Architecture Verdict & Initial ADRs:**
   - State the chosen **Repository Strategy**, **Frontend Pattern**, **Backend Pattern**, **API Contract Strategy**, and **Runtime/Deployment Model** (including pragmatic hybrids).
   - Explain *why* this combination fits my exact quality attributes, scale horizon, and team size.
   - Include a concise **Decision Table / ADR Summary** explicitly stating which runner-up patterns were rejected and why.
3. **System & Runtime Topology (C4 Container Diagram):**
   - Provide a clear **ASCII or Mermaid C4 Container diagram** showing runtime components (Client/Browser, Reverse Proxy/Edge, API Server, Background Workers, Database, Cache/Storage, Docker networks & persistent volumes, and external services) plus request/data flow directions.
4. **Production ASCII Directory Tree:**
   - Provide a deep, realistic **50+ line ASCII folder tree** tailored to my exact tech stack, domain entities, and infra/Docker setup (zero generic `foo/bar` placeholders).
   - Include concrete production files (`docs/ARCHITECTURE.md`, `docs/design.md`, `docs/adr/`, `Dockerfile`, `compose.yaml`, routes, controllers/actions, ports/adapters, per-module schemas, `env` validation, seed/migration scripts, and tests) with inline `# comments`.
5. **State, Data, Contract & Schema Ownership Rules:**
   - **Frontend State Taxonomy:** Strict ownership rules for *Server Cache State*, *Global Client UI State*, *URL/Router State*, *Form State*, and (if applicable) *Local/Offline Sync State*.
   - **API Contract Boundary:** Single source of truth for request/response validation, versioning, and client type generation.
   - **Backend Data & Schema Isolation:** How database schemas and migrations are partitioned per module, zero-downtime / expand-and-contract migration rules, ban on cross-module SQL joins, and synchronous facade vs. transactional outbox event rules.
6. **Strict Dependency & Import Governance (+ Stack-Native Executable Configs):**
   - Define 5–6 non-negotiable architectural import laws (unidirectional layer flow, public module entry points, framework-agnostic `domain/`/`core/`).
   - Provide **copy-pasteable, stack-native enforcement configs** that break CI on violation:
     - *TypeScript/JS:* `tsconfig.json` `paths` (or `package.json` `exports`) + `eslint-plugin-boundaries`, `@nx/enforce-module-boundaries`, or `dependency-cruiser`.
     - *Go:* `internal/` package layout + `golangci-lint` (`depguard`) rules.
     - *Python:* `import-linter` contract config (`pyproject.toml`).
     - *Java / .NET:* `ArchUnit` / `NetArchTest` rule definitions.
7. **Containerization, Local Dev & Data Persistence Strategy:**
   - Define the multi-stage `Dockerfile` strategy, `compose.yaml` service topology (dev vs. prod/local-release), named volume persistence for databases, startup healthchecks (`depends_on: condition: service_healthy`), automatic vs. manual migration execution, backup/restore path, and `.env.example` / secret hygiene.
8. **Cross-Cutting Concerns, Resilience & Testing Topology:**
   - Define exact architectural placement and patterns for:
     - **Auth, RBAC & Tenant/Data Isolation**
     - **Error Handling** (Domain Errors vs. Transport/HTTP Errors, standardized error envelopes like RFC 9457)
     - **Resilience & Resource Hygiene** (bounded DB/HTTP connection pools, timeouts, idempotency keys, graceful `SIGTERM` shutdown, memory-leak/race-condition safeguards)
     - **Observability** (structured JSON logging, correlation/request IDs, health/readiness endpoints `/healthz` & `/readyz`, OpenTelemetry hooks)
     - **Type-Safe Environment Validation** (`env` schema failing fast at startup)
     - **Testing Topology** (Testing Trophy/Pyramid split: colocated unit tests, containerized integration/contract tests, E2E smoke tests)
9. **"Where Does New Code Go?" Developer Cheat Sheet:**
   - Provide a Markdown table mapping **10 common tasks** (*Adding a UI primitive*, *Adding a feature workflow/form*, *Adding an API endpoint/server action*, *Adding a DB table + migration*, *Adding a 3rd-party SDK/adapter*, *Adding a background job/webhook*, *Requesting data from another domain module*, *Adding unit/integration/E2E tests*, *Adding an env variable/secret*, *Adding a Docker service or infra config*) to the exact folder path and governing boundary rule.
10. **Scale Horizon & Evolution Triggers:**
    - Define concrete quantitative and qualitative thresholds indicating a module or layer has outgrown its current structure, plus the low-risk refactoring playbook (e.g., promoting a CRUD module to Hexagonal/DDD, migrating local SQLite/Docker DB to cloud-synced Postgres, or extracting a hot module into an independent worker/service).

---

## 4. Bonus Deliverable: AI Coding Agent Governance Block
**Outside** the 4-backtick `ARCHITECTURE.md` block at the very end of your response, provide:
1. A compact **20-line rule block** ready to append to **`AGENTS.md`** (linking `docs/ARCHITECTURE.md` and `docs/design.md` and hard-coding folder placement, import boundaries, schema isolation, and resource-hygiene checks).
2. A short always-on Cursor rule snippet for **`.cursor/rules/read-architecture.mdc`** (matching modern `.cursor/rules/*.mdc` format rather than legacy `.cursorrules`).
