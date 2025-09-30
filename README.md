🚀 Trading Bot - DigitalOcean Cloud-Init Setup Anleitung
📋 Übersicht
Dieses Cloud-Init Script richtet automatisch einen Trading Bot auf einem DigitalOcean Droplet ein mit:

✅ Docker & Docker Compose
✅ PostgreSQL 15 Datenbank
✅ 2 Trading Bots (Main + Backup)
✅ Firewall (UFW) Konfiguration
✅ Fail2ban Sicherheit
✅ Monitoring Tools
✅ Automatische Updates


🎯 Voraussetzungen
Bevor du startest, benötigst du:

DigitalOcean Account (www.digitalocean.com)
Docker Hub Account mit gepushten Bot Images
Exchange API Keys (z.B. Binance, Bybit)
Telegram Bot Token (von @BotFather)
SSH Key (optional aber empfohlen)


📦 Schritt-für-Schritt Anleitung
1️⃣ Droplet erstellen mit Cloud-Init

Gehe zu DigitalOcean → Create → Droplets
Wähle eine Region (z.B. Frankfurt für niedrige Latenz)
Image auswählen:

Ubuntu 24.04 LTS (empfohlen)
Oder Ubuntu 22.04 LTS


Droplet Größe:

Basic ($6/Monat): 1GB RAM, 1 CPU - Für Testing
Basic ($12/Monat): 2GB RAM, 1 CPU - Empfohlen für Produktiv
General Purpose ($18/Monat): 2GB RAM, 2 CPU - Für mehrere Bots


User Data hinzufügen:

Scrolle zu Advanced Options
Klicke auf Add Initialization scripts (free)
Kopiere den kompletten Inhalt von cloud-init-trading-bot.yaml und füge ihn ein


Authentication:

SSH Key hinzufügen (empfohlen)
Oder Root-Passwort verwenden


Hostname: trading-bot-01 (oder dein Wunschname)
Create Droplet klicken

2️⃣ Installation überwachen
Die Installation dauert ca. 3-5 Minuten. Du kannst den Status prüfen:
bash# Per SSH einloggen
ssh root@YOUR_DROPLET_IP

# Installation Status prüfen
tail -f /var/log/cloud-init-output.log

# Warte bis du siehst: "✅ Setup abgeschlossen!"
3️⃣ Konfiguration anpassen
WICHTIG: Bevor die Bots starten, musst du deine API Keys eintragen!
bash# Environment Variablen bearbeiten
nano /opt/trading-bot/.env
Ändere folgende Werte:
env# Exchange API Credentials - WICHTIG!
API_KEY=dein_echter_api_key_hier
API_SECRET=dein_echter_api_secret_hier

# Telegram Bot - WICHTIG!
TELEGRAM_TOKEN=123456MNOpqrsTUVwxyz
TELEGRAM_CHAT_ID=1236789

# Database Password - WICHTIG!
DATABASE_URL=postgresql://tradingbot:DEIN_STARKES_PASSWORT@postgres:5432/trading_db
DB_PASSWORD=DEIN_STARKES_PASSWORT

# Trading Einstellungen
EXCHANGE=binance
TRADING_PAIR=BTC/USDT
MAX_POSITIONS=5
POSITION_SIZE=100
Speichern: CTRL+O → Enter → CTRL+X
4️⃣ Docker Compose anpassen
bash# Docker Compose bearbeiten
nano /opt/trading-bot/docker-compose.yml
Ersetze die Image Namen:
yamlservices:
  trading-bot-1:
    image: dein_username/trading-bot:latest  # ← HIER ÄNDERN
    
  trading-bot-2:
    image: dein_username/trading-bot-alternative:latest  # ← HIER ÄNDERN
Speichern: CTRL+O → Enter → CTRL+X
5️⃣ Bots starten
bash# In das Bot-Verzeichnis wechseln
cd /opt/trading-bot

# Bots starten
./start-bots.sh

# Logs anzeigen
docker-compose logs -f

🛠️ Nützliche Befehle
Status & Monitoring
bash# Alle Container anzeigen
docker ps

# Bot Status prüfen (mit Monitor Script)
./monitor.sh

# Live Logs anschauen
docker-compose logs -f

# Logs von einem bestimmten Bot
docker-compose logs -f trading-bot-1
Bot Verwaltung
bash# Bots neustarten
docker-compose restart

# Bots stoppen
docker-compose down

# Bots wieder starten
docker-compose up -d

# Einzelnen Bot neustarten
docker restart trading-bot-1
Datenbank Zugriff
bash# PostgreSQL Shell öffnen
docker exec -it postgres psql -U tradingbot -d trading_db

# Tabellen anzeigen
\dt

# Trades anzeigen (Beispiel)
SELECT * FROM trades ORDER BY created_at DESC LIMIT 10;

# PostgreSQL Shell verlassen
\q
System Wartung
bash# Disk Space prüfen
df -h

# Memory Usage
free -h

# Docker Disk Cleanup
docker system prune -a

# Logs leeren
docker-compose logs --tail 0 > /dev/null

🔒 Sicherheit
Firewall Status
bash# UFW Status prüfen
ufw status

# Offene Ports:
# - 22 (SSH)
# - 80 (HTTP)
# - 443 (HTTPS)
# - 5432 (PostgreSQL)
Passwörter ändern
PostgreSQL:
bashdocker exec -it postgres psql -U tradingbot -c "ALTER USER tradingbot WITH PASSWORD 'neues_starkes_passwort';"

# Dann in .env anpassen!
nano /opt/trading-bot/.env
SSH Key Setup (falls noch nicht gemacht)
bash# Auf deinem lokalen Computer
ssh-keygen -t ed25519 -C "deine@email.de"

# Public Key auf Server kopieren
ssh-copy-id root@YOUR_DROPLET_IP

🐛 Troubleshooting
Bots starten nicht
bash# Logs prüfen
docker-compose logs

# Container Status
docker ps -a

# Environment Variablen prüfen
cat /opt/trading-bot/.env

# Manuelle Container Prüfung
docker inspect trading-bot-1
Datenbank Verbindungsprobleme
bash# PostgreSQL Container Status
docker ps | grep postgres

# PostgreSQL Logs
docker logs postgres

# Verbindung testen
docker exec postgres pg_isready -U tradingbot
Speicherplatz voll
bash# Alte Docker Images löschen
docker system prune -a

# Alte Logs löschen
find /var/log -name "*.log" -type f -mtime +7 -delete

# Docker Volumes prüfen
docker volume ls
docker volume prune
API Fehler
bash# API Key testen (Beispiel für Binance)
curl -X GET 'https://api.binance.com/api/v3/account' \
  -H "X-MBX-APIKEY: DEIN_API_KEY"

# Permissions prüfen in Exchange Settings
# Benötigt: Spot Trading, Read Info

📊 Monitoring & Alerts
Telegram Benachrichtigungen
Die Bots senden automatisch Benachrichtigungen wenn:

✅ Trade ausgeführt wurde
❌ Fehler auftreten
⚠️ Stop Loss getriggert
💰 Gewinn/Verlust Reports

Optional: Grafana Dashboard
Kommentiere in docker-compose.yml die Grafana Sektion aus:
yamlgrafana:
  image: grafana/grafana:latest
  ports:
    - "3000:3000"
Dann:
bashdocker-compose up -d grafana
Zugriff: http://YOUR_DROPLET_IP:3000
Login: admin / admin

💰 Kosten Übersicht
DigitalOcean Droplet

Testing: $6/Monat (1GB RAM)
Produktiv: $12/Monat (2GB RAM) ← Empfohlen
High Performance: $18-24/Monat (4GB RAM)

Zusätzliche Kosten

Backups: +20% der Droplet-Kosten (optional)
Managed PostgreSQL: Ab $15/Monat (falls separat)
Traffic: Inkludiert (1TB+)


🔄 Updates & Wartung
Bot Updates
bash# Neue Docker Images pullen
docker pull dein_username/trading-bot:latest

# Bots mit neuer Version neustarten
docker-compose pull
docker-compose up -d
System Updates
bash# Ubuntu Updates
apt update && apt upgrade -y

# Docker Updates
apt install docker.io docker-compose -y

# Reboot falls nötig
reboot
Backups
Datenbank Backup:
bash# Backup erstellen
docker exec postgres pg_dump -U tradingbot trading_db > backup_$(date +%Y%m%d).sql

# Backup herunterladen (auf lokalem Computer)
scp root@YOUR_DROPLET_IP:/root/backup_*.sql ./
DigitalOcean Snapshots:

Gehe zu Droplet → Snapshots → Take Snapshot
Kosten: ~$0.05/GB/Monat


📞 Support & Hilfe
Logs für Support
bash# System Info sammeln
./monitor.sh > system_info.txt

# Logs speichern
docker-compose logs > bot_logs.txt

# .env anonymisiert speichern (ohne API Keys!)
cat /opt/trading-bot/.env | grep -v "API_KEY\|API_SECRET\|TOKEN" > config.txt
Nützliche Links

DigitalOcean Docs: https://docs.digitalocean.com
Docker Docs: https://docs.docker.com
PostgreSQL Docs: https://www.postgresql.org/docs/


⚠️ Wichtige Hinweise

Niemals API Keys mit Read+Write Permissions verwenden!

Nutze nur Trading Permissions
Keine Withdrawal Permissions!


Teste zuerst mit kleinen Beträgen!

Starte mit $10-50
Erhöhe langsam nach erfolgreichen Trades


Überwache deinen Bot regelmäßig!

Prüfe täglich die Logs
Setze Telegram Alerts
Nutze Stop Loss!


Backups sind Pflicht!

Wöchentliche Datenbank Backups
Monatliche Droplet Snapshots


Sicherheit geht vor!

Starke Passwörter verwenden
SSH Keys statt Passwörter
Firewall aktiv lassen




🎉 Fertig!
Dein Trading Bot läuft jetzt auf DigitalOcean! 🚀
Happy Trading! 📈💰

📝 Changelog

v1.0 - Initial Release mit Cloud-Init Script

Docker & PostgreSQL Setup
2 Bot Configuration
Monitoring Tools
Sicherheits Features
