# Anforderungskatalog: Serververwaltung

Version 1.0 · Stand 2026-11-09

## 1. Zweck

Dieser Katalog beschreibt alle fachlichen und nichtfunktionalen Anforderungen
an eine Software, mit der Server vollständig **grafisch über den Browser**
verwaltet werden. Der Funktionsumfang vereint vier etablierte Produktklassen:

| Referenzprodukt | Produktklasse | Abgedeckt in |
|-----------------|---------------|--------------|
| Portainer (CE/BE) | Container- und Kubernetes-Verwaltung | [02-container.md](02-container.md) |
| Plesk Obsidian | Webhosting-Panel mit Kunden- und Reseller-Verwaltung | [03-webhosting.md](03-webhosting.md), [04-mail.md](04-mail.md) |
| Webmin | Systemadministration (Linux/Unix) | [05-systemverwaltung.md](05-systemverwaltung.md) |
| Usermin | Self-Service-Portal für Systembenutzer | [06-benutzerportal.md](06-benutzerportal.md) |
| – (eigene Anforderung) | Verwaltung von Container-Anwendungen wie OpenSlides, Vaultwarden, Keycloak, Nextcloud, Outline | [10-anwendungen.md](10-anwendungen.md) |

Übergreifende Funktionen (Anmeldung, Rechte, Dashboard, API, Sicherheit,
Backup, Monitoring) sind nur einmal beschrieben, auch wenn mehrere
Referenzprodukte sie anbieten:

- [01-plattform.md](01-plattform.md): Basis, Benutzer, Rollen, Oberfläche, API
- [07-sicherheit.md](07-sicherheit.md): Firewall, Intrusion Prevention, WAF, Malware-Schutz
- [08-backup-monitoring.md](08-backup-monitoring.md): Datensicherung, Überwachung, Protokolle
- [09-nichtfunktional.md](09-nichtfunktional.md): Qualität, Performance, Betrieb, Kompatibilität
- [10-anwendungen.md](10-anwendungen.md): Anwendungsprofile, Konfiguration und Betrieb von Container-Anwendungen

Die technische Umsetzung beschreibt
[../TECHNISCHE-SPEZIFIKATION.md](../TECHNISCHE-SPEZIFIKATION.md).

## 2. Konventionen

### 2.1 Anforderungs-IDs

`<BEREICH>-<GRUPPE>-<NR>`, z. B. `CON-CTR-012`. IDs werden nie neu vergeben.
Entfallene Anforderungen bleiben mit dem Vermerk „entfallen“ stehen.

| Präfix | Bereich |
|--------|---------|
| PLT | Plattform und Basisfunktionen |
| CON | Container und Kubernetes |
| WEB | Webhosting |
| MAIL | E-Mail |
| SYS | Systemverwaltung |
| USR | Benutzerportal |
| SEC | Sicherheit |
| BAK | Backup |
| MON | Monitoring und Protokolle |
| NFA | Nichtfunktionale Anforderungen |
| APP | Anwendungsverwaltung (Container-Anwendungen) |

### 2.2 Priorität (nach MoSCoW)

| Kürzel | Bedeutung |
|--------|-----------|
| **M** | MUSS: Pflicht für Version 1.0 |
| **S** | SOLL: wichtig, kann bei Engpässen in Version 1.x nachgeliefert werden |
| **K** | KANN: wünschenswert, später oder als Erweiterung |

### 2.3 Herkunft

Die Spalte „Ref.“ nennt das Referenzprodukt, in dem die Funktion vorkommt:
**P** = Portainer, **PL** = Plesk, **W** = Webmin, **U** = Usermin,
**N** = neu, in keinem Referenzprodukt vorhanden.

### 2.4 Rollenbegriffe

| Rolle | Beschreibung |
|-------|--------------|
| Server-Administrator | Vollzugriff auf Server, Panel und alle Mandanten |
| Reseller | Verwaltet eigene Kunden innerhalb zugewiesener Kontingente |
| Kunde | Verwaltet eigene Abonnements (Domains, Mail, Datenbanken) |
| Container-Operator | Bedient Container-Umgebungen gemäß zugewiesenen Rechten |
| Systembenutzer | Unix-Konto, nutzt das Benutzerportal (Usermin-Funktionen) |
| Mail-Benutzer | Postfach-Inhaber mit Zugriff auf Webmail und Postfach-Einstellungen |
| Nur-Lesen | Darf Informationen ansehen, aber nichts ändern |

## 3. Systemabgrenzung

**Im Umfang:**
- Linux-Server: Debian, Ubuntu LTS, RHEL-kompatible Systeme
- Verwaltung eines einzelnen Servers und mehrerer Server (Cluster/Flotte)
- Container-Laufzeiten: Docker, Podman, Docker Swarm, Kubernetes

**Nicht im Umfang von Version 1.0:**
- Windows-Server (IIS, MSSQL). Vorgemerkt als K-Anforderung `WEB-GEN-020`.
- Hardware-Provisionierung (Bare-Metal-Installation, IPMI-Erstinstallation)
- Abrechnung und Rechnungsstellung. Vorgesehen ist nur eine Schnittstelle zu externen Abrechnungssystemen.

## 4. Abdeckungsmatrix (Kurzfassung)

| Funktionsbereich | P | PL | W | U | Datei |
|------------------|:-:|:--:|:-:|:-:|-------|
| Anmeldung, 2FA, SSO, LDAP | ● | ● | ● | ● | 01 |
| Rollen und feingranulare Rechte | ● | ● | ● | ○ | 01 |
| Dashboard und Systeminformationen | ● | ● | ● | ● | 01 |
| Container, Images, Volumes, Netzwerke | ● | ○ | | | 02 |
| Stacks (Compose), GitOps | ● | ○ | | | 02 |
| Docker Swarm, Kubernetes, Helm | ● | | | | 02 |
| Edge-Geräte und Flottenverwaltung | ● | | ○ | | 02 |
| Domains, Webspace, PHP, Anwendungen | | ● | ○ | | 03 |
| Kunden, Reseller, Service-Pakete | | ● | | | 03 |
| DNS-Verwaltung | | ● | ● | | 03 |
| SSL/TLS, Let's Encrypt | ○ | ● | ● | | 03 |
| Datenbanken | | ● | ● | ● | 03 |
| FTP | | ● | ● | | 03 |
| E-Mail-Server, Webmail, Spam/Viren | | ● | ● | ● | 04 |
| Pakete, Dienste, Prozesse, Cron | | ○ | ● | ○ | 05 |
| Benutzer und Gruppen (Unix) | | | ● | | 05 |
| Festplatten, LVM, RAID, Quotas | | | ● | | 05 |
| Netzwerkkonfiguration | | ○ | ● | | 05 |
| Server-Dienste (Samba, NFS, SSH, …) | | | ● | | 05 |
| Dateimanager, Terminal | ● | ● | ● | ● | 01/05 |
| Cluster-Funktionen | ● | ○ | ● | | 05 |
| Self-Service für Systembenutzer | | | | ● | 06 |
| Firewall, Fail2Ban, WAF, Malware-Schutz | | ● | ● | | 07 |
| Backup und Wiederherstellung | ○ | ● | ● | | 08 |
| Monitoring, Alarme, Logs | ○ | ● | ● | | 08 |
| Anwendungsprofile, Konfiguration von Container-Anwendungen | ○ | | | | 10 |
| SSO-Verknüpfung, zentrale SMTP-Profile, App-Updates mit Rollback | | | | | 10 |

● = Kernfunktion im Referenzprodukt, ○ = teilweise vorhanden oder über eine Erweiterung
