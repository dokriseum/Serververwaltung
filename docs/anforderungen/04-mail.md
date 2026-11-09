# 04 · E-Mail

Funktionsumfang entsprechend Plesk (Mailserver, Webmail, Spam- und
Virenschutz) und den Webmin-Modulen Postfix, Dovecot, SpamAssassin, Procmail
und Fetchmail.

---

## 1. Mailserver (MAIL-SRV)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| MAIL-SRV-001 | SMTP-Server (MTA) für Empfang und Versand, IMAP/POP3-Server, Submission (587) und SMTPS (465) | M | PL, W |
| MAIL-SRV-002 | Mail-Dienst je Domain aktivierbar/deaktivierbar | M | PL |
| MAIL-SRV-003 | Serverweite Einstellungen: maximale Nachrichtengröße, Relay-Regeln, Authentifizierung erzwingen, erlaubte Netze, Smarthost/Relayhost mit Authentifizierung | M | PL, W |
| MAIL-SRV-004 | Ausgehende Mail begrenzen: Nachrichten pro Stunde je Postfach, Domain und Abonnement | M | PL |
| MAIL-SRV-005 | TLS für alle Mail-Protokolle, Zertifikat je Domain per SNI | M | PL |
| MAIL-SRV-006 | MTA-STS und TLS-RPT je Domain veröffentlichen | S | N |
| MAIL-SRV-007 | Mail-Warteschlange: anzeigen, filtern, Nachricht ansehen, erneut zustellen, löschen, Warteschlange leeren | M | PL, W |
| MAIL-SRV-008 | Zustellprotokoll (Maillog) mit Suche nach Absender, Empfänger, Message-ID, Zeitraum | M | PL, W |
| MAIL-SRV-009 | Erweiterte Konfiguration für Administratoren: Postfix (main.cf, master.cf, Maps: aliases, canonical, virtual, transport, relocated, header/body checks, Zugriffslisten) und Dovecot (Protokolle, Authentifizierung, Mailbox-Format, Namensräume) | S | W |
| MAIL-SRV-010 | Postfach-Format Maildir, Speicherung je Domain unter einem festen Pfad | M | PL |
| MAIL-SRV-011 | Volltextsuche im Postfach (IMAP-Suchindex) | S | N |
| MAIL-SRV-012 | Mail-Rollen auf getrennte Server verteilbar (siehe PLT-MSV-005) | K | PL |

## 2. Postfächer und Adressen (MAIL-BOX)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| MAIL-BOX-001 | Postfächer anlegen, bearbeiten, löschen, sperren; Passwort, Größenbegrenzung (Kontingent), Beschreibung | M | PL |
| MAIL-BOX-002 | E-Mail-Aliase (zusätzliche Adressen für ein Postfach) | M | PL |
| MAIL-BOX-003 | Weiterleitungen an eine oder mehrere Adressen, mit oder ohne lokale Kopie | M | PL, U |
| MAIL-BOX-004 | Abwesenheitsnotiz/Autoresponder: Betreff, Text (Text/HTML), Anhang, Zeitraum, Antwort nur einmal je Absender und Zeitraum | M | PL, U |
| MAIL-BOX-005 | Catch-all-Behandlung je Domain: zurückweisen, an Adresse weiterleiten, verwerfen | M | PL |
| MAIL-BOX-006 | Mailinglisten: anlegen, Abonnenten verwalten, Moderation, Archiv | S | PL |
| MAIL-BOX-007 | Gruppen-/Verteileradressen ohne eigenes Postfach | M | PL |
| MAIL-BOX-008 | Zugangsdaten zum Postfach direkt per E-Mail an den Benutzer senden | S | PL |
| MAIL-BOX-009 | Postfach-Benutzer erhält eigenen Panel-Zugang (Mail-Benutzer-Rolle) für Passwort, Weiterleitung, Autoresponder, Spamfilter | M | PL |
| MAIL-BOX-010 | Automatische Konfiguration von Mail-Clients (Autodiscover, Autoconfig, Apple-mobileconfig-Profil) | S | PL |
| MAIL-BOX-011 | Server-seitige Filterregeln (Sieve), mit grafischem Regel-Editor und Rohtext-Editor | M | U, PL |
| MAIL-BOX-012 | Abholung externer Postfächer (POP3/IMAP) in ein lokales Postfach nach Zeitplan | S | W, U |
| MAIL-BOX-013 | Import von Postfächern von einem fremden Server (IMAP-Synchronisation) | S | PL |
| MAIL-BOX-014 | Massenanlage von Postfächern per CSV | S | N |
| MAIL-BOX-015 | Freigegebene Ordner und Postfächer (IMAP-ACL) | K | N |

## 3. Authentifizierung und Reputation (MAIL-AUT)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| MAIL-AUT-001 | SPF: Datensatz automatisch erzeugen, SPF-Prüfung eingehender Mails | M | PL |
| MAIL-AUT-002 | DKIM: Schlüssel je Domain erzeugen (RSA 2048 und Ed25519), ausgehende Mails signieren, eingehende prüfen, Schlüsselrotation | M | PL |
| MAIL-AUT-003 | DMARC: Datensatz erzeugen, eingehende Mails prüfen, Berichte auswerten | M | PL |
| MAIL-AUT-004 | ARC-Signatur bei Weiterleitungen | S | N |
| MAIL-AUT-005 | Reverse-DNS- und HELO-Prüfung mit Hinweis bei fehlerhafter Konfiguration | M | PL |
| MAIL-AUT-006 | Prüfung, ob Server-IPs auf Blacklists stehen, mit Benachrichtigung | S | N |

## 4. Spam- und Virenschutz (MAIL-SPM)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| MAIL-SPM-001 | Spamfilter serverweit mit Schwellwert, Aktion (markieren, in Spam-Ordner verschieben, löschen), Betreff-Markierung | M | PL, W |
| MAIL-SPM-002 | Spamfilter-Einstellungen je Postfach durch den Benutzer überschreibbar | M | PL, U |
| MAIL-SPM-003 | Erlaubt- und Verboten-Listen (Absender, Domains) serverweit und je Postfach | M | PL, U |
| MAIL-SPM-004 | Lernfähiger Filter (Bayes): Lernen aus Ordnern „Spam“ und „Kein Spam“, auch per Verschieben im Mail-Client | M | PL, W |
| MAIL-SPM-005 | Greylisting mit Ausnahmen | M | PL |
| MAIL-SPM-006 | DNS-Blocklisten (RBL/DNSBL) konfigurierbar | M | PL, W |
| MAIL-SPM-007 | Virenscanner für ein- und ausgehende Mails, Aktion bei Fund (Quarantäne, Löschen, Benachrichtigung) | M | PL |
| MAIL-SPM-008 | Quarantäne-Bereich mit Freigabe durch Benutzer oder Administrator | S | N |
| MAIL-SPM-009 | Regeln für Anhänge (z. B. ausführbare Dateien blockieren) | S | N |
| MAIL-SPM-010 | Statistik über erkannte Spam- und Virenmails | S | N |
| MAIL-SPM-011 | Erweiterte Konfiguration des Spamfilters für Administratoren (Regeln, Gewichtungen, Module) | S | W |

## 5. Webmail (MAIL-WML)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| MAIL-WML-001 | Integrierter Webmail-Client (z. B. Roundcube) unter `webmail.<domain>` mit gültigem Zertifikat | M | PL |
| MAIL-WML-002 | Webmail je Domain aktivierbar/deaktivierbar, Auswahl des Webmail-Clients | M | PL |
| MAIL-WML-003 | Funktionen: Lesen, Schreiben (Text/HTML), Anhänge, Ordner, Suche, Adressbuch, Signaturen, Identitäten | M | PL, U |
| MAIL-WML-004 | Kalender und Kontakte per CalDAV/CardDAV | K | N |
| MAIL-WML-005 | Einstellungen für Filter, Abwesenheit und Weiterleitung auch im Webmail | S | PL |
| MAIL-WML-006 | PGP-Verschlüsselung und -Signatur im Webmail | K | U |
| MAIL-WML-007 | Einfacher Webmail-Client im Benutzerportal (siehe USR-MAIL) | S | U |
