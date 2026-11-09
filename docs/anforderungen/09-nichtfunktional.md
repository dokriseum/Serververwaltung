# 09 · Nichtfunktionale Anforderungen

---

## 1. Kompatibilität (NFA-KMP)

| ID | Anforderung | Prio |
|----|-------------|:----:|
| NFA-KMP-001 | Unterstützte Betriebssysteme (x86_64 und arm64): Debian 12 und 13, Ubuntu 22.04 LTS und 24.04 LTS, AlmaLinux/Rocky Linux/RHEL 9 und 10. Jede Version wird bis zum Ende ihres regulären Supports unterstützt. | M |
| NFA-KMP-002 | Neue LTS-Versionen der unterstützten Distributionen werden spätestens 3 Monate nach Erscheinen unterstützt | S |
| NFA-KMP-003 | Container-Laufzeiten: Docker Engine ≥ 24, Podman ≥ 4.4, Kubernetes in den drei jeweils aktuellen Minor-Versionen | M |
| NFA-KMP-004 | Browser: jeweils die zwei aktuellen Hauptversionen von Chrome, Edge, Firefox und Safari (Desktop und Mobil) | M |
| NFA-KMP-005 | Betrieb auf Bare Metal, auf VMs (KVM, VMware, Hyper-V) und in Cloud-Instanzen. In LXC-Containern mit Einschränkungen (dokumentiert). | M |
| NFA-KMP-006 | Mindestausstattung „Minimal“: 1 vCPU, 1 GB RAM, 10 GB Speicher. „Webhosting“: 2 vCPU, 4 GB RAM, 40 GB Speicher. | M |

## 2. Leistung und Skalierung (NFA-PRF)

| ID | Anforderung | Prio |
|----|-------------|:----:|
| NFA-PRF-001 | Seitenaufbau (erste Anzeige) < 2 s, Interaktionen < 300 ms (95. Perzentil) bei bis zu 50 gleichzeitigen Benutzern | M |
| NFA-PRF-002 | Ein Einzelserver verwaltet mindestens 1.000 Domains, 5.000 Postfächer und 500 Container ohne merkliche Verzögerung der Oberfläche | M |
| NFA-PRF-003 | Ein zentrales Panel verwaltet mindestens 500 angebundene Server bzw. 5.000 Edge-Geräte | S |
| NFA-PRF-004 | Ressourcenbedarf des Panels im Leerlauf ≤ 300 MB RAM und < 2 % CPU (ohne die verwalteten Dienste) | M |
| NFA-PRF-005 | Lange Operationen (Backups, Migration, Massenänderungen) laufen asynchron und blockieren die Oberfläche nicht | M |
| NFA-PRF-006 | Listen mit > 1.000 Einträgen werden serverseitig paginiert und gefiltert | M |

## 3. Verfügbarkeit und Robustheit (NFA-VFG)

| ID | Anforderung | Prio |
|----|-------------|:----:|
| NFA-VFG-001 | Ein Ausfall oder Neustart des Panels beeinträchtigt die verwalteten Dienste (Web, Mail, Container) nicht | M |
| NFA-VFG-002 | Jede Konfigurationsänderung ist atomar: Bei einem Fehler wird der vorherige Zustand wiederhergestellt (Validierung → Schreiben in temporäre Datei → Prüfbefehl → Umbenennen → Reload → Gesundheitsprüfung → ggf. Rücknahme) | M |
| NFA-VFG-003 | Unterbrochene Hintergrundaufgaben werden nach einem Neustart erkannt und fortgesetzt oder sauber als fehlgeschlagen markiert | M |
| NFA-VFG-004 | Manuelle Änderungen an verwalteten Konfigurationsdateien werden erkannt (Drift). Der Administrator entscheidet, ob er sie übernimmt oder überschreibt. | M |
| NFA-VFG-005 | Hochverfügbarer Betrieb des zentralen Panels (mehrere Instanzen, replizierte Datenbank) | K |
| NFA-VFG-006 | Notfallzugang: Panel-Administrator per CLI zurücksetzen, Panel ohne Weboberfläche reparieren | M |

## 4. Sicherheit (NFA-SEC)

| ID | Anforderung | Prio |
|----|-------------|:----:|
| NFA-SEC-001 | Entwicklung nach OWASP ASVS Level 2, regelmäßige Prüfung gegen OWASP Top 10 | M |
| NFA-SEC-002 | Trennung von Rechten: Die Weboberfläche und die API laufen ohne Root-Rechte. Privilegierte Operationen erfolgen ausschließlich über einen getrennten Agenten mit fest definierter, validierter Schnittstelle. Es gibt keine Funktion „beliebigen Befehl als root“, außer für Administratoren im Terminal und bei benutzerdefinierten Befehlen. | M |
| NFA-SEC-003 | Alle Eingaben werden serverseitig validiert. Shell-Aufrufe erfolgen nie über Zeichenkettenverkettung, sondern immer mit Argumentlisten. | M |
| NFA-SEC-004 | Kommunikation ausschließlich verschlüsselt (TLS 1.2+, bevorzugt 1.3). Panel ↔ Agent mit gegenseitiger Zertifikatsprüfung (mTLS). | M |
| NFA-SEC-005 | Schutz gegen CSRF, XSS (CSP ohne `unsafe-inline`), Clickjacking, SSRF, Path Traversal, Symlink-Angriffe beim Dateizugriff | M |
| NFA-SEC-006 | Signierte Releases und Pakete, reproduzierbare Builds, SBOM je Release | M |
| NFA-SEC-007 | Abhängigkeiten werden automatisch auf Schwachstellen geprüft. Kritische Lücken werden innerhalb von 7 Tagen behoben. | M |
| NFA-SEC-008 | Externer Penetrationstest vor Version 1.0 und danach jährlich | S |
| NFA-SEC-009 | Verantwortungsvolle Offenlegung: dokumentierter Meldeweg für Sicherheitslücken (`SECURITY.md`) | M |

## 5. Datenschutz (NFA-DSG)

| ID | Anforderung | Prio |
|----|-------------|:----:|
| NFA-DSG-001 | DSGVO-konform: Datensparsamkeit, Löschkonzept, Auskunft und Export personenbezogener Daten eines Kunden | M |
| NFA-DSG-002 | Keine Übertragung von Nutzungsdaten an den Hersteller ohne ausdrückliche Zustimmung (Opt-in) | M |
| NFA-DSG-003 | Anonymisierung von IP-Adressen in Logs und Statistiken konfigurierbar | S |
| NFA-DSG-004 | Betrieb ohne Internetverbindung (Offline-Updates über lokales Repository) möglich | S |

## 6. Bedienbarkeit (NFA-BED)

| ID | Anforderung | Prio |
|----|-------------|:----:|
| NFA-BED-001 | Einheitliches Design-System für alle Module | M |
| NFA-BED-002 | Aussagekräftige Fehlermeldungen mit Ursache und Lösungshinweis, keine rohen Stacktraces | M |
| NFA-BED-003 | Häufige Aufgaben (Domain anlegen, Postfach anlegen, Container starten) mit höchstens drei Schritten | S |
| NFA-BED-004 | Barrierefreiheit nach WCAG 2.1 AA (siehe PLT-UI-015) | M |

## 7. Wartbarkeit und Qualität (NFA-WRT)

| ID | Anforderung | Prio |
|----|-------------|:----:|
| NFA-WRT-001 | Modulare Architektur: Fachmodule sind unabhängig aktivierbar und haben klar definierte Schnittstellen | M |
| NFA-WRT-002 | Testabdeckung ≥ 80 % für Geschäftslogik, End-to-End-Tests für alle MUSS-Anforderungen, Integrationstests auf allen unterstützten Distributionen | M |
| NFA-WRT-003 | Statische Code-Analyse und Linting in der CI, ohne offene Befunde der Kategorie „hoch“ | M |
| NFA-WRT-004 | Dokumentation: Administratorhandbuch, Benutzerhandbuch, API-Referenz, Entwicklerdokumentation für Erweiterungen, Architekturentscheidungen (ADR) | M |
| NFA-WRT-005 | Semantische Versionierung, Änderungsprotokoll je Release | M |
| NFA-WRT-006 | Datenbankmigrationen sind vorwärts gerichtet, automatisiert und getestet. Ein Downgrade erfolgt über die Sicherung. | M |
| NFA-WRT-007 | Strukturierte Logs (JSON) und Traces (OpenTelemetry) des Panels selbst | M |

## 8. Rechtliches und Lizenzen (NFA-LIZ)

| ID | Anforderung | Prio |
|----|-------------|:----:|
| NFA-LIZ-001 | Lizenzen aller eingebundenen Komponenten sind mit dem Lizenzmodell des Produkts vereinbar (Prüfung in der CI) | M |
| NFA-LIZ-002 | Keine Übernahme von Code, Oberflächenelementen, Namen oder Markenzeichen der Referenzprodukte. Diese dienen ausschließlich als funktionale Referenz. | M |
| NFA-LIZ-003 | Kompatibilität zu offenen Formaten (Compose, Portainer-Template-JSON, BIND-Zonendateien) ist zulässig und erwünscht | S |
