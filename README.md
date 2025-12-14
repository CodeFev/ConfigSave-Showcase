# ☁️ ConfigSave - Cloud Save System (Showcase)

> **⚠️ Hinweis:** Dies ist ein **Portfolio-Showcase**. Der vollständige Quellcode ist aktuell **Private/Closed Source**, da er Teil eines kommerziellen Prototyps ist. Dieses Repository demonstriert die Architektur, das UI-Design und die Sicherheitsimplementierung.

**ConfigSave** ist ein fortgeschrittenes Tool für Unity, das Spielern erlaubt, lokale Konfigurationsdateien sicher in der Cloud zu speichern und zwischen PCs zu synchronisieren. Es wurde entwickelt, um komplexe Dateistrukturen (rekursiv) zu erkennen und sicher via **Supabase** zu verwalten.

![Status](https://img.shields.io/badge/Status-Active_Prototype-success) ![Engine](https://img.shields.io/badge/Engine-Unity_2022-blue) ![Backend](https://img.shields.io/badge/Backend-Supabase_PostgreSQL-green)

---

## 🖼️ Demo Showcase

<div align="center">
  <img src="https://github.com/user-attachments/assets/76a43733-5d76-42b7-a5ff-debf196a4fab" width="100%" alt="ConfigSave Demo GIF" />
  <br>
  <p><i>Live-Demo: Upload einer Konfiguration mit automatischer Erkennung und Feedback.</i></p>
</div>

---

## 💡 Key Features Implemented

### 🛡️ 1. Server-Side Security (Row Level Security)
Anstatt dem Client blind zu vertrauen, wurde die Sicherheit direkt in die Datenbank (PostgreSQL/Supabase) verlagert:
* **Hard Slot Limit:** Ein User kann maximal **50 Configs** besitzen. Die Datenbank blockiert weitere Uploads automatisch auf SQL-Ebene.
* **Anti-Malware Policy:** Strenge SQL-Regeln verhindern den Upload von ausführbaren Dateien (`.exe`, `.bat`) und erzwingen valide Dateitypen.

### 🧠 2. Smart Rate Limiting
Um API-Missbrauch (Spamming) zu verhindern, wird ein duales System genutzt:
1.  **Client-Side:** Registry-basiertes Tracking (`PlayerPrefs`), das den Upload-Button temporär sperrt, wenn ein User zu viele Anfragen sendet.
2.  **Server-Side:** Die Datenbank weist Anfragen hart ab, wenn Quotas pro Stunde überschritten sind.

### 🛡️ 3. Safety & Integrity Checks
Das Tool schützt den Nutzer vor versehentlichem Datenverlust durch intelligente Client-Prüfungen:
* **Active Process Detection:** Nutzung von Windows-API-Aufrufen (`user32.dll`), um zu prüfen, ob das Zielspiel (z.B. *Apex Legends*) gerade läuft. Schreibvorgänge werden blockiert, um Dateikorruption zu verhindern.
* **Conflict Resolution:** Vor dem Erstellen einer Config prüft das System asynchron gegen die Cloud-Datenbank, ob der Name bereits vergeben ist.

### ⚡ 4. Performance & Metadata
Statt bei jedem Start Gigabytes an Config-Dateien herunterzuladen, nutzt das System eine **Lightweight-Architektur**:
* **Metadata-First:** Beim Upload wird eine winzige `info.json` (Größe, Erstelldatum) generiert. Das UI lädt nur diese JSON-Dateien, um die Liste in Millisekunden anzuzeigen.
* **Aggressive Caching:** Dateilisten werden lokal gecached und nur bei Bedarf (oder User-Refresh) neu via API abgerufen.

### 🎮 5. Dynamic Game Detection
Das Tool liest eine externe JSON-Konfiguration (`game_paths.json`), um installierte Spiele automatisch zu finden. Es unterstützt:
* **SteamID Wildcards:** Automatische Pfadauflösung (z.B. `{UserID}/730/local/cfg`).
* **Flexible Pfade:** Unterstützung verschiedener Windows-Standardpfade (AppData, Documents, SteamApps).

---

## 🛠️ Tech Stack

| Bereich | Technologie |
| :--- | :--- |
| **Engine** | Unity 2022 LTS (C#) |
| **Backend** | Supabase (PostgreSQL, Storage Buckets) |
| **Auth** | Steamworks.NET (SteamID Integration) |
| **OS Integration** | User32.dll (Process & Window Detection) |
| **UI** | Unity uGUI mit Async Feedback Loops |

---
