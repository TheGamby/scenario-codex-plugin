# Scenario Codex Plugin - Phasenplan V0.2

## Zielbild

V0.2 erweitert das bestehende Scenario Codex Plugin von einem validierten
Minimal-Plugin zu einer vollstaendigeren, sicher gefuehrten Arbeitsanweisung
fuer den offiziellen Scenario MCP. Der technische Grundsatz bleibt unveraendert:
Das Plugin baut keine eigene Scenario-API-Integration und speichert keine
Secrets. Codex nutzt weiterhin den offiziellen Scenario MCP als Schnittstelle.

Der Fokus liegt auf:

- vollstaendigerer Abdeckung der live verfuegbaren Scenario-MCP-Tools
- einer klaren Safety- und Risiko-Matrix fuer read-only, kostenpflichtige,
  mutierende und destruktive Aktionen
- Budget-Management mit Vorschau, Freigaben und Reporting
- reproduzierbaren Beispiel-Workflows fuer Game-Art-Aufgaben
- besserer Wartbarkeit bei Scenario-MCP-Schemaaenderungen

## Abnahmebedingung

V0.2 gilt als abgenommen, wenn:

- alle live verfuegbaren Scenario-MCP-Tools in README und Skill mindestens
  klassifiziert sind
- Budget- und Freigaberegeln fuer kostenpflichtige Aktionen dokumentiert sind
- mindestens ein Budget-Dry-run-Workflow und ein Budget-Reporting-Workflow
  erfolgreich getestet wurden
- Upload-, Analyze-, Training-, Collection-, Asset-, Model-, Workflow- und
  Usage-Workflows mindestens als sichere Beispielablaeufe beschrieben sind
- Plugin-Validator und JSON/TOML-Syntaxpruefungen erfolgreich sind
- keine Secrets, OAuth-Tokens, API-Keys oder account-spezifischen Team-/Projekt-
  IDs im Repo liegen
- README und Skill den finalen V0.2-Stand beschreiben
- der finale Stand committed, getaggt und gepusht ist

## Nicht-Ziele fuer V0.2

- Keine eigene Scenario-REST-API-Integration.
- Kein eigener Asset-Viewer.
- Kein automatischer Upload-, Trainings- oder Workflow-Autopilot.
- Keine gespeicherten Scenario-Secrets.
- Keine account-spezifischen IDs oder lokalen Pfade im Public Repo.
- Keine automatische echte Generation ohne explizite Nutzerfreigabe.
- Kein Ersatz fuer Scenario-Billing; Budget-Management bleibt eine
  Codex-seitige Vorschau-, Freigabe- und Reporting-Schicht ueber MCP-Daten.

## Phase 0 - Live-MCP-Inventar aktualisieren

Ziel: Den aktuellen Scenario-MCP-Toolbestand als Grundlage fuer V0.2 erfassen.

Aufgaben:

- `tools/list` ueber `mcp-remote` erneut ausfuehren.
- Toolnamen, Titel, Required-Felder, Read-only-Hints, Destructive-Hints und
  relevante Parameter erfassen.
- Aktuelle Toolliste gegen den V0.1.1-Stand vergleichen.
- Ergebnis in einer neuen Dokumentation festhalten, z. B.
  `planing/scenario-mcp-tool-inventory-v0.2.md`.

Akzeptanzkriterien:

- Alle live gefundenen Tools sind aufgelistet.
- Neue, entfernte oder geaenderte Tools sind markiert.
- Keine account-spezifischen Werte werden committed.

Risiken:

- Scenario MCP ist beta; Tools koennen sich kurzfristig aendern.
- Einzelne Tool-Beschreibungen koennen sehr lang sein und muessen verdichtet
  werden.

## Phase 1 - Tool-Matrix und Risiko-Klassen

Ziel: Jede MCP-Funktion einer klaren Codex-Verhaltensklasse zuordnen.

Vorgeschlagene Klassen:

- `context-read`: sichere Kontextabfragen wie Teams, Projekte, Schema, Usage.
- `discovery-read`: Suche, Empfehlung, Planung, Analyse ohne neue Assets.
- `cost-preview`: Dry-run oder Usage-Abfrage zur Kostenvorschau.
- `paid-generate`: kostenpflichtige Generierung oder Analyse, die Jobs/Assets
  erzeugen kann.
- `mutating-safe`: mutierende Aktionen ohne destruktive Loeschung, z. B. Upload
  oder Tag-Ergaenzung.
- `destructive`: Loeschen, Uebertragen, Entfernen, Workflow-Aenderungen,
  Collection-/Model-/Asset-Mutationen mit Risiko.
- `training`: Training und Trainingsdatenverwaltung.
- `app-only`: Tools, die nur in einem MCP-App-Kontext sinnvoll sind.

Aufgaben:

- Matrix fuer alle live Tools erstellen.
- Fuer jede Klasse Standardverhalten definieren:
  - ob eine explizite Nutzerfreigabe erforderlich ist
  - ob vorher ein Dry-run erforderlich ist
  - welche IDs und Kontextdaten bestaetigt werden muessen
  - wie Ergebnisse berichtet werden
- Matrix in README oder eigener Referenzdatei dokumentieren.
- Skill-Regeln an die Matrix anpassen.

Akzeptanzkriterien:

- Jedes live Tool ist genau einer Primaerklasse zugeordnet.
- Kostenpflichtige, mutierende, destruktive und Trainingsaktionen haben klare
  Freigaberegeln.
- Read-only-Workflows bleiben schnell und ohne unnoetige Rueckfragen nutzbar.

Risiken:

- MCP-Annotations koennen nicht alle realen Risiken ausdruecken.
- Zu strenge Freigaben koennen einfache Workflows bremsen.

## Phase 2 - Budget-Management: Vorschau und Freigaben

Ziel: Kostenpflichtige Aktionen bekommen eine nachvollziehbare Vorschau und eine
explizite Freigabelogik.

Budget-Regeln:

- Vor `run_model` soll wenn moeglich `dry_run=true` ausgefuehrt werden.
- Vor `train` soll wenn moeglich ein Training-Dry-run oder mindestens
  `recommend_training` plus Konfigurationszusammenfassung erfolgen.
- Vor `manage_workflows` mit Ausfuehrung soll wenn moeglich `dry_run=true`
  genutzt werden.
- Wenn kein Dry-run verfuegbar ist, muss Codex das deutlich sagen und eine
  explizite Freigabe einholen.
- Codex soll nie stillschweigend mehrere kostenpflichtige Varianten starten.

Aufgaben:

- Budget-Freigabe-Template definieren:
  - Team/Projekt
  - Tool und Modell/Workflow
  - Parameterzusammenfassung
  - geschaetzte Creative Units oder Hinweis, dass keine Schaetzung verfuegbar
    ist
  - erwartete Outputs
  - Abbruch-/Statusstrategie
- README um Budget-Workflow ergaenzen.
- Skill-Regeln um Budget-Vorschau/Freigaben ergaenzen.
- Beispielprompt fuer Budget-Dry-run ergaenzen.

Akzeptanzkriterien:

- Codex kann einen Budget-Dry-run fuer eine kleine Generation planen und
  berichten.
- Codex fragt vor dem echten Start nach Freigabe, wenn der Nutzer nicht schon
  explizit die echte Ausfuehrung erlaubt hat.
- Kosten, Job-ID und Asset-IDs werden nach Ausfuehrung berichtet.

Risiken:

- Scenario MCP kann Kosten anders abrechnen als Dry-run erwartet.
- Manche Tools liefern keine Kostenprognose.
- Mehrstufige Workflows koennen Kosten aus mehreren Jobs enthalten.

## Phase 3 - Budget-Reporting und Usage-Auswertung

Ziel: Nutzer koennen nach einem Workflow und fuer Zeitraeume nachvollziehen, was
verbraucht wurde.

Aufgaben:

- `usage`-Tool dokumentieren:
  - Zeitraum
  - Team/Projekt
  - `include`-Optionen
  - kompakte vs. detaillierte Ausgabe
- Reporting-Format definieren:
  - Zeitraum
  - Gesamte Creative Units
  - relevante Modelle/Jobs
  - Asset-Typen
  - Hinweise auf fehlende oder aggregierte Daten
- Workflow dokumentieren:
  - vor einem Job: Dry-run-Kosten
  - nach einem Job: Job-Billing aus `manage_jobs`
  - fuer Zeitraum: `usage`
- Skill-Regeln fuer Kostenberichte ergaenzen.

Akzeptanzkriterien:

- Ein read-only Usage-Report kann fuer einen kurzen Zeitraum erzeugt werden.
- Ein Job-Billing kann ueber `manage_jobs` geprueft und berichtet werden.
- README beschreibt, wann `usage` und wann `manage_jobs` zu verwenden ist.

Risiken:

- Usage-Daten koennen verzoegert oder aggregiert sein.
- Account-/Projektberechtigungen koennen Details ausblenden.

## Phase 4 - Upload- und Asset-Workflows

Ziel: Uploads und Asset-Verwaltung sicher dokumentieren, ohne Autopilot.

Aufgaben:

- `upload_asset` und `complete_upload` Ablauf dokumentieren.
- `manage_assets` Aktionen klassifizieren:
  - read/list/get falls vorhanden
  - Tagging
  - Metadata
  - Delete/Batch-Aktionen
- Regeln fuer lokale Dateien:
  - keine Uploads ohne explizite Dateiauswahl
  - keine grossen Dateien ohne Groessenhinweis
  - keine sensiblen Dateien hochladen
- Beispiel-Workflow fuer ein Bild-Asset vorbereiten.

Akzeptanzkriterien:

- Skill verhindert Uploads als Nebenwirkung von Planung oder Analyse.
- README erklaert Upload/Complete als zweistufigen Ablauf.
- Destruktive Asset-Aktionen verlangen ausdrueckliche Freigabe.

Risiken:

- Upload-Flows koennen je nach MCP-Version anders aussehen.
- Lokale Dateipfade duerfen nicht ins Public Repo gelangen.

## Phase 5 - Analyze-Workflows

Ziel: Bildanalyse als read-only oder kostenbewusster Workflow sauber fuehren.

Aufgaben:

- `analyze` Toolvarianten klassifizieren.
- Klaeren, welche Analyze-Aktionen Dry-run oder Kosten verursachen koennen.
- Beispiel fuer Bildanalyse mit bestehenden Scenario-Asset-IDs dokumentieren.
- Reporting-Format fuer Analyseergebnisse definieren.

Akzeptanzkriterien:

- Codex fragt nach Referenzbild/Asset-ID statt lokale Pfade zu raten.
- Kostenpflichtige Analyse wird vor Ausfuehrung bestaetigt oder per Dry-run
  geprueft.
- Analyseergebnisse enthalten Asset-ID, Modell/Tool und naechste Schritte.

Risiken:

- Analyse kann je nach Modell als Job laufen und Kosten verursachen.
- Ergebnisse koennen Modell-spezifisch sein.

## Phase 6 - Training-Workflows

Ziel: Training sicher vorbereiten, aber nicht automatisieren.

Aufgaben:

- `recommend_training` als erster Schritt fuer Trainingsabsichten festlegen.
- `train` Aktionen und Required-Felder dokumentieren.
- Dataset-Checkliste definieren:
  - Ziel
  - Modality
  - Dataset-Shape
  - Anzahl/Qualitaet der Bilder oder Audiodaten
  - erwartete Kosten oder Dry-run
- Freigaberegeln fuer Training definieren.
- Dokumentieren, dass kein Training ohne explizite Nutzerfreigabe gestartet
  wird.

Akzeptanzkriterien:

- Codex kann einen Trainingsplan erstellen, ohne Training zu starten.
- Training-Dry-run oder Kostenhinweis ist Teil des Plans.
- Echter Trainingsstart erfordert ausdrueckliche Freigabe.

Risiken:

- Training kann teuer und langlaufend sein.
- Dataset-Vorbereitung ist account- und projektabhaengig.

## Phase 7 - Collections, Models und Workflows verwalten

Ziel: Verwaltungstools nutzbar machen, ohne riskante Mutationen zu verstecken.

Aufgaben:

- `manage_collections` dokumentieren:
  - list/get
  - create/update
  - add/remove assets/models
  - delete
- `manage_models` dokumentieren:
  - list/get
  - update tags/metadata
  - copy/transfer/download
  - delete
- `manage_workflows` dokumentieren:
  - list/get
  - create/update
  - execute/dry-run falls verfuegbar
  - delete
- Freigaberegeln je Aktion definieren.

Akzeptanzkriterien:

- Read-only Verwaltungsaktionen sind direkt nutzbar.
- Mutierende Aktionen zeigen Vorher/Nachher-Absicht.
- Destruktive Aktionen verlangen eine eindeutige Bestaetigung mit Ziel-ID.

Risiken:

- Einige MCP-Aktionen koennen in einem einzelnen Tool sowohl read-only als auch
  destruktiv sein.
- Uebertragungen zwischen Projekten koennen schwer rueckgaengig zu machen sein.

## Phase 8 - 3D/App-only Tools

Ziel: App-only Tools korrekt einordnen.

Aufgaben:

- `capture_3d_view` und `proxy_asset_fetch` dokumentieren.
- Klarstellen, dass diese Tools nur in passenden MCP-App-/Viewer-Kontexten
  sinnvoll sind.
- Fallback-Verhalten definieren, wenn Codex keinen App-Kontext hat.

Akzeptanzkriterien:

- Codex versucht App-only Tools nicht in normalen Text-Chats ohne passenden
  Kontext zu verwenden.
- README nennt die Einschraenkung.

Risiken:

- App-Kontext und Tool-Verfuegbarkeit koennen sich je nach Codex-Version
  unterscheiden.

## Phase 9 - Tests und Validierung

Ziel: V0.2 reproduzierbar pruefen.

Aufgaben:

- JSON/TOML-Syntax pruefen.
- Plugin-Validator ausfuehren.
- `tools/list` Smoke-Test ausfuehren.
- Read-only Tests:
  - `list_teams`
  - `search`
  - `get_model_schema`
  - `usage` fuer kurzen Zeitraum
- Budget Tests:
  - `run_model dry_run`
  - `manage_jobs check` fuer einen vorhandenen Job
- Keine Tests duerfen ohne ausdrueckliche Freigabe neue Kosten verursachen.

Akzeptanzkriterien:

- Validator meldet `Plugin validation passed`.
- Tests liefern echte MCP-Antworten.
- README enthaelt die getesteten Notizen ohne Secrets oder account-spezifische
  IDs.

Risiken:

- OAuth-Session kann ablaufen.
- Usage-Daten koennen accountabhaengig sein.

## Phase 10 - Release V0.2

Ziel: Den erweiterten Skill- und Dokumentationsstand veroeffentlichen.

Aufgaben:

- Version auf `0.2.0` setzen.
- Release-Dokument `planing/release-v0.2.0.md` erstellen.
- Follow-up-Issues aktualisieren oder echte GitHub-Issues anlegen, falls die
  Rechte verfuegbar sind.
- `main` pushen.
- Tag `v0.2.0` setzen und pushen.

Akzeptanzkriterien:

- GitHub enthaelt den finalen V0.2-Stand.
- `v0.2.0` zeigt auf denselben finalen Commit.
- Lokaler Arbeitsbaum ist sauber.
- Offene Folgearbeiten sind dokumentiert.

Risiken:

- GitHub-Issue-Erstellung kann weiterhin an Integrationsrechten scheitern.
- Tag-Updates duerfen nicht versehentlich alte Release-Staende ueberschreiben.

## Vorgeschlagene Reihenfolge

1. Live-MCP-Inventar aktualisieren.
2. Tool-Matrix und Safety-Klassen erstellen.
3. Budget-Vorschau/Freigaben/Reporting umsetzen.
4. Upload, Analyze, Training, Verwaltung und App-only Workflows dokumentieren.
5. Tests ausfuehren.
6. Version `0.2.0` releasen.
