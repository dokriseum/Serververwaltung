# ADR 0003: Deklarative Anwendungsprofile für Container-Anwendungen

- **Status:** Vorgeschlagen
- **Datum:** 2026-11-09

## Kontext

Anwendungen wie OpenSlides, Vaultwarden, Keycloak, Nextcloud und Outline laufen
bereits als Docker-Container. Ihre Einstellungen sind auf sehr unterschiedliche
Quellen verteilt: Umgebungsvariablen, Compose- und `.env`-Dateien,
Konfigurationsdateien in Volumes, generierte Dateien (OpenSlides), PHP-Dateien
mit CLI-Pflicht (Nextcloud `occ`), Datenbank und REST-API (Keycloak) sowie
konkurrierende Quellen mit Vorrangregeln (Vaultwarden `config.json`).

Für jede Anwendung eine eigene Oberfläche fest zu programmieren, wäre teuer,
schwer zu pflegen und für weitere Anwendungen nicht erweiterbar. Ein reiner
Datei-Editor ist dagegen nicht „schnell anpassbar“ und schützt nicht vor
Fehlkonfigurationen.

## Entscheidung

- Jede Anwendung wird durch ein **deklaratives Anwendungsprofil** (YAML)
  beschrieben: Einstellungen mit Schema, Konfigurationsquellen, Integrationen,
  Healthchecks, Backup, Update und Wartungsaktionen.
- Eine generische **Konfig-Engine** mit strukturerhaltenden Adaptern übersetzt
  Profilwerte in die jeweiligen Quellen.
- Jede Instanz bekommt ein **Git-basiertes Versions-Repository** für ihre
  Konfiguration (Historie, Diff, Rollback, Drift-Erkennung).
- Bestehende Installationen werden **übernommen, ohne sie neu zu installieren**.

## Konsequenzen

- (+) Neue Anwendungen brauchen nur ein Profil, keinen Programmcode.
- (+) Formularansicht, Validierung, Empfehlungen und Massenänderungen funktionieren für alle Anwendungen einheitlich.
- (+) Sichere Änderungen mit automatischer Rücknahme.
- (−) Profile müssen mit den Versionen der Anwendungen gepflegt werden. Gegenmaßnahme: Versionsbereiche und CI-Tests je Profil.
- (−) Sonderfälle (Generatoren, CLI- und API-Quellen) machen die Konfig-Engine komplexer.
