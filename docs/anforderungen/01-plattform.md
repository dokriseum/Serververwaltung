# 01 · Plattform und Basisfunktionen

Übergreifende Funktionen, die alle Fachbereiche nutzen.

---

## 1. Installation, Lizenz und Aktualisierung (PLT-INS)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-INS-001 | Installation per Einzeiler-Skript auf einem frischen, unterstützten Linux-System. Abhängigkeiten werden automatisch eingerichtet. | M | PL, W |
| PLT-INS-002 | Installation als Paket (`.deb`, `.rpm`) aus einem signierten Paket-Repository | M | W |
| PLT-INS-003 | Alternative Installation des Container-Moduls als Container (`docker run …`) ohne Host-Installation des Gesamtsystems | S | P |
| PLT-INS-004 | Installationsprofile wählbar: „Minimal“ (System + Container), „Webhosting“ (inkl. Web, Mail, DNS, DB), „Vollständig“, „Benutzerdefiniert“ | M | PL |
| PLT-INS-005 | Nachträgliches Hinzufügen und Entfernen von Komponenten (z. B. Mailserver, DNS, weitere PHP-Versionen) über die Oberfläche | M | PL, W |
| PLT-INS-006 | Erstkonfigurations-Assistent beim ersten Aufruf: Administrator-Konto, Hostname, IP-Adressen, Zeitzone, Panel-Zertifikat, E-Mail für Benachrichtigungen | M | PL, P |
| PLT-INS-007 | Panel-Aktualisierung über die Oberfläche: Kanäle „Stabil“ und „Vorabversion“, Änderungsprotokoll vor der Aktualisierung anzeigen | M | PL, W |
| PLT-INS-008 | Automatische Panel-Aktualisierung nach Zeitplan, abschaltbar | S | PL |
| PLT-INS-009 | Rücksprung auf die vorherige Panel-Version bei fehlgeschlagener Aktualisierung (automatische Prüfung nach dem Update) | S | N |
| PLT-INS-010 | Deinstallation, die alle Panel-Komponenten entfernt, Kundendaten auf Wunsch aber erhält | S | W |
| PLT-INS-011 | Selbstdiagnose und Reparatur („Diagnose & Reparatur“): Dateirechte, Konfiguration der Dienste, Datenbankkonsistenz, Zertifikate prüfen und korrigieren | M | PL |
| PLT-INS-012 | Lizenzverwaltung: Eingabe, Anzeige und Prüfung eines Lizenzschlüssels, Funktionsfreischaltung nach Edition. Offline-Aktivierung möglich. | K | PL, P |

## 2. Authentifizierung (PLT-AUTH)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-AUTH-001 | Anmeldung mit Benutzername/E-Mail und Passwort | M | alle |
| PLT-AUTH-002 | Lokale Benutzerdatenbank des Panels mit Passwort-Hashing nach aktuellem Stand (Argon2id) | M | P, PL |
| PLT-AUTH-003 | Anmeldung mit Unix-Systemkonten (PAM), getrennt aktivierbar für Administrator- und Benutzerportal | M | W, U |
| PLT-AUTH-004 | Zwei-Faktor-Authentifizierung per TOTP (RFC 6238) mit Wiederherstellungscodes | M | PL, W, U |
| PLT-AUTH-005 | Zwei-Faktor-Authentifizierung per WebAuthn/FIDO2 (Sicherheitsschlüssel, Passkeys) | S | N |
| PLT-AUTH-006 | 2FA erzwingbar pro Rolle (z. B. Pflicht für Administratoren und Reseller) | M | PL |
| PLT-AUTH-007 | Anbindung an LDAP und Active Directory: Benutzersuche, Gruppensynchronisation, automatische Kontoanlage, TLS/StartTLS | M | P, W |
| PLT-AUTH-008 | Single Sign-on per OAuth 2.0 / OpenID Connect (z. B. Microsoft Entra ID, Google, Keycloak, GitHub). Zuordnung von Gruppen-Claims zu Rollen. | M | P, PL |
| PLT-AUTH-009 | SAML 2.0 als Identitätsanbieter-Protokoll | K | N |
| PLT-AUTH-010 | Passwort-Richtlinien: Mindestlänge, Komplexität, Prüfung gegen Listen kompromittierter Passwörter, Ablaufzeit (optional) | M | PL, W |
| PLT-AUTH-011 | Passwort-vergessen-Funktion per E-Mail-Link mit begrenzter Gültigkeit | M | PL |
| PLT-AUTH-012 | Sperre nach n Fehlversuchen (pro Konto und pro IP), konfigurierbare Sperrdauer | M | PL, W |
| PLT-AUTH-013 | Zugriffsbeschränkung auf das Panel nach IP-Adressen/Netzen (Erlauben/Verbieten-Listen) | M | PL, W |
| PLT-AUTH-014 | Sitzungsverwaltung: Leerlauf-Timeout, maximale Sitzungsdauer, Bindung an IP (optional), Liste aktiver Sitzungen mit Abmelden-Funktion | M | PL, W |
| PLT-AUTH-015 | Anmeldung per Zertifikat (TLS-Client-Zertifikat) | K | W |
| PLT-AUTH-016 | „Anmelden als“: Administratoren und Reseller öffnen das Panel aus Sicht eines untergeordneten Kontos, ohne dessen Passwort zu kennen. Jede Nutzung wird protokolliert. | M | PL |
| PLT-AUTH-017 | Einmal-Anmeldelinks für Support-Zwecke mit begrenzter Gültigkeit | K | PL |
| PLT-AUTH-018 | Anmeldeseite mit konfigurierbarem Hinweistext (z. B. rechtlicher Hinweis) | S | W |
| PLT-AUTH-019 | Protokoll aller Anmeldeversuche (erfolgreich/fehlgeschlagen) mit Zeit, IP, Benutzeragent | M | P, PL, W |

## 3. Benutzer, Teams und Rollen (PLT-RBAC)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-RBAC-001 | Verwaltung von Panel-Benutzern: anlegen, bearbeiten, sperren, löschen, Passwort zurücksetzen | M | alle |
| PLT-RBAC-002 | Teams/Gruppen von Benutzern. Rechte können an Teams vergeben werden. | M | P, W |
| PLT-RBAC-003 | Vordefinierte Rollen gemäß Rollenbegriffen im README, nicht löschbar | M | P, PL |
| PLT-RBAC-004 | Eigene Rollen aus Einzelrechten zusammenstellbar | M | P, PL, W |
| PLT-RBAC-005 | Rechte auf Modulebene: jedes Funktionsmodul einzeln freigebbar | M | W |
| PLT-RBAC-006 | Rechte innerhalb eines Moduls, z. B. „Dienste: nur Neustart, nur Dienste X und Y“, „Benutzer: nur UID-Bereich 1000–2000“, „Dateimanager: nur Verzeichnis /srv/app“ | M | W |
| PLT-RBAC-007 | Rechte mit Geltungsbereich: global, je Server, je Umgebung (Container), je Abonnement, je Namespace | M | P, PL |
| PLT-RBAC-008 | Container-spezifische Rollen: Umgebungs-Administrator, Operator, Helpdesk, Standardbenutzer, Nur-Lesen | M | P |
| PLT-RBAC-009 | Eigentumsmodell für Container-Ressourcen: Ressourcen gehören Benutzer, Team, Administratoren oder sind öffentlich | M | P |
| PLT-RBAC-010 | Hierarchie Administrator → Reseller → Kunde → Zusatzbenutzer des Kunden. Jede Ebene sieht nur die eigenen und untergeordneten Objekte. | M | PL |
| PLT-RBAC-011 | Zusatzbenutzer eines Kunden mit eigener Rolle (z. B. nur Webmail, nur Dateien, nur WordPress) | M | PL |
| PLT-RBAC-012 | Rechteübersicht: effektive Rechte eines Benutzers anzeigen (inkl. geerbter Rechte aus Teams) | S | N |
| PLT-RBAC-013 | Rechte-Vorlagen kopieren und als Ausgangspunkt für neue Rollen verwenden | S | W |
| PLT-RBAC-014 | Zeitlich befristete Rechte (Ablaufdatum) | K | N |

## 4. Benutzeroberfläche (PLT-UI)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-UI-001 | Webbasierte Single-Page-Anwendung, vollständig ohne Browser-Plugins bedienbar | M | alle |
| PLT-UI-002 | Responsives Layout für Desktop, Tablet und Smartphone | M | PL, W |
| PLT-UI-003 | Helles und dunkles Farbschema, automatisch nach Systemeinstellung oder manuell | M | W |
| PLT-UI-004 | Mehrsprachigkeit. Version 1.0: Deutsch und Englisch. Sprache je Benutzer wählbar, Übersetzungen als austauschbare Dateien. | M | alle |
| PLT-UI-005 | Globale Suche über alle Objekte: Domains, Container, Benutzer, Dienste, Einstellungen, Menüpunkte | M | PL, W |
| PLT-UI-006 | Favoriten und zuletzt verwendete Seiten | S | W |
| PLT-UI-007 | Ansichten je Rolle: Administratoren sehen die Serveransicht, Kunden eine vereinfachte Abonnement-Ansicht | M | PL |
| PLT-UI-008 | Ansicht „Power-User“ für Einzelbetreiber ohne Kunden-/Reseller-Ebene | S | PL |
| PLT-UI-009 | Branding: Logo, Farben, Produktname, Favicon, eigene Panel-URL je Reseller | S | PL, P |
| PLT-UI-010 | Tabellen mit Sortierung, Filter, Spaltenauswahl, Mehrfachauswahl, Massenaktionen, Export (CSV) | M | P, PL |
| PLT-UI-011 | Live-Aktualisierung von Statusanzeigen (Container, Dienste, Jobs) ohne Seitenneuladen | M | P |
| PLT-UI-012 | Fortschrittsanzeige und Live-Ausgabe lang laufender Aufgaben. Aufgaben laufen weiter, wenn der Browser geschlossen wird. | M | PL, P |
| PLT-UI-013 | Aufgabenliste mit allen laufenden und abgeschlossenen Hintergrundaufgaben | M | PL |
| PLT-UI-014 | Kontextbezogene Hilfe und Links zur Dokumentation auf jeder Seite | S | PL, W |
| PLT-UI-015 | Barrierefreiheit nach WCAG 2.1 AA: Tastaturbedienung, Screenreader, Kontraste | M | N |
| PLT-UI-016 | Bestätigungsdialoge für destruktive Aktionen. Für besonders kritische Aktionen ist die Eingabe des Objektnamens nötig. | M | P |
| PLT-UI-017 | Anzeige der äquivalenten Shell-Befehle bzw. API-Aufrufe zu einer Aktion („Was passiert hier?“) | K | N |
| PLT-UI-018 | Tastaturkürzel für häufige Aktionen und die Suche | K | W |

## 5. Dashboard und Systeminformationen (PLT-DSH)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-DSH-001 | Startseite mit Systemübersicht: Hostname, Betriebssystem, Kernel, Uptime, CPU-Modell und -Kerne, Last, RAM, Swap, Datenträger, Netzwerkdurchsatz, Panel-Version | M | W, PL |
| PLT-DSH-002 | Echtzeit-Diagramme für CPU, RAM, Disk-I/O und Netzwerk | M | W |
| PLT-DSH-003 | Anzahl verfügbarer Paketaktualisierungen, davon Sicherheitsaktualisierungen, mit direktem Link zur Installation | M | W, PL |
| PLT-DSH-004 | Status der wichtigsten Dienste (Web, Mail, DNS, DB, Docker) mit Ampel | M | PL |
| PLT-DSH-005 | Container-Übersicht: Anzahl Container nach Status, Images, Volumes, Netzwerke, Stacks | M | P |
| PLT-DSH-006 | Hosting-Übersicht: Anzahl Kunden, Abonnements, Domains, Postfächer, Datenbanken, Speicherverbrauch | M | PL |
| PLT-DSH-007 | Hinweise und Empfehlungen (Sicherheit, Updates, ablaufende Zertifikate, volle Datenträger) mit Handlungsvorschlag | M | PL |
| PLT-DSH-008 | Dashboard per Widgets anpassbar (Anordnen, Ein-/Ausblenden) je Benutzer | S | PL |
| PLT-DSH-009 | Kunden-Dashboard: Ressourcenverbrauch des Abonnements gegen Kontingent | M | PL |
| PLT-DSH-010 | Hardware-Informationen: CPU-Temperaturen, Lüfter, Sensoren (lm-sensors), sofern verfügbar | S | W |

## 6. Dateimanager und Terminal (PLT-FIL)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-FIL-001 | Webbasierter Dateimanager: Verzeichnisbaum, Liste mit Größe, Rechten, Eigentümer, Änderungsdatum | M | PL, W, U |
| PLT-FIL-002 | Dateien und Ordner anlegen, umbenennen, kopieren, verschieben, löschen (mit Papierkorb) | M | PL, W, U |
| PLT-FIL-003 | Upload per Drag-and-drop, auch große Dateien (> 4 GB) mit fortsetzbarem Upload. Upload aus URL. | M | PL, W |
| PLT-FIL-004 | Download einzelner Dateien und ganzer Ordner (als ZIP/TAR) | M | PL, W |
| PLT-FIL-005 | Archive packen und entpacken: zip, tar, tar.gz, tar.bz2, tar.xz, 7z, rar (nur Entpacken) | M | PL, W |
| PLT-FIL-006 | Texteditor mit Syntaxhervorhebung, Zeilennummern, Suchen/Ersetzen, Zeichensatzwahl, Zeilenenden-Erkennung | M | PL, W |
| PLT-FIL-007 | Bildvorschau und Vorschau von PDF- und Textdateien | S | W |
| PLT-FIL-008 | Rechte (chmod, auch rekursiv), Eigentümer (chown), ACLs und erweiterte Attribute (chattr) ändern | M | PL, W |
| PLT-FIL-009 | Dateisuche nach Name, Inhalt, Größe, Datum | M | W |
| PLT-FIL-010 | Symbolische Links anlegen, Hardlinks erkennen | S | W |
| PLT-FIL-011 | Lesezeichen für Verzeichnisse | S | W |
| PLT-FIL-012 | Dateivergleich (Diff) zweier Dateien | K | W |
| PLT-FIL-013 | Freigabelinks für Dateien mit Ablaufzeit und optionalem Passwort | K | N |
| PLT-FIL-014 | Der Dateimanager arbeitet immer mit den Rechten des Zielkontos (Kunde, Systembenutzer). Root-Zugriff nur für Administratoren. | M | PL, W, U |
| PLT-FIL-015 | Browserbasiertes Terminal (xterm-kompatibel) mit PTY, Farben, Größenanpassung, Copy & Paste | M | W, P |
| PLT-FIL-016 | Terminal auch für Kunden mit eingeschränkter Shell (chroot/jail), sofern im Paket freigegeben | S | PL |
| PLT-FIL-017 | Ein-Befehl-Ausführung („Command Shell“) mit Verlauf, ohne interaktive Sitzung | S | W |
| PLT-FIL-018 | Aufzeichnung von Terminal-Sitzungen (optional, für Audit) | K | N |

## 7. Benachrichtigungen (PLT-NOT)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-NOT-001 | Benachrichtigungen bei Ereignissen per E-Mail | M | PL, W |
| PLT-NOT-002 | Weitere Kanäle: Webhook (JSON), Slack, Microsoft Teams, Telegram, Matrix, SMS-Gateway, Pushover/ntfy | S | P, W |
| PLT-NOT-003 | Ereigniskatalog: Panel- und Systemereignisse (z. B. Kunde angelegt, Domain gelöscht, Backup fehlgeschlagen, Dienst ausgefallen, Zertifikat läuft ab, Ressourcenlimit überschritten, Container gestoppt) | M | PL |
| PLT-NOT-004 | Empfänger je Ereignis wählbar: Administrator, Reseller, Kunde, beliebige Adressen | M | PL |
| PLT-NOT-005 | Vorlagen für Benachrichtigungstexte, mehrsprachig, mit Platzhaltern | S | PL |
| PLT-NOT-006 | Benachrichtigungszentrale in der Oberfläche (Glocke) mit gelesen/ungelesen | M | W, P |
| PLT-NOT-007 | Ereignisbehandlung („Event Manager“): bei einem Ereignis ein Skript ausführen, mit Ereignisdaten als Umgebungsvariablen | S | PL |
| PLT-NOT-008 | Drosselung und Zusammenfassung wiederholter Benachrichtigungen | S | N |

## 8. Audit und Aktivitätsprotokoll (PLT-AUD)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-AUD-001 | Jede ändernde Aktion wird protokolliert: Zeit, Benutzer, Quelle (UI/API/CLI), IP, Objekt, Aktion, vorher/nachher (Diff) | M | P, PL, W |
| PLT-AUD-002 | Bei Konfigurationsdateien wird ein Diff der geänderten Datei gespeichert | M | W |
| PLT-AUD-003 | Rückgängig machen einer Konfigurationsänderung aus dem Protokoll heraus (sofern technisch möglich) | S | W |
| PLT-AUD-004 | Filter nach Zeitraum, Benutzer, Modul, Objekt. Export als CSV/JSON. | M | P, W |
| PLT-AUD-005 | Manipulationsschutz: Hash-Kette, Weiterleitung an Syslog/SIEM | M | N |
| PLT-AUD-006 | Aufbewahrungsdauer konfigurierbar | M | PL |
| PLT-AUD-007 | Kunden sehen nur die Aktivitäten in ihren eigenen Abonnements | S | PL |

## 9. API, CLI und Automatisierung (PLT-API)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-API-001 | Vollständige REST-API: Jede Funktion der Oberfläche ist auch per API verfügbar (die Oberfläche nutzt selbst ausschließlich diese API) | M | P, PL |
| PLT-API-002 | OpenAPI-3.1-Beschreibung, interaktive API-Dokumentation im Panel | M | P |
| PLT-API-003 | API-Schlüssel je Benutzer mit Ablaufdatum, Beschreibung und eingeschränktem Rechteumfang | M | P, PL |
| PLT-API-004 | Kommandozeilenwerkzeug für alle Funktionen, lokal (als root) und remote (per API-Schlüssel) | M | PL, W |
| PLT-API-005 | Ausgaben der CLI als Tabelle und als JSON | M | N |
| PLT-API-006 | Ausgehende Webhooks bei Ereignissen (siehe PLT-NOT) | M | P, PL |
| PLT-API-007 | Eingehende Webhooks zum Auslösen von Aktionen (z. B. Container neu bereitstellen, Git-Deployment) | M | P, PL |
| PLT-API-008 | Terraform-Provider und Ansible-Collection für die Panel-API | K | N |
| PLT-API-009 | Schnittstelle zu Abrechnungssystemen (z. B. WHMCS, Blesta): Kunden und Abonnements anlegen, sperren, löschen, Paket wechseln | S | PL |
| PLT-API-010 | Benutzerdefinierte Befehle: Administratoren hinterlegen Skripte mit Parametern als Schaltflächen für bestimmte Rollen | M | W, U |
| PLT-API-011 | Ratenbegrenzung der API je Schlüssel | M | N |

## 10. Erweiterungen (PLT-EXT)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-EXT-001 | Erweiterungsschnittstelle: zusätzliche Module mit eigenen Seiten, API-Endpunkten, Rechten und Ereignis-Hooks | S | PL, W |
| PLT-EXT-002 | Erweiterungskatalog in der Oberfläche: suchen, installieren, aktualisieren, entfernen | S | PL |
| PLT-EXT-003 | Erweiterungen werden signiert, und die Signatur wird vor der Installation geprüft | S | N |
| PLT-EXT-004 | Erweiterungen laufen mit minimalen Rechten und deklarieren die Berechtigungen, die sie benötigen | S | N |
| PLT-EXT-005 | Import vorhandener Module im Webmin-Format | K | W |

## 11. Mehrserver-Betrieb (PLT-MSV)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| PLT-MSV-001 | Ein zentrales Panel verwaltet mehrere Server. Jeder Server wird per Agent angebunden. | M | P, W |
| PLT-MSV-002 | Server-Gruppen und Tags, Filter in allen Ansichten nach Server | M | P |
| PLT-MSV-003 | Server-Index: Liste aller Server mit Status, Version, Erreichbarkeit, direktem Wechsel | M | W |
| PLT-MSV-004 | Agent baut die Verbindung ausgehend auf (Edge-Modus), damit auch Server hinter NAT/Firewall verwaltbar sind | M | P |
| PLT-MSV-005 | Rollen von Servern im Hosting-Verbund: Web-, Mail-, DNS-, Datenbankserver getrennt zuweisbar | K | PL |
| PLT-MSV-006 | Massenaktionen über mehrere Server: Pakete aktualisieren, Befehl ausführen, Datei verteilen, Benutzer anlegen (Details in SYS-CLU) | M | W |
