# Technische Spezifikation: Serververwaltung

Version 1.0 · Stand 2026-11-09 · Status: Entwurf zur Abstimmung

Dieses Dokument beschreibt, **wie** die Anforderungen aus
[anforderungen/](anforderungen/README.md) technisch umgesetzt werden. Es löst
den ersten Architekturentwurf ([ARCHITEKTUR.md](ARCHITEKTUR.md), v0.1) ab.

---

## 1. Architekturprinzipien

1. **Rechtetrennung:** Oberfläche und API laufen ohne Root-Rechte. Jede
   privilegierte Operation geht über einen Agenten mit einer fest typisierten
   Schnittstelle (NFA-SEC-002).
2. **Deklarativ statt imperativ:** Das Panel speichert den Sollzustand (z. B.
   „Domain X mit PHP 8.3, SSL aktiv“) in der Datenbank. Der Agent erzeugt
   daraus die Konfigurationsdateien und gleicht laufend Soll und Ist ab.
3. **Atomare Änderungen mit Rücknahme:** validieren → rendern → prüfen →
   aktivieren → Gesundheitscheck → bei Fehler zurück (NFA-VFG-002).
4. **API zuerst:** Die Oberfläche nutzt ausschließlich die öffentliche API
   (PLT-API-001).
5. **Modular:** Jedes Fachmodul (Container, Web, Mail, DNS, System …) ist ein
   eigenes Paket mit eigener Datenbank-Schemadatei, eigenen API-Routen, eigenen
   Rechten und eigenem Agent-Treiber. Module lassen sich einzeln aktivieren.
6. **Bewährte Dienste statt Eigenbau:** Webserver, Mailserver, DNS usw. sind
   etablierte Open-Source-Dienste. Das Panel verwaltet sie, es ersetzt sie nicht.

---

## 2. Systemübersicht

```
┌──────────────────────────────── Browser / CLI / externe Systeme ───────────────────────────────┐
│   Web-UI (React SPA)          svctl (CLI)          Abrechnung, CI/CD, Terraform                  │
└───────────────┬───────────────────────┬───────────────────────┬────────────────────────────────┘
                │ HTTPS (REST, WebSocket, OpenAPI 3.1)          │
┌───────────────▼───────────────────────────────────────────────▼────────────────────────────────┐
│                               PANEL-SERVER  (Benutzer: svpanel, ohne Root)                      │
│  ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐ ┌────────────┐  │
│  │ Auth/    │ │ Hosting  │ │Container │ │ System   │ │ Mail/DNS │ │ Backup/  │ │ Audit/     │  │
│  │ RBAC     │ │ (Web/DB) │ │ (Docker, │ │ (Pakete, │ │          │ │ Monitor  │ │ Events/    │  │
│  │          │ │          │ │ K8s)     │ │ Dienste) │ │          │ │          │ │ Notify     │  │
│  └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘ └──────────┘ └────────────┘  │
│        Job-Queue (PostgreSQL-basiert) · Scheduler · Agent-Gateway (gRPC/mTLS) · WS-Hub          │
└───────┬──────────────────────────────┬──────────────────────────────────┬──────────────────────┘
        │                              │                                  │
 ┌──────▼───────┐            ┌─────────▼──────────┐            ┌──────────▼──────────┐
 │ PostgreSQL   │            │ VictoriaMetrics    │            │ Objektspeicher      │
 │ Sollzustand, │            │ (Metriken,         │            │ (Backups, lokal     │
 │ Benutzer,    │            │  Prometheus-API)   │            │  oder S3)           │
 │ Audit, Jobs  │            └─────────▲──────────┘            └─────────────────────┘
 └──────────────┘                      │ Remote-Write
                 gRPC über mTLS        │
          ┌────────────────────────────┴──────────────────────────────────────────────┐
          │                 HOST-AGENT  svagentd  (root, je verwaltetem Server)        │
          │  Treiber: pkg · systemd · users · fs · net · nftables · nginx · apache ·    │
          │  php-fpm · mariadb · postgres · postfix · dovecot · rspamd · powerdns ·    │
          │  acme · docker · podman · k8s-proxy · restic · metrics · terminal (PTY)    │
          └───────┬────────────────┬───────────────┬──────────────────┬────────────────┘
                  ▼                ▼               ▼                  ▼
            Systemdateien     Systemdienste   Docker/Podman-API   Kubernetes-API
```

### 2.1 Betriebsmodi

| Modus | Beschreibung |
|-------|--------------|
| **Einzelserver** | Panel-Server, Datenbank und Agent auf demselben Host. Der Agent ist über einen Unix-Socket angebunden (`/run/svagentd.sock`, Zugriff nur für die Gruppe `svpanel`). Standardfall. |
| **Zentral + Agenten** | Ein Panel verwaltet mehrere Server. Die Agenten verbinden sich per gRPC über mTLS. |
| **Edge** | Der Agent baut die Verbindung ausgehend auf (HTTP/2 bzw. WebSocket-Tunnel über 443), für Server und Geräte hinter NAT (PLT-MSV-004, CON-ENV-004). Der asynchrone Modus fragt nur periodisch ab. |

---

## 3. Komponenten

### 3.1 Panel-Server

| Aspekt | Festlegung |
|--------|------------|
| Sprache | **Go** (aktuelle stabile Version) |
| HTTP | `net/http` + `chi`, OpenAPI-3.1-Spezifikation als Quelle, Server-Stubs und Client per Code-Generierung (`oapi-codegen`) |
| Datenzugriff | `pgx` + `sqlc` (typsichere SQL-Abfragen), Migrationen mit `goose` |
| Job-Queue | PostgreSQL-basierte Queue (`river`). Keine zusätzliche Infrastruktur, transaktional mit den Fachdaten. |
| Echtzeit | WebSocket-Hub für Statusänderungen, Logs, Terminal und Job-Ausgaben. Mehrere Instanzen tauschen sich über PostgreSQL `LISTEN/NOTIFY` aus. |
| Prozess | Ein Binary `svpanel`, systemd-Unit, Benutzer `svpanel`, `NoNewPrivileges`, `ProtectSystem=strict` |
| Konfiguration | `/etc/svpanel/panel.toml`, Geheimnisse in `/etc/svpanel/secrets/` (0600) |

**Begründung für Go:** ein einziges statisches Binary pro Komponente (einfache
Installation, PLT-INS-001), geringer Speicherbedarf (NFA-PRF-004),
ausgezeichnete Bibliotheken für Docker, Kubernetes (`client-go`), ACME (`lego`),
nftables, gRPC und PTY. Panel und Agent teilen Typen und Werkzeuge.

### 3.2 Host-Agent `svagentd`

| Aspekt | Festlegung |
|--------|------------|
| Sprache | Go, statisch gelinkt, x86_64 und arm64 |
| Rechte | root (notwendig für Systemverwaltung). Angriffsfläche minimiert durch eine ausschließlich typisierte gRPC-Schnittstelle. |
| Schnittstelle | gRPC (Protobuf), Versionierung je Treiber, nur definierte Operationen (z. B. `WebDomain.Apply`, `Service.Restart`, `Package.Install`) mit Validierung im Agenten |
| Treiber | Je verwaltetem Dienst ein Treiber mit der Schnittstelle `Detect / Status / Render / Validate / Apply / Rollback / Collect` |
| Zustand | Lokaler Cache des letzten angewendeten Sollzustands in `/var/lib/svagentd/` (bbolt), damit Drift erkannt wird (NFA-VFG-004) |
| Vorlagen | Konfigurationsvorlagen (Go `text/template`) im Agenten. Überschreibungen in `/etc/svagentd/templates.d/` (WEB-WSV-011). |
| Ausführung fremder Prozesse | Ausschließlich `exec` mit Argumentliste, nie über eine Shell (NFA-SEC-003). Benutzerkontext per `setuid/setgid` und eigener cgroup. |
| Terminal | PTY im Benutzerkontext des Angemeldeten, Datenstrom über gRPC-Stream → WebSocket |
| Selbstaktualisierung | Signiertes Binary, Austausch über systemd, automatischer Rücksprung bei fehlendem Lebenszeichen |

**Ablauf einer Änderung** (Beispiel: PHP-Version einer Domain ändern):

```
UI ─PUT /api/v1/domains/42 {php: "8.3"}─▶ Panel
Panel: RBAC prüfen → Sollzustand in DB (Transaktion) → Audit-Eintrag → Job anlegen
Job-Worker ─gRPC WebDomain.Apply(spec)─▶ Agent
Agent: php8.3-fpm vorhanden? → Pool rendern → `php-fpm8.3 -t` → nginx-vhost rendern → `nginx -t`
       → Dateien atomar ersetzen → reload → HTTP-Healthcheck auf die Domain
       → Fehler? Vorherige Dateien zurück + reload → Fehlerbericht
Agent ─Ergebnis + Diff─▶ Panel → Job-Status, Audit-Diff, WebSocket-Push an UI
```

### 3.3 Weboberfläche

| Aspekt | Festlegung |
|--------|------------|
| Framework | React + TypeScript (strict), Build mit Vite |
| Daten | TanStack Query mit API-Client, der aus der OpenAPI-Spezifikation generiert wird |
| Routing | TanStack Router, Code-Splitting je Modul |
| Komponenten | Eigenes Design-System auf Basis von Radix-Primitives + Tailwind CSS. Barrierefreiheit über die Radix-Primitives (WCAG 2.1 AA). |
| Editoren | Monaco (Konfigurationsdateien, YAML, Dockerfile, SQL) mit Syntaxprüfung |
| Terminal | xterm.js |
| Diagramme | ECharts |
| i18n | i18next, Übersetzungsdateien je Modul (PLT-UI-004) |
| Auslieferung | Statische Dateien, eingebettet in das `svpanel`-Binary |

### 3.4 CLI `svctl`

Go-Binary, gleiche API wie die Oberfläche, Ausgabe als Tabelle oder JSON,
lokal über den Unix-Socket (als root) oder entfernt per API-Schlüssel
(PLT-API-004/005). Zusätzlich Notfallbefehle: `svctl admin reset-password`,
`svctl repair`, `svctl diagnose`.

### 3.5 Datenhaltung

| Daten | Speicher |
|-------|----------|
| Sollzustand, Benutzer, Rechte, Pakete, Jobs, Audit | PostgreSQL 16+ (mitinstalliert, eigener Cluster `svpanel`, Zugriff nur über Unix-Socket) |
| Metriken | VictoriaMetrics Single-Node (eingebettet, Prometheus-kompatible API, Remote-Write vom Agenten) |
| Geheimnisse | In PostgreSQL mit AES-256-GCM verschlüsselt (Envelope-Verschlüsselung). Hauptschlüssel in `/etc/svpanel/secrets/master.key` oder in einem externen KMS (SEC-SEC-001/004). |
| Backups | restic-Repositories (lokal, S3, SFTP, rclone-Ziele) |
| Agent-Cache | bbolt je Host |

---

## 4. Verwaltete Dienste (Standardauswahl)

| Bereich | Dienst | Begründung | Alternativen |
|---------|--------|------------|--------------|
| Webserver | **nginx** (Frontend), optional **Apache 2.4** (Backend) | Wie Referenzprodukt, `.htaccess`-Kompatibilität | – |
| PHP | **PHP-FPM**, mehrere Versionen (Debian: sury-Repository, RHEL: Remi) | Isolierte Pools je Abonnement | – |
| Node/Python/Ruby | Versionsverwaltung über **mise**, Prozesse als systemd-Units im Benutzerkontext | Kein globaler Konflikt zwischen Versionen | – |
| Datenbanken | **MariaDB** (LTS), **PostgreSQL** | Verbreitet, stabil | MySQL 8.4 |
| DB-Weboberfläche | **phpMyAdmin**, **Adminer** (PostgreSQL) | Bewährt, SSO über signierte Einmal-Tokens | – |
| DNS | **PowerDNS Authoritative** (Backend: eigene PostgreSQL-DB), **PowerDNS Recursor** optional | HTTP-API, DNSSEC integriert, Primär/Sekundär | BIND 9 (für WEB-DNS-013 Views) |
| MTA | **Postfix** | Standard, robust | – |
| IMAP/POP3 | **Dovecot** (Sieve, Quota, FTS) | Standard | – |
| Spam/Signatur | **Rspamd** (Spam, DKIM, ARC, DMARC, Greylisting, Bayes) | Ersetzt mehrere Einzelwerkzeuge, hohe Leistung, HTTP-API | SpamAssassin + OpenDKIM |
| Virenscan | **ClamAV** (clamd) | Standard | – |
| Webmail | **Roundcube** | Wie Referenzprodukt | SnappyMail |
| FTP | **Pure-FTPd** (virtuelle Benutzer, TLS) | Einfache Konfiguration für virtuelle Konten | ProFTPD |
| SFTP | OpenSSH mit `ChrootDirectory` und `internal-sftp` | – | – |
| Zertifikate | **ACME im Agenten** (Go-Bibliothek `lego`, HTTP-01/DNS-01 mit über 100 DNS-Anbietern) | Keine externe Abhängigkeit | certbot |
| Firewall | **nftables**, eigene Tabelle `inet svpanel`. Bei aktivem firewalld Steuerung über dessen D-Bus-API. | Kein Konflikt mit Docker-Regeln (eigene Tabelle, definierte Prioritäten) | – |
| Intrusion Prevention | **Fail2Ban** mit nftables-Aktion | Wie Referenzprodukt | CrowdSec |
| WAF | **ModSecurity v3** (libmodsecurity + nginx-Connector) mit **OWASP CRS 4** | Wie Referenzprodukt | Coraza |
| Malware | **ClamAV** + YARA-Regeln, Echtzeit über `fanotify` | Ohne Lizenzkosten | ImunifyAV (Erweiterung) |
| Backup | **restic** (Deduplizierung, Verschlüsselung, inkrementell), **rclone** für Cloud-Ziele | Erfüllt BAK-003/005/007 | Borg |
| Container | Docker Engine API, Podman-API (Docker-kompatibel + libpod), Kubernetes `client-go`, Helm SDK | Offizielle Bibliotheken | – |
| Schwachstellen-Scan | **Trivy** (Images, Pakete) | Breite Abdeckung | Grype |
| Metriken | Collector im Agenten (gopsutil, cgroups v2, procfs) | Kein separater Exporter nötig | node_exporter |

---

## 5. Sicherheitsarchitektur

### 5.1 Vertrauenszonen

```
[Internet] ──443──▶ [Panel-Server: svpanel, ohne Root] ──Unix-Socket/mTLS──▶ [Agent: root]
                         │                                                     │
                         └── PostgreSQL (nur lokal)                            └── Systemdienste
```

- **Panel kompromittiert:** Der Angreifer kann nur die typisierten
  Agent-Operationen aufrufen. Der Agent prüft jede Operation erneut: Pfade
  liegen innerhalb erlaubter Wurzeln, Benutzer liegen im erlaubten UID-Bereich,
  Domains sind syntaktisch gültig.
- **Kunde kompromittiert:** Abonnements sind isoliert (eigener Unix-Benutzer,
  eigener PHP-FPM-Pool, Rechte 0750, `open_basedir`, cgroup-Limits, optional
  chroot-Shell).

### 5.2 Maßnahmen im Überblick

| Thema | Umsetzung |
|-------|-----------|
| Passwörter | Argon2id (m=64 MiB, t=3, p=4), Prüfung gegen eine lokale Liste kompromittierter Passwörter (k-Anonymität optional online) |
| Sitzungen | Serverseitige Sitzungen (opakes Token in einem `HttpOnly`-, `Secure`-, `SameSite=Strict`-Cookie), Rotation bei Rechteänderung |
| API-Schlüssel | Präfix + Zufallswert (256 Bit), gespeichert als SHA-256-Hash, Scopes, Ablaufdatum |
| CSRF | SameSite plus Synchronizer-Token für zustandsändernde Anfragen |
| CSP | `default-src 'self'`, ohne `unsafe-inline`/`unsafe-eval`, Nonces |
| Agent-Kommunikation | mTLS mit eigener interner CA. Zertifikate laufen nach 30 Tagen ab und werden automatisch erneuert. Registrierung per Einmal-Token. |
| Datei-Operationen | Alle Pfadoperationen mit `openat2(RESOLVE_BENEATH, RESOLVE_NO_SYMLINKS)` im Benutzerkontext (verhindert Path Traversal und Symlink-Angriffe) |
| Audit | Nur anhängend, Hash-Kette (SHA-256 über Eintrag + Vorgänger-Hash), Export per Syslog (RFC 5424, TLS) |
| Lieferkette | Signierte Pakete (GPG für apt/dnf), Signaturen der Binaries (Sigstore/cosign), SBOM (CycloneDX), reproduzierbare Builds |

---

## 6. Datenmodell (Kernauszug)

```
-- Mandanten und Rechte
account(id, type[admin|reseller|customer|user|mail_user], parent_id, login, email,
        password_hash, totp_secret_enc, locale, status, created_at)
team(id, name)                            team_member(team_id, account_id)
role(id, name, builtin)                   role_permission(role_id, permission)
role_binding(subject_type, subject_id, role_id, scope_type, scope_id)
api_key(id, account_id, prefix, hash, scopes[], expires_at, last_used_at)

-- Server und Agenten
server(id, name, hostname, mode[local|agent|edge], agent_version, os, arch,
       status, last_seen_at, tags[])

-- Hosting
service_plan(id, owner_id, name, limits JSONB, permissions JSONB, hosting JSONB)
subscription(id, customer_id, plan_id, server_id, sys_user, sys_uid, status,
             customized bool, overrides JSONB, expires_at)
domain(id, subscription_id, name, parent_id, type[main|sub|alias], hosting_type,
       docroot, php_version, php_settings JSONB, web_settings JSONB, ssl_cert_id,
       dns_enabled, mail_enabled, status)
certificate(id, owner_scope, names[], issuer, not_after, source[acme|upload|self],
            key_enc, chain)
dns_zone(id, domain_id, type[primary|secondary], dnssec bool, soa JSONB)
dns_record(id, zone_id, name, type, ttl, content, prio)
database(id, subscription_id, server_id, engine, name)
db_user(id, database_id, name, password_enc, privileges, remote_hosts[])
mailbox(id, domain_id, local_part, password_hash, quota_mb, forwards[], keep_copy,
        autoreply JSONB, spam_settings JSONB, status)
ftp_account(id, subscription_id, login, password_hash, home, permissions)

-- Container
container_env(id, server_id, type[docker|podman|swarm|k8s], endpoint,
              edge_mode, group_id, tags[], settings JSONB)
stack(id, env_id, name, source[editor|upload|git|template], git JSONB,
      compose TEXT, env_vars_enc, owner_scope, version)
registry(id, type, url, credentials_enc)
resource_ownership(env_id, resource_type, resource_id, owner_type, owner_id)

-- Betrieb
job(id, kind, args JSONB, state, attempts, created_by, started_at, finished_at, result JSONB)
job_log(job_id, seq, stream, line, ts)
backup_policy(id, scope_type, scope_id, schedule, retention JSONB, targets[], content[])
backup_snapshot(id, policy_id, repo_id, restic_id, size, created_at, status)
alert_rule(id, scope, expr, duration, severity, actions JSONB)
audit_event(id, ts, actor_id, source, ip, action, object_type, object_id,
            diff JSONB, prev_hash, hash)
```

---

## 7. API-Gestaltung

| Aspekt | Festlegung |
|--------|------------|
| Stil | REST, Ressourcen im Plural, Versionierung im Pfad (`/api/v1/…`) |
| Format | JSON, Fehler nach RFC 9457 (`application/problem+json`) |
| Lange Operationen | `202 Accepted` + `Location: /api/v1/jobs/{id}`, Fortschritt per WebSocket `/api/v1/ws` |
| Paginierung | Cursor-basiert (`?cursor=…&limit=…`), Filter `?filter[status]=running` |
| Nebenläufigkeit | Optimistisches Sperren über `ETag`/`If-Match` |
| Container-Passthrough | `/api/v1/envs/{id}/docker/*` und `/k8s/*` als geprüfter Proxy (RBAC-Filter auf Antworten, Sperrliste gefährlicher Optionen gemäß CON-ENV-015) |
| Webhooks | HMAC-SHA256-Signatur im Header, Wiederholung mit exponentiellem Backoff |
| Ratenbegrenzung | Token-Bucket je API-Schlüssel und IP, Header `RateLimit-*` |

Beispiele:

```
POST   /api/v1/subscriptions                       Abonnement anlegen
GET    /api/v1/domains?filter[subscription]=17     Domains eines Abonnements
PATCH  /api/v1/domains/42                          Hosting-Einstellungen ändern
POST   /api/v1/domains/42/certificates/acme        Let's-Encrypt-Zertifikat anfordern
POST   /api/v1/envs/3/stacks                       Stack bereitstellen
POST   /api/v1/servers/1/services/nginx:restart    Dienst neu starten
GET    /api/v1/servers/1/metrics?query=…           Metrik abfragen (PromQL)
POST   /api/v1/backups/policies/5:run              Sicherung sofort ausführen
```

---

## 8. Installation und Paketierung

| Artefakt | Inhalt |
|----------|--------|
| `svpanel` (.deb/.rpm) | Panel-Server mit eingebetteter Oberfläche, CLI, Migrationen |
| `svagentd` (.deb/.rpm, statisches Binary) | Agent |
| `svpanel-stack-*` (Metapakete) | Profile „minimal“, „webhosting“, „voll“. Installieren die jeweiligen Dienste aus den Distributions-Repositories (bzw. sury/Remi für PHP). |
| Container-Image | `svpanel` für den reinen Container-Verwaltungsbetrieb (PLT-INS-003) |
| `install.sh` | Prüft Distribution und Voraussetzungen, bindet das Paket-Repository ein, installiert das Profil, startet den Erstkonfigurations-Assistenten |

**Verzeichnisstruktur auf dem Host:**

```
/etc/svpanel/            Panel-Konfiguration, Geheimnisse
/etc/svagentd/           Agent-Konfiguration, Template-Überschreibungen
/var/lib/svpanel/        Panel-Daten (PostgreSQL-Cluster, Metriken)
/var/lib/svagentd/       Agent-Zustand
/var/www/vhosts/<abo>/   Webspace je Abonnement (httpdocs, logs, private, tmp)
/var/mail/vhosts/<dom>/  Maildir-Postfächer
/var/backups/svpanel/    Lokale Backup-Repositories
```

---

## 9. Qualitätssicherung und Entwicklungsprozess

| Bereich | Festlegung |
|---------|------------|
| Repository | Monorepo: `cmd/svpanel`, `cmd/svagentd`, `cmd/svctl`, `internal/<modul>`, `agent/drivers/<dienst>`, `web/`, `api/openapi.yaml`, `proto/`, `packaging/`, `docs/` |
| Tests Backend | Go-Unit-Tests, Integrationstests mit Testcontainers (PostgreSQL), Treiber-Tests gegen echte Dienste in Distributions-Containern |
| Tests Agent | Matrix-Tests auf allen unterstützten Distributionen (VMs mit cloud-init in der CI), Golden-File-Tests für gerenderte Konfigurationen |
| Tests UI | Vitest (Komponenten), Playwright (E2E für alle MUSS-Anforderungen), axe-core (Barrierefreiheit) |
| Statische Analyse | `golangci-lint`, `govulncheck`, `gosec`, ESLint, TypeScript strict, Trivy für Abhängigkeiten |
| CI/CD | GitHub Actions: Lint → Tests → Build (amd64/arm64) → Pakete → Signatur → Integrationsmatrix → Veröffentlichung im Paket-Repository (Kanal stabil/vorab) |
| Rückverfolgbarkeit | Jede Anforderungs-ID wird in Tests referenziert (`// Req: WEB-SSL-001`). Ein Bericht zeigt die Abdeckung je ID. |
| Versionierung | SemVer, Conventional Commits, automatisch erzeugtes Änderungsprotokoll |

---

## 10. Umsetzungsplan

| Meilenstein | Inhalt (Anforderungsgruppen) | Dauer (Richtwert, Team von 4–6 Personen) |
|-------------|------------------------------|------------------------------------------|
| **M0 Fundament** | Monorepo, CI, Paketierung, Panel-Grundgerüst, Agent mit Treibern pkg/systemd, Auth (PLT-AUTH), RBAC-Kern, Audit, Jobs, UI-Shell | 6 Wochen |
| **M1 System** | PLT-DSH, PLT-FIL (Dateimanager, Terminal), SYS-PKG, SYS-SVC, SYS-PRC, SYS-CRN, SYS-USR, SYS-LOG, SYS-NET (Kern), SEC-FW, SEC-IPS, MON (Basis) | 8 Wochen |
| **M2 Container** | CON-ENV (lokal, Agent), CON-CTR, CON-IMG, CON-RES, CON-STK, CON-TPL, CON-REG | 8 Wochen |
| **M2a Anwendungen** | APP-PRO, APP-DSC (Übernahme der bestehenden Installationen), APP-CFG, APP-SEC, APP-INT, APP-UPD, APP-OPS, APP-ACT; Profile OpenSlides, Vaultwarden, Keycloak, Nextcloud, Outline | 8 Wochen |
| **M3 Webhosting** | WEB-CUS, WEB-DOM, WEB-WSV, WEB-RUN (PHP, Node), WEB-SSL, WEB-DNS, WEB-DB, WEB-FTP, WEB-SUB | 12 Wochen |
| **M4 Mail** | MAIL-SRV, MAIL-BOX, MAIL-AUT, MAIL-SPM, MAIL-WML | 8 Wochen |
| **M5 Betrieb** | BAK, MON vollständig, SEC-WAF, SEC-MAL, SEC-ADV, PLT-NOT vollständig | 8 Wochen |
| **M6 → Version 1.0** | USR (Benutzerportal), SYS-DSK, SYS-SRV, PLT-MSV/SYS-CLU, Härtung, Penetrationstest, Dokumentation | 8 Wochen |
| **1.x** | S-Anforderungen: Swarm, Kubernetes, Edge, WordPress-Werkzeuge, Migration, Erweiterungen | fortlaufend |

---

## 11. Risiken

| Risiko | Auswirkung | Gegenmaßnahme |
|--------|------------|---------------|
| Anwendungsprofile veralten bei neuen Versionen der Anwendungen | Fehlerhafte Formulare, gescheiterte Updates | Versionsbereiche je Einstellung, automatisierte Profil-Tests gegen neue Image-Versionen in der CI, generischer Editor als Rückfallebene |
| Sehr großer Funktionsumfang (drei Produkte in einem) | Verzögerung | Strikte Priorisierung nach MoSCoW, modularer Aufbau, Version 1.0 nur mit M-Anforderungen |
| Unterschiede zwischen Distributionen | Fehler auf einzelnen Systemen | Treiberabstraktion, Integrationsmatrix in der CI, begrenzte Liste unterstützter Systeme |
| Root-Agent als Angriffsziel | Vollständige Kompromittierung | Typisierte Schnittstelle, Validierung im Agenten, mTLS, Fuzzing der gRPC-Schnittstelle, externer Penetrationstest |
| Docker umgeht Firewall-Regeln | Unbeabsichtigt offene Ports | Eigene nftables-Tabelle mit `DOCKER-USER`-Integration, Warnung im Sicherheitsberater |
| Manuelle Änderungen an Konfigurationsdateien | Überschriebene Anpassungen | Drift-Erkennung, Override-Verzeichnisse, Hinweis in der Oberfläche |
| Lizenzkonflikte bei eingebundenen Komponenten | Rechtliches Risiko | Lizenzprüfung in der CI (NFA-LIZ-001) |

---

## 12. Anwendungsverwaltung (Anforderungen: Kapitel 10)

### 12.1 Bausteine

```
Panel-Server
 ├─ Modul „apps“
 │   ├─ Profil-Registry      (mitgelieferte + eigene Profile, Schema-Prüfung, Signaturen)
 │   ├─ Instanz-Verwaltung   (Sollzustand je Instanz: Profil, Version, Werte, Geheimnis-Referenzen)
 │   ├─ Konfig-Engine        (Werte ↔ Konfigurationsquellen, Diff, Rangfolge, Drift)
 │   ├─ Integrationen        (Reverse-Proxy, SMTP-Profile, Keycloak-Admin-API, DB-Provisionierung)
 │   └─ Lebenszyklus-Jobs    (Installieren, Übernehmen, Ändern, Update, Sicherung, Rollback)
 │
Agent (Treiber „apps“)
 ├─ Compose-Steuerung        (Docker Compose v2 über die Docker-API bzw. compose-go)
 ├─ Datei-Adapter            (Lesen/Schreiben in Volumes und Bind-Mounts, atomar)
 ├─ Exec-Adapter             (Anwendungs-CLI im Container: occ, kc.sh/kcadm.sh, openslides)
 └─ Versions-Repository      (Git-Repository je Instanz unter /var/lib/svagentd/apps/<instanz>/)
```

### 12.2 Konfigurationsadapter

Jede Konfigurationsquelle eines Profils hat einen Adapter. Die Adapter sind
**strukturerhaltend**: Unbekannte Schlüssel, Kommentare und Reihenfolge bleiben
erhalten (APP-CFG-003).

| Adapter | Verwendung | Umsetzung |
|---------|------------|-----------|
| `env-file` | `.env`, Env-Dateien von Diensten | Zeilenbasierter Parser mit Kommentar-Erhalt |
| `compose-env` | `environment:` in Compose-Dateien | YAML-AST (`yaml.v3`-Knoten), nur gezielte Knoten ändern |
| `yaml`, `json`, `toml`, `ini` | Konfigurationsdateien | AST-basierte Bearbeitung |
| `docker-secret` | Geheimnisse | Datei in `secrets/` mit 0400, Referenz in Compose |
| `cli` | Einstellungen, die nur per CLI sicher änderbar sind | Befehlsvorlagen im Profil, z. B. `occ config:system:set {key} --value={value} --type={type}` |
| `http-api` | Einstellungen in der Anwendungsdatenbank | REST-Aufrufe mit Token aus der Geheimnis-Ablage, z. B. Keycloak-Admin-API, Vaultwarden-Admin-API |
| `generator` | Dateien, die ein Herstellerwerkzeug erzeugt | Quelle bearbeiten, dann Generator ausführen (OpenSlides: `config.yml` → `openslides config`) |

### 12.3 Ablauf einer Konfigurationsänderung

```
Formular/Datei-Editor ─▶ Werte validieren (Profil-Schema) ─▶ Konfig-Engine rendert Ziel-Dateien
   ─▶ Diff + Aktionsplan (Reload/Restart/Recreate je Dienst) anzeigen ─▶ Freigabe
   ─▶ Agent: Commit im Versions-Repository (Stand „vorher“)
           → Dateien atomar schreiben / CLI- und API-Aufrufe ausführen
           → Prüfbefehl laut Profil (z. B. `docker compose config`, `nginx -t`)
           → Aktion ausführen (z. B. `docker compose up -d <dienst>`)
           → Healthcheck laut Profil (Timeout, Wiederholungen)
           → OK: Commit „nachher“ · Fehler: Checkout „vorher“ + Aktion erneut + Meldung
   ─▶ Audit-Eintrag mit maskiertem Diff, WebSocket-Push
```

Drift-Erkennung: Der Agent vergleicht periodisch und per `inotify` die
verwalteten Dateien mit dem letzten Commit. Abweichungen werden als
„externe Änderung“ gemeldet (APP-CFG-009).

### 12.4 Profilformat (Auszug)

```yaml
apiVersion: svpanel.apps/v1
kind: AppProfile
metadata:
  id: vaultwarden
  name: Vaultwarden
  versions: ">=1.30 <2.0"
  docs: https://github.com/dani-garcia/vaultwarden/wiki
detect:
  images: ["vaultwarden/server", "ghcr.io/dani-garcia/vaultwarden"]
services:
  app:
    image: vaultwarden/server
    volumes: { data: /data }
    ports: { http: 80 }
sources:
  env:      { adapter: compose-env, service: app, priority: 10 }
  admincfg: { adapter: json, path: "volume:data/config.json", priority: 20 }  # überschreibt env
settings:
  - key: domain
    label: { de: "Öffentliche URL", en: "Public URL" }
    type: url
    target: { source: env, name: DOMAIN, alt: { source: admincfg, name: domain } }
    effect: recreate
    managedBy: integration.reverseProxy          # wird bei Domain-Zuweisung gesetzt
  - key: signups_allowed
    label: { de: "Selbstregistrierung erlauben", en: "Allow sign-ups" }
    type: boolean
    default: false
    group: security
    target: { source: env, name: SIGNUPS_ALLOWED }
    effect: restart
    recommend: { value: false, severity: warning }
  - key: admin_token
    type: secret
    generate: argon2-phc
    target: { source: env, name: ADMIN_TOKEN }
    effect: restart
integrations:
  smtp:  { map: { host: SMTP_HOST, port: SMTP_PORT, security: SMTP_SECURITY,
                  from: SMTP_FROM, user: SMTP_USERNAME, password: SMTP_PASSWORD } }
  reverseProxy: { service: app, port: http, websocket: true }
health:
  http: { service: app, path: /alive, expect: 200 }
backup:
  pre:
    - exec: { service: app, cmd: ["sqlite3", "/data/db.sqlite3", ".backup '/data/db-backup.sqlite3'"] }
      when: "settings.database_url startsWith 'sqlite' or settings.database_url == ''"
  paths: ["volume:data"]
  exclude: ["volume:data/db.sqlite3*", "volume:data/icon_cache"]
update:
  strategy: pull-recreate
  versionScheme: semver
actions:
  - id: rotate-admin-token
    label: { de: "Admin-Token erneuern" }
    steps: [ { setSecret: admin_token, generate: argon2-phc }, { apply: true } ]
```

Profile für Nextcloud (Adapter `cli` mit `occ`), Keycloak (`http-api`,
Build-Optionen mit `effect: rebuild`) und OpenSlides (`generator`) nutzen
dieselbe Struktur. Für jedes mitgelieferte Profil gibt es in der CI einen
Integrationstest: Installation, Übernahme einer Beispielinstallation,
Änderung jeder Einstellung, Update von der vorherigen auf die aktuelle Version,
Sicherung und Wiederherstellung.

### 12.5 Keycloak-Verknüpfung (APP-INT-003)

1. Der Benutzer wählt eine Keycloak-Instanz und einen Realm, dazu die Zielinstanz (z. B. Outline).
2. Das Panel legt über die Admin-API einen vertraulichen OIDC-Client an. Redirect-URIs, Post-Logout-URI und Web-Origins werden aus der Domain der Zielinstanz berechnet. Hinzu kommen die nötigen Mapper (E-Mail, Name, Gruppen).
3. Das Client-Secret wird in der Geheimnis-Ablage gespeichert. Das Panel trägt Issuer- bzw. Endpunkt-URLs, Client-ID und Secret über das Profil der Zielinstanz ein und übernimmt sie (Abschnitt 12.3).
4. Test: Discovery-Dokument abrufen, Token-Endpunkt mit Client-Credentials prüfen, Anmeldeseite der Zielinstanz auf Weiterleitung prüfen.
5. Die Verknüpfung wird als Abhängigkeit gespeichert (APP-INT-008). Beim Löschen der Zielinstanz bietet das Panel an, den Client zu entfernen.
