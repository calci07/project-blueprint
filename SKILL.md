---
name: project-blueprint
description: >-
  Interactive, multi-pillar project planning, architecture, backend/security, and frontend design
  workflow for both new (greenfield) and existing (audit/upgrade) projects. Use whenever the user
  wants to set up a full project blueprint, design or update docs/design.md (frontend UI/UX,
  conversion, Apple HIG, motion, tokens), design or update docs/ARCHITECTURE.md (system/repo
  architecture, C4 topology, directory structure, import boundaries, Docker/runtime), design or
  audit docs/BACKEND.md (auth, RBAC/ABAC, database integrity, concurrency, caching/queues, OWASP
  security), or configure unified cross-AI agent governance rules (AGENTS.md, CLAUDE.md,
  .agents/rules/, .cursor/rules/).
---

# Project Blueprint Skill

Turn any new or existing codebase into a coordinated, production-grade specification across three
pillars—**Frontend Design + Apple HIG**, **System Architecture**, and **Backend + Security**—backed
by unified governance files compatible with Antigravity, Claude Code, Codex, Cursor, and Copilot.

---

## Reference Specifications (Progressive Disclosure)

Full, verbatim rules for each pillar live in `references/`. **Always read the complete reference
file for a pillar before running that pillar's stage—never rely on memory or drop requirements.**
Load only the reference file(s) needed for the active stage(s):

- **Pillar 1 — Frontend Design & Apple HIG:** [references/design-spec.md](./references/design-spec.md)
- **Pillar 2 — System Architecture:** [references/architecture-spec.md](./references/architecture-spec.md)
- **Pillar 3 — Backend & Security:** [references/backend-spec.md](./references/backend-spec.md)

---

## Canonical Reconciliation & Override Rules

Where the three verbatim reference specs differ on paths, IDE rule files, or chat formatting,
**this table is the single source of truth** so all three pillars work together with zero conflicts:

| Concern | Reconciled Standard (Overrides Verbatim Spec Differences) |
| :--- | :--- |
| **Spec File Locations** | Store all three specs under `docs/`: `docs/design.md`, `docs/ARCHITECTURE.md`, and `docs/BACKEND.md` (plus `docs/adr/` if ADRs are created). If an existing repo already has a root `design.md`, `ARCHITECTURE.md`, or `BACKEND.md`, ask the user whether to move it to `docs/` or preserve the existing path consistently across all files. |
| **Deliverable Output Mode** | After the user approves each stage's options, **write the finalized spec directly to disk** (`docs/design.md`, `docs/ARCHITECTURE.md`, `docs/BACKEND.md`) and reply with a concise summary and clickable file link. Do **not** dump giant 4-backtick copy-paste blocks into chat unless the user explicitly asks for raw Markdown in chat. |
| **Shared Folder Map Alignment** | Marketing/editorial sections (`src/sections/` or `apps/web/src/sections/`) and shared design tokens (`src/shared/tokens/` or `packages/ui/tokens/`) in `docs/design.md` MUST match the production directory tree in `docs/ARCHITECTURE.md` (`src/features/` for app workflows, `src/shared/ui/` for design system primitives). Whenever Stage 2 adapts paths for a framework or monorepo, update `docs/design.md`'s folder map in the same turn. |
| **Shared Runtime Quality Gates** | The **Memory Leaks, Race Conditions, and Connection Pools** checks appear across Design, Architecture, and Backend. Write them once as a dedicated shared section in `AGENTS.md` covering both frontend animation/UI cleanup (GSAP, Lenis, Anime.js, Three.js, listeners, stale responses) and backend resources (bounded pools, timeouts, release on error, graceful `SIGTERM` shutdown). |
| **Cross-AI Governance Files** | Never emit Cursor-only rules. Generate one unified source of truth in root `AGENTS.md`, a thin pointer in root `CLAUDE.md` (for Claude Code), modular Antigravity always-on rules in `.agents/rules/*.md`, and modular Cursor always-on rules in `.cursor/rules/*.mdc` (matching only the pillars active in the repo). |
| **Default Full-Run Order** | Run full 3-pillar setups in this order: **1. Frontend Design → 2. System Architecture → 3. Backend & Security → 4. Cross-AI Governance** (unless the user explicitly requests a different order). |

---

## Phase 0: Workspace Inspection & Scope Routing (Always First)

Before asking questions or proposing designs, inspect the current workspace:

1. **Detect Project State:**
   - Check directory structure, manifests (`package.json`, `pyproject.toml`, `go.mod`, `Cargo.toml`, `compose.yaml`, `Dockerfile`, etc.), existing frontend/backend code, database schemas/migrations, and existing docs (`docs/design.md`, `design.md`, `docs/ARCHITECTURE.md`, `ARCHITECTURE.md`, `docs/BACKEND.md`, `BACKEND.md`, `AGENTS.md`, `CLAUDE.md`, `.agents/rules/`, `.cursor/rules/`).
   - Classify the project as **Greenfield** (new/empty workspace) or **Existing Codebase** (partial/full app or backend needing alignment or an Audit & Upgrade).
2. **Read Existing Blueprint Docs First:**
   - If any of `docs/design.md`, `docs/ARCHITECTURE.md`, `docs/BACKEND.md`, or `AGENTS.md` already exist, read them completely before proceeding so no recommendation contradicts already-locked decisions.
3. **Confirm Run Scope:**
   - **Full Blueprint:** Run Stages 1 → 2 → 3 → 4 sequentially, pausing for user approval at each stage.
   - **Single-Pillar / Partial Run:** If the user asked for one pillar (e.g., *"audit my backend"*, *"set up design.md"*, *"create ARCHITECTURE.md"*), jump straight to that stage while reading any existing companion docs, then finish with Stage 4 to update only that pillar's governance rules.
4. **Never Guess Missing Context:**
   - Summarize what you found in the workspace in 3–5 concise bullets.
   - Ask grouped clarifying questions ONLY for information missing from the codebase and existing docs (e.g., product goal, ideal client, tech stack preferences, online vs. offline/local Docker runtime, security/compliance sensitivity).
   - **Stop and wait for answers** before proposing options or writing files.

---

## Stage 1: Frontend Design & Apple HIG (`docs/design.md`)

1. **Read Full Spec:** Open and read [references/design-spec.md](./references/design-spec.md) completely.
2. **Gather Missing Context:**
   - Fill every blank line of the **Project** block with the user (*Name, What it is, Ideal client, One-line company description, Primary next action/CTA, Why they should care, Feel*).
   - If an existing UI/codebase exists, note existing brand assets, tokens, components, and gaps against Apple HIG / WCAG 2.2 AA / conversion goals.
3. **Research & References:**
   - Search the web for current visual hierarchy standards as instructed by `design-spec.md`.
   - When designing or auditing specific UI surfaces, use Mobbin MCP (and Figma reference board workflow if requested/available) per the References section of `design-spec.md`.
4. **Propose Options With Pros & Cons (Mandatory Gate):**
   - Present **2–3 Color Palette options** (explicit 60/30/10 roles, WCAG 2.2 AA contrast ratios, dark-mode calibration, semantic token mapping) with **Pros & Cons**.
   - Present **2–3 Typography pairings** (max 2–3 families, mobile iPhone SE vs. desktop scale, readability/Dynamic Type notes) with **Pros & Cons**.
   - Present the proposed **Section & Folder Map** (`src/sections/...`, `src/shared/tokens/`, aligned with `docs/ARCHITECTURE.md` if it already exists) and initial **Visual Hierarchy** plan (3 jobs: explain → trust → action).
   - **WAIT for the user's explicit selection before writing `docs/design.md` or building any UI.**
5. **Write `docs/design.md`:**
   - Write the complete, locked specification directly to `docs/design.md` incorporating 100% of the approved decisions and mandatory rules from `design-spec.md` (Project block, core principles & branding, 3 jobs, folder map, 3 hierarchy questions answered per section, solid CTA policy, color/dark mode/materials tokens, typography/UX writing/inclusion, icons/media/charts/web views, mobile-first 320px+ layout/safe areas/RTL/print/accessibility, motion/loading/inputs/cleanup, core product patterns, and any relevant specialized experiences).
   - Summarize what was written and ask whether to proceed to **Stage 2: System Architecture** (or **Stage 4** on a single-pillar run).

---

## Stage 2: System Architecture (`docs/ARCHITECTURE.md`)

1. **Read Full Spec:** Open and read [references/architecture-spec.md](./references/architecture-spec.md) completely.
2. **Sync With Existing Context:**
   - Read `docs/design.md` (and `docs/BACKEND.md` if present) so product flows, `sections/`, `shared/tokens/`, and stack constraints are respected.
   - Ask grouped clarifying questions for any unanswered fields in Section 1 of `architecture-spec.md` (*Scale 1–3 yrs, Team Size, Frontend/Backend/API/DB stack, Connectivity/Offline vs. Cloud mode, Deployment/Docker target, Tenancy/Compliance, Primary Bottleneck*).
3. **Evaluate & Propose Options With Pros & Cons (Mandatory Gate):**
   - Independently evaluate **Repository Strategy**, **Frontend Pattern**, **Backend Pattern**, **API & Contracts**, **State/Data Ownership**, and **Runtime/Container Topology** against Section 2 of `architecture-spec.md`.
   - Challenge weak assumptions and propose better hybrids when warranted.
   - Present the recommended architecture alongside the top runner-up alternatives with explicit **Pros & Cons** and why the recommendation fits.
   - **WAIT for the user's explicit approval before writing `docs/ARCHITECTURE.md`.**
4. **Write `docs/ARCHITECTURE.md` & Reconcile Paths:**
   - Write the complete specification directly to `docs/ARCHITECTURE.md` containing **all 10 required sections** from Section 3 of `architecture-spec.md` (Header & Docs Map, Executive Verdict & Initial ADRs, C4 Container Diagram, deep 50+ line tailored ASCII production tree with zero generic placeholders, State/Data/Contract/Schema Ownership, Dependency & Import Governance with copy-pasteable stack-native CI linter configs, Containerization/Local Dev/Persistence, Cross-Cutting Concerns/Resilience/Testing Topology, 10-task "Where Does New Code Go?" Cheat Sheet, and Scale Horizon & Evolution Triggers).
   - **Reconcile `docs/design.md`:** If `docs/design.md` exists and the locked repo/framework layout changed frontend paths (e.g., `apps/web/src/sections/`, `packages/ui/tokens/`), update the folder map in `docs/design.md` immediately so both docs match 100%.
   - Summarize what was written and ask whether to proceed to **Stage 3: Backend & Security** (or **Stage 4** on a single-pillar run).

---

## Stage 3: Backend & Security (`docs/BACKEND.md`)

1. **Read Full Spec:** Open and read [references/backend-spec.md](./references/backend-spec.md) completely.
2. **Sync & Detect Mode:**
   - Read `docs/ARCHITECTURE.md` and `docs/design.md` (if present). Never contradict locked stack, module boundaries, or API contracts.
   - Automatically branch into **Greenfield Mode** or **Audit & Upgrade Mode**:
     - **Greenfield Mode:** Ask grouped clarifying questions for any unanswered fields in Section 1 of `backend-spec.md` (*Interactivity Profile, Scale/Concurrency, Data Sensitivity/Compliance, Critical Operations Requiring Strict Integrity*).
     - **Audit & Upgrade Mode:** Inspect existing routes/controllers, auth/session/token handling, permission & ownership checks, DB schemas/migrations/queries, caching, rate limits, background jobs, Docker/env configs, and error handlers. First present a **Severity-Ranked Audit Report** (`Critical / High / Medium / Low`) citing exact file paths and business impact.
3. **Evaluate & Propose Options With Pros & Cons (Mandatory Gate):**
   - For every relevant category in Section 2 of `backend-spec.md` (**Rendering/API Boundary**, **Auth/Sessions/Tokens**, **Authorization: Permission RBAC + ABAC/ReBAC/Row-Level Ownership**, **Database + 4-Layer Double-Entry Defense + Concurrency/Locking Primitives**, and **Caching/Rate Limiting/Background Queues** including proportionality check for Redis vs. PostgreSQL/in-process LRU), present top 2–3 choices with **Pros, Cons, Why It Fits, and Upgrade Triggers**.
   - Address all **10 OWASP security pillars** in Section 3 of `backend-spec.md`.
   - **WAIT for the user's explicit approval before writing `docs/BACKEND.md` or modifying backend code.**
4. **Write `docs/BACKEND.md`:**
   - Write the complete specification directly to `docs/BACKEND.md` containing **all 9 required sections** from Section 4 of `backend-spec.md` (Header & Companion Docs Map, Audit Findings if Audit Mode, Locked Decisions & Pros/Cons Matrix, Auth/Permission Bucket Map & Object-Level Ownership Policy tailored to real domain entities, Data Model/Constraints/Concurrency Playbook per critical endpoint, Caching/Rate Limiting/Async Queue Catalog, Security Hardening Spec covering all 10 OWASP pillars, Resilience/Resource Hygiene/SIGTERM/Backups/Healthchecks, and Security & Concurrency Verification Checklist).
   - Summarize what was written and proceed to **Stage 4: Cross-AI Governance** (or confirm before applying code refactors in Audit Mode).

---

## Stage 4: Cross-AI Governance Setup

Generate or update governance files so **every AI coding agent** (Antigravity, Claude Code, Codex, Cursor, Copilot, etc.) enforces all active blueprint specs automatically. Only reference docs that actually exist in the workspace.

### 1. Unified `AGENTS.md` (Root — Universal Source of Truth)

Create or surgically update root `AGENTS.md`. Preserve any pre-existing project-specific rules outside these sections. Include only sections corresponding to active `docs/*.md` files:

- **`## Project Blueprint Docs`:** List active spec paths (`docs/design.md`, `docs/ARCHITECTURE.md`, `docs/BACKEND.md`) and mandate reading them before planning or editing, and updating the corresponding doc in the same turn whenever a decision changes.
- **`## Frontend Design & Apple HIG Rules`** *(when `docs/design.md` exists)*: Include all working rules from `references/design-spec.md` (check blank Project block / unchosen palette or type before UI work; one folder per section recorded in `docs/design.md`; answer 3 hierarchy questions per section; use Mobbin/Figma reference flow; verify 3 page jobs; enforce all Apple HIG & WCAG 2.2 AA quality gates).
- **`## System Architecture Rules`** *(when `docs/ARCHITECTURE.md` exists)*: Enforce locked repo/folder placement per the Cheat Sheet, strict unidirectional import boundaries and public module facades, schema isolation (no cross-module SQL joins), single-source-of-truth API contracts, and stack-native boundary lint compliance.
- **`## Backend & Security Rules`** *(when `docs/BACKEND.md` exists)*: Enforce permission-based RBAC + object/tenant-level ownership on every query/action (never hardcoded role checks alone), no auth tokens in `localStorage`, boundary schema validation, 100% parameterized queries, PII/secret log redaction, RFC 9457 safe error envelopes, 4-layer double-entry protection, and explicit concurrency primitives on critical writes.
- **`## Shared Runtime Quality Gates (Memory Leaks, Race Conditions & Connection Pools)`** *(always include)*: Combine the exact checks from `design-spec.md`, `architecture-spec.md`, and `backend-spec.md` (cleanup listeners/timers/observers/GSAP/Lenis/Anime.js/Three.js; stale-response & unmounted-view guards; single completion path for loaders; bounded pools, timeouts, release on success/failure, graceful `SIGTERM` shutdown).

### 2. `CLAUDE.md` (Root — Thin Pointer for Claude Code)

Keep `CLAUDE.md` minimal so rules never duplicate or drift from `AGENTS.md`:

```markdown
# Project Instructions

@AGENTS.md

Read `AGENTS.md` and all applicable blueprint specs in `docs/` (`docs/design.md`, `docs/ARCHITECTURE.md`, `docs/BACKEND.md`) before planning, designing, or modifying code. Keep those docs updated whenever locked decisions change.
```
*(List only the `docs/*.md` files that exist in the workspace.)*

### 3. Antigravity Modular Always-On Rules (`.agents/rules/*.md`)

Create one small file per active pillar using Antigravity's native `trigger: always_on` frontmatter:

- **`.agents/rules/read-design.md`** *(if `docs/design.md` exists)*:
  ```markdown
  ---
  trigger: always_on
  ---
  Read `docs/design.md` and `AGENTS.md` before planning, designing, or editing any UI, layout, styling, tokens, copy, or motion. Update `docs/design.md` in the same change whenever design decisions evolve.
  ```
- **`.agents/rules/read-architecture.md`** *(if `docs/ARCHITECTURE.md` exists)*:
  ```markdown
  ---
  trigger: always_on
  ---
  Read `docs/ARCHITECTURE.md` and `AGENTS.md` before creating files, changing folder structure, adding modules/dependencies, or editing infra/Docker configs. Enforce all import boundaries and module isolation rules, and update `docs/ARCHITECTURE.md` when architectural decisions change.
  ```
- **`.agents/rules/read-backend.md`** *(if `docs/BACKEND.md` exists)*:
  ```markdown
  ---
  trigger: always_on
  ---
  Read `docs/BACKEND.md`, `docs/ARCHITECTURE.md`, and `AGENTS.md` before modifying APIs, auth, permissions, database schemas, queries, caching, queues, or security configs. Update `docs/BACKEND.md` whenever backend or security decisions change.
  ```

### 4. Cursor Modular Always-On Rules (`.cursor/rules/*.mdc`)

Mirror the exact same active pillars in `.cursor/rules/` using modern Cursor `.mdc` frontmatter so teams/workflows using Cursor stay 100% aligned:

- **`.cursor/rules/read-design.mdc`** *(if `docs/design.md` exists)*:
  ```markdown
  ---
  description: Always read docs/design.md and AGENTS.md before any UI, styling, layout, copy, or motion work.
  alwaysApply: true
  ---
  Read `docs/design.md` and `AGENTS.md` before planning, designing, or editing any UI, layout, styling, tokens, copy, or motion. Update `docs/design.md` in the same change whenever design decisions evolve.
  ```
- **`.cursor/rules/read-architecture.mdc`** *(if `docs/ARCHITECTURE.md` exists)*:
  ```markdown
  ---
  description: Always read docs/ARCHITECTURE.md and AGENTS.md before structural, module, contract, or infra work.
  alwaysApply: true
  ---
  Read `docs/ARCHITECTURE.md` and `AGENTS.md` before creating files, changing folder structure, adding modules/dependencies, or editing infra/Docker configs. Enforce all import boundaries and module isolation rules, and update `docs/ARCHITECTURE.md` when architectural decisions change.
  ```
- **`.cursor/rules/read-backend.mdc`** *(if `docs/BACKEND.md` exists)*:
  ```markdown
  ---
  description: Always read docs/BACKEND.md, docs/ARCHITECTURE.md, and AGENTS.md before backend, auth, or DB work.
  alwaysApply: true
  ---
  Read `docs/BACKEND.md`, `docs/ARCHITECTURE.md`, and `AGENTS.md` before modifying APIs, auth, permissions, database schemas, queries, caching, queues, or security configs. Update `docs/BACKEND.md` whenever backend or security decisions change.
  ```

---

## Final Verification Checklist

Before concluding any full or single-pillar run, verify:

1. **Zero Conflicts:** Folder paths, stack choices, auth/UI flows, and shared tokens agree across all existing `docs/*.md` files.
2. **100% Fidelity:** Every required section of each executed reference spec is complete—no generic placeholders, no skipped OWASP/HIG/concurrency rules.
3. **Approval Honored:** Every locked decision in `docs/*.md` was explicitly approved by the user before writing.
4. **Governance Synced:** `AGENTS.md`, `CLAUDE.md`, `.agents/rules/*.md`, and `.cursor/rules/*.mdc` exist, contain valid frontmatter, and reference only real, existing `docs/*.md` paths.
