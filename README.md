# GitHub Auditor — Audit quotidien IA de tous vos projets

> Un workflow n8n qui analyse **chaque matin à 06h00** tous vos dépôts GitHub actifs via un LLM local, et vous envoie un rapport bienveillant et constructif sur Telegram.

---

## ✨ Prompt idéal de départ

> *« Crée un workflow n8n « GitHub Auditor » qui tous les jours à 6h00 vérifie tous mes projets GitHub actifs, les analyse via Ollama avec le rôle d'un « Architecte Collaborateur » enthousiaste et constructif, et m'envoie le rapport sur Telegram. »*

---

## 🎯 Ce que fait le workflow

| Étape | Description |
|---|---|
| **06h00** | Cron quotidien (Europe/Paris) |
| **Étape 1** | Appelle l'API GitHub (`/user/repos`) pour lister **tous les dépôts** où vous avez les droits push |
| **Étape 2** | Filtre les repos actifs (non archivés, non disabled) |
| **Étape 3** | Pour chaque repo, construit un prompt d'analyse avec les métadonnées (nom, description, langage, topics, dernière activité, issues ouvertes…) |
| **Étape 4** | Envoie le prompt à **Ollama (modèle `qwen2.5:3b`)** — inférence locale, aucune donnée ne quitte le VPS |
| **Étape 5** | Formate la réponse de l'IA pour Telegram (≤ 4000 caractères) |
| **Étape 6** | Envoie le rapport d'audit sur **Telegram** |

---

## 🧠 Le prompt système : « Architecte Collaborateur »

Le LLM utilise un **système de rôle** complet qui le transforme en pair programmeur de niveau **Staff Engineer** :

| Aspect | Détail |
|---|---|
| **Persona** | Mentor technique enthousiaste, constructif, jamais condescendant |
| **Analyse** | 4 dimensions : Issues manquantes, Bugs/Risques, Code Review (architecture/sécurité/perf), PR |
| **Format** | Rapport structuré avec emojis : ✨ Observation · 💡 Axes d'amélioration · 🛠 Prochaine étape · ✅ Synthèse |
| **Ton** | Ultra-positif : chaque critique est formulée comme une suggestion d'amélioration |

---

## 🏗️ Architecture n8n (6 nœuds)

```
Horloge (06h00)
  │
  ▼
HTTP Request — Lister les dépôts GitHub (API /user/repos)
  │
  ▼
Code — Filtrer les repos actifs + construire le prompt et le payload Ollama
  │
  ▼
HTTP Request — Ollama /api/chat (qwen2.5:3b, timeout 5 min, keep_alive: 0)
  │
  ▼
Code — Formater la réponse (↘ 4000 caractères max pour Telegram)
  │
  ▼
Telegram — Envoyer le rapport (chat 8634051625, notification silencieuse)
```

---

## ⚙️ Dépendances

| Élément | Version / Détail |
|---|---|
| **n8n** | ≥ 2.41 (workflow publié via la mécanique snapshot + outbox) |
| **Ollama** | Modèle `qwen2.5:3b` local (`keep_alive: 0` → décharge après usage) |
| **GitHub PAT** | Variable d'environnement `GITHUB_PAT` (Bearer token, accès `repo` + `read:user`) |
| **Telegram** | Bot token stocké en credential n8n, chat ID `8634051625` |
| **DB** | PostgreSQL 17 (n8n_db) |

---

## 📥 Installation

Le workflow est déployé directement dans n8n via la base de données PostgreSQL.

### Méthode SQL (comme les autres workflows de ce VPS)

```sql
-- 1. Insérer le workflow_entity
-- 2. Créer le snapshot workflow_history
-- 3. Ajouter shared_workflow (projectId nécessaire)
-- 4. Aligner versionId = activeVersionId
-- 5. Créer la ligne workflow_published_version
-- 6. Insérer dans workflow_publication_outbox (status: pending, reason: publish)
-- 7. Redémarrer n8n
```

Le script de build est disponible dans l'historique du commit initial (ou référez-vous au skill `n8n-ops` du repo [rennesdev-vps-ops](https://github.com/Ruaudel-Emmanuel/rennesdev-vps-ops)).

### Fichier fourni

- [`workflow.json`](./workflow.json) — export du workflow n8n complet (IDs des nœuds, connections, code JS, paramètres)
- ⚠️ Les secrets (PAT GitHub, clé Telegram) ne sont **pas** dans ce fichier : ils sont lus via `$env.GITHUB_PAT` (variable d'env) et le credential Telegram stocké dans n8n

---

## 🔒 Sécurité

- **Aucun secret dans le code** : le PAT GitHub est lu via `$env.GITHUB_PAT` (variable d'environnement n8n)
- **Aucune donnée ne quitte le VPS** pour l'analyse : Ollama tourne en local
- **Le credential Telegram** est stocké dans n8n (chiffré par `N8N_ENCRYPTION_KEY`)
- **Le modèle Ollama est déchargé** après chaque analyse (`keep_alive: 0`)

---

## 🚀 Prochaine évolution possible

- [ ] Ajouter la récupération du README.md de chaque repo pour enrichir l'analyse
- [ ] Ajouter les derniers commits (7 jours) dans le contexte
- [ ] Ajouter les PR ouvertes et issues récentes
- [ ] Regrouper les rapports en un seul message Telegram (éviter le spam si 15+ repos)
- [ ] Générer automatiquement des issues GitHub à partir des suggestions de l'IA

---

*Fait partie de l'infrastructure [rennesdev-vps-ops](https://github.com/Ruaudel-Emmanuel/rennesdev-vps-ops) — un VPS qui tourne tout seul.*