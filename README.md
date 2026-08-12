# ☁️ ConfigSave – Cloud Save System (Showcase)

> ⚠️ **Hinweis:** Dies ist ein Portfolio-Showcase. Der vollständige Quellcode ist aktuell **private / closed source**, da er Teil eines kommerziellen Prototyps ist. Dieses Repository demonstriert Architektur, UI-Design und ausgewählte Sicherheitsmechanismen.

**ConfigSave** ist ein Unity-Tool, mit dem Spieler lokale Konfigurationsdateien sicher in der Cloud speichern und zwischen PCs synchronisieren können. Es erkennt komplexe Ordner- und Dateistrukturen rekursiv und verwaltet diese über **Supabase**.

Die Nutzung ist an den Steam-Account des Nutzers gebunden: Steam muss aktiv sein und der Nutzer muss mit dem lizenzierten Steam-Account angemeldet sein.

---

## 🖼️ Demo Showcase
<img src="https://github.com/user-attachments/assets/76a43733-5d76-42b7-a5ff-debf196a4fab" width="100%" />
*Live-Demo: Upload einer Konfiguration mit automatischer Erkennung, Sicherheitsprüfungen und direktem UI-Feedback.*

🖼️ GIF Showcase
<div align="center">
  <img src="[https://github.com/user-attachments/assets/76a43733-5d76-42b7-a5ff-debf196a4fab]" width="100%" />
  <img src="https://github.com/user-attachments/assets/76a43733-5d76-42b7-a5ff-debf196a4fab" width="100%" />
</div>

## 🔗 Links

- **Get it now:** [configsave.com/steam](https://configsave.com/steam)
- **Website:** [configsave.com](https://configsave.com/)

---

## 💡 Implementierte Kernfunktionen

### 🛡️ 1. Serverseitige Sicherheit mit Row Level Security

Anstatt dem Client vollständig zu vertrauen, werden zentrale Sicherheitsregeln direkt auf Datenbankebene in PostgreSQL bzw. Supabase durchgesetzt.

- **Hard Slot Limit:** Ein Nutzer kann maximal **50 Konfigurationen** besitzen. Weitere Uploads werden direkt auf SQL-Ebene blockiert.
- **Anti-Malware Policy:** Datenbankregeln verhindern den Upload ausführbarer oder potenziell gefährlicher Dateien wie `.exe` und `.bat`.
- **Validierte Dateitypen:** Uploads sind auf definierte und erwartete Konfigurationsdateien beschränkt.

### 🧠 2. Smart Rate Limiting

Zum Schutz vor API-Missbrauch und wiederholten Upload-Versuchen wird ein zweistufiges Rate-Limiting-System eingesetzt.

- **Client-seitig:** Lokales Tracking über `PlayerPrefs` sperrt den Upload-Button temporär nach zu vielen Anfragen.
- **Server-seitig:** Die Datenbank bzw. Backend-Regeln weisen Anfragen ab, wenn Stundenlimits oder Quotas überschritten sind.

### 🛡️ 3. Safety & Integrity Checks

Das Tool reduziert das Risiko von Datenverlust und beschädigten Konfigurationsdateien durch clientseitige Schutzmechanismen.

- **Active Process Detection:** Über Windows-API-Aufrufe mit `user32.dll` wird geprüft, ob ein Zielspiel — etwa Apex Legends — aktiv läuft. Schreibvorgänge werden in diesem Fall blockiert, um Dateikorruption zu vermeiden.
- **Conflict Resolution:** Vor dem Erstellen einer Konfiguration wird asynchron geprüft, ob bereits eine Cloud-Konfiguration mit demselben Namen existiert.
- **Gezielte Nutzer-Rückmeldung:** Das UI zeigt Ladezustände, Sperren, Fehler und erfolgreiche Vorgänge direkt an.

### ⚡ 4. Performance & Metadata-Architektur

ConfigSave lädt nicht bei jedem Start komplette Konfigurationsarchive herunter. Stattdessen wird eine schlanke Metadaten-Architektur verwendet.

- **Metadata-First:** Beim Upload wird eine kompakte `info.json` mit Metadaten wie Größe und Erstellungsdatum erzeugt.
- **Schnelle Listenansicht:** Das UI lädt zunächst nur die Metadaten und kann gespeicherte Konfigurationen dadurch in Millisekunden anzeigen.
- **Aggressives Caching:** Dateilisten werden lokal gespeichert und nur bei Bedarf oder nach einem manuellen Refresh erneut über die API geladen.

### 🎮 5. Dynamische Spielerkennung

Das Tool liest eine externe Konfiguration aus `game_paths.json`, um unterstützte Spiele und Konfigurationspfade automatisch zu erkennen.

Unterstützt werden unter anderem:

- **SteamID-Wildcards:** Automatische Auflösung accountbezogener Pfade, beispielsweise `{UserID}/730/local/cfg`.
- **Flexible Speicherorte:** Unterstützung für typische Windows-Pfade wie `AppData`, `Documents` und `SteamApps`.
- **Erweiterbare Spielprofile:** Neue Spiele und Pfadstrukturen können über JSON ergänzt werden, ohne die Anwendung neu kompilieren zu müssen.

### 🔐 6. SteamID-gebundene Lizenzprüfung

ConfigSave verwendet eine Steam-basierte Accountbindung für die Lizenzprüfung.

- **Steam-Account-Erkennung:** Die Anwendung ermittelt die SteamID des aktuell angemeldeten Nutzers.
- **Accountgebundene Lizenz:** Eine Lizenz ist einem konkreten Steam-Account zugeordnet und nicht frei auf andere Nutzer übertragbar.
- **Steam-Anmeldung erforderlich:** Zur Nutzung muss Steam aktiv sein und der Nutzer muss mit dem Steam-Account angemeldet sein, für den die Lizenz aktiviert wurde.
- **Lizenzstatus im Client:** Der Client zeigt Lizenztyp und gegebenenfalls das Ablaufdatum einer Subscription an.
- **Schutz vor einfachem Missbrauch:** Die Prüfung verhindert, dass ein Kauf ohne Weiteres mit beliebigen Steam-Accounts verwendet wird.

> Die SteamID-gebundene Lizenzprüfung ersetzt eine reine Steam-DRM-Logik. Der Fokus liegt auf der Bindung einer kommerziellen Lizenz an einen eindeutig angemeldeten Steam-Account.

### 📡 7. Monitoring, Stabilität und Manipulationsschutz

Das Projekt ist auf einen kommerziellen Live-Betrieb ausgelegt und enthält mehrere Maßnahmen für Diagnose, Stabilität und Schutz.

- **Crash Reporting mit Sentry:** Sentry.io ist integriert, einschließlich IL2CPP-Symbol-Upload. Dadurch bleiben Abstürze auch im kompilierten C++-Build nachvollziehbar.
- **Steam-Initialisierung:** Die Anwendung prüft beim Start, ob Steam verfügbar und aktiv ist.
- **Manipulationsschutz:** Zusätzliche Prüfungen erkennen verdächtige Änderungen an relevanten Steam-Dateien, etwa eine manipulierte `steam_appid.txt`, sowie typische DLL-Injection-Szenarien.
- **IL2CPP-Build:** Das Unity-Projekt verwendet IL2CPP, um den C#-Code für den Release zu kompilieren und die direkte Einsicht in den Anwendungscode zu erschweren.

---

## 🛠️ Tech Stack

| Bereich | Technologie |
|---|---|
| Engine | Unity 2022 LTS, C#, IL2CPP |
| Backend | Supabase, PostgreSQL, Storage Buckets, Row Level Security |
| Lizenzbindung | Steamworks.NET, SteamID-Integration |
| Monitoring | Sentry, automatisiertes Crash Reporting |
| Betriebssystem-Integration | Windows Native APIs, `user32.dll`, Registry |
| UI | Unity uGUI mit asynchronen Feedback-Loops |

---

## 🪟 Plattform-Kompatibilität

ConfigSave nutzt native Windows-Bibliotheken, unter anderem `user32.dll`, für die Prozesserkennung. Daher ist das Tool derzeit ausschließlich für **Windows-Systeme** konzipiert.
