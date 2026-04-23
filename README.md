# Dira

**A terminal-native AI co-pilot for solo developers working across multiple repositories.**

Dira answers one question at the start of every session:

> *"Where am I, and what matters right now?"*

And one question once a week:

> *"What's the big picture across all my projects?"*

---

## The problem

You're a solo builder shipping multiple SaaS products. You work in bursts — deep sessions followed by days away. Every time you come back, you spend 10–15 minutes just remembering where you were. Your PRDs drift from reality. Plan changes happen silently with no record of why. Linear and Jira were built for teams, not for you.

Dira fixes that.

---

## How it works

Dira is a standalone CLI tool that lives outside your repos. It reads your git history, your PRDs, and a lightweight decision log to generate a focused session brief — in 60 seconds, every time you start working.

It never guesses silently. Everything is markdown files in git. No black boxes, no external services, no background processes.

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
 DIRA BRIEF — SAAS-A — 23 Apr 2026
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

WHERE YOU LEFT OFF
You were working on the email notification system. Last 3 commits
touched /src/notifications and /src/templates. The send logic is
in place but delivery tracking appears incomplete.

OPEN DECISIONS
→ Webhook retry strategy: started in /src/webhooks but no commits
  in 8 days. Unresolved.

PRD DIVERGENCE — ACTION NEEDED
Feature: Payment webhooks — no commits in 23 days

  A  Still planned, just delayed
  B  Descoped — remove from PRD
  C  It's in a different module
  D  Other (type a note)

Your choice:

FEATURE TIMING
→ Email notifications    active     today    6 commits
→ Billing integration    active     11d      14 commits
→ Payment webhooks       drifting   23d      4 commits  ↓ may be stuck

FOCUS THIS SESSION
  (from last week) Complete email notification delivery tracking
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
```

---

## Install

```bash
npm install -g @kenorekader/dira
```

Requires Node.js 20+ and an `ANTHROPIC_API_KEY` environment variable.

```bash
export ANTHROPIC_API_KEY=your_key_here
```

---

## Commands

```bash
dira init                          # First-time setup — register your projects
dira bootstrap <project>           # One-time per project — build the feature map
dira brief <project>               # Start of every session — get your brief
dira log <project> "your note"     # Mid-session — capture a plan change
dira digest                        # Once a week — big picture across all projects
```

### `dira init`
Registers your projects — name, local repo path, PRD file location. Writes a config file to `~/.dira/`. Run once.

### `dira bootstrap <project>`
Reads your PRD, proposes a feature map (PRD features → codebase modules), and asks you to confirm. Produces `feature-map.md` and `baseline.md` for that project. Run once per project, takes 20–30 minutes.

### `dira brief <project>`
The core command. Reads git history since your last brief, cross-references your feature map, flags any PRD divergences, and calls Claude to synthesize a session brief. Takes 60 seconds.

### `dira log <project> "note"`
One-liner to capture a plan change mid-session. No AI call — pure append to `decisions.md`.

```bash
dira log saas-a "dropped Stripe in favour of Lemon Squeezy — simpler tax handling"
```

### `dira digest`
Cross-project weekly summary. Shows what shipped, what's active, what's drifting, and your velocity trend. Ends with a prompt to set next week's focus per project.

---

## Data model

Everything lives in a standalone directory — transparent, version-controlled, no database.

```
~/.dira/
  config.json          ← project registry
  projects/
    saas-a/
      feature-map.md   ← PRD features → codebase modules + timing
      decisions.md     ← timestamped change log with reasoning
      baseline.md      ← one-time project state snapshot
      focus.md         ← next week's declared priority
    saas-b/
      ...
```

**Feature map** tracks elapsed time and commit count per feature — inferred from git, confirmed by you.

**Decision log** captures why things changed, not just what:
```
[2026-04-21] DECIDED: dropped Stripe in favour of Lemon Squeezy
BECAUSE: simpler tax handling for solo SaaS
AFFECTS: billing module, onboarding flow
```

---

## Principles

- **Terminal-first** — lives where you already are, no browser tab
- **Transparent** — everything is markdown in git, fully auditable
- **Git as source of truth** — progress inferred from commits, not manual updates
- **Developer confirms, AI proposes** — Dira never silently mutates your project state
- **No external dependencies** — no Linear, no Notion, no MCP integrations
- **Lightweight** — a 60-second ritual, not a process

---

## Tech stack

| Component | Choice |
|---|---|
| Language | TypeScript |
| CLI framework | Commander.js |
| Interactive prompts | Inquirer.js |
| Terminal styling | Chalk + Ora |
| AI | Anthropic Claude API (Sonnet 4.6) |
| Storage | Markdown files in git |
| Git data | `gather.ts` via `child_process` |

---

## Status

🚧 **Building in public.** Dira is under active development. Core commands are being built in milestone order — scaffold → data layer → bootstrap → brief → log → digest.

Follow along or contribute — PRs welcome.

---

## License

MIT — see [LICENSE](LICENSE)

---

*Dira means "compass" in Swahili.*
