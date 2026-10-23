# 🚀 Nouvelle capacité : réinstallation VPS automatisée — 2026-10-23

Le VPS rennesdev.fr dispose désormais d'un **package de réinstallation complet** :

## 📦 Repos créés

| Repo | Visibilité | Lien |
|---|---|---|
| **vps-reinstall** | 🔒 Privé | github.com/Ruaudel-Emmanuel/vps-reinstall |
| **install-vps** | 🌍 Public | github.com/Ruaudel-Emmanuel/install-vps |

## 🔧 vps-reinstall (personnel)

Réinstallation **complète et idempotente** du VPS :
- `reinstall.sh` : 6 étapes (system → caddy → docker → monitoring → scripts → finish)
- Configs versionnées : Caddyfile, fail2ban, 10 services systemd
- Secrets externalisés (.env gitigné)
- Scripts : backup, màj auto, bot Telegram, watchdog, journal quotidien
- Procédure d'urgence incluse (nouveau VPS → clone → restore Kopia → OK)

## 🌍 install-vps (client)

Repo **public et anonyme** pour installer un VPS chez un client :
- `install.sh` : 8 étapes, aucun secret, template .env
- README orienté client : 8 cas d'usage métier
- Services : n8n, Ollama, PostgreSQL, Browserless, Uptime-Kuma, Netdata, FileBrowser, Umami
- Sécurité : UFW, fail2ban, Cloudflare, Caddy SSL, Docker isolé

## 🏆 Compétences démontrées

- **DevOps** : script d'infrastructure idempotent, systemd, Docker Compose
- **Sécurité** : externalisation des secrets, anonymisation des repos clients
- **Productivité** : réinstallation complète en ~30 min au lieu de 1-3 jours
