# SmartRoom – Lokales KI-gesteuertes Smartroom-System

## Übersicht
SmartRoom ist ein modulares, lokal ausgeführtes Smartroom-System mit Sprachsteuerung.  
Es kombiniert **Go** (für Echtzeitsteuerung und Geräteintegration) mit **Python** (für KI-, Sprach- und Automatisierungsfunktionen).

Das Ziel: Ein vollständig lokales, privates System, das Geräte, Fenster, Beleuchtung und Aufgaben automatisiert – und über Sprache gesteuert werden kann.

---

## Hauptfunktionen

### Sprachsteuerung
- Lokale Spracherkennung mit Whisper (Python Worker)
- Text-to-Speech-Ausgabe (z. B. mit Piper oder Coqui)
- Intenterkennung über LLM (z. B. GPT oder lokal)

### Automatisierungen
- Modi und Szenen wie:
  - **Ich gehe aus dem Haus** → prüft Fenster, schaltet Geräte und Lichter aus  
  - **Ich bin zu Hause** → aktiviert Standardbeleuchtung, startet PC  
  - **Schlafenszeit** → dimmt Licht, fährt Geräte herunter
- Regel-Engine in Go (eventbasiert über MQTT oder lokale Events)

### Fenstersensoren
- Zwei Sensoren (oben und unten) erkennen Zustand:
  - geschlossen, gekippt oder offen
- Beim Verlassen des Hauses erfolgt eine automatische Warnung, z. B.:  
  "Fenster Schlafzimmer ist noch gekippt."

### Geräte- und Energiesteuerung
- **PC/Monitor**:
  - Wake-on-LAN zum Einschalten
  - Lokaler Agent (Go) zum Sperren, Herunterfahren oder Energiesparen
  - Monitor-Steuerung über DDC/CI oder Smart-Plugs
- **Strom sparen**:
  - Lichter und Geräte aus bei „Away“-Modus
  - Präsenzabhängige Steuerung (optional mit PIR-Sensor)

### KI-Integration
- Sprachbefehle für produktive Aufgaben:
  - Jira: Issues verschieben, kommentieren, erstellen
  - Kalender: Termine erstellen, verschieben, Tagesplan ausgeben
  - Memos: Notizen diktieren und automatisch zusammenfassen

---

## Architekturüberblick

        Sprach-Input
   Whisper + VAD + TTS
            │
            ▼
+---------------------------------------+
| Go Core Engine |
| - Event Bus / MQTT |
| - Scene/Mode Management |
| - Device Control (PC, Lights, etc.) |
| - Intent Routing (→ Python Worker) |
+-------------------┬-------------------+
│ gRPC/Queue
▼
+----------------------+
| Python Worker |
| - Whisper ASR |
| - GPT/NLU Intents |
| - Piper TTS |
+----------------------+
▼
(Lokale App – React Native)



---

## Tech Stack

| Ebene | Technologie | Beschreibung |
|--------|--------------|--------------|
| Core / Controller | Go (Gin / Fiber) | Hauptlogik, Geräte-, Szenen- und Eventsteuerung |
| AI & Speech | Python (faster-whisper, GPT API, Piper) | Sprachverarbeitung und Text-to-Speech |
| Kommunikation | MQTT (Mosquitto) | Gerätebus / Event-System |
| Frontend | React Native | Mobile und Desktop App |
| Lokale Geräte | ESP32, Smart-Plugs | Sensoren und Aktoren |
| Datenbank | SQLite (optional) | Konfiguration, Logs, Tokens |

---

## Erste Schritte

### Voraussetzungen
- Go ≥ 1.22
- Python ≥ 3.10
- Docker (optional, für MQTT oder TTS-Services)
- ffmpeg (für Audio-Verarbeitung)

### Setup

```bash
# Core (Go)
cd core
go mod tidy
go run main.go

# Python Worker
cd worker
pip install -r requirements.txt
python worker.py

# React Native App
cd app
npm install
npm run start
