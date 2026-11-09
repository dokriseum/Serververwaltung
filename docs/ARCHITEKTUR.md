# Architekturvorschlag: Serververwaltung

> **Status: abgelöst.** Dieser erste Entwurf (v0.1) entstand ohne konkrete
> Anforderungen. Maßgeblich sind jetzt der
> [Anforderungskatalog](anforderungen/README.md) und die
> [Technische Spezifikation](TECHNISCHE-SPEZIFIKATION.md). Das Dokument bleibt
> zur Nachvollziehbarkeit erhalten.

---

## 1. Zielbild

Eine zentrale Plattform, mit der ein kleines bis mittleres Team eine heterogene
Serverlandschaft (physisch, virtuell, Cloud) **inventarisiert, überwacht,
konfiguriert und wartet**, mit einer einheitlichen Weboberfläche und einer API.

### Annahmen (bitte prüfen)

| # | Annahme | Auswirkung, falls falsch |
|---|---------|--------------------------|
| A1 | 10–500 verwaltete Server, überwiegend Linux, vereinzelt Windows | Ab ca. 2.000 Hosts sind Sharding und horizontale Skalierung früher nötig |
| A2 | 1–20 Personen im Betrieb, Rollen: Admin, Operator, Lesezugriff | Bei Mandantenfähigkeit (mehrere Kunden) ändert sich das Datenmodell grundlegend |
| A3 | Self-Hosted, On-Premises oder eigene Cloud-VM | SaaS-Betrieb verlangt Mandantentrennung, Abrechnung und strengere Isolation |
| A4 | SSH-Zugriff auf die Zielserver ist möglich | Ohne eingehenden SSH-Zugriff ist ein Agent mit ausgehender Verbindung Pflicht |
| A5 | DSGVO-konformer Betrieb in der EU | Bestimmt Hosting, Logging-Aufbewahrung und Auftragsverarbeitung |

---

## 2. Funktionale Module

| Modul | Kernfunktionen | Priorität |
|-------|----------------|-----------|
| **Inventar** | Hosts, Gruppen, Tags, Hardware- und OS-Fakten, Standort, Verantwortliche | MVP |
| **Monitoring** | CPU/RAM/Disk/Netz, Dienststatus, Erreichbarkeit, Schwellwerte, Alarme | MVP |
| **Aktionen / Jobs** | Befehle und Playbooks auf Host-Gruppen ausführen, Dienste neu starten | MVP |
| **Benutzer und Rechte** | Login per SSO, rollenbasierte Rechte (RBAC), Audit-Log | MVP |
| **Patch-Management** | Verfügbare Updates anzeigen, Wartungsfenster, gestaffelter Rollout | Phase 2 |
| **Backup-Übersicht** | Status der Backup-Jobs, Restore-Tests, Aufbewahrungsregeln | Phase 2 |
| **Konfigurationsdrift** | Soll/Ist-Vergleich gegen definierte Baseline (z. B. CIS) | Phase 3 |
| **Zertifikate und Secrets** | Ablaufüberwachung von TLS-Zertifikaten, Rotation | Phase 3 |

---

## 3. Grundsatzentscheidung: Eigenbau oder Integration?

Für jedes Teilproblem gibt es ausgereifte Open-Source-Werkzeuge. Alles selbst
zu bauen wäre teuer und fehleranfällig. Nur auf Fremdwerkzeuge zu setzen ergibt
dagegen fünf getrennte Oberflächen ohne gemeinsames Rechtemodell.

| Option | Vorteile | Nachteile |
|--------|----------|-----------|
| **A: Nur Fremdwerkzeuge** (NetBox + Prometheus/Grafana + AWX + Keycloak) | Schnell produktiv, bewährt | Fünf Oberflächen, kein einheitliches Rechte- und Audit-Modell, hoher Pflegeaufwand |
| **B: Kompletter Eigenbau** | Volle Kontrolle | Monitoring und Orchestrierung neu zu erfinden kostet viel Zeit und erzeugt Sicherheitsrisiken |
| **C: Eigene Steuerungsebene + bewährte Engines** ✅ | Eine Oberfläche, ein Rechtemodell, ein Audit-Log. Die schwere Arbeit leisten erprobte Komponenten | Integrationsaufwand an den Schnittstellen |

**Empfehlung: Option C.** Selbst gebaut werden Inventar, Rechte, Audit,
Job-Steuerung und Oberfläche. Für Metriken, Ausführung und Identität werden
Standardkomponenten eingebunden.

---

## 4. Systemarchitektur

```
                         ┌─────────────────────────────┐
   Browser ── HTTPS ───▶ │  Reverse Proxy (Caddy/Traefik)│  TLS, Rate-Limit
                         └──────────────┬──────────────┘
                     ┌──────────────────┼───────────────────┐
                     ▼                  ▼                   ▼
            ┌────────────────┐  ┌────────────────┐  ┌───────────────┐
            │ Web-Frontend   │  │  API-Server    │  │  Keycloak     │
            │ React + TS     │  │  (FastAPI)     │◀▶│  (OIDC / SSO) │
            │ (statisch)     │  │  REST + WS     │  └───────────────┘
            └────────────────┘  └───┬───┬───┬────┘
                                    │   │   │
              ┌─────────────────────┘   │   └─────────────────────┐
              ▼                         ▼                         ▼
     ┌────────────────┐       ┌──────────────────┐      ┌──────────────────┐
     │  PostgreSQL    │       │  Redis           │      │  Prometheus      │
     │  Inventar,     │       │  Job-Queue,      │      │  (+ Alertmanager)│
     │  Rechte, Audit │       │  Pub/Sub, Cache  │      │  Metriken        │
     └────────────────┘       └────────┬─────────┘      └────────▲─────────┘
                                       ▼                         │ scrape
                              ┌──────────────────┐               │
                              │  Worker (n×)     │               │
                              │  Ansible-Runner  │── SSH ──┐     │
                              └────────┬─────────┘         │     │
                                       │ liest Secrets     ▼     │
                              ┌──────────────────┐   ┌──────────────────────┐
                              │  Vault /         │   │  Zielserver          │
                              │  OpenBao         │   │  node_exporter       │
                              └──────────────────┘   │  (optional: Agent)   │
                                                     └──────────────────────┘
```

### Datenflüsse

1. **Lesen (Inventar/Status):** Frontend → API → PostgreSQL (Stammdaten) und
   Prometheus-HTTP-API (aktuelle Metriken). Live-Updates per WebSocket über
   Redis-Pub/Sub.
2. **Aktion ausführen:** Frontend → API (Rechte prüfen, Audit-Eintrag schreiben)
   → Job in Redis → Worker holt kurzlebige SSH-Credentials aus Vault →
   `ansible-runner` gegen die Zielgruppe → Ausgabe zeilenweise zurück per
   Pub/Sub → Ergebnis in PostgreSQL.
3. **Monitoring:** Prometheus fragt `node_exporter` auf den Zielservern ab.
   Die Scrape-Ziele erzeugt die API dynamisch aus dem Inventar
   (`http_sd_configs`), es gibt also keine doppelte Pflege.
4. **Alarme:** Alertmanager → Webhook an die API (Anzeige und Verlauf) sowie
   E-Mail, Teams oder Slack.

---

## 5. Technologie-Stack

| Schicht | Empfehlung | Alternative | Begründung |
|---------|------------|-------------|------------|
| Backend | **Python 3.12 + FastAPI**, SQLAlchemy 2, Pydantic v2 | Go (Echo/Chi) | Ansible und `ansible-runner` sind Python-nativ, das Ökosystem für Ops ist reif und OpenAPI wird automatisch erzeugt. Go lohnt sich erst bei einem eigenen Agenten |
| Job-Queue | **arq** oder **Dramatiq** auf Redis | Celery | Schlanker als Celery, async-fähig |
| Datenbank | **PostgreSQL 16** | – | JSONB für Host-Fakten, robuste Transaktionen, Row-Level Security für spätere Mandanten |
| Migrationen | **Alembic** | – | Standard mit SQLAlchemy |
| Frontend | **React + TypeScript + Vite**, TanStack Query, shadcn/ui | Vue 3 | Großes Ökosystem, typisierter API-Client aus OpenAPI generierbar |
| Auth | **Keycloak** (OIDC), RBAC in der API | Authentik | SSO, MFA, LDAP/AD-Anbindung ohne Eigenbau |
| Ausführung | **Ansible** über `ansible-runner` | Salt | Agentenlos über SSH, sehr große Modulbibliothek |
| Metriken | **Prometheus + node_exporter**, optional Grafana | VictoriaMetrics | Industriestandard. VictoriaMetrics als Ersatz bei sehr vielen Hosts |
| Secrets | **OpenBao/Vault** (SSH-CA für kurzlebige Zertifikate) | SOPS + age | Keine dauerhaft gültigen privaten Schlüssel auf dem Worker |
| Betrieb | **Docker Compose** (Start), später **Kubernetes/Helm** | – | Compose reicht für A1. Helm-Chart erst, wenn HA verlangt wird |

---

## 6. Backend-Struktur (modularer Monolith)

Microservices wären bei dieser Teamgröße Overhead. Ein **modularer Monolith**
mit klaren Modulgrenzen lässt sich später entlang dieser Grenzen aufteilen.

```
backend/
├── app/
│   ├── main.py                 # App-Factory, Router-Registrierung
│   ├── core/                   # Konfiguration, Logging, Sicherheit, DB-Session
│   ├── modules/
│   │   ├── inventory/          # Host, Gruppe, Tag
│   │   │   ├── api.py          #   HTTP-Schicht (Router)
│   │   │   ├── schemas.py      #   Pydantic-DTOs
│   │   │   ├── service.py      #   Geschäftslogik
│   │   │   ├── repository.py   #   Datenzugriff
│   │   │   └── models.py       #   SQLAlchemy-Modelle
│   │   ├── jobs/               # Job-Definition, Ausführung, Logs
│   │   ├── monitoring/         # Prometheus-Adapter, Alarm-Webhook
│   │   ├── auth/               # OIDC-Validierung, RBAC
│   │   └── audit/              # Unveränderliches Audit-Log
│   └── workers/                # Queue-Consumer, Ansible-Runner-Adapter
├── migrations/                 # Alembic
└── tests/                      # unit / integration (Testcontainers)
```

Regeln:
- Module kommunizieren nur über ihre `service`-Schnittstelle, nie über fremde
  Modelle oder Tabellen.
- Externe Systeme (Prometheus, Vault, Ansible) laufen hinter Adapter-Interfaces
  (Ports & Adapters). Dadurch sind sie im Test austauschbar.

### Kern-Datenmodell (vereinfacht)

```
Host(id, hostname, fqdn, ip, os_family, os_version, environment,
     status, facts JSONB, created_at, updated_at)
Group(id, name, description)            Host n:m Group
Tag(key, value)                         Host n:m Tag
JobTemplate(id, name, playbook_ref, params_schema JSONB, required_role)
JobRun(id, template_id, target_selector, requested_by, status,
       started_at, finished_at, exit_code)
JobRunLog(run_id, seq, host, stream, line, ts)
AuditEvent(id, ts, actor, action, resource_type, resource_id,
           payload JSONB, ip, prev_hash)       -- Hash-Kette, nur anhängend
Role / Permission / RoleBinding(subject, role, scope)
```

---

## 7. Sicherheitskonzept

| Bereich | Maßnahme |
|---------|----------|
| Authentifizierung | Ausschließlich OIDC über Keycloak, MFA verpflichtend für Admin-Rollen |
| Autorisierung | RBAC mit Scope (global / Gruppe / Umgebung). Jede schreibende Aktion wird serverseitig geprüft |
| Zugriff auf Zielserver | SSH-Zertifikate aus einer Vault-SSH-CA mit 5 Minuten Gültigkeit. Keine statischen Schlüssel, eigener Service-Account mit eingeschränktem `sudo` |
| Befehlsausführung | Nur freigegebene **JobTemplates** mit validierten Parametern (JSON-Schema). Freie Shell-Befehle nur für die Rolle `admin`, immer mit Audit |
| Audit | Nur anhängendes Log mit Hash-Kette (manipulationssichtbar), Export an ein externes SIEM möglich |
| Transport | TLS 1.3 nach außen, interner Verkehr im eigenen Docker-Netz. mTLS bei verteiltem Betrieb |
| Secrets | Nie in Git oder in Umgebungsvariablen im Image. Zur Laufzeit aus Vault |
| Lieferkette | Gepinnte Abhängigkeiten, Dependabot/Renovate, Image-Scan (Trivy), SBOM |
| Web | CSP, SameSite-Cookies, CSRF-Schutz, Rate-Limiting am Proxy |

**Hinweis:** Ein System, das Befehle auf allen Servern ausführen darf, ist das
attraktivste Angriffsziel im ganzen Netz. Der Worker gehört deshalb in ein
eigenes Netzsegment, und die API selbst erhält keinen SSH-Zugriff.

---

## 8. Betrieb, Qualität und CI/CD

- **Lokale Entwicklung:** `docker compose up` startet den gesamten Stack,
  inklusive zwei Test-Containern mit SSH und `node_exporter` als Zielserver.
- **Tests:** pytest (Unit), Testcontainers für PostgreSQL und Redis
  (Integration), Playwright (E2E im Frontend). Ziel: ≥ 80 % Abdeckung der
  Service-Schicht.
- **Qualität:** `ruff` + `mypy --strict` im Backend, `eslint` + `tsc` im
  Frontend, pre-commit-Hooks.
- **CI (GitHub Actions):** Lint → Tests → Container-Build → Trivy-Scan →
  Push in die Registry. Deployment per Tag.
- **Beobachtbarkeit der Plattform selbst:** strukturierte JSON-Logs,
  OpenTelemetry-Traces, `/healthz` und `/readyz`, eigene Metriken unter
  `/metrics`.
- **Backup der Plattform:** täglicher `pg_dump` plus WAL-Archivierung,
  Vault-Snapshots, dokumentierter und getesteter Restore.

---

## 9. Umsetzungsplan

| Phase | Inhalt | Ergebnis |
|-------|--------|----------|
| **0 – Fundament** (1–2 Wochen) | Repo-Struktur, Compose-Stack, CI, Keycloak-Login, Health-Checks | Lauffähiges Grundgerüst mit Login |
| **1 – MVP** (4–6 Wochen) | Inventar-CRUD, Prometheus-Anbindung mit Statusanzeige, JobTemplates + Ausführung mit Live-Log, RBAC, Audit | Hosts sehen, überwachen und gezielt Aktionen ausführen |
| **2 – Wartung** (4 Wochen) | Patch-Übersicht, Wartungsfenster, Backup-Status, Alarm-Verlauf | Planbare, nachvollziehbare Wartung |
| **3 – Compliance** | Drift-Erkennung, Zertifikatsüberwachung, Berichte, optionaler Agent (Go) für Hosts ohne SSH-Zugang | Revisionssicherer Betrieb |

---

## 10. Offene Fragen

Diese Punkte verändern die Architektur wesentlich und sollten zuerst geklärt
werden:

1. **Umfang:** Wie viele Server, welche Betriebssysteme, welche Standorte
   bzw. Clouds?
2. **Zweck:** Internes Werkzeug für das eigene Team, oder ein Produkt für
   externe Kunden (Mandantenfähigkeit)?
3. **Bestand:** Gibt es schon Werkzeuge (z. B. Zabbix, Ansible, NetBox, AD/LDAP),
   die weiterverwendet werden müssen?
4. **Netzwerk:** Ist SSH von einem zentralen Punkt zu allen Servern erlaubt, oder
   sind Server nur ausgehend erreichbar (NAT, DMZ, Kundennetze)?
5. **Sprache und Team:** Welche Sprachen beherrscht das Team? Python oder Go
   im Backend?
6. **Hochverfügbarkeit:** Muss die Plattform selbst ausfallsicher sein, oder
   reicht ein Einzelknoten mit Backup?
7. **Compliance:** Gelten Vorgaben wie ISO 27001, BSI IT-Grundschutz oder
   KRITIS?
8. **Ist „Serververwaltung“ eventuell etwas anderes**, z. B. die Verwaltung von
   Game-Servern, Discord-Bots oder ein Hosting-Kundenpanel? In dem Fall würde
   der Vorschlag deutlich anders ausfallen.
