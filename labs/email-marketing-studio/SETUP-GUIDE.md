# Email Marketing Studio — Setup & Deployment Guide

My own record of how I set up the Email Marketing Studio lab with Claude Code on Windows,
so I can repeat it on any computer.

- **Starter repo:** https://github.com/mjunaidca/email-marketing-studio (README + .gitignore only)
- **My machine:** Windows, PowerShell, project folder `E:\FDE\my-email-studio`
- **Tools used:** Node.js 22+, Git for Windows 2.56, Claude Code v2.1.220 (Claude Pro, Sonnet 5)
- **Last done:** 27 October 2026

---

## Part 1 — Install the tools (one time per computer)

Open **PowerShell** and check each tool:

```powershell
node --version     # must be v22 or higher
git --version      # any version
claude --version   # any version
```

| If this is missing | Do this |
|---|---|
| Node.js | Install the **LTS** version from https://nodejs.org |
| Git | `winget install --id Git.Git -e --source winget` (or download from https://git-scm.com/download/win, keep all defaults) |
| Claude Code | `npm install -g @anthropic-ai/claude-code` |

> **Important:** after installing anything, **close the terminal and open a new one**.
> An old window does not see newly installed programs.

### Problem I hit: `git : The term 'git' is not recognized`

- **Cause:** Git was not installed. Claude Code on Windows needs Git (it uses Git Bash).
- **Fix:** install Git (table above), then **close and reopen** PowerShell, then run `git --version` again.
- **Still failing?** Check `Test-Path "C:\Program Files\Git\cmd\git.exe"`. If it prints `True`,
  Git is installed but not on PATH — reinstall and keep the option
  "Git from the command line and also from 3rd-party software".

---

## Part 2 — Create the studio folder

Every lab gets **its own folder**. Do not run Claude Code in a general folder like `E:\FDE`.

```powershell
cd E:\FDE
mkdir my-email-studio
cd my-email-studio
git init
claude
```

- `git init` should print: `Initialized empty Git repository in E:/FDE/my-email-studio/.git/`
- The first time `claude` runs, sign in (or type `/login`).
- The welcome box must show **`E:\FDE\my-email-studio`**. If it shows another folder,
  type `/exit`, `cd` into the right folder, and run `claude` again.

**Check inside Claude Code:**

```text
Am I in E:\FDE\my-email-studio? If yes, confirm it is a git repository.
```

> Tip: you can ask Claude Code in plain English to run commands for you
> (for example, typing `git version` made Claude run it and report the result).

---

## Part 3 — Step 1: find skills others have made (before building)

Paste this prompt into Claude Code:

```text
I'm setting up an email marketing studio in this folder. Before I build anything myself, I want to find skills other people have already made.

1. Search the skills directory at skills.sh by running these one at a time:
   npx skills find email marketing
   npx skills find email sequence
   npx skills find campaign plan
   npx skills find resend
2. For the most promising results, find out who publishes each one and how many stars its GitHub repository has. Look out for official publishers such as anthropics and resend.
3. Write notes/skills-i-chose.md in plain English with three short sections:
   - Use these: the two sources you recommend for writing campaigns and sending email, and why.
   - Avoid these: two results you would not trust, and why.
   - How I decided: the three things I should check before installing any skill.
```

- Approve each `npx skills find ...` command — they only **search**, nothing is installed.
- If npx asks `Ok to proceed? (y)`, type `y`.
- Approve creating `notes/skills-i-chose.md`, then check it with: `Show me notes/skills-i-chose.md`

**Before installing ANY skill, check these three things:**
1. **Publisher** — official (`anthropics`, `resend`) beats an unknown account.
2. **Trust** — GitHub stars and recent updates.
3. **Contents** — read its `SKILL.md`; flag anything that contacts outside servers or handles keys.

---

## Part 4 — The finished studio (target)

```text
my-email-studio/
├── CLAUDE.md                       the studio's rules (loaded every session)
├── brand/voice.md                  how the brand sounds
├── leads/
│   ├── leads.csv                   24 practice customers (made-up data only)
│   └── CLAUDE.md                   rules for this folder only
├── campaigns/steep-club-launch/
│   ├── plan.md                     the campaign plan
│   ├── emails/                     4 launch emails
│   └── groups/                     a version per customer group
├── .claude/
│   ├── rules/emails.md             rules for every email file
│   ├── skills/personalize/         skill: personalize per customer group
│   └── hooks/send-guard.mjs        hook: blocks sending without approval
├── .mcp.json                       connector (MCP) to Resend, which sends email
└── scripts/review.mjs              automatic review
```

| Piece | Claude Code feature it teaches |
|---|---|
| `CLAUDE.md` | Project memory — standing rules for every session |
| `leads/CLAUDE.md` | Folder-scoped rules — load only inside that folder |
| `.claude/rules/emails.md` | Path-scoped rules — switch on for email files |
| `.claude/skills/personalize/` | Skill — a repeatable procedure that fires on its own |
| `.claude/hooks/send-guard.mjs` | Hook — code that always runs and can block an action |
| `.mcp.json` | Connector (MCP) — gives Claude "hands" to send email via Resend |
| `scripts/review.mjs` | Automatic review — runs without me |

**Three lessons in this structure**
1. Rules come in layers: project → folder → file type. Each loads only where needed.
2. Skill + connector are partners: the skill decides *how* to write, the connector *sends*.
3. Safety rises with power: once Claude can send real email, a hook guards the send button.

---

## Part 5 — Steps still to do

- [x] Install Node, Git, Claude Code
- [x] Create `my-email-studio` and `git init`
- [ ] Step 1 — find skills, write `notes/skills-i-chose.md`
- [ ] Later steps — add them here as I do them

---

## Quick troubleshooting

| Symptom | Fix |
|---|---|
| `git` / `node` / `claude` "not recognized" | Install it, then close and reopen the terminal |
| Claude Code opened in the wrong folder | `/exit`, `cd` to the project folder, `claude` |
| Need to sign in again | Type `/login` inside Claude Code |
| A command wants approval | Read it first; approve searches and reads, think twice before installs or sends |
| Secrets (API keys) | Put them in `.env` — it is git-ignored; share only `.env.example` |
