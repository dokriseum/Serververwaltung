# ADR 0002: Privilegierter Host-Agent und Go als Implementierungssprache

- **Status:** Vorgeschlagen
- **Datum:** 2026-11-09
- **Ersetzt teilweise:** [ADR 0001](0001-modularer-monolith-mit-standard-engines.md)
  (Ausführungsweg und Backend-Sprache)

## Kontext

Die Anforderungen sind inzwischen konkret (siehe `docs/anforderungen/`). Gefordert
ist ein grafisches Verwaltungspanel mit dem Funktionsumfang von
Container-Verwaltung, Webhosting-Panel und Systemadministration. Daraus folgt:

- Hunderte feingranulare, synchrone Operationen auf dem Host: Dateien,
  Dienste, Benutzer, Firewall, Webserver-Konfiguration.
- Interaktive Funktionen: Terminal, Dateimanager, Live-Logs, Container-Konsole.
- Drift-Erkennung und atomare Rücknahme von Konfigurationsänderungen.

Agentenlose Ausführung per SSH und Ansible (ADR 0001) ist dafür zu langsam und
zu grobgranular. Außerdem fehlt ein dauerhafter Kanal für interaktive Funktionen.

## Entscheidung

1. Auf jedem verwalteten Server läuft ein **Host-Agent** (`svagentd`, root) mit
   einer typisierten gRPC-Schnittstelle. Das Panel selbst läuft ohne Root-Rechte.
2. **Go** ist die Sprache für Panel-Server, Agent und CLI.
3. Weiterhin gilt aus ADR 0001: **modularer Monolith** im Panel-Server,
   **PostgreSQL** als Datenbank, bewährte Dienste werden verwaltet und nicht neu
   implementiert.
4. Keycloak ist nicht mehr Pflicht. Anmeldung, 2FA, LDAP und OIDC sind im
   Panel integriert, ein externer Identitätsanbieter bleibt optional.

## Konsequenzen

- (+) Schnelle, feingranulare und transaktionale Operationen, Echtzeit-Funktionen
  über einen dauerhaften Kanal.
- (+) Einfache Installation: wenige statische Binaries.
- (+) Edge-Modus für Server hinter NAT möglich.
- (−) Der Agent ist ein hochprivilegierter Dienst und muss besonders
  abgesichert und geprüft werden (siehe Technische Spezifikation, Abschnitt 5).
- (−) Ansible-Know-how ist nicht mehr direkt nutzbar. Ansible bleibt als
  Integration über die API möglich (PLT-API-008).
