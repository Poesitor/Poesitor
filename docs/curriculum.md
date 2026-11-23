# Poesitor curriculum

The destination is [plans/poesitor-blog.md](plans/poesitor-blog.md). The route is **ship early and level up live**: chapters follow what you need to learn first, Poesitor goes live in chapter 1, and every chapter after that upgrades the live site toward the plan. Some early pieces get replaced later (posts stored as files → database → API → editor). Doing those replacements is part of the lesson.

How to use it: each session starts with `/coach`. Task titles below are outlines. The coach turns each one into a task card sized to one ~90-minute session, and splits it if it's too big. Cards get thinner as you go: **full cards** in Ch 1–3, **partial** in Ch 4–7, **goal + done-when only** in Ch 8–11, and **you write your own cards** in Ch 12–14.

Rough pace: at about 5 hours a week, each chapter takes 2–3 weeks, about 8–10 months in total. Poesitor is live after Ch 1.

---

## Ch 1 — Poesitor v0
**You come out able to:** build a small Next.js (App Router) site, load content from files, route `/topic/slug`, write your first unit tests, and deploy.
**Goes live:** a real Poesitor on Vercel with your first post, and a link you can share.

- 1.1 **Workspace & repo:** `git init`, a public GitHub repo, `create-next-app` (TypeScript, App Router, ESLint), AI autocomplete off for this workspace, then walk through every generated file and make the first commit. *(Coach note: the generator may refuse a folder that isn't empty. Let them hit that and solve it.)*
- 1.2 **Layout & home page:** root layout, header/footer components, props, a home page listing hard-coded posts. Server components vs client components.
- 1.3 **Posts as Markdown files:** a content folder organised by topic; frontmatter (title, date, topic, tags, excerpt, draft); a loader that validates frontmatter so a broken post fails the build.
- 1.4 **First tests:** set up Vitest and test the loader: valid post, missing field, duplicate slug within a topic, sort order, drafts excluded.
- 1.5 **Post pages:** dynamic `/[topic]/[slug]` routes, static generation, a proper 404 for unknown slugs, safe Markdown rendering.
- 1.6 **Topic sections:** `/[topic]` landing pages, topic config (name, description, accent colour), accent colour through CSS variables, navigation.
- 1.7 **Ship it:** a Vercel project, a production deploy, a title template ("· Poesitor by Abdulazeem Bakare"), publish your first real post and share the link.

**Chapter done when:** the site is live; it has at least 1 real post; `/topic/slug` works; unknown paths show a 404 page; loader tests pass; you can explain every file in the repo.

## Ch 2 — Shareable & solid
**You come out able to:** make pages share well and get found by search engines, build a small design system, write end-to-end tests, and run CI with a PR workflow.
**Goes live:** rich link previews, RSS, dark mode, About / Work with me / Projects pages, share buttons, a CI badge.

- 2.1 Per-post metadata and auto-generated preview images in the topic colour
- 2.2 RSS per topic, sitemap, robots, JSON-LD (`Person`, `BlogPosting`)
- 2.3 Design tokens, typography scale, dark mode, responsive layout
- 2.4 About (with the *poesitor* definition), Work with me (a `mailto` link until Ch 10), and Projects (Markdown for now)
- 2.5 Share buttons (X / LinkedIn share links, copy link) with UTM tags
- 2.6 Playwright: the first end-to-end tests (home → topic → post, 404), plus axe accessibility checks
- 2.7 GitHub Actions CI (lint, type-check, unit, e2e), branch protection, and PRs from now on

**Chapter done when:** a shared link shows a rich card; RSS validates; CI is required to pass on `main`; axe reports no serious issues.

## Ch 3 — Data
**You come out able to:** write SQL, design a schema around invariants, use Drizzle migrations, and test against a real database.
**Goes live:** posts served from Neon Postgres, and links that survive renames.

- 3.1 SQL fundamentals on Neon: tables, constraints, CRUD, joins (by hand, in the SQL editor)
- 3.2 Design the schema yourself: topics, posts, tags, post_tags, redirects, checked against the plan's invariants
- 3.3 Drizzle schema and migrations; Neon branches for development
- 3.4 An import script that moves the Markdown posts into the database (safe to run twice)
- 3.5 Next.js reads from the database; caching and revalidation; delete the file loader
- 3.6 Redirects: changing a slug or topic writes a redirect, old paths return 301, and a redirect can never shadow a live path (with tests)
- 3.7 Integration tests against a throwaway Neon branch in CI

**Chapter done when:** the live site reads from the database; renaming a post keeps its old link working; migrations are in the repo; database tests run in CI.

## Ch 4 — The API
**You come out able to:** structure a monorepo, build a NestJS API (modules, DI, validation, errors), share types, and test an API at three levels.
**Goes live:** nothing visible. The API is built and tested in CI, and the web app still reads the database directly.

- 4.1 Monorepo conversion: pnpm workspaces + Turborepo, `apps/web`, `packages/db`, CI updated. Why monorepos exist.
- 4.2 NestJS fundamentals: `nest new` into `apps/api`, modules/controllers/providers/DI, a health endpoint, a first HTTP test
- 4.3 Read endpoints for posts and topics: DTOs, validation, 404 handling, pagination
- 4.4 A shared contracts package (schemas used by both api and web); Swagger/OpenAPI docs
- 4.5 API tests: unit (with mocks), integration (real Neon branch), HTTP end-to-end
- 4.6 Config and secrets: env validation at startup, nothing secret in git

**Chapter done when:** the API serves everything the web app needs, is documented in Swagger, and is tested in CI.

## Ch 5 — Ship the API
**You come out able to:** containerise an app, set up and secure a Linux server, run services with Compose behind an HTTPS reverse proxy, and deploy continuously with rollbacks.
**Goes live:** the public site is served through *your* API on *your* VPS.

- 5.1 Containers: a multi-stage Dockerfile for the API, built in Codespaces/CI (not your laptop)
- 5.2 VPS basics (provisioned by hand this time; Terraform comes in Ch 12): SSH keys, a non-root user, firewall, automatic updates
- 5.3 Docker Compose on the VPS: api + redis, healthchecks, restart policies, logs
- 5.4 Traefik with Let's Encrypt; DNS for `api.` your domain
- 5.5 CD: Actions builds → GHCR → deploys by commit SHA; a rollback drill
- 5.6 Switch the web app from the database to the API, step by step (the "strangler" pattern); what readers see when the API is down
- 5.7 Sentry, structured logs, an external uptime monitor; an outage drill

**Chapter done when:** production web reads from your API over HTTPS; merging deploys automatically; you've rehearsed a rollback; alerts fire.

## Ch 6 — Admin & auth
**You come out able to:** build secure sessions, passkey login, route guards, and a CRUD admin.
**Goes live:** you publish posts from your own admin, from your phone.

- 6.1 Set up the admin app (`apps/admin`) on its own subdomain
- 6.2 Auth concepts: sessions vs tokens, cookie flags, CSRF, session storage
- 6.3 Passkeys (WebAuthn) registration and login; bootstrapping the first admin with an SSH-only command
- 6.4 Backup codes, the recovery command, guards on every admin API route (with tests)
- 6.5 Post management (a plain editor for now): drafts/published, topic, tags
- 6.6 Slug/topic edits create redirects; publishing tells the web app to refresh that page (on-demand revalidation)
- 6.7 Managing topics and projects

**Chapter done when:** passkey login works on laptop and phone; public endpoints can't change data; old links still redirect after edits made in the admin.

## Ch 7 — The editor
**You come out able to:** work with structured documents (ProseMirror/Tiptap), custom nodes, data migrations, and safe rendering.
**Goes live:** rich posts written in your own editor.

- 7.1 The ProseMirror/Tiptap model: schema, nodes, marks, JSON
- 7.2 Editor UI: toolbar, bubble or slash menu, keyboard shortcuts
- 7.3 Store Tiptap JSON; migrate the existing Markdown posts into it
- 7.4 Render JSON to React on the web app; XSS safety (with tests)
- 7.5 Custom blocks: code (with highlighting), callout, YouTube (validated URLs)
- 7.6 Autosave and preview

**Chapter done when:** editor posts render correctly on the web; malicious content can't execute; old posts are migrated.

## Ch 8 — Media & jobs
**You come out able to:** use object storage behind an interface, handle uploads, run background jobs with retries, and guarantee something happens exactly once.
**Goes live:** images and audio in posts, and scheduled publishing.

- 8.1 Object storage concepts; Neon Object Storage (S3 API) behind a `StorageService` interface
- 8.2 Uploads: presigned URLs or uploads through the API; type and size validation
- 8.3 BullMQ + Redis: why queues; an image-processing job (variants, blur placeholders)
- 8.4 Image block in the editor; responsive images on the web
- 8.5 Audio: a waveform job, an audio block and player
- 8.6 Scheduled publishing: delayed jobs, idempotency, retries, alerts

**Chapter done when:** uploaded media is optimised and served; a scheduled post publishes exactly once, even if the worker restarts.

## Ch 9 — Comments
**You come out able to:** design public write APIs that resist abuse, enforce data invariants, and build moderation workflows.
**Goes live:** readers can comment and react.

- 9.1 Comments data model + API: depth ≤ 2, statuses, guest email never leaves the API (proved by a test)
- 9.2 Comment UI: form, threads, author badge
- 9.3 Abuse defences: per-IP rate limits, a honeypot field, validation, XSS
- 9.4 Moderation queue in the admin: status transitions (with tests)
- 9.5 Reactions: visitor cookie, unique constraint, rate limits

**Chapter done when:** guests can comment; nothing appears unmoderated; reactions can't be stuffed; emails never leak.

## Ch 10 — Email
**You come out able to:** send email that gets delivered, build token-based confirmation and unsubscribe flows, and handle bounces.
**Goes live:** the newsletter, reply notifications, and the Work with me inbox.

- 10.1 Deliverability: SPF, DKIM, DMARC; domain set up on Resend
- 10.2 React Email templates; sending through jobs
- 10.3 Double opt-in and one-click unsubscribe tokens (with expiry and the `List-Unsubscribe` header)
- 10.4 Reply notifications (opt-in, confirmed on first use)
- 10.5 Newsletter by topic: sent on publish, batched when several posts go out the same day
- 10.6 Work with me form + admin inbox; bounce/complaint webhooks → suppression list

**Chapter done when:** a subscriber gets a new-post email for their chosen topics only; unsubscribing takes one click; unconfirmed addresses never get mail.

## Ch 11 — AI engineering I: moderation
**You come out able to:** call an LLM safely from a backend, get structured output, design for failure, **measure quality with evals**, and control cost.
**Goes live:** AI-moderated comments and contact messages. **Wave 1 complete.**

- 11.1 LLM fundamentals: tokens, models, pricing, latency, nondeterminism; an `AiModule` with the key only on the server
- 11.2 Moderation prompt + structured output schema; validate what the model returns
- 11.3 Plug it into the comment/contact pipeline as a job: timeouts, retries, **fail closed**
- 11.4 Build a labelled eval set (~50–100 comments), choose metrics, write an eval script
- 11.5 Evals in CI with a threshold gate; change prompts by measuring, not guessing
- 11.6 Rate limits per visitor, a monthly spend cap, usage logging, graceful degradation

**Chapter done when:** clean comments auto-publish, borderline ones get queued with a reason, an OpenAI outage leaves comments pending, the eval gates merges, and the spend cap is enforced (with tests).

## Ch 12 — DevOps II: infrastructure as code & previews *(you write your own cards)*
**You come out able to:** use Terraform, rebuild a server reproducibly, set up dynamic routing, and run temporary environments for each PR.
**Goes live:** a full-stack preview for every PR.

- 12.1 Terraform fundamentals and remote state; import the existing VPS and DNS
- 12.2 Terraform for Neon and Vercel; managing secrets
- 12.3 cloud-init; rebuild the VPS from scratch (disaster drill)
- 12.4 Traefik routing through Docker labels; wildcard certificates
- 12.5 Per-PR environments: Neon branch + API container + Vercel preview, URLs commented on the PR
- 12.6 Teardown on close + a nightly sweep; resource limits; self-hosted Umami

**Chapter done when:** opening a PR gives a working preview stack; closing it removes everything; the VPS rebuilds from code in under an hour.

## Ch 13 — AI engineering II: the copilot (wave 2)
- 13.1 Streaming responses from the API to the admin
- 13.2 Copilot actions in Tiptap: tighten/rewrite a selection, titles, SEO description, tags (structured output)
- 13.3 Social drafts for each platform and topic account in the publish flow, with share links + UTM
- 13.4 Quality checks for the copilot (a rubric, a review set)

**Chapter done when:** copilot actions stream into the editor, and publishing offers social drafts.

## Ch 14 — AI engineering III: retrieval (wave 3)
- 14.1 Embeddings: vectors, similarity, models, dimensions
- 14.2 pgvector on Neon; chunking; an embedding job on publish/update; a backfill
- 14.3 Semantic search (stretch goal: combined with keyword search)
- 14.4 Related posts
- 14.5 "Ask my blog": retrieve → answer with sources → stream with citations; say honestly when a question isn't covered
- 14.6 RAG evals, abuse limits, spend cap, prompt-injection risks from content

**Chapter done when:** answers cite real posts, unanswerable questions get an honest "not covered", and the RAG eval gates CI.
