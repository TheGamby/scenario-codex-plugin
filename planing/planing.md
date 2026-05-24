# Scenario Codex Plugin - Phasenplan

## Zielbild

Dieses Repo soll ein nachvollziehbares, kleines Codex-Plugin fuer Scenario
bereitstellen. V1 verpackt den offiziellen Scenario MCP-Server, dokumentiert
OAuth als Standard-Authentifizierung und gibt Codex ueber einen Skill klare
Arbeitsregeln fuer Scenario-Workflows.

Das Plugin baut keine eigene Scenario-API-Integration nach. Der offizielle MCP
bleibt die technische Schnittstelle.

## Abnahmebedingung

Die Umsetzung gilt als abgenommen, wenn das Plugin aus dem Repo heraus in Codex
geladen werden kann, der Scenario MCP-Server ueber die konfigurierte
`mcp-remote`-Anbindung startet, der Scenario-OAuth-Flow erfolgreich
abgeschlossen wurde und mindestens eine read-only Scenario-Anfrage echte Daten
liefert. Zusaetzlich muessen JSON-Syntaxpruefungen und Plugin-Validator
erfolgreich sein, das README den getesteten Installationsweg beschreiben und
der finale Stand in GitHub committed und gepusht sein.

## Phase 0 - Aktueller Stand sichern

Ziel: Den vorhandenen Scaffold sauber versionieren.

Aufgaben:

- Aktuellen Git-Status pruefen.
- Neue Dateien reviewen:
  - `.codex-plugin/plugin.json`
  - `.mcp.json`
  - `skills/scenario/SKILL.md`
  - `README.md`
  - `planing/planing.md`
- JSON-Syntax pruefen.
- Plugin-Validator ausfuehren.
- Initialen Commit erstellen.
- Remote pruefen und `main` nach GitHub pushen.

Akzeptanzkriterien:

- GitHub-Repo enthaelt den initialen Plugin-Scaffold.
- Lokaler Arbeitsbaum ist nach dem Commit sauber.
- Validator meldet `Plugin validation passed`.

Risiken:

- Der Remote-Branch `origin/main` existiert derzeit offenbar noch nicht.
- Push kann fehlschlagen, wenn das GitHub-Repo noch nicht initialisiert oder
  Berechtigungen fehlen.

## Phase 1 - Lokale Codex-Plugin-Einbindung

Ziel: Das Repo als Codex-Plugin nutzbar machen, ohne die Plugin-Dateien in ein
anderes Verzeichnis zu kopieren.

Aufgaben:

- Klaeren, welchen lokalen Installationsweg Codex fuer Repo-basierte Plugins
  erwartet.
- Falls noetig, eine lokale Marketplace-Entry separat planen oder manuell
  anlegen.
- Sicherstellen, dass Codex `.codex-plugin/plugin.json`, `.mcp.json` und
  `skills/` aus diesem Repo erkennt.
- Pruefen, ob die `mcpServers`-Referenz im Manifest fuer lokale Plugins korrekt
  aufgeloest wird.

Akzeptanzkriterien:

- Das Plugin erscheint lokal in Codex oder laesst sich eindeutig laden.
- Der Scenario-Skill ist in einer neuen Codex-Session verfuegbar.
- Keine persoenlichen Marketplace-Dateien werden ohne bewusste Entscheidung
  veraendert.

Risiken:

- Codex-Plugin-Discovery kann andere Pfadkonventionen erwarten als der aktuelle
  Repo-Root.
- Ein Marketplace-Eintrag koennte fuer die praktische Nutzung doch notwendig
  sein.

## Phase 2 - MCP-Start und OAuth validieren

Ziel: Nachweisen, dass der Scenario MCP-Server ueber die Plugin-Konfiguration
startet und OAuth funktioniert.

Aufgaben:

- Pruefen, ob Node.js und `npx` im Codex-Kontext verfuegbar sind.
- Scenario MCP ueber die konfigurierte `mcp-remote`-Command starten lassen.
- OAuth-Flow ausloesen und abschliessen.
- Eine read-only Aktion ausfuehren, zum Beispiel Teams, Projekte oder Modelle
  listen.
- README mit realen Testnotizen ergaenzen, falls sich Setup-Details zeigen.

Akzeptanzkriterien:

- MCP-Server startet ohne lokale Script-Anpassungen.
- OAuth kann abgeschlossen werden.
- Mindestens eine read-only Scenario-Anfrage liefert echte Daten.

Risiken:

- Browser-/OAuth-Handoff kann je nach Codex-Umgebung anders laufen.
- `npx -y mcp-remote@latest` benoetigt Netzwerkzugriff und npm-Verfuegbarkeit.
- Scenario MCP ist beta; Toolnamen oder Schemas koennen sich aendern.

## Phase 3 - Skill-Verhalten in echten Workflows pruefen

Ziel: Verifizieren, dass der Codex-Skill tatsaechlich zu guten Scenario-
Arbeitsablaeufen fuehrt.

Aufgaben:

- Testprompt: `List my Scenario projects.`
- Testprompt: `Find a Scenario model for game assets.`
- Testprompt: `Plan a Scenario image generation.`
- Pruefen, ob Codex zuerst Kontext und Modell-Schema ermittelt.
- Sicherstellen, dass keine Uploads, Trainings oder kostenpflichtigen Aktionen
  ohne explizite Nutzerabsicht gestartet werden.
- Skill-Text nach praktischen Erkenntnissen schaerfen.

Akzeptanzkriterien:

- Codex nutzt Scenario-MCP-Discovery vor mutierenden Aktionen.
- Modellwahl und Parameterplanung sind nachvollziehbar.
- Ergebnisse enthalten stabile Namen, IDs oder naechste Schritte.
- Write-/Training-/Upload-Aktionen werden nicht versehentlich gestartet.

Risiken:

- Der Skill ist nur eine Verhaltensanweisung; reale Toolauswahl haengt von den
  live verfuegbaren MCP-Tools ab.
- Zu strenge Regeln koennen einfache Workflows unnoetig verlangsamen.

## Phase 4 - Erste sichere Generation

Ziel: Eine kontrollierte, explizit gewuenschte Scenario-Generation Ende-zu-Ende
testen.

Aufgaben:

- Ein kleines, nicht-kritisches Testprojekt oder bestehendes Testmodell waehlen.
- Modell-Schema inspizieren.
- Prompt und Parameter aus dem Schema ableiten.
- Generation explizit starten.
- Ergebnis anzeigen oder Asset-/Job-IDs dokumentieren.
- README um einen echten Minimal-Workflow erweitern.

Akzeptanzkriterien:

- Eine Generation wird erfolgreich gestartet.
- Ergebnis ist in Codex sichtbar oder eindeutig ueber Scenario-IDs auffindbar.
- Der Workflow ist im README so dokumentiert, dass er reproduzierbar ist.

Risiken:

- Generation kann Kosten oder Credits verbrauchen.
- Ergebnisse koennen projekt- oder accountabhaengig sein.
- Lange Jobs brauchen Statusabfragen statt sofortiger Ergebnisse.

## Phase 5 - Version 0.1.1: Nutzbarkeit verbessern

Ziel: Nach den ersten realen Tests kleine Reibungen entfernen, ohne den Scope
unnuetig zu vergroessern.

Moegliche Aufgaben:

- README mit konkreter Codex-Installationsanleitung ergaenzen.
- Troubleshooting fuer OAuth, `npx`, npm-cache und Scenario-Permissions
  praezisieren.
- Skill-Regeln anhand echter Toolnamen aktualisieren.
- Optional einen lokalen Marketplace-Eintrag dokumentieren oder vorbereiten.
- Optional Beispielprompts fuer typische Game-Art-Workflows ergaenzen.

Akzeptanzkriterien:

- Neue Nutzer koennen das Plugin anhand des README lokal einrichten.
- Bekannte Fehlerbilder haben klare Diagnosepfade.
- Plugin bleibt weiterhin ohne gespeicherte Secrets nutzbar.

Risiken:

- Zu viele beispielhafte Workflows koennen veralten, wenn Scenario MCP seine
  Tools aendert.
- Marketplace-Setup kann nutzerspezifische Pfade enthalten und sollte deshalb
  nicht unbedacht ins Repo.

## Phase 6 - Optionale API-Key-Unterstuetzung

Ziel: Nur falls OAuth unpraktisch ist, einen sauberen API-Key-Modus ergaenzen.

Entscheidungspunkt:

- Diese Phase wird nur umgesetzt, wenn OAuth in Codex wiederholt stoert oder ein
  nicht-interaktiver Workflow benoetigt wird.

Moegliche Umsetzung:

- Wrapper-Script ergaenzen, das `SCENARIO_API_KEY` und
  `SCENARIO_API_SECRET` aus der Umgebung liest.
- Basic-Auth-Header zur Laufzeit erzeugen.
- Keine Secrets in `.mcp.json`, README oder Git speichern.
- README um API-Key-Setup und Sicherheitswarnungen erweitern.

Akzeptanzkriterien:

- API-Key-Modus funktioniert ohne im Repo gespeicherte Secrets.
- OAuth bleibt Default.
- Fehlende Environment-Variablen erzeugen eine klare Fehlermeldung.

Risiken:

- Mehr Komplexitaet in der MCP-Startlogik.
- Hoeheres Risiko, dass Nutzer versehentlich Secrets in Shell-History oder
  Konfigurationsdateien speichern.

## Phase 7 - Release und Wartung

Ziel: Das Plugin als kleine, wartbare Version veroeffentlichen.

Aufgaben:

- Versionsnummer nach stabiler Validierung erhoehen, falls noetig.
- Git-Tag fuer stabile Version setzen, zum Beispiel `v0.1.0`.
- GitHub-README final pruefen.
- Issues fuer bekannte offene Punkte anlegen.
- Aenderungen an Scenario MCP regelmaessig gegen Skill und README pruefen.

Akzeptanzkriterien:

- GitHub enthaelt einen nachvollziehbaren Release-Stand.
- Offene Folgearbeiten sind als Issues oder Planpunkte dokumentiert.
- Plugin bleibt schlank und delegiert Scenario-Funktionalitaet an den
  offiziellen MCP.

## Nicht-Ziele fuer V1

- Keine eigene Scenario-REST-API-Integration.
- Kein eigener Asset-Viewer.
- Kein Training- oder Upload-Autopilot.
- Keine gespeicherten Scenario-Secrets.
- Kein automatischer Eingriff in persoenliche Codex-Marketplace-Dateien.
- Keine Live-Generation als Pflichtbestandteil der Grundvalidierung.

## Empfohlene naechste Reihenfolge

1. Phase 0 abschliessen: Commit und Push.
2. Phase 1 durchfuehren: lokale Plugin-Einbindung klaeren.
3. Phase 2 validieren: MCP-Start und OAuth.
4. Phase 3 testen: read-only und Planungsworkflows.
5. Erst danach Phase 4 starten: kontrollierte Generation.
