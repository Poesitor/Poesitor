# Poesitor — Blog Plan

> Status: design settled through a planning interview on 2026-11-23. Nothing built yet.
> Author: Abdulazeem Bakare · Brand: **Poesitor**
>
> **This is the destination, not the build order.** Poesitor is built by hand as a coached learning project. The route is [../curriculum.md](../curriculum.md): *ship early, level up live*. It goes live in chapter 1 as a simple Next.js site and is upgraded chapter by chapter. That **overrides** decision 8's "full custom admin from day one" and the "walking skeleton" first milestone below (decided 2026-11-23).

## What & why

Poesitor is a personal, multi-topic blog written by one author: Abdulazeem Bakare. It covers software, AI and tech, business, hardware, music, art, animation and guitars. Its purpose is **personal branding that brings in opportunities**: developers, founders and CEOs read the posts relevant to them and come to Abdulazeem with jobs, collaborations and other openings.

The codebase is **itself a portfolio piece**. It's a public GitHub repo meant to show off full-stack, DevOps and AI engineering. "Flamboyant" means impressive *engineering*, not flashy visuals.

> **poesitor** (n.) — a person or entity that creates or arranges poetic or creative works with skill and artistry. From Latin *poesis* ("poetry, creation"; Greek *poiesis*, "making") + *-sitor* (agent, as in *compositor*). Used as the site's tagline and About-page hook.

## Decisions

### Product & audience
1. **One author** (Abdulazeem). Readers can't publish posts.
2. **Many topics, different audiences.** Software/AI/tech, business, hardware, music, art, animation, guitars, with more added later.
3. **Every post has a permanent, shareable link** meant for topic-specific social accounts.
4. **Success = inbound opportunities**, not just traffic.
5. **Pages:** Home, topic sections, posts, **About**, **Work with me** (contact form stating what Abdulazeem is open to), **Projects/portfolio**, and a **newsletter you subscribe to by topic**. No CV page.
6. **Brand:** "Poesitor" is the site name and domain, and its definition is the tagline. The full name *Abdulazeem Bakare* appears on every byline, in every page title ("Post · Poesitor by Abdulazeem Bakare"), on the About page, and in the JSON-LD `Person` metadata. Optionally, a full-name domain redirects to the site. *Why:* the coined word ties all the topics together and matches the X handle, while the name keeps the site findable when someone searches for him.
7. **Design:** clean, editorial and typography-led, with an accent colour per topic, light/dark mode and subtle motion. The visuals serve the writing.

### Architecture & stack
8. **Built from scratch, with a full custom admin from day one** (rejected: Substack/Hashnode/Medium, Ghost/WordPress). *Why:* the repo is the showcase.
9. **TypeScript monorepo:** pnpm workspaces + Turborepo, containing `apps/web` (reader site, Next.js), `apps/admin` (Next.js), `apps/api` (NestJS), and shared `packages/*` (db, ui, editor, types/validation).
10. **Next.js front ends + a separate NestJS API.** *Why NestJS over bare Express:* NestJS runs on Express but adds modules, dependency injection, guards, validation, scheduling and Swagger, and gives a structure reviewers recognise.
11. **Database:** Neon Postgres with the **Drizzle** ORM.
12. **Media:** **Neon Object Storage** (S3-compatible, branches along with the database), accessed through a `StorageService` interface so it can be swapped for Backblaze B2 or AWS S3. Uploads are processed in the API: resized into AVIF/WebP variants, given blur placeholders, and given audio waveforms. **Videos are YouTube embeds.**
13. **Editor:** **Tiptap**, with a custom headless UI. Posts are stored as Tiptap JSON and rendered to React on the server. Custom block types cover audio, gallery, YouTube, code and so on.
14. **Scheduling and background jobs:** Redis + BullMQ in NestJS.
15. **Email:** Resend + React Email, used for the newsletter, reply notifications, contact alerts and email confirmation.

### Content model
16. **Topic sections + tags.** Each post has exactly **one main topic**, plus free-form tags. Topics are managed in the admin, each with its own landing page, description, accent colour and RSS feed.
17. **URLs: `/{topic}/{slug}` with automatic 301 redirects.** Every slug or topic change records the old path.

### Comments
18. **Guest comments** (name + email, no account). *Trade-off accepted:* more spam, offset by AI moderation and a review queue.
19. **Threaded replies limited to 2 levels.** The author's own comments carry an "Author" badge so guests can't pass themselves off as him.
20. **Opt-in email notifications for replies.** The first use of an email address requires a confirmation click, and every email has one-click unsubscribe.
21. **Reactions** on posts and comments: a small fixed set, one per visitor per item (tracked by an anonymous cookie), with per-IP rate limits.

### AI (provider: OpenAI, using the existing API key)
22. Released in waves:
    - **Wave 1 (launch):** AI **moderation** of comments and contact-form messages. Clean messages publish automatically; borderline ones go to a review queue with a reason.
    - **Wave 2:** **Editor copilot**: rewrite/tighten text, suggest titles, SEO descriptions and tags, and draft social posts for each platform and topic account.
    - **Wave 3 (after ~10–15 posts):** **"Ask my blog"** (retrieval-augmented answers with citations, streamed) + **semantic search** + **related posts**, using OpenAI embeddings in pgvector.
23. All AI calls go through a single NestJS `AiModule`. The API key stays on the server only. Public AI endpoints get **rate limits per visitor and a monthly spend cap**.

### Admin auth
24. **Passkey-only sign-in (WebAuthn), no password.** At least two passkeys are registered (laptop via Windows Hello PIN, and phone). Sign-in from other devices works by QR code. Recovery uses offline backup codes, with an **SSH-only recovery command** on the VPS as the last resort. The admin runs on its own subdomain (e.g. `admin.poesitor.dev`).

### Sharing & growth
25. **AI-drafted social copy plus one-click share buttons** (X / LinkedIn share links, copy buttons). Links carry **UTM tags** so analytics shows which platform each reader came from. Until wave 2 arrives, it's share buttons and copy-link only.
26. **SEO:** an automatically generated preview image per post in its topic colour, a sitemap, RSS per topic, canonical URLs and JSON-LD.
27. **Newsletter:** double opt-in, topic preferences, one email per new post (batched if several are published the same day), and one-click unsubscribe.

### Hosting & DevOps
28. **No Cloudflare** (the user's call).
29. **Next.js apps on Vercel.** **NestJS, Redis, background workers and Umami run on a VPS** (≈4–8 GB RAM, ≈€8–15/mo) using Docker Compose behind **Traefik**. The VPS must be in the **same region as Neon**.
30. **No Docker on the laptop** (it's low-end). Local development runs apps directly with `pnpm dev` against a Neon dev branch. **GitHub Actions builds all container images** and pushes them to GHCR.
31. **Full per-PR environments:** each PR gets a Vercel preview, its own API container (`pr-N.api.poesitor.dev`), and its own Neon branch with copied data and files. Everything is torn down on merge or close, with a nightly sweep for leftovers.
32. **Infrastructure as code:** Terraform for the VPS, DNS, Neon and Vercel; cloud-init for the server's first boot.
33. **Releases:** images are tagged with the git commit, so rollback means redeploying the previous commit's image. Migrations only move forward (expand → migrate → contract).
34. **Observability:** Sentry (web, admin, API), structured logs, an **external** uptime monitor, and self-hosted Umami analytics.

### Quality bar (verification)
35. **Full test pyramid with CI gates and AI evals:**
    - Unit tests for domain logic (slug/redirects, scheduling, reaction limits, comment depth).
    - Integration tests for the API against a throwaway Neon branch.
    - **Playwright** end-to-end tests for critical flows: write → schedule → publish a post; comment → moderation → notification; subscribe → confirm → receive; passkey login.
    - Automated **WCAG 2.2 AA** checks (axe) inside the Playwright tests.
    - A **moderation eval**: a labelled set of comments scored in CI, which fails if accuracy regresses.
    - Required checks before merge: type-check, lint, format, all test layers, and the moderation eval.
36. **Repo hygiene:** MIT licence for the code (post content lives in the database, not the repo), secret scanning, conventional commits, PRs even when working solo, and ADRs for the major decisions.
37. **English only. No fixed deadline.** Ship in waves.

## Data model sketch

| Entity | Key fields | Notes |
|---|---|---|
| `Topic` | slug, name, description, accentColor, order | Managed in the admin |
| `Post` | topicId, slug, title, excerpt, contentJson (Tiptap), status (`draft`/`scheduled`/`published`/`archived`), publishAt, publishedAt, coverMediaId, seo fields | One main topic |
| `Tag`, `PostTag` | name/slug | Many-to-many |
| `Redirect` | fromPath → postId | Written on every slug or topic change |
| `Media` | storageKey, kind (image/audio), variants, blurhash, waveform, alt, status | Videos are YouTube blocks, not media rows |
| `Project` | slug, title, description, media, links | Linked to related posts |
| `Comment` | postId, parentId, authorName, authorEmail (private), isAuthor, status (`pending`/`approved`/`rejected`/`spam`), moderation {verdict, reason, model} | Max depth 2 |
| `ReplySubscription` | email, commentId, confirmedAt, unsubscribeToken | |
| `Reaction` | targetType (post/comment), targetId, kind, visitorId | |
| `Subscriber` | email, topicIds, confirmedAt, unsubscribeToken | Newsletter |
| `ContactMessage` | name, email, kind (job/freelance/collab/other), body, moderation, status | Admin inbox |
| `AdminPasskey`, `BackupCode` | credential data; hashed codes + usedAt | |
| `PostChunk` *(wave 3)* | postId, text, embedding (pgvector) | For search, related posts and the assistant |

**Invariants (rules that must always hold)**
- A URL that was ever published returns either the post (200) or a 301 to its current URL, never a 404. A redirect may never shadow a live path.
- A post has exactly one main topic, and `(topic, slug)` is unique.
- A comment's parent must be a top-level comment (depth ≤ 2).
- Guest emails never appear in public pages or public API responses.
- Notification and newsletter emails go only to **confirmed** addresses, and bounced or complaining addresses are suppressed.
- Each visitor has at most one reaction per target.
- A scheduled post publishes **exactly once** (the job is idempotent).
- **Moderation fails closed:** if the AI call errors or times out, the comment goes to `pending`, never to `approved`.

## Edge cases & failure modes
- **OpenAI outage:** comments queue as pending and a retry job runs; the admin shows the backlog.
- **Spend cap reached:** public AI features degrade gracefully (the assistant is hidden or shows a notice; moderation falls back to manual review).
- **Neon cold start** (the free tier pauses when idle): reader pages are cached as static pages (ISR) on Vercel, so readers rarely reach the API directly.
- **Media processing failure:** the upload is marked failed and the editor offers a retry.
- **Scheduled publish failure:** BullMQ retries with backoff and reports to Sentry.
- **Leftover PR environment:** a teardown runs on PR close, plus a nightly sweep.
- **Admin lockout:** backup codes, then the SSH recovery command.
- **Impersonation in comments:** only the authenticated author gets the "Author" badge.

## Release waves
- **Wave 1 — Launch:** the reader site (home, topics, posts, About, Work with me, Projects, newsletter sign-up); the admin (Tiptap editor + custom blocks, media pipeline, drafts/scheduling, topics/tags, moderation queue, inbox, subscribers); comments with threads, reactions and reply emails; AI moderation; newsletter sending; share buttons + UTM; SEO/preview images/RSS; analytics and monitoring; CI/CD, per-PR environments and Terraform.
- **Wave 2:** editor copilot (including social drafts).
- **Wave 3:** embeddings, semantic search, related posts and "Ask my blog".

**Suggested first milestone:** a *walking skeleton*. Scaffold the monorepo, set up CI, Terraform the VPS, and deploy a hello-world web + admin + API end to end, including one per-PR preview. This proves the DevOps setup works before any features are built.

## Estimated running cost
Vercel free tier · VPS ≈ €8–15/mo · Neon free tier to start · domain ≈ $10–15/yr · Resend free tier · Sentry free tier · Umami self-hosted · OpenAI usage (pennies at launch, capped). **About $10–20/month in total.**

## Open questions (deliberately deferred)
- **Domain & handles:** check whether `poesitor.com` / `.dev` / `.io` and matching social handles are available. Optionally get a full-name domain to redirect.
- **VPS provider & region:** must be next to Neon. Neon Object Storage may be limited to certain regions (one source said `us-east-2` for new projects), so verify at setup.
- **Neon Object Storage maturity:** sources disagree on beta vs GA. Verify it before relying on it; the `StorageService` interface is the fallback.
- **OpenAI models per feature, the embedding model and its vector dimension:** choose at build time from OpenAI's lineup and pricing then.
- **Monthly AI spend cap amount** and per-visitor rate limits.
- **Initial topic list, accent colours, and the reaction set.**
- **The list of custom editor blocks for v1** (e.g. code, image, gallery, audio, YouTube, callout, link embed).
- **Weekly time budget.** There's no deadline. Draft 3–5 posts while building so the site launches with content.
