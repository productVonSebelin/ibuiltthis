# ARCHITECTURE.md — IBUILTTHIS Technical Spine

*Status: **Core framework signed off (2026-05-19); rest of stack still proposed.** See latest Decisions Log entry. | Last updated: 2026-05-19*

This document is the single source of truth for **how IBUILTTHIS is built**. It complements [PRODUCT_DEFINITION.md](PRODUCT_DEFINITION.md) (what we're building and why) and [CLAUDE.md](CLAUDE.md) (how we work). Read all three before proposing or writing any technical code.

This file lives forever. It captures three things:

1. **The stack** — every framework, library, and platform we depend on.
2. **The third-party tools & integrations registry** — every external service the product talks to. Adding a service without recording it here is a bug.
3. **The decisions log** — every load-bearing technical decision, with date and rationale. We refer back to this when re-evaluating choices.

If a decision is not yet made, it lives under **Open Questions** until it is. Agents must surface unresolved decisions, never silently invent them.

---

## How To Use This Document

- **Architect agent:** read this before producing a task list. If your plan adds or changes a dependency, append to the Stack and Decisions Log sections.
- **Developer agents:** read this before writing code. Match the patterns and tools listed here. If your task forces a deviation, stop and surface it — do not silently introduce a new dependency.
- **Sören:** review the Decisions Log when deciding whether a new tool/library is worth adding. Bias is toward fewer dependencies, not more.

**Editing rules:**
- Append to the Decisions Log with a date and a one-paragraph rationale. Never delete past entries; if a decision is reversed, write a new entry referencing the old one.
- Update the Stack and Third-Party Tools tables in place when versions or providers change.
- If a section starts to dwarf the rest of the file, split it into a sibling doc and link it from here.

---

## Proposed Stack (v0)

The stack below is optimised for **a solo PM building with AI**. That means: maximum AI-coding velocity, the highest density of training data and community examples, and conventions that Claude/Cursor/Codex can follow without you babysitting them. We will pay for that bias later if we hit niche performance requirements — that's an acceptable trade for now.

| Layer | Choice | Why this, not the alternative |
|---|---|---|
| **Language** | TypeScript (strict) | One language end-to-end. Catches the class of bugs an AI is most likely to introduce. |
| **Runtime / package manager** | Node.js 22 LTS · pnpm | LTS for stability; pnpm for fast, deterministic installs and disk-friendly monorepo readiness if we split kickstart from showcase. |
| **App framework** | **Next.js 16** (App Router, React 19, RSC) | The default vibe-coder stack. Highest AI training density, full-stack in one repo (UI + API routes + server actions), great SEO for public project cards. Same App Router as 15; React 19 is the floor (since 15); `@cloudflare/next-on-pages` supports it. Migration footgun: `experimental.turbopack` moved to top-level `turbopack` in `next.config.ts`. |
| **Styling** | Tailwind CSS v4 | The de-facto vibe styling. AI is fluent in Tailwind tokens. |
| **UI components** | **HeroUI v3** (React Aria primitives) | Polished, animation-forward components that fit the "celebrate building" identity of IBUILTTHIS. Built on Tailwind v4 + React 19 natively, no provider wrapper required, accessible by default via React Aria. Trade-off: stronger default visual identity than shadcn — we commit to a custom theme (palette, radii, semantic tokens via Tailwind `@theme`) before first feature ships, so the product looks like IBUILTTHIS, not like a HeroUI demo. |
| **Database** | Postgres (local: Docker; managed later: Neon) | Postgres is universal. Neon is serverless, branchable per-PR (great with GitHub Actions later), and Edge-compatible — keeping the Cloudflare target reachable. |
| **ORM** | Drizzle ORM | Type-safe, edge-runtime compatible, SQL-first. Drizzle keeps Cloudflare on the table; Prisma would force us to rip it out when we deploy. The AI gap between Drizzle and Prisma has narrowed enough that Drizzle is the right call. |
| **Auth** | Auth.js (NextAuth v5) | Email magic link as default; GitHub OAuth ready for the day "platform follows along" needs to scrape repos. Mature, AI-supported. |
| **Forms / validation** | Zod + React Hook Form | Zod schemas double as DB validators and API contracts. |
| **Testing** | Vitest (unit), Playwright (e2e — sparingly) | Vitest is fast and Vite-native; Playwright only for golden-path e2e flows. Coverage strategy is decided per task by the developer agent. |
| **Linting / formatting** | ESLint (Next.js config) + Prettier | One opinionated config, no bikeshedding. |
| **Content (kickstart resources)** | MDX in-repo | Tool-compass entries, kickstart prompts, walkthroughs live as MDX for now. If volume grows or non-engineers need to edit, we revisit (Sanity / Notion-as-CMS / TinaCMS). |
| **Analytics** | *Deferred* | Decide once we have something to measure. Bias: PostHog (self-hostable, AI-friendly) or Plausible (privacy-first, simpler). |
| **Error tracking** | *Deferred* | Likely Sentry once we host. |

### Repo shape (proposed)

```
src/
  app/                # Next.js App Router routes
    (marketing)/      # public surfaces — landing, public cards
    (app)/            # authed surfaces — dashboard, builder
    api/              # API routes (only when server actions don't fit)
  components/         # shared UI (shadcn/ui lives in components/ui)
  lib/                # server-side helpers, db client, auth config
  db/
    schema.ts         # Drizzle schema
    migrations/       # generated SQL migrations
  content/            # MDX content (kickstart, tool compass)
tests/                # Vitest + Playwright tests
```

**This shape is a starting hypothesis, not a contract.** The architect agent owns refining it as features land.

### Dev environment

- Local Postgres via Docker Compose (committed `docker-compose.yml`).
- `.env.local` for secrets. Never committed; agents are denied read/write on `.env*` (see [.claude/settings.json](.claude/settings.json)).
- One-command bootstrap: `pnpm install && pnpm db:up && pnpm db:migrate && pnpm dev`. The first feature we ship will be this bootstrap, owned end-to-end by the build pipeline.

---

## Third-Party Tools & Integrations Registry

Every external service the product depends on lives here. Adding a service without an entry is a bug. Removing one requires a Decisions Log entry.

| Service | Purpose | Tier / cost | Owner of the account | Status | Notes |
|---|---|---|---|---|---|
| _(none yet)_ | | | | | |

**Categories to fill in as we go:**

- **Auth providers** — GitHub OAuth app, email sender (Resend / Postmark / SES)
- **Database** — Neon (when we leave local)
- **Hosting** — Cloudflare Pages + Workers (deferred — see below)
- **CI/CD** — GitHub Actions (deferred)
- **AI APIs** — Anthropic / OpenAI / others if/when we generate cards or guidance
- **Analytics & error tracking** — deferred
- **Storage** — R2 (Cloudflare) or S3 if we host media
- **Email transactional** — Resend / Postmark
- **Payments** — Stripe if/when monetization happens

**Rule:** when a developer agent integrates a new service, the same task must add a row here. The architect should embed this in the task's acceptance criteria.

---

## Deferred Decisions

These are intentionally postponed. We've named them so they don't get silently decided by accident.

| Decision | Default direction (subject to confirmation) | When to revisit |
|---|---|---|
| **CI/CD** | GitHub Actions → Vercel (or rely on Vercel's git integration) | Decide when we set up the first deploy. |
| **Staging environment** | TBD (likely Vercel preview deployments + Neon DB branch) | Decide alongside first deploy. |
| **Domain & DNS** | TBD | When we have something to share publicly. |
| **Monetization stack** | TBD (Stripe likely) | Only after we know what we're charging for. |
| **Mobile** | Web-first; revisit native only if usage data demands it | Post-MVP. |
| **Social graph mechanics** | Shareable cards only at first; follow/feed is a separate decision | When discovery patterns are clearer. |

Today's scope is **build locally**. Hosting infra is a session of its own.

---

## Open Questions

1. **One product or two?** (See PRODUCT_DEFINITION.md §Open Questions.) Affects whether the kickstart and showcase share a single Next.js app or split into two surfaces under one monorepo. **Default until decided:** one app, two route groups (`(kickstart)` and `(showcase)`). Lowest cost to reverse if we split.
2. **Card generation source.** Does the platform passively observe (GitHub commits, AI tool logs) or actively prompt the builder for milestones? Affects how heavy the integrations layer needs to be.
3. **MDX vs CMS for kickstart content.** MDX in-repo is fine for v0. The trigger for revisiting is when a non-technical contributor (or Sören wearing his PM hat) wants to edit content without a PR.

---

## Decisions Log

Append-only. Newest first. Each entry: date, what was decided, why, and what was considered but not chosen.

### 2026-05-19 — Hosting: Vercel (replaces Cloudflare Pages + Workers default)

- **What:** Hosting target is **Vercel**, superseding the prior Cloudflare Pages + Workers default in [Deferred Decisions](#deferred-decisions).
- **Why:** Zero-friction Next.js deploys (first-party, no `@cloudflare/next-on-pages` shim), best DX for a solo PM optimising for momentum, full Node runtime available (no edge-only API constraints when we add auth/DB later).
- **Implications for the rest of the stack:** Drizzle + Neon still work cleanly on Vercel (Neon is the recommended Postgres). The Cloudflare-driven constraints in the original stack proposal (edge-runtime ORM, R2 for storage) relax — we can revisit when those decisions come up. **Drizzle stays for now** (still a fine choice on Vercel); we don't reopen it unless a feature forces it.
- **Considered but not chosen:** Cloudflare Pages + Workers. Cheaper at scale and edge-native, but the `next-on-pages` adapter adds friction we don't need at v0, and Node-API constraints would bite us once we add auth and server actions. Trade-off we'd reconsider only if hosting cost becomes meaningful.
- **Reversal cost:** low at v0 (no deploys yet). Higher once we have Vercel-specific features (KV, blob storage) baked in.

### 2026-05-19 — Core framework signed off: Next.js + TypeScript + Tailwind

- **What:** Sören signed off on the **core three** of the proposed stack — Next.js (App Router) + TypeScript + Tailwind CSS — as the foundation we start building on. Triggered by the "where do I start" conversation: Sören wants to see the core look-and-feel of the product (Showcase) without boilerplate friction.
- **Why:** Highest AI-coding velocity, biggest training-data corpus, full-stack in one repo, zero infrastructure overhead to get a first screen up. Matches the audience (vibe-coder PMs recognise this stack and trust it).
- **What is *not* signed off yet — still "proposed":** HeroUI v3, Drizzle, Auth.js, Postgres + Neon, Vitest/Playwright, MDX-in-repo. These weren't part of the conversation that produced this sign-off. For the first Showcase slice (hardcoded data, no auth, no DB) most of these aren't on the critical path anyway — we'll confirm them feature-by-feature as they become load-bearing.
- **Reversal cost:** low for the core three (no code yet).

### 2026-05-07 — UI library: HeroUI v3, replacing shadcn/ui

- **What:** HeroUI v3 (`@heroui/react` + `@heroui/styles`) chosen as the component library, superseding shadcn/ui in the original stack proposal.
- **Why:** IBUILTTHIS is a showcase product celebrating people's first builds — a more vibrant, animation-forward identity fits the brief better than shadcn's neutral defaults. HeroUI v3 is built natively on Tailwind v4 + React 19, no longer requires a `HeroUIProvider` wrapper, and uses React Aria Components for accessibility (peer of Radix). Removes the "this clearly looks like a shadcn template" tell.
- **Considered but not chosen:**
  - *shadcn/ui with a custom theme.* Genuinely defensible — the "AI look" is the defaults, not the library. Rejected because the work to de-shadcn-ify it (custom palette, radii, type, restyling Button/Card/Input) is roughly equivalent to the work to de-HeroUI-ify HeroUI, and HeroUI starts from a more product-flavored baseline.
  - *Mantine, Park UI, Radix Themes.* Each trades shadcn's identity for theirs. No clear win.
  - *Hand-rolled with Radix + Tailwind.* Most distinctive, but slow for a solo PM working with AI. Wrong call at v0.
- **Mitigation for HeroUI's recognizability:** before any feature work, we lock a custom theme spec — palette, radii, semantic tokens via Tailwind v4's `@theme` directive — so the product has its own identity from day one. `className` overrides on the 5–10 most-used components carry the rest.
- **Reversal cost:** moderate at v0 (no UI code yet); high once we've built ~10+ screens against HeroUI primitives.

### 2026-05-07 — Next.js 16, not 15

- **What:** Next.js 16 selected over the originally-proposed 15.
- **Why:** Same App Router, React 19 already required by 15, `@cloudflare/next-on-pages` supports 16, and Context7's current docs show 16.2.2 stable. Starting on 15 would mean an upgrade tax within weeks.
- **Migration footgun to be aware of:** Turbopack config moved from `experimental.turbopack` → top-level `turbopack` in `next.config.ts`. Min Node 20.9+ (we're on 22 LTS ✅), TS ≥ 5.1.
- **Considered but not chosen:** Sticking with Next.js 15 to follow tutorial defaults. Rejected — the architect/developer agents will pull current Context7 docs anyway, so the tutorial-lag cost is small.
- **Reversal cost:** low.

### 2026-05-07 — Initial stack proposed (this document)

- **What:** Next.js 15 + TypeScript + Tailwind + shadcn/ui + Postgres + Drizzle + Auth.js as the v0 stack.
- **Why:** Optimise for solo + AI-coded velocity. This is the stack with the highest density of AI training data and the fewest surprises for a PM building their first product. Hosting target (Cloudflare) is preserved by choosing edge-compatible primitives (Drizzle, Neon, Auth.js).
- **Considered but not chosen:**
  - *Astro + React islands.* Cleaner on Cloudflare and great for content-heavy public surfaces, but weaker for app-shaped flows (auth, social actions) and a smaller AI corpus. Revisit if we ever split the marketing/kickstart surface from the dashboard.
  - *Remix / React Router 7 on Cloudflare Workers.* Purpose-built for the eventual host but smaller ecosystem and AI footprint. Trade not worth it at v0.
  - *SvelteKit.* Excellent runtime, but the AI corpus is materially smaller. Wrong call when the user is leaning on AI to write code.
  - *Prisma.* More AI-friendly than Drizzle today, but does not run cleanly on Cloudflare Pages Edge runtime. Switching ORMs later is painful, so we eat the smaller AI gap now.
- **Status:** Proposed. Not yet signed off.
- **Reversal cost if we change our minds:** moderate at v0 (no code yet), high once we have a database with users. Decide deliberately.
