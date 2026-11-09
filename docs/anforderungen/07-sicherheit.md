# 07 · Sicherheit

Funktionsumfang entsprechend Plesk (Firewall, Fail2Ban, ModSecurity,
Malware-Scanner, Security Advisor) und Webmin (Linux-Firewall, FirewallD,
Shorewall, Fail2Ban).

---

## 1. Firewall (SEC-FW)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SEC-FW-001 | Paketfilter-Verwaltung auf Basis von nftables. Unterstützt auch firewalld, wenn die Distribution es verwendet. | M | PL, W |
| SEC-FW-002 | Vereinfachte Ansicht: vordefinierte Regeln für Panel, SSH, Web, Mail, DNS, FTP, Datenbanken, Container; je Regel erlauben/verbieten/nur von bestimmten Quellen | M | PL |
| SEC-FW-003 | Eigene Regeln: Richtung, Protokoll, Ports/Portbereiche, Quell-/Zieladressen, Schnittstelle, Aktion, Protokollierung, Kommentar | M | PL, W |
| SEC-FW-004 | Experten-Ansicht: Tabellen, Ketten, Sets, NAT (SNAT/DNAT/Masquerading), Portweiterleitung, Rate-Limits | S | W |
| SEC-FW-005 | IPv4 und IPv6 gleichwertig | M | W |
| SEC-FW-006 | Länderblockierung (GeoIP) | S | PL |
| SEC-FW-007 | Sicheres Übernehmen: Regeln werden vor der Aktivierung geprüft und automatisch zurückgenommen, wenn die Verbindung zum Panel nicht innerhalb von n Sekunden bestätigt wird | M | PL |
| SEC-FW-008 | Zusammenspiel mit Docker: Container-Ports unterliegen den Firewall-Regeln (Docker umgeht die Regeln nicht unbemerkt), Anzeige der von Docker erzeugten Regeln | M | N |
| SEC-FW-009 | Regelsätze exportieren, importieren, als Vorlage auf andere Server übertragen | S | W |
| SEC-FW-010 | Zonen-Konzept (firewalld-Zonen bzw. Schnittstellen-Gruppen) | S | W |

## 2. Intrusion Prevention (SEC-IPS)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SEC-IPS-001 | Fail2Ban-Integration: Jails für Panel, SSH, Mail (SMTP/IMAP/POP3), FTP, Webserver, WordPress-Login, Webmail, ModSecurity | M | PL, W |
| SEC-IPS-002 | Jails aktivieren/deaktivieren, Parameter je Jail (maxretry, findtime, bantime, steigende Sperrzeiten) | M | PL |
| SEC-IPS-003 | Gesperrte IP-Adressen anzeigen, entsperren, manuell sperren | M | PL |
| SEC-IPS-004 | Vertrauenswürdige IP-Adressen/Netze (nie sperren) | M | PL |
| SEC-IPS-005 | Eigene Filter (reguläre Ausdrücke) und Jails anlegen, mit Test gegen eine Log-Datei | S | PL, W |
| SEC-IPS-006 | Externe Reputationslisten für Sperrungen einbinden (z. B. CrowdSec-Blocklisten) | K | N |

## 3. Web Application Firewall (SEC-WAF)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SEC-WAF-001 | WAF-Modul für den Webserver, Modi: aus, nur erkennen, blockieren | M | PL |
| SEC-WAF-002 | Regelsätze: OWASP Core Rule Set und weitere, mit automatischer Aktualisierung | M | PL |
| SEC-WAF-003 | Paranoia-Level/Profile (schnell, ausgewogen, streng) | S | PL |
| SEC-WAF-004 | Regeln je Domain deaktivieren (nach ID, Tag, Meldung) | M | PL |
| SEC-WAF-005 | WAF-Log mit Zuordnung zu Domain und Regel, direktes Ausnehmen einer Regel aus dem Log heraus | M | PL |
| SEC-WAF-006 | Eigene Regeln hinzufügen | S | PL |
| SEC-WAF-007 | Rate-Limiting/Bot-Schutz je Domain (Anfragen je IP und Zeit) | S | N |

## 4. Malware-Schutz (SEC-MAL)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SEC-MAL-001 | Malware-Scanner für Webspace-Dateien: vollständiger Scan nach Zeitplan und Echtzeitüberwachung neuer und geänderter Dateien | M | PL |
| SEC-MAL-002 | Aktionen bei Fund: melden, in Quarantäne verschieben, bereinigen (sofern möglich), löschen, Wiederherstellen aus Quarantäne | M | PL |
| SEC-MAL-003 | Bericht je Domain/Abonnement, Benachrichtigung von Administrator und Kunde | M | PL |
| SEC-MAL-004 | Ausnahmen (Pfade, Dateien) | M | PL |
| SEC-MAL-005 | Rootkit-Prüfung des Systems (z. B. rkhunter/chkrootkit) | S | N |
| SEC-MAL-006 | Integritätsüberwachung von Systemdateien (Prüfsummen, Abweichungsbericht) | S | N |

## 5. Sicherheitsberater und Härtung (SEC-ADV)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SEC-ADV-001 | Sicherheitsberater mit Prüfliste und Bewertung: Panel-TLS, 2FA, Updates, Firewall, Fail2Ban, WAF, SSH-Härtung, offene Ports, Zertifikate, schwache Passwörter, veraltete PHP-Versionen | M | PL |
| SEC-ADV-002 | Jede Empfehlung mit Ein-Klick-Umsetzung (sofern möglich) und Erläuterung | M | PL |
| SEC-ADV-003 | Härtungsprofile (z. B. auf Basis der CIS-Benchmarks) prüfen und anwenden, mit Abweichungsbericht | S | N |
| SEC-ADV-004 | Prüfung auf bekannte Schwachstellen (CVE) installierter Pakete | S | N |
| SEC-ADV-005 | Kennwortrichtlinien serverweit für alle Konten (Panel, Mail, FTP, DB) | M | PL |
| SEC-ADV-006 | Isolation von Abonnements: getrennte Benutzer, PHP-FPM-Pools je Abonnement, Dateirechte ohne Zugriff durch andere, optional chroot/Namespaces | M | PL |
| SEC-ADV-007 | Panel-Sicherheitseinstellungen: Sicherheitsheader (CSP, HSTS, X-Frame-Options), Cookie-Flags, Sperre von Iframes | M | PL |

## 6. Geheimnisse und Kryptografie (SEC-SEC)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| SEC-SEC-001 | Alle gespeicherten Zugangsdaten (Registry, Backup-Speicher, DNS-APIs, Remote-DBs) werden verschlüsselt abgelegt. Der Schlüssel liegt getrennt von der Datenbank. | M | N |
| SEC-SEC-002 | Passwörter von Mail-, FTP- und Datenbankkonten werden nur gehasht gespeichert (wo das Protokoll es erlaubt) | M | N |
| SEC-SEC-003 | Schlüsselrotation des Panel-Hauptschlüssels | S | N |
| SEC-SEC-004 | Anbindung eines externen Schlüsselspeichers (Vault/OpenBao, KMS) | K | N |
