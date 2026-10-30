# Email Marketing Studio — Setup & Deployment Guide

My own record of how I set up the Email Marketing Studio lab with Claude Code on Windows,
so I can repeat it on any computer.

- **Full lab text (all 15 steps, every BUILD and CHECK prompt):** [LAB-STEPS.md](LAB-STEPS.md)
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
| GitHub CLI (`gh`) | `winget install --id GitHub.cli -e`, reopen terminal, then `gh auth login` (GitHub.com → HTTPS → Yes → browser) and check `gh auth status` |

> **Important:** after installing anything, **close the terminal and open a new one**.
> An old window does not see newly installed programs.

### Problem I hit: `git : The term 'git' is not recognized`

- **Cause:** Git was not installed. Claude Code on Windows needs Git (it uses Git Bash).
- **Fix:** install Git (table above), then **close and reopen** PowerShell, then run `git --version` again.
- **Still failing?** Check `Test-Path "C:\Program Files\Git\cmd\git.exe"`. If it prints `True`,
  Git is installed but not on PATH — reinstall and keep the option
  "Git from the command line and also from 3rd-party software".


### GitHub setup (done 29 Oct 2026)

- **Claude GitHub App** — installed on my account, *Only select repositories* → `rashid200gb/FDE-102`.
  Add a repo here only when a lab step needs it; never switch to "All repositories". Don't touch the Danger zone.
- **GitHub CLI** v2.103.3 — installed, logged in as `rashid200gb` (HTTPS).
- Needed later for `/install-github-app` (Claude GitHub Actions, likely Step 13). That command also adds a
  `CLAUDE_CODE_OAUTH_TOKEN` secret and workflow files — only run it when the lab says so.

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

## Part 3 — Step 1 "Look before you build": find skills others have made

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

Don't install anything yet.
```

> In plain words: a skill is like an app for Claude. Before you build one, look in the app store.
> Download counts can be faked, so also check who made it.

- Approve each `npx skills find ...` command — they only **search**, nothing is installed.
- If npx asks `Ok to proceed? (y)`, type `y`.
- Approve creating `notes/skills-i-chose.md`, then check it with: `Show me notes/skills-i-chose.md`

**Expected result (`notes/skills-i-chose.md`):**
- *Use these* — writing: `anthropics/knowledge-work-plugins` (`campaign-plan`, `email-sequence`), official, ~32.9K stars;
  sending: `resend/resend-skills` (`resend`, `resend-cli`, plus `email-best-practices`, `react-email`), Resend's official repo.
  Runner-up: `coreyhaines31/marketingskills` (popular, but one person's collection).
- *Avoid these* — `caffeinelabs/skills` and `mbfinotti/sales-skills`: tens of thousands of installs but only 2–3 stars,
  new or unknown publishers — a sign install counts may be inflated.
- *How I decided* — who publishes it; do GitHub stars match the install count; what its `SKILL.md` tells the agent to do.

**Before installing ANY skill, check these three things:**
1. **Publisher** — official (`anthropics`, `resend`) beats an unknown account.
2. **Trust** — GitHub stars and recent updates.
3. **Contents** — read its `SKILL.md`; flag anything that contacts outside servers or handles keys.

---

## Part 3b — Step 2 "Set up your studio"

Paste this into Claude Code (or click **Copy** on the lab page):

```text
Let's set up my email studio in this folder.

1. Install Anthropic's official marketing plugin for this project only, by running these two commands:
   claude plugin marketplace add anthropics/knowledge-work-plugins --scope project
   claude plugin install marketing@knowledge-work-plugins --scope project
2. Create four folders: brand, leads, campaigns and notes.
3. Write a CLAUDE.md for the studio in plain English. Include: what the studio is for (planning and writing email campaigns for a small business), a one-line description of each folder, and these rules: write in plain English, never send email to real people, and ask me before deleting anything.
4. Create a .gitignore that ignores .claude/settings.local.json and system files like .DS_Store.
5. Commit everything with a clear message.

Then tell me what I have to do so the plugin loads.
```

**Before running it (fresh Git install only)** — Git must know who I am, or the commit fails with
"Author identity unknown". Run once in PowerShell:

```powershell
git config --global user.name "Rashid Mahmood Sajid"
git config --global user.email "rashid200gb@gmail.com"
```

**What it builds**
- Plugin installed at **project scope**: recorded in this project's settings, so anyone who clones the studio gets it — not installed for every project on my laptop.
- Folders `brand/`, `leads/`, `campaigns/`, `notes/` (Git does not track empty folders, so Claude may add a small placeholder file in each).
- `CLAUDE.md` — the studio's rules, read every session.
- `.gitignore` — keeps `.claude/settings.local.json` (my personal settings) and system junk out of Git.
- One Git commit.

**What I saw when I did it (28 Oct 2026)**
- `marketing` plugin v1.2.0 installed with 8 skills: `campaign-plan`, `email-sequence`, `draft-content`,
  `content-creation`, `brand-review`, `competitive-brief`, `performance-report`, `seo-audit`.
  For this studio: `campaign-plan` + `email-sequence` (writing) and `brand-review` (check before sending).
- Skills only load in a **new session**: `/exit`, then `claude` again.
- Warning "18 MCP servers need authentication" = connectors bundled with the plugin (Slack etc.).
  **Do not authenticate them** — not needed; choose *Disable* or ignore. Only Resend is used, at Step 9.
- Permission prompt "Read outside the working directories" (Claude reading `C:\Users\XAD\.claude\plugins`):
  choose **"Yes, but ask again next time"** — never the blanket "keep allowing any reads".
- `/plugin` opens on *Discover* (all plugins); use the **Installed** tab to check mine.

**Claude's answer on loading the plugin:** nothing extra in the same session once skills show in its list.
In general: restart Claude Code in the folder (plugins load at session start); say yes to trusting the folder /
installing the knowledge-work-plugins marketplace; check with `/plugin` (marketing listed and enabled) or type
`/marketing:` to see its commands. Anyone who copies the folder gets the same prompt, because the setting is saved in the project.

**To make the plugin load:** do what Claude says — usually `/exit`, then start `claude` again in the same folder,
and approve the plugin/marketplace prompt. Check with `/plugin` that `marketing` is listed.

## Part 3c — Step 3 "Your brand voice"

**How every step works:** 1 BUILD (paste prompt) → 2 FRESH (`/exit`, then `claude`) → 3 CHECK (paste the check prompt; a fresh Claude tests work it didn't build) → 4 DONE (answer its questions, tick Done).

BUILD prompt:

```text
Interview me about my brand so you can write our brand voice guide. Ask one question at a time and wait for my answer. Ask at most six questions.

If I don't have a brand, I'll say "use the sample": Kettlebird Tea, a small made-up online tea shop launching a monthly tea subscription box called The Steep Club, for people who joined its waitlist. Even with the sample, still ask me every question. I'll make up the answers, because practising the interview is the point.

When you have enough, write brand/voice.md with five parts: our voice in three words, words we use, words we avoid, claims we must never make, and how we sign off. Then add the line @brand/voice.md to CLAUDE.md so it loads in every session.
```

**Lesson learned:** the first time, Claude "played both sides" and answered the six questions itself. The lab wants ME to answer — I asked it to redo the interview one question at a time.

**My answers (Kettlebird sample):** two-person loose-leaf shop, ~1,200 waitlist; voice = warm, knowledgeable never showy, quietly funny;
use steep/brew/cup/loose-leaf/"Steepers"; avoid elevate, curated, indulge, journey, game-changer, exclusive, luxury, superfood, detox, artisanal;
no ALL CAPS, max one "!" per email, no emoji in subject lines; never: health claims, organic/fair trade unless certified,
"best in the world"/comparisons, fake urgency, farm visits; sign-off "Happy steeping, Mei & Tom, Kettlebird Tea" + P.S. brewing tip.

**Result:** commit `1c8ecd6` "Add Kettlebird Tea brand voice guide and load it from CLAUDE.md".
**Test it loads:** in a fresh session ask "Without opening any file, how should Kettlebird emails sign off, and which words must we avoid?" — it answered correctly from CLAUDE.md → @brand/voice.md (exam topic 3.1, @import).

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

## Part 5 — Lab progress (Email Studio Lab, 15 steps)

**Setup**
- [x] Install Node, Git, Claude Code
- [x] Create `my-email-studio` and `git init`

**Part 1 · Plan and write**
- [x] 1. Look before you build
- [x] 2. Set up your studio (28 Oct 2026)
- [x] 3. Your brand voice (commit 1c8ecd6; CHECK prompt still to run)
- [ ] 4. Plan the campaign
- [ ] 5. Practice leads
- [ ] 6. Email rule and sequence
- [ ] 7. Fresh-eyes review
- [ ] 8. Your own skill

**Part 2 · Send safely**
- [ ] 9. Connect Resend
- [ ] 10. The safety guard
- [ ] 11. Practice launch

**Part 3 · Work like a team**
- [ ] 12. Resume and fork
- [ ] 13. Automatic review
- [ ] 14. Package your studio kit
- [ ] 15. Stretch — What customers love

---

## Words you'll see (lab glossary)

| Word | Meaning | In my studio |
|---|---|---|
| Session | One conversation with Claude Code. `/exit` ends it; `claude` starts a new one | Every BUILD → FRESH → CHECK loop |
| Commit | A saved snapshot of files in Git; you can always go back | e.g. `1c8ecd6` brand voice |
| Plugin | A package of skills, commands, hooks and connections installed in one go | `marketing` plugin (8 skills) |
| Skill | Saved instructions Claude follows when called with `/name` or when the task fits | `campaign-plan`, `email-sequence`, my `personalize` |
| MCP server | A connection that gives Claude tools from another service | Resend, in `.mcp.json` (Step 9) |
| Hook | A small script Claude Code runs at a set moment (e.g. just before a tool runs); it can block the action | `send-guard.mjs` (Step 10) |
| Practice mode | Resend's tools connected to a pretend email server on my own computer | Step 11 practice launch — no real email leaves |
| Fresh session | A new conversation that doesn't remember the old one — a fair checker | The CHECK part of every step |

---

## Quick troubleshooting

| Symptom | Fix |
|---|---|
| `git` / `node` / `claude` "not recognized" | Install it, then close and reopen the terminal |
| Claude Code opened in the wrong folder | `/exit`, `cd` to the project folder, `claude` |
| Need to sign in again | Type `/login` inside Claude Code |
| A command wants approval | Read it first; approve searches and reads, think twice before installs or sends |
| Secrets (API keys) | Put them in `.env` — it is git-ignored; share only `.env.example` |
