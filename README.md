# ☁️ ConfigSave – Cloud Save System

> ⚠️ **Portfolio-Showcase:** Der vollständige Quellcode von ConfigSave bleibt privat und Closed Source, da die Anwendung Teil eines kommerziellen Produkts ist. Dieses Repository zeigt Produktkonzept, Architektur, UI-Design sowie ausgewählte Sicherheits- und Betriebsmechanismen — ohne Zugangsdaten, Secrets oder produktionskritische Implementierungsdetails.

**ConfigSave** ist ein Windows-Tool für unterstützte Steam-Spiele. Nutzer können lokale Spielkonfigurationen als benannte Cloud-Konfigurationen sichern, verwalten und auf einem anderen Windows-PC wiederherstellen.

ConfigSave wird unabhängig von Steam veröffentlicht und über die eigene Website vertrieben. Der Checkout erfolgt über Stripe. Nach erfolgreicher Zahlung wird Premium an die SteamID64 des Käufers gebunden. Für die Nutzung muss Steam lokal aktiv sein; der angemeldete Steam-Account muss mit dem Account übereinstimmen, dem die Lizenz zugeordnet ist.

## 🖼️ Demo-Showcase

<img src="https://github.com/user-attachments/assets/76a43733-5d76-42b7-a5ff-debf196a4fab" width="100%" alt="ConfigSave: Upload-Ablauf" />

*Upload einer Konfiguration mit automatischer Dateierkennung, Sicherheitsprüfungen und direktem UI-Feedback.*

<img width="3063" height="1892" alt="ConfigSave: Benutzeroberfläche" src="https://github.com/user-attachments/assets/ea166ce9-632f-49d2-9a9e-b0b07a197619" />

<img width="2291" height="1823" alt="ConfigSave: Konfigurationsverwaltung" src="https://github.com/user-attachments/assets/5a49a975-2c28-4cc5-8b73-d5af1dadeca1" />

## 🔗 Links

- **ConfigSave Premium:** [configsave.com](https://configsave.com/)
- **Download:** [configsave.com/download](https://configsave.com/download)
- **Unterstützte Spiele:** [configsave.com/supported-games.html](https://configsave.com/supported-games.html)
- **FAQ:** [configsave.com/faq.html](https://configsave.com/faq.html)

## 💡 Kernfunktionen

### ☁️ 1. Cloud-Konfigurationen

ConfigSave sichert unterstützte lokale Spieleinstellungen als benannte Cloud-Konfigurationen.

- Konfigurationen erstellen, laden, anwenden, umbenennen und löschen
- Wiederherstellung bevorzugter Einstellungen nach einem PC-Wechsel oder einer Neuinstallation
- Unterstützung rekursiver Ordner- und Dateistrukturen
- Cloud-Dateien werden logisch pro Nutzer getrennt gespeichert
- Bis zu 50 gespeicherte Konfigurationen pro Nutzer

### 🧠 2. Metadaten, Caching und schnelle Listen

ConfigSave vermeidet unnötige Downloads vollständiger Konfigurationsarchive.

- **Metadata-first:** Beim Upload wird eine schlanke Metadatendatei mit relevanten Informationen erstellt
- **Schnelle Listenansicht:** Gespeicherte Konfigurationen können ohne vollständigen Download dargestellt werden
- **Lokales Caching:** Dateilisten werden lokal vorgehalten und bei Bedarf oder manuellem Refresh aktualisiert
- **Asynchrones UI:** Ladezustände, erfolgreiche Aktionen und Fehler werden direkt im Client angezeigt

### 🎮 3. Erweiterbare Spieleprofile

Unterstützte Spiele und Konfigurationspfade werden über eine zentrale Konfigurationsdatei verwaltet.

- Unterstützung typischer Windows-Pfade wie `AppData`, `Documents`, Steam-Bibliotheken und spielbezogener Konfigurationsordner
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

ConfigSave verwendet keine SteamPipe-Veröffentlichung und kein Steam-DRM als Kauf- oder Lizenzsystem. Stattdessen verwendet die Anwendung eine eigene, serverseitig kontrollierte Lizenzprüfung mit SteamID64-Bindung.

1. ConfigSave erkennt, ob Steam lokal aktiv ist.
2. Der Client ermittelt die SteamID64 des aktuell angemeldeten Steam-Accounts sowie verfügbare Kontoinformationen.
3. Die App fragt einen serverseitigen Lizenz-Endpunkt ab.
4. Der Server prüft die SteamID64 gegen den aktuellen Lizenzstatus.
5. Lizenzpflichtige Cloud-Funktionen werden nur bei gültiger Lizenz freigegeben.

Die SteamID64 dient als Accountbindung und nicht als frei übertragbarer Lizenzschlüssel.

- Steam muss aktiv sein
- Der angemeldete Steam-Account muss der Lizenz zugeordnet sein
- Der Client zeigt Steam-Name, Lizenztyp und Status an
- Die Lizenzprüfung bleibt serverseitig kontrolliert
- ConfigSave fordert keine Steam-Passwörter, Steam-Guard-Codes oder sonstigen Steam-Login-Daten an

### 💳 6. Eigener Checkout und Stripe-Zahlungen

Premium wird über die ConfigSave-Website verkauft.

- Eigene Pricing- und Checkout-Oberfläche
- Steam-Profil, Custom URL oder SteamID64 wird vor dem Checkout geprüft und der Bestellung zugeordnet
- Stripe verarbeitet den Zahlungs- und Subscription-Workflow
- Serverseitige Webhooks aktualisieren Lizenz- und Zahlungsstatus idempotent
- Checkout-Session, Stripe-Kunde und Subscription werden der jeweiligen Lizenz zugeordnet
- Payment-Events werden separat protokolliert, um eine doppelte Webhook-Verarbeitung zu verhindern

### 📅 7. Subscription-Lebenszyklus im Client

Die Anwendung übersetzt serverseitige Lizenzzustände in klare Nutzerinformationen.

| Lizenzzustand | Verhalten in ConfigSave |
|---|---|
| `active` | Cloud-Funktionen verfügbar; nächster Verlängerungstermin sichtbar |
| `canceled` | Zugriff bis zum Ablaufdatum; Anzeige „Access until“ |
| `past_due` | Statushinweis „Next payment“ gemäß Billing-Policy |
| `expired` | Cloud-Funktionen gesperrt; Ablauf- und Aufbewahrungsstatus sichtbar |
| `refunded` | Cloud-Funktionen gesperrt; Erstattungsstatus sichtbar |
| Keine Lizenz | Kein Cloud-Zugriff; Kaufhinweis |
| Bereinigter Account | Cloud-Daten wurden nach Ende der Aufbewahrungsfrist gelöscht; Zugriff bleibt gesperrt |

### 🗂️ 8. Aufbewahrung und automatische Datenlöschung

Cloud-Konfigurationen bleiben nach Ende eines Abos nicht unbegrenzt gespeichert.

- Für abgelaufene, gekündigte oder erstattete Premium-Zugänge kann eine begrenzte Aufbewahrungsfrist gelten
- Ein geplanter Wartungsprozess prüft Konten, deren Aufbewahrungsfrist abgelaufen ist
- Bereinigungsfähige Cloud-Daten werden kontrolliert aus dem jeweiligen Nutzerbereich entfernt
- Der Bereinigungsstatus wird serverseitig dokumentiert
- Der Client kann Nutzer über abgelaufenen Zugriff und gelöschte Cloud-Daten informieren

### 📡 9. Backend-Sicherheit und Betrieb

Der Produktbetrieb kombiniert Client-Prüfungen mit serverseitiger Kontrolle.

- Supabase mit PostgreSQL, Edge Functions und Storage
- Row Level Security und serverseitige Autorisierungsprüfungen für Datenzugriffe
- Serverseitiger Lizenz-Endpunkt für die App
- Service-Role-Zugriffe bleiben ausschließlich im Backend
- Idempotente Payment-Event-Verarbeitung
- Getrennte Nutz-, Lizenz- und Zahlungsdaten
- Geplante Wartungsprozesse für Ablauf- und Bereinigungsabläufe

### ⚡ 10. Monitoring und Release-Härtung

- Sentry für Crash-Reporting und Diagnose
- Unity-IL2CPP-Release-Builds für Windows x64
- Bereinigte Release-Pakete ohne `.env`-Dateien, private Tokens, Debug-Logs oder PDB-Dateien
- Separater Inno-Setup-Installer für die Windows-Auslieferung
- Wiederholbare Build-, Lizenz-, Checkout- und Bereinigungs-Tests vor Releases

## ✅ Mit Testdaten geprüfte Produktabläufe

Die folgenden Abläufe wurden in einer kontrollierten Testumgebung mit Testdaten, Test-Zahlungen und nicht-produktiven Lizenzen geprüft:

- Erfolgreicher Checkout → aktive Lizenz in der App
- Anzeige von Steam-Persona, Plan und Verlängerungsdatum
- Verhinderung beziehungsweise Behandlung eines doppelten Kaufs
- Gekündigte Subscription mit „Access until“
- Fehlgeschlagene Zahlung mit „Next payment“
- Abgelaufene Subscription mit gesperrten Cloud-Funktionen
- Erstattung mit gesperrten Cloud-Funktionen
- Steam-Account ohne Lizenz
- Steam nicht aktiv
- Geplanter Löschtermin nach Ablauf der Aufbewahrungsfrist
- Physische Löschung von Test-Cloud-Daten nach Ablauf der Aufbewahrungsfrist
- Nachweis eines erfolgreich dokumentierten Bereinigungsstatus

## 🛠️ Tech Stack

| Bereich | Technologie |
|---|---|
| Engine | Unity 2022 LTS, C#, IL2CPP |
| Plattform | Windows x64 |
| Backend | Supabase, PostgreSQL, Edge Functions, Storage |
| Zahlungen | Stripe Checkout, Stripe Subscriptions, Webhooks |
| Lizenzsystem | Eigene SteamID64-gebundene, serverseitige Lizenzprüfung |
| Steam-Integration | Lokale Steam-Sitzungs-, SteamID64- und Persona-Erkennung |
| Monitoring | Sentry Crash Reporting |
| Windows-Integration | Windows Registry, `kernel32.dll`, Prozessprüfung |
| Installer | Inno Setup |
| Website | HTML, CSS, JavaScript |

## 🪟 Plattform-Kompatibilität

ConfigSave ist derzeit für **Windows x64** entwickelt.

Die Anwendung verwendet Windows-spezifische Funktionen für die Erkennung der lokalen Steam-Sitzung und für sicherheitsrelevante Prozessprüfungen. Andere Plattformen werden aktuell nicht unterstützt.

## 🔒 Sicherheitsnotiz

Dieses Repository enthält bewusst keine produktiven Secrets, API-Keys, Webhook-Secrets, Service-Role-Keys, privaten URLs, `.env`-Dateien oder vollständigen Produktcode. Öffentliche Showcase-Inhalte stellen keine vollständige Betriebs- oder Sicherheitsdokumentation dar.

## 🛡️ Sicherheitslücken melden

Wenn du eine mögliche Sicherheitslücke in ConfigSave gefunden hast, veröffentliche sie bitte nicht vor einer Kontaktaufnahme.

Sende den Hinweis an [support@configsave.com](mailto:support@configsave.com) mit dem Betreff `Security report`.

Bitte füge nach Möglichkeit Folgendes hinzu:

- Eine klare Beschreibung des Problems
- Schritte zur Reproduktion
- Die betroffene ConfigSave-Version, falls bekannt
- Relevante Screenshots oder nicht sensible Logs
- Eine Einschätzung der möglichen Auswirkungen, falls vorhanden

Bitte sende keine Passwörter, Zahlungsdaten, API-Keys, Zugriffstokens oder sonstige sensible personenbezogene Daten per E-Mail.

## ⚖️ Steam und Drittanbieter

ConfigSave ist ein unabhängiges Produkt und ist nicht mit Valve Corporation, Steam, Spiele-Publishern oder Spieleentwicklern verbunden, von ihnen unterstützt oder durch sie gesponsert.

Steam ist eine Marke der Valve Corporation. Alle weiteren Marken sind Eigentum der jeweiligen Rechteinhaber.

## © Lizenz

Copyright © 2026 Orhan Fevzi Karakas / ConfigSave. Alle Rechte vorbehalten.

Dieses Repository wird ausschließlich als Portfolio- und Produkt-Showcase bereitgestellt. Es wird keine Erlaubnis erteilt, die Software, Designs, Screenshots, Dokumentation oder sonstige Inhalte dieses Repositorys zu kopieren, zu verändern, zu verbreiten, unterzulizenzieren, kommerziell zu verwenden, zurückzuentwickeln oder abgeleitete Werke daraus zu erstellen.

Der vollständige Quellcode von ConfigSave ist privat und nicht Teil dieses Repositorys.
