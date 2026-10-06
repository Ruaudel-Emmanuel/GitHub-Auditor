# Scan ubuntu-latest — alerte Telegram quotidienne

Scanne tous les repos GitHub actifs pour détecter les workflows utilisant `runs-on: ubuntu-latest`, analyse les résultats avec Ollama (qwen2.5:3b) et envoie un rapport sur Telegram.

## Déclencheur

- **Cron :** 05h00 (Europe/Paris) — tous les jours
- Expression : `0 5 * * *`

## Chaîne du workflow

1. **Horloge (05h00)** → déclencheur cron
2. **Code — scanne les workflows GitHub** → liste les repos, récupère les workflows, détecte `ubuntu-latest`
3. **Ollama — analyse le rapport** → LLM local qwen2.5:3b, analyse et recommande les actions
4. **Code — formate le message Telegram** → assemble le rapport + analyse LLM
5. **Telegram — envoi** → envoie à @Vosmanubot (chat 8634051625)

## Fonctionnement

Le nœud Code utilise `$env.GITHUB_PAT` (PAT GitHub centralisé) pour :
1. Lister tous les repos (non fork, non archivés)
2. Pour chaque repo, inspecter l'arbre Git pour trouver `.github/workflows/*.yml`
3. Pour chaque workflow, télécharger le contenu via Contents API
4. Chercher les lignes contenant `runs-on: ubuntu-latest`
5. Compiler un rapport structuré → envoyé à Ollama pour analyse

## Dépendances

- `GITHUB_PAT` dans les variables d'environnement (avec accès repos + contents)
- Ollama local (qwen2.5:3b)
- Telegram Bot (@Vosmanubot)

## Contexte

Ce workflow prépare la migration `ubuntu-latest` → `ubuntu-24.04`/`ubuntu-26.04`. 
Le label `ubuntu-latest` passera de Ubuntu 24.04 à Ubuntu 26.04, ce qui peut casser 
des workflows (dépendances système, chemins, noyau). L'objectif est de :

1. **Diagnostiquer** (ce workflow) — détecter toutes les occurrences
2. **Stabiliser** — figer sur `ubuntu-24.04` dans chaque workflow
3. **Tester** — créer des workflows de staging sur `ubuntu-26.04`
4. **Migrer** — basculer une fois les tests validés

## Historique

- 2026-10-06 : création du workflow