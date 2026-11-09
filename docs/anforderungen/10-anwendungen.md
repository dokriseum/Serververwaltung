# 10 · Anwendungsverwaltung (Container-Anwendungen)

Verwaltung fertiger Anwendungen, die als Docker-Container laufen. Die
Container-Verwaltung aus Kapitel 02 arbeitet auf der Ebene einzelner
Container. Dieses Kapitel arbeitet auf der Ebene der **Anwendung**: ihre
Einstellungen, Konfigurationsdateien, Geheimnisse, Abhängigkeiten,
Aktualisierungen und Wartungsbefehle, ohne dass Compose-Dateien oder
Konfigurationsdateien von Hand bearbeitet werden müssen.

Zum Start werden folgende Anwendungen unterstützt:

| Anwendung | Zweck | Abschnitt |
|-----------|-------|-----------|
| OpenSlides 4 | Versammlungs- und Antragssystem | 11 |
| Vaultwarden | Passwortverwaltung (Bitwarden-kompatibel) | 12 |
| Keycloak | Identitäts- und Zugriffsverwaltung (SSO) | 13 |
| Nextcloud | Dateien, Kalender, Kontakte, Zusammenarbeit | 14 |
| Outline | Wissensdatenbank/Wiki | 15 |

Weitere Anwendungen lassen sich über Anwendungsprofile ergänzen (APP-PRO).

Ref.-Spalte: Für dieses Kapitel gibt es kein vollständiges Referenzprodukt.
**P** bedeutet, dass Portainer Teilaspekte abdeckt (Stacks, Vorlagen),
**N** steht für neu.

---

## 1. Begriffe

| Begriff | Bedeutung |
|---------|-----------|
| **Anwendungsprofil** | Maschinenlesbare Beschreibung einer Anwendung: Container, Konfigurationsquellen, Einstellungsschema, Abhängigkeiten, Healthchecks, Backup- und Update-Verfahren, Wartungsaktionen |
| **Instanz** | Eine konkrete Installation einer Anwendung, z. B. „Nextcloud Verwaltung“ oder „OpenSlides Mitgliederversammlung 2027“. Eine Anwendung kann mehrfach installiert sein. |
| **Konfigurationsquelle** | Ort, an dem eine Einstellung wirksam wird: Umgebungsvariable, `.env`-Datei, Compose-Datei, Konfigurationsdatei im Volume oder auf dem Host, Docker-Secret, Anwendungs-API oder Anwendungs-CLI |
| **Einstellung** | Ein einzelner, im Profil beschriebener Parameter mit Typ, Standardwert, Validierung und Wirkung (z. B. „SMTP-Server“) |

---

## 2. Anwendungsprofile (APP-PRO)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-PRO-001 | Jede unterstützte Anwendung wird durch ein versioniertes Anwendungsprofil beschrieben (Format siehe Technische Spezifikation, Abschnitt 12) | M | N |
| APP-PRO-002 | Ein Profil enthält: Name, Logo, Beschreibung, unterstützte Versionen, Container/Dienste, Volumes, Ports, Konfigurationsquellen, Einstellungsschema, Abhängigkeiten, Healthchecks, Backup-Definition, Update-Verfahren, Wartungsaktionen, Links zur Herstellerdokumentation | M | N |
| APP-PRO-003 | Einstellungsschema je Einstellung: Schlüssel, Bezeichnung (mehrsprachig), Hilfetext, Typ (Text, Zahl, Boolesch, Auswahl, Liste, URL, E-Mail, Host:Port, Dauer, Größe, Geheimnis, Mehrzeilentext), Standardwert, Validierung, Gruppe, Ziel-Konfigurationsquelle, Wirkung (sofort, Reload, Neustart, Neuerstellung des Containers), Versionsbereich | M | N |
| APP-PRO-004 | Profile für die fünf genannten Anwendungen werden mitgeliefert und mit neuen Anwendungsversionen gepflegt | M | N |
| APP-PRO-005 | Administratoren können eigene Profile anlegen (YAML-Editor mit Schema-Prüfung, Upload, Git-Repository) | M | N |
| APP-PRO-006 | Profil-Assistent: Aus einer bestehenden Compose-Datei bzw. aus laufenden Containern wird ein Profil-Entwurf erzeugt. Erkannt werden Umgebungsvariablen, Volumes, Ports und Abhängigkeiten. | S | N |
| APP-PRO-007 | Profilkataloge aus externen Quellen (URL/Git) mit Signaturprüfung | S | N |
| APP-PRO-008 | Profile können vorhandene Profile erweitern (z. B. „Nextcloud mit Collabora“) | K | N |
| APP-PRO-009 | Anwendungen ohne Profil bleiben über die generischen Funktionen verwaltbar: Umgebungsvariablen, Dateien, Container (APP-CFG-010) | M | N |

## 3. Erkennung und Übernahme bestehender Installationen (APP-DSC)

Die Anwendungen laufen bereits als Docker-Container. Sie müssen ohne
Neuinstallation übernommen werden können.

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-DSC-001 | Automatische Erkennung laufender Anwendungen anhand von Image-Namen, Labels und Compose-Projekten (`com.docker.compose.project`) mit Zuordnung zum passenden Profil | M | N |
| APP-DSC-002 | Übernahme-Assistent: Compose-Datei(en), `.env`-Dateien und Konfigurationsdateien in Volumes bzw. Bind-Mounts lokalisieren, Version ermitteln, abhängige Container (Datenbank, Redis, Proxy) zuordnen | M | N |
| APP-DSC-003 | Bei der Übernahme werden zunächst keine Dateien verändert. Das Panel liest den Ist-Zustand ein und zeigt Abweichungen vom Profil sowie Empfehlungen (z. B. „Klartext-Passwort in Compose-Datei“). | M | N |
| APP-DSC-004 | Vor der ersten Änderung durch das Panel wird automatisch eine Sicherung aller Konfigurationsdateien erstellt (Ausgangsstand in der Versionshistorie) | M | N |
| APP-DSC-005 | Übernahme auch von Installationen, die mit herstellereigenen Werkzeugen erzeugt wurden (z. B. OpenSlides-Manage-Tool, Nextcloud All-in-One), unter Beachtung ihrer Besonderheiten (siehe Abschnitte 11–15) | M | N |
| APP-DSC-006 | Übernahme von Anwendungen auf entfernten Servern über den Agenten | M | N |
| APP-DSC-007 | Übernahme rückgängig machen: Das Panel entfernt seine Metadaten, Container und Dateien bleiben unverändert | S | N |

## 4. Installation und Instanzen (APP-INS)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-INS-001 | Neuinstallation einer Anwendung aus dem Katalog per Assistent: Instanzname, Domain, Version, Datenbank (mitgeliefert oder vorhanden), SMTP, SSO, Ressourcenlimits | M | P |
| APP-INS-002 | Geheimnisse (Datenbank-Passwörter, Schlüssel, Tokens) werden bei der Installation automatisch und sicher erzeugt | M | N |
| APP-INS-003 | Mehrere Instanzen derselben Anwendung auf einem Server, getrennt durch Compose-Projekt, Netzwerk, Volumes und Domain | M | N |
| APP-INS-004 | Instanz klonen (Konfiguration mit oder ohne Daten), z. B. als Test- oder Staging-Instanz | M | N |
| APP-INS-005 | Instanz-Vorlagen: vorkonfigurierte Einstellungen (z. B. „OpenSlides für Veranstaltung“) für wiederkehrende Neuinstallationen | M | N |
| APP-INS-006 | Instanz deaktivieren (Container stoppen, Domain zeigt Wartungsseite), archivieren (Sicherung, dann Entfernen) und löschen (mit Bestätigung und optional mit Datenlöschung) | M | N |
| APP-INS-007 | Instanz auf einen anderen Server verschieben (Sicherung, Übertragung, Wiederherstellung, DNS/Proxy umstellen) | S | N |
| APP-INS-008 | Übersicht aller Instanzen: Anwendung, Version, Status, Health, Domain, Server, Update verfügbar, letzte Sicherung, Ressourcenverbrauch | M | N |

## 5. Konfiguration (APP-CFG)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-CFG-001 | **Formularansicht:** Alle im Profil beschriebenen Einstellungen in Gruppen (Allgemein, Domain/URL, Datenbank, E-Mail, Anmeldung/SSO, Sicherheit, Speicher, Leistung, Protokollierung, Erweitert) mit Hilfetexten und Validierung | M | N |
| APP-CFG-002 | **Dateiansicht:** Jede Konfigurationsdatei der Anwendung im Editor mit Syntaxhervorhebung und Formatprüfung (ENV, YAML, JSON, TOML, INI, PHP-Konfigurations-Array, XML, nginx/Caddy) | M | N |
| APP-CFG-003 | Formular- und Dateiansicht sind synchron. Änderungen in der Datei werden im Formular angezeigt und umgekehrt. Nicht im Profil beschriebene Einstellungen und Kommentare in Dateien bleiben erhalten. | M | N |
| APP-CFG-004 | Anzeige der **wirksamen Konfiguration** je Einstellung: aktueller Wert, Quelle (Umgebungsvariable, Datei, Datenbank, Standard) und Rangfolge, wenn mehrere Quellen konkurrieren, mit Warnung bei Konflikten (Beispiele: Vaultwarden `config.json` überschreibt Umgebungsvariablen; Nextcloud-Umgebungsvariablen wirken nur bei der Erstinstallation) | M | N |
| APP-CFG-005 | **Vorschau vor dem Übernehmen:** Diff aller betroffenen Dateien, Liste der erforderlichen Aktionen (Reload, Neustart, Neuerstellung) und der betroffenen Container | M | N |
| APP-CFG-006 | **Übernehmen** nach definierter Strategie: Datei schreiben → Formatprüfung → anwendungseigene Prüfung (falls vorhanden) → Reload/Neustart/Neuerstellung → Healthcheck → bei Fehlschlag automatische Rücknahme | M | N |
| APP-CFG-007 | Wahl des Zeitpunkts: sofort, zu einem geplanten Zeitpunkt oder im nächsten Wartungsfenster | S | N |
| APP-CFG-008 | **Versionshistorie** aller Konfigurationsänderungen je Instanz (wer, wann, was, Kommentar), Vergleich beliebiger Stände, Wiederherstellen eines früheren Stands mit einem Klick | M | N |
| APP-CFG-009 | **Drift-Erkennung:** Änderungen außerhalb des Panels (z. B. per SSH) werden erkannt, angezeigt und können übernommen oder verworfen werden | M | N |
| APP-CFG-010 | Generische Bearbeitung ohne Profil: Umgebungsvariablen, Labels, Ports, Volumes der Compose-Dienste sowie beliebige Dateien in den Volumes der Anwendung | M | P |
| APP-CFG-011 | Massenänderung über mehrere Instanzen (z. B. neuer SMTP-Server für alle Instanzen, neue Log-Stufe für alle OpenSlides-Instanzen), mit Vorschau je Instanz | M | N |
| APP-CFG-012 | Einstellungen, die in der Datenbank der Anwendung liegen, werden über die API oder die CLI der Anwendung gelesen und gesetzt, nicht durch direkte Datenbankzugriffe | M | N |
| APP-CFG-013 | Konfigurationsexport einer Instanz als Bundle (Compose, Konfigurationsdateien, Profil-Werte, ohne Geheimnisse oder verschlüsselt) und Import in eine andere Instanz | S | N |
| APP-CFG-014 | Konfiguration als Code: Synchronisation der Instanz-Konfiguration mit einem Git-Repository (Panel → Git und Git → Panel mit Freigabe), Geheimnisse ausgenommen | S | N |
| APP-CFG-015 | Empfehlungen und Prüfungen je Anwendung (z. B. fehlende Sicherheits-Header, offene Registrierung, Debug-Modus aktiv, fehlender Cron-Job) mit Ein-Klick-Korrektur | S | N |
| APP-CFG-016 | Suche über die Einstellungen aller Instanzen (z. B. „Welche Instanzen nutzen SMTP-Server X?“) | S | N |

## 6. Geheimnisse (APP-SEC)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-SEC-001 | Geheime Einstellungen (Passwörter, Tokens, Schlüssel, Client-Secrets) sind im Profil gekennzeichnet, in der Oberfläche maskiert und werden nur auf ausdrückliche Anforderung mit Audit-Eintrag angezeigt | M | N |
| APP-SEC-002 | Geheimnisse werden verschlüsselt im Panel gespeichert und als Docker-Secret oder als Datei mit Rechten 0600/0400 bereitgestellt. Sie stehen, wenn die Anwendung es unterstützt, nicht im Klartext in Compose- oder `.env`-Dateien. | M | N |
| APP-SEC-003 | Rotation von Geheimnissen per Assistent (z. B. Datenbank-Passwort: in der Datenbank und in der Anwendung gleichzeitig ändern, Neustart, Prüfung) | S | N |
| APP-SEC-004 | Erkennung von Klartext-Geheimnissen in übernommenen Konfigurationen mit Angebot zur Umstellung auf sichere Ablage | S | N |
| APP-SEC-005 | Geheimnisse werden nie in Logs, Diffs, Versionshistorie oder Exporte im Klartext geschrieben | M | N |

## 7. Integrationen (APP-INT)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-INT-001 | **Domain und Reverse-Proxy:** Domain(s) einer Instanz zuweisen. Das Panel richtet den Reverse-Proxy samt TLS-Zertifikat ein, inklusive WebSocket und passender Header (`X-Forwarded-*`). Die Anwendung wird automatisch auf die externe URL eingestellt. | M | N |
| APP-INT-002 | **Zentrale SMTP-Profile:** Mailserver-Zugangsdaten einmal anlegen und beliebigen Instanzen zuweisen. Das Panel übersetzt sie in die anwendungsspezifischen Einstellungen. Testmail-Funktion. | M | N |
| APP-INT-003 | **SSO-Verknüpfung mit Keycloak:** Für eine Instanz (Nextcloud, Outline, Vaultwarden, OpenSlides) wird per Assistent im gewählten Keycloak-Realm ein Client angelegt (OIDC bzw. SAML, Redirect-URIs, Mapper, Rollen). Die Zugangsdaten werden in der Anwendung eingetragen, anschließend wird die Anmeldung getestet. | M | N |
| APP-INT-004 | SSO-Verknüpfung auch mit anderen OIDC-Anbietern (manuelle Eingabe von Issuer, Client-ID, Secret) | S | N |
| APP-INT-005 | **Gemeinsame Datenbankserver:** Instanzen können eine mitgelieferte, eigene Datenbank oder einen gemeinsamen Datenbankserver (PostgreSQL/MariaDB) nutzen. Das Panel legt Datenbank und Benutzer an. | M | N |
| APP-INT-006 | Gemeinsamer oder eigener Redis/Valkey-Dienst je Instanz | S | N |
| APP-INT-007 | S3-kompatibler Objektspeicher als Dateiablage (sofern von der Anwendung unterstützt), mit zentral verwalteten Zugangsdaten | S | N |
| APP-INT-008 | Abhängigkeitsgraph: Anzeige, welche Instanzen von welchen Diensten abhängen (z. B. Keycloak → Nextcloud, Outline). Warnung vor Änderungen oder Abschaltung eines Dienstes, von dem andere abhängen. | S | N |

## 8. Aktualisierungen (APP-UPD)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-UPD-001 | Prüfung auf neue Versionen je Instanz (Registry-Tags gemäß Versionsschema im Profil), Anzeige von Änderungsprotokoll und Hinweisen auf inkompatible Änderungen | M | N |
| APP-UPD-002 | Update-Assistent: Vorabprüfung (Speicherplatz, unterstützter Update-Pfad, Kompatibilität der Konfiguration) → Sicherung → Wartungsmodus → Images ziehen → Migrationen → Container neu erstellen → Healthcheck → Wartungsmodus aus | M | N |
| APP-UPD-003 | Einhaltung der Update-Pfade aus dem Profil (z. B. Nextcloud nur eine Hauptversion pro Schritt). Mehrstufige Updates werden automatisch nacheinander ausgeführt. | M | N |
| APP-UPD-004 | Rollback auf den Stand vor dem Update (Images, Konfiguration, Volumes und Datenbank aus der Update-Sicherung) | M | N |
| APP-UPD-005 | Automatische Updates je Instanz nach Richtlinie: aus, nur Patch-Versionen, Minor-Versionen, im Wartungsfenster, mit Benachrichtigung | M | N |
| APP-UPD-006 | Konfigurationsmigration: Das Profil beschreibt umbenannte oder entfallene Einstellungen zwischen Versionen, das Panel passt die Konfiguration beim Update an | S | N |
| APP-UPD-007 | Update zuerst auf einer geklonten Testinstanz durchführen (APP-INS-004), dann auf die Live-Instanz anwenden | S | N |
| APP-UPD-008 | Versionen anpinnen (Image-Digest) und Updates einzelner Instanzen sperren | M | N |

## 9. Sicherung, Überwachung, Protokolle (APP-OPS)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-OPS-001 | Anwendungskonsistente Sicherung gemäß Profil: Hooks vor und nach der Sicherung (z. B. Wartungsmodus, Datenbank-Dump, SQLite-Online-Sicherung), dann Volumes, Konfiguration und Geheimnisse (verschlüsselt) | M | N |
| APP-OPS-002 | Wiederherstellung einer Instanz vollständig oder teilweise (nur Konfiguration, nur Datenbank, nur Dateien), auch als neue Instanz | M | N |
| APP-OPS-003 | Healthchecks je Anwendung gemäß Profil (HTTP-Endpunkt, CLI-Befehl), Statusanzeige und Alarmierung über das Monitoring (Kapitel 08) | M | N |
| APP-OPS-004 | Anwendungsmetriken, sofern die Anwendung sie bereitstellt (z. B. Keycloak-Metrik-Endpunkt), plus Container-Ressourcen je Instanz | S | N |
| APP-OPS-005 | Zusammengeführte Logs aller Container einer Instanz mit Filter nach Dienst und Schweregrad. Anwendungsspezifische Logdateien in Volumes werden ebenfalls angezeigt. | M | N |
| APP-OPS-006 | Ressourcenlimits (CPU, RAM) je Instanz und Dienst | M | N |

## 10. Wartungsaktionen und Rechte (APP-ACT)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-ACT-001 | Wartungsaktionen aus dem Profil als Schaltflächen und Formulare. Hinter jeder Aktion steht ein Befehl der Anwendungs-CLI oder ein API-Aufruf mit validierten Parametern, die Ausgabe erscheint live. | M | N |
| APP-ACT-002 | Wartungsmodus ein/aus je Instanz (anwendungseigener Modus oder Wartungsseite am Reverse-Proxy) | M | N |
| APP-ACT-003 | Anwendungs-CLI im Browser-Terminal mit korrektem Container und Benutzer (z. B. `occ` als `www-data`, `kcadm.sh` mit Admin-Kontext) | M | N |
| APP-ACT-004 | Notfallzugang je Anwendung: Passwort des Anwendungs-Administrators zurücksetzen bzw. neues Admin-Token erzeugen | M | N |
| APP-ACT-005 | Rechte je Instanz delegierbar: z. B. Team „Gremienbüro“ darf OpenSlides-Instanzen anlegen und konfigurieren, aber keine Geheimnisse sehen. Rechte auf Ebene von Einstellungsgruppen und Aktionen. | M | N |
| APP-ACT-006 | Alle Konfigurationsänderungen und Aktionen werden im Audit-Log mit Diff protokolliert (Geheimnisse maskiert) | M | N |

---

## 11. Profil OpenSlides 4 (APP-OSL)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-OSL-001 | Unterstützung des Microservice-Aufbaus von OpenSlides 4 (u. a. Proxy, Client, Backend, Autoupdate, Auth, Media, ICC, Vote, Manage, PostgreSQL, Redis). Anzeige des Status je Dienst. | M | N |
| APP-OSL-002 | Übernahme von Installationen, die mit dem OpenSlides-Manage-Tool erstellt wurden: `config.yml`, erzeugte `docker-compose.yml` und Verzeichnis `secrets/`. Das Panel bearbeitet die `config.yml` und erzeugt die Compose-Datei wie das Manage-Tool neu (`openslides config`), statt die Compose-Datei direkt zu ändern. | M | N |
| APP-OSL-003 | Einstellungen im Formular: Host/Port, Container-Registry und Image-Tag, Umgebungsvariablen (`defaultEnvironment`) wie E-Mail-Versand (Host, Port, Benutzer, Passwort, Verbindungssicherheit, Absender), Log-Stufe, externe Datenbank | M | N |
| APP-OSL-004 | Verwaltung der Geheimnisse im Verzeichnis `secrets/` (u. a. Auth-Token- und Cookie-Schlüssel, interne Passwörter, Datenbank-Passwort, Superadmin-Passwort) gemäß APP-SEC | M | N |
| APP-OSL-005 | Wartungsaktionen: Server-Prüfung, Initialdaten anlegen, Superadmin-Passwort setzen, Benutzer anlegen, Migrationen ausführen, Migrationsstatus anzeigen und abschließen | M | N |
| APP-OSL-006 | Viele Instanzen effizient betreiben (typisch: eine Instanz je Organisation oder Veranstaltung): Vorlagen, Massenanlage, Massen-Update, gemeinsamer Datenbankserver optional, Ressourcenübersicht | M | N |
| APP-OSL-007 | Zeitgesteuerte Instanzen: zu einem Datum starten und nach der Veranstaltung automatisch archivieren | S | N |
| APP-OSL-008 | TLS-Betrieb wahlweise über den Reverse-Proxy des Panels oder über den eingebauten Proxy von OpenSlides (lokales bzw. automatisches HTTPS) | M | N |
| APP-OSL-009 | SSO-Anbindung an Keycloak (APP-INT-003), soweit von der eingesetzten OpenSlides-Version unterstützt | S | N |
| APP-OSL-010 | Sicherung: PostgreSQL-Dump, Mediendateien, `config.yml`, `secrets/` | M | N |

## 12. Profil Vaultwarden (APP-VWD)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-VWD-001 | Einstellungen im Formular, u. a.: `DOMAIN`, Registrierung (`SIGNUPS_ALLOWED`, `SIGNUPS_VERIFY`, erlaubte Domains), Einladungen, Organisationserstellung, Passwort-Hinweise, Notfallzugriff, Sends, Log-Stufe, Datenbank (`DATABASE_URL`: SQLite, MariaDB, PostgreSQL), SMTP (Host, Port, Sicherheit, Absender, Zugangsdaten), Push-Benachrichtigungen, 2FA-Anbieter (z. B. YubiKey, Duo), Client-IP-Header | M | N |
| APP-VWD-002 | Rangfolge der Konfigurationsquellen berücksichtigen: Die über die Admin-Seite erzeugte `config.json` im Datenverzeichnis überschreibt Umgebungsvariablen. Das Panel zeigt die wirksame Quelle je Einstellung an und bietet an, die Werte in eine Quelle zusammenzuführen. | M | N |
| APP-VWD-003 | Admin-Token: Erzeugung als Argon2-PHC-Hash statt Klartext, Rotation, Admin-Seite aktivieren/deaktivieren, Zugriff auf die Admin-Seite am Reverse-Proxy auf bestimmte IP-Netze beschränkbar | M | N |
| APP-VWD-004 | SSO über OIDC (Keycloak), sofern von der eingesetzten Version unterstützt, inkl. Option „nur SSO“ | S | N |
| APP-VWD-005 | Sicherheitsempfehlungen: Registrierung offen, Admin-Seite öffentlich erreichbar, fehlendes HTTPS, fehlende Fail2Ban-Regel für Anmeldungen | M | N |
| APP-VWD-006 | Fail2Ban-Jail für fehlgeschlagene Anmeldungen und Admin-Zugriffe automatisch einrichten | S | N |
| APP-VWD-007 | Konsistente Sicherung: SQLite-Online-Sicherung (bzw. DB-Dump), Anhänge, Sends, RSA-Schlüssel, `config.json`. Sicherungen immer verschlüsselt. | M | N |
| APP-VWD-008 | Wartungsaktionen: Benutzer einladen, deaktivieren, löschen, 2FA eines Benutzers zurücksetzen, Testmail senden (über die Admin-API) | S | N |

## 13. Profil Keycloak (APP-KCK)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-KCK-001 | Server-Einstellungen im Formular (Umgebungsvariablen `KC_*` bzw. `keycloak.conf`): Datenbank (Typ, URL, Benutzer, Passwort), Hostname-Einstellungen, Proxy-Header, HTTP/HTTPS, Health- und Metrik-Endpunkte, Features, Log-Stufe, Cache, Bootstrap-Administrator | M | N |
| APP-KCK-002 | Unterscheidung zwischen Build-Optionen und Laufzeit-Optionen: Bei Änderung von Build-Optionen wird ein optimierter Build (eigenes Image oder `kc.sh build`) erzeugt, statt nur neu zu starten | M | N |
| APP-KCK-003 | Realm-Verwaltung über die Admin-REST-API: Realms auflisten, anlegen, Grundeinstellungen (Anzeigename, Login-Optionen, Passwort-Richtlinie, Sitzungsdauer, Brute-Force-Schutz, SMTP, Theme), Clients, Identity Provider, Benutzerföderation (LDAP/AD) | M | N |
| APP-KCK-004 | Realm-Export und -Import (gesamter Realm oder einzelne Clients), Vergleich zweier Realms bzw. zweier Stände eines Realms | M | N |
| APP-KCK-005 | Realm-Konfiguration als Code: Versionierung exportierter Realms in der Versionshistorie bzw. in Git (APP-CFG-014), Geheimnisse maskiert | S | N |
| APP-KCK-006 | Themes und Provider (JAR-Erweiterungen) hochladen, aktivieren und Build auslösen | S | N |
| APP-KCK-007 | Zentrale Rolle als Identitätsanbieter für die anderen Instanzen (APP-INT-003): Übersicht aller vom Panel angelegten Clients und der zugehörigen Instanzen | M | N |
| APP-KCK-008 | Wartungsaktionen: Administrator-Notfallzugang, Benutzer anlegen/sperren, Passwort zurücksetzen, Sitzungen eines Benutzers oder Realms beenden, Schlüssel rotieren | M | N |
| APP-KCK-009 | Update-Hinweise: Datenbankmigration beim Start, Prüfung auf veraltete Konfigurationsoptionen und Features vor dem Update | M | N |
| APP-KCK-010 | Sicherung: Datenbank-Dump und Realm-Export, Themes/Provider, Konfiguration | M | N |
| APP-KCK-011 | Cluster-Betrieb mehrerer Keycloak-Knoten (gemeinsame Datenbank, verteilter Cache) | K | N |

## 14. Profil Nextcloud (APP-NXC)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-NXC-001 | Unterstützung des offiziellen Nextcloud-Images (Apache- und FPM-Variante) und Erkennung von Nextcloud All-in-One. Bei All-in-One nur Anzeige und unterstützte Aktionen, die Steuerung bleibt beim Mastercontainer. | M | N |
| APP-NXC-002 | Systemeinstellungen aus `config/config.php` im Formular, u. a. `trusted_domains`, `overwrite.cli.url`, `overwriteprotocol`, `trusted_proxies`, `forwarded_for_headers`, `default_phone_region`, `maintenance_window_start`, Mail-Einstellungen, Cache (`memcache.local`, `memcache.locking`, Redis), Log-Stufe, Standardsprache | M | N |
| APP-NXC-003 | Schreiben der Systemeinstellungen über `occ config:system:set/get` statt durch direktes Parsen der PHP-Datei. Die Dateiansicht der `config.php` bleibt zusätzlich verfügbar. | M | N |
| APP-NXC-004 | Hinweis, dass Image-Umgebungsvariablen (z. B. Datenbank, Admin, Trusted Domains) nur bei der Erstinstallation wirken. Spätere Änderungen erfolgen über `occ`. | M | N |
| APP-NXC-005 | PHP-Einstellungen des Containers (Speicherlimit, Upload-Größe, OPcache) | M | N |
| APP-NXC-006 | Hintergrundaufgaben: Cron-Container oder Host-Cron für `cron.php` (alle 5 Minuten als `www-data`) einrichten, Modus auf „Cron“ setzen, letzte Ausführung überwachen | M | N |
| APP-NXC-007 | App-Verwaltung: installierte Apps anzeigen, aktivieren, deaktivieren, aktualisieren, installieren (über `occ app:*`) | M | N |
| APP-NXC-008 | Anzeige der Ergebnisse der Nextcloud-Einrichtungsprüfungen (Sicherheits- und Setup-Warnungen) mit Korrektur-Aktionen, sofern automatisierbar | M | N |
| APP-NXC-009 | Wartungsaktionen: Wartungsmodus, fehlende Indizes/Spalten/Primärschlüssel ergänzen, Reparatur (inkl. aufwendiger Reparaturschritte), Dateien neu einlesen, Papierkorb/Versionen bereinigen, Benutzer anlegen/sperren/Passwort zurücksetzen, Status anzeigen | M | N |
| APP-NXC-010 | Update-Pfad einhalten: nur eine Hauptversion pro Schritt, `occ upgrade` und Nacharbeiten (Indizes, Reparatur) automatisch ausführen | M | N |
| APP-NXC-011 | SSO über Keycloak mit der Nextcloud-App `user_oidc` (Einrichtung per `occ`), wahlweise SAML | M | N |
| APP-NXC-012 | Zusatzdienste als optionale Komponenten der Instanz: Collabora Online oder ONLYOFFICE, Talk High-Performance-Backend, Imaginary, Volltextsuche, ClamAV | S | N |
| APP-NXC-013 | Externer Speicher und primärer Objektspeicher (S3) konfigurierbar | S | N |
| APP-NXC-014 | Sicherung: Wartungsmodus → Datenbank-Dump → `config/`, `data/`, `custom_apps/`, Themes → Wartungsmodus aus | M | N |

## 15. Profil Outline (APP-OUT)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| APP-OUT-001 | Einstellungen im Formular, u. a.: `URL`, Datenbank (`DATABASE_URL`, SSL-Modus), Redis (`REDIS_URL`), Dateiablage (`FILE_STORAGE` lokal oder S3 mit Bucket, Region, Endpunkt, Zugangsdaten; maximale Upload-Größe), SMTP, Standardsprache, HTTPS erzwingen, Rate-Limiter, Log-Stufe, Anzahl Webprozesse | M | N |
| APP-OUT-002 | Automatische, sichere Erzeugung von `SECRET_KEY` und `UTILS_SECRET`. Warnung, dass eine Änderung von `SECRET_KEY` bestehende verschlüsselte Daten unbrauchbar macht. | M | N |
| APP-OUT-003 | Outline benötigt mindestens einen Anmeldeanbieter. Der Assistent richtet OIDC über Keycloak ein (Client-ID, Secret, Auth-/Token-/Userinfo-/Logout-URI, Anzeigename, Benutzername-Claim, Scopes), alternativ andere unterstützte Anbieter oder Anmeldung per E-Mail-Link (erfordert SMTP). | M | N |
| APP-OUT-004 | Prüfung vor dem Start: Ohne konfigurierten Anmeldeanbieter oder mit ungültiger `URL` wird nicht übernommen | M | N |
| APP-OUT-005 | Datenbankmigrationen beim Update kontrolliert ausführen und deren Ergebnis anzeigen | M | N |
| APP-OUT-006 | Sicherung: PostgreSQL-Dump und Dateiablage (lokales Volume bzw. Hinweis/Option zur Sicherung des S3-Buckets) | M | N |
| APP-OUT-007 | Wartungsaktionen: Healthcheck, Testmail, Neustart der Webprozesse | S | N |
