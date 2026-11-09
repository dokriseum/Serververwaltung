# Serververwaltung

Software zur vollständig grafischen Verwaltung von Linux-Servern über den
Browser. Sie vereint Container-Verwaltung, Webhosting mit Kunden- und
Reseller-Verwaltung, Systemadministration und ein Self-Service-Portal für
Systembenutzer.

## Status

Spezifikationsphase. Code gibt es noch nicht.

## Dokumentation

| Dokument | Inhalt |
|----------|--------|
| [Anforderungskatalog](docs/anforderungen/README.md) | Alle funktionalen und nichtfunktionalen Anforderungen mit IDs und Prioritäten |
| [Technische Spezifikation](docs/TECHNISCHE-SPEZIFIKATION.md) | Architektur, Komponenten, verwaltete Dienste, Sicherheit, Datenmodell, API, Umsetzungsplan |
| [Architekturentscheidungen (ADR)](docs/adr/) | Begründete Grundsatzentscheidungen |
| [Architekturentwurf v0.1](docs/ARCHITEKTUR.md) | Erster Entwurf (abgelöst) |

## Funktionsbereiche

1. Plattform: Anmeldung (2FA, LDAP, OIDC), Rollen und Rechte, Dashboard, Dateimanager, Terminal, API, CLI
2. Container: Docker, Podman, Swarm, Kubernetes, Stacks, GitOps, Edge
3. Webhosting: Kunden, Reseller, Service-Pakete, Domains, PHP/Node.js, SSL, DNS, Datenbanken, FTP
4. E-Mail: Postfächer, Spam- und Virenschutz, DKIM/SPF/DMARC, Webmail
5. Systemverwaltung: Pakete, Dienste, Prozesse, Benutzer, Datenträger, Netzwerk, Serverdienste, Cluster
6. Benutzerportal: Self-Service für Systembenutzer
7. Sicherheit: Firewall, Fail2Ban, WAF, Malware-Scanner, Sicherheitsberater
8. Backup und Monitoring
9. Anwendungsverwaltung: OpenSlides, Vaultwarden, Keycloak, Nextcloud, Outline und weitere Container-Anwendungen

## Geplanter Stack (Kurzfassung)

Go (Panel-Server, Host-Agent, CLI) · PostgreSQL · VictoriaMetrics ·
React + TypeScript · nginx/Apache · PHP-FPM · MariaDB/PostgreSQL · PowerDNS ·
Postfix/Dovecot/Rspamd · nftables · restic

## Verwaltete Container-Anwendungen

OpenSlides, Vaultwarden, Keycloak, Nextcloud und Outline über deklarative
Anwendungsprofile: Formular- und Dateiansicht der Konfiguration, Versionshistorie
mit Rollback, SSO-Verknüpfung mit Keycloak, Updates mit Sicherung und Rücknahme
([Kapitel 10](docs/anforderungen/10-anwendungen.md)).
