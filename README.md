# ☁️ ConfigSave – Cloud Save System (Showcase)

> ⚠️ **Portfolio-Showcase.** Der vollständige Quellcode bleibt private / closed source, da ConfigSave Teil eines kommerziellen Produkts ist. Dieses Repository zeigt Produktkonzept, Architektur, UI-Design und ausgewählte Sicherheits- sowie Betriebsmechanismen — ohne Zugangsdaten, Secrets oder produktionskritische Implementierungsdetails.

**ConfigSave** ist ein Windows-Tool für unterstützte Steam-Spiele. Nutzer können lokale Konfigurationsdateien als benannte Cloud-Konfigurationen sichern, verwalten und auf einem anderen PC wiederherstellen.

Die aktuelle Version wird unabhängig von Steam veröffentlicht und über die eigene Website vertrieben. Der Checkout erfolgt über Stripe. Die Lizenz wird nach erfolgreicher Zahlung an die SteamID64 des Käufers gebunden. Steam muss beim Verwenden von ConfigSave laufen und der lokal angemeldete Steam-Account muss mit dem lizenzierten Account übereinstimmen.

---

## 🖼️ Demo Showcase

<img src="https://github.com/user-attachments/assets/76a43733-5d76-42b7-a5ff-debf196a4fab" width="100%" alt="ConfigSave upload workflow" />

*Upload einer Konfiguration mit automatischer Dateierkennung, Sicherheitsprüfungen und direktem UI-Feedback.*

<img width="3063" height="1892" alt="ConfigSave interface overview" src="https://github.com/user-attachments/assets/ea166ce9-632f-49d2-9a9e-b0b07a197619" />

<img width="2291" height="1823" alt="ConfigSave configuration management" src="https://github.com/user-attachments/assets/5a49a975-2c28-4cc5-8b73-d5af1dadeca1" />

---

## 🔗 Links

- **Get Premium:** [configsave.com](https://configsave.com/)
- **Download:** [configsave.com/download](https://configsave.com/download)
- **Supported games:** [configsave.com/supported-games.html](https://configsave.com/supported-games.html)
- **FAQ:** [configsave.com/faq.html](https://configsave.com/faq.html)

---

## 💡 Kernfunktionen

### ☁️ 1. Cloud-Konfigurationen

ConfigSave sichert unterstützte lokale Spieleinstellungen als benannte Cloud-Konfigurationen.

- Konfigurationen erstellen, laden, anwenden, umbenennen und löschen
- Wiederherstellung der bevorzugten Einstellungen nach PC-Wechsel oder Neuinstallation
- Unterstützung rekursiver Ordner- und Dateistrukturen
- Cloud-Dateien werden im Storage-Bereich pro Nutzer getrennt gespeichert
- Bis zu 50 gespeicherte Konfigurationen pro Nutzer

### 🧠 2. Metadaten, Caching und schnelle Listen

ConfigSave vermeidet unnötige Downloads kompletter Konfigurationsarchive.

- **Metadata-first:** Beim Upload wird eine schlanke `info.json` mit relevanten Metadaten erstellt
- **Schnelle Listenansicht:** Gespeicherte Konfigurationen lassen sich ohne vollständigen Download darstellen
- **Lokales Caching:** Dateilisten werden lokal gehalten und bei Bedarf oder manuellem Refresh aktualisiert
- **Asynchrones UI:** Ladezustände, erfolgreiche Aktionen und Fehler werden direkt im Client angezeigt

### 🎮 3. Erweiterbare Spieleprofile

Unterstützte Spiele und Konfigurationspfade werden über `game_paths.json` verwaltet.

- Unterstützung typischer Windows-Pfade wie `AppData`, `Documents`, Steam-Bibliotheken und spielbezogene Konfigurationsordner
- SteamID-bezogene Pfadvariablen für accountabhängige Speicherorte
- Neue Spiele und Pfadstrukturen können über Konfiguration ergänzt werden
- Keine erneute Kompilierung der Anwendung erforderlich, wenn nur Spieleprofile ergänzt werden

### 🛡️ 4. Schutz vor Datenverlust

Mehrere clientseitige Prüfungen reduzieren das Risiko beschädigter oder unvollständiger Konfigurationsstände.

- Aktive Zielspiele werden vor kritischen Schreibvorgängen erkannt
- Schreib- oder Wiederherstellungsvorgänge können blockiert werden, solange ein betroffenes Spiel läuft
- Prüfung auf doppelte Konfigurationsnamen
- Validierung von Konfigurationsnamen und erwarteten Dateitypen
- Verständliche UI-Meldungen bei Sperren, Konflikten und Fehlern

### 🔐 5. Eigene Lizenzprüfung mit SteamID64-Bindung

ConfigSave verwendet keine SteamPipe-Veröffentlichung und kein Steam-DRM als Kauf- oder Lizenzsystem. Stattdessen besitzt die Anwendung eine eigene SteamID64-gebundene Lizenzprüfung.

1. ConfigSave erkennt, ob Steam lokal läuft.
2. Der Client liest die SteamID64 des aktuell angemeldeten Steam-Accounts sowie den lokalen Persona-Namen.
3. Die App fragt einen serverseitigen Lizenz-Endpunkt ab.
4. Der Server prüft die SteamID64 gegen den Lizenzstatus.
5. Lizenzpflichtige Cloud-Funktionen werden nur bei gültiger Lizenz freigegeben.

Die SteamID64 dient als Accountbindung, nicht als frei übertragbarer Lizenzschlüssel.

- Steam muss aktiv sein
- Der angemeldete Steam-Account muss der Lizenz zugeordnet sein
- Der Client zeigt Steam-Name, Lizenztyp und Status an
- Die Lizenzprüfung bleibt serverseitig kontrolliert

### 💳 6. Eigener Checkout und Stripe-Zahlungen

Premium wird über die ConfigSave-Website verkauft.

- Eigene Pricing- und Checkout-Oberfläche
- Steam-Profil, Custom URL oder SteamID64 wird vor dem Checkout geprüft und der Bestellung zugeordnet
- Stripe verarbeitet den Zahlungs- und Subscription-Workflow
- Serverseitige Webhooks aktualisieren Lizenz- und Zahlungsstatus idempotent
- Checkout-Session, Stripe-Kunde und Subscription werden für die jeweilige Lizenz verknüpft
- Payment-Events werden separat protokolliert, um doppelte Webhook-Verarbeitung zu verhindern

### 📅 7. Subscription-Lebenszyklus im Client

Die Anwendung übersetzt serverseitige Lizenzzustände in klare Nutzerinformationen.

| Lizenzzustand | Verhalten in ConfigSave |
|---|---|
| `active` | Cloud-Funktionen verfügbar; nächster Verlängerungstermin sichtbar |
| `canceled` | Zugriff bis Ablaufdatum; Anzeige `Access until` |
| `past_due` | Statushinweis `Next payment` gemäß Billing-Policy |
| `expired` | Cloud-Funktionen gesperrt; Ablauf- und Aufbewahrungsdatum sichtbar |
| `refunded` | Cloud-Funktionen gesperrt; Erstattungsstatus sichtbar |
| Keine Lizenz | Kein Cloud-Zugriff; Kaufhinweis |
| `purged_at` gesetzt | Cloud-Daten wurden bereinigt; Zugriff bleibt gesperrt |

### 🗂️ 8. Aufbewahrung und automatische Datenlöschung

Cloud-Konfigurationen bleiben nach Ende eines Abos nicht unbegrenzt gespeichert.

- Der Lizenzstatus kann ein `delete_after`-Datum enthalten
- Ein geplanter Wartungsprozess prüft bereinigungsfähige Konten
- Nach Ende der Aufbewahrungsfrist löscht der Prozess ausschließlich die Dateien des betreffenden Nutzers im Storage-Bucket `userdata`
- Der Löschvorgang wird über `purge_started_at` und `purged_at` nachvollziehbar dokumentiert
- Der Client informiert Nutzer, wenn gespeicherte Cloud-Konfigurationen gelöscht wurden

### 📡 9. Backend-Sicherheit und Betrieb

Der Produktbetrieb kombiniert Client-Prüfungen mit serverseitiger Kontrolle.

- Supabase mit PostgreSQL, Edge Functions und Storage
- Row Level Security für Datenzugriffe
- Serverseitiger Lizenz-Endpunkt für die App
- Service-Role-Zugriffe bleiben ausschließlich im Backend
- Idempotente Payment-Event-Verarbeitung
- Getrennte Nutz-, Lizenz- und Zahlungsdaten
- Geplante Wartung für Ablauf- und Löschprozesse

### ⚡ 10. Monitoring und Release-Härtung

- Sentry für Crash Reporting und Diagnose
- Unity IL2CPP-Release-Builds für Windows x64
- Bereinigte Release-Pakete ohne `.env`-Dateien, private Tokens, Debug-Logs oder PDB-Dateien
- Separater Inno-Setup-Installer für die Windows-Auslieferung
- Wiederholbare Build-, Lizenz-, Checkout- und Purge-Tests vor Releases

---

## ✅ Ende-zu-Ende getestet

Die finale Produktstrecke wurde mit Testdaten geprüft:

- Erfolgreicher Checkout → aktive Lizenz in der App
- Anzeige von Steam-Persona, Plan und Verlängerungsdatum
- Verhinderung bzw. Behandlung eines doppelten Kaufs
- Gekündigte Subscription mit `Access until`
- Fehlgeschlagene Zahlung mit `Next payment`
- Abgelaufene Subscription mit gesperrten Cloud-Funktionen
- Erstattung mit gesperrten Cloud-Funktionen
- Steam-Account ohne Lizenz
- Steam nicht aktiv
- Geplanter Löschtermin nach Ablauf
- Physische Löschung der Testdateien im Storage-Bucket `userdata`
- Nachweis des bereinigten Lizenzstatus über `purge_started_at` und `purged_at`

---

## 🛠️ Tech Stack

| Bereich | Technologie |
|---|---|
| Engine | Unity 2022 LTS, C#, IL2CPP |
| Plattform | Windows x64 |
| Backend | Supabase, PostgreSQL, Edge Functions, Storage |
| Zahlungen | Stripe Checkout, Stripe Subscriptions, Webhooks |
| Lizenzsystem | Eigene SteamID64-gebundene serverseitige Lizenzprüfung |
| Steam-Integration | Lokale Steam-Sitzungs-, SteamID64- und Persona-Erkennung |
| Monitoring | Sentry Crash Reporting |
| Windows-Integration | Windows Registry, `kernel32.dll`, Prozessprüfung |
| Installer | Inno Setup |
| Website | HTML, CSS, JavaScript |

---

## 🪟 Plattform-Kompatibilität

ConfigSave ist derzeit für **Windows x64** entwickelt.

Die Anwendung verwendet Windows-spezifische Funktionen für die Erkennung der lokalen Steam-Sitzung und für sicherheitsrelevante Prozessprüfungen. Andere Plattformen werden aktuell nicht unterstützt.

---

## 🔒 Sicherheitsnotiz

Dieses Repository enthält bewusst keine produktiven Secrets, API-Keys, Webhook-Secrets, Service-Role-Keys, privaten URLs, `.env`-Dateien oder vollständigen Produktcode. Öffentliche Showcase-Inhalte stellen keine vollständige Betriebs- oder Sicherheitsdokumentation dar.
