# unCoded Trading Bot - DigitalOcean Deployment

## 🚀 1-Click Deploy

[![Deploy to DigitalOcean](https://www.deploytodo.com/do-btn-blue.svg)](https://cloud.digitalocean.com/apps/new?repo=https://github.com/AureumVictoria/unCodedTradingBotDigitalOcean/tree/main)

---

## 📋 Setup-Anleitung

### Voraussetzungen:
- DigitalOcean Account
- Docker Hub Account (mit gepushten Images)
- API Keys von deinem Exchange
- Telegram Bot Token

### Deployment Schritte:

1. **Klicke auf den "Deploy to DigitalOcean" Button** ☝️

2. **Autorisiere DigitalOcean:**
   - Verbinde dein GitHub/GitLab Konto
   - Wähle das Repository aus

3. **Konfiguriere Environment Variables:**
   
   In der DigitalOcean App Platform musst du folgende **Secrets** eingeben:
   
   | Variable | Beschreibung | Beispiel |
   |----------|--------------|----------|
   | `API_KEY` | Dein Exchange API Key | `abc123...` |
   | `API_SECRET` | Dein Exchange API Secret | `xyz789...` |
   | `TELEGRAM_TOKEN` | Telegram Bot Token | `1234567890:ABC...` |

4. **Deploy starten** - Klicke auf "Create Resources"

5. **Fertig!** 🎉
   - Die App wird automatisch deployed
   - PostgreSQL Datenbank wird eingerichtet
   - Beide Bots starten automatisch

---

## 💰 Kosten

- **Basic Container** (2x): ~$10/Monat
- **Managed PostgreSQL**: ~$15/Monat
- **Total**: ca. **$25/Monat**

---

## 🔧 Wichtige Hinweise

### ⚠️ Vor dem Deploy:

1. **Docker Images müssen auf Docker Hub sein:**
```bash
