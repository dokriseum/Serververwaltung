# ADR 0001: Modularer Monolith als Steuerungsebene mit Standard-Engines

- **Status:** Teilweise abgelöst durch [ADR 0002](0002-host-agent-und-go.md)
- **Datum:** 2026-11-09

## Kontext

Die Serververwaltung braucht Inventar, Monitoring, Befehlsausführung, Rechte
und Audit. Für Monitoring (Prometheus), Ausführung (Ansible), Identität
(Keycloak) und Secrets (Vault/OpenBao) gibt es ausgereifte Werkzeuge. Ein
einheitliches Rechte- und Audit-Modell über alle Funktionen hinweg bieten diese
Werkzeuge einzeln aber nicht.

## Entscheidung

- Eine eigene **Steuerungsebene** (FastAPI, PostgreSQL) übernimmt Inventar,
  RBAC, Audit, Job-Steuerung und API.
- Sie ist als **modularer Monolith** aufgebaut, nicht als Microservices.
- Metriken, Ausführung, Identität und Secrets werden über Adapter an
  Standardkomponenten **angebunden** und nicht neu implementiert.

## Konsequenzen

- (+) Eine Oberfläche, ein Rechtemodell, ein lückenloses Audit-Log.
- (+) Geringer Betriebsaufwand bei kleinem Team, ein einziges Deployment.
- (+) Modulgrenzen erlauben später eine Aufteilung, falls nötig.
- (−) Die Schnittstellen zu den Fremdkomponenten müssen gepflegt werden
  (Versionen, API-Änderungen).
- (−) Python ist für einen späteren, ressourcenschonenden Agenten weniger
  geeignet. Ein Agent würde gesondert in Go entstehen.
