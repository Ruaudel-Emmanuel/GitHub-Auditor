# GitHub Auditor — Daily AI Audit for All Your Projects

> An n8n workflow that **analyzes all your active GitHub repositories every morning at 06:00** in a single batched analysis via a local LLM, and delivers a friendly, constructive report to Telegram.

---

## ✨ Original Prompt

> *"Create an n8n 'GitHub Auditor' workflow that checks all my active GitHub projects every day at 6:00 AM, analyzes them via Ollama as an enthusiastic and constructive 'Collaborative Architect,' and sends me the report on Telegram."*

---

## 🎯 What It Does

| Step | Description |
|---|---|
| **06:00** | Daily cron (Europe/Paris) |
| **Step 1** | Calls the GitHub API (`/user/repos`) to list **all repositories** where you have push access |
| **Step 2** | Filters active repos (not archived, not disabled) |
| **Step 3** | **Groups all active repositories into a single analysis** (instead of one call per repo) and builds the prompt for Ollama |
| **Step 4** | Sends the prompt to **Ollama (model `qwen2.5:3b`)** — **a single API call** that analyzes all projects at once |
| **Step 5** | Formats the AI response for Telegram (≤ 4000 characters) |
| **Step 6** | Sends the audit report to **Telegram** |

### Why Batching?

The old version sent **one Ollama call per repository** (18 calls for 18 repos). Each call took ~30–90s, so total duration exceeded the n8n runner timeout (300s). The new version sends **all repositories in a single analysis**, which:
- ✅ Fits within the 300s window
- ✅ Avoids loading/unloading the LLM model between repos
- ✅ Produces a single, more concise report with a **Top 3 most promising projects**

---

## 🧠 The System Prompt: "Collaborative Architect"

The LLM uses a **complete role system** that transforms it into a Staff Engineer-level programming peer:

| Aspect | Detail |
|---|---|
| **Persona** | Enthusiastic technical mentor, constructive, never condescending |
| **Analysis** | 4 dimensions: Missing issues, Bugs/Risks, Code Review (architecture/security/perf), PRs |
| **Format** | Structured report with emojis: ✨ Observation · 💡 Improvement areas · 🛠 Next step · ✅ Summary + 🏆 Top 3 |
| **Tone** | Ultra-positive: every critique is framed as a suggestion for improvement |

---

## 🏗️ n8n Architecture (6 nodes)

```
Clock (06:00)
  │
  ▼
HTTP Request — List GitHub repos (API /user/repos)
  │
  ▼
Code — Filter + Group all repos into 1 Ollama prompt
  │
  ▼
HTTP Request — ONE Ollama call /api/chat (qwen2.5:3b, 5 min timeout)
  │
  ▼
Code — Format response (≤ 3900 chars for Telegram)
  │
  ▼
Telegram — Send report (chat 8634051625, silent notification)
```

---

## ⚙️ Dependencies

| Component | Version / Detail |
|---|---|
| **n8n** | ≥ 2.41 (workflow published via snapshot + outbox mechanism) |
| **Ollama** | Model `qwen2.5:3b` local (`keep_alive: 0` → unloads after use) |
| **GitHub PAT** | Environment variable `GITHUB_PAT` (Bearer token, `repo` + `read:user` scope) |
| **Telegram** | Bot token stored in n8n credential, chat ID `8634051625` |
| **DB** | PostgreSQL 17 (n8n_db) |

---

## 📥 Installation

The workflow is deployed directly into n8n via PostgreSQL.

### SQL Method

```sql
-- 1. Insert workflow_entity
-- 2. Create workflow_history snapshot
-- 3. Add shared_workflow (requires projectId)
-- 4. Align versionId = activeVersionId
-- 5. Create workflow_published_version row
-- 6. Insert into workflow_publication_outbox (status: pending, reason: publish)
-- 7. Restart n8n
```

### Provided File

- [`workflow.json`](./workflow.json) — full n8n workflow export (node IDs, connections, JS code, parameters)
- ⚠️ Secrets (GitHub PAT, Telegram key) are **not** in this file: they are read via `$env.GITHUB_PAT` (env variable) and the Telegram credential stored in n8n

---

## 🔒 Security

- **No secrets in code**: GitHub PAT is read via `$env.GITHUB_PAT` (n8n environment variable)
- **No data leaves the VPS** for analysis: Ollama runs locally
- **Telegram credential** is stored in n8n (encrypted with `N8N_ENCRYPTION_KEY`)
- **Ollama model is unloaded** after each analysis (`keep_alive: 0`)

---

## 🚀 Possible Next Steps

- [ ] Fetch each repo's README.md to enrich the analysis
- [ ] Include last 7 days of commits in the context
- [ ] Include open PRs and recent issues
- [ ] Auto-generate GitHub issues from AI suggestions

---

*Part of the [rennesdev-vps-ops](https://github.com/Ruaudel-Emmanuel/rennesdev-vps-ops) infrastructure — a self-running VPS.*