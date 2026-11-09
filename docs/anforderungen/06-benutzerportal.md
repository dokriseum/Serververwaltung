# 06 · Benutzerportal

Funktionsumfang entsprechend Usermin: ein Self-Service-Portal für
Systembenutzer. Alle Aktionen laufen ausschließlich mit den Rechten des
angemeldeten Unix-Benutzers.

---

## 1. Grundlagen (USR-GEN)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| USR-GEN-001 | Eigener Einstiegspunkt (eigener Port oder Pfad, z. B. `/portal`), Anmeldung mit Unix-Konto über PAM | M | U |
| USR-GEN-002 | Jeder Prozess des Portals für einen Benutzer läuft unter dessen UID/GID. Eine Rechteausweitung ist ausgeschlossen. | M | U |
| USR-GEN-003 | Administrator legt fest, welche Module für welche Benutzer und Gruppen verfügbar sind | M | U |
| USR-GEN-004 | Administrator legt Standardeinstellungen je Modul fest, die Benutzer überschreiben dürfen oder nicht | M | U |
| USR-GEN-005 | Anmeldung für bestimmte Benutzer/Gruppen sperren (z. B. Systemkonten, root) | M | U |
| USR-GEN-006 | 2FA für Portalbenutzer (siehe PLT-AUTH-004) | M | U |
| USR-GEN-007 | Eigene Sprache und Farbschema je Benutzer | M | U |
| USR-GEN-008 | Startseite mit Kontoinformationen: Quota-Nutzung, letzte Anmeldung, neue Mails | S | U |

## 2. Konto (USR-ACC)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| USR-ACC-001 | Eigenes Passwort ändern, unter Beachtung der Passwortrichtlinien und Synchronisation mit Samba/Mail, sofern konfiguriert | M | U |
| USR-ACC-002 | Persönliche Angaben ändern (Name/GECOS), Login-Shell aus erlaubter Liste wählen | S | U |
| USR-ACC-003 | Eigene Disk-Quota-Nutzung anzeigen | M | U |
| USR-ACC-004 | Eigene laufende Prozesse anzeigen und beenden | S | U |
| USR-ACC-005 | Eigene `.plan`/`.project`-Datei bearbeiten | K | U |

## 3. Dateien und Shell (USR-FIL)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| USR-FIL-001 | Dateimanager im eigenen Home-Verzeichnis (Funktionsumfang PLT-FIL), Einschränkung auf bestimmte Verzeichnisse durch den Administrator | M | U |
| USR-FIL-002 | Upload und Download, auch Download von URL | M | U |
| USR-FIL-003 | Terminal und Command-Shell mit eigenen Rechten (abschaltbar durch Administrator) | M | U |
| USR-FIL-004 | SSH-/Telnet-Client zu anderen Hosts | K | U |
| USR-FIL-005 | Passwortgeschützte Web-Verzeichnisse im eigenen `public_html` anlegen (.htaccess/.htpasswd) | S | U |
| USR-FIL-006 | Eigene Cron-Jobs verwalten (sofern erlaubt) | M | U |
| USR-FIL-007 | Benutzerdefinierte Befehle des Administrators ausführen | S | U |

## 4. SSH und Verschlüsselung (USR-SSH)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| USR-SSH-001 | Eigene SSH-Schlüssel erzeugen (Ed25519, RSA), öffentliche Schlüssel in `authorized_keys` verwalten | M | U |
| USR-SSH-002 | Eigene SSH-Client-Konfiguration (`~/.ssh/config`) und `known_hosts` bearbeiten | S | U |
| USR-SSH-003 | GnuPG: Schlüssel erzeugen, importieren, exportieren, signieren; Dateien ver- und entschlüsseln, signieren, Signaturen prüfen | K | U |

## 5. E-Mail (USR-MAIL)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| USR-MAIL-001 | Lesen der eigenen Mails (lokales Postfach oder IMAP): Ordner, Suche, Anhänge, Schreiben, Antworten, Weiterleiten, Adressbuch, Signatur | S | U |
| USR-MAIL-002 | Weiterleitung und Abwesenheitsnotiz für das eigene Konto (Sieve/`.forward`) | M | U |
| USR-MAIL-003 | Eigene Filterregeln (Sieve, alternativ Procmail) | M | U |
| USR-MAIL-004 | Eigene Spamfilter-Einstellungen: Schwellwert, erlaubte/verbotene Absender, Aktion | M | U |
| USR-MAIL-005 | Abholung externer Postfächer (Fetchmail-Äquivalent) | K | U |

## 6. Datenbanken (USR-DB)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| USR-DB-001 | Verwaltung eigener MariaDB/MySQL- und PostgreSQL-Datenbanken mit eigenen Datenbank-Zugangsdaten: Tabellen anzeigen, bearbeiten, SQL ausführen, Export/Import | S | U |
