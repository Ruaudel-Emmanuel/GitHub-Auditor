# GitHub Auditor — Your Daily Code Review, Automated

Behind this project: I'm a Digital Transformation Director for French SMEs — I help business owners save 5–10 hours a week by automating repetitive tasks (n8n · Python · on-premise AI). Contact: ruaudel.emmanuel@orange.fr ·  rennesdev.fr

> An n8n workflow that **analyzes all your GitHub repositories every morning** using Mistral Cloud, and delivers a friendly, actionable report to Telegram.  
> **Only repo metadata is sent to the AI — never your source code.**

---

## 🎯 Why GitHub Auditor?

### The Problem

You manage **multiple projects** — some personal, some professional. Keeping up is hard:
- Reviewing code quality across **all your repos** means opening each one manually
- **Stale issues and PRs** pile up without you noticing
- Getting a **daily overview** costs 30+ minutes of context-switching

### The Solution

**GitHub Auditor** sends you a **single AI-powered report every morning at 06:00** covering **all your active projects** in one batched analysis:

| Before (manual) | After (GitHub Auditor) |
|---|---|
| 🕐 30 min checking everything | ☕ 0 min — it's done automatically |
| 🔍 You miss half your repos | 📋 100 % of repos reviewed |
| 😤 Critique = chore | ✨ Constructive feedback, daily |
| 📊 No history | 📈 Daily report + trends |

---

## ✨ What Makes It Unique

| Feature | Benefit |
|---|---|
| 🔒 **Code never sent to the AI** | Only metadata (name, description, language, topics, last push, open issues) goes to Mistral — **no source code** |
| ⚡ **Batched analysis** | All repos analyzed **in a single LLM call** (18x faster than one-by-one) |
| 🤝 **"Collaborative Architect" tone** | Not a cold robot — an **enthusiastic engineering peer** who suggests, not judges |
| 📱 **Delivered to Telegram** | Read your report first thing in the morning, no n8n login needed |
| 🔧 **Zero maintenance** | Daily cron, one-shot SQL deployment |
| 🏆 **Top 3 of the day** | Each report highlights the **most promising projects** right now |

---

## 👥 Who Is This For?

| Audience | Main Benefit |
|---|---|
| **🧑‍💻 Solo dev / indie hacker (5–20 repos)** | Bird's-eye view without burning your morning |
| **🏢 CTO / lead dev (small team)** | Code quality oversight without micromanaging |
| **🔧 Open‑source maintainer** | Keep multiple projects healthy, none left behind |
| **🏠 Self-hoster / homelabber** | Runs on your own n8n VPS — you keep full control of the pipeline |

---

## 🧠 The System Prompt: "Collaborative Architect"

The AI acts as a **Staff Engineer** who reviews your projects across 4 dimensions:

| Dimension | What It Looks At |
|---|---|
| ✨ **Observations** | Promising projects, good practices, strong points |
| 💡 **Improvement areas** | Missing issues, potential bugs, security gaps |
| 🛠 **Next step** | Concrete action: review a PR, refactor a file, write docs |
| ✅ **Summary + 🏆 Top 3** | Ranking of the most exciting projects right now |

---

## 🏗️ n8n Architecture (6 nodes, ~5 min setup)

```
⏰ Daily cron (06:00)
   ↓
📡 GitHub API → List all your repos
   ↓
🧠 Single Mistral Cloud call (mistral-small-latest) → batched analysis
   ↓
📱 Telegram → Report delivered to your phone
```

---

## 🚀 Getting Started

### Prerequisites
- **n8n** ≥ 2.41 (running instance)
- **A Mistral API key** (`MISTRAL_API_KEY`) with access to `mistral-small-latest`
- **A GitHub PAT** (token) with `repo` + `read:user` scopes
- **A Telegram bot** (token stored as n8n credential)

### Deployment (5 minutes)

1. **Download** [`workflow.json`](./workflow.json)
2. **Import** it into n8n (via UI or the SQL method described in the file)
3. **Set credentials**: `GITHUB_PAT` as env variable, Telegram token in n8n credential
4. **Activate** the workflow — done.

> ⚠️ The GitHub PAT is read via `$env.GITHUB_PAT` — no hardcoded secrets.

---

## 🔒 Security & Privacy

- ✅ **No source code is ever sent to the AI** — only repo metadata (name, description, language, topics, last push date, open issue count)
- ✅ One batched LLM call per day to **Mistral Cloud** (`mistral-small-latest`)
- ✅ GitHub PAT read from env (`$env.GITHUB_PAT`), Telegram token stored encrypted in n8n
- ✅ Each report is committed to this repo (`agent-github/audit-<date>.md`) and sent to Telegram

---

## 📊 Sample Report (excerpt)

```
🧑‍💻 Agent Github - Audit

✨ Observation: Your project "construction-site-tracker"
doubled its test coverage this week (42% → 78%).
Kudos — consistent effort paying off.

💡 Suggestion: File `api/upload.ts` has 3 fetch() calls
without error handling → consider a wrapper.

🏆 Top 3 today:
1. lecteur-pdf — building Play Store presence
2. rennesdev-vps-ops — solid technical foundation
3. construction-site-tracker — great dev momentum
```

---

## 📣 Find Me

This project is part of the **rennesdev-vps-ops** infrastructure — a self-running, fully documented VPS.

- 🌐 [rennesdev.fr](https://rennesdev.fr)
- 🐙 [Ruaudel-Emmanuel/rennesdev-vps-ops](https://github.com/Ruaudel-Emmanuel/rennesdev-vps-ops)

---

*Built with ❤️ for developers who want to code more and review less.*
