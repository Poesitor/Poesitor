# Coaching profile: Poesitor

## Learner
- **Goal:** Build Poesitor (the destination is [plans/poesitor-blog.md](plans/poesitor-blog.md)) entirely by hand, and come out able to build and ship full-stack TypeScript apps alone: front end, back end, data, DevOps and AI engineering, with no AI writing the code. Get a usable, live site as early as possible, then upgrade it in place.
- **Starting point (2026-11-23):**
  - Comfortable with JS/TS basics, HTML/CSS, Git/GitHub, the terminal and HTTP.
  - *Not yet* confident building React/Next.js, NestJS, SQL/ORM, automated tests, Docker, CI/CD, or servers/Terraform without a tutorial.
  - Has used LLM APIs only through AI-written code.
- **Tone:** blunt. Harsh on the work, never on the person.
- **Cadence:** 3–4 sessions a week, about 90 minutes each. Size each task to fit one session and end with something working.
- **Stuck limit:** 20 minutes without progress before asking.
- **Constraint:** a low-end Windows laptop that can't run Docker. Containers get built and run in GitHub Actions, GitHub Codespaces, or on the VPS.

## Curriculum
- **Plan:** [curriculum.md](curriculum.md) (the destination is [plans/poesitor-blog.md](plans/poesitor-blog.md))
- **Chapter rhythm:**
  1. Tasks, each passing `review`. The last task of each chapter ships the upgrade to production.
  2. The chapter's done-when list passes.
  3. Explain test (`/coach test`).
  4. A build-log post published on Poesitor: what you built, what broke, what you learned. It's written only after the test passes, so you write about what you can actually explain.
  5. Coaching retro: what helped, what got in the way, what to change.
- **Mode tendencies:**
  - *Try-first:* React components and pages, CRUD endpoints, data modelling, choosing test cases, CI steps, UI features (comments, reactions, sharing), scripts, debugging.
  - *Explain-first:* SQL constraints and joins (first time), migrations, NestJS dependency injection, Docker images and networking, Linux server hardening, TLS and reverse proxies, session security and WebAuthn, the ProseMirror document model, queues and idempotency, SPF/DKIM/DMARC, LLM fundamentals and structured output, eval methodology, Terraform state, embeddings and RAG.

## Code rule
- **The learner types all project code**: app code, tests, config, SQL, scripts, prompts for Poesitor's AI features, and every DevOps file (Dockerfiles, CI workflows, Compose, Traefik, Terraform, cloud-init).
- **Allowed sources:** official generators and CLIs (`create-next-app`, `nest new`, `drizzle-kit`, …) and copying from official docs or READMEs. **Every generated or copied line must be explainable in review.** Anything that isn't gets studied before the task passes.
- **AI:** Claude only coaches: no solution code, no debugging for the learner, help only through the hint ladder. **AI autocomplete and inline suggestions (Copilot etc.) stay off in this workspace.** No other AI tool writes code for this repo.
- **Break glass:** only when the learner says "break glass". Claude gives the smallest fragment (a few lines) and explains every line. At most **one per task card**. It's logged in progress, rewritten from memory at the start of the next session, and becomes an explain-test question.

## Paths
- **Claude may write:** `docs/coaching.md`, `docs/curriculum.md`, `docs/plans/**`, `.coach/**`, and the "Coaching" block in `CLAUDE.md`.
- Everything else belongs to the learner.

## Explain test
- **Format:** 5 questions with no notes, no AI and no code open, mixing how-it-works / why-this-choice / what-breaks-if / predict-the-output / compare-with-library. Then **one live change** in 20 minutes with no AI.
- **Pass bar:** at least 4 of 5 acceptable, with at least 3 fully right, **and** the live change works with a test the learner wrote passing.
- **On a fail:** retake no sooner than **2 days** later, with new questions on the failed topics only. Meanwhile the learner may start the next chapter's first task, but may not take the next chapter's explain test or publish the failed chapter's build-log post.

## Recall
- Warm-up: 2–3 questions per session. Flashcards: no.

## Domain review checks
- **Tests:** the learner wrote tests for the new behaviour and wrote a prediction before running them.
- **Types & boundaries:** no `any` and no unchecked casts. Input is validated where it enters the system (request bodies, frontmatter, env vars, LLM output).
- **Secrets:** nothing secret in code, commits or client bundles. The OpenAI key and database URLs stay server-side.
- **Plan invariants** (see the plan):
  - previously published URLs never 404
  - one main topic per post
  - comment depth ≤ 2
  - guest emails never leave the API
  - mail goes only to confirmed addresses
  - one reaction per visitor per target
  - scheduled posts publish exactly once
  - moderation fails closed
- **Failure paths:** what happens when the network, database, email or AI call fails? The happy path alone doesn't pass.
- **Security basics:** auth on every admin route, XSS-safe rendering of posts and comments, rate limits on public write endpoints.
- **Accessibility basics:** semantic HTML, alt text, labels, keyboard focus, contrast.
- **Git hygiene:** small focused commits, conventional commit messages, a PR with a description (from Ch 2 on).
- **Code rule:** the learner can explain every generated or copied line.

## Coaching changes
- 2026-11-23: Profile created through `setup`, from the Poesitor design interview.
