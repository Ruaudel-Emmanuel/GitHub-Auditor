# GitHub Auditor — Votre revue de code quotidienne, automatisée et privée

> Un workflow n8n qui **analyse chaque matin l'ensemble de vos dépôts GitHub** via un LLM local (Ollama), et vous livre un rapport bienveillant et actionnable sur Telegram.  
> **Aucune donnée ne quitte votre serveur.**

---

## 🎯 Pourquoi GitHub Auditor ?

### Le problème

Vous gérez **plusieurs projets** — certains personnels, d'autres professionnels. Difficile de :
- Suivre la qualité de code sur **tous vos dépôts** sans ouvrir chacun un par un
- Repérer les **issues ou PR qui stagnent**  
- Avoir une **vue d'ensemble quotidienne** sans y passer 30 minutes

### La solution

**GitHub Auditor** vous envoie chaque matin à 06h00 un **rapport IA unique** qui couvre **tous vos projets actifs** en une seule analyse groupée :

| Avant (manuel) | Après (GitHub Auditor) |
|---|---|
| 🕐 30 min à tout vérifier | ☕ 0 min — c'est fait automatiquement |
| 🔍 Vous oubliez la moitié des projets | 📋 100 % des dépôts passés en revue |
| 😤 Critique = corvée | ✨ Feedback constructif chaque jour |
| 📊 Pas d'historique | 📈 Rapport quotidien + évolution |

---

## ✨ Ce qui le rend unique

| Caractéristique | Bénéfice |
|---|---|
| 🔒 **100 % privé** | Ollama tourne en local sur votre infra — **zéro donnée vers le cloud** |
| ⚡ **Analyse groupée** | Tous vos repos analysés **en un seul appel** LLM (gain x18 vs. un par un) |
| 🤝 **Ton « Architecte Collaboratif »** | Pas un robot froid — un **pair programmeur enthousiaste** qui suggère, ne juge pas |
| 📱 **Livré sur Telegram** | Vous lisez votre rapport au réveil, pas besoin d'ouvrir n8n |
| 🔧 **Zéro maintenance** | Cron quotidien, déploiement SQL one-shot |
| 🏆 **Top 3 du jour** | Chaque rapport met en avant les **projets les plus prometteurs** |

---

## 👥 Pour qui ?

| Public | Bénéfice principal |
|---|---|
| **🧑‍💻 Développeur solo (5-20 repos)** | Avoir une vue d'ensemble sans y passer la matinée |
| **🏢 CTO / lead dev (petite équipe)** | Superviser la qualité sans micromanager |
| **🔧 Mainteneur open-source** | Suivre plusieurs projets sans en laisser un à l'abandon |
| **🏠 Self-hosteur / homelaber** | IA privée + infra maîtrisée, pas de dépendance cloud |

---

## 🧠 Le prompt système : « Architecte Collaboratif »

L'IA adopte le rôle d'un **Staff Engineer** qui examine vos projets avec 4 dimensions :

| Dimension | Ce qu'il regarde |
|---|---|
| ✨ **Observations** | Projets prometteurs, bonnes pratiques, points forts |
| 💡 **Axes d'amélioration** | Issues manquantes, bugs potentiels, failles de sécurité |
| 🛠 **Prochaine étape** | Suggestion concrète : PR à review, refacto, doc à écrire |
| ✅ **Synthèse + 🏆 Top 3** | Classement des projets les plus passionnants du moment |

---

## 🏗️ Architecture n8n (6 nœuds, 5 min chrono)

```
⏰ Cron (06h00)
   ↓
📡 GitHub API → Liste tous vos repos
   ↓
🧠 Un seul appel Ollama (qwen2.5:3b) → analyse groupée
   ↓
📱 Telegram → Rapport livré sur votre téléphone
```

---

## 🚀 Installation

### Prérequis
- **n8n** ≥ 2.41 (et une instance qui tourne)
- **Ollama** avec le modèle `qwen2.5:3b` disponible localement  
- **Un token GitHub** (PAT) avec scope `repo` + `read:user`  
- **Un bot Telegram** (token dans un credential n8n)

### Déploiement (5 minutes)

1. **Téléchargez** le fichier [`workflow.json`](./workflow.json)
2. **Importez-le** dans n8n (via l'UI ou la méthode SQL décrite dans le fichier)
3. **Crédentiallez** : `GITHUB_PAT` en variable d'env, token Telegram dans un credential n8n
4. **Activez** le workflow — c'est tout.

> ⚠️ Le PAT GitHub est lu via `$env.GITHUB_PAT` — pas de secret en dur dans le code.

---

## 🔒 Sécurité & Vie privée

- ✅ **Aucune donnée ne quitte votre VPS/serveur**
- ✅ Ollama tourne en local — pas d'appel API externe pour l'analyse
- ✅ Le token Telegram est stocké chiffré dans n8n
- ✅ Le modèle Ollama est déchargé après chaque analyse (`keep_alive: 0`)

---

## 📊 Exemple de rapport (extrait)

```
🧑‍💻 Agent Github - Audit

✨ Observation : Votre projet « construction-site-tracker »
a doublé sa couverture de tests cette semaine (42% → 78%). 
Bravo, c'est un effort continu qui paie.

💡 Suggestion : Le fichier `api/upload.ts` contient 3 appels
fetch() sans gestion d'erreur → envisager un wrapper.

🏆 Top 3 du jour :
1. lecteur-pdf — contribue à la notoriété Play Store
2. rennesdev-vps-ops — fondation technique stable
3. construction-site-tracker — belle dynamique de dev
```

---

## 📣 Retrouvez-moi

Ce projet fait partie de l'infrastructure **rennesdev-vps-ops** — un VPS qui tourne tout seul, documenté et automatisé.

- 🌐 [rennesdev.fr](https://rennesdev.fr)
- 🐙 [Ruaudel-Emmanuel/rennesdev-vps-ops](https://github.com/Ruaudel-Emmanuel/rennesdev-vps-ops)

---

*Construit avec ❤️ pour les développeurs qui veulent coder plus et reviewer moins.*