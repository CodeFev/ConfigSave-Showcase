☁️ ConfigSave - Cloud Save System (Showcase)
⚠️ Note: This is a portfolio showcase. The full source code is currently private/closed source as it is part of a commercial prototype. This repository demonstrates the architecture, UI, and security implementation.

ConfigSave ist ein fortgeschrittenes Tool für Unity, das Spielern erlaubt, lokale Konfigurationsdateien sicher in der Cloud zu speichern und zwischen PCs zu synchronisieren. Es wurde entwickelt, um komplexe Dateistrukturen (rekursiv) zu erkennen und sicher via Supabase zu verwalten.

🖼️ GIF Showcase
<div align="center">
  <img src="[LINK_VON_GITHUB_HIER_EINFÜGEN](https://github.com/user-attachments/assets/76a43733-5d76-42b7-a5ff-debf196a4fab)" width="100%" />
</div>

💡 Key Features Implemented
🛡️ 1. Server-Side Security (Row Level Security)
- nstatt dem Client zu vertrauen, habe ich die Sicherheit direkt in die Datenbank (PostgreSQL/Supabase) verlagert.
- Hard Slot Limit: Ein User kann maximal 50 Configs besitzen. Die Datenbank blockiert weitere Uploads automatisch.
- Anti-Malware Policy: SQL-Regeln verhindern den Upload von ausführbaren Dateien (.exe, .bat).

🧠 2. Smart Rate Limiting
Um API-Abuse zu verhindern, wird ein duales System genutzt:
1. Client-Side: Registry-basiertes Tracking (PlayerPrefs), das Uploads temporär sperrt, wenn ein User spammt.
2. Server-Side: Die Datenbank weist Anfragen ab, wenn Quotas überschritten sind.

🛡️ 3. Safety & Integrity Checks
Das Tool schützt den Nutzer vor versehentlichem Datenverlust durch intelligente Client-Prüfungen:
- Active Process Detection: Das Tool nutzt Windows-API-Aufrufe (user32.dll), um zu prüfen, ob das Zielspiel (z.B. Apex Legends) gerade läuft. Schreibvorgänge werden blockiert, um Dateikorruption zu verhindern.
- Conflict Resolution: Vor dem Erstellen einer Config prüft das System asynchron gegen die Cloud-Datenbank, ob der Name bereits vergeben ist.

⚡ 4. Performance & Metadata
Statt bei jedem Start alle Config-Dateien herunterzuladen (was langsam wäre), nutzt das System eine Lightweight-Architektur:
- Metadata-First: Beim Upload wird eine winzige info.json (Größe, Erstelldatum) generiert. Das UI lädt nur diese JSON-Dateien, um die Liste in Millisekunden anzuzeigen.
- Aggressive Caching: Dateilisten werden lokal gecached und nur bei Bedarf (oder User-Refresh) neu via API abgerufen.

🎮 Dynamic Game Detection
Das Tool liest eine externe JSON-Konfiguration (game_paths.json), um installierte Spiele automatisch zu finden. Es unterstützt:
- SteamID Wildcards ({UserID}/730/local/cfg)
- Verschiedene Windows-Pfade (AppData, Documents, SteamApps)

🛠️ Tech Stack
Engine: Unity 2022 (C#)
- Backend: Supabase (PostgreSQL, Storage Buckets)
- Authentication: Steamworks.NET (SteamID based auth)
- UI: Unity uGUI with Async Feedback Loops
