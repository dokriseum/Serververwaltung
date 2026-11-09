# 08 · Backup, Monitoring und Protokolle

---

## 1. Backup und Wiederherstellung (BAK)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| BAK-001 | Sicherungsebenen: gesamter Server, je Reseller, je Kunde, je Abonnement | M | PL |
| BAK-002 | Sicherungsinhalte wählbar: Konfiguration, Webinhalte, Datenbanken, Mail, Zertifikate, Container-Volumes und Stack-Definitionen, Systemkonfigurationsdateien | M | PL, W |
| BAK-003 | Vollsicherung und inkrementelle Sicherung, Deduplizierung | M | PL |
| BAK-004 | Zeitpläne (stündlich, täglich, wöchentlich, monatlich) mit Aufbewahrungsregeln (Anzahl, Alter, Großvater-Vater-Sohn) | M | PL |
| BAK-005 | Speicherziele: lokal, FTP/FTPS, SFTP, S3-kompatibel (AWS, Wasabi, MinIO, Hetzner …), Google Drive, Dropbox, OneDrive, WebDAV, Azure Blob, Backblaze B2, rsync | M | PL |
| BAK-006 | Mehrere Ziele gleichzeitig (z. B. lokal und extern) | S | PL |
| BAK-007 | Clientseitige Verschlüsselung mit Passwort/Schlüssel, Kompression | M | PL |
| BAK-008 | Wiederherstellung vollständig oder granular: einzelne Domain, Datenbank, Postfach, Datei/Verzeichnis, Container-Volume | M | PL |
| BAK-009 | Wiederherstellung an anderen Ort bzw. auf einen anderen Server, Konfliktbehandlung (überschreiben, umbenennen, überspringen) | M | PL |
| BAK-010 | Kunden dürfen eigene Sicherungen erstellen und wiederherstellen, sofern im Paket erlaubt; Kontingent für Sicherungsspeicher | M | PL |
| BAK-011 | Download und Upload von Sicherungsarchiven | M | PL |
| BAK-012 | Prüfung der Integrität von Sicherungen, regelmäßiger automatisierter Wiederherstellungstest | S | N |
| BAK-013 | Benachrichtigung bei Erfolg/Fehler, Bericht | M | PL |
| BAK-014 | Konsistente Datenbanksicherung (Transaktions-Snapshots), Hooks vor/nach der Sicherung (z. B. Container pausieren) | M | N |
| BAK-015 | Snapshot-basierte Sicherung über LVM/Btrfs/ZFS | K | N |
| BAK-016 | Dateisystem-Sicherung (dump/tar) beliebiger Verzeichnisse mit Zeitplan | M | W |
| BAK-017 | Sicherung der Panel-Konfiguration als eigenes Objekt (siehe SYS-PNL-006) | M | W |
| BAK-018 | Bandbreiten- und CPU-Begrenzung während der Sicherung | S | PL |

## 2. Monitoring und Alarme (MON)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| MON-001 | Erfassung von Systemmetriken: CPU (je Kern), Last, RAM, Swap, Disk-Belegung und -I/O, Netzwerk, Prozesse, Temperaturen | M | PL, W |
| MON-002 | Erfassung von Dienstmetriken: Webserver-Anfragen, PHP-FPM-Pools, Datenbankverbindungen, Mail-Warteschlange, Container-Ressourcen | M | PL, P |
| MON-003 | Verlaufsdiagramme mit wählbarem Zeitraum (1 h bis 1 Jahr), Zoom, Vergleich | M | PL |
| MON-004 | Aufbewahrung und Verdichtung der Metriken konfigurierbar | M | N |
| MON-005 | Dienstüberwachung: lokale Dienste, entfernte Ports (TCP/UDP), HTTP(S)-Prüfung mit Statuscode und Inhalt, Ping, DNS-Abfrage, SMTP/IMAP-Prüfung, Zertifikatsablauf, Speicherplatz, Last, Prozess vorhanden, beliebiges Skript | M | W, PL |
| MON-006 | Alarmregeln mit Schwellwerten und Dauer (z. B. „Disk > 90 % für 10 min“), Schweregrad | M | PL, W |
| MON-007 | Aktionen bei Alarm: Benachrichtigung (Kanäle PLT-NOT), Dienst neu starten, Skript ausführen | M | W |
| MON-008 | Externe Erreichbarkeitsprüfung von Websites (Uptime) mit Antwortzeiten | S | PL |
| MON-009 | Ressourcenverbrauch je Abonnement/Kunde im Zeitverlauf | M | PL |
| MON-010 | Statusseite (öffentlich oder intern) | K | N |
| MON-011 | Export der Metriken im Prometheus-Format und Anbindung externer Systeme (Grafana, Zabbix) | M | N |
| MON-012 | Wartungsfenster, in denen Alarme unterdrückt werden | S | N |
| MON-013 | Überwachung über mehrere Server in einer gemeinsamen Ansicht | M | W |

## 3. Protokolle (MON-LOG)

| ID | Anforderung | Prio | Ref. |
|----|-------------|:----:|------|
| MON-LOG-001 | Zentraler Log-Browser für System-, Dienst-, Webserver-, Mail-, Panel- und Container-Logs | M | PL, W |
| MON-LOG-002 | Live-Modus, Filter nach Quelle, Schweregrad, Zeitraum, Freitext | M | PL |
| MON-LOG-003 | Kunden sehen nur Logs ihrer eigenen Domains und Container | M | PL |
| MON-LOG-004 | Weiterleitung aller Logs an externe Systeme (Syslog, Loki, Elasticsearch/OpenSearch) | S | N |
