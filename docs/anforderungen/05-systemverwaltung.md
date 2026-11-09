# 05 · Systemverwaltung

Funktionsumfang entsprechend Webmin (Kategorien System, Server, Netzwerk,
Hardware, Cluster, Werkzeuge), ergänzt um die Systemfunktionen von Plesk.

Alle Funktionen in diesem Kapitel sind standardmäßig nur für
Server-Administratoren sichtbar. Über feingranulare Rechte (PLT-RBAC-006)
lassen sie sich gezielt an andere Rollen delegieren.

---

## 1. Software und Aktualisierungen (SYS-PKG)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-PKG-001 | Liste installierter Pakete mit Version, Beschreibung, Größe, Herkunft (Repository) | M | W |
| SYS-PKG-002 | Pakete suchen und installieren aus den Repositories, aus einer hochgeladenen Datei oder einer URL | M | W |
| SYS-PKG-003 | Pakete entfernen, mit Anzeige abhängiger Pakete | M | W |
| SYS-PKG-004 | Verfügbare Aktualisierungen anzeigen, Sicherheitsaktualisierungen getrennt ausweisen, einzeln oder alle installieren | M | W, PL |
| SYS-PKG-005 | Automatische Aktualisierungen: aus, nur Sicherheit, alle, mit Zeitplan und E-Mail-Bericht | M | W, PL |
| SYS-PKG-006 | Pakete von der Aktualisierung ausschließen (hold/versionlock) | M | PL |
| SYS-PKG-007 | Paketquellen verwalten: hinzufügen, deaktivieren, Signaturschlüssel importieren | M | W |
| SYS-PKG-008 | Hinweis auf erforderlichen Neustart (Kernel-Update) bzw. neu zu startende Dienste | M | N |
| SYS-PKG-009 | Live-Kernel-Patching, sofern auf der Distribution verfügbar | K | PL |
| SYS-PKG-010 | Distributions-Upgrade auf die nächste Hauptversion mit Vorabprüfung | K | PL |
| SYS-PKG-011 | Perl-Module, Python-Pakete (pip) und weitere Sprach-Paketmanager systemweit verwalten | K | W |
| SYS-PKG-012 | Aktualisierungen auf Wartungsfenster beschränken | S | N |

## 2. Dienste und Systemstart (SYS-SVC)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-SVC-001 | Liste aller Dienste (systemd-Units) mit Status, Beschreibung, Start beim Booten | M | W, PL |
| SYS-SVC-002 | Dienste starten, stoppen, neu starten, neu laden, aktivieren, deaktivieren, maskieren | M | W, PL |
| SYS-SVC-003 | Unit-Dateien anzeigen und bearbeiten, Drop-in-Overrides anlegen, neue Dienste anlegen (Assistent für einfache Dienste) | M | W |
| SYS-SVC-004 | Logs eines Dienstes (journald) direkt aus der Dienstansicht | M | W |
| SYS-SVC-005 | systemd-Timer anzeigen und verwalten | S | W |
| SYS-SVC-006 | Server neu starten und herunterfahren (mit Bestätigung und optionaler Verzögerung) | M | W, PL |
| SYS-SVC-007 | Überwachte Dienste: bei Ausfall automatisch neu starten und benachrichtigen | M | PL, W |
| SYS-SVC-008 | Unterstützung für SysV-Init und OpenRC, sofern auf dem System vorhanden | K | W |
| SYS-SVC-009 | xinetd/inetd-Dienste verwalten | K | W |

## 3. Prozesse (SYS-PRC)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-PRC-001 | Prozessliste als Baum, sortierbar nach CPU, RAM, Benutzer, Laufzeit | M | W |
| SYS-PRC-002 | Prozessdetails: Befehlszeile, Umgebung, offene Dateien, Netzwerkverbindungen, Limits, cgroup | M | W |
| SYS-PRC-003 | Signale senden (TERM, KILL, HUP, STOP, CONT, beliebig), Priorität ändern (nice/renice, ionice) | M | W |
| SYS-PRC-004 | Prozesssuche nach Name, Benutzer, Port, geöffneter Datei | M | W |
| SYS-PRC-005 | Neuen Prozess starten (im Hintergrund, mit Ausgabe) | S | W |
| SYS-PRC-006 | Live-Ansicht im Stil von `top`/`htop` | S | N |

## 4. Geplante Aufgaben (SYS-CRN)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-CRN-001 | Cron-Jobs aller Benutzer anzeigen, anlegen, bearbeiten, löschen, aktivieren/deaktivieren | M | W |
| SYS-CRN-002 | Zeitplan-Editor mit einfacher Auswahl (stündlich, täglich …) und Experten-Ausdruck, Anzeige der nächsten Ausführungen | M | W |
| SYS-CRN-003 | Cron-Job sofort ausführen und Ausgabe anzeigen | M | W |
| SYS-CRN-004 | Ausgabe per E-Mail senden oder verwerfen, Umgebungsvariablen für Cron | M | W |
| SYS-CRN-005 | Zugriff auf Cron je Benutzer steuern (cron.allow/cron.deny) | S | W |
| SYS-CRN-006 | Einmalige Aufgaben (`at`) anzeigen, anlegen, löschen | S | W |
| SYS-CRN-007 | Verlauf der Ausführungen mit Exit-Code und Dauer (für über das Panel angelegte Aufgaben) | S | N |

## 5. Benutzer und Gruppen (SYS-USR)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-USR-001 | Unix-Benutzer auflisten, anlegen, bearbeiten, sperren, löschen (optional mit Home-Verzeichnis und Dateien) | M | W |
| SYS-USR-002 | Benutzerfelder: Name, UID, Hauptgruppe, Zusatzgruppen, Home, Shell, Kommentar, Passwort (gesetzt, gesperrt, kein Login, vorverschlüsselt) | M | W |
| SYS-USR-003 | Passwortalterung: Ablauf, Mindest-/Höchstalter, Warnzeit, Kontoablaufdatum | M | W |
| SYS-USR-004 | Gruppen anlegen, bearbeiten, löschen, Mitglieder verwalten | M | W |
| SYS-USR-005 | Standards für neue Benutzer: UID/GID-Bereich, Home-Vorlage (/etc/skel), Shell, Quota, Gruppen | M | W |
| SYS-USR-006 | Aktionen beim Anlegen/Ändern/Löschen eines Benutzers (Skripte), Übertragung in andere Module (z. B. Samba, Mail, Quota) | S | W |
| SYS-USR-007 | Massenanlage, -änderung, -löschung per CSV | S | W |
| SYS-USR-008 | SSH-Schlüssel (authorized_keys) je Benutzer verwalten | M | N |
| SYS-USR-009 | sudo-Rechte verwalten (sudoers.d) mit Syntaxprüfung | M | N |
| SYS-USR-010 | Anzeige angemeldeter Benutzer und letzter Anmeldungen (who, last, lastlog) | M | W |
| SYS-USR-011 | Passwort beliebiger Benutzer ändern („Change Passwords“) | M | W |
| SYS-USR-012 | Zentrale Benutzerverwaltung über LDAP (siehe SYS-SRV-LDAP), NIS-Client | K | W |
| SYS-USR-013 | PAM-Konfiguration anzeigen und bearbeiten | K | W |

## 6. Datenträger und Dateisysteme (SYS-DSK)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-DSK-001 | Übersicht der Datenträger: Modell, Größe, Partitionstabelle, Partitionen, Belegung | M | W |
| SYS-DSK-002 | Partitionen anlegen, löschen, vergrößern (GPT und MBR), Dateisystem erstellen (ext4, xfs, btrfs, vfat, swap), Dateisystemprüfung | M | W |
| SYS-DSK-003 | Eingehängte Dateisysteme anzeigen, einhängen, aushängen, dauerhaft eintragen (fstab) mit Optionen | M | W |
| SYS-DSK-004 | Netzwerk-Dateisysteme einhängen: NFS, SMB/CIFS, SSHFS, mit Zugangsdaten | M | W |
| SYS-DSK-005 | Logical Volume Management: Volume-Gruppen, physische Volumes, logische Volumes anlegen, vergrößern, verkleinern, Snapshots, Thin Provisioning | M | W |
| SYS-DSK-006 | Software-RAID (mdadm): Arrays anlegen (Level 0, 1, 5, 6, 10), Status und Synchronisationsfortschritt, Datenträger hinzufügen, entfernen, als fehlerhaft markieren, Benachrichtigung bei Degradierung | M | W |
| SYS-DSK-007 | SMART-Status aller Datenträger: Attribute, Selbsttests starten, Warnung bei Grenzwerten, NVMe-Gesundheitsdaten | M | W |
| SYS-DSK-008 | Disk-Quotas für Benutzer und Gruppen: aktivieren je Dateisystem, weiche/harte Grenze, Gnadenfrist, Berichte | M | W, PL |
| SYS-DSK-009 | Speicherplatz-Analyse: größte Verzeichnisse und Dateien (grafisch, z. B. als Baumkarte) | S | N |
| SYS-DSK-010 | Swap-Dateien und -Partitionen verwalten, Swappiness | S | N |
| SYS-DSK-011 | ZFS: Pools, Datasets, Snapshots, Scrub-Status | K | N |
| SYS-DSK-012 | Btrfs: Subvolumes, Snapshots, Balance, Scrub | K | N |
| SYS-DSK-013 | iSCSI: Initiator (Client) sowie Target (Server) konfigurieren | K | W |
| SYS-DSK-014 | Verschlüsselte Datenträger (LUKS): anzeigen, öffnen, Schlüssel verwalten | K | N |

## 7. Netzwerk (SYS-NET)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-NET-001 | Netzwerkschnittstellen anzeigen (Status, MAC, MTU, Adressen, Durchsatz) | M | W |
| SYS-NET-002 | Schnittstellen konfigurieren: statisch/DHCP, IPv4/IPv6, mehrere Adressen, MTU, aktiv beim Booten. Unterstützt werden die systemeigenen Backends (netplan, NetworkManager, systemd-networkd, ifupdown). | M | W |
| SYS-NET-003 | VLANs, Bridges, Bonds (alle Modi), virtuelle Schnittstellen anlegen | M | W |
| SYS-NET-004 | Routing: Standard-Gateway, statische Routen, Richtlinien-Routing | M | W |
| SYS-NET-005 | Hostname, DNS-Resolver (Server, Suchdomains), `/etc/hosts` | M | W |
| SYS-NET-006 | Sicherheitsmechanismus bei Netzwerkänderungen: automatische Rücknahme, wenn die Änderung nicht innerhalb von n Sekunden bestätigt wird | M | N |
| SYS-NET-007 | Netzwerkwerkzeuge in der Oberfläche: ping, traceroute/mtr, DNS-Abfrage, Port-Test, Whois | S | N |
| SYS-NET-008 | Offene Ports und lauschende Dienste anzeigen (ss/netstat) mit Prozesszuordnung | M | N |
| SYS-NET-009 | Bandbreitenüberwachung je Schnittstelle, Host und Port, mit Berichten | S | W |
| SYS-NET-010 | VPN: WireGuard-Server und -Clients mit QR-Code für Mobilgeräte | S | N |
| SYS-NET-011 | VPN: IPsec (strongSwan) und OpenVPN | K | W |
| SYS-NET-012 | TCP-Wrappers (hosts.allow/hosts.deny) | K | W |
| SYS-NET-013 | Kerberos-Client-Konfiguration | K | W |
| SYS-NET-014 | PPP-Einwahl und PPTP (veraltet, nur zur Vollständigkeit) | K | W |

## 8. Systemeinstellungen (SYS-CFG)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-CFG-001 | Systemzeit und Zeitzone einstellen, Zeitsynchronisation (chrony/systemd-timesyncd) konfigurieren und Status anzeigen, Hardware-Uhr | M | W, PL |
| SYS-CFG-002 | Sprache/Locale und Tastaturbelegung | S | N |
| SYS-CFG-003 | Kernel-Parameter (sysctl) anzeigen und dauerhaft setzen | M | N |
| SYS-CFG-004 | Kernel-Module anzeigen, laden, entladen, beim Booten laden, sperren | S | W |
| SYS-CFG-005 | Bootloader (GRUB): Standardeintrag, Timeout, Kernel-Parameter | S | W |
| SYS-CFG-006 | Systemweite Ressourcen-Limits (limits.conf) | S | N |
| SYS-CFG-007 | MIME-Typen und Programmzuordnungen | K | W |
| SYS-CFG-008 | Druckerverwaltung (CUPS): Drucker, Warteschlangen, Aufträge | K | W |
| SYS-CFG-009 | Message of the Day und Login-Banner | K | N |

## 9. Systemprotokolle (SYS-LOG)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-LOG-001 | Journal (journald) anzeigen: Filter nach Unit, Priorität, Zeitraum, Boot, Volltextsuche, Live-Modus | M | W |
| SYS-LOG-002 | Klassische Log-Dateien (/var/log) anzeigen mit Live-Modus, Suche, Download | M | W |
| SYS-LOG-003 | Syslog-Konfiguration (rsyslog): Regeln, Weiterleitung an entfernte Server, Empfang von entfernten Servern | S | W |
| SYS-LOG-004 | Log-Rotation (logrotate): Regeln anzeigen, anlegen, bearbeiten, manuell ausführen | M | W |
| SYS-LOG-005 | Beliebige weitere Log-Dateien zur Ansicht registrieren (für Rollen freigebbar) | S | W |

## 10. Server-Dienste (SYS-SRV)

Fachliche Hosting-Dienste (Web, Mail, DNS, Datenbank, FTP) sind in den Kapiteln
03 und 04 beschrieben. Hier folgen weitere Serverdienste.

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-SRV-001 | **SSH-Server:** Port, Adressen, Root-Login, Passwort-Login, erlaubte Benutzer/Gruppen, Schlüsselalgorithmen, Banner, Host-Schlüssel neu erzeugen; Syntaxprüfung vor Neustart | M | W |
| SYS-SRV-002 | **Samba:** Freigaben (Pfad, Rechte, Gäste, sichtbar), Samba-Benutzer und Synchronisation mit Unix-Benutzern, globale Einstellungen, Domänenmitgliedschaft (AD), verbundene Clients | M | W |
| SYS-SRV-003 | **NFS-Server:** Exporte mit Clients und Optionen, NFSv4, aktive Clients | M | W |
| SYS-SRV-004 | **DHCP-Server:** Subnetze, Bereiche, feste Zuordnungen, Optionen, Leases anzeigen | S | W |
| SYS-SRV-005 | **Proxy-Server (Squid):** Ports, Zugriffslisten, Cache, Authentifizierung, Logs | K | W |
| SYS-SRV-006 | **LDAP-Server (OpenLDAP):** Konfiguration, Schemas, Einträge durchsuchen und bearbeiten | K | W |
| SYS-SRV-007 | **LDAP-Client:** System an einen LDAP-Verzeichnisdienst anbinden | K | W |
| SYS-SRV-008 | **Hochverfügbarkeit:** Keepalived (VRRP) für virtuelle IP-Adressen | K | W |
| SYS-SRV-009 | **Zeitserver:** chrony als NTP-Server für das lokale Netz | K | W |
| SYS-SRV-010 | **SNMP-Agent:** Community/v3-Benutzer, Zugriff | K | N |
| SYS-SRV-011 | **Backup-Software:** Bacula/Bareos-Integration (Director, Jobs, Status) | K | W |
| SYS-SRV-012 | **Jabber/XMPP-Server** | K | W |
| SYS-SRV-013 | Generischer Konfigurationseditor: beliebige Konfigurationsdatei eines Dienstes bearbeiten, mit Syntaxprüfung (falls ein Prüfbefehl hinterlegt ist), Diff und Neustart | M | W |

## 11. Werkzeuge (SYS-TLS)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-TLS-001 | Upload und Download: Dateien vom Rechner auf den Server, vom Server auf den Rechner, von einer URL auf den Server (auch zeitgesteuert) | M | W |
| SYS-TLS-002 | SSH-/Telnet-Client im Browser zu anderen Hosts | S | W |
| SYS-TLS-003 | HTTP-Tunnel zum Aufruf interner Weboberflächen über das Panel | K | W |
| SYS-TLS-004 | Benutzerdefinierte Befehle (siehe PLT-API-010) mit Parametertypen: Text, Auswahl, Datei, Benutzer, Gruppe, Passwort | M | W |
| SYS-TLS-005 | Bearbeitbare Dateien: Administrator gibt bestimmte Dateien zur Bearbeitung durch bestimmte Rollen frei, mit Befehl nach dem Speichern | S | W |
| SYS-TLS-006 | Systemvergleich: Konfigurationsunterschiede zweier Server anzeigen | K | N |

## 12. Cluster-Verwaltung (SYS-CLU)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-CLU-001 | Server zu Cluster-Gruppen zusammenfassen (siehe PLT-MSV) | M | W |
| SYS-CLU-002 | Shell-Befehl parallel auf allen Servern einer Gruppe ausführen, Ausgabe je Server | M | W |
| SYS-CLU-003 | Dateien auf mehrere Server verteilen, mit Befehl nach dem Kopieren | M | W |
| SYS-CLU-004 | Pakete auf mehreren Servern installieren, aktualisieren, entfernen, mit Übersicht der Versionen | M | W |
| SYS-CLU-005 | Benutzer und Gruppen clusterweit anlegen, ändern, löschen, synchronisieren | S | W |
| SYS-CLU-006 | Cron-Jobs clusterweit verwalten | S | W |
| SYS-CLU-007 | Passwörter clusterweit ändern | S | W |
| SYS-CLU-008 | Panel- und Agent-Konfiguration auf mehrere Server übertragen | S | W |
| SYS-CLU-009 | Gestaffelte Ausführung (n Server gleichzeitig, Abbruch bei Fehlerquote) | S | N |

## 13. Panel-Konfiguration (SYS-PNL)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SYS-PNL-001 | Port und Bind-Adressen des Panels, HTTP→HTTPS-Weiterleitung, Panel-Zertifikat | M | W, PL |
| SYS-PNL-002 | Erlaubte Referer/Hostnamen (Schutz vor DNS-Rebinding) | M | W |
| SYS-PNL-003 | Protokollierungsumfang und -aufbewahrung | M | W |
| SYS-PNL-004 | Module ein- und ausblenden, Kategorien umsortieren | S | W |
| SYS-PNL-005 | Proxy-Einstellungen für ausgehende Verbindungen des Panels (Updates, ACME, Registries) | M | W |
| SYS-PNL-006 | Sicherung und Wiederherstellung der Panel-Konfiguration und ausgewählter Systemkonfigurationsdateien | M | W |
| SYS-PNL-007 | Konfiguration für Paketpfade und Befehle der Distribution überschreibbar (bei nicht standardmäßigen Systemen) | K | W |
