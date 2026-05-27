# Designer's Vibe Coding Workflow — Lessons from the Tillett Project

> Designers don't need to learn how to "write code" — they need to learn how to "direct AI to write code." This guide is built on real lessons from the **Tillett Homepage** Project — use it to build your own **reusable, deliverable** AI-assisted development workflow from scratch.

- Ship frontend UI independently, from build to delivery
- Cut the design-to-development handoff cycle by 50%
- Work more effectively alongside your engineering team

---

## Golden Rule

During the UI/UX design development phase, **only use Mock data — never touch business logic.** Keep UI and business logic separate: designers own the frontend interface and interactions; developers own the APIs and business logic.

---

## When to Use This

### Good fit

- UI development with clear design specs (new pages, new components, redesigns)
- Well-defined requirements where interactions and visuals are already locked
- Teams open to accepting frontend code submitted by designers

### Not a good fit

- Exploratory prototypes or concept validation (requirements not yet finalized)
- Features that are heavily logic-driven on the backend

---

## Three-Phase Workflow Overview

| Phase | Core Tasks |
|---|---|
| **1 · Preparation** | Nail down scope and which modules you own / Get the project running locally / Set up CLAUDE.md conventions |
| **2 · Development** (Core) | AI maps out a plan + technical approach / Prepare Mock data + build static UI / Polish interactions + placeholder business logic with TODOs |
| **3 · Delivery** | Pre-push self-check + push to your own branch / Write handoff documentation / Developer Review → API integration → Launch |

---

## Phase 1 · Preparation

### 1-1 Understand the Business First, Then Start Building

After receiving requirements, do these three things before touching any code:

- Read the product document (PRD) thoroughly — understand the business goal, core flow, and edge cases
- Check in with a developer to confirm the tech stack (for Tillett, this was Next.js 16 + React 19 + TypeScript + Tailwind CSS v4 + shadcn/ui, with Bun as the package manager)
- Confirm which pages / modules you're responsible for

Write down anything you're unsure about and have AI help you think through it later — don't guess on your own.

---

### 1-2 Environment Setup (First Time Only)

Things you need to install (check your project's README for the exact tools — see the [Tillett README](https://github.com/FSCO-Web/tillett_redesign/blob/development/README.md) as an example):

- **Git** — Clone the repo, create branches, push changes. [Download Git](https://git-scm.com/downloads).
- **[Bun](https://bun.sh)** — Installs packages and runs the dev server. Tillett is built around Bun — don't swap in npm or pnpm, it will break the lockfile.
- **Claude Code** — AI coding assistant
- **Cursor / VSCode** — Code editor

Ask a developer to walk you through the local setup once, and take notes on any issues you hit along the way.

---

### 1-3 Set Up the CLAUDE.md Project Context File

Create a `CLAUDE.md` file in the project root — this is your standing agreement with the AI:

```
# Project Overview

## Tech Stack
# Replace with your project's actual stack
- Framework: [e.g. Next.js 16 + React 19 + TypeScript]
- Styling: [e.g. Tailwind CSS v4]
- Component library: [e.g. shadcn/ui]
- Package manager: [e.g. Bun]
- Carousel: [e.g. embla-carousel-react]
- Icons: [e.g. lucide-react]

## File Structure
# Replace with your project's actual folder structure
- Page files: [e.g. src/app/<route>/page.tsx]
- Feature components: [e.g. src/ui/features/<feature>/]
- Shared/reusable components: [e.g. src/ui/shared/components/ui/]
- Mock data: [e.g. src/ui/features/<feature>/constants/]

## Development Conventions
- All Mock data must have TypeScript type definitions
- Business logic uses TODO comments as placeholders — do not implement yourself
- Do not modify any files under [e.g. src/api/]
- Use existing primitives (Button, Modal, Typography) before writing new ones
- Extract all UI strings and magic numbers to a constants file

## Off-limits
- Do not call real APIs
- Do not modify routing config files
- Do not modify existing business components
- Do not introduce new UI libraries — use [e.g. shadcn/ui] only
```

**Why this matters:** AI will "forget" conventions you set early in a long conversation. Writing them in a file means the AI picks them up at the start of every session — the rules never get lost.

---

### 1-4 Start the Project & Check It in the Browser

Once the environment is set up, get the project running first. During development, **every change** needs a quick check in the browser — that's the core loop.

#### Option A — Using the Claude Code tab (recommended for designers)

You don't need to touch the terminal at all. In the Tillett project, everything was done through the **Claude Code app's chat interface** — just type what you need in plain English:

```
clone the tillett_redesign repo from [repo URL] into my projects folder,
install dependencies, and start the dev server
```

Claude handles the `git clone`, `bun install`, and `bun dev` commands for you. When it's done, it'll tell you the local URL to open — usually `http://localhost:3000`.

For day-to-day use, the same applies:

| What you want | What to type |
|---|---|
| Start the dev server | `start the dev server` |
| Install new dependencies | `install dependencies` |
| Check for errors | `run lint and tell me what's broken` |
| Restart after a crash | `restart the dev server` |

#### Option B — Using the terminal directly

If you prefer to run commands yourself, or your project setup requires it:

```bash
# ① Open a terminal in the editor (Terminal panel at the bottom of Cursor / VSCode)
# Shortcut: ⌃` (Control + backtick)

# ② Install project dependencies (only needed once after cloning)
bun install

# ③ Start the dev server
bun dev
```

When you see something like this, you're good:

```
▲ Next.js 16.2.4
- Local: http://localhost:3000
- Ready in 2.1s
```

Hold `⌘ Cmd` and click the link — the browser opens automatically.

#### The dev loop: Edit → Save → Refresh

1. **AI edits the code** — changes happen in the editor
2. **Save** — `⌘S` (usually auto-saves)
3. **Browser refreshes** — switch over and see the result

Modern frontend projects support **Hot Reload** — the browser updates the moment you save, no manual refresh needed. You'll be going through this loop constantly while tweaking UI.

#### Things going wrong on startup?

| Error | Fix |
|---|---|
| `command not found: bun` | Bun isn't installed — go back to 1-2 and install it from [bun.sh](https://bun.sh) |
| `EACCES permission denied` or install failure | Copy the full error and paste it to AI: "help me fix this" |
| `Port 3000 is already in use` | Close the old terminal window and restart, or ask AI to switch ports |

**Important:** If you're using Option B, keep the terminal window open while you're working — the dev server needs to stay running. If you close it by accident, just run `bun dev` again. With Option A, Claude manages this for you.

---

## Phase 2 · Development (Core)

### 01 Have AI Map Out a Plan Before Writing Code

**Don't jump straight to asking AI to write code** — have it think things through first:

```
I'm a UI/UX designer building a [module name] page.

Here's what's in the design:
- [Describe the main page content]
- [Describe main interactions: e.g. filtering, pagination, modals]
- [Describe data display: e.g. list, cards, charts]

Before we start coding, help me:
1. Break this page down into a feature checklist
2. Figure out which components we'll need
3. Plan the build order (simple to complex)
4. Identify what data structures need to be Mocked

Note: This is static UI only — no real API calls.
```

Once you have the plan, **make sure nothing is missing** before diving in.

---

### 02 Prepare Mock Data (Critical Step)

Before writing any UI, have AI generate the Mock data first:

```
Based on the breakdown above, generate the Mock data for this page.

Requirements:
1. Define types using TypeScript (interface)
2. Generate 10+ records with varied, realistic data
3. Include edge cases (very long text, null values, special characters)
4. Place the file at src/mock/[module-name].ts
```

- **Mirrors the real API shape** — much less rework when you swap in real data later
- **Diverse data** — avoid test data being identical
- **Edge cases included** — catches UI layout problems in advance

---

### 03 Static UI Development

Work from the outside in — **overall layout first, then fill in the details**:

| Round | Goal | Description |
|---|---|---|
| Round 1 | Lay out the skeleton | Build the overall layout, fill it with Mock data, no interactions yet |
| Round 2 | Build the components | Tackle each piece one by one: filter bar, data table, modal, etc. |
| Round 3 | Layer in interactions | Expand/collapse, filtering, animations — all still using Mock data |

Check the browser after every small piece — don't wait until the whole thing is done.

**Prompt Example — Round 1:**

```
Read CLAUDE.md first.

Build the skeleton layout for the [page name] page — static structure only, no interactions in this round.

Desktop design: [Figma Link]
Mobile design: [Figma Link]

Scope:
- Page shell and section structure
- Header and footer are already in the component library, use them as-is
- Populate content with Mock data (5 records is enough for now)
- Page file goes in src/app/[route]/page.tsx
- Components go under src/[features]/[page-name]/

Constraints:
- No real API calls — Mock data only
- Check src/[shared]/ for existing components before building new ones

Before you start, briefly outline:
- What sections you'll create
- Which existing components you plan to reuse
- Anything in the design that looks ambiguous
```

> 💡 **Figma MCP required:** For Claude to read a Figma file directly from a link, you need the Figma MCP plugin connected to Claude Code. Without it, Claude can't open the design — you'll need to attach screenshots manually instead. Ask your developer to help set this up, or check the [Claude Code MCP setup guide](https://docs.anthropic.com/en/docs/claude-code/mcp).

---

### 04 Polish and Self-Check

Once the visuals and interactions are in place, ask AI to audit the code:

```
Please review the current code and fix any of the following issues you find:
1. Hardcoded strings that should be variables
2. Any leftover console.log statements
3. Unhandled TODOs
4. Responsive layout issues (mobile / wide screen)
5. Obvious performance issues (e.g. list items missing keys)
```

> 💡 **What is `console.log`?** It's a debugging line AI commonly adds while building — it prints values to the browser console so you can see what's happening internally. Harmless during development, but always clean them up before handoff: they clutter the console and signal to your engineer that the code wasn't reviewed. Think of them like sticky notes left in a final design file.

Also stress-test with edge cases:

- Set Mock data to an **empty array** — does the empty state look right?
- Use a **very long string** — does anything overflow?
- Use **0 or a negative number** — does the display break?

---

### 05 Business Logic Placeholder

Once the UI is complete, actively flag all places where real logic needs to be integrated after:

```typescript
// TODO: Fetch user list - needs to call getUserList API
// Currently: using Mock data
// Replace with: getUserList method in src/api/user.ts
```

---

## Phase 3 · Delivery

### Git concepts — quick reference for designers

You don't need to memorize Git, but knowing what these words mean makes conversations with your engineer (and with Claude) much smoother:

| Term | What it means |
|---|---|
| **Repository (repo)** | The project folder that Git tracks — think of it as the single source of truth for all the code |
| **Clone** | Downloading a copy of the repo to your computer for the first time |
| **Branch** | Your own working copy of the project where changes don't affect anyone else until you're ready |
| **Commit** | Saving a snapshot of your current changes with a short description — like hitting "save" with a label |
| **Push** | Sending your committed changes from your computer up to GitHub so others can see them |
| **Pull** | Downloading the latest changes from GitHub onto your computer |
| **Fetch** | Checking what's changed on GitHub without actually applying the changes yet |
| **Merge** | Combining two branches together — usually your engineer does this after reviewing your PR |
| **Pull Request (PR)** | A formal request to merge your branch into the main codebase — this is where your engineer reviews your work |
| **Conflict** | When two people changed the same part of the same file and Git can't figure out which version to keep — Claude can help resolve these |

---

### 3-1 Pre-Push Checklist

- [ ] No errors in the browser
- [ ] All interactive features work correctly
- [ ] No `console.log` statements left in the code
- [ ] All TODO comments are in place
- [ ] Mock data has type definitions
- [ ] File naming follows project conventions
- [ ] No off-limits files were touched

---

### 3-2 Push Your Branch

Keep each feature / page on its own branch. Naming convention:

```
feat/[your-initials]-[feature-name]-ui

Example: feat/hsf-seo-audit-ui
```

#### Option A — Using the Claude Code tab (recommended for designers)

Just describe what you want in plain English:

| What you want | What to type |
|---|---|
| Start a new branch | `create a new branch called feat/[your-initials]-[feature-name]-ui off the latest development` |
| Save your work | `commit my changes with the message "[short description]"` |
| Send it to GitHub | `push my branch to origin` |
| Open a PR | `create a pull request against development with the title "[PR title]"` |
| Get the latest changes | `fetch origin and rebase my branch onto development` |

#### Option B — Using the terminal directly

```bash
# Create and switch to a new branch
git checkout -b feat/hsf-seo-audit-ui

# See what changed
git status

# Commit everything
git add . && git commit -m "feat: seo audit page UI"

# Push to remote
git push origin feat/hsf-seo-audit-ui
```

---

### 3-3 Write a Handoff Document

Write a clear PR description so the developer knows exactly what you shipped and what's left for them:

```markdown
## What's in this PR
- Static UI for [page/feature name]
- Uses Mock data; all business logic is marked with TODOs

## What the developer needs to complete
- [ ] src/pages/UserList.tsx line 23: wire up the user list API
- [ ] src/components/UserFilter.tsx line 45: wire up the filter API
- [ ] Pagination logic (currently mocked)

## Design spec
[Figma link]

## Screenshots
[Screenshots]
```

---

### 3-4 Building Trust with Developers

Early on, developers might be skeptical about code coming from a designer. The best response is to **let the work speak for itself**:

- **Stay in your lane** — UI layer only; mark all business logic with TODOs. Don't overstep or create extra work for the developer.
- **Lead with documentation** — Every delivery comes with a clear handoff doc so the developer isn't left guessing.
- **Be proactive** — Loop the developer in for a review before launch; take their feedback and iterate.
- **Be consistent** — Nail the first few deliveries and trust follows naturally.

---

## Prompt Template Library

> Copy and use directly — replace content in `[brackets]` with your actual information

---

### Universal Opener (Use Every Session)

```
I'm a UI/UX designer using Cursor to help with frontend development.
Start by reading the CLAUDE.md in the project root — it covers the tech stack and conventions.

We're working on [feature name].

Constraints:
- All data is Mocked — no real API calls
- Business logic gets a TODO comment explaining what needs to be wired up later
- Only touch [the relevant directories/files] — leave everything else alone

Before you start: read through what I've written and flag anything that needs clarification. If it's clear, go ahead.
```

---

### Component Development

```
Help me build the [component name] component. Design spec screenshot attached.

Visual: [Key styles, e.g. card border-radius 12px, primary color #5B4CDB]
Interactions: [hover effects, click-to-expand, loading states, etc.]
States needed: [empty / loading / default / error]

Data: pull from Mock data at src/mock/[filename].ts
Constraints: no real API calls; stub business logic with TODOs
```

---

### Bug Fix

```
Something's wrong with [component name / file path].

What I'm seeing: [describe the current behavior]
What it should do: [describe the expected behavior]
Error output: [paste the full error here if there is one]

Only touch the files related to this issue — don't change other components or pages.
Once it's fixed, tell me what you changed and why.
```

---

### Code Explanation

```
Explain what this code does in plain language.
Specifically: how does this logic affect what the user sees?

[Paste code here]
```

---

### Design Spec Comparison (Use Often)

```
Here's the design spec (attached) and here's what the page looks like now.
Go through these one by one and fix anything that's off:

1. Spacing and dimensions — do they match the spec?
2. Font size, weight, and color
3. Details: border-radius, shadows, borders
4. Alignment

List every change you make.
```

---

### Responsive Adaptation (Use Often)

```
[Page / component name] only works on desktop right now.
Add mobile support (breakpoint: [768px / 1024px]):

1. [Describe the mobile layout, e.g.: sidebar becomes a bottom tab bar]
2. Tables on small screens switch to a card layout
3. Keep all functionality — only adjust layout and type sizes

After making changes, check it at both 375px and 768px.
```

---

### Handoff Documentation Generator (Required for Every Delivery)

```
I've finished the static UI for [page / feature name] and need a handoff doc for the developer.

Please put together the following:

1. Everything delivered — a list of all new and modified files
2. All TODO placeholders — file path, line number, and what needs to be wired up
3. A mapping from Mock data fields to the expected real API fields
4. Any edge cases or special behavior the developer should know about
5. Notes on which pages/states are most important to verify

Output as Markdown, ready to drop straight into the PR description.
```

---

## Rolling This Out Internally

Before pitching this to the team, decide which mode fits where you are right now:

| Mode | Description | Difficulty |
|---|---|---|
| **Mode A · Collaborative** (Start here) | Design spec → generate frontend code → hand off to developer for review / integration | Lower risk — good starting point |
| **Mode B · Ownership** | Designer fully owns the frontend delivery for specific pages / features | Higher stakes — build trust first |

**Recommendation: Start with A, prove it works, then gradually move toward B.**

---

### 01 Find a Low-Risk First Project

Pick something where the cost of failure is low:

- **Internal tools, one-off campaign pages, or non-critical pages** make the best test cases
- Don't start on the main product

---

### 02 Get One Developer on Your Side

Don't try to work around developers — **bring one along from the start.** That's much easier than convincing the whole team at once:

- **What they get** — Less time writing boilerplate UI; they just review and integrate
- **What you get** — A technical ally who makes the next conversation with the team much easier

---

### 03 Show the Numbers

Concrete data lands better than a pitch deck. Track the time difference:

| | Time | Flow |
|---|---|---|
| Before | ~2 weeks | Design spec → backlog → developer writes UI |
| Now | ~3 days | Designer ships usable UI code directly |

---

## FAQ

**The code AI generated has errors — what do I do?**
Paste the **full error output** to AI and ask it to fix it — don't try to guess the cause. AI can usually pinpoint the problem from the error alone.

---

**AI changed a file I told it not to touch — what do I do?**
Add "do not modify xxx" to CLAUDE.md, and mention it again at the start of your next session. You can always use Git to revert anything that got changed by accident.

---

**The generated UI looks nothing like the design spec — what do I do?**
Take a screenshot of both side by side and tell AI what's off: "here's the design spec, here's what it looks like now — fix these specific things." A screenshot beats a paragraph of description every time.

---

**How do I get AI to produce UI that feels on-brand?**

The key is turning "our style" into rules AI can actually follow, rather than re-describing it in every prompt. Lock down four things in `CLAUDE.md` or `.cursor/rules/`:

1. **Set a visual reference** — Write a line describing the product's feel, like "dense and focused like Linear" or "spacious and minimal like Notion." That alone shifts AI's default output noticeably.
2. **Lock down Design Tokens** — Require that all colors, spacing, and font sizes come from Design Tokens (CSS variables / theme variables) — no hardcoded values. Keeps AI from improvising with the brand palette.
3. **Enforce the component library** — Make it explicit: "**don't bring in new UI libraries — use the existing xxx** (shadcn/ui, Ant Design, whatever the project uses)." Always check what already exists before building something new.
4. **Ban inline styles** — Except for dynamically computed values, all styles go through Tailwind / CSS Modules / the theme system. Keeps everything consistent and easy to change later.

Short version: **constrain the prompt, define the rules.** Give AI a sandbox, not a blank canvas.

---

**AI starts going off the rails in long conversations — what do I do?**
Open a fresh conversation, re-paste the CLAUDE.md content and a quick summary of where you are, and keep going. Long context is the main reason output quality slips.

---

**I don't know how to implement something — what do I do?**
Ask AI "what are the different ways to implement this, and what are the trade-offs?" Pick one, then ask it to build. Don't guess — use AI as your technical sounding board.

---

**I'm worried the developer will reject my code — what do I do?**
You're delivering a **static UI layer** — you're not making architecture decisions. If you: ① follow the project's file and naming conventions; ② mark all business logic with TODOs; ③ include a handoff doc — it's easy for a developer to pick up where you left off. For the first couple of PRs, invite the developer to review and adjust based on their feedback. Trust comes quickly once they see it works.

---

**Can I do this without knowing Git?**
Yes. You really only need 4 commands: `checkout -b` (new branch), `status` (see what changed), `add + commit` (save your work), `push` (send it up). When in doubt, just tell AI "commit and push my code" — it'll take care of it.

---

**What are the common pitfalls when rolling this out, and how do I handle them?**

- **Developer pushes back on code quality:** Frame it as "this is a first draft — help me see what needs work," not "here's my code."
- **Developer feels like you're stepping on their territory:** Focus on "taking repetitive work off your plate," not "doing your job."
- **You take on a delivery and get blamed for bugs:** Set clear acceptance criteria with the PM and developer upfront.
- **AI keeps forgetting your conventions:** Put them in CLAUDE.md — don't rely on saying it in the chat.

---

## Recommended Tools

| Tool | What it's for |
|---|---|
| **Claude Code** | Primary AI coding tool |
| **Cursor** | Editor with built-in AI |
| **Figma** | Design source files |
| **shadcn/ui** | Component library that works well with AI |
| **Tailwind CSS** | The CSS framework AI handles most reliably |
| **Node.js** | JavaScript runtime |
| **Git + GitHub** | Branch management and code delivery |
| **TypeScript** | Keeps your Mock data structured and reliable |

---

*Made with Claude — Junyu Shen*
*Designers don't need to learn how to write code — they need to learn how to direct AI to write code.*
