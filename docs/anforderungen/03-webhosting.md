# 03 · Webhosting

Funktionsumfang entsprechend Plesk Obsidian, ergänzt um die Webserver-,
DNS-, Datenbank- und FTP-Module von Webmin. E-Mail siehe [04-mail.md](04-mail.md).

---

## 1. Kunden, Reseller, Abonnements (WEB-CUS)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-CUS-001 | Kunden anlegen, bearbeiten, sperren, entsperren, löschen. Felder: Kontaktdaten, Firma, Sprache, Login, Notizen, externe ID. | M | PL |
| WEB-CUS-002 | Reseller anlegen mit eigenem Kontingent, eigenen Service-Paketen, eigenem Branding und eigenen Kunden | M | PL |
| WEB-CUS-003 | Service-Pakete (Hosting-Pläne) mit Ressourcen-Limits: Speicher, Traffic, Anzahl Domains, Subdomains, Alias-Domains, Postfächer, Postfachgröße, Mailinglisten, Datenbanken, Datenbankgröße, FTP-Konten, Websites, Zusatzbenutzer, Cron-Jobs | M | PL |
| WEB-CUS-004 | Service-Pakete mit Berechtigungen: z. B. DNS verwalten, PHP-Einstellungen ändern, SSH-Zugang, Cron-Jobs, Backups, Anwendungsinstallation, Git, Docker | M | PL |
| WEB-CUS-005 | Service-Pakete mit Hosting-Parametern: Webserver-Einstellungen, PHP-Version und -Handler, Mail-Einstellungen, DNS-Vorlage, Log-Rotation | M | PL |
| WEB-CUS-006 | Zusatzpakete (Add-ons), die Limits und Rechte eines Abonnements erweitern | S | PL |
| WEB-CUS-007 | Abonnement = Kunde + Service-Paket + Hauptdomain + Systembenutzer. Ein Kunde kann mehrere Abonnements haben. | M | PL |
| WEB-CUS-008 | Abonnements sperren (mit Grund), entsperren, Paket wechseln, an einen anderen Kunden übertragen, Ablaufdatum | M | PL |
| WEB-CUS-009 | Synchronisation: Änderungen am Service-Paket werden auf alle zugehörigen Abonnements übertragen. Ein Abonnement kann vom Paket gelöst werden („angepasst“). | M | PL |
| WEB-CUS-010 | Überschreitungsrichtlinie je Limit: blockieren, erlauben mit Benachrichtigung, erlauben | M | PL |
| WEB-CUS-011 | Ressourcenverbrauch je Abonnement und Kunde: Speicher (Web, Mail, DB, Logs, Backups getrennt), Traffic je Monat, Verlaufsdiagramme | M | PL |
| WEB-CUS-012 | Systemressourcen-Limits je Abonnement über cgroups v2: CPU, RAM, I/O-Bandbreite, Prozesse | S | PL |
| WEB-CUS-013 | Hosting-Typ je Domain: Website-Hosting, Weiterleitung (301/302, Frame), kein Hosting | M | PL |
| WEB-CUS-014 | Massenaktionen: Pakete zuweisen, sperren, Benachrichtigung senden | S | PL |
| WEB-CUS-015 | Willkommens-E-Mail mit Zugangsdaten beim Anlegen (Vorlage) | S | PL |

## 2. Domains und Websites (WEB-DOM)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-DOM-001 | Domains anlegen und löschen, inkl. internationalisierter Domainnamen (IDN/Punycode) | M | PL |
| WEB-DOM-002 | Subdomains mit eigenem Dokumentenstamm | M | PL |
| WEB-DOM-003 | Alias-Domains, die auf eine Domain zeigen (Web, Mail und DNS jeweils wählbar) | M | PL |
| WEB-DOM-004 | Wildcard-Subdomains | S | PL |
| WEB-DOM-005 | Hosting-Einstellungen: Dokumentenstamm, bevorzugte Domain (www/ohne www), HTTPS-Weiterleitung (301), HSTS, IPv4/IPv6-Adresse | M | PL |
| WEB-DOM-006 | Website-Vorschau über eine temporäre Domain, bevor DNS umgestellt ist | S | PL |
| WEB-DOM-007 | Website sperren mit eigener Sperrseite. Wartungsmodus mit Freigabe für eigene IPs. | M | PL |
| WEB-DOM-008 | Eigene Fehlerseiten (403, 404, 500, 503 …) | M | PL |
| WEB-DOM-009 | Weiterleitungen und Rewrite-Regeln über die Oberfläche | S | PL |
| WEB-DOM-010 | Passwortgeschützte Verzeichnisse (HTTP Basic Auth) mit Benutzerverwaltung | M | PL, W |
| WEB-DOM-011 | Virtuelle Verzeichnisse und Aliase | S | PL |
| WEB-DOM-012 | Hotlink-Schutz für Dateitypen | S | PL |
| WEB-DOM-013 | Domain umbenennen und in ein anderes Abonnement verschieben | M | PL |
| WEB-DOM-014 | Website klonen (Dateien + Datenbanken) auf eine andere Domain/Subdomain | S | PL |
| WEB-DOM-015 | Website-Import von einem fremden Server per FTP/SSH (Dateien, Datenbanken, Mail) | S | PL |
| WEB-DOM-016 | Website-Screenshot als Vorschaubild in der Domainliste | K | PL |

## 3. Webserver (WEB-WSV)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-WSV-001 | nginx als Frontend-Webserver (Reverse-Proxy, statische Dateien) | M | PL, W |
| WEB-WSV-002 | Apache als optionaler Backend-Webserver (für `.htaccess`-Kompatibilität), alternativ nur nginx | M | PL, W |
| WEB-WSV-003 | Je Domain wählbar: Proxy-Modus zu Apache ein/aus, statische Dateien direkt durch nginx | M | PL |
| WEB-WSV-004 | Zusätzliche Webserver-Direktiven je Domain (Apache und nginx getrennt, HTTP/HTTPS getrennt) mit Syntaxprüfung vor dem Übernehmen | M | PL |
| WEB-WSV-005 | Serverweite Webserver-Einstellungen: Worker/MPM, Timeouts, Gzip/Brotli, HTTP/2, HTTP/3 (QUIC), Keepalive, Größenlimits | M | PL, W |
| WEB-WSV-006 | Caching: nginx-Cache je Domain, Bereinigung über die Oberfläche | S | PL |
| WEB-WSV-007 | Reverse-Proxy-Regeln je Domain zu beliebigen Zielen, z. B. Container-Ports, mit WebSocket-Unterstützung | M | N |
| WEB-WSV-008 | Apache-Module aktivieren/deaktivieren | M | W, PL |
| WEB-WSV-009 | Vollständige Apache-Verwaltung für Administratoren: virtuelle Hosts, Direktiven je Verzeichnis, Module, MIME-Typen, Zugriffsregeln | S | W |
| WEB-WSV-010 | Vollständige nginx-Verwaltung für Administratoren: Server-Blöcke, Upstreams, Includes | S | W |
| WEB-WSV-011 | Konfigurationsvorlagen für Webserver-Dateien anpassbar (Templates mit Überschreibungen, update-sicher) | S | PL |
| WEB-WSV-012 | Webserver-Konfiguration neu erzeugen (einzelne Domain oder alle) | M | PL |
| WEB-WSV-013 | Statusseite des Webservers (aktive Verbindungen, Anfragen/s) | S | W |

## 4. Skriptsprachen und Anwendungslaufzeiten (WEB-RUN)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-RUN-001 | Mehrere PHP-Versionen parallel (alle von PHP unterstützten Versionen, optional ältere), installierbar über die Oberfläche | M | PL |
| WEB-RUN-002 | PHP-Handler je Domain: PHP-FPM (über nginx oder Apache), FastCGI | M | PL |
| WEB-RUN-003 | PHP-Einstellungen je Domain: memory_limit, max_execution_time, upload_max_filesize, post_max_size, open_basedir, disable_functions, display_errors, error_reporting, eigene Direktiven | M | PL, W |
| WEB-RUN-004 | PHP-FPM-Pool-Einstellungen je Domain: pm-Modus, max_children, start_servers, Timeouts | M | PL |
| WEB-RUN-005 | PHP-Erweiterungen je Version aktivieren/deaktivieren, PECL-Erweiterungen installieren | M | PL, W |
| WEB-RUN-006 | Serverweite `php.ini` je Version bearbeiten | M | W |
| WEB-RUN-007 | OPcache-Status und -Reset | S | N |
| WEB-RUN-008 | Node.js-Anwendungen: Version wählbar, Startdatei, Umgebung, npm/yarn/pnpm install, Prozessverwaltung, automatischer Neustart, Proxy über die Domain | M | PL |
| WEB-RUN-009 | Python-Anwendungen (WSGI/ASGI): Version, virtuelle Umgebung, `pip install -r requirements.txt` | S | PL |
| WEB-RUN-010 | Ruby-Anwendungen (Rack): Version, Bundler | K | PL |
| WEB-RUN-011 | .NET-Core-Anwendungen unter Linux | K | PL |
| WEB-RUN-012 | Composer-Unterstützung: Abhängigkeiten installieren/aktualisieren, `composer.json` erkennen | S | PL |
| WEB-RUN-013 | Laravel-Unterstützung: Artisan-Befehle, Scheduler, Queue-Worker, `.env`-Editor, Wartungsmodus | K | PL |
| WEB-RUN-014 | Website als Container betreiben: Domain wird per Reverse-Proxy mit einem Container verbunden (Verbindung zu Modul 02) | M | PL, P |

## 5. Anwendungen und CMS (WEB-APP)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-APP-001 | Anwendungskatalog mit Ein-Klick-Installation (z. B. WordPress, Joomla, Drupal, Nextcloud, Matomo, PrestaShop, Moodle) | S | PL |
| WEB-APP-002 | WordPress-Verwaltung: Installation, Erkennung bestehender Installationen, Übersicht aller Installationen auf dem Server | M | PL |
| WEB-APP-003 | WordPress: Kern-, Plugin- und Theme-Updates (einzeln, massenhaft, automatisch nach Richtlinie) | M | PL |
| WEB-APP-004 | WordPress: Plugins und Themes suchen, installieren, aktivieren, deaktivieren, löschen, auch serverweit als Sets | M | PL |
| WEB-APP-005 | WordPress: Sicherheitsprüfung und Härtung (Dateirechte, XML-RPC, wp-config-Schutz, Debug aus, Versionsanzeige entfernen, Admin-Benutzername) mit Rückgängig-Funktion | M | PL |
| WEB-APP-006 | WordPress: Prüfung auf bekannte Schwachstellen in Kern, Plugins und Themes | S | PL |
| WEB-APP-007 | WordPress: Staging-Kopie anlegen, Synchronisation Staging ↔ Live (Dateien, Datenbank, wählbare Tabellen) | M | PL |
| WEB-APP-008 | WordPress: Klonen, Suchmaschinen-Indexierung an/aus, Debug-Modus, Wartungsmodus, Passwort zurücksetzen, Anmelden ohne Passwort | M | PL |
| WEB-APP-009 | WordPress: „Smart Update“, d. h. Update zuerst auf einer Kopie testen und visuell vergleichen, dann übernehmen | K | PL |
| WEB-APP-010 | WordPress: WP-CLI in der Oberfläche | S | PL |
| WEB-APP-011 | Git-Bereitstellung: entferntes Repository (GitHub, GitLab, Bitbucket, eigenes) oder lokales Repository auf dem Server | M | PL |
| WEB-APP-012 | Git: automatische Bereitstellung per Webhook bei Push oder manuell, Zielverzeichnis, Branch, Befehle nach der Bereitstellung (Build-Schritte) | M | PL |
| WEB-APP-013 | Git: Deploy-Keys je Repository erzeugen, Verlauf der Bereitstellungen | M | PL |
| WEB-APP-014 | Bereitstellung ohne Ausfallzeit (Release-Verzeichnisse mit Symlink-Wechsel) | K | N |

## 6. SSL/TLS (WEB-SSL)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-SSL-001 | Kostenlose Zertifikate per ACME (Let's Encrypt, ZeroSSL, eigener ACME-Server) für Domains, www, Aliase, Subdomains, Webmail und Mail | M | PL, W |
| WEB-SSL-002 | Wildcard-Zertifikate per DNS-01-Validierung (lokales DNS oder Anbieter-API, z. B. Cloudflare, Hetzner, Route 53) | M | PL |
| WEB-SSL-003 | Automatische Verlängerung mit Benachrichtigung bei Fehlschlag | M | PL |
| WEB-SSL-004 | Eigene Zertifikate hochladen (Zertifikat, Schlüssel, Zwischenzertifikate), Zertifikatsanfrage (CSR) erzeugen | M | PL, W |
| WEB-SSL-005 | Selbstsignierte Zertifikate erzeugen | M | PL, W |
| WEB-SSL-006 | Zertifikatspool je Abonnement und serverweit, Zuweisung an Domains, Panel, Mail-Dienste und FTP | M | PL |
| WEB-SSL-007 | Übersicht aller Zertifikate mit Ablaufdatum, Aussteller, abgedeckten Namen, Warnung vor Ablauf | M | PL |
| WEB-SSL-008 | TLS-Einstellungen serverweit: Protokollversionen, Cipher-Suites, Profile (modern/kompatibel), OCSP-Stapling | M | PL |
| WEB-SSL-009 | Eigene Zertifizierungsstelle (CA): CA erstellen, Zertifikate ausstellen und widerrufen (z. B. für interne Dienste, Client-Zertifikate) | S | W |
| WEB-SSL-010 | Anzeige der SSL-Bewertung einer Domain (eigener Test oder externer Dienst) | K | PL |

## 7. DNS (WEB-DNS)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-DNS-001 | Integrierter autoritativer DNS-Server, Zonen werden beim Anlegen einer Domain automatisch erzeugt | M | PL, W |
| WEB-DNS-002 | Datensatztypen: A, AAAA, CNAME, MX, TXT, SRV, NS, PTR, CAA, TLSA, SSHFP, DS, HTTPS/SVCB | M | PL, W |
| WEB-DNS-003 | DNS-Vorlage serverweit und je Service-Paket, mit Platzhaltern (`<domain>`, `<ip>`) | M | PL |
| WEB-DNS-004 | SOA-Einstellungen: TTL, Refresh, Retry, Expire, Minimum, Serienformat | M | PL, W |
| WEB-DNS-005 | DNSSEC: Signierung je Zone, Schlüsselverwaltung, automatische Schlüsselrotation, Anzeige der DS-Einträge für die Registry | M | PL, W |
| WEB-DNS-006 | Primär- und Sekundärbetrieb: Zonen als Sekundär von einem fremden Server beziehen, eigene Zonen an Sekundärserver übertragen (AXFR/IXFR, NOTIFY, TSIG) | M | PL, W |
| WEB-DNS-007 | DNS-Dienst je Domain aus-/einschalten (bei externem DNS) | M | PL |
| WEB-DNS-008 | Synchronisation mit externen DNS-Anbietern über API (z. B. Cloudflare, Route 53, Hetzner, deSEC) | S | PL |
| WEB-DNS-009 | Reverse-Zonen für eigene IP-Netze | S | W |
| WEB-DNS-010 | Import und Export von Zonendateien (BIND-Format) | M | W |
| WEB-DNS-011 | Prüfung der Zonen und der Delegation (stimmen die NS-Einträge bei der Registry?) | S | N |
| WEB-DNS-012 | Rekursiver Resolver und Forwarder für interne Netze, mit Zugriffsbeschränkung (ACL) | S | W |
| WEB-DNS-013 | Views/Split-Horizon (unterschiedliche Antworten je Client-Netz) | K | W |
| WEB-DNS-014 | Dynamisches DNS (RFC 2136 mit TSIG) | K | W |

## 8. Datenbanken (WEB-DB)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-DB-001 | Datenbankserver MariaDB/MySQL und PostgreSQL, lokal oder entfernt registriert | M | PL, W |
| WEB-DB-002 | Datenbanken je Abonnement anlegen, löschen, umbenennen, kopieren (inkl. auf andere Server) | M | PL |
| WEB-DB-003 | Datenbankbenutzer anlegen, Passwort ändern, Rechte einzeln oder als Profil (Lesen/Schreiben, nur Lesen), Zugriff von entfernten Hosts erlauben | M | PL, W |
| WEB-DB-004 | Webbasierte Datenbankverwaltung mit automatischer Anmeldung (phpMyAdmin bzw. Adminer/pgAdmin-Äquivalent) | M | PL |
| WEB-DB-005 | Export (Dump) und Import (SQL, komprimiert) über die Oberfläche | M | PL, W |
| WEB-DB-006 | Prüfen und Reparieren von Tabellen | S | PL |
| WEB-DB-007 | Integrierte Tabellenansicht: Tabellen, Spalten, Indizes anzeigen und bearbeiten, SQL ausführen, Ergebnisse anzeigen | S | W |
| WEB-DB-008 | Serverweite Einstellungen des Datenbankservers bearbeiten (my.cnf bzw. postgresql.conf, pg_hba.conf) | M | W |
| WEB-DB-009 | Datenbankserver-Status: Prozesse/Verbindungen, Variablen, Replikationsstatus. Prozesse beenden. | S | W |
| WEB-DB-010 | Mehrere Versionen von MariaDB/MySQL/PostgreSQL parallel (z. B. über Container) | K | PL |
| WEB-DB-011 | Datenbank-Größe in Ressourcenverbrauch und Limits einbeziehen | M | PL |
| WEB-DB-012 | Redis/Valkey-Instanzen je Abonnement (Socket, Passwort, Speicherlimit) | K | N |

## 9. FTP und SFTP (WEB-FTP)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-FTP-001 | FTP-Server mit FTPS (explizites TLS), Passivmodus-Portbereich konfigurierbar | M | PL, W |
| WEB-FTP-002 | Zusätzliche FTP-Konten je Abonnement mit eigenem Startverzeichnis und Lese-/Schreibrechten | M | PL |
| WEB-FTP-003 | SFTP-Zugang je Systembenutzer, wahlweise in chroot-Umgebung | M | PL |
| WEB-FTP-004 | Anonymer FTP-Zugang je Domain (optional, standardmäßig aus) | K | PL |
| WEB-FTP-005 | Serverweite FTP-Einstellungen (Willkommensnachricht, Verbindungen, Zeitlimits, IPv6, TLS erzwingen) | M | W |
| WEB-FTP-006 | Liste aktiver FTP-Sitzungen mit Trennen-Funktion | S | PL |

## 10. Systembenutzer, SSH und Cron je Abonnement (WEB-SUB)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-SUB-001 | Jedes Abonnement hat einen eigenen Unix-Benutzer. Webprozesse laufen isoliert unter diesem Benutzer. | M | PL |
| WEB-SUB-002 | SSH-Zugang je Abonnement: keine Shell, chroot-Shell, volle Shell | M | PL |
| WEB-SUB-003 | SSH-Schlüssel je Abonnement verwalten | M | PL |
| WEB-SUB-004 | Geplante Aufgaben je Abonnement: Befehl, URL-Aufruf oder PHP-Skript, Zeitplan, Benachrichtigung bei Ausgabe/Fehler, sofort ausführen | M | PL |
| WEB-SUB-005 | Log-Dateien je Domain im Log-Browser: Zugriffs- und Fehlerprotokolle von Webserver und PHP, Live-Ansicht, Filter | M | PL |
| WEB-SUB-006 | Log-Rotation je Abonnement (Größe/Zeit, Komprimierung, Aufbewahrung) | M | PL |
| WEB-SUB-007 | Webstatistiken aus Logdateien (z. B. GoAccess, AWStats) je Domain, optional passwortgeschützt | S | PL |

## 11. Server-IP-Adressen und Webserver-Grundeinstellungen (WEB-GEN)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| WEB-GEN-001 | IP-Adressen des Servers verwalten (IPv4/IPv6), als gemeinsam genutzt oder exklusiv kennzeichnen, Zuweisung an Reseller/Kunden | M | PL |
| WEB-GEN-002 | Standard-Website für nicht zugeordnete Anfragen je IP | M | PL |
| WEB-GEN-003 | Panel-Zugriff über eigene Domain (z. B. `panel.example.de`) mit eigenem Zertifikat | M | PL |
| WEB-GEN-004 | Standardseite für neue Domains (Vorlage für den Dokumentenstamm) | S | PL |
| WEB-GEN-005 | Migration von anderen Panels (Plesk, cPanel, DirectAdmin, ISPConfig) und von anderen Servern dieses Systems, mit Vorprüfung und Teilmigration | S | PL |
| WEB-GEN-006 | Übersicht aller Domains serverweit mit Filtern (Kunde, IP, PHP-Version, SSL-Status) | M | PL |
| WEB-GEN-020 | Windows-Server-Unterstützung (IIS, MSSQL, ASP.NET) | K | PL |
